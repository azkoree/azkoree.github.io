---
title: RMXP脚本教程1 - 图片标题
tags:
  - rmxp
  - 游戏开发
categories:
  - - 游戏开发
    - rmxp脚本教程
abbrlink: 7552dc29
date: 2025-12-05 18:24:15
---

# 前言
这是我看[RyanBern的rmxp脚本教程](https://www.bilibili.com/video/BV1Es411S7my/?p=4&share_source=copy_web&vd_source=7456c30741184bc32056a597f75c8c84)做的笔记，发在这只是用来督促自己。因为我自己是零基础，所以有些用词不够专业，记的也有些乱七八糟，请各位高抬贵手
如果要学的话，建议还是自己去看大大的视频教程

[toc]

# 显示标题图片

## 生成和释放图片

scene_title类处理标题画面
在“生成标题图形”处生成标题的文字图片，使用显示图片的命令
25行后：
```ruby
# 显示标题文字图片
@sprite1 = Sprite.new #新建一个Sprite
@sprite1.bitmap = RPG::Cache.title("Title-Words.png") #载入这张图片
@sprite2 = Sprite.new
@sprite2.bitmap = RPG::Cache.title("Title-Subwords.png")
```
sprite是用于显示图片的类

在使用图片过后，需要进行释放
90行后：
```ruby
@sprite1.bitmap.dispose # 释放位图
@sprite1.dispose #释放精灵本身
@sprite2.bitmap.dispose
@sprite2.dispose
```
注意顺序是先释放位图再释放精灵

## 调整图片位置

使用sprite类的属性

> x 
精灵的 X 座标。
y 
精灵的 Y 座标。
z 
精灵的 Z 座标。该值大的东西显示在上面。Z 座标相同的话，则后生成的对象显示在上面。

```ruby
# 显示标题文字图片
@sprite1 = Sprite.new # 新建一个Sprite
@sprite1.bitmap = RPG::Cache.title("Title-Words.png") # 载入这张图片
@sprite1.x = 112
@sprite1.y = 32 #设定图片的位置
@sprite2 = Sprite.new
@sprite2.bitmap = RPG::Cache.title("Title-Subwords.png")
@sprite2.x = 128
@sprite2.y = 96
```

## 动画效果

制作两个标题从左右分别滑入，从完全透明到不透明，在屏幕中间重合为一个的效果
首先添加另一个标题图片的精灵

```ruby
@sprite3 = Sprite.new
@sprite3.bitmap = @sprite1.bitmap #图片和sprite1相同，因此直接引用sprite1的图片
@sprite3.x = 240
@sprite3.y = 32
@sprite4 = sprite.new
@@sprite4.bitmap = @sprite2.bitmap
@sprite4.x = 256
@sprite4.y = 96
```
进行释放

```ruby
@sprite3.dispose
@sprite4.dispose #sprite1的图片已经释放，再次释放会出错
```

要实现画面的动态效果，需要进行过渡，来解除画面的冻结
82行后：

```ruby
# 执行过渡
Graphic.transition
# 标题过渡
title_fade_in 
```
在位图新建位置设置初始不透明度

```ruby
@sprite1.opacity = 0
@sprite2.opacity = 0
@sprite3.opacity = 0
@sprite4.opacity = 0 # 设置初始透明度为0
```


然后对title_fade_in方法进行定义

过渡有16帧，因此标题过渡的过程要循环执行16次
两个图片之间相差128，执行16次，每次移动4个像素，透明度提高16

```ruby
def title_fade_in
    16.times do # 16次循环
        @sprite1.x += 4
        @sprite2.x += 4
        @sprite3.x -= 4
        @sprite4.x -= 4
        @sprite1.opacity += 16
        @sprite2.opacity += 16
        @sprite3.opacity += 16
        @sprite4.opacity += 16
        Graphic.update # 屏幕刷新
    end
```

# 图片选项

## 载入图片
用数组的方式来载入图片，避免啰嗦

```ruby
@sprite_commands = [] # 建立数组
(0..2).each do |i| # 迭代，依次传入参数i
    sprite = Sprite.new
    sprite.bitmap = RPG::Cache.title("menu-#{i}-off.png") #内嵌表达式
    sprite.x = 420
    sprite.y = 320 + 32 * i
    @sprite_commands << sprite # 生成的sprite依次加入到建立的数组中
end
```

进行释放

```ruby
# 释放图片命令
@sprite_commands.each do |sprite|
    sprite.bitmap.dispose
    sprite.dispose
end
```

## 图片选项的处理

删除涉及到command_window的部分，包括命令窗口的生成，对“继续”是否为灰色的判断，释放命令窗口，刷新命令窗口，光标分支
在生成图片命令后，新建一个变量，用于表示光标位置

```ruby
@Index = 0 #表示光标的第一个位置
```

同时将139行对光标位置的判断变量改为index

```ruby
    case @index
```

在光标位置改变时，图片选项也要变化
定义一个新的方法

```ruby
def Index=(index) # 括号内的index为参数，括号外的为方法名
    if index > 2
        index = 0
    if index < 0
        index = 2
    end
    sprite = @sprite_commands[@index] # 简单来说，@index是光标位置改变之前，index是改变之后，这里读取改变之前的精灵
    sprite.bitmap.dispose # 释放原来的图片
    sprite.bitmap = RPG::Cache.title("menu-#{@index}-off.png") # 光标位置改变后，原先的选项图片会变成关闭的状态
     sprite_new = @sprite_commands[index] # 建立一个光标改变后的sprite
    sprite.bitmap.dispose # 释放原来的图片
    sprite_new.bitmap = RPG::Cache.title("menu-#{@index}-on.png") # 光标位置改变后，对应的选项图片变为打开的状态
    @index = index # @index变为了新的，用于下一次光标位置的改变
end
```
定义方法后，在生成图片选项的位置进行调用

```ruby
@sprite_commands = [] # 建立数组
(0..2).each do |i| # 迭代，依次传入参数i
    sprite = Sprite.new
    sprite.bitmap = RPG::Cache.title("menu-#{i}-off.png") #内嵌表达式
    sprite.x = 420
    sprite.y = 320 + 32 * i
    @sprite_commands << sprite # 生成的sprite依次加入到建立的数组中
end
@index = 0 #初始化实例变量
self.index = 0 # 调用index方法，参数为0，因为在类的内部调用方法，因此对象为self
```

游戏测试，光标无法移动

## 图片选项移动的效果

在update下调用一个用于图片选项刷新的方法，然后进行定义

```ruby
def update
    update_command
```

```ruby
def update_command
    if Input trigger?(Input::DOWN) #判断是否按下了下方向键
        $game_system.se_play($data_system.cursor_se)
        self.index = @Index +=1
        return #防止处理出错
    end
    if Input.trigger?(Input::UP)
        self.index = @index -1
        return
    end
end
```

## 判定继续的有效性

在有存档的情况下，“继续”选项可以选择，反之则为灰色的无效状态

```ruby
# 无效的情况下，继续的颜色显示为灰色
if @continue_enabled
    self.index = 1
else
    @sprite_command[1].bitmap.dispose
    @sprite.command[1].bitmap = RPG::Cache.title("menu-5-off.png") #如果继续无效，就释放正常情况下的图片，改为无效状态的图片
end
```

对@index部分也要改

```ruby
def Index=(index) 
    if index > 2
        index = 0
    if index < 0
        index = 2
    end
    sprite = @sprite_commands[@index] 
    sprite.bitmap.dispose 
    filename = !@continue_enabled && @index == 1 ? "menu-5-off.png" : "menu-#{@index}-off.png" #逻辑判断，若？前的表达式为真，则计算：前的表达式
    sprite.bitmap = RPG::Cache.title(filename) 
     sprite_new = @sprite_commands[index] 
    sprite_new.bitmap.dispose 
    filename_new = !@continue_enabled && index == 1 ? "menu-5-off.png" : "menu-#{index}-off.png"#同上
    sprite_new.bitmap = RPG::Cache.title("filename.new") 
    @index = index 
end
```

