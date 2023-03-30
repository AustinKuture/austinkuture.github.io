---
layout: post
title: 基于Linux系统下的奥比中光Genmini安装与调用
categories:
  - Linux
description: 基于Linux系统下的奥比中光Genmini安装与调用
keywords: '奥比中光, Genmini, openni, open3d'
comments: true
---

# 基于Linux系统下的奥比中光Genmini安装与调用

```
* Ubuntu 20.04
* python 3.7.13
* openni 2.3.0
* open3d 0.15.2
* primesense 2.2.0.30.post5
* 奥比中光 gemini
* Driver OpenNI_2.3.0.85_linux
```

* **依赖库**

  从第三方驱动网站下载**[OpenNI](https://developer.orbbec.com.cn/download.html?spm=wolai.workspace.0.0.614d72f6OM3mxb&id=64)**，解压后OpenNI2以及sdk中的libOpenNI2.so和OpenNI.ini复制到项目根目录

  ![](/images/posts/Linux/genmini_01.png)

* **深度摄像头的调用**

  ```python
  import time
  from primesense import openni2
  import numpy as np
  import cv2
  
  
  DEPTH_WIDTH = 640
  DEPTH_HEIGHT = 480
  
  
  # Depth Camera
  class DepthCameraProcessor(object):
  
      def __init__(self, dll_dir, log, intermediate_processor):
  
          try:
              # Initial openni
              log.logger.info('[ A0000 ] - Initial Openni...')
              openni2.initialize(dll_directories=dll_dir)
  
              # Open Device
              log.logger.info('[ A0001 ] - Open Depth Device...')
              depth_dev = openni2.Device.open_any()
              log.logger.info('[ A0002 ] - Depth Camera Device Information: {}'.format(depth_dev.get_device_info()))
  
              # Depth Stream
              self.depth_stream = depth_dev.create_depth_stream()
              self.depth_stream.start()
          except Exception as error:
              error_msg = '[ A0003 ] - Depth Camera Device Initial Error: {}'.format(error)
              log.logger.error(error_msg)
              
              intermediate_processor.set_value_to_conifg(config_sub_key='depth_camera_state',
                                                         config_sub_value=False,
                                                         config_key_first='driver')
          
          else:
              log.logger.info('[ A0005 ] - Depth Camera Initial Success')
              intermediate_processor.set_value_to_conifg(config_sub_key='depth_camera_state',
                                                         config_sub_value=True,
                                                         config_key_first='driver')
  
          self.log = log
  
      def depth2mi(self, depthValue):
          return depthValue * 0.001
  
      def depth2xyz(self, u, v, depthValue):
          fx = 577.54679
          fy = 578.63325
          cx = 310.24326
          cy = 253.65539
  
          # depth = depth2mi(depthValue)
          depth = depthValue * 0.001
  
          z = float(depth)
          x = float((u - cx) * z) / fx
          y = float((v - cy) * z) / fy
  
          result = [x, y, z]
          return result
      
      # Generate Depth Image
      def get_depth_image(self, fps=20, image_type='32fc1'):
  
          if fps < 200: time.sleep(1/fps)
          try:
              depth_frame = self.depth_stream.read_frame()
              # depth_frame_data = np.array(depth_frame.get_buffer_as_triplet()).reshape([480, 640, 2])
              # print('--', np.array(depth_frame.get_buffer_as_uint16()).shape)
              depth_frame_data = np.array(depth_frame.get_buffer_as_uint16()).reshape([DEPTH_HEIGHT, DEPTH_WIDTH])
  
              # dpt1 = np.asarray(depth_frame_data[:, :, 0], dtype='uint16')
              # dpt2 = np.asarray(depth_frame_data[:, :, 1], dtype='uint16')
              # dpt1 = np.asarray(depth_frame_data[:, :, 0], dtype='float32')
              # dpt2 = np.asarray(depth_frame_data[:, :, 1], dtype='float32')
  
              # print('dpt', dpt[300][300])
              # print('dpt2', dpt2[300][300]/255)
  
              # dpt2 *= 255
              # dpt = dpt1 + dpt2
              # dpt = dpt[:, ::-1]
  
              # img_color = cv2.applyColorMap(cv2.convertScaleAbs(depth_frame_data, alpha=0.05), cv2.COLORMAP_JET)
              # print('Image: ', img_color[200][300])
  
          except Exception as error:
              error_msg = '[ A0006 ] - Get Depth Image Error: {}'.format(error)
              self.log.logger.info(error_msg)
              return None
          else:
              # img_color = img_color.astype('float32')
              return depth_frame_data
            
            
  if __name__ == '__main__':
  
      from config import log, intermediate_processor
      depth_device_path = './Resource'
      _depth = DepthCameraProcessor(depth_device_path, log, intermediate_processor)
  
      # _depth.get_3d_view()
      from config import ServerTools
  
      tools = ServerTools()
  
      while True:
          st_time = time.time()
          depth_data = _depth.get_depth_image(200)
          if not depth_data is None:
  
              # cv2.imshow('', depth_data)
              dis_sum = []
              for i in range(20):
                  dis_sum.append(depth_data[230+i, 310+i])
  
              distance_str = '{:.1f}cm'.format((sum(dis_sum)/len(dis_sum))/10)
  
              color_image = cv2.applyColorMap(cv2.convertScaleAbs(depth_data, alpha=0.05), cv2.COLORMAP_JET)
              cv2.putText(color_image, distance_str, (320, 240), cv2.FONT_HERSHEY_SIMPLEX,
                          1, (0, 255, 0), 1)
              cv2.circle(color_image, (320, 240), 2, (255, 255, 255), 2)
              # color_image = tools.dataset_image_crop(color_image)
  
              cv2.imshow('dis', cv2.convertScaleAbs(depth_data, alpha=0.05))
              cv2.imshow('color', color_image)
  
  
  
          print('FPS: {:.1f}'.format(1/(time.time()-st_time)))
  
          if cv2.waitKey(1) == ord('q'):
              break
  ```

* **运行效果**

  ![](/images/posts/Linux/genmini_02.png)

