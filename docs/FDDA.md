# FDDA

## 雷达资料处理（只能在GPU节点运行）

> 工作路径：`/data3/XuRan/datest/SWAN`

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

结果会保存在`wrffdda/$GMODJOBS/$TIME`下，将生成的文件拼接到一起：
```bash
ncrcat wrffdda_d02_* wrffdda_d02
```

## 运行同化实验

> 工作路径：`/data3/XuRan/datest/cycles/$GMODJOBS/GFS_WCTRL/$TIME/`

配置`WRF_P`文件夹作为同化模板：
```bash
WRF_P
ln -s /data3/XuRan/datest/SWAN/wrffdda/$GMODJOBS/$TIME/wrffdda_d02
```

复制`WRF_P`：
```bash
cp -r WRF_P SWAN
```

按需修改`namelist.input`里的`nproc_x`和`nproc_y`、编辑`hosts`文件，并提交任务：
```bash
for node in 13 14 15 16; do echo "node$node:32" >> hosts; done
nohup mpirun -np 32 ./wrf.mpich &> /dev/null &
nohup mpirun -np 36 ./wrf.mpich &> /dev/null &
nohup mpirun -machinefile hosts ./wrf.mpich &> /dev/null &
```
