---
title: VPP Deep Dive - Initialization
date: 2018-03-27
description: Notes taken from VPP PreLaunch Summit - Chapter 1.
categories:
- Linux
- VPP
tags:
- Linux
- VPP
photos:
-
---

# Directory

Following is a list of the main directories in VPP source code, where basic information is given:

- `build-data`
    + contains makefile fragments which tell vpp build system how to build various components
    + e.g. `/build-data/packages/vpp.mk` takes advantages of `auto_conf` files to install relevant dependencies
- `build-root`
    + rooted build system where all executable tools are built
    + the main make file here is fairly complicated... (data-driven)
    + normally the dependency should have no problem, it's quite accurate
- `dpdk`
    + open source DPDK package
    + nothing really touched, used directly
- `sample-plugin`
    + install engine development package
    + configure and build the sample plug-in with installed libraries
- `svm`
    + library of shared virtual memory, used for control plane binary api between clients and vpp itself
- `vlib`
    + the vector library, including means by which VPP does vector processing
    + definition of node types etc.
- `vlib-api`
    + the set of api infrustracture routines layered on top of shared virtual memory which allow clients to communicate with the overall process
- `vnet`
    + the network stack (the largest piece)
    + a static library
- `vpp`
    + the container app
- `vppapigen`
    + the api compiler
- `vpp-api-test`
    + the canonical binary api client
- `vppinfra`
    + the fundation libraries
    + e.g. dynamic arrays, vectors, spin maps, hashes (of various sorts), etc.
- `vpp-japi`
    + vpp java binding

# Initialization

One of the highlights of VPP is that it naturally supports plugins. Here we are going to take a quick journey through the initialization of the plugins.

## /vlib/init.h

Initialization is implemented on basis of _constructor function_, as is defined in `src/vlib/init.h`, so that the plugins will be included just as main line codes.

```c
/* Declaration is global (e.g. not static) so that init functions can
   be called from other modules to resolve init function depend. */

#define VLIB_DECLARE_INIT_FUNCTION(x, tag)                      \
vlib_init_function_t * _VLIB_INIT_FUNCTION_SYMBOL (x, tag) = x; \
static void __vlib_add_##tag##_function_##x (void)              \
    __attribute__((__constructor__)) ;                          \
static void __vlib_add_##tag##_function_##x (void)              \
{                                                               \
 vlib_main_t * vm = vlib_get_main();                            \
 static _vlib_init_function_list_elt_t _vlib_init_function;     \
 _vlib_init_function.next_init_function                         \
    = vm->tag##_function_registrations;                         \
  vm->tag##_function_registrations = &_vlib_init_function;      \
 _vlib_init_function.f = &x;                                    \
}

#define VLIB_INIT_FUNCTION(x) VLIB_DECLARE_INIT_FUNCTION(x,init)
```

## /vpp/vnet/main.c

Then, by default, main heap will be created and allocated to vnet and plugins. And then `vlib_unix_main (argc, argv)` will be called.

```c
defaulted:

  /* Set up the plugin message ID allocator right now... */
  vl_msg_api_set_first_available_msg_id (VL_MSG_FIRST_AVAILABLE);

  /* Allocate main heap */
  if (clib_mem_init (0, main_heap_size))
    {
      vm->init_functions_called = hash_create (0, /* value bytes */ 0);
      vpe_main_init (vm);
      return vlib_unix_main (argc, argv);
    }
  else
    {
      {
    int rv __attribute__ ((unused)) =
      write (2, "Main heap allocation failure!\r\n", 31);
      }
      return 1;
    }
}
```

## /vlib/unix/main.c

```c
int
vlib_unix_main (int argc, char *argv[])
{
  vlib_main_t *vm = &vlib_global_main;  /* one and only time for this! */
  unformat_input_t input;
  clib_error_t *e;
  int i;

  vm->argv = (u8 **) argv;
  vm->name = argv[0];
  vm->heap_base = clib_mem_get_heap ();
  ASSERT (vm->heap_base);

  unformat_init_command_line (&input, (char **) vm->argv);
  if ((e = vlib_plugin_config (vm, &input)))
    {
      clib_error_report (e);
      return 1;
    }
  unformat_free (&input);

  i = vlib_plugin_early_init (vm);
  if (i)
    return i;

  unformat_init_command_line (&input, (char **) vm->argv);
  if (vm->init_functions_called == 0)
    vm->init_functions_called = hash_create (0, /* value bytes */ 0);
  e = vlib_call_all_config_functions (vm, &input, 1 /* early */ );
  if (e != 0)
    {
      clib_error_report (e);
      return 1;
    }
  unformat_free (&input);

  vlib_thread_stack_init (0);

  __os_thread_index = 0;
  vm->thread_index = 0;

  i = clib_calljmp (thread0, (uword) vm,
            (void *) (vlib_thread_stacks[0] +
                  VLIB_THREAD_STACK_SIZE));
  return i;
}
```

### /vlib/unix/plugin.c

In the `vlib_unix_main` function, we can observe that there is a function called `vlib_plugin_early_init`, which is defined in `vlib/unix/plugin.c` and initialize plugin before `main()`. The following chunk of code basically allows plugins being added via CLI.

```c
int
vlib_plugin_early_init (vlib_main_t * vm)
{
  plugin_main_t *pm = &vlib_plugin_main;

  if (pm->plugin_path == 0)
    pm->plugin_path = format (0, "%s%c", vlib_plugin_path, 0);

  clib_warning ("plugin path %s", pm->plugin_path);

  pm->plugin_by_name_hash = hash_create_string (0, sizeof (uword));
  pm->vlib_main = vm;

  return vlib_load_new_plugins (pm, 1 /* from_early_init */ );
}
```

### unformat_init_command_line

These following lines basically take the command line and assign values to different variables. If necessary, configuration functions will be called. 

```c
  unformat_init_command_line (&input, (char **) vm->argv);
  if (vm->init_functions_called == 0)
    vm->init_functions_called = hash_create (0, /* value bytes */ 0);
  e = vlib_call_all_config_functions (vm, &input, 1 /* early */ );
  if (e != 0)
    {
      clib_error_report (e);
      return 1;
    }
  unformat_free (&input);
```

### vlib_thread_stack_init

The following important concept is the _thread stack_. The following lines basically initialize the thread stack and assign the os thread as _0_ index.

```c
  vlib_thread_stack_init (0);

  __os_thread_index = 0;
  vm->thread_index = 0;
```

### clib_calljmp

```c
  i = clib_calljmp (thread0, (uword) vm,
            (void *) (vlib_thread_stacks[0] +
                  VLIB_THREAD_STACK_SIZE));
```

This function will then lead us to our initialized thread, which is called now `thread0`. If we take a closer look into this `thread0`, it will finally lead us to an interesting function called `vlib_main`, which located in `/vlib/main.c`.

### vlib_main

In this function, all static nodes are registered by `vlib_register_all_static_nodes`, all subsystems are configured by `vlib_call_all_config_functions`, and there is one last chance for some systems to do the last-minute setup and configuration in order to get ready and dive into the main loop with `vlib_call_all_main_loop_enter_functions`.

And finally, this function leads to the `vlib_main_loop`, which is the main loop for our `thread0`.

```c
/* Main function. */
int
vlib_main (vlib_main_t * volatile vm, unformat_input_t * input)
{
  clib_error_t *volatile error;
  vlib_node_main_t *nm = &vm->node_main;

  vm->queue_signal_callback = dummy_queue_signal_callback;

  clib_time_init (&vm->clib_time);

  /* Turn on event log. */
  if (!vm->elog_main.event_ring_size)
    vm->elog_main.event_ring_size = 128 << 10;
  elog_init (&vm->elog_main, vm->elog_main.event_ring_size);
  elog_enable_disable (&vm->elog_main, 1);

  /* Default name. */
  if (!vm->name)
    vm->name = "VLIB";

  if ((error = unix_physmem_init (vm)))
    {
      clib_error_report (error);
      goto done;
    }

  if ((error = vlib_buffer_main_init (vm)))
    {
      clib_error_report (error);
      goto done;
    }

  if ((error = vlib_thread_init (vm)))
    {
      clib_error_report (error);
      goto done;
    }

  /* Register static nodes so that init functions may use them. */
  vlib_register_all_static_nodes (vm);

  /* Set seed for random number generator.
     Allow user to specify seed to make random sequence deterministic. */
  if (!unformat (input, "seed %wd", &vm->random_seed))
    vm->random_seed = clib_cpu_time_now ();
  clib_random_buffer_init (&vm->random_buffer, vm->random_seed);

  /* Initialize node graph. */
  if ((error = vlib_node_main_init (vm)))
    {
      /* Arrange for graph hook up error to not be fatal when debugging. */
      if (CLIB_DEBUG > 0)
    clib_error_report (error);
      else
    goto done;
    }

  /* See unix/main.c; most likely already set up */
  if (vm->init_functions_called == 0)
    vm->init_functions_called = hash_create (0, /* value bytes */ 0);
  if ((error = vlib_call_all_init_functions (vm)))
    goto done;

  /* Create default buffer free list. */
  vlib_buffer_create_free_list (vm, VLIB_BUFFER_DEFAULT_FREE_LIST_BYTES,
                "default");

  nm->timing_wheel = clib_mem_alloc_aligned (sizeof (TWT (tw_timer_wheel)),
                         CLIB_CACHE_LINE_BYTES);

  vec_validate (nm->data_from_advancing_timing_wheel, 10);
  _vec_len (nm->data_from_advancing_timing_wheel) = 0;

  /* Create the process timing wheel */
  TW (tw_timer_wheel_init) ((TWT (tw_timer_wheel) *) nm->timing_wheel,
                0 /* no callback */ ,
                10e-6 /* timer period 10us */ ,
                ~0 /* max expirations per call */ );

  vec_validate (vm->pending_rpc_requests, 0);
  _vec_len (vm->pending_rpc_requests) = 0;

  switch (clib_setjmp (&vm->main_loop_exit, VLIB_MAIN_LOOP_EXIT_NONE))
    {
    case VLIB_MAIN_LOOP_EXIT_NONE:
      vm->main_loop_exit_set = 1;
      break;

    case VLIB_MAIN_LOOP_EXIT_CLI:
      goto done;

    default:
      error = vm->main_loop_error;
      goto done;
    }

  if ((error = vlib_call_all_config_functions (vm, input, 0 /* is_early */ )))
    goto done;

  /* Call all main loop enter functions. */
  {
    clib_error_t *sub_error;
    sub_error = vlib_call_all_main_loop_enter_functions (vm);
    if (sub_error)
      clib_error_report (sub_error);
  }

  vlib_main_loop (vm);

done:
  /* Call all exit functions. */
  {
    clib_error_t *sub_error;
    sub_error = vlib_call_all_main_loop_exit_functions (vm);
    if (sub_error)
      clib_error_report (sub_error);
  }

  if (error)
    clib_error_report (error);

  return 0;
}
```

### vlib_main_loop

There are two loop functions defined in `/vlib/main.c`, namely, `vlib_main_loop` and `vlib_worker_loop`, whose difference is kind of obvious. Here, we are mainly talking about the former one, that is `vlib_main_loop`.

```c
static void
vlib_main_loop (vlib_main_t * vm)
{
  vlib_main_or_worker_loop (vm, /* is_main */ 1);
}

void
vlib_worker_loop (vlib_main_t * vm)
{
  vlib_main_or_worker_loop (vm, /* is_main */ 0);
}
```

If we dive inside the `vlib_main_or_worker_loop`, we can observe the mainstream concepts inside a `while(1)` loop one after another:

- Pre-Input Node
    + epoll / pop ...
    + file io / interrupt ...
- input
    + getting packets into the graph
- queue_signal_pending
    + shoulder tap the main thread
- service pending interrupts
- timer_wheel / processes
    + Check if process nodes have expired from timing wheel 
- pending frames
    + 97% of vector processing is done in the loop here
    + vector packets + **dispatching function** (can be interesting to look into cause we can see how a node is dispatched according to the `time_stamp`, how things can be different with different `CLI_DEBUG` value, and how each node is processing its received packets.) 

# Reference

- \[1\] - [PreLaunch Summit Day2 - Chapter 1 - VPP initialization](https://www.youtube.com/watch?v=D4_PBAaVmco&t=0s&list=PLWHpG2-3ZXXsomIWyDG1L72eLXay0Z1Yu&index=1)