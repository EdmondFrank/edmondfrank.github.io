---
layout: post
title: "树莓派和L298N电机驱动模块实现智能小车控制"
date: 2018-02-05 14:56:53 +0800
comments: true
categories: 树莓派
---
﻿<h1 id="树莓派与l298n驱动模块实现智能小车控制">树莓派与L298N驱动模块实现智能小车控制</h1>



<h2 id="准备">准备</h2>

<p>首先在讲整体实现之前，笔者先附上自己的开发环境以及使用到的工具、硬件等。</p>

<p>用到的工具有：</p>

<ul>
<li>树莓派3代（自带wifi模块）</li>
<li>L298N电机驱动板</li>
<li>USB移动电源一个（为树莓派供电） </li>
<li>电池组一组（为驱动模块、智能小车的电机供电）</li>
<li>智能小车底盘</li>
<li>杜邦线若干</li>
<li>电脑一台（我的系统：Ubuntu 16.04 LTS Python3.5）</li>
</ul>



<h2 id="树莓派的gpio引脚定义">树莓派的GPIO引脚定义</h2>

<p>树莓派的GPIO引脚共分为两种类型，一种是<strong>PHYSICAL NUMBERING</strong> <br>
<img src="https://ws1.sinaimg.cn/large/a3d23450gy1fo5jigs4fdj20o00b2whc.jpg" alt="" title=""></p>

<p>单纯地用从上至下，从左至右的顺序来定义引脚。</p>

<p>另外一种引脚定义方式是<strong>GPIO NUMBERING</strong> <br>
<img src="https://ws1.sinaimg.cn/large/a3d23450gy1fo5jkecmuvj20o408rdi1.jpg" alt="" title=""></p>

<p>采用特殊（未知）的方式来标记GPIO接口</p>

<p><strong>这里，我采用的是第一种使用的引脚定义的方式。</strong></p>

<h2 id="l298n电机驱动模块">L298N电机驱动模块</h2>

<p>L298N 是一种双H桥电机驱动芯片，其中每个H桥可以提供2A的电流，功率部分的供电电压范围是2.5-48v，逻辑部分5v供电，接受5vTTL电平。一般情况下，功率部分的电压应大于6V否则芯片可能不能正常工作。</p>

<p>实物图如下 <br>
<img src="https://ws1.sinaimg.cn/large/a3d23450gy1fo5jpmguxqj20j60kg40s.jpg" alt="" title=""></p>

<p>使用说明如下 <br>
<img src="https://ws1.sinaimg.cn/large/a3d23450gy1fo5jql5sdpj20j60c6q42.jpg" alt="" title=""></p>



<h2 id="我的接线图">我的接线图</h2>

<p><img src="https://ws1.sinaimg.cn/large/a3d23450gy1fo5kpjg1dij20zk0jwabr.jpg" alt="" title=""> <br>
<img src="https://ws1.sinaimg.cn/large/a3d23450gy1fo5koxpcwcj20zk0jwtae.jpg" alt="" title=""></p>

<h2 id="代码实现">代码实现</h2>
```python
import argparse
import tornado.ioloop
import tornado.web
from datetime import datetime
import os
from operator import itemgetter
import RPi.GPIO as GPIO
import requests
from time import sleep

class PostHandler(tornado.web.RequestHandler):

    # I don't understand decorators, but this fixed my "can't set attribute" error
    @property
    def settings(self):
        return self._settings

    @settings.setter
    def settings(self,settings):
        self._settings = settings

    def initialize(self, settings):
        self.settings = settings

    def post(self):
        timestamp = datetime.now()
        data_json = tornado.escape.json_decode(self.request.body)
        allowed_commands = set(['37','38','39','40'])
        command = data_json['command']
        command = list(command.keys())
        command = set(command)
        command = allowed_commands & command
        file_path = str(os.path.dirname(os.path.realpath(__file__)))+"/session.txt"
        log_entry = str(command)+" "+str(timestamp)
        log_entries.append((command,timestamp))
        with open(file_path,"a") as writer:
            writer.write(log_entry+"\n")
        print(log_entry)
        speed = self.settings['speed']
        if '37' in command:
            motor.forward_left(speed)
        elif '38' in command:
            print("forward")
            motor.forward(100)
        elif '39' in command:
            motor.forward_right(speed)
        elif '40' in command:
            print("backward")
            motor.backward(100)
        else:
            motor.stop()
        
# This only works on data from the same live python process. It doesn't 
# read from the session.txt file. It only sorts data from the active
# python process. This is required because it reads from a list instead
# of a file  on data from the same live python process. It doesn't 
# read from the session.txt file. It only sorts data from the active
# log_entries python list
class StoreLogEntriesHandler(tornado.web.RequestHandler):
    def get(self):
        file_path = str(os.path.dirname(os.path.realpath(__file__)))+"/clean_session.txt"
        sorted_log_entries = sorted(log_entries,key=itemgetter(1))
        prev_command = set()
        allowed_commands = set(['38','37','39','40'])
        for log_entry in sorted_log_entries:
            command = log_entry[0]
            timestamp = log_entry[1]
            if len(command ^ prev_command) > 0:
                prev_command = command
                with open(file_path,"a") as writer:
                    readable_command = []
                    for element in list(command):
                        if element == '37':
                            readable_command.append("left")
                        if element == '38':
                            readable_command.append("up")
                        if element == '39':
                            readable_command.append("right")
                        if element == '40':
                            readable_command.append("down")
                    log_entry = str(list(readable_command))+" "+str(timestamp)
                    writer.write(log_entry+"\n")
                print(log_entry)
        self.write("Finished")
   
class MultipleKeysHandler(tornado.web.RequestHandler):

    def get(self):
        print("HelloWorld")
        self.write('''
                <!DOCTYPE html>
                <html>
                    <head>
                        <script src="https://cdn.bootcss.com/jquery/3.2.1/jquery.min.js"></script>
                        <script>
                            var keys = {};

                            $(document).keydown(function (e) {
                                keys[e.which] = true;
                                
                                var json_upload = JSON.stringify({command:keys});
                                var xmlhttp = new XMLHttpRequest(); 
                                xmlhttp.open("POST", "/post");
                                xmlhttp.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");
                                xmlhttp.send(json_upload);

                                printKeys();
                            });

                            $(document).keyup(function (e) {
                                delete keys[e.which];
                                
                                var json_upload = JSON.stringify({command:keys});
                                var xmlhttp = new XMLHttpRequest(); 
                                xmlhttp.open("POST", "/post");
                                xmlhttp.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");
                                xmlhttp.send(json_upload);

                                printKeys();
                            });

                            function printKeys() {
                                var hcommandtml = '';
                                for (var i in keys) {
                                    if (!keys.hasOwnProperty(i)) continue;
                                    html += '<p>' + i + '</p>';
                                }
                                $('#out').html(html);
                            }

                        </script>
                    </head>
                    <body>
                        Click in thiscommand frame, then try holding down some keys
                        <div id="out"></div>
                    </body>
                </html>
            ''')


# class Motor:
#     def __init__(self, pinForward, pinBackward, pinControlStraight,
#     pinLeft, pinRight, pinControlSteering):
#         self.pinForward = pinForward
#         self.pinBackward = pinBackward
#         self.pinControlStraight = pinControlStraight
#         self.pinLeft = pinLeft
#         self.pinRight = pinRight
#         self.pinControlSteering = pinControlSteering
#         GPIO.setup(self.pinForward, GPIO.OUT)
#         GPIO.setup(self.pinBackward, GPIO.OUT)
#         GPIO.setup(self.pinControlStraight, GPIO.OUT)

#         GPIO.setup(self.pinLeft, GPIO.OUT)
#         GPIO.setup(self.pinRight, GPIO.OUT)
#         GPIO.setup(self.pinControlSteering, GPIO.OUT)

#         self.pwm_forward = GPIO.PWM(self.pinForward, 100)
#         self.pwm_backward = GPIO.PWM(self.pinBackward, 100)
#         self.pwm_forward.start(0)
#         self.pwm_backward.start(0)

#         self.pwm_left = GPIO.PWM(self.pinLeft, 100)
#         self.pwm_right = GPIO.PWM(self.pinRight, 100)
#         self.pwm_left.start(0)
#         self.pwm_right.start(0)

#         GPIO.output(self.pinControlStraight,GPIO.HIGH) 
#         GPIO.output(self.pinControlSteering,GPIO.HIGH) 

#     def forward(self, speed):
#         """ pinForward is the forward Pin, so we change its duty
#              cycle according to speed. """
#         self.pwm_backward.ChangeDutyCycle(0)
#         self.pwm_forward.ChangeDutyCycle(speed)    

#     def forward_left(self, speed):
#         """ pinForward is the forward Pin, so we change its duty
#              cycle according to speed. """
#         self.pwm_backward.ChangeDutyCycle(0)
#         self.pwm_forward.ChangeDutyCycle(speed)  
#         self.pwm_right.ChangeDutyCycle(0)
#         self.pwm_left.ChangeDutyCycle(100)   

#     def forward_right(self, speed):
#         """ pinForward is the forward Pin, so we change its duty
#              cycle according to speed. """
#         self.pwm_backward.ChangeDutyCycle(0)
#         self.pwm_forward.ChangeDutyCycle(speed)
#         self.pwm_left.ChangeDutyCycle(0)
#         self.pwm_right.ChangeDutyCycle(100)

#     def backward(self, speed):
#         """ pinBackward is the forward Pin, so we change its duty
#              cycle according to speed. """

#         self.pwm_forward.ChangeDutyCycle(0)
#         self.pwm_backward.ChangeDutyCycle(speed)

#     def left(self, speed):
#         """ pinForward is the forward Pin, so we change its duty
#              cycle according to speed. """
#         self.pwm_right.ChangeDutyCycle(0)
#         self.pwm_left.ChangeDutyCycle(speed)  

#     def right(self, speed):
#         """ pinForward is the forward Pin, so we change its duty
#              cycle according to speed. """
#         self.pwm_left.ChangeDutyCycle(0)
#         self.pwm_right.ChangeDutyCycle(speed)   

#     def stop(self):
#         """ Set the duty cycle of both control pins to zero to stop the motor. """

#         self.pwm_forward.ChangeDutyCycle(0)
#         self.pwm_backward.ChangeDutyCycle(0)
#         self.pwm_left.ChangeDutyCycle(0)
#         self.pwm_right.ChangeDutyCycle(0)

class Motor:
    def __init__(self, pinForward, pinBackward, pinForward2,
     pinBackward2,pinLeft, pinRight):
        """ Initialize the motor with its control pins and start pulse-width
             modulation """

        self.pinForward = pinForward
        self.pinBackward = pinBackward
        self.pinForward2 = pinForward2
        self.pinLeft = pinLeft
        self.pinRight = pinRight
        self.pinBackward2 = pinBackward2


        GPIO.setup(self.pinLeft, GPIO.OUT)
        GPIO.setup(self.pinRight, GPIO.OUT)

        GPIO.setup(self.pinForward, GPIO.OUT)
        GPIO.setup(self.pinBackward, GPIO.OUT)
        GPIO.setup(self.pinForward2, GPIO.OUT)
        GPIO.setup(self.pinBackward2, GPIO.OUT)

        self.pwm_left = GPIO.PWM(self.pinLeft, 100)
        self.pwm_right = GPIO.PWM(self.pinRight, 100)
        self.pwm_left.start(0)
        self.pwm_right.start(0)
    def forward(self, speed):
        self.pwm_right.ChangeDutyCycle(speed)
        self.pwm_left.ChangeDutyCycle(speed)
        GPIO.output(self.pinForward, True)
        GPIO.output(self.pinBackward, False)
        GPIO.output(self.pinForward2, True)
        GPIO.output(self.pinBackward2, False)
    def backward(self, speed):
        self.pwm_right.ChangeDutyCycle(speed)
        self.pwm_left.ChangeDutyCycle(speed)
        GPIO.output(self.pinForward, False)
        GPIO.output(self.pinBackward,  True)
        GPIO.output(self.pinForward2, False)
        GPIO.output(self.pinBackward2,  True)

    def forward_right(self, speed):
        self.pwm_right.ChangeDutyCycle(speed)
        self.pwm_left.ChangeDutyCycle(100)
        # GPIO.output(self.pinForward, True)
        # GPIO.output(self.pinBackward, False)
        # GPIO.output(self.pinForward2, True)
        # GPIO.output(self.pinBackward2, False)
    def forward_left(self, speed):
        self.pwm_right.ChangeDutyCycle(100)
        self.pwm_left.ChangeDutyCycle(speed)
        # GPIO.output(self.pinForward, True)
        # GPIO.output(self.pinBackward, False)
        # GPIO.output(self.pinForward2, True)
        # GPIO.output(self.pinBackward2, False)

    def stop(self):
        """ Set the duty cycle of both control pins to zero 
            to stop the motor. """
        self.pwm_left.ChangeDutyCycle(0)
        self.pwm_right.ChangeDutyCycle(0)  
        GPIO.output(self.pinForward,0) 
        GPIO.output(self.pinBackward,0) 
        GPIO.output(self.pinForward2,0)
        GPIO.output(self.pinBackward2,0)
def make_app(settings):
    return tornado.web.Application([
        (r"/drive",MultipleKeysHandler),(r"/post", PostHandler, {'settings':settings}),
        (r"/StoreLogEntries",StoreLogEntriesHandler)
    ])

if __name__ == "__main__":

    # Parse CLI args
    ap = argparse.ArgumentParser()
    ap.add_argument("-s", "--speed_percent", required=True, help="Between 0 and 100")
    args = vars(ap.parse_args())
    #GPIO.cleanup(0)
    GPIO.setmode(GPIO.BOARD)
    motor = Motor(18, 19, 21, 22, 23, 24)
    log_entries = []
    settings = {'speed':float(args['speed_percent'])}
    app = make_app(settings)
    app.listen(81)
    tornado.ioloop.IOLoop.current().start()

```


<h2 id="使用说明">使用说明</h2>

<p>首先，按照依赖配置好树莓派中的python环境，建议使用python3以上版本。</p>

<p>然后在树莓派接入路由后，采用远程终端的方式，运行以上的python脚本。</p>

<pre><code>sudo python3 drive_api.py --speed_percent 5
</code></pre>

<p><strong>注：非root用户一定要加上sudo，否则无法读写树莓派的GPIO口。</strong></p>

<p>最后，通过电脑在同一个内网内使用浏览器打开地址<a>192.168.1.208:81/drive</a>（这个地址根据你的树莓派接入路由的实际地址而定，以上为笔者实验使用地址，仅供格式参考）。</p>

<p>在打开的网页内，通过电脑的方向键就可以对树莓派进行“驾驶”了。</p>
