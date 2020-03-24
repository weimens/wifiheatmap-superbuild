# wifiheatmap-superbuild

Overview
--------

This is a cmake superbuid repository to compile the necessary dependencies to build [wifiheatmap](https://github.com/weimens/wifiheatmap) for android.
This currently will only work on linux.

Setup
-----

* Make sure that you can compile the linux version of [wifiheatmap](https://github.com/weimens/wifiheatmap).
* Setup [Qt for Android](https://doc.qt.io/qt-5/android-getting-started.html)
 * Android ndk must be version ndk-r20b or lower.
 * Try at least to compile and deploy a simple android program with Qt Creator before going to the next step.

Compiling
---------

Get the source code,

```
git clone https://github.com/weimens/wifiheatmap.git wifiheatmap-source
git clone https://github.com/weimens/wifiheatmap-superbuild.git wifiheatmap-superbuild
```

Open the ```wifiheatmap-superbuild/CMakeLists.txt``` file with Qt Creator and select the kit used before to build an android package.
To run cmake click on ```Build->Run CMake```. This outputs something like

```
Boost_INCLUDE_DIR:PATH=.../BoostForAndroid-build/build/out/x86/include/boost-1_69
CGAL_DIR:PATH=.../CGAL
QuaZip5_DIR:PATH=.../quazip-install/usr/local/lib/cmake/QuaZip5
CGAL_DISABLE_GMP:BOOL=ON
CGAL_NO_GMP:BOOL=ON
WITH_CGAL_Core:BOOL=OFF
WITH_CGAL_Qt5:BOOL=OFF
WITH_CGAL_ImageIO:BOOL=OFF
```

These are the cmake arguments you need the pass to the wifiheatmap cmake configuration later on.
But before you have to build the dependencies.
```Build->Build Project "wifiheatmap-superbuild"```

Now click on ```Tools->Options...``` and then ```Kits->Kits```.
Clone the kit that you used to build wifiheatmap-superbuild and change the CMake Configuration.
Copy and past the CMAKE Configuration that was generated by the wifiheatmap-superbuild cmake command.
Now you can build and deploy wifiheatmap on android with this kit.
For each android ABI (x86, armeabi-v7a, ...) you would like to build wifiheatmap for, you have to repeat the superbuild and the kit configuration.
