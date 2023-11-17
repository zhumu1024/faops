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

## faops 命令解释

### help 获取帮助信息
```shell
faops help

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


