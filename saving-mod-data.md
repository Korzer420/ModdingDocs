---
nav_order: 9
---
# Saving Mod Data

## Introduction 

Sometimes a mod needs to save some data, to be able to adjust settings according to the user setup or just for handling various things (like picked up items, counters etc.). We can create global data which work game wide as well as local data that is being used for the different save files. 

## Creating global settings

Global settings get loaded once you started the game and can determine the different states of toggle buttons in your menu for example. For using this, you need to implement the Interface IGlobalSettings<MyGlobalSaveData> to your Mod class:
```cs
public class MyMod : Mod, IGlobalSettings<MyGlobalSaveData>
{
   private int _mySetting1 = 43;
  
   private bool _mySetting2 = true;
  
   private string _mySetting3 = "SampleText";
}
```
As well as creating your own class which holds the save data:
```cs
// Example save data class
public class MyGlobalSaveData
{
  public int Setting1 {get; set;}
  
  public bool Setting2 {get; set;}
  
  public string Setting3 {get;set;}
}
```
Implementing the interface will force you to write the Methods OnLoadGlobal and OnSaveGlobal (For syntax convinience you can just press ctrl and . on the interface name and then choose implement interface which will automaticlly add the methods, if you have intelliSense.)
  
Use OnSaveGlobal to store your mods values in the save data class:

```cs
// This is in the MyMod class
public MyGlobalSaveData OnSaveGlobal()
{
    MyGlobalSaveData mySaveData = new MyGlobalSaveData();
    mySaveData.Setting1 = _mySetting1; // Stores the 43 from above
    mySaveData.Setting2 = _mySetting2; // Stores the true from above
    mySaveData.Setting3 = _mySetting3; // Stores the "Sample Text" from above
    return mySaveData;
}
```
When this function is called (when the game is closed probably) it will create a MyMod.GlobalSettings.Json file in the %AppData% directory of the game. This will look like this:
```
{
  "Setting1": 43,
  "Setting2": true,
  "Setting3": "Sample Text"
}
```
To load in the data the OnLoadGlobal will be called upon starting the game. It could look like this: 
```cs
// This is in the MyMod class
public void OnLoadGlobal(MyGlobalSaveData s)
{
    // The parameter object "s" contains the data the game fetched from the MyMod.GlobalSettings.Json file.
    _mySetting1 = s.Setting1;
    _mySetting2 = s.Setting2;
    _mySetting3 = s.Setting3;
}  
```

## Creating local settings

Local settings are used for save file dependend data (like if you already took certain items in the save file or whatever). It can be implemented almost the same way as the global settings.
  
Implement the ILocalSettings<MyLocalSaveData> interface to the class:
```cs
public class MyMod : Mod, ILocalSettings<MyLocalSaveData>
{
   private List<int> _mySetting1 = new List<int>(){ 43, 55};
  
   private Dictionary<int,bool> _mySetting2 = new Dictionary<int, bool>();
  
   private string _mySetting3 = "SampleText";
}
```

Create a class that contains the data that you need to save:
```cs
// Example save data class
public class MyLocalSaveData
{
  public List<int> Setting1 {get; set;}
  
  public Dictionary<int,bool> Setting2 {get; set;}
  
  public string Setting3 {get;set;}
}
```
  
Implement both methods OnLocalSave() and OnLocalLoad() to your mod class. (For syntax convinience you can just press ctrl and . on the interface name and then choose implement interface which will automaticlly add the methods, if you have intelliSense.)
  
Use OnLocalSave to save for the save file relevant data to the save data class object:
```cs
public MyLocalSaveData OnSaveLocal()
{
    MyLocalSaveData mySaveData = new MyLocalSaveData();
    mySaveData.Setting1 = _mySetting1; // Stores the list
    mySaveData.Setting2 = _mySetting2; // Stores the dictionary
    mySaveData.Setting3 = _mySetting3; // Stores the "Sample Text" from above
    return mySaveData;
}
```
This adds a section in the user<file slot number>.modded.json file for your mod.
To load in the data the OnLoadGlobal will be called upon starting the game. It could look like this: 
```cs
// This is in the MyMod class
public void OnLoadLocal(MyLocalSaveData s)
{
    // You can also use complex objects as save data like lists, dictionaries or own classes.
    _mySetting1 = s.Setting1;
    _mySetting2 = s.Setting2;
    _mySetting3 = s.Setting3;
}  
```  
