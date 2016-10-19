# 基于DEM的水系/河网提取方法、流程学习笔记

## 1. 水系提取
>汤国安等，数字高程模型及地学分析的原理和方法，p243

### 1.1 概述
1. 集水流域：水流及其他物质流向流域出口的过程中所流经的区域，即流向集水出口的水流所流经的整个地区
2. 集水出口：水流离开集水流域的点，是集水流域边界上的最低点
3. 子流域：较大的集水流域结构中的一部分
4. 分水岭：两个集水流域的边界
5. 水流网络：流水到达集水出口所流经的网络结构，可视为一树状结构，根节点为集水出口，两支流的交点成为水流网络节点

![enter description here][1]
					
## 2. ArcHydro Tools 及多源 DEM 提取河网
### 2.1 缺乏辅助信息下的河网提取划分
>曾红伟等，ArcHydro Tools 及多源 DEM 提取河网与精度分析——以洮儿河流域为例

1. 填洼
2. 流向 Flow Direction
3. 汇流累积（矩阵）生成 Flow Accumulation - 汇流累积栅格图
4. 河流栅格图生成 Stream Definition 给定最小汇水面积阈值，并采用 Stream Segmentation 方法建立栅格河段上下游拓扑关系
5. 流域边界生成 Catchment Grid Delineation，使用 Catchment Polygon Processing 方法, 生成的流域整体边界 Catchment
6. 河网矢量化 Drainage Line Processing

### 2.2 辅助信息下河网提取
>流域内真实河流

增加步骤：
 1. 河网校正 DEM Reconditioning
 2. 流域边界校正 Build Walls
 3. 划分流域与子流域: 以水文站点为流域出口, 划分各流域和子流域（Watershed & Subwatershed）。


  [1]: ./images/1476890742558.jpg