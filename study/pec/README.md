
# Re:PhiEdit 谱面格式详解

参考版本：1.6.0 正式版  
以下简称RPE或PhiEdit，注意PE是另外一个东西：PhiEditer

-----------------

# .pez 打包的谱面文件

[glsl]: https://www.khronos.org/opengl/wiki/Core_Language_(GLSL) "OpenGL Wiki"

[PhiraOwnShader]: https://teamflos.github.io/phira-docs/chart-standard/extra/effect/custom-shader.html "Phira文档-谱面标准-拓展特性-特效-自行编写着色器"
[PhiraExtra]: https://teamflos.github.io/phira-docs/chart-standard/extra/index.html "Phira文档-谱面标准-拓展特性"

**.pez** 全称 ***PhiEdit Zipped file***，
对哦，它本质上是一个 **.zip** 文件  
通过 **rpe** 导出的 **.pez** 文件包含以下文件：  

以\*开头的表示始终存在，  
其中不以*开头的，表示不使用对应功能时，文件不存在。

| 默认的文件名 | 描述 |
| --- | ----------- |
| *Info.txt | 谱面信息 |
| ***[谱面标识]** .json | 谱面本体 |
| ***[谱面标识]** .mp3 (或ogg wav flac) | 音频文件 |
| ***[谱面标识]** .png (或jpg jpeg) | 曲绘文件 |
| 其它图片文件们 | 判定线贴图们 |
| 其它音频文件们 | Note打击音效们 |
| extra.json | 适配Phira Shader的[拓展特性][PhiraExtra] |
| **[glsl][glsl]** 文件们 | 适配Phira的[着色器][PhiraOwnShader]们 |


其中 **[谱面标识]** 是在rpe中为谱面生成的唯一编号。  

除了info.txt之外，所有的文件名都可以被修改，但需要在info.txt内修改对应元素，详见下一章节。

<br>
<br>

-------------------

# Info.txt 谱面信息内容


info.txt是一个序列化的数据格式，用于存储谱面信息，供RPE自己与模拟器们快速读取  
下面是一个info.txt的实例  

```info.txt
#
Name: Zero Talking
Path: 8117786007850681
Song: 8117786007850681.ogg
Picture: 8117786007850681.png
Chart: 8117786007850681.json
Level: IN  Lv.14
Composer: はるまきごはん feat.初音ミク
Charter: MilkCOCO
LastEditTime: 2025_2_12_6_22_47_
Length: 162.500
EditTime: 168712.868
Group: Miku

```
Tips：结尾还有一个空行  

首先，一个info由一个 **#** 符号开头，之后的每一行都储存一个数据。

<h2 id="info">info</h2>

| 数据名 | 描述 |
| --- | ----------- |
| Name | 歌曲昵称 |
| Path | 谱面文件夹，即谱面内容位于的目录，为RPE中Resources文件夹下的相对路径 |
| Song | 歌曲文件路径，为 谱面文件夹 下的相对路径 |
| Picture | 曲绘文件路径，为 谱面文件夹 下的相对路径 |
| Chart | 谱面文件路径，为 谱面文件夹 下的相对路径 |
| Level | 谱面等级标识 |
| Composer | 作曲家名义 |
| Charter | 谱师名义 |
| LastEditTime | 上次编辑时间，格式为``年_月_日_时_分_秒_`` |
| Length | 谱面长度，播放到该时间就会结束播放，在RPE中会回到0时刻 |
| EditTime | 总编辑时长，单位秒 |
| Group | 谱面分组，在RPE选谱页面可分组谱面 |

若数据重复，最终会呈现最后一个。  

比如：

```info.txt
#
Name: Zero Talking
... 省略其它数据 ...
Name: Many Talkings

```

最后实际的昵称读取为Many Talkings（好耶

<br>
<br>

-------------------

# chart.json 谱面文件格式 

RPE的谱面文件一个是格式化了的 [JSON 文件](https://www.runoob.com/json/json-tutorial.html "菜鸟教程 json教程")，储存实际谱面内容  

我会基于下面的定义来描述谱面  
其中，带-号前缀的属性表示在播放时无用

## 缓动类型

[easing]: /study/pec/Easing.md
[点我跳转][easing]

## 特别定义

我加入了属性的类型，但感觉有些时候太复杂，有些时候太简单了，会让人看不明白。
于是我把那些内容按照下面的规则，优化了一些  
<br>

### 数组的表示
JSON中，由方括号`[ ]`括起来一系列属性的东西叫`数组`  
<br>

#### List\<T\>

List\<T\>表示一个列表，T为任意类型，可以是int string Beat等  
对于这种不定长，但类型可以确定的数组，我使用List<T>来描述

例：

```JSON
"BPMList" : [
    {
        "bpm" : 133.0,
        "startTime" : [ 0, 0, 1 ]
    }
]
```

这是一个List\<BPM\>  
<br>

#### T[n]

T[n]表示一个定长列表，T为任意类型，n为列表长度  
对于定长，且类型可以确定的数组，我使用这玩意来描述

例：

```JSON
"bezierPoints" : [ 0.0, 0.0, 0.0, 0.0 ]
```

这是一个double[4]  
<br>

#### Beat

RPE中，描述谱面中的一个时间的单位不是秒，更不是毫秒，而是拍  
在谱面中，拍存储为一个3个`int`组成的数组  
我用`Beat`类型来描述这类表示拍事件的数组，而不是`int[3]`。

例：

```JSON
"startTime" : [ 0, 0, 1 ]
```
`Beat` startTime

--------------------------------

### 对象的表示

JSON中，由大括号`{ }`括起来的，储存一系列键值对的东西叫做`对象`  
在描述时，在行头有 点缩进  
例如：

`JObject` *META
* `int` RPEVersion  
* ... 省略其它元素 ...
<br>

#### Dictionary\<Key,Value\>

对于Key的基本类型可以统一的对象，我使用`Dictionary<Key,Value>`来描述  

`Dictionary<Key,Value>`表示一个字典，或者说，储存键值对的列表  
Key和Value为任意类型  

例：

```JSON
"extended" : {
    "paintEvents" : [ ],  //... 省略事件们，下同 ...
    "colorEvents" : [ ],
    "gifEvents" : [ ],
    "inclineEvents" : [ ],
    "scaleXEvents" : [ ],
    "scaleYEvents" : [ ],
    "textEvents" : [ ]
}
```

`Dictionary<ExtendedEvents,EventList>` extended  
<br>

#### JObject

其中值类型不确定的对象，我会使用`JObject`类型描述

例：

```JSON
"META" : {
    "RPEVersion" : 160,
    "background" : "8117786007850681.png",
    "charter" : "MilkCOCO",
    "composer" : "はるまきごはん feat.初音ミク",
    "duration" : 162.5,
    "id" : "8117786007850681",
    "illustration" : "はるまきごはん",
    "level" : "IN  Lv.14",
    "name" : "Zero Talking",
    "offset" : -77,
    "song" : "8117786007850681.ogg"
}
```
`JObject` META

-----------------------------------

### 数组元素的特色命名

首先，表示部分数组中的元素，会使用大驼峰给它额外命名。  
我会给它的名字前面标出*号的。比如：    
`List<Line>` judgeLineList  
`JObject` *Line

--------------------------------------

### 属性等于类型

换句话说，就是说属性的名字可以直接表示的这个属性的类型。  

比如：  
`List<Dictionary<NormalEvents,List<Event>>>` eventLayers    

简化后：  
`List<EventLayer>` eventLayers  
`Dictionary<NormalEvents,EventList>` *EventLayer  
`List<Event>` *EventList：储存一种事件的列表


-------------------------------------

## multiLineString 多线编辑选中线
`double` -multiLineString ：在播放时无用，仅仅用来记忆多线编辑相关参数

## multiScale 多线编辑横向缩放
`double` -multiScale ：在播放时无用，仅仅用来记忆多线编辑相关参数

## chartTime 总计谱面编辑时长
`double` -multichartTimeScale ：在播放时无用，仅仅用来记忆相关编辑器参数

## judgeLineGroup 判定线组的昵称列表
`List<string>` -judgeLineGroup ：由string组成的数组，在播放时无用，仅仅用来记忆相关编辑器参数



## BPMList BPM列表

`List<BPM>` BPMList ：存储全局BPM列表  

### BPM
`JObject` BPM ：BPM实例  

* `Beat` startTime ：开始使用该BPM的时间。  
* `double` bpm ：BPM（每分钟拍数）。  



## META 根数据

`JObject` *META ：储存数据

* `int` RPEVersion ：从什么版本的RPE保存的谱面  
* `double` duration ：谱面长度  
* `int` offset ：谱面相对歌曲的时间偏移，单位毫秒(ms)
<br>


* `string` name ：歌曲昵称  
* `string` -id ：谱面标识，在rpe中为谱面生成的唯一编号  
* `string` song ：曲绘文件路径，为 谱面文件夹 下的相对路径  
* `string` background ：曲绘文件路径，为 谱面文件夹 下的相对路径
<br>


* `string` level ：谱面等级标识  
* `string` composer ：作曲家名义  
* `string` charter ：谱师名义  
* `string` illustration ：曲绘画师名义


## Line 判定线
`List<Line>` judgeLineList ：储存判定线们  
`JObject` *Line ：判定线

* `string` Texture ：判定线的贴图。只需要填入文件名就可以了，RPE会自己找到图片文件。  
  默认line.png，判定线显示一条4000*5的线。如果不为默认，导出pez时会自动一起打包判定线贴图。  
* `double[2]` anchor  ：描述原点位于贴图的什么位置。原点位于贴图左上角为`[0.0,0.0]`，右下角为`[1.0,1.0]`
<br>


* `double` bpmfactor ：判定线BPM倍率，运用在这条线上的BPM将会乘以这个值
* `int` father ：父线，负数表示没有父线。有则该线的参考系为父线的锚点，但不继承角度。
* `int` zOrder ：渲染顺序，该值更低的判定线先被渲染。也就是说该值越高，"图层"越高
* `int` isCover ：默认为1。当为1时，在判定线下面的`Note`不渲染。
* `bool` isGif ：当Texture是一个gif时，该值为`true`。此时`gifEvents`可用。见下面内容。
<br>


* `int` -Group ：记录该判定线属于judgeLineGroup中的哪一组。在播放时无用。
* `string` -Name ：判定线的昵称。
* `int` -numOfNotes ：该线上Note的数量。无任何用途。


* `List<NormalEventLayer>` eventLayers ：储存事件层们
### NormalEventLayer 普通事件层
* `Dictionary<NormalEvents,NormalEventList>` *NormalEventLayer：事件层，储存一层中各种事件的列表

* `List<NormalEvent>` *NormalEventList：事件条，储存一种事件
#### NormalEvent 普通事件

```JSON
{
    "bezier" : 0,
    "bezierPoints" : [ 0.0, 0.0, 0.0, 0.0 ],
    "easingLeft" : 0.0,
    "easingRight" : 1.0,
    "easingType" : 1,
    "end" : 0,
    "endTime" : [ 1, 0, 1 ],
    "linkgroup" : 0,
    "start" : 0,
    "startTime" : [ 0, 0, 1 ]
}
```

* `JObject` *Event：事件，储存单个事件  
  * `int` -linkgroup ：绑定组，相同绑定组的事件，在RPE中start和end属性相同
  * `double` start ：起始值
  * `double` end ：结束值
  * `Beat` startTime ：起始时间
  * `Beat` endTime ：结束值
  如果在 速度事件 中，下面这些属性不存在
  * `int` easingType ：缓动类型，详见[缓动对照表][easing]  
    缓动左下角为[0,0]，右上角为[1,1]
  * `double` easingLeft ：缓动切割左  
  * `double` easingRight ：缓动切割右  
    若原缓动函数表示为f(x)，实际缓动由 原函数的  
    [easingLeft,f(easingLeft)]为左下角[0,0]  
    [easingRight,f(easingRight)]为右上角[1,1] 组成  
  * `int` bezier ：若为1，自定义贝塞尔曲线 bezierPoints 可用  
  * `double[4]` bezierPoints ：自定义贝塞尔曲线的两个控制点的相对坐标，x1 y1 y2 y2
  





<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>