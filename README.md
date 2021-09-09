## 原神钓鱼辅助工具

> * 您只需抛出鱼竿，然后便可等待钓鱼机自动完成钓鱼过程
 
![](images/demo.png)

⭐欢迎大家在 Issues 中分享自己的配置文件⭐

⭐也祝各位早日钓到精五鱼叉⭐

### 使用教程：

#### 请首先检查您的游戏设置中是否能选择 **1600x900** 这一尺寸的窗口

* 有

1. 进入游戏设置，将画面大小改为 1600x900，此时游戏窗口应当没有边框

2. 以**管理员身份**运行 `python main.py`

3. 选择合适位置抛下鱼竿，等待脚本自动完成钓鱼

* 没有

&emsp;&emsp;很遗憾，现有的配置文件并不支持你的电脑。但请不要灰心，您可以参照下面的教程构建自己的配置文件

### 关于 `detects/detects.yml`

&emsp;&emsp;该配置文件中存储着一些图片检测和坐标查找相关的选项：

```yaml
detects:
  - name: button
    convert: 'gray'  # 颜色转换，会通过 cv2.COLOR_BGR2{{convert.upper()}} 进行转换
    rect: { left: 0.83, top: 0.88, right: 0.13, bottom: 0.03 }  # 标注的矩形框
    mode: match  # 可选 match（匹配） | find（大图找小图）
    threshold: 0.95  # 置信度，超过此阈值时认为匹配成功
    template: button.png

  - name: hook
    rect: { left: 0.49, top: 0.1, right: 0.49, bottom: 0.78 }
    mode: find
    threshold: 0.85
    template: hook.png

progress:  # 进度条相关
  width: 0.26
  height: 0.027
  offset: 0.053  # 进度条中心点到鱼钩图案中心点的高度
  frame-color: [ 192, 255, 255 ]  # 金色滑框和游标的颜色【BGR】
  threshold: 0.04  # 金色像素点数量在进度框中占比达到阈值时，才认为这是一个合法的进度条
  sp: [ 6, 18 ]  # 在 Y 轴方向上金色像素点数量达到一级时，判定为滑框；数量达到二级时，判定为游标
```

* **detects**

&emsp;&emsp;指定一个待识别 / 查找的区域，其 `rect` 属性中按比例存储了区域的位置信息，比如左上四分之一方框可以表示为：

```yaml
rect: { left: 0, top: 0, right: 0.5, bottom: 0.5 }
```

&emsp;&emsp;`threshold` 和 `template` 必须同时指定或均不指定，当不指定时，脚本仅会在屏幕上划定一块区域，并为其赋予一个下标 `[i]`，可以通过按下快捷键 <kbd>Alt</kbd> + <kbd>小键盘对应数字</kbd> 对该区域快速截图，并保存到 `detects/clips/` 文件夹下

* **progress**

&emsp;&emsp;用于提高进度条识别准确度的一些配置，脚本通过检测其下方的鱼钩图标，再进行对应坐标换算的方式定位进度条，其 `width`、`height`、`offset` 属性均为与 `detects` 项中相同的比例表示方式。这里着重介绍一下 `sp` 属性的作用

&emsp;&emsp;钓鱼时出现的金色游标和滑框并不像进度条本身一样半透明或颜色会发生变化，其颜色始终为不透明的金色 `#ffffc0`，故通过统计金色像素在 y 轴方向上出现的数量，加上合适的阈值，便可推断出滑框和游标的位置

&emsp;&emsp;`sp` 为一个二元数组，设某一横坐标 `x` 下 y 轴方向金色像素数目为 `n`，则当 `sp[0] <= n < sp[1]` 时，认为这是一个滑框的左边界或右边界，而当 `n >= sp[1]` 时，则认为该位置是「更长」的游标。用截图工具截图并设法放大计数，便可得到 `sp` 的最佳取值

### 一些技巧

* 自动钓鱼时，将检测鱼钩图样的黄色方框置于不反光的水或蓝色天空背景下，有助于提高检测效率

* 如果在雪山、踏鞴砂等特殊钓点出现无法自动收竿的情况，请尝试更改阈值或设置专门的匹配图

### 声明和警告

* 脚本需要管理员权限是因为**游戏以管理员权限启动**，若无管理员权限则无法模拟鼠标动作

* **脚本使用 PostMessage 向游戏发送鼠标事件，存在被检测到的可能，如果你担心被封号，请不要使用该脚本**
