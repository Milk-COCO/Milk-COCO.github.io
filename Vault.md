如何用世界上最标新立异的编程语言写出世界上最标新立异的音游制谱器？？！！  

# Vault谱面逻辑设计

初版  
**vault：Vault Ain't Useless Level Tools！**  
部分语言使用第一人称，请把*我*当做一个正在自我介绍的Actor。

--------

# Actor 演出

构成谱面的基本元素。  
Note是Actor，UI是Actor，***各种各样*** 的图形也是Actor  
对的。图形是图形，判定线是判定线（Reference），这两个被分开了。
中文名：**演出**  

Actor设计为对象，所有Actor的存储，实际上是存的它的唯一编号  
（或者你理解为引用或指针也不是不行）  

-------------------

## 各种各样的Actor
每种Actor的简介。  

Reference：参考系，真正的判定线

Line：纯色矩形

--Ring：环形

Curve：曲线

Texture：显示图像

--Animation：播放动画

Canvas：画布，用来画画



-------------------

## 属性 默认值 事件

Vault中，属性、默认值、事件通常是成对出现的。  
每一个Actor的每一个属性，都有一个默认值，且都可以通过事件来修改属性。  
有一些属性是例外的，它们是不可更改的，也就是说只有默认值。  

属性可以看作实际数值，我们通过事件来设置属性。  

与传统的谱面设计（如：官谱 json、pec、RPE json、pbc等）不同：  
Actor**不所有**事件，而是事件它**作用在**Actor上。  

## 属性多层叠加 事件作用模式

事件支持多层。一层内可拆分为块来方便管理。

每一层的事件执行时，传递给Actor当前值。  
传递完成后，Actor根据得到的值进行计算每一层的属性的叠加，并得到结果值

其中属性叠加的方式，可以通过事件的**作用模式**被控制

部分事件有不同的作用模式，来方便编写一些特效  
目前，可选的只有两种  

`Increase`
增值模式。默认为与上一层的结果作加法。  
`Factor`
倍率模式。默认为与上一层的结果作乘法。  

第一层时，上一层为默认值。  

有部分事件会有特殊处理，也有部分事件不支持作用模式。  
下面会逐个列出每一种事件的处理，如果没写就是可选加法或乘法。  

## 默认值

若没有在执行事件，那么对应的属性就会被设为默认值。  
事件作用在谁的身上，对于被作用者来看，这些都是未知的。这样设计保证了运行效率。  
否则每次更新时，必须要重新遍历所有的事件来找到每一个Actor的每一个属性应该是哪个事件的延续，这效率就很逆天了。  

<>
（计划实现）
如果你不愿意手动写，你也可以开启"自动填充后接事件"选项，编辑器会在空白处自动生成事件。  
于是就适配了事件后续空白处，属性会延续为事件末尾值的设计。  

开启"隐藏"选项将不会显示这些填充了整个空白处的后接事件。  
开启后如果你手动写了这类事件，同样会被自动隐藏。  
<>

## 每个属性

描述格式：  
`属性名 : 值类型 = 创建时自动填充的默认值 <事件昵称>`

--------------------

`Refer : Option<Actor> = None <ReferEvent>`  
不支持作用模式，不支持分层。  

参考的Actor。  
若为Nope，则参考的是视口中心。  
参考实际上并不是将对应Actor作为坐标系原点，而是它隐式创建的Reference。  
Reference也是一种Actor，但是它不渲染，只是作为参考系来被参考用的。  

在Vault中，判定线Actor也**不所有**Note，实际上是Reference所有Note。  
没想好怎么写，所以目前禁止设置Note的参考。

`Reference : Option<Reference> = None <None>`  
存储Actor隐式创建的Reference。  
不主动更新，只有在被参考请求时才会创建、更新。  
在Reference里，这个值是他自己  


我参考的Reference叫做我的参考(系)。  
顾名思义，参考会作为我参考的坐标系原点。  
对哦，Note也可以被参考  

`Sleep : bool = false SleepEvent`  
不支持作用模式，不支持分层。  

休眠时，Actor不渲染，不计算任何属性，不接受任何事件。  
参考和依靠它的Actor会一起休眠，直到你设为不休眠。  
若Note休眠时进入判定，视为全屏判定，不显示判定特效。  

`PositionX : f64 = 0 <PositionXEvent>`  
`PositionY : f64 = 0 <PositionYEvent>`  
`Rotation : f64 = 0 <RotationEvent>`  
相对于参考的坐标和旋转。  
旋转为顺时针的角度。  
注意：与RPE不同，参考会同样继承角度。  
想要不受参考的影响，你可以调整GlobalRotation。

`GlobalPositionX : f64 = 0 <GlobalPositionXEvent>`  
`GlobalPositionY : f64 = 0 <GlobalPositionYEvent>`  
绝对坐标的偏移。  
`GlobalRotation : Option<f64> = None <GlobalRotationEvent>`  
绝对角度。  
如果不为None，角度会设为这个值，忽略相对角度。  
推荐默认设为None，然后在播放时动态改变值。  

---------------------------------

### Reference
中文名：参考系  

`Depend : Option<Actor> = None <ReferenceDependEvent>`  
不支持作用模式，不支持分层。  

我们所有的参考其实都是参考Reference，而不是Actor  
而Reference可以设置依靠于指定Actor，也就是位置和角度会绑定它  

为了方便编写，当我被参考时，我会隐式自动创建默认依靠于自己的Reference。  

对的，依靠也可以改变。但无论怎么变，隐式Reference都只能被所依靠的Actor操作。

那么这些只能作用在Reference的事件和属性也被允许  
作用在我身上相当于作用在我的Reference属性上存储。  
事件作用在Reference上则为控制当前Reference；  

<>
性能问题不是你需要担心的，因为压根就没有性能问题！！请尽情享用！！！  
<>

这些属性并不存储在Actor里面，而是隐式创建的Reference中。

`ReferencePositionX : f64 = 0 <ReferencePositionXEvent>`  
`ReferencePositionY : f64 = 0 <ReferencePositionYEvent>`  
`ReferenceRotation : f64 = 0 <ReferenceRotationEvent>`  
Reference相对于依靠的偏移和旋转  

`ReferenceScaleX : f64 = 0 <ReferenceScaleX>`  
`ReferenceScaleY : f64 = 0 <ReferenceScaleY>`  
坐标系的缩放，也就是说所有参考者的相对坐标会乘以这两个值。  

`ReferenceAlpha : f64 = 1 <ReferenceAlphaEvent>`  
透明度倍率，所有参考者的不透明度通道将叠加这个值。  

`ReferenceColorR : f64 = 1 <ReferenceColorREvent>`  
`ReferenceColorG : f64 = 1 <ReferenceColorGEvent>`  
`ReferenceColorB : f64 = 1 <ReferenceColorBEvent>`  
颜色乘法叠加，所有参考者的颜色通道将叠加这个值  


`bpm_list : Option<Vec<BPM>> = None <None>`  
不支持实时修改。  
每个Reference单独存储bpm列表。  
如果为None，使用全局的bpm列表。  

`Velocity : f64 = 0 <VelocityEvent>`  
`Floor : f64 = 0 <FloorEvent>`  
分层为加法，叠加每层的FP。  
这是Reference独有的事件，Reference所有这些事件，和RPE处理相同。  

前者是Note流动速度，后者是FP增值。  
同一层内二者也是加法叠加。
<br>
<br>
<br>
下面几个Note事件作用与上面相同，但只作用在所有Note上，其它参考者不受这些值影响。  

`NotePositionX : f64 = 0 <NotePositionXEvent>`  
`NotePositionY : f64 = 0 <NotePositionYEvent>`  
`NoteRotation : f64 = 0 <NoteRotationEvent>`  
`NoteScaleX : f64 = 1 <NoteScaleX>`  
`NoteScaleY : f64 = 1 <NoteScaleY>`  
`NoteAlpha : f32 = 1.0 <NoteAlphaEvent>`  
`NoteColorR : f32 = 1.0 <NoteColorREvent>`  
`NoteColorG : f32 = 1.0 <NoteColorGEvent>`  
`NoteColorB : f32 = 1.0 <NoteColorBEvent>`  

`NoteSizeX : f64 = 1 <NoteSizeXEvent>`  
`NoteSizeY : f64 = 1 <NoteSizeYEvent>`  
Note水平、垂直大小  

`NoteVisible : f64 = 0 <NoteColorBEvent>`  
Note可视时间  

`NoteJudgable : bool = true <NoteJudgableEvent>`  
`NoteAnimatable : bool = true <NoteJudgableEvent>`  
IsFake拆分为Judgable和Animatable。  
一个是否判定，一个是否显示打击特效。  

`NoteJudgeSizeX : f64 = 150 <NoteJudgeSizeXEvent>`  
`NoteJudgeSizeY : f64 = 0 <NoteJudgeSizeYEvent>`  
Note判定水平和竖直区域半径，0表示忽略水平或垂直。  

`NoteControl : Option<Control> = None <NoteControlEvent>`  
禁止分层，适配RPE的Control，但是有作用域  

单独储存Control内容，事件中只储存对象引用。  
Control永远是最后一个，换句话说，Control结果。  

---------------------------------

### Line
中文名：线  
虽说叫做Line，实际上是显示的一个不可修改的贴图line.png。  

`SizeX : f64 = 0.0075 <SizeXEvent>`  
`SizeY : f64 = 0.0 <SizeYEvent>`  
宽高度，0为无限长度  
第一层若为Factor，图片原始宽高作为"第0层"参与计算

`ColorR : f32 = 1.0 <ColorREvent>`  
`ColorG : f32 = 1.0 <ColorGEvent>`  
`ColorB : f32 = 1.0 <ColorBEvent>`  
颜色。
分层为l1*l2/255

--------------------------

### Texture

`SizeX : f64 = 0.0 <SizeXEvent>`  
`SizeX : f64 = 0.0 <SizeYEvent>`  
控制贴图的宽度高度
第一层若为Factor，图片原始宽高作为"第0层"参与计算

`OffsetX : f64 = 0.0 <OffsetXEvent>`  
`OffsetY : f64 = 0.0 <OffsetYEvent>`  
控制贴图的偏移
第一层若为Factor，图片原始宽高/2作为"第0层"参与计算

`ColorR : f32 = 1.0 <ColorREvent>`  
`ColorG : f32 = 1.0 <ColorGEvent>`  
`ColorB : f32 = 1.0 <ColorBEvent>`  
控制Texture的每一个像素各个通道的倍率。
分层为l1*l2/255
    
-------------------------
    
### Canvas
中文名：画布  
用来画画用的  
画布事件独自所有这些事件。

`PainterSize : f32 = 1.0 <PainterSizeEvent>`  
画笔大小  
`PainterPositionX : f32 = 1.0 <PainterPositionXEvent>`  
`PainterPositionY : f32 = 1.0 <PainterPositionYEvent>`  
用于生成轨迹。  
坐标移动为画布的整体偏移  

-------------------------

### Text
中文名：文本  
`Text : &str = "" <TextEvent>`  
`TextSize : u32 = 16 <TextEvent>`  

-------------------------

### Curve  
中文名：曲线  

`CurveFunctionX : &str = "x" <CurveFunctionXEvent>`  
`CurveFunctionY : &str = "0" <CurveFunctionYEvent>`  
储存xy坐标曲线方程。  
其中x、y是自变量，t是相对时间，也就是事件执行的进度。  
可以通过easing(缓动编号, 值)来调用缓动函数

`CurveArgument : Option<Vec<f64>> = None <CurveArgumentEvent>`  
默认用来修改修改曲线中的参数。  
