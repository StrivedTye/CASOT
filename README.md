# Class-agnostic Tracker for LiDAR Point Clouds
Pytorch-Lightning implementation of the Class-agonostic Tracker, based on Open3DSOT.  

### Setup
Installation
+ Create the environment
  ```
  git clone https://github.com/strivedtye/casot.git
  cd casot
  conda create -n casot  python=3.6
  conda activate casot
  ```
+ Install pytorch
  pytorch 1.7.0 + cuda 10.1

+ Install other dependencies:
  ```
  pip install -r requirement.txt
  ```
  Install the nuscenes-devkit if you use want to use NuScenes dataset:
  ```
  pip install nuscenes-devkit
  ```
  Install the torch-scatter
  ```
  pip install torch-scatter -f https://data.pyg.org/whl/torch-1.7.0+cu101.html
  ```
  Install the RoIAwarePooling
  ```
  cd ./roiaware_pool3d
  python setup.py develop
  ```
  

KITTI dataset
+ Download the data for [velodyne](http://www.cvlibs.net/download.php?file=data_tracking_velodyne.zip), [calib](http://www.cvlibs.net/download.php?file=data_tracking_calib.zip) and [label_02](http://www.cvlibs.net/download.php?file=data_tracking_label_2.zip) from [KITTI Tracking](http://www.cvlibs.net/datasets/kitti/eval_tracking.php).
+ Unzip the downloaded files.
+ Put the unzipped files under the same folder as following.
  ```
  [Parent Folder]
  --> [calib]
      --> {0000-0020}.txt
  --> [label_02]
      --> {0000-0020}.txt
  --> [velodyne]
      --> [0000-0020] folders with velodynes .bin files
  ```

NuScenes dataset
+ Download the dataset from the [download page](https://www.nuscenes.org/download)
+ Extract the downloaded files and make sure you have the following structure:
  ```
  [Parent Folder]
    samples	-	Sensor data for keyframes.
    sweeps	-	Sensor data for intermediate frames.
    maps	        -	Folder for all map files: rasterized .png images and vectorized .json files.
    v1.0-*	-	JSON tables that include all the meta data and annotations. Each split (trainval, test, mini) is provided in a separate folder.
  ```
>Note: We use the **train_track** split to train our model and test it with the **val** split. Both splits are officially provided by NuScenes. During testing, we ignore the sequences where there is no point in the first given bbox.

### Quick Start
#### Training
To train a model, you must specify the `.yaml` file with `--cfg` argument. The `.yaml` file contains all the configurations of the dataset and the model.

KITTI-Setting-1:
```bash
python main.py --cfg cfgs/P2B_Car.yaml --gpu 0 1 --category_name noCar --re_weight
```
KITTI-Setting-2:
```bash
python main.py --cfg cfgs/P2B_Car.yaml --gpu 0 1 --category_name noPed  --re_weight
```
After you start training, you can start Tensorboard to monitor the training process:
```
tensorboard --logdir=./lightning/version_[xx] --port=6006
```
By default, the trainer runs a full evaluation on the full test split after training every epoch. You can set `--check_val_every_n_epoch` to a larger number to speed up the training.

#### Testing
To test a trained model, specify the checkpoint location with `--checkpoint` argument and send the `--test` flag to the command.
```bash
python main.py --cfg cfgs/P2B_Car.yaml --gpu 0 1 --checkpoint /path/to/checkpoint/xxx.ckpt --test
```

### Results
|Trackers|KITTI Setting-1|     |KITTI Setting-2| |
|--------|---------|-----------|---------|-------|
|        |Observed |Unseen(Car)|Observed |Unseen(Pedestrian)|
|P2B-U   |38.6/57.4|32.4/41.0  |55.6/70.1|24.2/45.4|
|BAT-U   |33.9/49.8|24.9/33.7  |59.6/73.1|12.9/21.8|
|V2B-U   |52.1/74.7|47.8/59.6  |66.1/77.1|24.4/43.7|
|STNet-U |50.3/73.8|44.1/57.9  |66.9/78.9|27.1/46.3|
|MBPTrack-U |58.9/82.3|51.9/66.2  |69.63/81.97|25.69/44.15|

### Acknowledgment
+ This repo is built upon [Open3DSOT](https://github.com/HaozheQi/P2B).
