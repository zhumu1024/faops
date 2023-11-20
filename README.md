# faops 使用说明书
  faops (fasta operations)，是一个可以对fasta文件（包含fastq文件和gzipped/bzipped文件）中的序列进行操作的轻量级高效工具。它相当于来自于[ UCSC Jim Kent's utilities](http://hgdownload.cse.ucsc.edu/admin/exe/)的faCount, faSize, faFrag, faRc, faSomeRecords, faFilter 和 faSplit这些工具的集合。同时faops具有体量更小，更易安装，系统兼容性好，更易安装（只有一个外部依赖），被广泛使用验证的优点。

faops可以完成的功能：
* 统计序列基础信息
* 从fasta文件中提取子片段
* 获得fasta文件中序列的反向互补序列
* 对fasta中的序列进行筛选
* 统计N50，以及其他统计特征
* 合并双端测序数据
* 。。。。。。。

faops的命令：

| Header 1 | Header 2 | Header 3 |
|----------|:---------:|----------|
| Cell 1   |   Cell 2  |  Cell 3  |
| Cell 4   |   Cell 5  |  Cell 6  |


## faops下载编译

```shell
git clone https://github.com/wang-q/faops
cd faops
make
```
使用Homebrew or Linuxbrew下载：
```shell
brew install wang-q/tap/faops
```
本说明中展示例子中使用的的fa文件： u1.fa
```
>read1
taGGCGcGGgCggtgTgGATTAaggCAGaggtTgCGCGCtTgaTAaAACTacgtaACatcggGAAcTtcgaccGgtCTCgGccCtatAtgaTtCcGatc
GCaTaTC
>read2
AtagcAagCtcAgttcaACttCAcCGGT
A
AaTtcttgTAGtgTcTCgacCgcCcCctTGTACtgtaGG
caAtaGTaaTgAcTagGaCGTaagagAcCaccaCagGAAgAGAATccgCGaAtcCcTcacCCttGGTC
ctGgAttttgcgcgTggtatgagGgAGtctcaATTGTCaccTaC
GTatcccAGCgCtAcAcaAGAcTaCAtCTggCatTAG
>read3
gTTTTcttaGgCgtccCGAAgcAtCtCTagCCgggGgTAatctccAgg
tgTgTttGTTaCCtcCTCGtgACCC
```
## faops 命令解释

### 1. help 获取帮助信息

输入：
```shell
$ faops help
```
输出：

```shell

$ ./faops

Usage:     faops <command> [options] <arguments>
Version:   0.8.21

Commands:
    help           print this message
    count          count base statistics in FA file(s)
    size           count total bases in FA file(s)
    masked         masked (or gaps) regions in FA file(s)
    frag           extract sub-sequences from a FA file
    rc             reverse complement a FA file
    one            extract one fa record
    some           extract some fa records
    order          extract some fa records by the given order
    replace        replace headers from a FA file
    filter         filter fa records
    split-name     splitting by sequence names
    split-about    splitting to chunks about specified size
    n50            compute N50 and other statistics
    dazz           rename records for dazz_db
    interleave     interleave two PE files
    region         extract regions from a FA file

Options:
    There're no global options.
    Type "faops command-name" for detailed options of each command.
    Options *MUST* be placed just after command.


```
### 2. count 

功能： 统计基础统计信息，包括fa文件中各序列名称，长度，A/C/G/T/N这几个碱基的数量

使用方法：`faops count <in.fa> [more_files.fa]`

实用举例：

统计fa文件：

输入：
```shell
faops count u1.fa
```
u1.fa内容：
```
>read1
taGGCGcGGgCggtgTgGATTAaggCAGaggtTgCGCGCtTgaTAaAACTacgtaACatcggGAAcTtcgaccGgtCTCgGccCtatAtgaTtCcGatc
GCaTaTC
>read2
AtagcAagCtcAgttcaACttCAcCGGT
A
AaTtcttgTAGtgTcTCgacCgcCcCctTGTACtgtaGG
caAtaGTaaTgAcTagGaCGTaagagAcCaccaCagGAAgAGAATccgCGaAtcCcTcacCCttGGTC
ctGgAttttgcgcgTggtatgagGgAGtctcaATTGTCaccTaC
GTatcccAGCgCtAcAcaAGAcTaCAtCTggCatTAG
>read3
gTTTTcttaGgCgtccCGAAgcAtCtCTagCCgggGgTAatctccAgg
tgTgTttGTTaCCtcCTCGtgACCC
```
输出：
```
#seq    len     A       C       G       T       N
read1   106     24      25      32      25      0
read2   217     57      58      49      53      0
read3   73      10      21      19      23      0
total   396     91      104     100     101     0
```
----------------------------------------------------------------------------------------------------------
 从gzipped文件中读取数据

输入：
```shell
faops count u1.fa.gz
```
输出：
```
#seq    len     A       C       G       T       N
read1   106     24      25      32      25      0
read2   217     57      58      49      53      0
read3   73      10      21      19      23      0
total   396     91      104     100     101     0
```
----------------------------------------------------------------------------------------------------------
 从stadin（标准输入）中读取数据
   输入：
   ```shell
    cat u1.fa | faops count stdin
   ```
输出：
```
#seq    len     A       C       G       T       N
read1   106     24      25      32      25      0
read2   217     57      58      49      53      0
read3   73      10      21      19      23      0
total   396     91      104     100     101     0
```
### 2.size 

功能：统计序列长度 输入

使用方法： `faops size <in.fa> [more_files.fa]`

实用举例：
输入：可以从fa文件，zipped文件，及stdin中读取数据，具体请参照[count](https://github.com/zhumu1024/faops/blob/main/README.md#2-count)
```shell
faops size u1.fa
```

输出：
```
read1   106
read2   217
read3   73
```
### 3.masked
功能： 在统计时屏蔽fa文件中的某些区域
使用方法：faops masked [options] <in.fa> [more_files.fa]
* options选项
  ```
  -g 只记录N/N的区域
  ```
输入：
```shell
 faops masked u1.fa | grep "read1"
 # 只查看第一条序列的输出数据
```

输出：
```
read1:1-2
read1:7
read1:10
read1:12-15
read1:17
read1:23-25
read1:29-32
read1:34
read1:40
read1:42-43
read1:46
read1:51-55
read1:58-62
read1:66
read1:68-73
read1:75-76
read1:80
read1:82-83
read1:85-87
read1:89-91
read1:93
read1:95
read1:97-99
read1:102
read1:104
```

### 4. frag
功能： 从fa文件中提取子片段

使用方法： faops frag [options] <in.fa> <start> <end> <out.fa>
* options选项：
```
 -l INT     设置输出时每行序列的长度，默认为80
```

实用举例：
输入：在在输入的片段不指定的情况下，默认使用第一个序列，如果想要指定，可以与其他命令联用。

```shell
faops frag -l 20  u1.fa 1 20 out.fa

# -l 20 规定输出时每行的序列长度为20
# 1 20 规定提取子片段的区域

```

输出：
```
>read1:1-20
taGGCGcGGgCggtgTgGAT
```

### 5. rc
功能：获得所提供序列的reverse complement （反向互补）片段

使用方法：faops rc [options] <in.fa> <out.fa>

* options选项：
```
    -n         保持各序列的name不变，不添加RC_
    -r         仅得到数据的反向片段，而不互补, name中添加R_
    -c         仅得到数据的互补片段，而不反向, name中添加C_
    -f STR     仅对给定列表（list.file)中的序列进行处理
    -l INT     设置输出时每行序列的长度，默认为80
```
实用举例：
输入：
```shell
faops rc u1.fa out.fa
```
输出：
```
>RC_read1
GAtAtGCgatCgGaAtcaTataGggCcGAGacCggtcgaAgTTCccgatGTtacgtAGTTtTAtcAaGCGCGcAacctCT
GcctTAATCcAcaccGcCCgCGCCta
>RC_read2
CTAatGccAGaTGtAgTCTtgTgTaGcGCTgggatACGtAggtGACAATtgagaCTcCctcataccAcgcgcaaaaTcCa
gGACCaaGGgtgAgGgaTtCGcggATTCTcTTCctGtggtGgTctcttACGtCctAgTcAttACtaTtgCCtacaGTACA
agGgGgcGgtcGAgAcaCTAcaagaAtTTACCGgTGaaGTtgaacTgaGctTgctaT
>RC_read3
GGGTcaCGAGgaGGtAACaaAcAcaccTggagatTAcCcccGGctAGaGaTgcTTCGggacGcCtaagAAAAc
```
----------------------------------------------------------------------------------------------------------
进阶使用： 使用-f 选项 例子
list.file 内容
```
read1
```

输入：
```shell
faops rc -f list.file u1.fa out.fa
```

输出：
```
# 在list.file中只规定了read1，这里只对read1进行处理
>RC_read1
GAtAtGCgatCgGaAtcaTataGggCcGAGacCggtcgaAgTTCccgatGTtacgtAGTTtTAtcAaGCGCGcAacctCT
GcctTAATCcAcaccGcCCgCGCCta
>read2
AtagcAagCtcAgttcaACttCAcCGGTAAaTtcttgTAGtgTcTCgacCgcCcCctTGTACtgtaGGcaAtaGTaaTgA
cTagGaCGTaagagAcCaccaCagGAAgAGAATccgCGaAtcCcTcacCCttGGTCctGgAttttgcgcgTggtatgagG
gAGtctcaATTGTCaccTaCGTatcccAGCgCtAcAcaAGAcTaCAtCTggCatTAG
>read3
gTTTTcttaGgCgtccCGAAgcAtCtCTagCCgggGgTAatctccAggtgTgTttGTTaCCtcCTCGtgACCC
```

### 6. one
功能：只提取一个fa序列

使用方法：faops one [options] <in.fa> <name> <out.fa>

* options选项：
```
 -l INT     设置输出时每行序列的长度，默认为80   
```
实用举例：
输入：
```shell
faops one u1.fa read1 out.fa
```
输出：
```
>read1
taGGCGcGGgCggtgTgGATTAaggCAGaggtTgCGCGCtTgaTAaAACTacgtaACatcggGAAcTtcgaccGgtCTCg
GccCtatAtgaTtCcGatcGCaTaTC
```
### 7. some
功能：提取某些序列

使用方法：faops some [options] <in.fa> <list.file> <out.fa>

* options选项：
```
    -i         invert 提取未提供的fa序列
    -l INT     设置输出时每行序列的长度，默认为80   
```
实用举例：
输入：
```shell
faops some u1.fa list.file out.fa
```
输出：
```
>read1
taGGCGcGGgCggtgTgGATTAaggCAGaggtTgCGCGCtTgaTAaAACTacgtaACatcggGAAcTtcgaccGgtCTCg
GccCtatAtgaTtCcGatcGCaTaTC
>read2
AtagcAagCtcAgttcaACttCAcCGGTAAaTtcttgTAGtgTcTCgacCgcCcCctTGTACtgtaGGcaAtaGTaaTgA
cTagGaCGTaagagAcCaccaCagGAAgAGAATccgCGaAtcCcTcacCCttGGTCctGgAttttgcgcgTggtatgagG
gAGtctcaATTGTCaccTaCGTatcccAGCgCtAcAcaAGAcTaCAtCTggCatTAG

```
### 8. order
功能：通过给定的某些order（命令信息）提取fa序列
使用方法：faops order [options] <in.fa> <list.file> <out.fa>

* options选项：
```
    -l INT     设置输出时每行序列的长度，默认为80     
```
实用举例：
输入：
```shell
faops order -l 200 u1.fa list.file out.fa
```
输出：
```
>read1
taGGCGcGGgCggtgTgGATTAaggCAGaggtTgCGCGCtTgaTAaAACTacgtaACatcggGAAcTtcgaccGgtCTCgGccCtatAtgaTtCcGatcGCaTaTC
>read2
AtagcAagCtcAgttcaACttCAcCGGTAAaTtcttgTAGtgTcTCgacCgcCcCctTGTACtgtaGGcaAtaGTaaTgAcTagGaCGTaagagAcCaccaCagGAAgAGAATccgCGaAtcCcTcacCCttGGTCctGgAttttgcgcgTggtatgagGgAGtctcaATTGTCaccTaCGTatcccAGCgCtAcAcaAG
AcTaCAtCTggCatTAG

通过其他形式传递order
输入：
```shell
faops order -l 200 u1.fa  <(echo read1)  out.fa
```

输出：
```
>read1
taGGCGcGGgCggtgTgGATTAaggCAGaggtTgCGCGCtTgaTAaAACTacgtaACatcggGAAcTtcgaccGgtCTCgGccCtatAtgaTtCcGatcGCaTaTC
```


### 9. replace
功能：对特定序列名进行替换，也可以对指定的序列进行提取，改名，并输出

使用方法：faops replace [options] <in.fa> <replace.tsv> <out.fa>

* options选项：
```
    -s         只提取某些序列，类似some
    -l INT     设置输出时每行序列的长度，默认为80     
```
实用举例：
replace.tsv 内容：
```
read1	106
read2	217
read3	73
```

输入：
```shell
faops replace u1.fa replace.tsv out.fa
```
输出：
```
>106
taGGCGcGGgCggtgTgGATTAaggCAGaggtTgCGCGCtTgaTAaAACTacgtaACatcggGAAcTtcgaccGgtCTCg
GccCtatAtgaTtCcGatcGCaTaTC
>217
AtagcAagCtcAgttcaACttCAcCGGTAAaTtcttgTAGtgTcTCgacCgcCcCctTGTACtgtaGGcaAtaGTaaTgA
cTagGaCGTaagagAcCaccaCagGAAgAGAATccgCGaAtcCcTcacCCttGGTCctGgAttttgcgcgTggtatgagG
gAGtctcaATTGTCaccTaCGTatcccAGCgCtAcAcaAGAcTaCAtCTggCatTAG
>73
gTTTTcttaGgCgtccCGAAgcAtCtCTagCCgggGgTAatctccAggtgTgTttGTTaCCtcCTCGtgACCC
```
### 10. filter
功能：对fa文件进行筛选

使用方法： faops filter [options] <in.fa> <out.fa>

* options选项：
```
    -a INT     设定筛选的序列长度的最小值 
    -z INT     设定筛选的序列长度的最大值 
    -n INT     设定筛选的序列中所含N/n的最大限制数量
    -u         Unique,  去除重复
    -U         Upper case, 将序列数据中的小写全部改为大写
    -b         将几行数据合成一行
    -N         将一些模糊碱基用N代替
    -d         将破折号（dashes）去除
    -s         simplify 简化序列的名称
    -l INT     设置输出时每行序列的长度，默认为80     
```
实用举例：
输入：
```shell
faops filter -a 100 -z 200 -d -s u1.fa out.fa
# -a 所筛选数据长度最小值为100
# -z 所筛选数据长度最大值为200
# read1 read2 read3的长度分别为106 217 73

```
输出：
```
>read1
taGGCGcGGgCggtgTgGATTAaggCAGaggtTgCGCGCtTgaTAaAACTacgtaACatcggGAAcTtcgaccGgtCTCg
GccCtatAtgaTtCcGatcGCaTaTC

```
### 11. split-name
功能：分割序列文件，使用序列名称作为files的名称

使用方法：faops split-name [options] <in.fa> <outdir>

* options选项：
```
    -l INT     设置输出时每行序列的长度，默认为80         
```
实用举例：
输入：
```shell
faops split-name -l 100 u1.fa ../out_file
```
输出：
```
read1.fa
read2.fa
read3.fa
```


### 12. split-about
功能：按byte大小对序列数据文件进行切割

使用方法：faops split-about [options] <in.fa> <approx_size> <outdir>

* options选项：
```
    -e         even 分割时保持均匀
    -m INT     切割行数的最大值
    -l INT     设置输出时每行序列的长度，默认为80         
```
实用举例：
输入：
```shell
 faops split-about u1.fa 12 ../out_file
```
输出：
```
000.fa内容：
>read3
gTTTTcttaGgCgtccCGAAgcAtCtCTagCCgggGgTAatctccAggtgTgTttGTTaCCtcCTCGtgACCC

001.fa内容：
>read2
AtagcAagCtcAgttcaACttCAcCGGTAAaTtcttgTAGtgTcTCgacCgcCcCctTGTACtgtaGGcaAtaGTaaTgA
cTagGaCGTaagagAcCaccaCagGAAgAGAATccgCGaAtcCcTcacCCttGGTCctGgAttttgcgcgTggtatgagG
gAGtctcaATTGTCaccTaCGTatcccAGCgCtAcAcaAGAcTaCAtCTggCatTA

002.fa内容：
>read3
gTTTTcttaGgCgtccCGAAgcAtCtCTagCCgggGgTAatctccAggtgTgTttGTTaCCtcCTCGtgACCC

```


### 13. n50 
功能：计算N50或其他统计特征

使用方法： faops n50 [options] <in.fa> [more_files.fa]

* options选项：
```
    -H         不展示header，只返回统计值
    -N INT     计算Nx 默认是N50
    -S         sum 碱基总数计算
    -A         average 计算序列的平均碱基数
    -E         计算E-size 表示随机匹配的可能性，E值越大，随机匹配的可能性越大
    -C         计算总序列数
    -g INT     指定预计的基因组大小，方便计算N50
```
实用举例：
输入：
```shell
 faops n50 u1.fa
```
输出：
```
N50     217
```
 faops n50 -N 90 -S -A -E -C u1.fa
输入：
```shell

```
输出：
```
N90     73
S       396
A       132.00
E       160.74
C       3
```
### 14. dazz
功能：序列信息标准化，重复序列仅保留第一个

使用方法：faops dazz [options] <in.fa> <out.fa>

* options选项：
```
    -p STR     设置新的序列名称前缀 默认是read
    -s INT     设置起始的序号，默认是0
    -a         不删除重复序列
    -l INT     设置输出时每行序列的长度，默认为80       
```
实用举例：
输入：
```shell
faops dazz u1.fa out.fa
```
输出：
```
>read/1/0_106
taGGCGcGGgCggtgTgGATTAaggCAGaggtTgCGCGCtTgaTAaAACTacgtaACatcggGAAcTtcgaccGgtCTCg
GccCtatAtgaTtCcGatcGCaTaTC
>read/2/0_217
AtagcAagCtcAgttcaACttCAcCGGTAAaTtcttgTAGtgTcTCgacCgcCcCctTGTACtgtaGGcaAtaGTaaTgA
cTagGaCGTaagagAcCaccaCagGAAgAGAATccgCGaAtcCcTcacCCttGGTCctGgAttttgcgcgTggtatgagG
gAGtctcaATTGTCaccTaCGTatcccAGCgCtAcAcaAGAcTaCAtCTggCatTAG
>read/3/0_73
gTTTTcttaGgCgtccCGAAgcAtCtCTagCCgggGgTAatctccAggtgTgTttGTTaCCtcCTCGtgACCC
```


### 15. interleave 
功能：将双端测序的两个文件合成一个文件（也支持只输入一个文件，此时另一端以N填补）

使用方法：faops interleave [options] <R1.fa> [R2.fa]

* options选项：
```
    -q         此时输入需要是fastq文件
    -p STR     设置新的序列名称前缀 默认是read
    -s INT     设置起始的序号，默认是0
```
实用举例：
输入：
```shell
faops interleave -q -p test R1.fq R2.fq > out.fa
```
### 16. region
功能：从一个fasta文件中摘取一个或多个指定的序列片段，可以标注

使用方法：faops region [options] <in.fa> <region.txt> <out.fa>

* options选项：
```
    -s         在header中添加 +
    -l INT     置输出时每行序列的长度，默认为80       
实用举例：
输入：
```shell
 faops region -s  u1.fa region.txt out.fa
```
region.txt的内容：
```
read1:50-60
```
输出：
```
>read1(+):50-60
TacgtaACatc

```
