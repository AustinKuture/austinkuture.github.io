---
layout: wiki
title: TKinter GUI
categories: Tools
description: TKinter GUI
keywords: Windows, GUI, tkinter

---



# TKinter GUI



* **自定义进度条：**

  ```
  import Tkinter as tk
  import ttk as ttk
  root = tk.Tk()
  frame = tk.Frame(root)
  frame.grid()
  s = tkinter.ttk.Style()
  s.theme_use('clam')
  s.configure("red.Horizontal.TProgressbar", troughcolor='white', background='red', bordercolor='white')
  ttk.Progressbar(frame, style="red.Horizontal.TProgressbar", orient="horizontal",
                  length=600, mode="determinate", maximum=4, value=1).grid(row=1, column=1)
  frame.pack()
  ```

  进度条属性：

  | Element       | Defines                                                      |
  | ------------- | ------------------------------------------------------------ |
  | `troughcolor` | background of full widget                                    |
  | `background`  | background of progress bar                                   |
  | `darkcolor`   | bottom & right progress bar innermost border                 |
  | `lightcolor`  | top & left bar progress bar innermost border                 |
  | `bordercolor` | outline border of the progress bar (outside the above border) and the whole widget |

  ```
  TROUGH_COLOR = 'blue'
  BAR_COLOR = 'green'
  style.configure("bar.Horizontal.TProgressbar", troughcolor=TROUGH_COLOR, 
                  bordercolor=TROUGH_COLOR, background=BAR_COLOR, lightcolor=BAR_COLOR, 
                  darkcolor=BAR_COLOR)
  ```

  

