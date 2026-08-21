# WRF

## 文件结构

|名称|含义|是否需要修改|
|:--|:--|:--:|
|`fddahome`|运行时的必要组件（一般不改）|:x:|
|$\quad$|$\quad$|$\quad$|
|`data`|输入的`GFS`驱动场和可选的观测资料||
|`data/gfs4`|输入的`GFS`驱动场数据|:white_check_mark:|
|`data/cma_decoded`|输入的观测资料（可选）|:x:|
|$\quad$|$\quad$|$\quad$|
|`GMODJOBS`|运行配置和启动脚本||
|`GMODJOBS/$GMODJOBS`|案例配置和启动主文件夹||
|`GMODJOBS/$GMODJOBS/executables`|可执行文件|:x:|
|`GMODJOBS/$GMODJOBS/namelists`|`WRF`运行时配置（修改`WRF.nl.template.WCTRL`）|:white_check_mark:|
|`GMODJOBS/$GMODJOBS/wrfrun`|`WRF`运行时必要组件|:x:|
|`GMODJOBS/$GMODJOBS/wps`|`WPS`静态数据（将生成的`geo_em.d0?.nc`放到`6pass_glc`和`org`）|:white_check_mark:|
|`GMODJOBS/$GMODJOBS/start_rtfddaflex_gmod.pl`<br>`GMODJOBS/$GMODJOBS/rtfddaflex.pl`|启动脚本和工作脚本|:x:|
|`GMODJOBS/$GMODJOBS/member-nodes`|配置工作节点|:white_check_mark:|
|`GMODJOBS/$GMODJOBS/flexinput.pl`|配置实验时间和使用核数|:white_check_mark:|
|$\quad$|$\quad$|$\quad$|
|`cycles`|运行结果|:x:|
|`cycles/$GMODJOBS/zout`|运行日志||
|`cycles/$GMODJOBS/GFS_WCTRL/$TIME`|案例运行结果||
|`cycles/$GMODJOBS/GFS_WCTRL/$TIME/WRF_F`|spin up工作文件夹||
|`cycles/$GMODJOBS/GFS_WCTRL/$TIME/WRF_P`|预报工作文件夹||

## 运行前准备

### 驱动数据

将`GFS`数据软链接到`data/gfs4`。

```{dropdown} 服务器数据分布
:open: 
:icon: code
:color: info
:animate: fade-in
- 历史数据：`/data1/premdev/datainput_arc/gfs4/`
- 实时数据：`/public/home/premdev/data/datainput/gfs4/`
```

### 静态数据

将生成的`geo_em.d0?.nc`复制到`wps`：
```bash
cp $WPS/geo_em.d0?.nc wps/6pass_glc
cp $WPS/geo_em.d0?.nc wps/org
```

### 配置文件

根据`namelist.wps`修改`namelists/WRF.nl.template.WCTRL`：
|参数名称|实际含义|
|:--:|:--:|
|`e_we`/`e_sn`|每个域东西向/南北向的网格数|
|`dx`/`dy`|最外层网格东西向/南北向网格距（单位：米）|
|`i_parent_start`/`j_parent_start`|嵌套域左下角在父域中的`(i,j)`坐标，最外层网格为`1`|
|`parent_grid_ratio`|父域相对于嵌套域相的网格距比值，推荐`3`或`5`，最外层网格为`1`|

## 运行流程

根据实际需要的`node`编号修改`member-nodes`。

根据案例时间修改`flexinput.pl`：
|参数名称|实际含义|
|:--:|:--:|
|`NUM_DOMS`|嵌套域数量|
|`CYC_INT`|spin up时长|
|`FCST_LENGTH`|预报时长|
|`NUM_PROCS`|总核数|
|`CPUPERNODE`|单个节点的核数|
|`NPROCX`/`NPROCY`|不同方向上分配的核数|

> [!TIP]
> 当前只支持一个节点，所以在设置的时候需要满足`NUM_PROCS = CPUPERNODE = NPROCX * NPROCY`。

> [!NOTE]
> `node13`、`node14`各有32个核
> `node15`、`node16`各有36个核

提交任务（提交前记得用`nps`检查各个节点使用情况）：
```bash
./start_rtfddaflex_gmod.pl $GMODJOBS $YYYYMMDDHH
```
其中`$GMODJOBS`是实验名称，`$YYYYMMDDHH`是spin up结束时间。
