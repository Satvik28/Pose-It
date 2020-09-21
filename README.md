# Pose-It!
The COVID-19 pandemic means that many of us are staying at home and sitting down more than we usually do. It’s hard for a lot of us to do the sort of exercise we normally do. It’s even harder for people who don’t usually do a lot of physical exercise. Exercise is especially important now because it can reduce stress, prevent weight gain, boost the immune system, and improve sleep. We can safely engage in physical activity by exercising with family, using online fitness resources or taking a virtual class. Many fitness experts and trainers have started their streaming services using apps and have seen unprecedented growth in the last five months.

The sedentary lifestyle that most of the working professionals and students live has led to rise in lifestyle diseases in recent years. Diseases like hypertension and Blood Sugar are the most common ones due to lack of exercise. These diseases have several long-term consequences that do hamper our quality of living. This has in turn lead to rise of fitness and wellness apps which push an individual to exercise and make better lifestyle choices. Upwards of 38 thousand such apps are available on the Play store and Appstore.
Our pose detection model can implement in several such apps for ensuring whether an individual is doing a certain exercise correctly. It can also be used for monitoring purposes by the individual himself when no external supervision is available. 


A human pose detector which on further development can be used for the streaming of online fitness services. This is inspired form the demo of Intel OPENVINO’s pose detection model.


## Installation
•	OpenVINO toolkit
•	python 3.6.5
•	OpenCV
•	ONXX

Hardware
•	Intel(R) Core (TM) i5-9300H CPU @ 2.40GHz, 2400 MHz, 4 Core(s), 8 Logical Processor(s)
•	Intel(R) UHD Graphics 630 GPU
•	Inbuilt Camera for CV


```
keras==2.1.5
scipy==1.2.0
tensorflow==1.12.0
opencv-python==3.4.3.18
```

## [Keras] Convert pre-trained Keras models
### Download pre-trained hourglass models 
- Download models from Google drive and save them to `models`. You are going to download two files, one is json file for network configuration while another is weight.
- [hg_s2_b1_mobile](https://drive.google.com/drive/folders/12ioJONmse658qc9fgMpzSy2D_JCdkFVg?usp=sharing), inputs: 256x256x3, Channel Number: 256, pckh 78.86% @MPII.
- [hg_s2_b1_tiny](https://drive.google.com/open?id=1noM_3hu_55STzghKOeapciMop7A1dllV), inputs:192x192x3, Channel Number: 128, pckh@75.11%MPII.

### Convert keras models to tensorflow forzen pb  
- Convert keras models to tf frozen pb 
```
python3 tools/keras_to_tfpb.py --input_model_json ./models/path/to/network/json --input_model_weights
./models/path/to/network/weight/h5 --out_tfpb ./models/hg_s2_b1_tf.pb
```

### Use OpenVino Model Optimizer to convert tf pb to IR. 
*  For CPU, please use mobile version `hg_s2_b1_mobile` and FP32 
```
~/intel/computer_vision_sdk/deployment_tools/model_optimizer/mo_tf.py -w ./models/hg_s2_b1_tf.pb --input_shape [1,256,256,3] --data_type FP32 --output_dir ./models/ --model_name hg_s2_mobile
```
*  For NCS2(Myriad), please use tiny version `hg_s2_b1_tiny` and FP16 
```
~/intel/computer_vision_sdk/deployment_tools/model_optimizer/mo_tf.py -w ./models/hg_s2_b1_tf.pb --input_shape [1,192,128,3] --data_type FP16 --output_dir ./models/ --model_name hg_s2_tiny
```
* `.xml` and `.bin` will be generated.

## [PyTorch] Convert pre-trained Onnx models
### Download model trained by pytorch 
Download the `model_best.onnx` model from below table to fit your accuracy and speed requirements.  `hg_s2_b1_mobile_fpd` model trained by using the knowledge distillation proposed by paper [Fast Human Pose Estimation](https://arxiv.org/abs/1811.05419). Details can be found in [Fast_Human_Pose_Estimation_Pytorch](https://github.com/yuanyuanli85/Fast_Human_Pose_Estimation_Pytorch).
  

| Model|in_res |featrues| # of Weights |Head|Shoulder|	Elbow|	Wrist|	Hip	|Knee|	Ankle|	Mean|Link|
| --- |---| ----|----------- | ----| ----| ---| ---| ---| ---| ---| ---|----|
| hg_s2_b1|256|128|6.73m| 95.74| 94.51| 87.68| 81.70| 87.81| 80.88 |76.83| 86.58|[GoogleDrive](https://drive.google.com/open?id=1c_YR0NKmRfRvLcNB5wFpm75VOkC9Y1n4)
| hg_s2_b1_mobile|256|128|2.31m|95.80|  93.61| 85.50| 79.63| 86.13| 77.82| 73.62|  84.69|[GoogleDrive](https://drive.google.com/open?id=1FxTRhiw6_dS8X1jBBUw_bxHX6RoBJaJO)
| hg_s2_b1_mobile_fpd|256|128|2.31m| 95.67|94.07| 86.31| 79.68| 86.00| 79.67|75.51| 85.41|[GoogleDrive](https://drive.google.com/open?id=1zFoecNCc7alND8ODh8lg3UeRaB6_gY_V)
| hg_s2_b1_tiny|192|128|2.31m|94.95| 92.87|84.59| 78.19| 84.68| 77.70|  73.07|  83.88|[GoogleDrive](https://drive.google.com/open?id=1qrkaUDPbHwdSBozRbN150O4Mu9HMWIOG)


```

## Reference 
-original repository :https://github.com/yuanyuanli85/Fast_Stacked_Hourglass_Network_OpenVino
- OpenVino: https://github.com/opencv/dldt 
- OpenCV OpenModelZoo: https://github.com/opencv/open_model_zoo 
- Keras implementation for stacked hourglass: https://github.com/yuanyuanli85/Stacked_Hourglass_Network_Keras  
- Pytorch-pose: https://github.com/yuanyuanli85/pytorch-pose
- Fast Human Pose Estimation https://github.com/yuanyuanli85/Fast_Human_Pose_Estimation_Pytorch