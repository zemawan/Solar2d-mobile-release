# Solar - android version Solar2d
Many people want to create apps and games, but don't have a computer. Soladroid was created especially for them as a transfer of the "Solar2d" engine (Corona SDK) to mobile devices with the ability to make exe and apk files.
Solar2d is a simple and powerful 2D game engine, its PC version: https://github.com/coronalabs/corona/releases

## Builder
Soladroid allows you to build your project in apk (Android) and Exe (Windows). Please note, native plugins do not work for Windows builds.

## Plugins
To create a native plugin for android, open the "__plugins__" folder in the root directory of the project (If it is not there, create it), create a new folder (Its name is the name of your plugin). Create 3 folders inside - java, libs, jniLibs.

### Java
Here is your java code. Create a plugin folder, then a folder with the name of your plugin and create a class there "LuaLoader.java ".

Code:
```java
package YOUR_PACKAGE;

import com.naef.jnlua.JavaFunction;
import com.naef.jnlua.LuaState;
import com.naef.jnlua.NamedJavaFunction;

@SuppressWarnings({"WeakerAccess", "unused"})
public class LuaLoader implements JavaFunction {
 @Override
 public int invoke(LuaState luaState) {
 luaState.register(luaState.toString(1), new NamedJavaFunction[]{new FirstFunction()});
 return 1;
 }
}
```

```java 
package YOUR_PACKAGE;
```
This is your package

```java
import com.naef.jnlua.JavaFunction;
import com.naef.jnlua.LuaState;
import com.naef.jnlua.NamedJavaFunction;
```
These are imports

```java
@SuppressWarnings({"WeakerAccess", "unused"})
public class LuaLoader implements JavaFunction {
	@Override
	public int invoke(LuaState luaState) {
		return 1;
	}
}
```
This is the method that is called when importing the plugin, in our case, creating functions.

```java
luaState.register(luaState.toString(1), new NamedJavaFunction[]{new FirstFunction()});
```
Here we register the "FirstFunction" function that we are going to write now.

Creating the FirstFunction class.java in the same folder
```java
package YOUR_PACKAGE;

import com.naef.jnlua.LuaState;

public class FirstFunction implements com.naef.jnlua.NamedJavaFunction {
 @Override
 public String getName() {
 return "firstFunction";
 }

 @Override
 public int invoke(final LuaState luaState) {
 luaState.pushString("Hello world!");
 return 1;
 }
}
```
This method returns "Hello world!" on a call. 

```java
@Override
public String getName() {

}
```
This is the name of our function in Lua.

```java
@Override
public int invoke(final LuaState luaState) {

}
```
This is the method that is called when the function is called.

### Important!
When creating new functions, add them to the LuaLoader.java

### libs folder
Here are the jar files that will be included in the project. Make sure that the jar files contain only .class files!

### jniLibs folder
Here you need to store binary files in C/C++ that have already been compiled before

There should be folders inside:
- arm64-v8a
- armeabi-v7a
- x86
- x86_64

They contain binary files named libNAME.so (Without subfolders)

## How to use?
Download "projectTemplate.zip", move to the desired folder, unzip and try to assemble. OR create a structure yourself:

- main.lua is the launch code for your application
- icon.png is the icon in the Apk (It is not in the Exe)
- build.settings -
- config.lua project settings - Height/width (NOT TO BE CONFUSED WITH WIDTH AND HEIGHT FROM BUILD.SETTINGS), fps, scaling method
- __plugins__ - Discussed in the last paragraph

## Additional details: 
- To build the apk, go to the desired directory and click on the Android icon in the upper right corner. 
- To build the exe, click on the icon next to the android icon.
- Initially, the application version is installed as 1, the package as com.the name (Folder name), and the display name as the folder name. You can copy from github AndroidManifest.xml , add to the main directory of the project and install the tinctures you need there. (The package changes in 3 places, not just at the beginning. Find all the places with SLONOAPP_PACKAGE)

### Paxkage location in manifest
- Line 9
- Line 211
- Line 217

## Examples
### Watch in solar
```lua
local centerX = display.contentCenterX
local centerY = display.contentCenterY
local width = display.actualContentWidth
local height = display.actualContentHeight

local bg = display.newImage("bg.jpeg", centerX, centerY)
bg.width = width
bg.height = height

local timeText = display.newText({
    text = "00:00:00",
    x = centerX,
    y = centerY,
    font = native.systemFont,
    fontSize = 48
})

local function updateTime()
    local currentTime = os.time()
    local hours = os.date("%H", currentTime)
    local minutes = os.date("%M", currentTime)
    local seconds = os.date("%S", currentTime)

    timeText.text = string.format("%02d:%02d:%02d", hours, minutes, seconds)
    -- timeText.text = lib.jopa
end

local function onEnterFrame(event)
    updateTime()
end

Runtime:addEventListener("enterFrame", onEnterFrame)
updateTime()
```