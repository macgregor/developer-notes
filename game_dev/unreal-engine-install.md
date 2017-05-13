# Installing Unreal Engine on Linux (Fedora 23)
This was a bit painful for me. Here goes...

## Clone Unreal Engine Source Code
1. create Epic games account and link github user - [details](https://wiki.unrealengine.com/GitHub_Setup)
2. clone UnrealEngine [source](https://github.com/EpicGames/UnrealEngine)

## Install Dependencies
The UnrealEngine `Setup.sh` will get alot of the dependencies but it will miss some

```bash
sudo dnf install dos2unix cmake gtk3-devel glib2-devel libgdiplus SDL2*
```

### clang
Make sure you install clang 3.5, `dnf install clang` will install clang 3.7 and that seems
to be the only version available in default dnf repositories at the time of writing.

```
sudo wget http://llvm.org/releases/3.5.0/clang+llvm-3.5.0-x86_64-fedora20.tar.xz
sudo tar xvf clang+llvm-3.5.0-x86_64-fedora20.tar.xz && cd clang+llvm-3.5.0-x86_64-fedora20 && sudo cp -R * /usr/local/
clang --version
```

## Build
Details can be found in `Engine/Build/BatchFiles/Linux/README.md` in your local repository

1. run `./Setup.sh`
2. run `./GenerateProjectFiles.sh`
3. `make CrashReportClient ShaderCompileWorker UnrealLightmass UnrealPak UE4Editor`
4. add `Engine/Binaries/Linux/` to path
5. start editor: `U4Editor`

## Troubleshooting:

### Segfault on starting UE4Editor
```bash
[2017.05.13-18.40.59:098][  0]LogInit:Error: _PlatformCreateOpenGLContextCore - Could not create OpenGL 4.3 context, SDL error: 'Could not create GL context: GLXBadFBConfig'
[2017.05.13-18.40.59:098][  0]LogRHI:Error: OpenGL 4.3 not supported by driver
[2017.05.13-18.40.59:099][  0]LogInit:Error: _PlatformCreateOpenGLContextCore - Could not create OpenGL 4.3 context, SDL error: 'Could not create GL context: GLXBadFBConfig'
```
Your video card doesnt support OpenGL or, in my case, you need to switch from integrated graphics to
your NVidia card:
```bash
primusrun Engine/Binaries/Linux/UE4Editor
```

See also: [Bumblebee](https://fedoraproject.org/wiki/Bumblebee)

## Additional Resources
* [building unreal engine 4 on fedora 20](https://forums.unrealengine.com/showthread.php?53451-Successful-4-6-0-build-on-Fedora-20-x86_64:)
