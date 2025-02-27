# SSD-based object and text detection with Keras
This repository contains the implementation of various approaches to object detection in general and text detection/recognition in particular.

Its code was initially used to carry out the experiments for the author's master thesis [End-to-End Scene Text Recognition based on Artificial Neural Networks](http://46.163.79.21/thesis/thesis.pdf) and later extended with the implementation of more recent approaches.

## Technical background

Most of the ideas used for this project go back to the following papers:

#### SSD: Single Shot MultiBox Detector [arXiv:1512.02325](https://arxiv.org/abs/1512.02325)
SSD is a generic object detector that does local regression and classification on multiple feature maps of a CNN to predict a dense population of bounding boxes, which are subsequently filtered by a confidence threshold and NMS.

#### TextBoxes: A Fast Text Detector with a Single Deep Neural Network [arXiv:1611.06779](https://arxiv.org/abs/1611.06779)
TextBoxes is a modification of SSD that uses non-square convolution kernels and prior boxes with a large aspect ratio to better detect horizontal text.

#### DSOD: Learning Deeply Supervised Object Detectors from Scratch [arXiv:1708.01241](https://arxiv.org/abs/1708.01241)
DSOD is a modification of SSD that uses DenseNet as backbone architecture and thus can be trained form scratch instead of depending on a pretrained VGG-16 model.

#### Detecting Oriented Text in Natural Images by Linking Segments [arXiv:1703.06520](https://arxiv.org/abs/1703.06520)
SegLink builds on SSD and detects oriented text by locally predicting text segments (objects in SSD) and there linking with each other. The segments (edges) and links (vertices) are considered as a graph and thresholded by confidence. The remaining groups are finally combined to form bounding boxes.

#### TextBoxes++: A Single-Shot Oriented Scene Text Detector [arXiv:1801.02765](https://arxiv.org/abs/1801.02765)
TextBoxes++ extends TextBoxes for arbitrary oriented text by predicting horizontal bounding boxes as well as quadrilaterals and oriented bounding boxes. It additionally uses the recognition score to eliminate false positives from the detection stage (currently not implemented).

#### Focal Loss for Dense Object Detection [arXiv:1708.02002](https://arxiv.org/abs/1708.02002)
The focal loss is a dynamically weighted version of the cross entropy loss that can better handle a large imbalance between the classes and focus the training process on the difficult samples. It can be applied to the aforementioned detectors, instead of hard negative mining, to overcome the dominance of the background class.

#### An End-to-End Trainable Neural Network for Image-based Sequence Recognition and Its Application to Scene Text Recognition [arXiv:1507.05717](https://arxiv.org/abs/1507.05717)
CRNN is a relatively simple architecture with some convolutional-pooling blocks, followed by two bidirectional LSTM (GRU in this implementation) layers, which can be trained with a CTC for efficient text recognition. It can be used to read the text in the cropped bounding boxes generated by the text detectors mentioned above.

#### Xception: Deep Learning with Depthwise Separable Convolutions [arXiv:1610.02357](https://arxiv.org/abs/1610.02357)
Separable Convolution splits up the normal convolution operation into a channalwise Depthwise Convolution and a subsequent 1x1 convolution. This trick reduces the number of parameters, especially for large kernel size. 

#### MobileNets: Efficient Convolutional Neural Networks for Mobile VisionApplications [arXiv:1704.04861](https://arxiv.org/abs/1704.04861)
Separable Convolution got its attention not only through Francois Chollet's Xception architecture and the Keras implementation, but most of all through the MobileNet architecture.

## Supported datasets
Currently supported datasets for object detection are

- PASCAL VOC
- MS COCO

and supported datasets related to text are

- ICDAR2015 FST
- ICDAR2015 IST
- SynthText
- MSRA TD500
- SVT
- COCO Text

For more information about the datasets, see [datasets.ipynb](datasets.ipynb).

## Dependencies
For suitable versions of the necessary dependencies, see [environment.ipynb](misc/environment.ipynb).

## Usage
The usage of the code is quite straightforward.
- use `git` to clone the repository
- `pip install -r requirements.txt`
- run the related Jupyter notebooks.

Some of the scripts (e.g. for video and model conversion) can also be executed form the command line.

## Pretrained models
Pretrained SSD models can be converted from the [original Caffe implementation](https://github.com/weiliu89/caffe/tree/ssd).

#### [Converted SSD300 VOC](http://46.163.79.21/ssd_detectors/ssd300_voc_weights_fixed.zip)
PASCAL VOC 07+12+COCO SSD300* from Caffe implementation

#### [Converted SSD512 VOC](http://46.163.79.21/ssd_detectors/ssd512_voc_weights_fixed.zip)
PASCAL VOC 07+12+COCO SSD512* from Caffe implementation

#### [Converted SSD300 COCO](http://46.163.79.21/ssd_detectors/ssd300_coco_weights_fixed.zip)
COCO trainval35k SSD300* from Caffe implementation

#### [Converted SSD512 COCO](http://46.163.79.21/ssd_detectors/ssd512_coco_weights_fixed.zip)
COCO trainval35k SSD512* from Caffe implementation

#### [SegLink](http://46.163.79.21/ssd_detectors/201809231008_sl512_synthtext.zip)
initialized with converted SSD512 weights  
trained and tested on subsets of SynthText  
segment_threshold  0.60  
link_threshold     0.25  
precision         0.884  
recall            0.854  
f-measure         0.869  
parameters   24,358,681  
model size        94 MB  

#### [SegLink with DSOD backbone and Focal Loss](http://46.163.79.21/ssd_detectors/201806021007_dsodsl512_synthtext.zip)
trained and tested on subsets of SynthText  
segment_threshold  0.60  
link_threshold     0.50  
precision         0.937  
recall            0.926  
f-measure         0.932  
parameters   12,905,177  
model size        50 MB  

#### [TextBoxes++ with DSOD backbone and Focal Loss](http://46.163.79.21/ssd_detectors/201906190710_dsodtbpp512fl_synthtext.zip)
trained and tested on subsets of SynthText  
threshold          0.35  
precision         0.984  
recall            0.890  
f-measure         0.934  
parameters   23,477,798  
model size        91 MB  

#### [TextBoxes++ with dense blocks, separable convolution and Focal Loss](http://46.163.79.21/ssd_detectors/202003070004_dstbpp512fl_synthtext.zip)
the number of parameters has been reduced by ≈ 94% compared to the original TextBoxes++ with VGG backbone (35,763,078 parameters)  
trained and tested on subsets of SynthText  
threshold          0.45  
precision         0.973  
recall            0.879  
f-measure         0.924  
parameters    2,226,374  
model size         9 MB  


#### [CRNN with LSTM](http://46.163.79.21/ssd_detectors/201806162129_crnn_lstm_synthtext.zip)
trained and tested on cropped word level bounding boxes form SynthText  
mean editdistance             0.332  
mean normalized editdistance  0.081  
character recogniton rate     0.916  
word recognition rate         0.861  
iterations                     400k  
parameters                8,747,351  
model size                    34 MB  
runtime (GPU)      114 ms ± 2.75 ms  

#### [CRNN with GRU](http://46.163.79.21/ssd_detectors/201806190711_crnn_gru_synthtext.zip)
trained and tested on cropped word level bounding boxes form SynthText  
mean editdistance             0.333  
mean normalized editdistance  0.081  
character recogniton rate     0.916  
word recognition rate         0.858  
iterations                     400k  
parameters                7,959,895  
model size                    31 MB  
runtime (GPU)     85.1 ms ± 1.19 ms  

#### [CRNN with CNN](http://46.163.79.21/ssd_detectors/202001131747_crnn_cnn_synthtext.zip)
fully convolutional architecture for the recognition stage (probably not optimal)  
trained and tested on cropped word level bounding boxes form SynthText  
mean editdistance             0.355  
mean normalized editdistance  0.086  
character recogniton rate     0.911  
word recognition rate         0.847  
iterations                     600k  
parameters                7,877,719  
model size                    31 MB  
runtime (GPU)     3.68 ms ± 24.5 µs  

#### [CRNN with CNN concat](http://46.163.79.21/ssd_detectors/202002030820_crnn_cnn_synthtext_concat_continued.zip)
fine-tuned fully convolutional model on concatenated word images form SynthText  
<img src="./images/crnn_input_concat1.jpg" />
mean editdistance             1.842  
mean normalized editdistance  0.064  
character recogniton rate     0.939  
sample recognition rate       0.517  
iterations                600k+100k  


## Demo images

#### SSD on PASCAL VOC 2007 test
<p float="left">
<img src="./images/ssd_result1.jpg" width="256" />
<img src="./images/ssd_result3.jpg" width="256" />
<img src="./images/ssd_result5.jpg" width="256" />
</p>

#### SegLink with DenseNet on SynthText
<p float="left">
<img src="./images/dsodsl_predition1.jpg" width="384" />
<img src="./images/dsodsl_combining1.jpg" width="384" />
</p>

<p float="left">
<img src="./images/dsodsl_result1.jpg" width="256" />
<img src="./images/dsodsl_result3.jpg" width="256" />
<img src="./images/dsodsl_result5.jpg" width="256" />
</p>

#### TextBoxes++ with DenseNet on SynthText
<p float="left">
<img src="./images/dsodtbpp_result1.jpg" width="256" />
<img src="./images/dsodtbpp_result2.jpg" width="256" />
<img src="./images/dsodtbpp_result3.jpg" width="256" />
</p>

#### SegLink with DenseNet, Focal Loss and CRNN end-to-end on SynthText
<p float="left">
<img src="./images/dsodslcrnn_result1.jpg" width="256" />
<img src="./images/dsodslcrnn_result2.jpg" width="256" />
<img src="./images/dsodslcrnn_result4.jpg" width="256" />
</p>

#### SegLink with DenseNet, Focal Loss and CRNN end-to-end real-time recogniton
[<img src="./images/dsodslcrnn_end2end_record_preview.jpg" width="768" />](http://46.163.79.21/ssd_detectors/dsodslcrnn_end2end_record.mp4)

