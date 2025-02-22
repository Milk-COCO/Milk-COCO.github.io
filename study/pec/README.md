# Re:PhiEdit 谱面格式详解

参考版本：1.6.0 正式版  
以下简称RPE或PhiEdit，注意PE是另外一个东西：PhiEditer

-----------------

## .pez 文件

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
| extra.json | 适配Phira的[拓展特性][PhiraExtra] |
| **[glsl][glsl]** 文件们 | 适配Phira的[着色器][PhiraOwnShader]们 |


其中 **[谱面标识]** 是在rpe中为谱面生成的唯一编号。  

除了info.txt之外，所有的文件名都可以被修改，但需要在info.txt内修改对应元素，详见下一章节。  

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