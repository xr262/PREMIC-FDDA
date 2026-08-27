# FDDA

## 雷达资料处理

> 工作路径：`/data3/XuRan/datest/SWAN`

> [!IMPORTANT]
> SWAN数据目前只在GPU节点可用。

在命令行执行：
```bash
./run.sh -n $GMODJOBS -i $INDEX -t $TIME -d $DURATION
```
其中：
- `$GMODJOBS`表示案例名称；
- `$INDEX`表示模式区域；
- `$TIME`表示同化开始时间（格式：YYYYMMDDHH）；
- `$DURATION`表示同化时长（单位：小时）。

或者也可以修改文件内的参数并直接执行：
```bash
./run.sh
```

结果会保存在`wrffdda/$GMODJOBS/$TIME`下，在该目录下，执行如下命令，将生成的文件拼接到一起：
```bash
ncrcat wrffdda_d02_* wrffdda_d02
```

## 运行同化实验

> 工作路径：`/data3/XuRan/datest/cycles/$GMODJOBS/GFS_WCTRL/$TIME/`

进入`/WRF_P`目录，配置`WRF_P`文件夹作为同化模板：
```bash
WRF_P
```
把刚处理好的雷达资料同化数据wrffdda链接过来：
```bash
ln -s /data3/XuRan/datest/SWAN/wrffdda/$GMODJOBS/$TIME/wrffdda_d02
```

返回上一级`/$TIME`目录，复制`WRF_P`同化模版并命名成SWAN：
```bash
cp -r WRF_P SWAN
```

进入新复制命名的同化模版`SWAN`目录下，运行同化试验 
用`nps`查看可运行的节点，按需修改`/SWAN/namelist.input`里的`nproc_x`和`nproc_y`

双核运行：编辑`hosts`文件，并提交任务
```bash
for node in 13 14 15 16; do echo "node$node:32" >> hosts; done
nohup mpirun -machinefile hosts ./wrf.mpich &> /dev/null &
```
>[!NOTE]
>13&14双核，32+32=64，所以修改x、y别是8、8；15&16双核，36+36=72，所以修改x、y别是8、9。

单核运行：
```bash
nohup mpirun -np 32 ./wrf.mpich &> /dev/null &
```
或者
```bash
nohup mpirun -np 36 ./wrf.mpich &> /dev/null &
```

## Python后处理可视化
### 组合反射率图
找到`/data3/code/assimilation/case.py`文件，修改前置参数，即可运行
>[!TIP]
>`mode = "SWAN"`中的SWAN是复制命名后的配置WRF_P同化模版，名变此处变
### 定量评估图
`/data3/code/assimilation/metrics.py`文件用于定义FSS和TS如何计算
`/data3/code/assimilation/sequence.py`文件用于画图