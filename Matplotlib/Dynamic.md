## Matplotlib 绘制动态图

- [官方实例](https://matplotlib.org/stable/gallery/index.html#animation)

### 1. [animation.FuncAnimation](https://matplotlib.org/stable/api/_as_gen/matplotlib.animation.FuncAnimation.html)

- 参考资料
  - [Python制作动态图-matplotlib.animation](https://littleround.cn/2019/01/04/Python制作动态图-matplotlib.animation/)

#### 1.1. 参数说明

```python
matplotlib.animation.FuncAnimation(
    fig, 
    func, 
    frames=None, 
    init_func=None, 
    fargs=None, 
    save_count=None, 
    **kwargs
)
```

- `fig`：`Figure`对象
- `func`：绘图函数，其参数由`frames`传入
- `frames`：帧。是一个可迭代对象（如：`range(frames_number)`），每过一个`interval`的时间长度，会自动调用`func`并将下一个`frame`传入
- `fargs`：tuple or None。传递给`func`的附加参数
- `save_count`：缓存的帧数
- `interval`：两帧之间的时间间隔，单位：毫秒。
- `repeat_delay`：如果重复循环，每个循环之间的间隔。
- `repeat`：是否重复循环。

#### 1.2. 例子

```python
import matplotlib.pyplot as plt
import numpy as np

x = np.linspace(0, 10, 100)
y = np.sin(x)

fig = plt.figure()
ax = fig.add_subplot(111)
 
def func(n):
    ax.cla()
    ax.set_ylim([-1.1, 1.1])
    ax.plot(x[:n], y[:n])
    
from matplotlib.animation import FuncAnimation
ani = FuncAnimation(fig=fig, func=func, frames=range(100), interval=10, repeat=False)
```

#### 1.3. 保存

保存为gif格式需要安装`pillow`库

保存为视频格式需要安装`ffmpeg`库：`conda install ffmpeg`

##### 1.3.1. 保存维html5

```python
with open('test.html', 'w') as f:
    f.write(ani.to_html5_video())
```

##### 1.3.2. 保存为gif

```python
ani.save('test.gif', writer='pillow', fps=30)
```

##### 1.3.3. 保存为mp4

[`matplotlib.animation.FFMpegWriter`](https://matplotlib.org/stable/api/_as_gen/matplotlib.animation.FFMpegWriter.html)类

```python
from matplotlib.animation import writers
FFMpegWriter = writers['ffmpeg']
writer = FFMpegWriter(
    fps      = 30, 
    metadata = dict(title='file', artist='veager', comment="comment"), 
    bitrate  = 1800
)
ani.save('test.mp4', writer=writer)
```

### 2. `plt.pause()`实现

```python
`plt.pause(interval) # interval单位为秒
```

#### 2.1. 例子

```python
# 保存图片
import os
if not os.path.exists('pic'):
    os.makedirs('pic')

for n in range(100):
    ax.cla()
    ax.set_ylim([-1.1, 1.1])
    ax.plot(x[:n], y[:n])
    plt.pause(0.01)
    # 保存图片，用于合成gif
    plt.savefig('pic\{0:0>3d}.png'.format(n), dpi=300)
```

#### 2.2. 图片合成gif和视频

##### 2.2.1. 合成gif

```python
import re
import imageio

outfilename = "test.gif" # 转化的GIF图片名称
file_folder = 'pic'
file_path = []
# 获取所有图片路径
for file in os.listdir(file_folder):
    file_path.append(os.path.join(file_folder, file))
# 图片路径排序  
file_path = list(
    sorted(
        file_path, 
        key=lambda x:int(re.findall('\d+', x.split('\\')[-1])[0]), 
        reverse=True
    )
)
# 保存为gif
frames = []
for file in file_path:
    im = imageio.imread(file)  
    frames.append(im)
imageio.mimwrite(outfilename, frames, 'GIF', duration=0.1)
```

