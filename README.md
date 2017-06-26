# Modeling Trajectory with Recurrent Neural Networks

The source code of my paper.

Hao Wu, Ziyang Chen, Weiwei Sun, Baihua Zheng, Wei Wang, Modeling Trajectories with Recurrent Neural Networks. IJCAI 2017.

## Directory Structure
The directory structure may be as follows
```
workspace (e.g., /data)
    └ dataset_name1 (e.g., porto_6k)
        ├ data
        |   └ your_traj_file.txt
        ├ map
        |   ├ nodeOSM.txt
        |   └ edgeOSM.txt
        └ ckpt
            └ CSSRNN
                ├ dest_emb
                |   └ emb_50_hid_50_deep_1
                ├ dest_coord
                |   └ emb_200_hid_50_deep_1
                └ without_dest
                    └ emb_250_hid_350_deep_3

  
codespace
    ├ config
    ├ main.py
    ├ geo.py
    ├ trajmodel.py
    └ ngram_model.py
    
```

## Format of the Data
### Map/road network data
The map is recommended to be constructed from [OpenStreetMap](http://www.openstreetmap.org/export#).
You can get your own road network file from OpenStreetMap by selecting the rectangle area and export the file by, say, `Overpass API` (the first choice in the web page).
Then you will be prompted to download a data named `map` which is actually an XML-formatted file. 
By executing `OSMGen.cs` (you may have to maually set the file path in the code) on the raw map file, the `nodeOSM.txt` and `edgeOSM.txt` will be automatically extracted. 
The format of these two files are shown as follows.

#### nodeOSM.txt
Format: `[NodeID]`\t`[latitude]`\t`[longitude]`

One node/vertex per line with increasing (continuous) ids.
E.g.,
```
0   41.1689665  -8.6444747
1   41.1658735  -8.6444774
2   41.1670798  -8.6424338
3   41.1673856  -8.642543
4   41.1669776  -8.6417132
5   41.1676312  -8.6424866
...
1000   41.1575375  -8.6443184
```
, which records 1,000 vertices in the road network.

#### edgeOSM.txt
Format: `[EdgeId]`\t`[StartNodeId]`\t`[EndNodeId]`\t`[k]`\t`[lat1]`\t`[lon1]`\t`[lat2]`\t`[lon2]`...\t`[latk]`\t`[lonk]`

One edge (`StartNode` -> `EndNode`) per line with increasing (continuous) ids.
And `k` refers to the number of points of a polyline representing the shape of the road (including the start and the end node).
I.e., `(lat1, lon1)` is just the coordinate of `StartNode`, and `(latk, lonk)` is the coordinate of `EndNode`.

E.g.,
```
0   0   1326    5   41.1689665  -8.6444747  41.1688112  -8.6443785  41.1685579  -8.6440804  41.1683059  -8.6438068  41.1680768  -8.6437482
1   4   5   2   41.1669776  -8.6417132  41.1676312  -8.6424866
...
1500    1499    1494    2   41.1849529  -8.6317477  41.185196   -8.6318118
```
, which records 1,500 edges in the road network.
Edge `0` represents an edge from node `0` to node `1326` with `5` points representing the shape of the road as a 
polyline. And edge `1` represents an edge from node `4` to `5` with `2` points representing the shape, which means the 
shape of this road is a straight line segment (i.e., the first point`(41.1669776, -8.6417132)` is just the coordinate of 
node `4` and the second point `(41.1676312, -8.6424866)` is just the coordinate of node `5`).

### Trajectory data
The format of trajectory data is very simple. The data may like as follows,
```
1,2,4,6,8,12,7,23,
9,2,4,18,76,42,3,78,98,54,
432,214,678,532,3,5,74,13,123,67,4,
...
```
Each line records several edge ids in the road network, which represents a trajectory w.r.t. the definition introduced 
in the paper, i.e.,
>Definition 2 (Trajectory). 
A trajectory T in the form of r_1 → r_2 → … → r_k captures the movement of an object from r_1 to r_2 and so on to r_k 
along the road network G, where every two consecutive road segments are connected, i.e., \forall r_i, r_{i+1} ∈ T, 
r_i, r_{i+1}∈ E ∧ r_i.e = r_{i+1}.s.

## Environment and Package Dependencies
The code can be successfully run under following environments
- Python: py2/py3 compatible
- Tensorflow version: 12.0 (You may have to change some code if you want to use higher version of Tensorflow since some APIs have been changed after v1.0)
- OS: Linux (I've tried this code on Windows and there may occur some strange runtime problems.)

[TODO] I'll modify some code to make it compatible with newest API of Tensorflow.

The project will also need following package dependencies
- Numpy
- Matplotlib

## Usage
- Put the codes and the config file into the code space. Leave the trajectory data and the map data in the workspace
 following the directory structure as above.
- Modify `config` file
- Run main function in `main.py`   

### Details of configuration
All model settings are included in `config` file or can be set through the instance of `Config` class.
To run the model, the following settings are important and should be set according to your own dataset.
- `dataset_name`: give a name to your own dataset and put all stuffs w.r.t. this dataset into the directory named by 
this name (as the directory tree structure in the above section).
- `workspace`: the place you want to put all data in, e.g. `\home\data`. Note that you may have several datasets, 
e.g., with the names being, `dataset1`, `dataset2`, ... .
The directory may like as follows,
```
/home/data/dataset1/data
/home/data/dataset1/map
/home/data/dataset1/ckpt
 
/home/data/dataset2/data
/home/data/dataset2/map
/home/data/dataset2/ckpt
...
```
- `file_name`: set this as the name of your own trajectory file. If you follow the directory tree sturcture as above,
you may set the `file_name` as `data/trajectory.txt` since the file is located in the `data/` directory.

If you have correctly set these fields in the config, the model will be able to run with configuration printed in the screen.

To get the details of the remaining attributes of config, please refer to `main.py`. Each field is described in detail
 in comments.

