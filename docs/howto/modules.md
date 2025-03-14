# How-To Modules

Here we provide a hopefully thorough explanation of how to set-up, build, and use GodotSteam. You can, of course, skip all this and just download our pre-compiles or plug-in.

---

## 1a. Downloading

By far the easiest way to use GodotSteam is to download our pre-compiled editors and templates; especially good for folks who don't want to set up the tools for compiling and just want to get going.

- [x] Download the [pre-compiled editor from the Releases section](https://github.com/CoaguCo-Industries/GodotSteam/releases){ target="_blank" } and unpack it.
- [x] Alternatively, for Godot 3.x, you can download and install the [GDNative plug-in through Godot Asset Library](https://godotengine.org/asset-library/asset/1045){ target="_blank" }.
- [x] Alternatively, for Godot 4.x, you can download and install the [GDExtension 4.0](https://godotengine.org/asset-library/asset/1768){ target="_blank" } or [GDExtension 4.1](https://godotengine.org/asset-library/asset/1972){ target="_blank" } plug-in through Godot Asset Library.
- [x] Everything you need should be included.

At this point you can skip all the following steps and check out our tutorials to learn more about integrating Steamworks or just explore the SDK!

---

## 1b. Compile Yourself

For those of you who are comfortable compiling or want to give it a shot, here are some steps to follow.

- [x] Set your system up for [compiling based on Godot's recommendations / required tools.](https://docs.godotengine.org/en/stable/development/compiling/index.html){ target="_blank" }
- [x] Download and unpack the [Godot source](https://github.com/godotengine/godot){ target="_blank" }.
- [x] Acquire the GodotSteam source either by downloading it or cloning the repo:
    * [Download the godot3 or godot4 branch from our repository](https://github.com/CoaguCo-Industries/GodotSteam){ target="_blank" } then unpack it into a folder called **godotsteam** inside your Godot Engine source code **/modules** folder.
        * You will have to create the **godotsteam** folder and it must be named exactly this.
    * Alternatively, clone the godot3 or godot4 branch from our repository into your Godot Engine source code **/modules** folder
        * Use ````git clone -b godot3 https://github.com/CoaguCo-Industries/GodotSteam.git godotsteam```` for the Godot 3.x version
        * Use ````git clone -b godot4 https://github.com/CoaguCo-Industries/GodotSteam.git godotsteam```` for the Godot 4.x version
- [x] Download and unpack the [Steamworks SDK](https://partner.steamgames.com){ target="_blank" }.
    * This requires a Steam developer account.

---

## 2. Setting Up the SDK

Move the following from the unzipped Steamworks SDK to the **/modules/godotsteam/sdk/** folder:
````
    sdk/public/
    sdk/redistributable_bin/
````

---

## 3. Double-Checking Folder / File Structure

The compiling directory contents should now look like this:
````
    godotsteam/sdk/public/*
    godotsteam/sdk/redistributable_bin/*
    godotsteam/SCsub
    godotsteam/config.py
    godotsteam/godotsteam.cpp
    godotsteam/godotsteam.h
    godotsteam/register_types.cpp
    godotsteam/register_types.h
````

You can also just put the godotsteam directory where ever you like and just apply the ````custom_modules=.../path/to/dir/godotsteam```` flag in SCONS when compiling. Make sure the ````custom_modules=```` flag points to where the godotsteam folder is located.

---

## 4. Compiling Time

Recompile for your platform:

!!! godotsteam "For editors"
    === "Godot 2.x, 3.x"
        ````scons platform=<your platform> production=yes tools=yes target=release_debug````
    === "Godot 4.x"
        ````scons platform=<your platform> tools=yes target=editor````

!!! godotsteam "For debug templates"
    === "Godot 2.x, 3.x"
        ````scons platform=<your platform> production=yes tools=no target=release_debug````
    === "Godot 4.x"
        ````scons platform=<your platform> tools=no target=template_debug````

!!! godotsteam "For release templates"
    === "Godot 2.x, 3.x"
        ````scons platform=<your platform> production=yes tools=no target=release````
    === "Godot 4.x"
        ````scons platform=<your platform> tools=no target=template_release````

Some things to be aware of:

- If using Ubuntu 16.10 or higher and having issues with PIE security in GCC, use ````LINKFLAGS='-no-pie'```` to get an executable instead of a shared library.
- When creating templates for OSX, [please refer to this post for assistance as the documentation is a bit lacking.](http://steamcommunity.com/app/404790/discussions/0/364042703865087202/){ target="_blank" }

---

## 5. All Together Now

When recompiling the engine is finished, do the following before running it the first time:

- [x] Copy the shared library (steam_api), for your OS, from `sdk/redistributable_bin/` folders to the Godot binary location (by default in the godot source `/bin/` file but you can move them to a new folder).
    - These files are called **steam_api.dll, steam_api64.dll, libsteam_api.so, or libsteam_api.dylib**; no other files are needed.

The finished hierarchy should look like this (if you downloaded the pre-compiles, the editor files go in place of these tools files, which are the same thing):

!!! godotsteam "Linux 32/64-bit"
    ```
    libsteam_api.so
    ./godot.linux.tools.32 or ./godot.linux.tools.64
    ```
 
!!! godotsteam "MacOS"
    ```
    libsteam_api.dylib
    ./godot.osx.tools.32 or ./godot.osx.tools.64
    ```
  
!!! godotsteam "Windows"
    === "Windows 32-bit"
        ```
        steam_api.dll
        ./godot.windows.tools.32.exe
        ```
    === "Windows 64-bit"
        ```
        steam_api64.dll
        ./godot.windows.tools.64.exe
        ```

A lack of the **Steam API .dll/.so/.dylib** for your respective OS will cause the editor or game to fail and crash when testing or running the game _outside_ of the Steam client.

- **NOTE:** Some people report putting the Steam API file inside their project folder fixes some issues.
- **NOTE:** For MacOS, the `libsteam_api.dylib` must be in the `Content/MacOS/` folder in your application zip or the game will crash.
- **NOTE:** For Linux, you may have to load the overlay library for Steam overlay to work:
  ```
  export LD_PRELOAD=~/.local/share/Steam/ubuntu12_32/gameoverlayrenderer.so;~/.local/share/Steam/ubuntu12_64/gameoverlayrenderer.so
  
  or 
  
  export LD_PRELOAD=~/.local/share/Steam/ubuntu12_32/gameoverlayrenderer.so;
  export LD_PRELOAD=~/.local/share/Steam/ubuntu12_64/gameoverlayrenderer.so;
  ```
  This can be done in an .sh file that runs these before running your executable. This issue may not arise for all users and can also just be done by the user in a terminal separately. You can [read more about it in our Linux Caveats doc](../tutorials/linux_caveats.md).

---

## 6. Good to Go

From here you should be able to call various functions of Steamworks. You should be able to look up the functions in Godot itself under the search section. In addition, you should be able to [read the Steamworks API documentation](https://partner.steamgames.com/doc/){ target="_blank" } to see what all is available and cross-reference with GodotSteam's documentation.

---

## 7. Exporting / Shipping Your Game

For a full explanation of exporting and shipping your game with GodotSteam, [please refer to our Export and Shipping tutorial.](../tutorials/exporting_shipping.md)

That being said, when uploading your game to Steam, you _**must**_ upload your game's executable and **Steam API .dll/.so/.dylb** (steam_api.dll, steam_api64.dll, libsteam_api.dylib, and/or libsteam_api.so).