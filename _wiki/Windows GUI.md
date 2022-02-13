---
layout: wiki
title: Windows GUI
categories: Tools
description: Windows GUI
keywords: Windows, GUI, USB, Serial
---

# Windows GUI可视化编程技巧



* **Windows 下获取U盘序列号：**

  ```python
  class ScanUStore(object):
  
      def __init__(self):
          pass
  
      # obtain usb serial
      def get_usb_serial(self):
  
          usb_list = []
          try:
              res = os.popen('wmic diskdrive').readlines()
              for line in res:
                  try:
                      line = line.strip()
                      if len(line) > 10 and line.lower().count('removable') > 0 and line.count('USBSTOR') > 0:
                          re_res = re.findall('.*(USBSTOR\S+)\s.*', line)[0].split('\\')[-1].replace('&0', '')
                          usb_list.append(re_res.replace('&', ''))
                  except Exception as error:
                      print('Get Serial Error : ', error)
          except Exception as error:
              print('Get USB Error : ', error)
  
          return usb_list
  
  ```

* **Mac OS、Windows获取U盘序列号：**

  ```python
  from usb import core
  
  res = core.find(find_all=True)
  
  for line in res:
      print(dir(line))
      print(line)
      print(line.serial_number)
   
  # 判断系统
  import sys
  os_type = sys.platform.lower()
  print(os_type)
  if "win" in os_type and os_type != 'darwin':
  
      print('win')
  elif "linux" in os_type or os_type == 'darwin':
  
      print('linux')
  ```

  

* **文件随机加解密：**

  ```python
  class UFileDecrypt(object):
  
      def __init__(self):
          self.base2233 = Base2233()
  
      def encrypt_content(self, content):
  
          enctypt_str = self.base2233.encrypt(content, ak_rand=True)
          return enctypt_str
  
      # Decrypt Content
      def decrypt_content(self, file_content):
  
          decrypt_str = self.base2233.decrypt(file_content, ak_rand=True)
  
          return decrypt_str
  
      def encrypt_pdf_file(self, source_path, save_path):
  
          with open(source_path, 'rb') as rf:
              content = rf.read()
              encrypt_str = self.base2233.encrypt(content, ak_rand=True)
  
          with open(save_path, 'w') as sf:
              sf.write(encrypt_str)
  
      def generate_licence_file(self, input_str, save_path):
  
          encrypt_str = ufd.encrypt_content(input_str)
          with open(save_path, 'w') as sf:
              sf.write(encrypt_str)
          os.chmod(save_path, S_IREAD)
  
          return encrypt_str
  ```

* **TKinter PDF可视化：**

  ```python
  import tkinter.messagebox
  
  try:
      from tkinter import *
      import fitz
      from tkinter.ttk import Progressbar
      from threading import Thread
      import math
      from PIL import Image
      import io
  except Exception as e:
      print(f"This error occured while importing neccesary modules or library {e}")
  
  ZOOM_X = 1.7  # X方向分辨率倍数
  ZOOM_Y = 1.7  # Y方向分辨率倍数
  
  
  class ShowPdf():
      img_object_li = []
      pdf_thread = None
  
      def pdf_view(self, master, ufd, width=1200, height=600, pdf_location="", bar=True, load="after"):
  
          self.frame = Frame(master, width=width, height=height, bg="white")
  
          scroll_y = Scrollbar(self.frame, orient="vertical")
          scroll_x = Scrollbar(self.frame, orient="horizontal")
  
          scroll_x.pack(fill="x", side="bottom")
          scroll_y.pack(fill="y", side="right")
  
          percentage_view = 0
          percentage_load = StringVar()
  
          if bar == True and load == "after":
              self.display_msg = Label(textvariable=percentage_load)
              self.display_msg.pack(pady=10)
  
              loading = Progressbar(self.frame, orient=HORIZONTAL, length=100, mode='determinate')
              loading.pack(side=TOP, fill=X)
  
          self.text = Text(self.frame, yscrollcommand=scroll_y.set, xscrollcommand=scroll_x.set, width=width,
                           height=height)
          self.text.pack(side="left")
  
          self.text.tag_configure("center", justify='center')
  
          scroll_x.config(command=self.text.xview)
          scroll_y.config(command=self.text.yview)
  
          def add_img():
  
              precentage_dicide = 0
              # open_pdf = fitz.open(pdf_location)  # 基于文件打开
  
              # 基于bytes格式打开
              try:
                  with open(pdf_location, 'r') as rf:
                      pdf_content = rf.read()
                  pdf_content = ufd.decrypt_content(pdf_content)
                  open_pdf = fitz.open(stream=pdf_content, filetype='bytes')
              except Exception as error:
                  tkinter.messagebox.showinfo('Warning', '{}，文件识别异常，请使用官方指定文件！'.format(pdf_location))
                  return
  
              for page in open_pdf:
  
                  # 提高page分辨率
                  trans = fitz.Matrix(ZOOM_X, ZOOM_Y)  # 分辨率倍数 ZOOM_X * ZOOM_Y
  
                  # 转换page为pyimage
                  pix = page.getPixmap(matrix=trans, alpha=False)
                  pix1 = fitz.Pixmap(pix, 0) if pix.alpha else pix
                  img = pix1.getImageData("ppm")
  
                  # resize page
                  # img = self._resize_pdf_page(img, width, height)
  
                  timg = PhotoImage(data=img)
                  self.img_object_li.append(timg)
  
                  if bar == True and load == "after":
                      precentage_dicide = precentage_dicide + 1
                      percentage_view = (float(precentage_dicide) / float(len(open_pdf)) * float(100))
                      loading['value'] = percentage_view
                      percentage_load.set(f"Please wait!, your pdf is loading {int(math.floor(percentage_view))}%")
              if bar == True and load == "after":
                  loading.pack_forget()
                  self.display_msg.pack_forget()
  
              for i in self.img_object_li:
                  try:
                      self.text.image_create(END, image=i)
                      try:
                          if i.width() >= width:
                              self.text.tag_add("left", 1.0, "end")  # left, center
                          else:
                              self.text.tag_add("center", 1.0, "end")
                      except Exception as _:
                          pass
  
                      self.text.insert(END, "\n\n")
                  except Exception as error:
                      print(error)
              self.text.configure(state="disabled")
  
          def start_pack():
              self.pdf_thread = Thread(target=add_img)
              self.pdf_thread.start()
  
          if load == "after":
              master.after(250, start_pack)
          else:
              start_pack()
  
          return self.frame
  ```

  

