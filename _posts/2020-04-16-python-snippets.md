---
title: Python Utils & Snippets
date: 2020-04-16
description: Some util functions, classes, or just snippets, aiming at improving dev efficiency.
categories:
- Python
tags:
- Python
photos:
---

# Outline<a name="outline"></a>

- [File/IO](#io)
- [Pandas](#pandas)
- [Matplotlib](#matplotlib)
- [Seaborn](#seaborn)
- [NetworkX](#networkx)
- [Multiprocessing](#multiprocess)
- [Json](#json)
- [Class](#class)
- [Wrapper](#wrap)
- [Subprocess](#subprocess)
- [Log](#log)
- [Cordinate](#cordinate)
- [Timestamp](#timestamp)
- [Pickle](#pickle)
- [Dictionary](#dict)
- [Curl](#curl)
- [Inspect](#inspect)

# File/IO<a name="io"></a>

```python
import os

# @desc:   recursively get files given a directory
# @params: (str)path
# @return: list of directories
def get_files_r(path):
    files = []
    for root, directories, filenames in os.walk(path):
        for filename in filenames: 
            files.append(os.path.join(root,filename))
    return [f for f in files if '/.' not in f]

# @desc:   read lines from a file into a list
# @params: (str)filename
# @return: list of strings
def read_file(filename):
    lines = []
    with open (filename, "r") as myfile:
        lines = [line.rstrip('\n') for line in myfile]
    return lines

# @desc:    write lines from a list of strings to a file
# @params:  (str) filename
#           (list[str]) lines
def write_file(lines, filename):
    with open (filename, "w") as f:
        for l in lines:
            f.write("{}\n".format(l))

# @desc:   filter list of strings containing keyword
# @params: lines - list of strings
#          key - keyword
# @return: list of strings
# @e.g.:   ['blah key', 'blah', 'blah key blah'] -> ['blah key', 'blah key blah']
def filter_line_keyword(lines, key):
    return [l for l in lines if key in l]

# @desc:   replace keyword in a list of strings to a target string
# @params: lines - list of strings
#          key - keyword
#          target - target string
# @return: list of strings
# @e.g.:   ['blah key', 'blah', 'blah key blah'] -> ['blah target', 'blah', 'blah target blah']
def replace_keyword2target(lines, key, target):
    return [l.replace(key, target) for l in lines]

# @desc:   split each element of input (strings) by keyword and merge them again 
# @params: lines - list of strings
#          key - keyword
# @return: list of strings
# @e.g.:   ['blah1 key blah1 key blah1', 'blah2 key blah2'] -> ['blah1 ', ' blah1 ', ' blah1', 'blah2 ', ' blah2']
def split_merge_string(lines, key):
    res = []
    for l in lines:
        res += (l.split(key))
    return res

'''
put all items in dictionary into list
'''
def dict2list(_dict):
    return [_dict[k] for k in _dict.keys()]

'''
create a list of folders if not yet exists
@param:
    _dir - list of folders e.g. ['./foo', './foo/bar']
'''
def create_folder(_dir):
    for d in _dir:
        if not os.path.exists(d):
            os.mkdir(d)
```

To import files from a specific directory:

```py
# some_file.py
import sys
sys.path.insert(0, '/path/to/application/app/folder')

import file
```

To check if file exists or not:
```py
if os.path.exists(filename):
    df = pd_dataframe_from_csv(filename)
else:
    print("file {} doesn't exist".format(filename))
```

# Pandas<a name="pandas"></a>

```python
import pandas as pd
pd.set_option('display.max_columns', 500) # change dataframe display range
pd.set_option('display.max_rows', 500) # change dataframe display range

# @desc:   create pandas dataframe from 2d array
# @params: array2d - 2d array w/ features by column and instances by row
#          col_name - list of feature name for columns
#          col_type - list of data type for columns
# @return: dataframe
def pd_dataframe_from_2darray(array2d, col_name, col_type):
    res = pd.DataFrame(np.array(array2d), columns=col_name, dtype='str')
    for i, col in enumerate(res.columns):
        res[col] = res[col].astype(col_type[i])
    return res

# @desc:   dealing with pandas dataframe - filter rows that fall within a time interval [t0, t1]
#          !need to have a column called time which is astype('datetime64[ns]')
# @params: df - pandas dataframe
#          t0 - begin of time interval (pd.Timestamp)
#          t1 - end of time interval (pd.Timestamp)
# @return: pandas dataframe
def pd_filter_time_interval(df, t0, t1):
    t0 = pd.Timestamp(t0)
    t1 = pd.Timestamp(t1)
    assert(t0 < t1) # check timeline
    res = df[df['time'] >= t0]
    res = res[res['time'] <= t1]
    return res

# @desc:   read csv file into pandas dataframe
# @params: filename
# @return: pandas dataframe
def pd_dataframe_from_csv(filename, sep=','):
    return pd.read_csv(filename, sep=sep, encoding='utf-8')

# @desc:   read csv file without header into pandas dataframe
# @params: filename
#          col_name - list of feature name for columns
#          col_type - list of data type for columns
# @return: pandas dataframe
def pd_dataframe_from_csv_no_header(filename, col_name, col_type, sep=','):
    res = pd.read_csv(filename, header=None, sep=sep, encoding='utf-8', names=col_name)
    for i, col in enumerate(res.columns):
        res[col] = res[col].astype(col_type[i])
    return res

# @desc:   output pandas dataframe to csv files
# @params: df - pandas dataframe
#          filename
def pd_dataframe_to_csv(df, filename, sep=',', index=False):
    df.to_csv(filename, sep=sep, encoding='utf-8', index=index)

'''
alist.sort(key=natural_keys) sorts in human order
http://nedbatchelder.com/blog/200712/human_sorting.html
(See Toothy's implementation in the comments)
'''
def natural_keys(text):
    def atoi(text):
        return int(text) if text.isdigit() else text
    return [ atoi(c) for c in re.split(r'(\d+)', text) ]

# @desc:   read csv files in a directory into pandas dataframe
#          !caution: make sure that all csv files in the directory has the same layout
# @params: path - path to the directory
# @return: pandas dataframe
def pd_dataframe_from_csv_dir(path):
    files = []
    for root, _, filenames in os.walk(path):
        for filename in filenames: 
            files.append(os.path.join(root,filename))
    files = [f for f in files if '/.' not in f and '.csv' in f]
    files.sort(key=natural_keys) # sort the files according to the number in the string
    print(files)
    dfs = []
    for f in files:
        print(f)
        df = pd_dataframe_from_csv(f)
        dfs.append(df)
    return pd.concat(dfs)

'''
get time difference between two pd.TimeStamps
@param:
    t0/t1 : pd.Timestamps
        start/end time
@return
    minute difference between t0 and t1
'''
import time
def get_time_difference_minute(t0, t1):
    d1_ts = time.mktime(t0.timetuple())
    d2_ts = time.mktime(t1.timetuple())
    # They are now in seconds, subtract and then divide by 60 to get minutes.
    return int(d2_ts-d1_ts) / 60
```

To get rows with certain conditions:

```py
df.loc[(df['isp'] == 'ntt') & df['rtt'] >= 200]
```

To get distribution of one column:

```py
# Get the meaning of each emotion index
emotion_cat = {0:'Angry', 1:'Disgust', 2:'Fear', 3:'Happy', 4:'Sad', 5:'Surprise', 6:'Neutral'}

# See the target distribution (check for imbalance)
target_counts = data['emotion'].value_counts().reset_index(drop=False)
target_counts.columns = ['emotion', 'number_samples']
target_counts['emotion'] = target_counts['emotion'].map(emotion_cat)
target_counts

# output
	emotion	number_samples
0	Happy	8989
1	Neutral	6198
2	Sad	6077
3	Fear	5121
4	Angry	4953
5	Surprise	4002
6	Disgust	547

```

To check if there is any `nan` in a df:

```py
df.isnull().values.any()
# True if nan exists
```

To check whether a row or a column contains a `nan`:

```py
df.isnull().any(axis=0) # columns, axis=1 for rows
```

To list all rows which contains `nan`:

```py
df[df.isnull().any(axis=1)]
```

To pretty print a DataFrame:

```python
from tabulate import tabulate

df = pd.DataFrame({'col_two' : [0.0001, 1e-005 , 1e-006, 1e-007],
                   'column_3' : ['ABCD', 'ABCD', 'long string', 'ABCD']})
print(tabulate(df, headers='keys', tablefmt='psql'))

+----+-----------+-------------+
|    |   col_two | column_3    |
|----+-----------+-------------|
|  0 |    0.0001 | ABCD        |
|  1 |    1e-05  | ABCD        |
|  2 |    1e-06  | long string |
|  3 |    1e-07  | ABCD        |
+----+-----------+-------------+
```



# Matplotlib<a name="matplotlib"></a>

Color pallete:

<div align="center">
<img src="plot/color_pallete.png" alt=""/>
(Color Pallete)
</div>

```python
COLORS = ["#3978af", 
          "#559d3f",
          "#d1342b",
          "#ef8532", 
          "#634295", 
          "#a65d34", 
          "#aecde1", 
          "#b4dc93",
          "#ee9e9b", 
          "#f4c07b", 
          "#c6b4d3",
          "#fffea6",
          "#df3583"]
```

```python
'''
@desc save figure to a file
@params:
    f : matplotlib figure
    name : string
        name of the saved figure, recommended to use '*.pdf'
    transparent : (default=True)
        decides whether the background is transparent
'''
def plt_savefig(f, name, transparent=True):
    f.savefig(name, bbox_inches='tight', transparent=transparent)
```

Plot CDF:

```py
num_bins = 20
counts, bin_edges = np.histogram(my_array, bins=num_bins, normed=True)
cdf = np.cumsum(counts)
plt.plot(bin_edges[1:], cdf)
```

Better CDF plot w/ percentiles and semilogx:

```py
# plot with data as the data source
percentiles = [np.percentile(data, p) for p in range(0, 110, 10)]
plt.semilogx(np.sort(data), np.linspace(0, 1, len(data), endpoint=False), color='r', label='kvmp3')
plt.semilogx(percentiles, np.linspace(0, 1, 11, endpoint=True), 'x', color='r')
```



# Seaborn<a name="seaborn"></a>

```py
import seaborn as sns
sns.set(style="ticks")
sns.set_style({"xtick.direction": "in","ytick.direction": "in","xtick.top":True,"ytick.right":True,"axes.grid":True})

# Load an example dataset with long-form data
fmri = sns.load_dataset("fmri")

# Plot the responses for different events and regions
f = sns.lineplot(x="timepoint", y="signal",
             hue="region", style="event",
             data=fmri)

# To save figure as pdf
f.figure.savefig('blah.pdf')

# To check current style
sns.axes_style()
```

To plot pairplots and check correlationship:

```py
def corrfunc(x,y, ax=None, **kws):
    """Plot the correlation coefficient in the top left hand corner of a plot."""
    r, p = pearsonr(x, y)
    ax = ax or plt.gca()
    # Unicode for lowercase rho (ρ)
    rho = '\u03C1'
    ax.annotate('{}={:.4f} p={:.4f}'.format(rho, r, p), xy=(.1, .9), xycoords=ax.transAxes)

g = sns.pairplot(data2check, kind='reg', plot_kws={'line_kws':{'color':'red'}, 'scatter_kws': {'alpha': 0.01}})
g.map_lower(corrfunc)
plt.show()
```

# NetworkX<a name="networkx"></a>

```python
'''
@desc: generate directed network from pandas edge list
@param:
    df: pd.DataFrame that contains all the edge information
    src: column name of the src node for each edge
    dst: column name of the dst node for each edge
    attr: column name of the edge attributes
@return:
    G: a directed graph
@example:
    Given a dataframe like:
    >> df.head()
        | site1 | site2 | rtt_avg | rtt_std | n_log |
        +-------+-------+---------+---------+-------+ 
        |  yyz  |  pao  |  10.15  |  0.259  |  110  |
        |  ...  |  ...  |   ...   |   ...   |  ...  |
    We can put:
    G = nx_get_di_graph_from_df(df, 'site1', 'site2', ['rtt_avg', 'rtt_std', 'n_log'])
'''
def nx_get_di_graph_from_df(df, src, dst, edge_attr=['']):
    return nx.from_pandas_edgelist(df, src, dst, edge_attr=edge_attr, create_using=nx.DiGraph())

'''
plot networkx directed graph
@param:
    G : graph
        A networkx graph
    layout : type of layout that determines node positions
        support "spring_layout" and "kamada_kawai_layout"
    edge_weight : name of one edge_attr of G (default=True)
        will be used for "kamada_kawai_layout" to calculate node positions
    arrows : bool, optional (default=True)
        For directed graphs, if True draw arrowheads.
        Note: Arrows will be the same color as edges.
    
@return: figure
@src: https://networkx.github.io/documentation/stable/_modules/networkx/drawing/nx_pylab.html#draw_networkx
'''
def nx_draw_graph(G, layout="spring", edge_weight=None, seed=None, arrows=True, dpi=96, fig_width=800, fig_height=800, node_color='#3978af', node_alpha=1., edge_color='#ef8532', edge_width=1., edge_alpha=1.):
    f = plt.figure(figsize=(fig_height/my_dpi, fig_height/my_dpi), dpi=dpi)
    nodes = list(G.nodes)
    edges = list(G.edges)
    degrees = [G.degree(nodes)[n] * 20 for n in nodes]
    if layout == "spring":
        pos = nx.spring_layout(G, seed=seed)
    elif layout == "kawai" and if edge_weight
        pos = nx.kamada_kawai_layout(G, weight=edge_weight)
    else:
        print("@Warning: No layout matched...")
        return
    nx.draw_networkx(G, pos, nodes=nodes, edges=edges, arrows=arrows, node_size=degrees, node_alpha=node_alpha, node_color=node_color, edge_color=edge_color, width=edge_width, edge_alpha=edge_alpha)

    plt.axis('off')
    plt.show()
    return f
```

To calculate shortest path:

```py
nx.shortest_path(G, weight='name_of_edge_attr')
```

To get all edge attributes between 2 nodes:

```py
G['node1']['node2']
```

# Multiprocessing<a name="Multiprocessing"></a>

An example is shown below:

```py
'''
From parse_dt_multi_thread.ipynb
'''
import multiprocessing

# ...

def chunk2csv_dt(dt, chunk_id):
    chunk_id = "chunk" + str(chunk_id)
    filename = os.path.join("data", chunk_id + ".csv")
    df = cie_pd_read_csv(filename)
    dirs = ["dt", os.path.join("dt", "chunk"+chunk_id), os.path.join("dt", "chunk"+chunk_id, str(dt))]
    create_folder(dirs)
    rt = dirs[-1]
    t0 = df.time.iloc[0]
    t1 = t0 + pd.Timedelta(minutes=1)
    T = get_time_difference_min(t0, df.time.iloc[-1])
    cnt = 0
    with open('{}_dt_{}.log'.format(chunk_id, dt), 'a') as the_file:
        while t1 < df.time.iloc[-1]:
            cnt += 1
            str2print = "progress {:.2%}: from {} to {}\r".format(cnt/T, t0, t1)
            print(str2print)
            the_file.write(str2print)
            data_dt = cie_get_dt_dict(df, t0, t1)
            t0 = t1
            t1 += pd.Timedelta(minutes=1)
            data_dt.to_csv(os.path.join(rt, str(t0)+".csv"), sep='\t', encoding='utf-8', index=False)

if __name__ == '__main__':
    jobs = []
    for i in range(1, 44):
        p = multiprocessing.Process(target=chunk2csv_dt, args=(10,i,))
        jobs.append(p)
        p.start()
```

# Json<a name="json"></a>

```py
import json

'''
write data to json file
'''
def json_write2file(data, filename):
    with open(filename, "w") as write_file:
        json.dump(data, write_file, indent=4) # w/ indent the file looks better

'''
read data from json file
'''
def json_read_file(filename):
    with open(filename, "r") as read_file:
        return json.load(read_file)
```

Reference: [working w/ json data in python](https://realpython.com/python-json/)

# Class<a name="class"></a>

## Inheritance

```python
class BasicConfig(object):
    # this init function will take whatever input as its attribute
    def __init__(self, **kwargs):
        self.__dict__.update({k: v for k, v in kwargs.items() if k != 'self'})
        
class PathConfig(BasicConfig):
    
    def __init__(self,
                 root, # root directory
                 loop  # loop directory where image file will be mounted
                ):
        BasicConfig.__init__(self, **{k: v for k, v in locals().items() if k != 'self'})
        self.vpp = join(self.root, 'vpp') # store vpp deb files and plugin files
        self.img = join(self.root, 'img') # store image files
        self.tmp = join(self.root, 'tmp') # tmp folder
        self.orig_img = join(self.img, 'vpp_wiki_orig.img') # original image file
        self.base_img = join(self.img, 'vpp_wiki_base.img') # base image file
        
class NetConfig(BasicConfig):
    
    def __init__(self,
                 sv4_vip,      # server vip v4 addr
                 sv6_vip,      # server vip v6 addr
                 lb4_vip_px,   # lb vip v4 addr prefix
                 lb6_vip_px,   # lb vip v6 addr prefix
                 br4_px,       # bridge ip v4 prefix
                 br6_px,       # bridge ip v6 prefix
                 tap4_px,      # tap interface ip v4 prefix
                 tap6_px,      # tap interface ip v6 prefix
                 mgmt_px,      # management interface ip prefix
                 lb_mgmt_port, # lb node management (ssh) port baseline 
                 sv_mgmt_port, # server node management (ssh) port baseline 
                 bridge,       # name of the bridge that links all lbs and servers
                 mgmt_bridge,  # name of the management bridge that links all lbs and servers
                ):
        BasicConfig.__init__(self, **{k: v for k, v in locals().items() if k != 'self'})
```

## Variable w/ Constraint

```python
class IP4Range:
    def __get__(self, instance, owner):
        return instance.__dict__[self.name]
    def __set__(self, instance, value):
        if value < 0 or value >= 254:
            raise ValueError('Does not fit ip4 suffix format (range [0-254)).')
        instance.__dict__[self.name] = value
    def __set_name__(self, owner, name):
        self.name = name

class NodeConfig(object):
    id = IP4Range()
    
    def __init__(self, **kwargs):
        keep_list = ['id', 'n_peer', 'tap4_px', 'tap6_px', 'mgmt_port']
        self.__dict__.update({k: v for k, v in kwargs.items() if k in keep_list})
        self.peer_id_list = [i for i in range(self.n_peer)]
        self.ip4_addr = '{}.{}'.format(kwargs['tap4_px'], kwargs['v4_sx']) # tap int v4 addr
        self.ip6_addr = '{}:{}'.format(kwargs['tap6_px'], kwargs['v6_sx']) # tap int v6 addr
        self.mgmt_ip = '{}.{}'.format(kwargs['mgmt_px'], kwargs['v4_sx']) # mgmt int v4 addr
        vip4_px = kwargs['vip4_px']
        vip6_px = kwargs['vip6_px']
        self.vip4_addr = '{}.{}.1'.format(vip4_px, kwargs['v4_sx']) if vip4_px.count('.') < 3 else vip4_px # vip v4 addr
        self.vip6_addr = '{}:{}::beef'.format(vip6_px, kwargs['v6_sx']) if '::' not in vip6_px else vip6_px # vip v6 addr
```

## Get Class as Dict

```python
class SV_Node(NodeConfig):
    
    def __init__(self,
                 sv_id, # id of this server node
                 n_lb, # number of lb nodes
                 net_conf, # NetConfig
                ):
        v4_sx = '{:d}'.format(sv_id + 1) # ip v4 suffix
        v6_sx = '{:04x}'.format(sv_id + 1) # ip v6 suffix
        self.hostname = 'node_server_{}'.format(sv_id)
        NodeConfig.__init__(self,
                            id=sv_id,
                            n_peer=n_lb,
                            tap4_px=net_conf.tap4_px,
                            tap6_px=net_conf.tap6_px,
                            mgmt_px=net_conf.mgmt_px,
                            vip4_px=net_conf.sv4_vip,
                            vip6_px=net_conf.sv6_vip,
                            v4_sx=v4_sx,
                            v6_sx=v6_sx,
                            mgmt_port=net_conf.sv_mgmt_port+sv_id
                           )
        self.br4_addr = '{}.{}'.format(net_conf.br4_px, 1) # br int v4 addr
        self.br6_addr = '{}:{}'.format(net_conf.br6_px, 1) # br int v6 addr


    def __getitem__(self, key):
        return getattr(self, key)
    
    def keys(self):
        return('id', 'hostname', 'n_peer', 'peer_id_list', 'mgmt_port',
               'ip4_addr', 'ip6_addr', 'br4_addr', 'br6_addr', 
               'mgmt_ip', 'vip4_addr', 'vip6_addr')
```

Then when calling `dict(SV_Node_Instance)` we will get the fields as listed in the keys.

## Get Nested Class Instance as Dict

```python 
class Config:
    
    def __init__(self,
                 n_lb, # number of load balancer nodes
                 n_sv, # number of server nodes
                 path_conf, # path config param
                 net_conf # net config param
                ):
        self.n_lb = n_lb
        self.n_sv = n_sv
        self.path = PathConfig(**path_conf)
        self.net = NetConfig(**net_conf)
        self.lb_nodes = [LB_Node(i, n_sv, self.net) for i in range(n_lb)]
        self.sv_nodes = [SV_Node(i, n_lb, self.net) for i in range(n_sv)]
    
    def to_dict(self):
        return {k: (v.__dict__ if isinstance(v, BasicConfig) 
                    else [dict(_) for _ in v] if isinstance(v, list) 
                    else v) 
                for k, v in self.__dict__.items()}
        
    def to_json(self):
        with open('config.json', "w") as write_file:
            json.dump(self.to_dict(), write_file, indent=4)
```

# Wrapper<a name="wrap"></a>

```python
'''
function decorator
'''
def timetest(input_func):

    def timed(*args, **kwargs):

        start_time = time.time()
        result = input_func(*args, **kwargs)
        end_time = time.time()
        print(color.DARKCYAN + "Method Name - {0}, Args - {1}, Kwargs - {2}, Execution Time - {3}".format(
            input_func.__name__,
            args,
            kwargs,
            end_time - start_time
        ) + color.END)
        return result
    
    return timed
```

Put this wrapper in front of a function will print info as shown below:

```python
@timetest
def rebuild(from_orig=None):
    ...

rebuild(from_orig='-o')

>> Method Name - rebuild, Args - (), Kwargs - {'from_orig': '-o'}, Execution Time - 97.59658622741699
```

# Subprocess<a name="subprocess"></a>

The following function will excecute bash script and print out output.

```python
'''
execute command and print out result
'''
def subprocess_cmd(command):
    '''
    pretty print
    '''
    def pretty_print(_str, _color="green"):
        if (len(_str) != 0):
            print(colored(_str, _color))
    process = subprocess.Popen(command, stdout=subprocess.PIPE, shell=True)
    proc_stdout = process.communicate()[0].strip()
    pretty_print(proc_stdout.decode("utf-8"))
    return proc_stdout.decode("utf-8")
```

E.g.:

```python
'''
mount image: img
@param:
    img: image to be mounted
@e.g.:
    mount_image(CONFIG['img']['base'])
    # then run `sudo chroot /mnt/loop/ /bin/bash` to check something like:
    # `apt list --installed | grep vpp`
'''
def mount_image (img):
    global LOOP_DIR
    print(">> Mounting image: {}...".format(img), end='\r')
    cmd = "sudo modprobe nbd max_part=8;sudo qemu-nbd --connect=/dev/nbd0 {};sudo mount -o loop /dev/nbd0p1 {};\n".format(img, LOOP_DIR)
    subprocess_cmd(cmd)
    print(">> Mounting image done!")

mount_image('/home/yzy/testbed/img/vpp_wiki_base.img')

>> Formatting '/home/yzy/testbed/img/vpp_wiki_base.img', fmt=qcow2 size=53687091200 backing_file=/home/yzy/testbed/img/vpp_wiki_orig.img cluster_size=65536 lazy_refcounts=off refcount_bits=16
```

However, in case of running script in the background, just do:

```python
subprocess.Popen('command_here', shell=True)
```

# Log<a name="log"></a>

Sometimes, we run long projects on jupyter notebook, which makes it harder to track the printed information if we accidentally close the window.

```python

# Log
def setup_file_logger(log_file):
    hdlr = logging.FileHandler(log_file)
    formatter = logging.Formatter('%(asctime)s %(levelname)s %(message)s')
    hdlr.setFormatter(formatter)
    logger.addHandler(hdlr) logger
    logger.setLevel(logging.INFO)

def log(message):
    #outputs to Jupyter console
    print('{} {}'.format(datetime.now(), message))
    #outputs to file
    logger.info(message)

setup_file_logger('test.log')

while True:
    _h, _interval = get_hour_interval()
    if _h != h or _interval != interval:
        h = _h
        interval = _interval
        log("interval shifted! new h={}, interval={}".format(h, interval))
        for t in tasks_global['task_per_hour'][h][interval]['tasks']:
            t.create_ripe_request()
    time.sleep(1)
```

Or with a different format:

```python
import logging
import sys
import datetime

def init_logger(filename, logger_name):
    '''
    @brief:
        initialize logger that redirect info to a file just in case we lost connection to the notebook
    @params:
        filename: to which file should we log all the info
        logger_name: an alias to the logger
    '''

    # get current timestamp
    timestamp = datetime.datetime.utcnow().strftime('%Y%m%d_%H-%M-%S')
    
    logging.basicConfig(
        level=logging.INFO, 
        format='[%(asctime)s] %(name)s {%(filename)s:%(lineno)d} %(levelname)s - %(message)s',
        handlers=[
            logging.FileHandler(filename=filename),
            logging.StreamHandler(sys.stdout)
        ]
    )

    # Test
    logger = logging.getLogger(logger_name)
    logger.info('### Init. Logger {} ###'.format(logger_name))
    return logger
```

It can be combined with jupyter notebook's magic commands like `%timeit` and `capture`. In one cell:

```python
%%catpure out
%%timeit
a = 1+1
```

Then in the following cell:

```python
logger.info("capture & timeit: "+out.stdout)
```



# Cordinate<a name="cordinate"></a>

```py
'''
convert 26°12′16″S, 28°2′44″E to -26.2044, 28.0456
'''
def cordinate2float(lat, lon):
    # parse latitude
    N = 'N' in lat
    _ = lat.split('°')
    d = float(_[0])
    m = 0
    s = 0
    if '′' in _[1]:
        _ = _[1].split('′')
        m = float(_[0])
        if '″' in _[1]:
            _ = _[1].split('″')
            s = float(_[0])
    lat = (d + m / 60. + s / 3600.) * (1 if N else -1)
    # parse longitude
    W = 'W' in lon
    _ = lon.split('°')
    d = float(_[0])
    m = 0
    s = 0
    if '′' in _[1]:
        _ = _[1].split('′')
        m = float(_[0])
        if '″' in _[1]:
            _ = _[1].split('″')
            s = float(_[0])
    lon = (d + m / 60. + s / 3600.) * (-1 if W else 1)
    return lat, lon
```

# Timestamp<a name="timestamp"></a>

```py
from datetime import datetime
from time import mktime

'''
converts unix timestamp to readable datetime
'''
def unixtime2datetime(t):
    return datetime.utcfromtimestamp(t).strftime('%Y-%m-%d %H:%M:%S')

'''
converts string to datetime
@params:
    input: a string like "2019-05-28 00:00:00"
'''
def str2datetime(input):
    return datetime.strptime(input, '%Y-%m-%d %H:%M:%S')

'''
converts datetime to unix timestamp
'''
def datetime2unixtime(dt):


'''
get unix timestamp for d_min later from now (local time)
'''
def get_unix_timestamp_plus_minute(d_min):
    # offset = 2 * 60 * 60 # this offset will be important if you want to get UTC while in 
    return int(time.time() + d_min)
```

# Pickle<a name="pickle"></a> 

```py
import pickle

a = {'hello': 'world'}

with open('filename.pickle', 'wb') as handle:
    pickle.dump(a, handle, protocol=pickle.HIGHEST_PROTOCOL)

with open('filename.pickle', 'rb') as handle:
    b = pickle.load(handle)

print a == b
```

# Dictionary<a name="dict"></a>

```py
# Python code to merge dict using a single  
# expression 
def Merge(dict1, dict2): 
    res = {**dict1, **dict2} 
    return res
```

# Curl<a name='curl'></a>

To curl in python, we need to install `pycurl` first. An example is shown below, where I need to curl some json file and save them as a file.

```python
import pycurl
from io import BytesIO

def curl(url):
    buffer = BytesIO()
    c = pycurl.Curl()
    c.setopt(c.URL, url)
    c.setopt(c.WRITEDATA, buffer)
    c.perform()
    c.close()
    body = buffer.getvalue()
    return body.decode('iso-8859-1')

'''
write data to json file
'''
def json_write2file(data, filename):
    with open(filename, "w") as write_file:
        json.dump(data, write_file, indent=4) # w/ indent the file looks better

def save_measurement_result(hour, interval, mid):
    # get its filename
    measurement_url = 'https://atlas.ripe.net/api/v2/measurements/{}/?format=json'.format(mid)
    m = json.loads(curl(measurement_url))
    filename = '{}_{}.json'.format(m['type'], mid)
    # get result
    result_url = 'https://atlas.ripe.net/api/v2/measurements/{}/results/?format=json'.format(mid)
    result = json.loads(curl(result_url))
    json_write2file(result, os.path.join('./result', str(hour), str(interval), filename))
```

# Inspect<a name='inspect'></a>

To check arguments' names, we can use library `inspect`. 

```python
>>> inspect.getfullargspec(a_method)
(['arg1', 'arg2'], None, None, None)
```

The first element is what we want. The other results are the name of the *args and **kwargs variables, and the defaults provided. ie.

```python
>>> def foo(a, b, c=4, *arglist, **keywords): pass
>>> inspect.getfullargspec(foo)
(['a', 'b', 'c'], 'arglist', 'keywords', (4,))
```

Then the first list returned is the list of arguments that we normally want.

# Mujoco<a name='mujoco'></a>

To install Mujoco on Linux, besides getting a license:

- Install mujoco_py from source: `git clone https://github.com/openai/mujoco-py`, then `cd` into the folder, and `sudo pip install -e .`
- It might require to install these packages first: `sudo apt install libosmesa6-dev libgl1-mesa-glx libglfw3`
- And 
- Then install patchelf from source
- Install SOTA-RL-Algorithm requirements: `pip install -r requirements.txt`
- Install Lasagne: `pip install --upgrade https://github.com/Lasagne/Lasagne/archive/master.zip `
- Install pylocker: `pip install pylocker`

# Reference: 

- [Issue 147](https://github.com/openai/mujoco-py/issues/147)
- [Issue 284](https://github.com/openai/mujoco-py/issues/284)
- [Issue 374](https://github.com/openai/mujoco-py/issues/374)