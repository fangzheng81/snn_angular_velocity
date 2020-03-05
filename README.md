# Event-Based Angular Velocity Regression with Spiking Networks

<p align="center">
  <img src="http://rpg.ifi.uzh.ch/img/papers/ICRA20_Gehrig.png" width="600"/>
</p>

This is the code for the paper **Event-Based Angular Velocity Regression with Spiking Networks** by Mathias Gehrig, Sumit Bam Shrestha, Daniel Mouritzen and [Davide Scaramuzza](http://rpg.ifi.uzh.ch/people_scaramuzza.html).

You can find a pdf of the paper [here](http://rpg.ifi.uzh.ch/docs/ICRA20_Gehrig.pdf).
If you use any of this code, please cite the following publication:

```bibtex
@Article{Gehrig20icra,
  author        = {Mathias Gehrig and Sumit Bam Shrestha and Daniel Mouritzen and Davide Scaramuzza},
  title         = {Event-Based Angular Velocity Regression with Spiking Networks},
  journal       = {{IEEE} International Conference on Robotics and Automation (ICRA)},
  url           = {http://rpg.ifi.uzh.ch/docs/ICRA20_Gehrig.pdf},
  year          = 2020
}
```

## Setup
Tested with:
- PyTorch 1.4.0
- torchvision 0.5.0
- strictyaml 1.0.6
- tqdm 4.43.0

using:
- Ubuntu 18.04.4 LTS
- gcc 7.4.0
- CUDA Tookit 10.0
- Nvidia Turing GPUs

### Data and Log Directory
```bash
git clone git@github.com:uzh-rpg/snn_angular_velocity.git
cd snn_angular_velocity
```
**Either** set up *data* and *logs* directory within the cloned repository with:
```bash
data_dir=$(pwd)
mkdir -p $data_dir logs/train logs/test
```
**or** set up an external *data* and *logs* directory with symbolic links:
```bash
data_dir="YOUR_DATA_PATH"
log_dir="YOUR_LOG_PATH"

mkdir -p $data_dir $log_dir/train $log_dir/test
ln -s $data_dir data
ln -s $log_dir logs
```
**or** pass the data and logging directories with arguments. For more info:
```bash
python test.py -h
```

### Installation with [Anaconda](https://www.anaconda.com/distribution/)
Adapt the CUDA toolkit version according to your setup. We also recommend to build PyTorch [from source](https://github.com/pytorch/pytorch#from-source) to avoid conflicts while compiling [SLAYER](https://github.com/bamsumit/slayerPytorch). However the following option might work for you as well:

#### Create conda environment from scratch with precompiled PyTorch
```bash
cuda_version=10.0

conda create -y -n snn python=3.7
conda activate snn
conda install -y pytorch torchvision cudatoolkit=$cuda_version -c pytorch
conda install -y -c conda-forge strictyaml
conda install -y -c conda-forge tqdm
conda install -y -c anaconda h5py
conda install -y -c anaconda pyyaml

# Setup for SLAYER
cd slayerpytorch
python setup.py install
cd ..
```

## Download Dataset and Model
Download pretrained model.
```bash
wget "http://rpg.ifi.uzh.ch/data/snn_angular_velocity/models/pretrained.pt" -O pretrained/cnn5-avgp-fc1.pt

```
Download and extract test set. This requires [zstd](https://github.com/facebook/zstd) which you can get with `sudo apt install zstd` on Ubuntu.
```bash
wget "http://rpg.ifi.uzh.ch/data/snn_angular_velocity/dataset/test.tar.zst" -O $data_dir/test.tar.zst
zstd -vd $data_dir/test.tar.zst
tar -xvf test.tar
```

## Test
To reproduce the numbers in the paper run:
```bash
python test.py
```
#### Config file
This uses by default the configuration file in `test_config.yaml`.
Modify the test config if you want to change one of the following parameters:
- Batch size
- Number of reader threads
- GPU device number

#### Writing predictions to disk
If you would like write the predictions of the network to disk:
```bash
python test.py --write
```
This will generate the following three files in `logs/test/*/out/`:
- `indices.npy` (sample): for each sample the index of the h5 filename that has been used.
- `groundtruth.npy` (sample, angle, time): groundtruth angular velocity
- `predictions.npy` (sample, angle, time): predicted angular velocity

## Train
**Code in progress**