# SCP_UserGuide

single-cell RNA-seq analysis R package SCP user guide Chinese version （未完整）
SCP version: v0.5.5

# SCP单细胞数据分析使用说明

## 数据质控

| function | RunCellQC                                                                                                                                                                                   |
| -------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 使用示范 | `data<-RunCellQC(data)`                                                                                                                                                                   |
| 结果     | 返回完整SeuratObject，data@meta.data中增加<br />umi_qc,gene_qc,mito_qc,ribo_qc,ribo_mito_ration_qc,species_qc等统计信息，<br />如果认为默认QC判定标准过于严格，可自行调整过滤参数重新过滤。 |
| 完整说明 | [https://zhanghao-njmu.github.io/SCP/reference/RunCellQC.html]()                                                                                                                               |

常用参数：

* db_method: 默认使用scDblFinder，可选 `Scrublet`, `DoubletDetection`, `scds_cxds`, `scds_bcds`, `scds_hybrid`
* db_rate: 默认1%
* db_coefficient:默认0.01
* outlier_threshold: 默认 `c("log10_nCount:lower:2.5", "log10_nCount:higher:5", "log10_nFeature:lower:2.5", "log10_nFeature:higher:5", "featurecount_dist:lower:2.5")`
* UMI_threshold: 默认大于3000
* gene_threshold:默认大于1000
* mito_threshold:默认小于20
* mito_pattern: 根据物种选择mitochondria gene symbol格式，如人="MT-",小鼠="mt-"；
* mito_gene: 如果物种线粒体基因名字不具有一定格式，如猪，猴等其他物种，可以用mito_gene参数输入基因集合做计算。
* ribo_threshold: 默认50
* ribo_pattern：默认正则表达式匹配为 `c("RP[SL]\d+\w0,1\d*$)","Rp[sl]\d+\w0,1\d*$","rp[sl]\d+\w0,1\d*$")`
* ribo_gene: 使用与mito_gene类似，可输入基因结集合用作ribosome基因表达比例的计算

## batch处理

| function | Integration_SCP                                                                                                                                                                                      |
| -------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 使用示范 | 按照data@meta.data中"Sample"列的信息，使用Harmony的方法对数据进行去批次处理<br />`data<-Integration_SCP(srtMerge=data,batch="Sample",integration_method="harmony"，cluster_resolution=c(0.3,0.5))` |
| 结果     | 1.data@reduction中增加HarmonyUMAP2D,后续画图需设置参数 reduction="HarmonyUMAP2D"<br />2.data@meta.data中增加Harmony_SNN_res.0.3,Harmony_SNN_res.0.5的分群结果                                        |
| 完整说明 | [https://zhanghao-njmu.github.io/SCP/reference/Integration_SCP.html]()                                                                                                                                  |

常用参数：

* batch: 认为是批次的属性，如"Sample"
* integration_method: 去批次的方法，默认Uncorrected，其他可选  `"Seurat"`, `"scVI"`, `"MNN"`, `"fastMNN"`, `"Harmony"`, `"Scanorama"`, `"BBKNN"`, `"CSS"`, `"LIGER"`, `"Conos"`, `"ComBat"`
* do_normalization：默认NULL
* normalization_method: 默认LogNormalize, 其他可选：`"LogNormalize", "SCT", and "TFIDF"`
* do_HVF_finding：默认TRUE，是否重新计算highly variable features(同Seurat中HVG概念)
* HVF_method: 默认为vst，其他可选：
* nHVF:默认2000，HVF的features数
* neighbor_k:默认20，KNN的neighbor数设置，该值越大，点与点连线越多
* cluster_resolution：默认等于0.6，可同时设置多个参数值

## 标准降维分析

| function | Standard_SCP                                                     |
| :------- | :--------------------------------------------------------------- |
| 使用示范 | 使用默认过滤参数对数据进行质控<br />`data<-Standard_SCP(data)` |
| 结果     | 在data@reduction等数据结构中增加对应的结果                       |
| 完整说明 | [https://zhanghao-njmu.github.io/SCP/reference/Standard_SCP.html]() |

常用参数：

## 差异基因计算

| function | RunDEtest                                                                                                                                                                                                                                                                                                                                                           |
| -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 使用示范 | 1. 计算细胞类型(Celltype)间的差异表达基因<br />`data<-RunDEtest(data,group_by="Celltype",fc.threshold = 1, only.pos = TRUE,BPPARAM = BiocParallel::MulticoreParam(workers = 2))`<br />2. 计算细胞类型组间的差异表达基因<br />`data<-RunDEtest(data,group_by="Celltype",fc.threshold = 1, only.pos = TRUE,BPPARAM = BiocParallel::MulticoreParam(workers = 2))` |
| 结果     | 1. 在data@tools$DE_Celltype<br />2.                                                                                                                                                                                                                                                                                                                                |
| 完整说明 | [https://zhanghao-njmu.github.io/SCP/reference/RunDEGtest.html]()                                                                                                                                                                                                                                                                                                      |

常用参数：

* BPPARAM: 线程设置，默认使用所有线程，如设置两个线程设置参数BPPARAM = BiocParallel::MulticoreParam(workers = 2)

## 差异基因富集分析及画图

1. GO,KEGG 富集分析

| function | RunEnrichment                                                     |
| -------- | :---------------------------------------------------------------- |
| 使用示范 |                                                                   |
| 结果     |                                                                   |
| 完整说明 | [https://zhanghao-njmu.github.io/SCP/reference/RunEnrichment.html]() |

2. GSEA分析

|          |                                                             |
| -------- | ----------------------------------------------------------- |
| function | RunGSEA                                                     |
| 使用示范 |                                                             |
| 结果     |                                                             |
| 完整说明 | [https://zhanghao-njmu.github.io/SCP/reference/RunGSEA.html]() |

3. 结果可视化

| function | EnrichmentPlot                                                     |
| -------- | :----------------------------------------------------------------- |
| 使用     |                                                                    |
|          |                                                                    |
| 完整说明 | [https://zhanghao-njmu.github.io/SCP/reference/EnrichmentPlot.html]() |

## Trajectory分析之Slingshot及可视化

## Trajectory分析之Monocle2及可视化

## Trajectory分析之Monocle3及可视化

## Trajectory结果趋势基因分析及可视化

RunDynamicFeatures

## PAGA分析

## Velocity RNA速率分析

## 可视化

### 降维相关图

### 基因相关图

function: GroupHeatmap

个人推荐图：

更多图：

marker图（热图，气泡图，小提琴图等）

marker-cells图（热图，小提琴图等）

差异基因热图+富集结果展示图

差异基因富集结果可视化  EnrichmentPlot

### 比例相关图

### Trajectory结果可视化

DynamicHeatmap

DynamicPlot

## FAQ

1. SCP目前不支持Seurat5， 建议新建R包文件目录，先安装Seurat4，之后再使用该目录作为libPath安装SCP
