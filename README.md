### Windows

ICQ for windows is compiled by Visual Studio.

The solution file is `icq.sln`

To build it, you need to provide three environment variables.

- `QT_PATH` - path to dir containing the built Qt version (dir with folders `bin`, `include`, `lib`)
- `BOOST_INCLUDE` - path for boost inclusions (dir containing folder `boost`)
- `BOOST_LIB` - path to boost libraries

### MacOS

ICQ for Mac is compiled by Xcode and does not require any dependencies resolving.
Xcode project file is `mac/ICQ/ICQ.xcodeproj`

### Linux

ICQ for Linux is compiled by Qt creator, so you need to prepare Qt environment first.

Qt may be installed from the official installer (see Qt official website) or compiled from source code and linked statically/dynamically.

To configure Qt, we use the following command line:

    ./configure -static -release -c++11 -no-opengl -no-harfbuzz \
      -no-audio-backend -system-xkbcommon-x11 -dbus-linked -no-openssl -qt-xcb \
      -confirm-license -opensource -qt-zlib -qt-libjpeg -qt-libpng

You will also require the following packages, which can be obtained via your package manager:

    libfontconfig1-dev
    libfreetype6-dev
    libx11-dev
    libxext-dev
    libxfixes-dev
    libxi-dev
    libxrender-dev
    libxcb1-dev
    libx11-xcb-dev
    libxcb-glx0-dev

Additionally, we patched Qt source code:

1. `qtbase/src/gui/text/qtextlayout.cpp`
```cpp
if (lbh.currentPosition > 0 && lbh.currentPosition < end
                && attributes[lbh.currentPosition].lineBreak
                && eng->layoutData->string.at(lbh.currentPosition - 1).unicode() == QChar::SoftHyphen) {
```
   * comment or remove code block in `if` construction
   
2. `qtbase/src/gui/text/qtextengine.cpp`
```cpp
if (si->position + itemLength >= lineEnd
        && eng->layoutData->string.at(lineEnd - 1).unicode() == QChar::SoftHyphen)
        glyphs.attributes[glyphsEnd - 1].dontPrint = false;
```
   * change `false` to `true`

In order to install the prepared version of Qt in Qt Creator, use official Qt manuals.

We provides two `.pro` files containing build configuration for the project:

- `corelib/corelib/corelib.pro` - core library
- `gui/gui.pro` - gui (executable file)

In `gui.pro`, you may switch between `x32` and `x64` architecture, as well as external and internal resources linking (`CONFIG` variable).
You also have to provide a path to the directory, containing `corelib.a` file, which is obtained by building `corelib.pro` project (`CORELIB_PATH` variable).

Before building `gui.pro` file, you need to install `libqt4-dev` package and execute `config_linux.sh` in trunk root.

Additional library dependencies can be found in `gui.pro` or by following errors during the linkage process = )

The missing dependencies can be installed via your package manager.
