# Conda笔记

```shell
#创建虚拟环境
conda create -n your_env_name python=X.X（3.6、3.7等）
 
#激活虚拟环境
source activate your_env_name(虚拟环境名称) # 不用source也可以
 
#退出虚拟环境
source deactivate your_env_name(虚拟环境名称) # 不用source也可以
 
#删除虚拟环境
conda remove -n your_env_name(虚拟环境名称) --all
 
#重命名虚拟环境 
conda env rename <旧的名称> <新的名称>

#查看安装了哪些包
conda list
 
#安装包
conda install package_name(包名)
conda install scrapy==1.3 # 安装指定版本的包
conda install -n 环境名 包名 # 在conda指定的某个环境中安装包
 
#查看当前存在哪些虚拟环境
conda env list 
#或 
conda info -e
#或
conda info --envs
 
#检查更新当前conda
conda update conda
 
#更新anaconda
conda update anaconda
 
#更新所有库
conda update --all
 
#更新python
conda update python
```