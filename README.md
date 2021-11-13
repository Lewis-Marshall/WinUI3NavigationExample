# WinUI 3 Navigation Example

## Introduction

Example of a WinUI 3 NavigationView application using the Windows 11 styles. 
On Windows 11 this should look identical to a comparable WinUI 2 UWP app, other than the omission of the Mica material. 

![Screenshot](docs/images/img.jpg)

## How It Works
### Style
In App.xaml.cs we get the AppWindow for the main window via Win32Interop. 
We then modify the AppWindow instead of the main window or CoreWindow, like in UWP.
SetTitleBar appears to be bugged so we set the title bar draggable region instead.
```c#
using Microsoft.UI.Windowing;
```
```c#
if (AppWindowTitleBar.IsCustomizationSupported()) //Run only on Windows 11
{
  m_window.SizeChanged += SizeChanged; //Register handler for setting draggable rects

  appWindow = GetAppWindow(m_window); //Set ExtendsContentIntoTitleBar for the AppWindow not the window
  appWindow.TitleBar.ExtendsContentIntoTitleBar = true;
  appWindow.TitleBar.ButtonBackgroundColor = Colors.Transparent;
  appWindow.TitleBar.ButtonInactiveBackgroundColor = Colors.Transparent;
}
```
```c#
private AppWindow GetAppWindow(Window window)
{
    IntPtr hwnd = WinRT.Interop.WindowNative.GetWindowHandle(window);
    WindowId windowId = Win32Interop.GetWindowIdFromWindow(hwnd);
    return AppWindow.GetFromWindowId(windowId);
}
```
```c#
private void SizeChanged(object sender, WindowSizeChangedEventArgs args)
{
    //Update the title bar draggable region. We need to indent from the left both for the nav back button and to avoid the system menu
    Windows.Graphics.RectInt32[] rects = new Windows.Graphics.RectInt32[] { new Windows.Graphics.RectInt32(48, 0, (int)args.Size.Width - 48, 48) }; 
    appWindow.TitleBar.SetDragRectangles(rects);
}
```

We can then create space for a title bar in App.xaml
```xaml
<Thickness x:Key="NavigationViewContentMargin">0,48,0,0</Thickness>
```

### Navigation

A simple navigation example is provided in the MainWindow files. This is based on the documentation but simplified somewhat by including the full addess of each page in the NavigationViewItem's Tag.
```xaml
<NavigationViewItem Icon="Home" Content="Home" Tag="WinUI3NavigationExample.Views.HomePage" />
```


