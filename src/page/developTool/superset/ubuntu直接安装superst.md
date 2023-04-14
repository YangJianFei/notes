## docker安装
https://hub.docker.com/r/apache/superset

## 本地安装部署
###：aconda3安装
1:https://blog.csdn.net/long198715/article/details/127859436

装完anaconda之后，Ubuntu终端前面会多一个base，这指的是anaconda中env的根目录，可以通过以下命令，将其删除，即将“auto_activate_base”设置成False。
conda config --set auto_activate_base False
source .bashrc

conda进入虚拟环境
source activate  环境名称/conda activate 环境名称

conda退出环境
conda deactivate/source deactivate

2:conda的一些操作
https://blog.csdn.net/A33280000f/article/details/127114133

### superset开发环境搭建
https://zhuanlan.zhihu.com/p/439253667
https://www.jianshu.com/p/809990babb13