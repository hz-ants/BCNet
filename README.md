# Deep Occlusion-Aware Instance Segmentation with Overlapping BiLayers [BCNet, CVPR 2021]

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![PWC](https://img.shields.io/endpoint.svg?url=https://paperswithcode.com/badge/deep-occlusion-aware-instance-segmentation/instance-segmentation-on-coco)](https://paperswithcode.com/sota/instance-segmentation-on-coco?p=deep-occlusion-aware-instance-segmentation)
[![PWC](https://img.shields.io/endpoint.svg?url=https://paperswithcode.com/badge/deep-occlusion-aware-instance-segmentation/instance-segmentation-on-kins)](https://paperswithcode.com/sota/instance-segmentation-on-kins?p=deep-occlusion-aware-instance-segmentation)

This is the official pytorch implementation of [BCNet](https://arxiv.org/abs/2103.12340) built on the open-source detectron2.

> [**Deep Occlusion-Aware Instance Segmentation with Overlapping BiLayers**](https://arxiv.org/abs/2103.12340)           
> Lei Ke, Yu-Wing Tai, Chi-Keung Tang    
> CVPR 2021

Highlights
-----------------
- **BCNet:** Two/one-stage (detect-then-segment) instance segmentation with state-of-the-art performance.
- **Novelty:** Explicit occlusion modeling with **bilayer decouple** for the occluder and occludee in the RoI.
- **Efficacy:** Large improvements both the FCOS (anchor-free) and Faster R-CNN (anchor-based) detectors.
- **Simple:** Small additional computation burden and easy to use.

Visualization of Occluded Objects
-----------------
<table>
    <tr>
        <td><center><img src="figures/fig_vis2_new.png" height="260">
            
Qualitative instance segmentation results of our BCNet, using ResNet-101-FPN and Faster R-CNN detector. The bottom row visualizes squared heatmap of contour and mask predictions by the two GCN layers for the occluder and occludee in **the same ROI region** specified by the red bounding box, which also makes the final segmentation result of BCNet more explainable than previous methods.
          </center></td>
</tr>
</table>
<table>
    <tr>
          <td><center><img src="figures/fig_vis1_new.png" height="260">
              
Qualitative instance segmentation results of our BCNet, using ResNet-101-FPN and FCOS detector.
          </center></td>
</tr>
</table>

Results on COCO test-dev
------------
(Check Table 8 of the paper for full results, all methods are trained on COCO train2017)
Detector | Backbone  | Method | mAP(mask) |
|--------|----------|--------|-----------|
Faster R-CNN| ResNet-50 FPN | Mask R-CNN (ICCV'17) | 34.2 |
Faster R-CNN| ResNet-50 FPN | PANet (CVPR'18) | 36.6 |
Faster R-CNN| ResNet-50 FPN | MS R-CNN (CVPR'19) | 35.6 |
Faster R-CNN| ResNet-50 FPN | PointRend (1x CVPR'20) | 36.3 |
**Faster R-CNN**| **ResNet-50 FPN** | **BCNet (CVPR'21)** | [**38.4**](scores/stdout_r50_frcnn.txt) | 
Faster R-CNN| ResNet-101 FPN | Mask R-CNN (ICCV'17) | 36.1 | 
Faster R-CNN| ResNet-101 FPN | MS R-CNN (CVPR'19) | 38.3 |
Faster R-CNN| ResNet-101 FPN | BMask R-CNN (ECCV'20) | 37.7 | 
**Faster R-CNN**|**ResNet-101 FPN** | **BCNet (CVPR'21)** | [**39.8**](scores/stdout_frcnn.txt)|
FCOS| ResNet-101 FPN | BlendMask (CVPR'20) | 38.4 | 
FCOS| ResNet-101 FPN | CenterMask (CVPR'20)| 38.3 | 
FCOS| ResNet-101 FPN | SipMask (ECCV'20) | 37.8 |
**FCOS**| **ResNet-101 FPN**| **BCNet (CVPR'21)**| [**39.6**](scores/stdout_fcos.txt), [Pretrained Model](https://hkustconnect-my.sharepoint.com/:u:/g/personal/lkeab_connect_ust_hk/EfiDFLLEawFJpruwuOl3h3ABBjAKysTf0qJQU80iaKbqYg?e=igzC51), [Submission File](https://hkustconnect-my.sharepoint.com/:u:/g/personal/lkeab_connect_ust_hk/EVgMSMFwOmVDjAIB3LFusAMBTyTY-N_6qWbAWEBq_PK9xQ?e=5Lrmv7)|


Introduction
-----------------
Segmenting highly-overlapping objects is challenging, because typically no distinction is made between real object contours and occlusion boundaries. Unlike previous two-stage instance segmentation methods, **BCNet** models image formation as composition of two overlapping image layers, where the top GCN layer detects the occluding objects (occluder) and the bottom GCN layer infers partially occluded instance (occludee). **The explicit modeling of occlusion relationship with bilayer structure naturally decouples the boundaries of both the occluding and occluded instances, and considers the interaction between them during mask regression.** We validate the efficacy of bilayer decoupling on both one-stage and two-stage object detectors with different backbones and network layer choices. The network of BCNet is as follows:
<center>
<table>
    <tr>
          <td><center><img src="figures/framework_new.png" height="430"></center></td>
    </tr>
</table>
A brief comparison of mask head architectures, see our paper for full details.
<table>	
    <tr>
          <td><center><img src="figures/netcompare.png" height="270"></center></td>
    </tr>
</table>
</center>

## Step-by-step Installation
```
conda create -n bcnet python=3.7 -y
source activate bcnet
 
conda install pytorch==1.4.0 torchvision==0.5.0 cudatoolkit=10.1 -c pytorch
 
# FCOS and coco api and visualization dependencies
pip install ninja yacs cython matplotlib tqdm
pip install opencv-python==4.4.0.40
# Boundary dependency
pip install scikit-image
 
export INSTALL_DIR=$PWD
 
# install pycocotools. Please make sure you have installed cython.
cd $INSTALL_DIR
git clone https://github.com/cocodataset/cocoapi.git
cd cocoapi/PythonAPI
python setup.py build_ext install
 
# install BCNet
cd $INSTALL_DIR
git clone https://github.com/lkeab/BCNet.git
cd BCNet/
python3 setup.py build develop
 
unset INSTALL_DIR
```


## Dataset Preparation
Prepare for [coco2017](http://cocodataset.org/#home) dataset following [this instruction](https://github.com/facebookresearch/detectron2/tree/master/datasets). And use our [converted mask annotations](https://hkustconnect-my.sharepoint.com/:u:/g/personal/lkeab_connect_ust_hk/EW2ZVyev7e5Pr1fVfF2nn18BRod82j_jW5Z4ywYd1evq8Q?e=qj0Bbm) to replace original annotation file for bilayer decoupling training.

```
  mkdir -p datasets/coco
  ln -s /path_to_coco_dataset/annotations datasets/coco/annotations
  ln -s /path_to_coco_dataset/train2017 datasets/coco/train2017
  ln -s /path_to_coco_dataset/test2017 datasets/coco/test2017
  ln -s /path_to_coco_dataset/val2017 datasets/coco/val2017
```

Multi-GPU Training and evaluation on Validation set
---------------
```
bash all.sh
```
Or
```
CUDA_VISIBLE_DEVICES=0,1 python3 tools/train_net.py --num-gpus 2 \
	--config-file configs/fcos/fcos_imprv_R_50_FPN.yaml 2>&1 | tee log/train_log.txt
```

Pretrained Models
---------------
FCOS-version download: [link](https://hkustconnect-my.sharepoint.com/:u:/g/personal/lkeab_connect_ust_hk/EfiDFLLEawFJpruwuOl3h3ABBjAKysTf0qJQU80iaKbqYg?e=igzC51)
```
  mkdir pretrained_models
  #And put the downloaded pretrained models in this directory.
```

Testing on Test-dev
---------------
```
export PYTHONPATH=$PYTHONPATH:`pwd`
CUDA_VISIBLE_DEVICES=0,1 python3 tools/train_net.py --num-gpus 2 \
	--config-file configs/fcos/fcos_imprv_R_101_FPN.yaml \
	--eval-only MODEL.WEIGHTS ./pretrained_models/xxx.pth 2>&1 | tee log/test_log.txt
```

Visualization
---------------
```
bash visualize.sh
```

Reference script for producing bilayer mask annotation:
---------------
```
bash process.sh
```

Citation
---------------
If you find BCNet useful in your research or refer to the provided baseline results, please star this repository and consider citing:
```
@inproceedings{ke2021bcnet,
    author = {Ke, Lei and Tai, Yu-Wing and Tang, Chi-Keung},
    title = {Deep Occlusion-Aware Instance Segmentation with Overlapping BiLayers},
    booktitle = {CVPR},
    year = {2021}
}   
```
Related Links
---------------
[Youtube Video](https://www.youtube.com/watch?v=iHlGJppJGiQ) | [Poster](http://www.kelei.site/poster/BCNet_CVPR21.pdf)


[Media Report (Chinese) by arxivDaily](https://zhuanlan.zhihu.com/p/374377332) | [CVMart (Chinese)](https://zhuanlan.zhihu.com/p/359610583) | [52CV (Chinese)](https://zhuanlan.zhihu.com/p/366630524) | [CVer (Chinese)](https://zhuanlan.zhihu.com/p/367800052)

License
---------------
BCNet is released under the MIT license. See [LICENSE](LICENSE) for additional details.
Thanks to the Third Party Libs
[detectron2](https://github.com/facebookresearch/detectron2).    
