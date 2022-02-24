---
layout: wiki
title: 基于CV定位颜色目标
categories: OpenCV
description: 使用OpenCV定位颜色目标
keywords: OpenCV,寻线,定位,颜色目标 

---

# 基于OpenCV实现特定颜色目标的跟踪

```
python : 3.7.0
OpenCV : 4.5.2.54
System : Mac OS 12.0.1
SerialNumber : 20220224
```



* **源码**

  ```python
  import cv2
  import numpy as np
  
  
  class CVTrack(object):
  
      def __init__(self, obj_img_path, color_extend=5):
  
          self.obj_image = cv2.imread(obj_img_path)
          self.color_extend = color_extend
  
      def track_point(self, image: np.array, color_l:np.array, color_h:np.array):
  
          height, width = image.shape[:2]
          hsv = cv2.cvtColor(image, cv2.COLOR_RGB2HSV)
          mask = cv2.inRange(hsv, color_l, color_h)
          # img_res = cv2.bitwise_and(image, image, mask=mask)
  
          m = cv2.moments(mask, False)
          try:
              cx, cy = m['m10'] / m['m00'], m['m01'] / m['m00']
          except ZeroDivisionError:
              cx, cy = width / 2, height / 2
          cx, cy = [int(x) for x in [cx, cy]]
  
          return cx, cy, hsv, mask
  
      def obj_hsv_color(self):
  
          hsv = cv2.cvtColor(self.obj_image, cv2.COLOR_RGB2HSV)
  
          h_list = []
          s_list = []
          v_list = []
          for line1 in hsv:
              for line2 in line1:
                  h_list.append(line2[0])
                  s_list.append(line2[1])
                  v_list.append(line2[2])
  
          color_l = [abs(min(x) - self.color_extend) for x in [h_list, s_list, v_list]]
          color_h = [max(x) + self.color_extend for x in [h_list, s_list, v_list]]
  
          return np.array(color_l), np.array(color_h)
  
      def display(self):
  
          cap = cv2.VideoCapture(0)
          color_l, color_h = self.obj_hsv_color()
  
          while True:
              ret, frame = cap.read()
              if ret:
                  p_x, p_y, hsv, mask = self.track_point(frame, color_l, color_h)
  
                  cv2.circle(frame, (p_x, p_y), 5, (0, 0, 255), 5, 0)
                  cv2.imshow('', frame)
                  # cv2.imshow('hsv', hsv)
                  cv2.imshow('mask', mask)
  
              if cv2.waitKey(1) == ord('q'):
                  break
  
  
  if __name__ == '__main__':
  
      obj_img_path = 'Snip20220224_2.png'
  
      color_track = CVTrack(obj_img_path, 10)
      color_track.display()
  ```

  

* **常用HSV颜色表**

  |       | 黑   | 灰   | 白   | 红   |      | 橙   | 黄   | 绿   | 青   | 蓝   | 紫   |
  | ----- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
  | H_min | 0    | 0    | 0    | 0    | 156  | 11   | 26   | 35   | 78   | 100  | 125  |
  | H_max | 180  | 180  | 180  | 10   | 180  | 25   | 34   | 77   | 99   | 124  | 155  |
  | S_min | 0    | 0    | 0    | 43   |      | 43   | 43   | 43   | 43   | 43   | 43   |
  | S_max | 255  | 43   | 30   | 255  |      | 255  | 255  | 255  | 255  | 255  | 255  |
  | V_min | 0    | 46   | 221  | 46   |      | 46   | 46   | 46   | 46   | 46   | 46   |
  | V_max | 46   | 220  | 255  | 255  |      | 255  | 255  | 255  | 255  | 255  | 255  |

  