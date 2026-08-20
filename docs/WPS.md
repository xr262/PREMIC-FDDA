# WPS

> 源文件来自`/public/software/apps/wrf/intelmpi/4.1.1/WPS/`。

## 区域配置

在[WRF Domain Wizard](https://jiririchter.github.io/WRFDomainWizard/)里框选好区域后对应修改`namelist.wps`：
|参数名称|实际含义|
|:--:|:--:|
|`max_dom`|嵌套域总数|
|`parent_id`|每个域的父域编号，最外层网格固定为`1`|
|`parent_grid_ratio`|父域相对于嵌套域相的网格距比值，推荐`3`或`5`，最外层网格为`1`|
|`i_parent_start`/`j_parent_start`|嵌套域左下角在父域中的`(i,j)`坐标，最外层网格为`1`|
|`e_we`/`e_sn`|每个域东西向/南北向的网格数|
|`dx`/`dy`|最外层网格东西向/南北向网格距（单位：米），嵌套域按`parent_grid_ratio`自动推算|
|`map_proj`|地图投影，中纬度常用`lambert`|
|`ref_lat`/`ref_lon`|模拟域已知参考点的经纬度，一般取最外层网格中心|
|`truelat1`/`truelat2`|`Lambert`投影的两个真实纬度|
|`stand_lon`|与`y`轴平行的标准经度，设为与`ref_lon`相同可使最外层网格居中|
|`geog_data_path`|静态地理数据存放路径，固定为`/public/data/geog/`|

## 运行流程

生成`geo_em.d0?.nc`：
```bash
./geogrid.exe
```

## 可视化

开启X11图形界面转发后在`WPS`目录下执行：
```bash
ncl util/plotgrids_new.ncl
```

获取远端项目后参考其中的`README.md`运行绘制：
```bash
git clone https://github.com/XiaAnren/WRF-Domain.git
```
