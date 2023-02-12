在ubuntu里面打开

# 1. 前期准备
# 定位
```
cd /mnt/c/shengxin
```
# 新建一个biosoft文件夹用于存放生物软件工具
mkdir biosoft
# 新建一个项目文件夹其中包含大鼠的文件夹

mkdir用法：

mkdir [-p] dirName,-p 确保目录名称存在，不存在的就建一个。
```
mkdir -p project/rat
```

在工作目录下的project目录中，建立一个名为 rat的子目录。
# 进入

```
cd project/rat
```
# 新建各类存放的文件夹

```
mkdir annotation genome sequence output script
```

# 转到cd project/rat文件夹下看已建立的目录结构

```
cd project/rat
tree
```
结果
```
.
├── annotation  用于存放大鼠的基因组注释信息(.gff/gtf)
├── genome      用于存放大鼠的基因组数据(.fasta)
├── output      用于存放处理后的数据
├── script      用于部分脚本
└── sequence    用于存放测序原始数据

5 directories, 0 files
```
# 2. 工具

2.0 conda可以很方便的安装和管理生信相关的工具

# 2.1 sratoolkit
sra是NCBI的用于下载数据以及转化数据使用

- brew安装
```
brew install sratoolkit
```

# 2.2 fastqc
对测序文件质量控制

```
brew install fastqc
```
# 2.3 multiqc
将fastqc的统计结果汇聚成一个网页可视化文件，便于查看

- 使用python的安装器安装
```
pip install multiqc
```
# 2.4 cutadapt
用于去除测序接头
```
pip install cutadapt
```
# 2.5 Trim Galore
使用perl脚本编写的工具，是对cutapater和fastqc命令的封装。是自动检测adapter的质控软件。适用于所有高通量测序，包括RRBS(Reduced Representation Bisulfite-Seq ), Illumina、Nextera 和smallRNA测序平台的双端和单端数据

```
cd ~/biosoft

wget https://github.com/FelixKrueger/TrimGalore/archive/0.6.3.tar.gz -O TrimGalore.gz

gzip -d TrimGalore.gz
```
     gzip -d --decompress --uncompress 解压
- fastp: 一款超快速全功能的FASTQ文件自动化质控+过滤+校正+预处理软件
- trimmomatic

trimmomatic是一款多线程命令行工具，可以用来修剪Illumina (FASTQ)数据以及删除接头，是目前使用最多的高通量测序数据清洗的工具。

```
cd ~/biosoft
wget http://www.usadellab.org/cms/uploads/supplementary/Trimmomatic/Trimmomatic-0.38.zip
unzip Trimmomatic-0.38.zip

cd Trimmomatic-0.38
export PATH="$(pwd):$PATH"（导入临时环境变量）
```
# 2.6 hisat2
首先用浏览器进入[hisat2](https://daehwankimlab.github.io/hisat2/)
在右侧有对应的不同系统的安装版本，根据自己的系统进行选择

```
cd ~/biosoft/
wget https://cloud.biohpc.swmed.edu/index.php/s/hisat2-210-Linux_x86_64/download
unzip download.1
cd hisat2-2.1.0

- 导入临时环境变量
export PATH="~/biosoft/hisat2-2.1.0:$PATH"

- 测试是否可用
hisat2 -h
```
# 2.7 samtools
比对得到的sam或者bam文件的各种操作

```
brew install samtools
```
# 2.8 HTseq
对比对后的文件进行read计数
```
pip install -i https://pypi.tuna.tsinghua.edu.cn/simple HTseq
```
# 2.9 R
R语言最擅长统计学分析，这里在后续的基因表达量分析，差异分析以及作图时候会用到
 
```
brew install r
```
# 2.10 Rstudio
因为R自身带的界面使用起来不是特别方便和美观，这里使用Rstudio来对R的显示效果进行提升，除此之外还有其他功能。

# 2.11 parallel
parallel是进行多线程运行的工具，并行运行可以提升效率，节省时间
```
brew install parallel
```

# Ballgown[可选]
是R语言中基因差异表达分析的工具，能利用RNA-Seq实验的数据(StringTie, RSEM, Cufflinks)的结果预测基因、转录本的差异表达。

安装
source("http://bioconductor.org/biocLite.R")
biocLite("Ballgown")