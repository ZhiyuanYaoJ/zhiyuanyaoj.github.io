---
title: A General Approach to Network Configuration Analysis
date: 2019-02-08
description: A paper a day keeps the doctor awake.
categories:
- a3d2kpt
tags:
- a3d2kpt
photos:
- /assets/images/X/PhD/a3d2kpt/net-config-analysis/workflow.png
---

* [Link2Paper](https://www.usenix.org/system/files/conference/nsdi15/nsdi15-paper-fogel.pdf)
* [Author: Ari Fogel et al.] 
* [Keywords: TCP, Congestion Control, Reinforcement Learning]

# Background

## Prior Approaches

1. Directly analyzes network configuration files (static analysis), which can flag errors proactively, before a new configuration is applied to the network, and which can naturally answer “what if” questions with respect to different environments
2. Analyzing the data plane snapshots (i.e., forwarding behavior) of the network so that any configuration error that causes undesirable forwarding can be precisely detected, because the data plane reflects the combined impact of all configuration aspects.

<p align="center">
  <img src="/assets/images/X/PhD/a3d2kpt/net-config-analysis/rel-work.png" alt="Approaches">
</p>

## Motivation

Their proposed solution takes both the configuration files and data-plane snapshots so that it can not only be proactive but also detect configuration error. They focus on the configuration-based paradigm (instead of SDN which has controllers) because it currently dominates and continues to be a cause of subtle errors.

# Contribution

## Batfish

### Workflow

<p align="center">
  <img src="/assets/images/X/PhD/a3d2kpt/net-config-analysis/workflow.png" alt="Workflow">
</p>

1. Define control plane model

Encoding the semantics of a range of low-level configuration directives in a high-level, declarative language.

<p align="center">
  <img src="/assets/images/X/PhD/a3d2kpt/net-config-analysis/cm.png" alt="Step 1">
</p>

2. Generate data plane (DP) model

Taking an environment (the up/down status of each link in the network as well as a set of route announcements from each of the network’s neighboring ASes) as an additional input, the data plane is derived by firing all rules iteratively to derive new facts, until no new facts are generated.

> The first two stages analyze all aspects of network configuration that are relevant to the data plane, irrespective of the correctness properties of interest. The resulting data plane thus faithfully captures the forwarding behavior induced by the given configuration, topology, and environment.
> 
> Limitations:
> 1. assume that routers behave as expected based on their configurations. We cannot catch errors due to bugs in router hardware or software (e.g., BGP implementation)
> 2. under a given set of environments, which are a subset of all possible environ- ments
> 3. may encounter configuration features that are currently not implemented (e.g., the internal ‘color’ metrics of Juniper) but may influence local route selection

3. Analyze DP and check correctness properties

Check any property expressible as a first-order-logic formula over the relations that represent one or more data planes of interest by translating the data-plane relations and the correctness property to the language of the Z3 constraint solver. Then either verify the property or provide one or more counterexamples, which consist of a concrete packet header and originating router.

4. Understand property violations and repair the network configuration

Logically simulating the behavior of counterexample packets through the network on top of logical data plane model.

### Implementation

Stage 1: Java + Antlr [[1]](#ref1) -> LogiQL
Stage 2: Excecute LogiQL program
Stage 3: Network Optimized Datalog (NoD) [[2]](#ref2) -> generate counterexample
Stage 4: Counterexample -> testflow -> LogicBlox populates relations with facts

## Evaluation

Net1: 75 nodes, including 3 tiers (provider w/ 2 routers default route, campus network w/ 21 routers eBGP and IBGP on border routers and OSPF internal, and 52 customers eBGP)
Net2: 17 nodes, only 1 AS speaking OSPF

<p align="center">
  <img src="/assets/images/X/PhD/a3d2kpt/net-config-analysis/eval.png" alt="Evaluation">
</p>

# Discussion

## Limitations

1. Evaluated networks are relatively simple and small (though already impressive)
2. No explicit experiment configuration is provided, hard to verify

## Related work

### Static analysis of network configuration

Rcc [[3]](#ref3) and IP Assure [[4]](#ref4) perform a range of checks that pertain to particular protocols or configuration aspects (e.g., the two ends of an OSPF link are in the same area, link MTUs match, the two ends of an encrypted tunnel use the same type of encryption-decryption). 

While violations identified by such static analysis tools likely represent poor practices, the tools cannot indicate whether or how violations impact the network’s forwarding. 

On the other hand, for a violation that occurs only in specific environments (e.g., when certain kinds of external routes are injected in the network), Batfish can detect it only when given a concrete instance of one of these environments, but a specialized tool for checking particular properties may be able to uncover such a violation even without these concrete inputs by leveraging specific characteristics of those properties.

### Model network behavior from configuration

Feamster et al. [[5]](#ref5) develop a tool to compute the outcome of BGP route selection for an AS. 

Xie et al. [[6]](#ref6) outline how to infer reachability sets, which are sets of packets that can be properly carried between a given source and destination node in the network.

Benson et al. [[7]](#ref7) extend this notion of reachability to assess the complexity of a network.

### Generate data plane from network configuration

The C-BGP [[8]](#ref8) and Cariden [[9]](#ref9) use an imperative, simulation-based approach, and focus on specific configuration aspects (BGP and traffic engineering, respectively).

### Analyze data plane snapshots

Anteater [[10]](#ref10) and Hassel [[11]](#ref11) analyze data plane snap-shots, obtained by pulling router FIBs and parsing portions of configuration that map directly to forwarding state (e.g., ACLs).

### Data plane analysis tools focus on SDN and faster computations

[[12]](#ref12), [[13]](#ref13), [[2]](#ref2), [[14]](#ref14).

# Ref

- \[1\] [ANTLR: A predicated‐LL(k) parser generator](https://courses.engr.illinois.edu/cs421/sp2012/project/antlr.pdf)<a name="ref1"></a>
- \[2\] [Checking Beliefs in Dynamic Networks](https://www.usenix.org/system/files/conference/nsdi15/nsdi15-paper-lopes.pdf)<a name="ref2"></a>
- \[3\] [Detecting BGP configuration faults with static analysis](https://www.usenix.org/events/nsdi05/tech/feamster/feamster.pdf)<a name="ref3"></a>, [Tool source code](https:// github.com/noise-lab/rcc/)
- \[4\] [Network Configuration Validation](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.210.3129&rep=rep1&type=pdf)<a name="ref4"></a>
- \[5\] [A model of BGP routing for network engineering](http://david.choffnes.com/classes/cs4700fa14/papers/whatifatron.pdf)<a name="ref5"></a>
- \[6\] [On static reachability analysis of IP networks](http://www.dtic.mil/get-tr-doc/pdf?AD=ADA487990)<a name="ref6"></a>
- \[7\] [Unraveling the Complexity of Network Management](https://www.usenix.org/event/nsdi09/tech/full_papers/benson/benson_html/)<a name="ref7"></a>
- \[8\] [Modeling the routing of an autonomous system with C-BGP](http://informatique.umons.ac.be/perso/Quoitin.Bruno/downloads/quoitin-ieee-network-2005.pdf)<a name="ref8"></a>
- \[9\] Cariden Technologies, Inc. IGP Traffic Engineer- ing Case Study, October 2002<a name="ref9"></a>
- \[10\] [Debugging the data plane with anteater](http://www.cs.cornell.edu/~ragarwal/pubs/anteater.pdf)<a name="ref10"></a>
- \[11\] [Header Space Analysis: Static Checking for Networks](https://www.usenix.org/sites/default/files/conference/protected-files/headerspace_nsdi.pdf)<a name="ref11"></a>
- \[12\] [Real Time Network Policy Checking Using Header Space Analysis](https://www.usenix.org/system/files/tech-schedule/nsdi13-proceedings.pdf#page=108)<a name="ref12"></a>
- \[13\] [Veriflow: Verifying network-wide invariants in real time](https://www.usenix.org/system/files/conference/nsdi13/nsdi13-final100.pdf)<a name="ref13"></a>
- \[14\] [Libra: Divide and Conquer to Verify Forwarding Tables in Huge Networks](http://enigma.usenix.org/sites/default/files/nsdi14_full_proceedings_interior.pdf#page=95)<a name="ref14"></a>