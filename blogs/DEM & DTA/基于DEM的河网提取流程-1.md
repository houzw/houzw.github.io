# 基于DEM的河网提取流程

## ArcHydro Tools 及多源 DEM 提取河网
### 缺乏辅助信息下的河网提取划分
>曾红伟等，ArcHydro Tools 及多源 DEM 提取河网与精度分析——以洮儿河流域为例

1. 填洼
2. 流向 Flow Direction
3. 汇流累积（矩阵）生成 Flow Accumulation - 汇流累积栅格图
4. 河流栅格图生成 Stream Definition 给定最小汇水面积阈值，并采用 Stream Segmentation 方法建立栅格河段上下游拓扑关系
5. 流域边界生成 Catchment Grid Delineation，使用 Catchment Polygon Processing 方法, 生成的流域整体边界 Catchment
6. 河网矢量化 Drainage Line Processing

### 辅助信息下河网提取
>流域内真实河流

增加步骤：
1. 河网校正 DEM Reconditioning
2. 流域边界校正 Build Walls
3. 划分流域与子流域: 以水文站点为流域出口, 划分各流域和子流域（Watershed & Subwatershed）。
