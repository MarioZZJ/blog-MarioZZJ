---
title: 下载 OpenAlex 数据集并配置 PySpark 的一次记录
comment: true
abbrlink: fb1f3a84
date: 2022-10-15 10:06:33
updated: 2022-11-12 16:30:00
tags:
    - OpenAlex
    - Spark
categories: 工程实践
keywords: 
    - pyspark
    - OpenAlex
desc: 记录一下最近一次配置开源数据集的过程
cover: https://download.mariozzj.cn/img/picgo/202210151013511.png
katex: false
aside: true
---

# 关于 OpenAlex 数据集

2015 年微软发布了微软学术知识图谱（MAG）数据集[^1]，因其海量、全面的特点被广泛应用于各类研究，但好景不长，2021 年该数据集停止维护。2022 年，OpenAlex 数据集应运而生[^2]，该数据集继承了大量来自 MAG 的数据，同时整合了来自 OpenCitations[^3]、AMiner[^4]、PID Graph[^5]、Open Research Knowledge Graph[^6]、Semantic Scholar[^7]、OpenAIRE research graph[^8] 的数据。

该数据集开放下载，将科学研究中的重要环节实体化，共分为 Works、Authors、Concepts、Institutions、Venues 等多个实体，并建立关联，每日更新。详见 [OpenAlex 文档](https://docs.openalex.org/)
![OpenAlex Entities](https://download.mariozzj.cn/img/picgo/202210151055844.png)

因为研究需要，我需要对 OpenAlex 进行下载配置。阅读文档后发现数据的获取较为轻松，因此记录一下。

# 下载 OpenAlex

OpenAlex 官方最推荐的使用方法是通过 API 调用的方式获取需要的部分数据，但是这对我来说或许有些麻烦，且不利于我了解整个数据集，因此计划将 OpenAlex 快照下载至高性能计算服务器进行进一步分析。

OpenAlex 数据集存储在 Amazon S3 上，以 gzip 压缩的 json 文件格式存储，根据数据的更新日期，对不同实体数据文件分区存储，同一分区下根据数据规模划分为若干文件，每个文件大小不超过 2 GB。

## 安装 AWS CLI

由于数据集存储在 Amazon S3 上，下载需要通过亚马逊 Web 服务命令行界面（Amazon Web Service Command Line Interface，AWSCLI）下载。根据 [AWS CLI 下载示例](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)，以 Linux 为例，需要执行以下命令：

```Shell
$ curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip" # 下载安装包
$ unzip awscliv2.zip # 解压
$ sudo ./aws/install # 执行安装
```

安装完成后，执行 `aws` 命令检查是否成功安装：

```Shell
$ aws --version
aws-cli/2.7.24 Python/3.8.8 Linux/4.14.133-113.105.amzn2.x86_64 botocore/2.4.5
```

## 下载数据集

安装完 AWS CLI 后即可执行命令下载数据集快照。需注意：整个快照的大小为 330 GB 或更多（可以通过运行 `aws s3 ls --summarize --human-readable --no-sign-request --recursive "s3://openalex/"` 命令查看当前版本快照大小），需要保证磁盘有足够的存储空间，同时确保网络能够保证完成这段时间的下载。

```Shell
$ aws s3 sync "s3://openalex" "openalex-snapshot" --no-sign-request
```

所有的数据会被下载至 `openalex-snapshot` 文件夹中。最终该文件夹的目录形式如：

```
openalex-snapshot/
├── LICENSE.txt
├── RELEASE_NOTES.txt
└── data
    ├── authors
    │   ├── manifest
    │   └── updated_date=2021-12-28
    │       ├── 0000_part_00.gz
    │       └── 0001_part_00.gz
    ├── concepts
    │   ├── manifest
    │   └── updated_date=2021-12-28
    │       ├── 0000_part_00.gz
    │       └── 0001_part_00.gz
    ├── institutions
    │   ├── manifest
    │   └── updated_date=2021-12-28
    │       ├── 0000_part_00.gz
    │       └── 0001_part_00.gz
    ├── venues
    │   ├── manifest
    │   └── updated_date=2021-12-28
    │       ├── 0000_part_00.gz
    │       └── 0001_part_00.gz
    └── works
        ├── manifest
        └── updated_date=2021-12-28
            ├── 0000_part_00.gz
            └── 0001_part_00.gz
```

# 本地处理转化 TSV

下载的文件分布于多个文件夹、文件中，且以 gzip 压缩的 json 文件形式存储，可能不便于后续 Spark 计算性能的发挥，所以需要将数据文件进行合并，并转化为 TSV 形式（数据内容含逗号，因此采用制表符作为分隔符）。

由于文件存储形式为 json，需要实现这些 json 文件到关系型数据的转化。这里参照 [OpenAlex Postgres schema diagram](https://docs.openalex.org/download-snapshot/upload-to-your-database/load-to-a-relational-database/postgres-schema-diagram) 进行导入，文档中还给出了 [CSV 转换脚本](https://gist.github.com/richard-orr/152d828356a7c47ed7e3e22d2253708d)，但是经过测试，发现该脚本存在一些缺陷，可能对后续 spark 读取带来困难，因此我对脚本的缺陷部分进行了调整：

* 调整所有读取、写入的文件方法，规定编码集为 `utf-8`；
* 调整分隔符为制表符，取消压缩过程；
* 对处理 json 的方法 `json.dumps()` 添加参数 `ensure_ascii=False`，确保不会将特殊字符转为 Unicode。（[反馈](https://gist.github.com/richard-orr/152d828356a7c47ed7e3e22d2253708d?permalink_comment_id=4336113#gistcomment-4336113)后，作者已修复）
* 对于所有的 id，源文件为完整 url，不便于之后的聚合、连接等操作时提升速度，因此均转换为 int 类型。

最终得到了 [调整问题后的 TSV 转换脚本](https://gist.github.com/MarioZZJ/3373ecd492af16728ac6c726d8639acd)。在 `openalex-snapshot` 文件夹外运行指令即可（注意需保证 python 版本不低于 3.8，以支持海象运算符）：

```Shell
$ python3 flatten-openalex-jsonl.py 
```

这将会在 `csv-files` 文件夹生成 27 个 tsv 文件，文件名同下图 Schema 中的表名。生成的 tsv

![OpenAlex Postgres schema](https://download.mariozzj.cn/img/picgo/202210151148705.png)

# 配置 PySpark

文件生成后，即可配置 PySpark 进行读取。虽然 PySpark 可以直接读取 json，但是实践发现由于 schema 设置和解析等问题，初始化过程较慢，不如 tsv。在 tsv 中我们的表结构是确定的，因此可以在读入时直接告知 spark 该表的 schema，这样可以加速初始化过程。如 works_concepts 表的读取过程：

```Python
# spark 是已经创建的 SparkSession，StructType 等是 pyspark.sql.types 中的类
WorksConcepts = spark.read.format('csv').options(
    header='true', delimiter='\t' # 分隔符为制表符
    ).schema(
        StructType(
        ).add(
            StructField('work_id', StringType(), True)
        ).add(
            StructField('concept_id', StringType(), True)
        ).add(
            StructField('score', FloatType(), True)
        )
    ).load('xxx/csv-files/works_concepts.tsv')
```

当然可以对方法和数据进行封装，最终得到基础的 [OpenAlex PySpark 初始化脚本](https://github.com/whuscity/pySparkDemo_OpenAlex/blob/main/spark_openalex_init.py)，在需要使用前调用运行即可。



[^1]:Arnab Sinha, Zhihong Shen, Yang Song, Hao Ma, Darrin Eide, Bo-June (Paul) Hsu, and Kuansan Wang. 2015. An Overview of Microsoft Academic Service (MAS) and Applications. In Proceedings of the 24th International Conference on World Wide Web (WWW '15 Companion). Association for Computing Machinery, New York, NY, USA, 243–246. https://doi.org/10.1145/2740908.2742839
[^2]:Priem, J., Piwowar, H., & Orr, R. (2022). OpenAlex: A fully-open index of scholarly works, authors, venues, institutions, and concepts. ArXiv. https://arxiv.org/abs/2205.01833
[^3]:Peroni, S., Shotton, D., Vitali, F. (2017). One Year of the OpenCitations Corpus. In: , et al. The Semantic Web – ISWC 2017. ISWC 2017. Lecture Notes in Computer Science(), vol 10588. Springer, Cham. https://doi.org/10.1007/978-3-319-68204-4_19
[^4]:Jie Tang, Jing Zhang, Limin Yao, Juanzi Li, Li Zhang, and Zhong Su. 2008. ArnetMiner: extraction and mining of academic social networks. In Proceedings of the 14th ACM SIGKDD international conference on Knowledge discovery and data mining (KDD '08). Association for Computing Machinery, New York, NY, USA, 990–998. https://doi.org/10.1145/1401890.1402008
[^5]:Fenner, M., & Aryani, A. (2019). Introducing the PID graph. https://doi.org/10.5438/JWVF-8A66
[^6]:Mohamad Yaser Jaradeh, Allard Oelen, Kheir Eddine Farfar, Manuel Prinz, Jennifer D'Souza, Gábor Kismihók, Markus Stocker, and Sören Auer. 2019. Open Research Knowledge Graph: Next Generation Infrastructure for Semantic Scholarly Knowledge. In Proceedings of the 10th International Conference on Knowledge Capture (K-CAP '19). Association for Computing Machinery, New York, NY, USA, 243–246. https://doi.org/10.1145/3360901.3364435
[^7]:Waleed Ammar, Dirk Groeneveld, Chandra Bhagavatula, Iz Beltagy, Miles Crawford, Doug Downey, Jason Dunkelberger, Ahmed Elgohary, Sergey Feldman, Vu Ha, Rodney Kinney, Sebastian Kohlmeier, Kyle Lo, Tyler Murray, Hsu-Han Ooi, Matthew Peters, Joanna Power, Sam Skjonsberg, Lucy Wang, et al.. 2018. Construction of the Literature Graph in Semantic Scholar. In Proceedings of the 2018 Conference of the North American Chapter of the Association for Computational Linguistics: Human Language Technologies, Volume 3 (Industry Papers), pages 84–91, New Orleans - Louisiana. Association for Computational Linguistics.
[^8]:Manghi, P., Atzori, C., Bardi, A., Schirrwagen, J., Dimitropoulos, H., ... Summan, F. (2019).
OpenAIRE Research Graph Dump. Zenodo. https://doi.org/10.5281/zenodo.3516918