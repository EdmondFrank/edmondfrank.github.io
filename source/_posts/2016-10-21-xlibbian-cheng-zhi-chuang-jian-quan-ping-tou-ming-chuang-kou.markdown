---
layout: post
title: "Xlib编程之-创建全屏透明窗口"
date: 2016-10-21 20:11:20 +0800
comments: true
categories: 练习代码
---
##Xlib编程之-创建全屏透明窗口
```c
 //source file : win.c
 #include <X11/Xlib.h>
 #include <X11/Xutil.h>
 int main(int argc, char* argv[])
 {
    Display* display = XOpenDisplay(NULL);

    XVisualInfo vinfo;
    XMatchVisualInfo(display, DefaultScreen(display), 32, TrueColor, &vinfo);

    XSetWindowAttributes attr;//窗口属性设置
    attr.colormap = XCreateColormap(display, DefaultRootWindow(display), vinfo.visual, AllocNone);
    attr.border_pixel = 0;
    attr.background_pixel = 0;

    //取输出设备的长宽像素
    int screen = DefaultScreen(display);
    int height = DisplayHeight(display,screen);
    int width = DisplayWidth(display,screen);

    //开始创建窗口
    Window win = XCreateWindow(display, DefaultRootWindow(display), 0, 0,width, height , 0, vinfo.depth, InputOutput, vinfo.visual,  CWColormap | CWBorderPixel | CWBackPixel, &attr);
    XSelectInput(display, win, StructureNotifyMask);
    //GC gc = XCreateGC(display, win, 0, 0);

    /*
    Atom wm_delete_window = XInternAtom(display, "WM_DELETE_WINDOW", 0);
    XSetWMProtocols(display, win, &wm_delete_window, 1);
    */
    XMapWindow(display, win);
    XMoveWindow(display,win,0,0);

    XFlush(display);//刷新输出设备
    int keep_running = 1;
    XEvent event;

    //窗口事件监听尝试
    while (keep_running) {
        XNextEvent(display, &event);
        switch(event.type) {
            case ClientMessage:
                if (event.xclient.message_type == XInternAtom(display, "WM_PROTOCOLS", 1) && (Atom)event.xclient.data.l[0] == XInternAtom(display,"WM_DELETE_WINDOW",1))
                    keep_running = 0;
                break;
            default:
                break;
        }
    }

    //结束销毁
    XDestroyWindow(display, win);
    XCloseDisplay(display);
    return 0;
 }
```

**编译**
> gcc -o win ./win.c -L/usr/lib -lX11

**That is All**

