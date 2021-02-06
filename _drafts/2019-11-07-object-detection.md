---
layout: post
title: "Object-Detection API by using MSCOCO & customized handbag dataset"
author: "Eren"
tags: monde
---

Mondeique는 `여성 가방을 자동으로 인식하고 카테고리를 자동으로 추출하는 가방 검색 서비스` 라는 아이디어로 2019 하반기 예비창업패키지를 수행했습니다. 그 아이디어의 기반에는 `object-detection` 과 `image category classification` 두 가지 기술이 있었습니다,
<br>

본 포스트는 그 중 하나인 `object detection` 에 대해 서술하고자 합니다. 접근은 간단하게 기존의 여러 모델을 활용한 object detection API 를 사용했으며 우리는 사진에서 가방을 detect 했어야 하기 때문에 추가적인 dataset 으로 fine-tuning 하는 방식으로 접근했습니다. 본 포스트의 순서는 다음과 같습니다.
<br>
1. protocol buffer test
2. pycocotools installation
3. training with customzied dataset
4. Data Structure
5. Test Result
 
<hr>
<br>
 
## protocol buffer test
tensorflow 에서 기본적으로 제공하는 object detection API 를 사용하기 위해서는 protocol buffer 를 이용해야 한다. 
{% highlight python %}
$ conda activate mondeique
(mondeique) $ sudo apt-get install protobuf-compiler python-pil python-lxml python-tk
(mondeique) $ sudo pip install pillow
(mondeique) $ sudo pip install jupyter
(mondeique) $ sudo pip install matplotlib 
{% endhighlight %}
> 편의를 위해 conda virtualenv는 생략했다. (mondeique)
- Object Detection API는 protocol buffer를 이용한다. (실행시 아래 test를 계속 해줘야 한다)
```
$ git clone http://github.com/tensorflow/models
$ cd models/research

$ protoc object_detection/protos/*.proto --python_out=.
```
- 환경변수를 설정해준다.
```
$ export PYTHONPATH=$PYTHONPATH:`pwd`:`pwd`/slim
```
- 설치가 제대로 되었는지 확인한다. 
```
$ python object_detection/builders/model_builder_test.py
```
### How to test with MSCOCO dataset
```
$ CUDA_VISIBLE_DEVICES=0 python object_detection_run.py
```
### How to install pycocotools
- cocoapi를 local에 clone한다.
```
$ git clone https://github.com/philferriere/cocoapi.git
```
- setup 이동경로로 가서 pycocotools를 설치한다.
```
$ python setup.py install 
```
## How to train with customized dataset

1. split labels for training & evaluation with split_labels.ipynb
2. download image from s3 with [data-explorer](https://github.com/mondeique/data-explorer)
3. python generate_tfrecord.py 
```
$ python generate_tfrecord.py --csv_input=data/training_bag_csv --output_path=data/train.record --image_dir=images/
$ python generate_tfrecord.py --csv_input=data/test_bag_csv --output_path=data/test.record --image_dir=images/
```

4. change pipeline.config with selected network
> 원하는 network config 변경 가능 : hyperparameter-tuning (ex : batch_size, learning rate ...)
5. create object-detection.pbtxt
> item - id - class
6. python model_main.py
```
$ python model_main.py --pipeline_config_path=training/pipeline.config --model_dir=training/ \
    --num_train_steps=${NUM_TRAIN_STEPS} --sample_1_of_n_eval_examples=$SAMPLE_1_OF_N_EVAL_EXAMPLES \
    --alsologtostderr
```
## Data Structure
```
  Project
  |--- data
  |    |--- test_bag_csv
  |    |--- training_bag_csv
  |    |--- train.record
  |    |--- test.record
  |--- images (for training/evaluation)
       |--- image1.jpg
       |--- image2.jpg
       |---
       ...
  |--- test_images
       |--- test_image1.jpg
       |--- test_image2.jpg
       |---
       ...
  |
  |--- my_network_output
       |--- saved_model
       |--- pipeline.config
       |--- frozen_inference_graph.pb
       |--- model.ckpt
       |--- checkpoint
  |--- label_map
       |--- mscoco_label_map.pbtxt (for minimal working)
  |--- test_result
       |--- my_network_for_customized
       |--- faster_rcnn_resnet101 (for minimal working)
       |--- ssd_mobilenet_v1 (for minimal working)
       ...
  |--- utils (for import label_map_util)    
  |--- training
       |        
       |--- model.ckpt 
       |--- object-detection.pbtxt
       |--- pipeline.config
  |--- generate_tfrecord.py
  |--- export_inference_graph.py
  |--- model_main.py
  |--- object_detection_run.py (for test)
  |--- split_labels.ipynb   
  ```








