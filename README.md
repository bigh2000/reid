# deep-person-reid
[PyTorch](http://pytorch.org/) implementation of deep person re-identification models.

We support
- multi-GPU training.
- both image-based and video-based reid.
- unified interface for different reid models.
- easy dataset preparation.
- end-to-end training and evaluation.
- standard dataset splits used by most papers.
- fast cython-based evaluation.


## Get Started
1. `cd` to the folder where you want to download this repo.
2. Run `git clone https://github.com/KaiyangZhou/deep-person-reid`.
3. Install dependencies by `pip install -r requirements.txt`.
4. To accelerate evaluation (10x faster), you can use cython-based evaluation code (developed by [luzai](https://github.com/luzai)). First `cd` to `eval_lib`, then do `make` or `python setup.py build_ext -i`. After that, run `python test_cython_eval.py` to test if the package is successfully installed.

## Datasets
Image reid datasets:
- Market1501 [7]
- CUHK03 [13]
- DukeMTMC-reID [16, 17]
- MSMT17 [22]
- VIPeR [28]
- GRID [29]
- CUHK01 [30]
- PRID450S [31]

Video reid datasets:
- MARS [8]
- iLIDS-VID [11]
- PRID2011 [12]
- DukeMTMC-VideoReID [16, 23]

Instructions regarding how to prepare these datasets can be found [here](https://github.com/KaiyangZhou/deep-person-reid/blob/master/DATASETS.md).


## Models
* `models/resnet.py`: ResNet50 [1], ResNet101 [1], ResNet50M [2].
* `models/resnext.py`: ResNeXt101 [26].
* `models/seresnet.py`: SEResNet50 [25], SEResNet101 [25], SEResNeXt50 [25], SEResNeXt101 [25].
* `models/densenet.py`: DenseNet121 [3].
* `models/mudeep.py`: MuDeep [10]. 
* `models/hacnn.py`: HACNN [15].
* `models/squeezenet.py`: SqueezeNet [18].
* `models/mobilenetv2.py`: MobileNetV2 [19].
* `models/shufflenet.py`: ShuffleNet [20].
* `models/xception.py`: Xception [21].
* `models/inceptionv4.py`: InceptionV4 [24].
* `models/inceptionresnetv2.py`: InceptionResNetV2 [24].

See `models/__init__.py` for details regarding what keys to use to call these models.

Benchmarks can be found [here](https://github.com/KaiyangZhou/deep-person-reid/blob/master/BENCHMARK.md).


## Train
Training codes are implemented in
* `train_imgreid_xent.py`: train image model with cross entropy loss.
* `train_imgreid_xent_htri.py`: train image model with combination of cross entropy loss and hard triplet loss.
* `train_vidreid_xent.py`: train video model with cross entropy loss.
* `train_vidreid_xent_htri.py`: train video model with combination of cross entropy loss and hard triplet loss.

For example, to train an image reid model using ResNet50 and cross entropy loss, run
```bash
python train_imgreid_xent.py -d market1501 -a resnet50 --optim adam --lr 0.0003 --max-epoch 60 --stepsize 20 40 --train-batch 32 --test-batch 100 --save-dir log/resnet50-xent-market1501 --gpu-devices 0
```

To use multiple GPUs, you can set `--gpu-devices 0,1,2,3`.

Please run `python train_blah_blah.py -h` for more details regarding arguments.


## Test
Say you have downloaded ResNet50 trained with `xent` on `market1501`. The path to this model is  `'saved-models/resnet50_xent_market1501.pth.tar'` (create a directory to store model weights `mkdir saved-models/` beforehand). Then, run the following command to test
```bash
python train_imgreid_xent.py -d market1501 -a resnet50 --evaluate --resume saved-models/resnet50_xent_market1501.pth.tar --save-dir log/resnet50-xent-market1501 --test-batch 100 --gpu-devices 0
```

Likewise, to test video reid model, you should have a pretrained model saved under `saved-models/`, e.g. `saved-models/resnet50_xent_mars.pth.tar`, then run
```bash
python train_vid_model_xent.py -d mars -a resnet50 --evaluate --resume saved-models/resnet50_xent_mars.pth.tar --save-dir log/resnet50-xent-mars --test-batch 2 --gpu-devices 0
```
**Note** that `--test-batch` in video reid represents number of tracklets. If you set this argument to 2, and sample 15 images per tracklet, the resulting number of images per batch is 2*15=30. Adjust this argument according to your GPU memory.


## Citation
Please link this project in your paper.

## References
[1] [He et al. Deep Residual Learning for Image Recognition. CVPR 2016.](https://arxiv.org/abs/1512.03385)<br />
[2] [Yu et al. The Devil is in the Middle: Exploiting Mid-level Representations for Cross-Domain Instance Matching. arXiv:1711.08106.](https://arxiv.org/abs/1711.08106) <br />
[3] [Huang et al. Densely Connected Convolutional Networks. CVPR 2017.](https://arxiv.org/abs/1608.06993) <br />
[4] [Hermans et al. In Defense of the Triplet Loss for Person Re-Identification. arXiv:1703.07737.](https://arxiv.org/abs/1703.07737) <br />
[5] [Szegedy et al. Rethinking the Inception Architecture for Computer Vision. CVPR 2016.](https://arxiv.org/abs/1512.00567) <br />
[6] [Kingma and Ba. Adam: A Method for Stochastic Optimization. ICLR 2015.](https://arxiv.org/abs/1412.6980) <br />
[7] [Zheng et al. Scalable Person Re-identification: A Benchmark. ICCV 2015.](https://www.cv-foundation.org/openaccess/content_iccv_2015/papers/Zheng_Scalable_Person_Re-Identification_ICCV_2015_paper.pdf) <br />
[8] [Zheng et al. MARS: A Video Benchmark for Large-Scale Person Re-identification. ECCV 2016.](http://www.liangzheng.com.cn/Project/project_mars.html) <br />
[9] [Wen et al. A Discriminative Feature Learning Approach for Deep Face Recognition. ECCV 2016](https://ydwen.github.io/papers/WenECCV16.pdf) <br />
[10] [Qian et al. Multi-scale Deep Learning Architectures for Person Re-identification. ICCV 2017.](https://arxiv.org/abs/1709.05165) <br />
[11] [Wang et al. Person Re-Identification by Video Ranking. ECCV 2014.](http://www.eecs.qmul.ac.uk/~xiatian/papers/ECCV14/WangEtAl_ECCV14.pdf) <br />
[12] [Hirzer et al. Person Re-Identification by Descriptive and Discriminative Classification. SCIA 2011.](https://files.icg.tugraz.at/seafhttp/files/ba284964-6e03-4261-bb39-e85280707598/hirzer_scia_2011.pdf) <br />
[13] [Li et al. DeepReID: Deep Filter Pairing Neural Network for Person Re-identification. CVPR 2014.](https://www.cv-foundation.org/openaccess/content_cvpr_2014/papers/Li_DeepReID_Deep_Filter_2014_CVPR_paper.pdf) <br />
[14] [Zhong et al. Re-ranking Person Re-identification with k-reciprocal Encoding. CVPR 2017](https://arxiv.org/abs/1701.08398) <br />
[15] [Li et al. Harmonious Attention Network for Person Re-identification. CVPR 2018.](https://arxiv.org/abs/1802.08122) <br />
[16] [Ristani et al. Performance Measures and a Data Set for Multi-Target, Multi-Camera Tracking. ECCVW 2016.](https://arxiv.org/abs/1609.01775) <br />
[17] [Zheng et al. Unlabeled Samples Generated by GAN Improve the Person Re-identification Baseline in vitro. ICCV 2017.](https://arxiv.org/abs/1701.07717) <br />
[18] [Iandola et al. SqueezeNet: AlexNet-level accuracy with 50x fewer parameters and< 0.5 MB model size. arXiv:1602.07360.](https://arxiv.org/abs/1602.07360) <br />
[19] [Sandler et al. MobileNetV2: Inverted Residuals and Linear Bottlenecks. CVPR 2018.](https://arxiv.org/abs/1801.04381) <br />
[20] [Zhang et al. ShuffleNet: An Extremely Efficient Convolutional Neural Network for Mobile Devices. CVPR 2018.](https://arxiv.org/abs/1707.01083) <br />
[21] [Chollet. Xception: Deep Learning with Depthwise Separable Convolutions. CVPR 2017.](https://arxiv.org/abs/1610.02357) <br />
[22] [Wei et al. Person Transfer GAN to Bridge Domain Gap for Person Re-Identification. CVPR 2018.](http://www.pkuvmc.com/publications/msmt17.html) <br />
[23] [Wu et al. Exploit the Unknown Gradually: One-Shot Video-Based Person Re-Identification by Stepwise Learning. CVPR 2018.](http://xuanyidong.com/publication/cvpr-2018-eug/) <br />
[24] [Szegedy et al. Inception-v4, Inception-ResNet and the Impact of Residual Connections on Learning. ICLRW 2016.](https://arxiv.org/abs/1602.07261) <br />
[25] [Hu et al. Squeeze-and-Excitation Networks. CVPR 2018.](https://arxiv.org/abs/1709.01507) <br />
[26] [Xie et al. 
Aggregated Residual Transformations for Deep Neural Networks. CVPR 2017.](https://arxiv.org/abs/1611.05431) <br />
[27] [Chen et al. Dual Path Networks. NIPS 2017.](https://arxiv.org/abs/1707.01629) <br />
[28] [Gray et al. Evaluating appearance models for recognition, reacquisition, and tracking. PETS 2007.](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.331.7285&rep=rep1&type=pdf) <br />
[29] [Loy et al. Multi-camera activity correlation analysis. CVPR 2009.](https://ieeexplore.ieee.org/document/5206827/) <br />
[30] [Li et al. Human Reidentification with Transferred Metric Learning. ACCV 2012.](http://www.ee.cuhk.edu.hk/~xgwang/papers/liZWaccv12.pdf) <br />
[31] [Roth et al. Mahalanobis Distance Learning for Person Re-Identification. PR 2014.](https://pdfs.semanticscholar.org/f62d/71e701c9fd021610e2076b5e0f5b2c7c86ca.pdf) <br />
