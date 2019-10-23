## Overview

This is the PyTorch implementation of paper [Multi-resolution CSI Feedback with deep learning in Massive MIMO System](TODO:arxivlink).

## Requirements

To use this project, you need to ensure the following requirements are installed.

- Python >= 3.7
- [PyTorch >= 1.2](https://pytorch.org/get-started/locally/)
- [thop](https://github.com/Lyken17/pytorch-OpCounter)

## Getting started

#### A. Data preparation

The channel state information (CSI) matrix is generated from [COST2100](https://ieeexplore.ieee.org/document/6393523) model. Chao-Kai Wen and Shi Jin group provides a pre-processed version of COST2100 dataset in [Google Drive](https://drive.google.com/drive/folders/1_lAMLk_5k1Z8zJQlTr5NRnSD6ACaNRtj?usp=sharing), which is easier to use for the CSI feedback task; You can also download it from [Baidu Netdisk](https://pan.baidu.com/s/1Ggr6gnsXNwzD4ULbwqCmjA).

You can generate your own dataset according to the [open source library of COST2100](https://github.com/cost2100/cost2100) as well. The details of data pre-processing can be found in our paper.

#### B. Train CRNet 

Please arrange the project tree as follows.
```
home
├── CRNet  # The cloned CRNet repository
│   ├── dataset
│   ├── models
│   ├── utils
│   ├── checkpoints
│   ├── main.py
├── COST2100  # The data folder
│   ├── DATA_Htestin.mat
│   ├── ...
├── Experiments
│   ├── run.sh
...
```

An example of run.sh is listed below. It will start advanced scheme aided CRNet training in indoor scenario with compression ratio 1/4.

``` bash
python /home/CRNet/main.py \
  --data-dir '/home/COST2100' \
  --scenario 'in' \
  --epochs 2500 \
  --batch-size 200 \
  --workers 0 \
  --cr 4 \
  --scheduler cosine \
  --gpu 0 \
  2>&1 | tee log.out
```

**After downloading and processing the COST2100 dataset, managing the project tree and establishing the `run.sh` file, you can simply start the training with `sh run.sh`.**

## Results and their reproduction

The main results reported in our paper are presented as follows. All the listed results can be found in Table1 of our paper. They are achieved by training CRNet with our advanced training scheme (cosine annealing scheduler with warm up for 2500 epochs).

Scenario | Compression Ratio | NMSE | Flops | Checkpoints
:--: | :--: | :--: | :--: | :--:
indoor | 1/4 | -26.99 | 7.66M | in_04.pth
indoor | 1/8 | -16.01 | 5.56M | in_08.pth
indoor | 1/16 | -11.35 | 4.51M | in_16.pth
indoor | 1/32 | -8.93 | 3.99M | in_32.pth
indoor | 1/64 | -6.49 | 3.72M | in_64.pth
outdoor | 1/4 | -12.70 | 7.66M | out_04.pth
outdoor | 1/8 | -8.04 | 5.56M | out_08.pth
outdoor | 1/16 | -5.44 | 4.51M | out_16.pth
outdoor | 1/32 | -3.51 | 3.99M | out_32.pth
outdoor | 1/64 | -2.22 | 3.72M | out_64.pth

We provide model checkpoints for all the results in `/CRNet/checkpoints`. Our code library supports easy inference. **To reproduce all the important results, simple add `--evaluate` to `run.sh` and pick the corresponding pre-trained model with `--pretrained`.** 

``` bash
python /home/CRNet/main.py \
  --data-dir '/home/COST2100' \
  --scenario 'in' \
  --pretrained '/home/CRNet/checkpoints/in_04' \  # Pretrained model loading
  --evaluate \  # Inference mode
  --batch-size 200 \
  --workers 0 \
  --cr 4 \
  --cpu \
  2>&1 | tee log.out
```

## Acknowledgment

Thanks again for Chao-Kai Wen and Shi Jin group for providing the pre-processed COST2100 dataset, you can find their related work named CsiNet in [Github-Python_CsiNet]() 