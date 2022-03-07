---
title: How To Invoke Method On Main Thread
thumbnail: https://i.imgur.com/NRXpcll.png
categories: 
	- 技術文件
    - .NET
	- C#
tags: [CSharp, .NET]
date: 2022/03/07
---

{% img https://i.imgur.com/NRXpcll.png 500 %}

在寫VSTO或WinForm的時候，如果需要在其他執行緒(Other Thread)上更新主執行緒(Main Thread)的UI元件時，會引發[InvalidOperationException](https://docs.microsoft.com/zh-tw/dotnet/api/system.invalidoperationexception?view=net-6.0)這個例外，這是因為執行緒與建立控制項的執行緒不同所造成的問題。
今天來整理一下幾種方法，可以在其他執行緒(Other Thread)呼叫主執行緒(Main Thread)來執行Method。
<!-- more --> 

## 範例

### 1. [Control.Invoke](https://docs.microsoft.com/zh-tw/dotnet/api/system.windows.forms.control.invoke?view=windowsdesktop-6.0&viewFallbackFrom=net-5.0)
```c
        Control control = new Control();
        control.CreateControl();
        control.Invoke(new MethodInvoker(() =>
        {
            // TO DO ...
        }));
```
### 2. [SynchronizationContext.Send](https://docs.microsoft.com/zh-tw/dotnet/api/system.threading.synchronizationcontext.send?view=net-6.0)
```c
        SynchronizationContext synchronizationContext = WindowsFormsSynchronizationContext.Current ?? new WindowsFormsSynchronizationContext();
        synchronizationContext.Send(d =>
        {
            // TO DO ...
        }, null);
```
### 3. [Dispatcher.BeginInvoke](https://docs.microsoft.com/zh-tw/dotnet/api/system.windows.threading.dispatcher.begininvoke?view=windowsdesktop-6.0)
```c
        Dispatcher dispatcher = Dispatcher.CurrentDispatcher;
        dispatcher.BeginInvoke(new Action(() =>
        {
            // TO DO ...
        }));
```
### 4. [DispatcherObservable.ObserveOnDispatcher](https://reurl.cc/ZrvYya)
```c
        Observable.ObserveOnDispatcher().Subscribe(o =>
        {
            // TO DO ...
        });
```

## 參考
1. [Control.Invoke](https://docs.microsoft.com/zh-tw/dotnet/api/system.windows.forms.control.invoke?view=windowsdesktop-6.0&viewFallbackFrom=net-5.0)
2. [SynchronizationContext.Send](https://docs.microsoft.com/zh-tw/dotnet/api/system.threading.synchronizationcontext.send?view=net-6.0)
3. [Dispatcher.BeginInvoke](https://docs.microsoft.com/zh-tw/dotnet/api/system.windows.threading.dispatcher.begininvoke?view=windowsdesktop-6.0)
4. [DispatcherObservable.ObserveOnDispatcher](https://reurl.cc/ZrvYya)
