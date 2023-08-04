在ubuntu里面打开
# 1. 前期准备

```
# 定位 
cd /mnt/c/shengxin 

- 新建一个biosoft文件夹用于存放生物软件工具
mkdir biosoft

- 新建一个项目文件夹其中包含大鼠的文件夹
mkdir -p project/rat
 mkdir用法：
mkdir [-p] dirName,-p 确保目录名称存在，不存在的就建一个

- 在工作目录下的project目录中，建立一个名为 rat的子目录

# 进入
cd /mnt/c/shengxin/project/rat

# 新建各类存放的文件夹
mkdir annotation genome sequence output script

# 转到cd project/rat文件夹下看已建立的目录结构
cd /mnt/c/shengxin/project/rat
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

## 2.1 sratoolkit
sra是NCBI的用于下载数据以及转化数据使用

- brew安装
```
brew install sratoolkit

# 导入临时环境变量
$ export PATH="$(pwd):$PATH"
$ prefetch --help
```

## 2.2 fastqc
对测序文件质量控制

```
brew install fastqc
```
## 2.3 multiqc
将fastqc的统计结果汇聚成一个网页可视化文件，便于查看

- 使用python的安装器安装
```
pip install multiqc
```
## 2.4 cutadapt
用于去除测序接头
```
pip install cutadapt
```
## 2.5 Trim Galore
使用perl脚本编写的工具，是对cutapater和fastqc命令的封装。是自动检测adapter的质控软件。适用于所有高通量测序，包括RRBS(Reduced Representation Bisulfite-Seq ), Illumina、Nextera 和small RNA测序平台的双端和单端数据

```
cd /mnt/c/shengxin/biosoft

wget https://github.com/FelixKrueger/TrimGalore/archive/0.6.3.tar.gz -O TrimGalore.gz

gzip -d 0.6.10.tar.gz
#gzip -d --decompress --uncompress 解压
```

- fastp: 一款超快速全功能的FASTQ文件自动化质控+过滤+校正+预处理软件
- trimmomatic

trimmomatic是一款多线程命令行工具，可以用来修剪Illumina (FASTQ)数据以及删除接头，是目前使用最多的高通量测序数据清洗的工具。

安装如下
```
cd /mnt/c/shengxin/biosoft
wget http://www.usadellab.org/cms/uploads/supplementary/Trimmomatic/Trimmomatic-0.38.zip
unzip Trimmomatic-0.38.zip

cd Trimmomatic-0.38
export PATH="$(pwd):$PATH"（导入临时环境变量）
```
## 2.6 hisat2
Hisat2主要用于转录组数据的比对

首先用浏览器进入[hisat2](https://daehwankimlab.github.io/hisat2/)
在右侧有对应的不同系统的安装版本，根据自己的系统进行选择

```
cd /mnt/c/shengxin/biosoft
wget https://cloud.biohpc.swmed.edu/index.php/s/hisat2-210-Linux_x86_64/download
unzip download
cd hisat2-2.1.0

- 导入临时环境变量
export PATH="~/biosoft/hisat2-2.1.0:$PATH"

- 测试是否可用
hisat2 -h

#前面都没有问题，但是在这里一直出现
Command 'hisat2' not found, but can be installed with:

sudo apt install hisat2
#运行sudo apt install hisat2出现E: Unable to locate package hisat2
解决方案 sudo apt update#更新存储库缓存
```
## 2.7 samtools
比对得到的sam或者bam文件的各种操作

 SAM (Sequence Alignment/Map)格式是一种通用的比对格式，用来存储reads到参考序列的比对信息

 bam 是sam 则是一种比gz更加高效的压缩算法

```
brew install samtools一直报错
```
换源代码安装
```
cd /mnt/c/shengxin/biosoft
wget -c https://github.com/samtools/samtools/releases/download/1.17/samtools-1.17.tar.bz2 #wget -c 断点续传,对于大文件下载时需要继续中断的下载时可以使用-c参数)
tar jxvf samtools-1.17.tar.bz2 #tar jxvf是解压指令，指解压bzip2格式的文件，j-解压指令（*.bz)，x-释放，v-释放时的信息，f-文件
cd samtools-1.17

# 配置信息
./configure --prefix=$(pwd)#源码的安装一般由3个步骤组成：配置（configure）、编译（make）、安装（make install），Configure是一个可执行脚本，它有很多选项，使用命令./configure –help输出。 configure --prefix=/”的作用是：编译的时候用来指定程序存放路径

# 开始编译
make -j 4

# 导入临时环境变量
export PATH="$(pwd):$PATH"

#检查samtools是否安装成功
samtools

#出现以下界面
Program: samtools (Tools for alignments in the SAM format)
Version: 1.17 (using htslib 1.17)

Usage:   samtools <command> [options]

Commands:
  -- Indexing
     dict           create a sequence dictionary file
     faidx          index/extract FASTA
     fqidx          index/extract FASTQ
     index          index alignment
```

## 2.8 HTseq
对比对后的文件进行read计数
```
pip install -i https://pypi.tuna.tsinghua.edu.cn/simple HTseq #有问题
```
## 2.9 R
[官网](https://www.r-project.org)

- 介绍
  
R语言中集合了多种生物信息学的分析工具，其中RNA-seq分析的工具更是丰富，R语言最擅长统计学分析，这里在后续的基因表达量分析，差异分析以及作图时候会用到
- 下载

点击左上角```CRAN```，往下拉找到```china```的站点，就选第一个清华的站点，点击进去，之后最上面有三个对应系统的安装包，按照自己的系统下载，这里点击```Windows```，点击```install R for the first time```后在新界面点击```Download R-4.2.2 for Windows```就开始下载了，下载之后```双击```安装包安装。
- brew
```
brew install r
```
## 2.10 Rstudio
因为R自身带的界面使用起来不是特别方便和美观，这里使用Rstudio来对R的显示效果进行提升，除此之外还有其他功能。

进入网站：https://www.rstudio.com/products/rstudio/download/

点击```RStudio Desktop Open Source License``` 下面的DOWNLOAD，之后选择对应的版本的，这里选择```Windows 10/11 OS```版本
```RSTUDIO-2023.03.1-446.EXE```下载完成之后双击安装。

## 2.11 parallel
parallel是进行多线程运行的工具，并行运行可以提升效率，节省时间
```
brew install parallel
```

## Ballgown[可选]
是R语言中基因差异表达分析的工具，能利用RNA-Seq实验的数据(StringTie, RSEM, Cufflinks)的结果预测基因、转录本的差异表达。


# 3. 数据下载

这里使用大鼠的测序数据作为测试。大鼠又叫大白鼠（Rat，Rattus norvegicus），在ENSEMBL和UCSC中均有其基因组数据。

## 3.1 参考数据
下载基因组序列

[Ensembl](https://asia.ensembl.org/index.html)

在左侧```All genomes```中，选择物种```Rat```; 在左侧```Download DNA sequence (FASTA)``` 下载基因组序列数据; 在右侧的```Download GTF or GFF3 (files for genes, cDNAs, ncRNA, proteins)```下载基因注释文件

![image](https://github.com/QRunMiao/RNA-seq-study/blob/picture/1676251978571.jpg)

- [ensmble的readme](https://ftp.ensembl.org/pub/release-109/fasta/rattus_norvegicus/dna/README)

## ensmble FILE NAMES
首先规定了文件命名规律：<species>.<assembly>.<sequence type>.<id type>.<id>.fa.gz

第一部分<species>是物种名；

第二部分<assembly>是基因组版本号；

第三部分<sequence type>是序列类型：

· dna：原原本本的DNA序列

· dna_rm（hard_mask）：利用RepeatMasker工具将重复区域和低复杂度区域标记成一串N

· dna_sm（soft_mask）：所有重复区域和低复杂度区域替换为小写的碱基

第四部分<id type>是基因组类型：

· chromosome：染色体序列

· nonchromosomal：存储目前没有比对到染色体的DNA序列

· seqlevel：一般是scaffolds, chunks 或clones，还未组装到染色体标准的序列信息


第五部分<id>是实际的序列编号，比如属于哪条染色体或者属于哪个scaffold, chunk 或clone

最后这个fa文件以.gz形式压缩保存

· 如人类1号染色体的基因组序列：```Homo_sapiens.GRCh37.dna.chromosome.1.fa.gz```

- TOPLEVEL： 这样的数据包含了所有的序列区域（比如染色体、非染色体以及用大量N填充的单倍型haplotypes-(指一个染色单体里面具有统计学关联性的一类单核苷酸多态性（SNPs）或基因组补丁patches区域），比如：```Homo_sapiens.GRCh37.dna.toplevel.fa.gz```

- PRIMARY ASSEMBLY： 在上面toplevel的基础上，排除了单倍型或基因组补丁区域。如果看到目录中不存在这种类型的数据（比如这里果蝇就没有，而人类的基因组数据就存在），那么就意味着基因组不包含单倍型或基因组补丁区域，其实也就是等同于TOPLEVEL

- special 注意
一些染色体是单倍体，例如人类的X和Y染色体
为了比对时能正确输出报告，这些单倍体的assembly和patch区域都会补上同等数量的N


## 下载

一般下载toplevel.fa.gz文件，为参考基因组完整文件

基因组数据说明

格式为.fasta，格式为：
```
>seq_id
AGCTGAGCTAGCTACGGAGCTGAC
ACGACTGATCTGACGTTGATCGTT
```

文件中以>开头的是序列的名称，下面接着的ATGC是这条序列信息，基因组fasta文件记录大鼠的所有的被测得的染色体的序列信息，目前大鼠的基因组测序版本到了7.2

可以直接在网页下载，也可用代码

```
cd /mnt/c/shengxin/project/rat/genome

wget https://ftp.ensembl.org/pub/release-109/fasta/rattus_norvegicus/dna/Rattus_norvegicus.mRatBN7.2.dna.toplevel.fa.gz

gzip -d Rattus_norvegicus.mRatBN7.2.dna.toplevel.fa.gz
```
## 改名
（方便后面使用，名字太长一来不方便输入，二来可能会输错）

```mv Rattus_norvegicus.mRatBN7.2.dna.toplevel.fa R7.2.fa```

- cat & grep 一起使用可以实现在文本内查找关键词

```cat 文件 | grep 关键字``` 

下载得到的基因组文件可以查看一下包含哪些染色体，确认文件是否下载正确

```cat R7.2.fa | grep "^>" ```

结果：除了```1-20号+X+Y+MT```之外还有很多别的ID名。这些都是```scaffold```

```
>1 dna:primary_assembly primary_assembly:mRatBN7.2:1:1:260522016:1 REF
>2 dna:primary_assembly primary_assembly:mRatBN7.2:2:1:249053267:1 REF
>3 dna:primary_assembly primary_assembly:mRatBN7.2:3:1:169034231:1 REF
>4 dna:primary_assembly primary_assembly:mRatBN7.2:4:1:182687754:1 REF
>5 dna:primary_assembly primary_assembly:mRatBN7.2:5:1:166875058:1 REF
>6 dna:primary_assembly primary_assembly:mRatBN7.2:6:1:140994061:1 REF
>7 dna:primary_assembly primary_assembly:mRatBN7.2:7:1:135012528:1 REF
>8 dna:primary_assembly primary_assembly:mRatBN7.2:8:1:123900184:1 REF
>9 dna:primary_assembly primary_assembly:mRatBN7.2:9:1:114175309:1 REF
>10 dna:primary_assembly primary_assembly:mRatBN7.2:10:1:107211142:1 REF
>11 dna:primary_assembly primary_assembly:mRatBN7.2:11:1:86241447:1 REF
>12 dna:primary_assembly primary_assembly:mRatBN7.2:12:1:46669029:1 REF
>13 dna:primary_assembly primary_assembly:mRatBN7.2:13:1:106807694:1 REF
>14 dna:primary_assembly primary_assembly:mRatBN7.2:14:1:104886043:1 REF
>15 dna:primary_assembly primary_assembly:mRatBN7.2:15:1:101769107:1 REF
>16 dna:primary_assembly primary_assembly:mRatBN7.2:16:1:84729064:1 REF
>17 dna:primary_assembly primary_assembly:mRatBN7.2:17:1:86533673:1 REF
>18 dna:primary_assembly primary_assembly:mRatBN7.2:18:1:83828827:1 REF
>19 dna:primary_assembly primary_assembly:mRatBN7.2:19:1:57337602:1 REF
>20 dna:primary_assembly primary_assembly:mRatBN7.2:20:1:54435887:1 REF
>X dna:primary_assembly primary_assembly:mRatBN7.2:X:1:152453651:1 REF
>Y dna:primary_assembly primary_assembly:mRatBN7.2:Y:1:18315841:1 REF
>MT dna:primary_assembly primary_assembly:mRatBN7.2:MT:1:16313:1 REF
>MU150191.1 dna:primary_assembly primary_assembly:mRatBN7.2:MU150191.1:1:1794995:1 REF
```

```
# 首先将之前的名称更改一下
mv R7.2.fa R7.2.raw.fa
mv（英文全拼：move file）命令用来为文件或目录改名、或将文件或目录移入其它位置

# 然后去除染色体编号后的描述信息
$ cat R7.2.raw.fa | perl -n -e 'if(m/^>(.+?)(?:\s|$)/){ print ">$1\n";}else{print}' > R7.2.fa
#以 ">" 开头，表示是一个序列标题行。使用正则表达式 m/^>(.+?)(?:\s|$)/ 匹配标题，并提取出匹配结果中的第一个捕获组（即标题的部分）；m多行匹配，如果匹配到了以>开头多个字母空格或者$，"$1" 是匹配到的标题部分，添加 ">" 后并换行输出。否则，即为序列内容行，直接使用 print 命令将其输出。

# 删除
$ rm R7.2.raw.fa
```

可以使用脚本统计每一条染色体的长度
```
$ cat R7.2.fa | perl -n -e '#-n 参数告诉 perl 在每一行上执行给定的代码。-e 参数指定了要执行的代码块
     #除去末尾的回车符、换行符
     s/\r?\n//;
     # 得到序列的名称,
    if(m/^>(.+?)\s*$/){
       $title = $1;
        push @t, $title;#如果当前行以 ">" 开头，表示是一个序列标题行，你提取出标题，并将它添加到 @t 数组中。
    }elsif(defined $title){
     # 否则，如果 $title 已经定义（即前面已经遇到了序列标题），则计算当前行的长度，并将其加到相应的标题长度中 ($title_len)。
        $title_len{$title} += length($_); 
    }
     # 最后打印出信息来
    END{
        for my $title (@t){
            print "$title","\t","$title_len{$title}","\n";
        }在 END 块中，遍历 @t 数组中的标题，并打印出每个标题和其对应的长度。
    }
'

```

结果
```
1       260522016
2       249053267
3       169034231
4       182687754
5       166875058
6       140994061
7       135012528
8       123900184
9       114175309
10      107211142
11      86241447
12      46669029
13      106807694
14      104886043
15      101769107
16      84729064
17      86533673
18      83828827
19      57337602
20      54435887
X       152453651
Y       18315841
MT      16313
MU150191.1      1794995
MU150189.1      1402623
MU150194.1      648519
MU150190.1      573231
MU150195.1      529129
JACYVU010000493.1       444596
MU150193.1      383091
```

这里为了方便演示，直接用1号染色体的基因组作为后续比对。
```
cat R7.2.fa | perl -n -e '
  if(m/^>/){
    if(m/>1$/){
      $title = 1; # title=1，即一号染色体
    }else{
      $title = 0;
    }
  }else{
    push @s, $_ if $title;
  }
  END{
    printf ">1\n%s", join("", @s);
  }
' > R7.2.chr1.fa
```

- 下载基因组索引文件(可选)
  
基因组索引文件就像一本书的目录，里面集成了比对软件的算法，所以虽然基因组序列是一样的，但不同的比对软件需要不同的基因组索引文件。

在[hisat2](http://daehwankimlab.github.io/hisat2/) 官网上可以找到现成的已经建立好索引的大鼠基因组文件 （在```download```里面找），如果电脑配置一般建议直接下载好索引文件，可以直接下载这个索引文件（因为建立索引文件时间较长1个小时以上），这个索引文件是可以自己用命令基于之前下载的基因组文件自行建立的。

方法1：

在```hisat2```官网上可以找到现成的已经建立好索引的大鼠基因组文件, 点击 https://genome-idx.s3.amazonaws.com/hisat/rn6_genome.tar.gz

方法2：通过代码下载

```
cd /mnt/c/shengxin/project/rat/genome
wget https://genome-idx.s3.amazonaws.com/hisat/rn6_genome.tar.gz
gzip -d rn6_genome.tar.gz
```

- 下载注释信息(gff3 格式)

GFF (general feature format): 可以用于任何基因组注释的存储

GTF (gene transfer format): 严格的用于基因注释信息的存储

[gff/gtf各列代表含义](https://www.jianshu.com/p/c284a6b4e1c6)
```
cd /mnt/c/shengxin/project/rat/annotation
wget https://ftp.ensembl.org/pub/release-109/gff3/rattus_norvegicus/Rattus_norvegicus.mRatBN7.2.109.gff3.gz
gzip -d Rattus_norvegicus.mRatBN7.2.109.gff3.gz

# 同样的也改名
mv Rattus_norvegicus.mRatBN7.2.109.gff3 R7.2.gff

# 使用head查看部分
head R7.2.gff
```

R7.2.gff查看结果
```
##gff-version 3
##sequence-region   1 1 260522016
##sequence-region   10 1 107211142
##sequence-region   11 1 86241447
##sequence-region   12 1 46669029
##sequence-region   13 1 106807694
##sequence-region   14 1 104886043
##sequence-region   15 1 101769107
##sequence-region   16 1 84729064
##sequence-region   17 1 86533673
```

- 下载 gtf 格式
```  
cd /mnt/c/shengxin/project/rat/annotation
wget https://ftp.ensembl.org/pub/release-109/gtf/rattus_norvegicus/Rattus_norvegicus.mRatBN7.2.109.gtf.gz
gzip -d Rattus_norvegicus.mRatBN7.2.109.gtf.gz

# 同样的也改名
mv Rattus_norvegicus.mRatBN7.2.109.gtf R7.2.gtf

# 使用head查看部分
head R7.2.gtf
```

- 注释数据说明

注释gff文件的样例：
```
#!genome-build mRatBN7.2
#!genome-version mRatBN7.2
#!genome-date 2020-11
#!genome-build-accession GCA_015227675.2
#!genebuild-last-updated 2021-02
1       ensembl gene    36112690        36122387        .       -       .       gene_id "ENSRNOG00000066169"; gene_version "1"; gene_source "ensembl"; gene_biotype "protein_coding";
1       ensembl transcript      36112690        36122387        .       -       .       gene_id "ENSRNOG00000066169"; gene_version "1"; transcript_id "ENSRNOT00000101581"; transcript_version "1"; gene_source "ensembl"; gene_biotype "protein_coding"; transcript_source "ensembl"; transcript_biotype "protein_coding"; tag "Ensembl_canonical";
1       ensembl exon    36122324        36122387        .       -       .       gene_id "ENSRNOG00000066169"; gene_version "1"; transcript_id "ENSRNOT00000101581"; transcript_version "1"; exon_number "1"; gene_source "ensembl"; gene_biotype "protein_coding"; transcript_source "ensembl"; transcript_biotype "protein_coding"; exon_id "ENSRNOE00000618632"; exon_version "1"; tag "Ensembl_canonical";
1       ensembl CDS     36122324        36122387        .       -       0       gene_id "ENSRNOG00000066169"; gene_version "1"; transcript_id "ENSRNOT00000101581"; transcript_version "1"; exon_number "1"; gene_source "ensembl"; gene_biotype "protein_coding"; transcript_source "ensembl"; transcript_biotype "protein_coding"; protein_id "ENSRNOP00000083062"; protein_version "1"; tag "Ensembl_canonical";
1       ensembl exon    36121478        36121512        .       -       .       gene_id "ENSRNOG00000066169"; gene_version "1"; transcript_id "ENSRNOT00000101581"; transcript_version "1"; exon_number "2"; gene_source "ensembl"; gene_biotype "protein_coding"; transcript_source "ensembl"; transcript_biotype "protein_coding"; exon_id "ENSRNOE00000610554"; exon_version "1"; tag "Ensembl_canonical";
```
gtf文件开头描述了这个注释数据的基本信息，比如版本号，更新时间，组装的NCBI的Assembly编号等等，后面每一行表示描述信息，说明了在哪条染色体的什么位置是什么东西。比如第6行的表示在1号染色体负链36112690-36122387范围上有一个基因编号为ENSRNOG00000066169的基因

## 3.2 测试数据（实验数据）
从NCBI上查找相关的RNA-seq数据进行下载，在GEO数据库中找了一个数据```GSE72960```，对应的SRP数据为```SRP063345```

SRA和GEO (Gene Expression Omnibus database) 都是NCBI里面的，SRA原始数据GEO经过一定处理的数据

GEO-收录了世界各国研究机构提交的高通量基因表达数据，也就是说只要是目前已经发表的论文，论文中涉及到的基因表达检测的数据都可以通过这个数据库中找到。

通过[NCBI - GEO](https://www.ncbi.nlm.nih.gov/geo/)进入，搜索框中输入```GSE72960```（一般在文献中可查到）

之后在下方出现了这个基因表达数据集的描述信息，比如样本提交日期，样本文献来源，数据提交人的信息，样本测序样本数量，对应的编号等等

我们直接点击最下面的SRA Run selector这个里面包含了这8个测序样本的测序信息以及文件SRA编号，通过这个编号就可以下载测序数据。

将刚才在Run selector中查找到的数据的编号复制下来，之后下载测序数据，下载脚本如下，这里是采用SRAtoolkit工具包中的prefetch工具，如果部分数据下载失败，那么再次执行下面的代码
```
cd /mnt/c/shengxin/project/rat/sra
# 后台下载
$ nohup prefetch SRR2190795 SRR224018{2..7} SRR2240228 -o . &
# nohup 英文全称 no hang up（不挂起），用于在系统后台不挂断地运行命令，退出终端不会影响程序的运行。
# -o . & 下载完输出到当前目录，& 一般没有运行完、输入空格会卡住，只有运行完才会再出现主机名称；但是加上&后可以放在后台下载运行
#prefetch 批量下载数据

```
下载完成之后并不是之前说的.fastq.gz格式的文件，而是.sra文件，这里进行格式转换，这里还是使用SRAtoolkit工具包，但是是里面的fastq-dump工具，使用它来进行格式转化
```
# 将sra文件转化为fastq文件之后压缩为gz文件

# --gzip 把生成的fastq文件压缩为gz格式，节省内存
parallel -j 4 "
    fastq-dump --split-3 --gzip {1}#fastq-dump从SRA文件中提取fastq文件.{1} 表示占位符，会被后面的文件名替代。
" ::: $(ls *.sra)#::: 后面是要遍历的参数集合

# 删除sra文件
rm *.sra
```

fastq格式介绍
```
  cd /mnt/c/shengxin/project/rat/sequence
  gzip -d -c SRR2190795.fastq.gz | head -n 20
@SRR2190795.1 HWI-ST1147:240:C5NY7ACXX:1:1101:1320:2244 length=100
ATGCTGGGGGCATTAGCATTGGGTACTGAATTATTTTCAGTAAGAGGGAAAGAATCCATCTCCNNNNNNNNNNNNNNNNNNNNNNAAANAAAAATAAAAT
+SRR2190795.1 HWI-ST1147:240:C5NY7ACXX:1:1101:1320:2244 length=100
CCCFFFFFHHHHHJIJJJJJJJJDHHJJJIJJJJJIJJJJJJJJJJJJJJJJJJJJJJJJJHH#####################################
@SRR2190795.2 HWI-ST1147:240:C5NY7ACXX:1:1101:1598:2247 length=100
AACTTCGGTTCTCTACTAGGAGTATGCCTCATAGTACAAATCCTCACAGGCTTATTCCTAGCANNNNNNNNNNNNNNNNNNNNNNTAACAGCATTTTCAT
+SRR2190795.2 HWI-ST1147:240:C5NY7ACXX:1:1101:1598:2247 length=100
@@@7D8+@A:1CFG<C:23<:E<;FF<BHIIEHG:?:??CDF<9DCGGG?1?FEG@@<@CA#######################################
@SRR2190795.3 HWI-ST1147:240:C5NY7ACXX:1:1101:1641:2250 length=100
AGAAGGTCTTAGATCAGAAGGAGCACAGACTGGATGGTCGTGTCATTGACCCTAAAAAGGCTANNNNNNNNNNNNNNNNNNNNNTGAAGAAAATCTTTGT
+SRR2190795.3 HWI-ST1147:240:C5NY7ACXX:1:1101:1641:2250 length=100
BC@FFFDDHHHHHJJJJJJJJJJJJJJJJJJJJIJJJFHGHHEGHIIIHJIJJIJJIJIJJID#####################################
@SRR2190795.4 HWI-ST1147:240:C5NY7ACXX:1:1101:1851:2233 length=100
GGGATTTCATGGCCTCCACGTAATTATTGGCTCAACTTTCCTAATTGTCTGTCTACTACGACANNNNNNNNNNNNNNNNNNNNNNNNNNNNNNTNNCNNN
+SRR2190795.4 HWI-ST1147:240:C5NY7ACXX:1:1101:1851:2233 length=100
@@?DDBDDFFDDDGHGGGGI?B;FFHGHA@FEHGHDDGHEGGFGHIGEHIIHIGGBGACD6AH#####################################
@SRR2190795.5 HWI-ST1147:240:C5NY7ACXX:1:1101:1957:2243 length=100
CAGCCATTGTGGCTCCCGATGGCTTTGACATCATTGACATGACAGCCGGAGGTCAGATAAACTNNNNNNNNNNNNNNNNNNNNNNATCNGTGGCAAAGGT
+SRR2190795.5 HWI-ST1147:240:C5NY7ACXX:1:1101:1957:2243 length=100
@CCFFFFFHHHHAHJJJIJJJJJJIJJIGGGIFIJIIHIIGGJJJJJJJFHIGIJHHHHHHFC#####################################
```
# 4. 质量控制
## 4.1 质量评估
拿到测序数据文件，在序列比对之前需要对测序文件的测序质量进行查看，因为不同测序数据来源测序质量也不一样，为了保证后续分析的有效性和可靠性，需要对质量进行评估，如果数据很差那么在后续分析的时候就需要注意了。这里使用fastqc进行质量评估
```
用法
fastqc [选项] [测序文件]

实际使用
cd /mnt/c/shengxin/project/rat/sequence
# 因为程序不会自动新建目录，这里新建一个目录
mkdir -p ../output/fastqc

# -t 指定线程数
# -o 指定输出文件夹
# *.gz 表示这个目录下以 .gz 的所有文件
fastqc -t 6 -o ../output/fastqc *.gz
```
运行过程中会出现分析的进程，在分析完成之后会有分析报告生成。
```
cd /mnt/c/shengxin/project/rat/output/fastqc
ls
SRR2190795_fastqc.html  SRR2240182_fastqc.zip   SRR2240184_fastqc.html  SRR2240185_fastqc.zip   SRR2240187_fastqc.html  SRR2240228_fastqc.zip
SRR2190795_fastqc.zip   SRR2240183_fastqc.html  SRR2240184_fastqc.zip   SRR2240186_fastqc.html  SRR2240187_fastqc.zip
SRR2240182_fastqc.html  SRR2240183_fastqc.zip   SRR2240185_fastqc.html  SRR2240186_fastqc.zip   SRR2240228_fastqc.html
```
这里的.html用浏览器打开，查看一下情况，

可以看到这个测序质量不是特别好，

有关fastq的报告解读，这里有一篇文章写的可以用FastQC检查二代测序原始数据的质量[https://www.plob.org/article/5987.html]

绿色表示通过，红色表示未通过，黄色表示不太好。一般而言RNA-Seq数据在sequence deplication levels 未通过是比较正常的。毕竟一个基因会大量表达，会测到很多遍

这里因为有多份报告，有时候查看不是特别方便，这里有一个将所有的```fastqc```的检测报告合并到一个文件上的程序```multiqc```
```
cd /mnt/c/shengxin/project/rat/output/fastqc

multiqc .
```
进程
```
  /// MultiQC 🔍 | v1.14

|           multiqc | Search path : /mnt/c/shengxin/project/rat/output/fastqc
|         searching | ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 100% 16/16
|            fastqc | Found 8 reports
|           multiqc | Compressing plot data
|           multiqc | Report      : multiqc_report.html
|           multiqc | Data        : multiqc_data
|           multiqc | MultiQC complete
```
## 4.2 剔除接头以及测序质量差的碱基

上面看到，在接头那里是显示的通过，但是可以看到有部分是有4个碱基与接头序列匹配的，属于Illumina的通用接头。另外也可以看到，除了可能存在接头的情况，在测序质量那里也可以看到在5'端存在低质量的测序区域，所以像两端这种低质量的区域也是要去除的的，这一步采用trimmomatic进行。

```
cd /mnt/c/shengxin/project/rat/sequence
# 新建文件夹
mkdir -p ../output/adapter/

# 循环处理文件夹下的
for i in $(ls *.fastq.gz);
do
    cutadapt -a AATGATACGGCGACCACCGAGATCTACACTCTTTCCCTACACGACGCTCTTCCGATCT \
    --minimum-length 30 --overlap 4 --trim-n \
    -o ../output/adapter/${i}  ${i}
done

    #for in 循环结构
    # --minimum-length 如果剔除接头后read长度低于30，这条read将会被丢弃
    # --overlap        如果两端的序列与接头有4个碱基的匹配将会被剔除
    # --trim-n         剔除两端的N
    # -a 去除3端引物序列
    #AATGATACGGCGACCACCGAGATCTACACTCTTTCCCTACACGACGCTCTTCCGATCT是Illumina的小RNA测序adapter（Illumina Paried End PCR Primer 1）
```
## 4.4 再次去除低质量区域
```
cd /mnt/c/shengxin/project/rat/output/adapter/
mkdir ../trim

$ parallel -j 4 "
  # LEADING:20，从序列的开头开始去掉质量值小于 20 的碱基
  # TRAILING:20，从序列的末尾开始去掉质量值小于 20 的碱基
  # SLIDINGWINDOW:5:15，从 5' 端开始以 5bp 的窗口计算碱基平均质量，如果此平均值低于 15，则从这个位置截断read
  # Trimmomatic-0.38我下载到了别的盘内，记得更改路径
  # MINLEN:36， 如果 reads 长度小于 30bp 则扔掉整条 read。
  java -jar mnt/c/shengxin/biosoft/Trimmomatic-0.38/trimmomatic-0.38.jar \
    SE -phred33 {1} ../trim/{1} \
    LEADING:20 TRAILING:20 SLIDINGWINDOW:5:15 MINLEN:30 \
" ::: $( ls *.gz)

[Trimmomatic参数说明](http://www.chenlianfu.com/?p=1948)

# 本命令说明
#-jar 执行封装在 JAR 存档中的程序
java -jar  [Trimmomatic软件存储位置]\   
   [单端测序] SE -phred33 {变量名称}  [指明存储路径和名称] ../trim/{变量名称}\
   Trimmomatic软件的命令选项  
```
结果： # Trimmomatic软件处理转入后台，不会显示实时步骤，耐心等待
```
TrimmomaticSE: Started with arguments:
 -phred33 SRR2190795.fastq.gz ../trim/SRR2190795.fastq.gz LEADING:20 TRAILING:20 SLIDINGWINDOW:5:15 MINLEN:30
Automatically using 1 threads
Input Reads: 15105698 Surviving: 14998487 (99.29%) Dropped: 107211 (0.71%)
TrimmomaticSE: Completed successfully
TrimmomaticSE: Started with arguments:
 -phred33 SRR2240183.fastq.gz ../trim/SRR2240183.fastq.gz LEADING:20 TRAILING:20 SLIDINGWINDOW:5:15 MINLEN:30
Automatically using 1 threads
Input Reads: 17621681 Surviving: 17484940 (99.22%) Dropped: 136741 (0.78%)
TrimmomaticSE: Completed successfully
TrimmomaticSE: Started with arguments:
 -phred33 SRR2240184.fastq.gz ../trim/SRR2240184.fastq.gz LEADING:20 TRAILING:20 SLIDINGWINDOW:5:15 MINLEN:30
Automatically using 1 threads
Input Reads: 19777686 Surviving: 19615933 (99.18%) Dropped: 161753 (0.82%)
TrimmomaticSE: Completed successfully
出现这个表示运行结束
```
## 4.3 再次查看质量情况
```
$ cd /mnt/c/shengxin/project/rat/output/trim
#挂载到存储gz文件的文件夹下
$ mkdir ../fastqc_trim
$ parallel -j 4 "
    fastqc -t 4 -o ../fastqc_trim {1}
" ::: $(ls *.gz)

$ cd ../fastqc_trim
$ multiqc .
```
# 5. 去除rRNA序列[可不做]
如果在提取RNA过程中没有对RNA进行筛选的情况下，那么得到的大部分将会是rRNA，这个对于后续的分析可能会存在影响，另外也会让比对的时间变长。

注意：在使用sortmerna的时候需要确保测序文件是未压缩的文件

没做（）
# 6. 序列比对
得到干净的测序数据之后，接就可以进行序列比对了，比对的过程是一种寻找的过程，将read定位到它位于基因组的位置，通过找寻之后才能说这条read是属于哪条基因的，这样才能对基因的表达进行定量。另外```RNA-seq```的序列与基因组的序列有时候会不一样，因为存在内含子与外显子这种序列的差别，而```RNA-seq```是测的RNA的序列，所以会出现跨范围的序列的比对

## 6.1 建立索引
这一步使用```hisat2```中的工具```hisat2-build```建立索引。

- 用法

```hisat2-build [选项] [基因组序列(.fa)] [索引文件的前缀名]```
- 开始使用
```
$ cd /mnt/c/shengxin/project/rat/genome
$ mkdir index
$ cd index

$ hisat2-build  -p 6 ../R7.2.chr1.fa R7.2.chr1
师兄只用一号染色体做了比对，发现结果不太对，所以接下来以全基因组做比对
hisat2-build  -p 6 ../R7.2.fa R7.2
#p 运行线程数
```
在运行过程中会有部分信息提示，其中说到建立索引文件的分块情况以及运行时间的统计

索引建立完成之后在```c/mnt/c/shengxin/project/rat/genome```文件夹下会出现

结果:
```
ls
R7.2.1.ht2  R7.2.2.ht2  R7.2.3.ht2  R7.2.4.ht2  R7.2.5.ht2  R7.2.6.ht2  R7.2.7.ht2  R7.2.8.ht2

```
8个文件，这些文件是对基因组进行压缩之后的文件，这个将基因组序列数据分块成了8份，在执行序列比对的时候直接使用这些文件而不是基因组R7.2.fa文件
# 6.2 开始比对
这里使用hasat2进行比对
```
用法
hisat2 [选项] -x [索引文件] [ -1 1测序文件 -2 2测序文件 -U 未成对测序文件 ] [ -S 输出的sam文件 ]
实际使用
cd /mnt/c/shengxin/project/rat/output

mkdir align
cd trim  # 没有经过rRNA的去除，所以和师兄的不一样

parallel -k -j 4 "    #-k --keep-order 强制使输出与参数保持顺序；-j 并行任务数
    hisat2 -t -x ../../genome/index/R7.2 \    # -t 参数表示输出基于文本的 SAM 格式;-x 定位到索引文件
      -U {1} -S ../align/{1}.sam \#-U {1}.fq.gz 参数是要比对的输入文件，其中 {1} 是从参数中获取的文件名（不带扩展名）'-S ../align/{1}.sam 参数指定输出的 SAM 文件名，格式为 "../align/文件名.sam"
      # -U <r>：单端测序list，若为list，使用逗号隔开，-U lane1.fq,lane2.fq,lane3.fq,lane4.fq
      # -S <hit> ：SAM写入的文件名，默认写入到标准输出中

      2>../align/{1}.log      #将标准错误输出重定向到 "../align/文件名.log" 文件中
" ::: $(ls *.gz | perl -p -e 's/.fq.gz$//')    # perl 语句起到批量替换作用，得到不带扩展名的文件名
#:::将 perl 脚本输出的结果作为参数传递给 parallel 命令

运行中出现hisat2做比对时出现(ERR): hisat2-align exited with value 137 Killed
原因：内存不够，将运行线程改成1
```
比对完成之后可以进入文件夹查看一下日志信息
```
cd /mnt/c/shengxin/project/rat/output/align

cat SRR2190795.fastq.gz.log

Time loading forward index: 00:00:44
Time loading reference: 00:00:01
Multiseed full-index search: 00:07:27
14998487 reads; of these:
  14998487 (100.00%) were unpaired; of these:
    1198616 (7.99%) aligned 0 times
    13113773 (87.43%) aligned exactly 1 time
    686098 (4.57%) aligned >1 times
92.01% overall alignment rate
Time searching: 00:07:29
Overall time: 00:08:13

```

- 总结比对情况
  
自己写一个脚本将序列比对率和时间进行统计
```
cd /mnt/c/shengxin/project/rat/output/align
file_list=($(ls *.log))

echo -e "sample\tratio\ttime"# 输出表头，包括 "sample"、"ratio" 和 "time" 三列
for i in ${file_list[@]};
do
    prefix=$(echo ${i} | perl -p -e 's/\.log//')# 提取出每个文件的前缀（去掉 ".log" 后缀）
    echo -n -e "${prefix}\t"#输出前缀，并使用 -n 参数使得下一条命令输出不换行。输出格式为前缀加一个制表符。
    cat ${i} |
      grep -E "(overall alignment rate)|(Overall time)" |#-E将样式为延伸的正则表达式来使用
      #用 grep 进行正则匹配，只保留包含 "overall alignment rate" 或 "Overall time" 的行
      perl -n -e '
        if(m/alignment/){
          $hash{precent} = $1 if m/([\d.]+)%/;#如果当前行包含 "alignment"，则使用正则表达式 m/([\d.]+)%/ 匹配出百分比数值，并将其存入哈希表中的 "precent" 键
        }elsif(m/time/){
          if(m/(\d\d):(\d\d):(\d\d)/){
            my $time = $1 * 60 + $2 + $3 / 60;
            $hash{time} = $time;#如果当前行包含 "time"，则使用正则表达式 m/(\d\d):(\d\d):(\d\d)/ 提取出时间值，并将其转换为分钟数，存入哈希表中的 "time" 键
          }
        }
        END{
          $hash{precent} = "NA" if not exists $hash{precent};
          $hash{time} = "NA" if not exists $hash{time};#在 END 块中判断若哈希表中不存在 "precent" 或 "time" 键，则将相应的值设为 "NA
          printf "%.2f\t%.2f\n", $hash{precent}, $hash{time};#printf 是指格式化输出函数，主要功能是向标准输出设备按规定格式输出信息;按照 "%.2f\t%.2f" 的格式输出。这里的 "%.2f" 表示浮点数保留两位小数。
        }
      '
done
```
- 格式转化与排序

SAM格式是目前用来存放大量核酸比对结果信息的通用格式，也是人类能够“直接”阅读的格式类型，而BAM和CRAM是为了方便传输，降低存储压力将SAM进行压缩得到的格式形式。

```
$ cd /mnt/c/shengxin/project/rat/output/align
$ parallel -k -j 4 "
    samtools sort -@ 4 {1}.sam > {1}.sort.bam     
    # 将文件夹内sam文件全部转换为其二进制bam文件以减少内存,并按照 read name 排序
    #-@ 4表示使用4个线程来加快处理速度
    
    
    # samtools index对排序好的bam文件，可以通过以下命令进行index
    （注意只能对排序过的文件进行index），默认在当前文件夹产生*.bai的index文件
    samtools index {1}.sort.bam

" ::: $(ls *.sam | perl -p -e 's/\.sam$//')
# 该步产生 sort.bam 和 .sort.bam.bai 格式的文件
补充
1. samtools sort [options] input.bam  

options:
-n : 根据read的name进行排序，默认对最左侧坐标进行排序
-o : 设置排序后输出文件的文件名
-O : 后跟sam或bam，规定排序后输出文件的格式，默认是bam
-@ : 后跟正整数，指定分析所用线程数
-m : 后跟数字 + K/M/G，指定每个线程所使用内存量  

$ rm *.sam

$ ls
$ rm *.sam
                                                      
$ ls
samtools.756.3746.tmp.0002.bam  samtools.786.3758.tmp.0003.bam    SRR2240182.fastq.gz.log           SRR2240184.fastq.gz.sort.bam.bai  SRR2240187.fastq.gz.sort.bam
samtools.756.3746.tmp.0003.bam  samtools.865.3236.tmp.0000.bam    SRR2240182.fastq.gz.sort.bam      SRR2240185.fastq.gz.log           SRR2240187.fastq.gz.sort.bam.bai
samtools.757.3459.tmp.0002.bam  samtools.865.3236.tmp.0001.bam    SRR2240182.fastq.gz.sort.bam.bai  SRR2240185.fastq.gz.sort.bam      SRR2240228.fastq.gz.log
samtools.757.3459.tmp.0003.bam  samtools.866.1878.tmp.0000.bam    SRR2240183.fastq.gz.log           SRR2240185.fastq.gz.sort.bam.bai  SRR2240228.fastq.gz.sort.bam
samtools.779.2110.tmp.0000.bam  samtools.866.1878.tmp.0001.bam    SRR2240183.fastq.gz.sort.bam      SRR2240186.fastq.gz.log           SRR2240228.fastq.gz.sort.bam.bai
samtools.779.2110.tmp.0002.bam  SRR2190795.fastq.gz.log           SRR2240183.fastq.gz.sort.bam.bai  SRR2240186.fastq.gz.sort.bam
samtools.779.2110.tmp.0003.bam  SRR2190795.fastq.gz.sort.bam      SRR2240184.fastq.gz.log           SRR2240186.fastq.gz.sort.bam.bai
samtools.786.3758.tmp.0002.bam  SRR2190795.fastq.gz.sort.bam.bai  SRR2240184.fastq.gz.sort.bam      SRR2240187.fastq.gz.log
```
# 7. 表达量统计
使用HTSEQ-count - htseq的使用方法和计算原理[https://htseq.readthedocs.io/en/master/count.html#]

如何判断一个 reads 属于某个基因， htseq-count 提供了 union, intersection_strict,intersection_nonempty 3 种模型，如图（大多数情况下作者推荐用 union 模型），它描述了在多种情况下，比对到基因组上的read分配的问题，在这些问题中，最难分配的就是一条read在两个基因相交的地方比对上了之后的情况。一般情况下作者推荐使用union的方式。
```                       
用法
htseq-count [options] <alignment_files> <gff_file  >
```
参数说明    
| 参数 | 说明 | 
| :----:| :----:| 
|-f --format	 | default: sam 设置输入文件的格式，该参数的值可以是sam或bam。 | 
|-r --order | default: name 设置sam或bam文件的排序方式，该参数的值可以是name或pos。前者表示按read名进行排序，后者表示按比对的参考基因组位置进行排序。若测序数据是双末端测序，当输入sam/bam文件是按pos方式排序的时候，两端reads的比对结果在sam/bam文件中一般不是紧邻的两行，程序会将reads对的第一个比对结果放入内存，直到读取到另一端read的比对结果。因此，选择pos可能会导致程序使用较多的内存，它也适合于未排序的sam/bam文件。而pos排序则表示程序认为双末端测序的reads比对结果在紧邻的两行上，也适合于单端测序的比对结果。很多其它表达量分析软件要求输入的sam/bam文件是按pos排序的，但HTSeq推荐使用name排序，且一般比对软件的默认输出结果也是按name进行排序的。 | 
| -s --stranded | 	default: yes 设置是否是链特异性测序。该参数的值可以是yes,no或reverse。no表示非链特异性测序；若是单端测序，yes表示read比对到了基因的正义链上；若是双末端测序，yes表示read1比对到了基因正义链上，read2比对到基因负义链上；reverse表示双末端测序情况下与yes值相反的结果。根据说明文件的理解，一般情况下双末端链特异性测序，该参数的值应该选择reverse（本人暂时没有测试该参数）。 | 
|-a --a	 |default: 10 忽略比对质量低于此值的比对结果。在0.5.4版本以前该参数默认值是0。 |
| -t --type |	default: exon 程序会对该指定的feature（gtf/gff文件第三列）进行表达量计算，而gtf/gff文件中其它的feature都会被忽略。 |
|-i --idattr| default: gene_id 设置feature ID是由gtf/gff文件第9列那个标签决定的；若gtf/gff文件多行具有相同的feature ID，则它们来自同一个feature，程序会计算这些features的表达量之和赋给相应的feature ID。|
| -m --mode	| default: union 设置表达量计算模式。该参数的值可以有union, intersection-strict and intersection-nonempty。这三种模式的选择请见上面对这3种模式的示意图。从图中可知，对于原核生物，推荐使用intersection-strict模式；对于真核生物，推荐使用union模式。|
| -o --samout	 | 输出一个sam文件，该sam文件的比对结果中多了一个XF标签，表示该read比对到了某个feature上。 |
| -q --quiet | 不输出程序运行的状态信息和警告信息。|
| -h --help	|输出帮助信息。 |	

```
cd /mnt/c/shengxin/project/rat/output
mkdir HTseq

cd align
parallel -j 1 "
    htseq-count -s no -f bam {1}.sort.bam ../../annotation/R7.2.gtf \#-s no表示非链特异性测序;-f bam bam格式；
      >../HTseq/{1}.count  2>../HTseq/{1}.log
" ::: $(ls *.fastq.gz.sort.bam | perl -p -e 's/\.sort\.bam$//')
```

查看生成的文件
```
cd /mnt/c/shengxin/project/rat/output/HTseq
cat SRR2190795.fastq.gz.count | head -n 10
```
结果（第一列：基因的ID，第二列：read计数）
```
ENSRNOG00000000001      2
ENSRNOG00000000007      3
ENSRNOG00000000008      0
ENSRNOG00000000009      0
ENSRNOG00000000010      0
ENSRNOG00000000012      0
ENSRNOG00000000017      10
ENSRNOG00000000021      0
ENSRNOG00000000024      842
ENSRNOG00000000033      28
```

# 8. 合并表达矩阵与标准化
## 8.1 合并
这里就是将下面的这种表合并为一张表，作为一个整体输入到后续分析的程序中
```
      样本1 |        样本2 |       样本3
基因1   x   | 基因1    x   | 基因1   x
基因2   x   | 基因2    x   | 基因2   x
基因3   x   | 基因3    x   | 基因3   x
基因4   x   | 基因4    x   | 基因4   x
```
合并为
```
      样本1   样本2  样本3
基因1   x      x      x
基因2   x      x      x
基因3   x      x      x
基因4   x      x      x
```
下面使用R语言中的merge将表格合并
```
rm(list=ls())#清除R环境中的所有对象，确保开始时没有任何变量和数据
setwd("~/project/rat/output/HTseq")#设置当前工作目录

# 得到文件样本编号
files <- list.files(".", "*.count")#获取当前工作目录下所有以 ".count" 结尾的文件
f_lists <- list() #创建一个空的列表 f_lists 用于存储文件对应的前缀和文件名
for(i in files){
    prefix = gsub("(_\\w+)?\\.count", "", i, perl=TRUE)#gsub （"替换对象”，“替换成什么内容”，哪一列） gsub与后面的括号之间不能有空格
#从文件名中提取出前缀部分（去掉 ".count" 后缀）。将提取的前缀赋值给变量 prefix
    f_lists[[prefix]] = i#将文件名（包括扩展名）存储在 f_lists 列表中，以前缀为索引
}


id_list <- names(f_lists)#获取 f_lists 列表的索引（前缀），将其存储在 id_list 中
data <- list()#创建一个空的列表 data 用于存储读取的文件内容
count <- 0#初始化计数器 count 为0，用于记录当前处理的文件数量
for(i in id_list){
  count <- count + 1#计数器 count 加1，表示开始处理一个新的文件
  a <- read.table(f_lists[[i]], sep="\t", col.names = c("gene_id",i))#从文件 f_lists[[i]] 中读取数据，使用制表符"\t"作为分隔符，并指定列名为 "gene_id" 和变量 i。
  data[[count]] <- a#将读取的数据存储在 data 列表中，列表索引为 count
}

# 合并文件
data_merge <- data[[1]]#将第一个文件的数据赋值给 data_merge，作为后续合并的基础
for(i in seq(2, length(id_list))){#从第二个文件开始，遍历剩余的文件
    data_merge <- merge(data_merge, data[[i]],by="gene_id")
}
#使用 merge 函数逐个将文件的数据按照 "gene_id" 列进行合并，将结果赋值给 data_merge
write.csv(data_merge, "merge.csv", quote = FALSE, row.names = FALSE)#quote参数设为FALSE表示不对数据进行引号包围，row.names参数设为FALSE表示不写入行名。
#最后会得到一个统计好的excel表格
```
# 8.2 数据标准化
## 8.2.1 简介
表达量是个什么？
在一个细胞中，如果统计某一个基因的表达得到的所有的RNA，这个数量就是绝对的数量，就是实际上有多少。但是在RNA-seq中，并不知道被用于测序的组织块有多少个细胞，另外提取RNA的过程中损失了多少，那么最后我们通过这个read count以及标准化之后得到的并不是真实的具体的数量，这个数量是相对定量，就是这个数值单独拿出来没有什么意义，但是在数据相互比较中才显示出意义来。

read count与相对表达量
得到的原始read count并不能体现出基因与基因之间的相对的表达量的关系。比如经过HTseq-count之后得到的那些数值，这个数值就是说落在基因区域内的read的数量。但是如上图所示，不同的基因的长度不同，那么对应的read比对到的区域的大小不同，基因之间的长度不同这就带来了直接比落在基因上的read数量来说明表达量就是不公平的这种情况 Sample（就好比直接比两个人的体重来判定胖瘦一样，比如一个100斤的6-7岁的小胖子和110斤的成年人一样，不考虑身高因素这种关键是没有意义的），所以需要根据基因的长度来对原始的read count进行转化之后才能公平。这里的标准化是属于样本内的各个基因之间的表达量的标准化。

但是后续只是为了分析基因的差异表达，所以在对测序深度进行标准化之后就可以直接对不同样本同一个基因之间的read count数进行比较，因为并不涉及到一个样本内不同基因的对比。与上面的样本内的不同，这个是属于样本间的标准化，因为不同RNA-seq的测序深度可能是有差别的。

```            
样本间相同基因的对比（TMM分位数标准化或者深度标准化，或者还是CPM、RPKM、FPKM、TPM标准化）
                    |
          sample1   |   sample2      sample3
gene1       x    <--+-->  x            x
                                       ^
                                       |
                                       +------ 样本内的不同基因对比（RPKM、FPKM、TPM标准化）
                                       |
                                       v
gene2       x             x            x
gene3       x             x            x
```
样本之间的标准化会使用分位数标准化或者CPM(counts per million)或者log-CPM进行（log-CPM的计算为log2(CPM + 2 * 10^6/N)，这样的取对数避免了对0取对数的情况，这个说明在下面的vst标准化那里会提到，之所以取对数是因为使所有样本间的log倍数变化（log-fold-change）向0推移而减小低表达基因间微小计数变化带来的巨大的伪差异性，如果总的read数量有4千万，那么0的值就是log2(2 / 40) = -4.32）

因为这几个计算方法并不涉及到基因长度，所以在计算上是相对方便的，当然了，如果研究的样本在可变剪接的使用上有较大差异，那么在比较的时候使用上面的几种方式可能就不好，这个时候需要考虑长度的因素了。

为了后续可能需要的QPCR实验验证，这里将数据进行一个样本内的标准化的计算。但是这个数值不用于后续的差异分析当中。相关博文RNA-Seq分析|RPKM, FPKM, TPM, 傻傻分不清楚？；BBQ(生物信息基础问题35，36)：RNA-Seq 数据的定量之RPKM，FPKM和TPM，但是目前存在争议究竟是使用FPKM还是TPM的问题，这里对两种方法都进行计算。

## 8.2.2 cufflinks
对于Tophat的比对结果，可以使用Cufflinks软件进行后续的分析。而Cufflinks这个软件其实是个套装，其中是包含许多的软件，包括cuffflinks，cuffmerge，cuffnorm，cuffdiff等，可以对转录本进行拼接、整合、表达丰度的估计以及差异表达的筛选。

## 8.3手动计算
http://www.360doc.com/content/18/0112/02/50153987_721216719.shtml 这篇文章讲的很清楚

已知： 每个样本上不同基因对应到的reads数目
可计算： 每个样本的总reads数目
需要额外获得的数据： 基因长度

首先得到相关基因的长度信息
```
#使用R包 GenomicFeatures 统计每个基因的外显子长度
library(GenomicFeatures)
# 构建Granges对象
txdb <- makeTxDbFromGFF("r7.2.gff" )
# 查找基因的外显子
exons_gene <- exonsBy(txdb, by = "gene")
# 计算总长度
# reduce()、width()是Irange对象的方法
gene_len <- list()
for(i in names(exons_gene)){
    range_info = reduce(exons_gene[[i]])
    width_info = width(range_info)
    sum_len    = sum(width_info)
    gene_len[[i]] = sum_len
}

# 或者写为lapply的形式(快很多)
gene_len <- lapply(exons_gene,function(x){sum(width(reduce(x)))})

data <- t(as.data.frame(gene_len))
# 写入文件
write.table(data, file = "r7.2_gene_len.tsv", row.names = TRUE, sep="\t", quote = FALSE, col.names = FALSE)

#最后会得到一个文本文件，用记事本打开
要注意这里的单位是kb，不是bp
```
开始计算RPKM 和 TPM
```
cpm计算公式

CPM = (10^6 * nr) / N
CPM : Counts per million
nr : 比对至目标基因的read数量
N : 是总有效比对至基因组的read数量

用途∶在某些情况下，只想了解每个基因被覆盖到的相对reads数，而不希望对其做长度校正，就会使用这个指标。

用总reads进行均一化是最简单的方法，其基于以下两个基本假设∶
1）绝大多数的gene表达量不变；
2）高表达量的gene表达量不发生改变；

RPKM计算公式 RPKM适用于单端测序文库RPKM适用于单端测序文库

RPKM = (10^6 * nr) / (L * N)
RPKM: Reads Per Kilobase per Million
nr : 比对至目标基因的read数量
L : 目标基因的外显子长度之和除以1000(因此，要注意这里的L单位是kb，不是bp)
N : 是总有效比对至基因组的read数量

用途∶用于与基因表达量相关的后期分析。基因表达趋势分析、WGCNA共表达网络构建，热图绘制等都使用。
```
```
#!R
# =========== RPKM =============

gene_len_file <- "rn6_gene_len.tsv"
count_file <- "samples.count"

gene_len <- read.table(gene_len_file, header = FALSE, row.name = 1)
colnames(gene_len) <- c("length")

count <- read.table(count_file, header = FALSE, row.name = 1)
colnames(count) <- c("count")
# all read number
all_count <- sum(count["count"])

RPKM <- c()
for(i in row.names(count)){
    count_ = 0
    exon_kb = 1
    rpkm = 0
    count_ = count[i, ]
    exon_kb  = gene_len[i, ] / 1000
    rpkm    = (10 ^ 6 * count_ ) / (exon_kb * all_count )
    RPKM = c(RPKM, rpkm)
}
```
```
TPM计算公式

TPM = nr * read_r * 10^6 / g_r * T
T   = ∑(ni * read_i / g_i)
简言之

TPM = (nr / g_r) * 10^6 / ∑(ni / gi)
TPM : Transcripts Per Million
nr : 比对至目标基因的read数量
read_r: 是比对至基因r的平均read长度
g_r : 是基因r的外显子长度之和（这里无需将其除以1000）
```
```
# =========== 计算TPM ============
# 首先得到总的结果
sum_ <- 0
for(i in row.names(count)){
    count_ = 0
    exon = 1
    count_ = count[i, ]
    exon  = gene_len[i, ]
    value = count_ / exon
    if(is.na(value)){
        print(paste(i, " is error! please check"))
    }else{
        sum_ = sum_ + value
    }
}

TPM <- c()
for(i in row.names(count)){
    count_ = 0
    exon = 1
    count_ = count[i, ]
    exon  = gene_len[i, ]
    tpm = (10 ^ 6 * count_ / exon ) / sum_     
    # 先除以基因长度再除以总read数
    TPM = c(TPM, tpm)
}

count["RPKM"] <- RPKM
count["TPM"] <- TPM       
           
write.table(count, "123.normalize.count", col.names = TRUE, row.names = TRUE, sep="\t", quote = FALSE)
```
实际上上面两种计算方法都默认了每一个基因的所有的外显子都会表达出来，但是实际上可能并不是的，所以如果想要较为精确的值就需要对bam文件加上gff注释文件进行联合之后得到真实的基因的外显子的长度。但是这个对于RPKM的整体计算来说没有什么影响，但是对于TPM来说可能会有微弱的差异。

上面可以看到。RPKM的是单个基因的read count数做分子，然后分母均和总read数相关，实际上这样在一定程度上消除了测序的建库的大小的差异。但是这会带来一定的问题，就是不能保证不同的样本中总的RNA的表达总量总是一致的。假如肝细胞比红细胞的RNA总量高，但是在经过RPKM的时候将
```
某基因表达量： 
            +----+ 占比0.01
肝细胞总RNA：
            +------------------+


某基因表达量： 
            +--+ 占比0.01
红细胞总RNA： 
            +-------------+
            ```
经过nr / N归一化压缩到0~1的范围内（这个时候没有乘以10^6 / L这个常量），那么按照比例来说一样，但是实际上的RNA表达量数值是不等的。只能说表达的占比相等。
```
                    归一化

----------------------------------------------

肝细胞某基因表达量：         红细胞某基因表达量：

y^                         y^
1|-------------+           1|-------------+
 |             |            |             |
 |             |            |             |
 |   *         |            |   *         |
 | *           |            | *           |
 +--------------->          +--------------->
               1 x                         1x
               ```
但是这个数值相等了，是否能评判不同组织中的具体的基因表达量呢？

# 9. 差异表达分析

查看几个管家基因的表达量情况。
```GAPDH(ENSRNOG00000018630)、beta-actin(ENSRNOG00000034254)```
补充：管家基因（housekeeping gene），又名持家基因、看家基因或家务基因，是指在生物体内所有细胞中都表达，并且为维持细胞基本生命活动所需而时刻都在表达的高度保守的基因。
```
cd c/shengxin/project/rat/output/HTseq

cat merge.csv | grep -E "ENSRNOG00000018630|ENSRNOG00000034254"
#CSV（Comma-Separated Values，逗号分隔的值）是用于存储和表示包括文本、数值等各种类型的数据;特点是：文件内的数据以逗号 ,分隔，呈现一个表格形式。
```
这是两个基因的表达量情况
删除HTseq-count的总结行
```
dataframe <- read.csv("merge.csv", header=TRUE, row.names = 1)
```
在数据中存在总结的项，这些项对于后续分析有影响，在HTseq-count的结果有5行总结的内容，分别是：
| 项	| 说明 | 
| :----:|:----:| 
|__alignment_not_unique |比对到多个位置的reads数 | 
|__ambiguous | 不能判断落在那个单位类型的reads数| 
|__no_feature| 不能对应到任何单位类型的reads数 | 
|__not_aligned |存在于SAM文件，但没有比对上的reads数| 
|__too_low_aQual	|低于-a设定的reads mapping质量的reads数| 


这里删除掉
```
                       SRR2190795 SRR2240182 SRR2240183 SRR2240184
__alignment_not_unique    1237425    1821001    1327114    1554701
__ambiguous                237874     419677     260420     328308
__no_feature              1331291    1927193    1435345    1475574
__not_aligned             1350172    2317888    1262136     963510
__too_low_aQual                 0          0          0          0


                       SRR2240185 SRR2240186 SRR2240187 SRR2240228
__alignment_not_unique    1807092     962538    1703558    1436165
__ambiguous                364825     186332     336135     275106
__no_feature              2239366    1150812    1566965    1425114
__not_aligned             1336922    1027748    1734828    1539988
__too_low_aQual                 0          0          0          0
```
```
# 去除前面5行
countdata <- dataframe[-(1:5),]

# 查看数据
head(countdata)  

#不会在原始表格中修改，只是在运行过程中变量中修改

```
将ID的版本号去除

有的时候在基因名后面会有.1或者.2等等的标号出现（这里没有），这个时候需要把它除去

```
# 得到行的名
row_names <- row.names(countdata)

# 开始替换
name_replace <- gsub("\\.\\w+","", row.names(countdata))
# 句号.和加号w+是特殊的，要添加\\来识别
# 表示句号后面的word都要被替换为 [空]

row.names(countdata) <- name_replace

```	
- 去除低表达的基因
在任何样本中都没有足够多的序列片段的基因应该从下游分析中过滤掉。这样做的原因有好几个。 从生物学的角度来看，在任何条件下的表达水平都不具有生物学意义的基因都不值得关注，因此最好忽略。 从统计学的角度来看，去除低表达计数基因使数据中的均值 - 方差关系可以得到更精确的估计，并且还减少了在观察差异表达的下游分析中需要进行的统计检验的数量。
```
countdata <- countdata[rowSums(countdata) > 0,]
```
不是只要是个0的数据去删除，而是所有样本中该基因都是0才删除

到这里就得到了可以用于后续差异分析的数据了

## 9.2 差异分析
差异分析使用DESeq2包进行分析，这个对于输入的数据是原始的read count，所以上述经过HTseq的read计数之后的数据可以输入到DESeq2包中进行差异分析。它与EdgeR包类似，都是基于负二项分布模型。在转录组分析中有三个分析的水平基因水平(gene-level)、转录本水平(transcript-level)、外显子使用水平(exon-usage-level)。但是原始的read count数量并不能代表基因的表达量。

表达差异分析只对比不同样本之间的同一个转录本，所以不需要考虑转录本长度，只考虑总读段数。一个最简单思想就是，样本测序得到的总读段数（实际上是可以比对到转录组的总读段数）越多，则每个基因分配到的读段越多。因此最简单的标准化因子就是总读段数，用总读段数作标准化的前提是大部分基因的表达是非显著变化的，这与基因芯片中的基本假设相同。但是实际工作中发现很多情况下总读段数主要是一小部分大量表达的基因贡献的。Bullard等（2010）在比较了几种标准化方法的基础上发现在每个泳道内使用非零计数分布的上四分位数（Q75%）作为标准化因子是一种更稳健的选择，总体表现是所研究方法中最优的。

Bioconductor的edgeR包和DESeq包分别提供了上四分位数和中位数来作为标准化因子，就是出于这个思想。Bioconductor分析RNA-seq数据

DESeq2的差异分析的步骤
```
构建一个dds(DESeqDataSet)的对象
利用DESeq函数进行标准化处理
用result函数来提取差异比较的结果
```
### 9.2.1 安装与加载包
首先安装对应的R包
```
# 使用bioconductor进行安装
source("http://bioconductor.org/biocLite.R")
options(BioC_mirror="http://mirrors.ustc.edu.cn/bioc/")

# 安装包
biocLite("DESeq2")
biocLite("pheatmap")
biocLite("biomaRt")
biocLite("org.Rn.eg.db")
biocLite("clusterProfiler")

# 加载
library(DESeq2)
library(pheatmap) # 用于作热图的包
library(biomaRt)
library(org.Rn.eg.db)# 大鼠，褐家鼠物种包
library(clusterProfiler) #进行GO、KEGG的富集分析

#bioMart包是一个连接bioMart数据库的R语言接口，能通过这个软件包自由连接到bioMart数据库
这个·包可以做以下几个工作
1.查找某个基因在染色体上的位置。反之，给定染色体每一区间，返回该区间的基因s；
2.通过EntrezGene的ID查找到相关序列的GO注释。反之，给定相关的GO注释，获取相关的EntrezGene的ID；
3.通过EntrezGene的ID查找到相关序列的上游100bp序列（可能包含启动子等调控元件）；
4.查找人类染色体上每一段区域中已知的SNPs；
5.给定一组的序列ID，获得其中具体的序列；

# ↑ 师兄的代码，Error: With R version 3.5 or greater, install Bioconductor packages using BiocManager; 
see https://bioconductor.org/install，
而且package ‘Biocondutor’ is not available for Bioconductor version '3.15'没办法用BiocManager来install("Biocondutor")；
官网建议，一些bioconductor的packages用manager安装就可以，于是采用 BiocManager 来安装   

根据http://bioconductor.org/install/#bioc-version中代码来安装

我的代码：

 if (!require("BiocManager", quietly = TRUE))
    install.packages("BiocManager")  

  BiocManager::install("DESeq2")
  BiocManager::install("pheatmap")
  BiocManager::install("biomaRt")
  BiocManager::install("org.Rn.eg.db")
  BiocManager::install("clusterProfiler")


install.packages("BiocManager")
BiocManager::install("DESeq2", force = TRUE)
  BiocManager::install("pheatmap", force = TRUE)
  BiocManager::install("biomaRt", force = TRUE)
  BiocManager::install("org.Rn.eg.db", force = TRUE)
  BiocManager::install("clusterProfiler", force = TRUE)


```
### 9.2.2 构建对象
这里说白了就是把数据导入到R中生成对应的数据结构，它的基本用法如下：
```
#将创建的差异分析数据集存储在变量 dds 中
dds <- DESeqDataSetFromMatrix(countData = cts, colData = coldata, design= ~ batch + condition)
#用公式 "~ batch + condition" 表示模型中包括批次效应（batch）和条件效应（condition）两个变量
```
- countData（表达矩阵）：是上面一步生成的一个数据框（列对应着每一个样本，行对应的基因名称，中间的值是read的计数），类似于下面的
  
| 样本1	 | 样本2 | 样本3 |样本4|
 :----:|  :----:| :----: |:----: |
| 基因1	 | 10 |20|15|16|
|基因2|0|	0|	2|	2|
|基因3|	120|	110|	20	|10|
|基因4|	40	|44|	10|	20|
|基因5|	20|	10	|13|	12|

- colData（样本信息）：这个是用来描述样本的是实验组还是对照组，类似于下面

|sample|	treatment|
| :----: | :----: |
|Control1|control|
|Control2	|control|
|Experiment1|	treatment|
|Experiment2|	treatment|

treatment不一定就是指代样本是经过什么处理的，也可以是细胞类型、基因型、表现型、样本处理方式、批次等等信息，因为如果直接给样本信息程序是不知道究竟是怎样的分组的，而这些信息就是被用于区分样本的性质对样本分组，所以说是很重要的信息，如果分错那么数据比较的时候就会相应的发生变化，最后得到的结果就会发生变化。	

- design（样本差异比较）：就是指定样本依据什么分为实验组与对照组

上面的表达矩阵已经得到了，下面需要生成样本的信息，下面的表格我直接从NCBI的Run selector中得到。

|Run|BioSample|Sample name|Experiment|LoadDate|MBases	|MBytes|	health state|treatment
| :----: | :----: | :----: | :----: | :----: | :----: |:----: | :----: |:----: |
|SRR2240185|	SAMN03975629|	DEN_1_2__DEN251_255_index7	|SRX1182156	|2015-09-07	|2,195|	1,590	|Liver cirrhosis	|DEN
|SRR2240186	|SAMN03975630|	DEN_4_5__DEN24_59_index12	|SRX1182158	|2015-09-07	|1,128|	815	|Liver cirrhosis|	DEN
|SRR2240187	|SAMN03975631|	PBS_1_2__PBS8_9_index13	|SRX1182166	|2015-09-07	|1,861|	1,342|	Healthy control|	PBS
|SRR2240228	|SAMN03975632|	PBS_3_5__PBS18_19_index14	|SRX1182170	|2015-09-07	|1,649|	1,190	|Healthy control|	PBS

这个表格说明了样本ID及其处理的情况，可以看到就是treatment那一栏不一样，下面针对

表达数据已经有了，下面是写一下实验组与对照组的信息，打开终端，cd /mnt/c/shengxin/project/rat
```
cat <<EOF >./phenotype/phenotype.csv
"ids","state","condition","treatment"
"SRR2240185","Liver cirrhosis","DEN","treatment"
"SRR2240186","Liver cirrhosis","DEN","treatment"
"SRR2240187","Healthy control","PBS","control"
"SRR2240228","Healthy control","PBS","control"
EOF
#EOF “end of file”，表示文本结束符
```
下面将这些数据导入到R中
```
# 刚才countdata已经得到
countdata

# 读取样本分组信息(注意，需要加上row.names = 1, header = TRUE，将行列名需要看好)
coldata <- read.table("../phenotype/phenotype.csv", row.names = 1, header = TRUE, sep = "," )
# 确认一下行列名是否有（不是简单的数值）
head(coldata)
# 调整数据顺序
countdata <- countdata[row.names(coldata)]

# 构建dds对象
dds <- DESeqDataSetFromMatrix(countData = countdata, colData = coldata, design= ~ treatment)

# 查看dds
dds
```
### 9.2.3 样本相关性 --选择性步骤，不是差异分析所必须的
因为存在很多基因的差别等因素，在某些基因上可能样本间几乎没有差别，但是总体来看就会有较大差别了，这里对包含众多的基因这样的因素的情况下进行样本相关性进行评估，评估样本的重复组之间是否很相似或者是否实验组与对照组之间差别明显。

PCA分析(principal components analysis)
由于上面得到的是最原始的read count，但是PCA分析需要对数据进行转化才能进行。一般取对数，但是最原始的数据中有些基因的计数为0，这样在取log值的时候意味着−∞，这样是不行的，所以一般会加上一个常数再取log，也就是log(count + N)（其中N是一个常数），但是也有较好的方法来进行校正，比如DEseq2包自带的rlog和vst函数（全名为variance stabilizing transformation），它们消除了方差对均值的依赖，尤其是低均值时的高log counts的变异。

但是在DESeq2包中实际上已经有了归一化的方法，rlog和vst，在使用的需要根据样本量的多少来选择方法。样本量少于30的话，选择rlog，多于30的话，建议选择vst。
```
# 接续着上面的构建得到的dds对象
# DEseq2包提供了相应的函数
vsdata <- rlog(dds, blind=FALSE)#rlog 转换是一种常用的正态化方法，用于减少计数数据的离散性，并将数据转换为近似正态分布。转换后的数据适合进行差异表达分析和可视化
#使用 rlog 函数对差异表达数据集 dds 进行 rlog 转换。blind=FALSE 参数表示不进行盲转换，这样函数将自动选择最合适的参考样本。
#变量vsdata

# intgroup 分组
plotPCA(vsdata, intgroup="treatment") + ylim(-10, 10)
```