# Mobile Game Dev Engines
Doing some research among major game engines to find what fits our requirements best
for creating a cross platform 2D mobile game.

## Requirements
* 2D (doubt I will ever want to do 3D work)
* dev software available on linux/windows/mac
* cost: free or no upfront costs at least
* language: any OO language really, scripting engine could be nice? Dont know how those play in to game engines
* compiles to: android, ios, web (optional but desired)

## Options
Boiled down from [/r/gamedev engine faq page](https://www.reddit.com/r/gamedev/wiki/engine_faq):

Engine Name|Platforms (dev)| Paltforms(deploy)|2D/3D|Language|Cost|Pros|Cons
---|---|---|---|---|---|---|---
Unreal Engine 4|Win, Mac, Linux|Win, iOS,Mac, PS4, XB1, HTML 5, Android, Linux, SteamOS|3D & 2D|C++, Blueprint Visual Scripting|5% of profits from sales|Good documentation, Powerful tools/editor, active community, Does not require active subscription to release a game, Oculus/Morpheus VR Support|Not beginner friendly
Unity|Win/Mac|Win, Mac, Linux, Web, iOS, Android, Flash, PS3, Xbox360, Wii|2D/3D|C#, Boo, UnityScript|Free for commercial basic on Win/Mac, $$$ for other platforms, pro license|Great Asset Pipeline, 1-Click deploy to different platforms, Large, Active community, good documentation|Messy with version control, not as graphically capable as AAA engines
Cocos2D|Win, Mac, Linux (some platforms need cocos2d-x)|Pc, Mac, Linux, iPhone, Windows Phone, Android in development|2D|Depending on platform, C++, C# or Objective C|Free|Large number of resources online, very powerful for 2D games, widely used for commercial ios development|not as active a community
Allegro|???|Linux, Win, Mac, iOS, Android|2D|C, C++|Free|low-level functionality, good documentation, active community|new version may be lacking in some documentation, low-level

### Unreal Engine 4
Notes/Follow ups:
* I think ill need to do some extra hacking to get android working
* do I need to be on a mac to build for iOS?

#### Installing
[Fedora 23](unreal-engine-install.md)

#### Tutorial
* [unreal engine 4 2D game dev tutorial series](http://www.gamefromscratch.com/page/Unreal-Engine-2D-Game-Development-Tutorial-Series.aspx)

### Unity
Notes/Follow ups:
* confirm linux support - "[experiemental](https://blogs.unity3d.com/2015/08/26/unity-comes-to-linux-experimental-build-now-available/)"
* potential cost, make sure out use case wont cost $$$ (licenses in the thousands of dollars potentially)

#### Installing (Linux)
1. `wget http://download.unity3d.com/download_unity/unity-editor-installer-5.1.0f3+2015091501.sh`


### Cocos2D
Notes/Follow ups:
* Follow up on "android in development" - seems to be supported
* cocos2d vs cocos2d-x - x is the platform Cocos2D extension (initially oriented for iphone)
* Cocos Studio for integrated design environment (much less powerful than unity or unreal)
* no web deployment support, is this a problem?
* C++, learning curve

Seems to be very popular but alot of literature is saying a more full fledged engine like
Unity or Unreal would be better if you can use them.

### Allegro
Notes/Follow ups:
* follow up on devlopment platforms

## Additional Resources
* [/r/gamedev game engine faq](https://www.reddit.com/r/gamedev/wiki/engine_faq)
* [unity vs unreal](http://www.makinggames.biz/feature/unreal-vs-unity-which-engine-is-better-for-mobile-games,8472.html)
* [unity vs cocos2d](https://en.yeeply.com/blog/comparison-between-unity-and-cocos2d-platforms-for-2d-game-developments/)
