[TOC]

### 创建虚拟环境

```
conda create -n env_name python=x.x
例:
conda create -n my_env python=3.8.0
```

env_name：所要创建的虚拟环境名称。python=x.x：所选择的python版本。

### 复制虚拟环境

```
conda create -n env_new --clone env_old
```

env_new：即将生成的环境。env_old：现已有的环境。

### 激活虚拟环境

```
conda activate env_name
```

env_name：待激活的虚拟环境名称。

### 删除虚拟环境

```
conda remove --name env_name  --all
conda env remove --name your_env_name
```

env_name：待删除的虚拟环境名称。

### 退出虚拟环境

```
conda deactivate
```

### 查看已存在的虚拟环境

```
conda env list
```

### 查看虚拟环境下已安装的包

```
conda list
```

### 检查更新当前conda

```
conda update conda
```

### pip常见操作

可以在使用pip的时候加参数 -i https://pypi.tuna.tsinghua.edu.cn/simple
国内常见的其他镜像网址：
清华大学 https://pypi.tuna.tsinghua.edu.cn/simple/
阿里云 https://mirrors.aliyun.com/pypi/simple/
中国科技大学 https://pypi.mirrors.ustc.edu.cn/simple/
豆瓣(douban) http://pypi.douban.com/simple/
中国科学技术大学 http://pypi.mirrors.ustc.edu.cn/simple/

```
例：pip install -i https://pypi.tuna.tsinghua.edu.cn/simple tensorflow==1.31.1
```

pip批量下载.txt文件中的包

```
pip install -r 你的文件名.txt
```

