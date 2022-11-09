# PaddleOCR-OpenCV-DNN
尝试 opencv dnn 推理 paddleocr


## 环境：

 - onnx 	 1.11.0
 - onnxruntime 	 1.10.0
 - opencv  4.5.5.62
 - paddle2onnx 	1.0.1
 - paddlpaddle   2.3.2

## 转换模型
 - 使用paddle2onnx 转换模型：
 -  **重要**：dnn推理需要固化输入shape，onnxruntime支持动态shape不需要固化。 
 - 这里固化尺寸，需要使用Netron查看模型中写死的shape，需要根据自己需求设置原来动态的参数（如下图 输入为 ?x3x640x640 说明只有nchw中n可以固化。）

 - #检测模型 
  - paddle2onnx -m paddle2onnx.optimize --model_dir .\ch_ppocr_mobile_v2.0_det_infer\ --model_filename inference.pdmodel --params_filename inference.pdiparams --save_file ./ch_ppocr_mobile_v2.0_det_infer/model.onnx --opset_version 10 --input_shape_dict="{'x':[1,3,640,640]}" --enable_onnx_checker True --enable_dev_version False
 - #识别模型
 - paddle2onnx -m paddle2onnx.optimize --model_dir ./ch_ppocr_mobile_v2.0_rec_infer --model_filename inference.pdmodel --params_filename inference.pdiparams --save_file ./ch_ppocr_mobile_v2.0_rec_infer/model.onnx --opset_version 10 --input_shape_dict="{'x':[1,3,32,1000]}" --enable_onnx_checker True --enable_dev_version False

- onnx模型simplifier：https://convertmodel.com/

- 推理

## 不足：
	
 - 由于固化模型shape，使得部分特殊场景下效果不良，如长条形图片，降低了通用性；可根据自己对应的场景去固化模型尺寸（通用场景下建议导出dynamic shape模型，使用onnxruntime推理）。

 - 相同图片下（前后处理一致），速度方面DNN比onnxruntime慢（大约2~3倍）。
 - 精度未与原始paddle模型对比；简单测试效果尚可。
 - 测试PaddleOCR v3版本，det模型dnn支持，rec模型dnn推理**失败**。

## 引用：
 - https://github.com/PaddlePaddle/PaddleOCR
 - https://github.com/PaddlePaddle/Paddle2ONNX
 - https://github.com/daquexian/onnx-simplifier
 - https://blog.csdn.net/favorxin/article/details/115270800
