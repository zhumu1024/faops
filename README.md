# faops 使用说明书
  faops (fasta operations)，是一个可以对fasta文件（包含fastq文件和gzipped/bzipped文件）中的序列进行操作的轻量级高效工具。它相当于来自于[ UCSC Jim Kent's utilities](http://hgdownload.cse.ucsc.edu/admin/exe/)的faCount, faSize, faFrag, faRc, faSomeRecords, faFilter 和 faSplit这些工具的集合。同时faops具有体量更小，更易安装，系统兼容性好，更易安装（只有一个外部依赖），被广泛使用验证的优点。

faops可以完成：
* 统计序列基础信息
* 从fasta文件中提取子片段
* 获得fasta文件中序列的反向互补序列
* 对fasta中的序列进行筛选
* 统计N50，以及其他统计特征
* 合并双端测序数据
* 。。。。。。。

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
### 7. order
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

### 5. 
功能：

使用方法：

* options选项：
```
    
```
实用举例：
输入：
```shell

```
输出：
```

```

### 8. replace
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


