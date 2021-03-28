# PyMAF
This repository contains the code for the following paper:

**3D Human Pose and Shape Regression with Pyramidal Mesh Alignment Feedback Loop**  
Hongwen Zhang*, Yating Tian*, Xinchi Zhou, Wanli Ouyang, Yebin Liu, Limin Wang, Zhenan Sun

\* Equal contribution

[Project Page](https://hongwenzhang.github.io/pymaf)

[![PyMAF](https://hongwenzhang.github.io/images/pymaf.jpg "PyMAF")](https://hongwenzhang.github.io/pymaf)

## Requirements

- Python 3.6.10

### packages

- [PyTorch](https://www.pytorch.org) tested on version 1.1.0

- [Neural Renderer](https://github.com/daniilidis-group/neural_renderer)

- [opendr](https://gitlab.eecs.umich.edu/ngv-python-modules/opendr#)

- other packages listed in `requirements.txt`

### necessary files

> DensePose UV data

- Run the following script to fetch DensePose UV data.

```
bash get_densepose_uv.sh
```
> SMPL model files

- Collect SMPL model files from [https://smpl.is.tue.mpg.de](https://smpl.is.tue.mpg.de) and [UP](https://github.com/classner/up/blob/master/models/3D/basicModel_neutral_lbs_10_207_0_v1.0.0.pkl). Rename model files and put them into the `./data/smpl` directory.

> Fetch preprocessed data from [SPIN](https://github.com/nkolot/SPIN#fetch-data).

> Download the [pre-trained model](1drv) and put it into the `./data/pretrained_model` directory.

After collecting the above necessary files, the directory structure of `./data` is expected as follows.  
```
./data
├── dataset_extras
│   └── .npz files
├── J_regressor_extra.npy
├── J_regressor_h36m.npy
├── mesh_downsampling.npz
├── pretrained_model
│   └── PyMAF_model_checkpoint.pt
├── smpl
│   ├── SMPL_FEMALE.pkl
│   ├── SMPL_MALE.pkl
│   └── SMPL_NEUTRAL.pkl
├── smpl_mean_params.npz
├── static_fits
│   └── .npy files
└── UV_data
    ├── UV_Processed.mat
    └── UV_symmetry_transforms.mat
```

## Demo

Run the demo code.

```
python3 demo.py --checkpoint=data/pretrained_model/PyMAF_model_checkpoint.pt --vid_file ./flashmob.mp4
```

<p align="center">
    <img src="https://hongwenzhang.github.io/pymaf/files/flashmob.gif">
    <br>
    <sup>Video clipped from <a href="https://www.youtube.com/watch?v=2DiQUX11YaY" target="_blank"><i>Crazy Uptown Funk Flashmob in Sydney</i></a></sup>
</p>

## Evaluation

### Human3.6M / 3DPW

Run the evaluation code. Using `--dataset` to specify the evaluation dataset.
```
# Example usage:

# Human3.6M Protocol 2
python3 eval.py --checkpoint=data/pretrained_model/PyMAF_model_checkpoint.pt --dataset=h36m-p2 --log_freq=20

# 3DPW
python3 eval.py --checkpoint=data/pretrained_model/PyMAF_model_checkpoint.pt --dataset=3dpw --log_freq=20
```

### COCO Keypoint Localization

1. Download the preprocessed data [coco_2014_val.npz](https://drive.google.com/drive/folders/1R4_Vi4TpCQ26-6_b2PhjTBg-nBxZKjz6?usp=sharing). Put it into the `./data/dataset_extras` directory. 

2. Run the COCO evaluation code.
```
python3 eval_coco.py --checkpoint=data/pretrained_model/PyMAF_model_checkpoint.pt
```

## Training

To perform training, we need to collect preprocessed files of training datasets at first.

The preprocessed labels have the same format as SPIN and can be retrieved from [here](https://github.com/nkolot/SPIN#fetch-data). Please refer to [SPIN](https://github.com/nkolot/SPIN) for more details about data preprocessing.

PyMAF is trained on Human3.6M at the first stage and then trained on the mixture of both 2D and 3D datasets at the second stage. Example usage:
```
# training on Human3.6M
python3 train.py --regressor pymaf_net --single_dataset --misc TRAIN.BATCH_SIZE 64
# training on mixed datasets
python3 train.py --regressor pymaf_net --pretrained_checkpoint path/to/checkpoint_file.pt --misc TRAIN.BATCH_SIZE 64
```
Running the above commands will use Human3.6M or mixed datasets for training, respectively. We can monitor the training process by setting up a TensorBoard at the directory `./logs`.

## Citation
If this work is helpful in your research, please cite the following paper.
```
@article{pymaf2021,
  title={3D Human Pose and Shape Regression with Pyramidal Mesh Alignment Feedback Loop},
  author={Zhang, Hongwen and Tian, Yating and Zhou, Xinchi and Ouyang, Wanli and Liu, Yebin and Wang, Limin and Sun, Zhenan},
  year={2021}
}
```

## Acknowledgments

The code is developed upon the following projects. Many thanks to their contributions.

- [SPIN](https://github.com/nkolot/SPIN)

- [DaNet](https://github.com/HongwenZhang/DaNet-3DHumanReconstruction)

- [VIBE](https://github.com/mkocabas/VIBE)

- [PIFu](https://github.com/shunsukesaito/PIFu)

- [DensePose](https://github.com/facebookresearch/DensePose)

- [HMR](https://github.com/akanazawa/hmr)

- [pose_resnet](https://github.com/Microsoft/human-pose-estimation.pytorch)
