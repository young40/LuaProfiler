###LuaProfiler使用说明

LuaProfiler原自keplerproject(http://www.keplerproject.org/luaprofiler)，原项目自2007.08之后停止更新了，所以我们把LuaProfiler源代码拿过来进行了一番修改，这里感谢作者。

目前LuaProfiler已经可以支持Lua 5.3版本，对于Lua脚本的分析结果会生成一个JSON文件，然后通过解析JSON文件得到分析数据。

####使用方法
1. 将LuaProfiler源代码集成到你的项目中的Lua源代码中，最终编译成dll/lib/so。
2. 在调用```luaL_newstate()```之后，调用LuaProfiler暴露出来的接口：```profiler_open(L)```。
3. Lua脚本中，在要开始分析的地方调用```profiler_start(jsonFile)```，结束分析的地方调用```profiler_stop()```。

####函数说明

int profiler\_open(lua\_State *L)  
Type: C  
Note: LuaProfiler初始化  
Param1: L 指针类型，lua\_State指针  
Return: 1,成功  

function profiler\_start(jsonFilePath)  
Type: Lua  
Note: 开始分析  
Param1: jsonFilePath 字符串，生成json文件的绝对路径  
Return: bool, 是否成功  

function profiler\_stop()  
Type: Lua  
Note: 结束分析，生成结果JSON文件  
Param1: 无  
Return: bool, 是否成功   

####分析结果  
分析出来的结果是一个JSON数组，具体结果如下：  

    [
      {
        sub:
        [
          {}, 
          {}, 
          {
            sub:[{}, {}]
          }
        ]
      },
    ]

真实的数据如下：
  
    [
      {
        "ln": 9,
        "cs": 0.675849,
        "lv": 1,
        "info": "Lua",
        "mod": "Prefab.lua",
        "fn": "CloneGo",
        "sub": [
           {
            "ln": 9,
            "cs": 0.666518,
            "lv": 2,
            "info": "C",
            "mod": "=[C]",
            "fn": "cs_func",
            "sub": []
           }
        ]
      }
    ]

**ln:** 函数所在文件的行号，如果为-1则表示无法跟踪。  
**cs:** 函数耗时，单位毫秒（ms）。  
**lv:** 在Lua栈的位置。  
**info:** 调用类型，Lua或C。  
**mod:** 模块，如果info是Lua的话，mod则是具体的Lua脚本文件。  
**sub:** 子调用。  

**说明：**  
最终生成的JSON文件阅读性并不好，所以建议大家写一个工具解析成可读性比较好的格式。由于我是在Web上显示，所以我用了TableTree4J这个JS库去解析JSON文件