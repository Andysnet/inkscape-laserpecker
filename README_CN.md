# 激光啄木鸟Inkscape插件
这是一个为激光啄木鸟生成Gcode的Inkscape插件。在App内置Gcode切割不够理想的情况下，提供了手动测试和设置激光功率和速度的机会。

## 激光啄木鸟
激光啄木鸟是便宜好用的小型家用激光雕刻机器。具体信息请访问[官方网站](http://www.laserpecker.cn/)（[英文官网](https://www.laserpecker.net/)） 。



## 兼容性

此插件暂时针对的是激光啄木鸟L1和Pro型号（一代）。未来会有新型号的激光啄木鸟发布。如果我有机会拿到新型号测试的话，我会尽快更新代码提供支持。


## 安装方法

1) 安装Inkscape 0.92版。暂时还不支持1.0版。
2) 复制`extension`文件夹中的**laserpecker.inx**和**laserpecker.py**文件到...
	* Linux和Mac系统: `~/.config/inkscape/extensions/`
	* Windows系统: `C:\Program Files\Inkscape\share\extensions\`
3) 运行Inkscape，插件应该在"扩展"菜单下的"LaserPecker" > "LaserPecker Gcode Generator" 

## 设置和限制

* 激光头空走速度固定设置为3000mm/分，因为这是官方示例Gcode文件里用的值.
* 最低激光速度限制在70mm/分，因为这是机器允许的最慢速度。 （我测试过从0.01到70mm/分之间的各种速度设置，到了激光啄木鸟输出的时候没有区别，都等同设成70mm/分。）
* 激光功率可以设成1（最小）到255（最大）之间。（0=关闭，这样设置的话Gcode等于没用，所以禁止设成0。）
* 雕刻区域限制在100mm x 100mm。
* 如果图形超过100mm x 100mm，插件会提示。
* 雕刻原点 (0,0) 在100mm x 100mm雕刻区域的中心。也就是说，有效的绝对雕刻坐标实际上是(-50,-50)到(50,50)这么个方形范围之内。虽然通过测试，我发现激光啄木鸟一代(不是Pro)的实际雕刻范围在(-100,-70)到(100,70)这么大的范围，厂商还是限制了100x100这个保守的区域来减小形变，保证雕刻质量。演示效果请参考`misc`文件夹中的`engraving_over_200mm_x_140mm.mp4`和`engraving_over_200mm_x_140mm.jpg`。
* 你在Inkscape的画布上可以随意放置图形。此插件在输出Gcode坐标的时候会自动修正到整体图形以原点为中心。  
* 输出的Gcode文件是纯文本文件。一般来说扩展名不重要。但要传给安卓app的话，需要用txt扩展名才能被app识别。
* 输出的Gcode文件体积不能超过1MB，否则机器内存不够。

## 位图转Gcode操作流程

[视频演示点此处](tutorial/image_to_gcode.mp4)

1) 运行Inkscape
2) 导入图像
3) 选中图像，点"Path"菜单，然后选"Trace Bitmap"
4) 推荐用以下设置（没有中文版，直接翻译了选项名称）:
	* 颜色(Colours): 2
	* 扫描次数(Scans): 2
	* 平滑(Smooth）: 选中
	* 层叠扫描图层（Stack scans）: 选中
	* 删除背景（Remove background）: 选中
	* 实时预览（Live Preview）: 选中
	* 其他选项自己随便测试效果
5) 点"OK"按钮**一次**就好。然后关掉对话框。
6) 生成的矢量图现在重叠在你的位图上面。你可以把矢量图移开一点，选中位图然后把它删掉，免得碍事。
7) 矢量图形放画布的什么位置不重要，只要尺寸在100mm x 100mm以内就行。工具栏有显示宽高尺寸。利用这个缩放到你需要的尺寸。
8) 选中你要雕刻的图形，从"Path"菜单中选"Object to Path"转换成路径。
9) "扩展"菜单 > "LaserPecker" > "LaserPecker Gcode Generator" 
10) 填入功率，速度，文件夹和文件名，点Apply生成Gcode文件。图形复杂的话可能会花几秒钟。之后会有重叠雕刻路径预览。不用管它。选中删掉也行。这个跟Gcode文件没关系了。


## 额外技巧：检测生成的Gcode

1) 打开Gcode文件
2) 复制所有内容
3) 访问[NC Viewer](https://ncviewer.com/)
4) 清除左边已经有的示范Gcode，把你的Gcode贴进去
5) 点下面的"Plot"按钮就可以看到图形化的Gcode路径了。


## 例图

我在`sample_images`文件夹中存了一些适合切割的图片。我没有直接提供Gcode文件的原因，是想留给你在生成Gcode文件时可以选择最适合你的切割材料的设置的机会。


## 实用帮助文件

在`gcode`文件夹中有两个Gcode文件，可以帮助你快速测试和找到最合适你的材料的雕刻或者切割的设置组合（功率+速度）。

* `engraving_test_p160-255,s500-800.txt`: 用不同的功率和速度扫灰度来测试雕刻效果。
* `cutting_test_p200-255,s70-400.txt`: 这个是给你测试切割设置组合的。如果连最高有效设置(功率255,速度70)都无法一次切割你的材料的话，就只能考虑切多次了。
* `sample_result.jpg`: 我自己用回收纸的测试结果。

在`misc`文件夹中有若干`markers`定位标记文件，每套定位标记文件包含以下格式：
* `.svg`: 定位标记SVG矢量文件，建议用Inkscape打开编辑。
* `.txt`: 定位标记Gcode文件，速度500,功率200。
* `.png`: 定位标记PNG格式位图。注意这只是为了方便预览定位标记图样。雕刻位图要远远慢于雕刻Gcode。


## 声明

这个插件是在[J Tech Laser Tool Plugin V 2_2 for inkscape 0.92](https://jtechphotonics.com/?page_id=1980)的基础上为激光啄木鸟改进和优化的。


## 使用许可

This program is free software; you can redistribute it and/or modify
it under the terms of the GNU General Public License as published by
the Free Software Foundation; either version 2 of the License, or
(at your option) any later version.

This program is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
GNU General Public License for more details.

You should have received a copy of the GNU General Public License
along with this program; if not, write to the Free Software
Foundation, Inc., 59 Temple Place, Suite 330, Boston, MA  02111-1307  USA
