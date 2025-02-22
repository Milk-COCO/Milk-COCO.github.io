
## Info.txt 谱面信息内容


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
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>