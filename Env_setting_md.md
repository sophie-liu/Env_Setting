# 使用 Environment Module+conda 管理和运行软件

随着分析任务的增多，我们需要的软件也越来越多，这个时候软件及其依赖环境的管理也变得越来越困难，不经意的更新可能就会导致软件依赖冲突，而使软件突然无法运行。以往我们对该问题的解决方式是使用软件的绝对路径，并在运行软件之前修改其依赖环境的环境变量，但是这种方法不仅低效，而且会使得软件的可移植性降低。为了彻底解决这个问题，我们推荐使用 Environment Modules+conda组合，通过组合使用这两个工具，可以方便地管理不同软件和其依赖环境，并在需要时动态加载软件和其依赖环境。

## conda的安装与使用

### conda是什么？

conda是任何语言的包、依赖和环境的管理工具，目前conda支持的语言包括：Python、Perl、R、Ruby、Lua、Scala、Java、Javascript、C / C ++、FORTRAN。Conda是一个跨平台的开源的软件包管理系统和软件依赖环境管理系统，目前可以运行在windows、macOS和Linux系统之上。conda可以快速安装、运行、加载和更新软件包和它们的依赖环境。在本地计算机上，conda可以快速的创建、保存、加载依赖环境，并在多个环境之间实现快速切换。 虽然起初是为Python程序创建的，但现在可以打包和分发任何软件。conda作为一个包管理器可以帮助你查找和安装软件包。但同时conda作为一个软件依赖环境管理器，它也可以帮助你管理不同的软件开发环境。只需要几个命令就可以为你建立一个完全独立的 环境，你可以在里面运行不同的python版本，而你依然可以在通常的开发环境中使用之前的python版本。默认配置的下，conda可以安装和管理由repo.continuum.io构建、审核和维护的几千个软件包。

### 安装conda

我们通过安装anaconda或者miniconda就可以获得它们自带的conda,安装anaconda或者miniconda的方法自行搜索。

### 配置channel

在conda的默认配置中，并不包含生物信息学软件源，所以需要把生物信息学相关的chanels添加到conda中：

```
conda config --add channels r
conda config --add channels defaults
conda config --add channels conda-forge
conda config --add channels bioconda
```

### 添加国内软件源

安装完anaconda或者miniconda之后首要的就是要添加国内的conda镜像源，解决软件下载速度慢的问题。

```
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
conda config --add channels https://nanomirrors.tuna.tsinghua.edu.cn/anaconda/cloud/bioconda/
```

完成以上步骤基本安装算是完成了，下面我们来看一下使用conda来创建软件环境，并安装相应的软件。

### conda的使用

创建环境

```
conda create -n env_name python=2.7 ...
```

说明：env_name就是我们创建的软件环境名称，默认安装到anaconda或miniconda安装目录下的envs目录下。"python=2.7"就是在安装时初步安装的软件及使用的版本，"..."表示可以同时安装多个软件。如果在创建完成后还想再在该环境中安装软件，则可以使用如下命令：
```
conda install -n env_name -c channel soft_name
```

说明：“-n env_name表示要使用的环境”，“-c channel表示使用的软件源”，“sofname”就是我们要安装的软件。 创建完成之后，我们如果要使用该环境下的软件，我们必须先激活该环境：

```
source activate your_env_name #Linux
activate your_env_name        #windows
```

如果要退出该环境则运行命令：

```
source deactivate    #Linux
deactivate        #Windows
```

通过conda将依赖冲突的软件安装到不同的环境，我们基本实现了不同环境的隔离，但是还是有美中不足的地方，那就是每次只能有一个激活的环境可供使用，如果想要使用其他环境下的软件，则必须退出当前环境和激活新的环境。当然在很多情况下这个问题是可以忽略的，但是如果我们需要同时使用两个甚至多个不同环境的软件时，这就成了问题。那么我们怎样才能同时使用来自多个环境中软件呢？ 方法是使用Environment Module提供的module命令。

## Environment Module的安装与使用

Environment Module是一个可以动态修改软件运行环境的软件，在该软件中一个模块（module）就代表一个软件和它依赖环境的集合，目前多数集群上都使用module来实现软件的及其依赖的动态加载。

### Environment Module的安装

安装 module 程序，这个直接用软件仓库安装就行了。在 CentOS 7 下，命令是 ：

```
sudo yum install environment-modules
```
安装完之后，我们就可以运行命令module了。

```
[hanmin@mu01 ~]$ module -H
Modules Release 3.2.10 2012-12-21 (Copyright GNU GPL v2 1991):
Usage: module [ switches ] [ subcommand ] [subcommand-args ]
Switches:-H|--help this usage info
-V|--version modules version & configuration options
-f|--force force active dependency resolution
-t|--terse terse format avail and list format
-l|--long long format avail and list format
-h|--human readable format avail and list format
-v|--verbose enable verbose messages
-s|--silent disable verbose messages
-c|--create create caches for avail and apropos
-i|--icase case insensitive
-u|--userlvl <lvl> set user level to (nov[ice],exp[ert],adv[anced])
Available SubCommands and Args:
+ add|load modulefile [modulefile ...]
+ rm|unload modulefile [modulefile ...]
+ switch|swap [modulefile1] modulefile2
+ display|show modulefile [modulefile ...]
+ avail [modulefile [modulefile ...]]
+ use [-a|--append] dir [dir ...]
+ unuse dir [dir ...]
+ update
+ refresh
+ purge 
+ list
+ clear
+ help [modulefile [modulefile ...]]
+ whatis [modulefile [modulefile ...]]
+ apropos|keyword string
+ initadd modulefile [modulefile ...]
+ initprepend modulefile [modulefile ...]
+ initrm modulefile [modulefile ...]
+ initswitch modulefile1 modulefile2
+ initlist
+ initclear
```

源码编译方法参见这里如果是采取编译安装的，需要设置一下脚本和资源。对于 Bash 用户，创建一个 module_bashrc 文件（假设放在~/）如下（所有<>中的路径请替换为你系统中的路径，下同）：

```
#----------------------------------------------------------------------#
# system-wide bashrc ## functions and settings for sh-derivative shells #
#----------------------------------------------------------------------#
. <MODULE_INSTALL_PATH>/init/bash
# module initialization
#
case "$0" in
-sh|sh|*/sh)    modules_shell=sh ;;
-ksh|ksh|*/ksh)    modules_shell=ksh ;;
-zsh|zsh|*/zsh)    modules_shell=zsh ;;
-bash|bash|*/bash)    modules_shell=bash ;;
esac
eval "module() { eval \`<MODULE_INSTALL_PATH>/bin/modulecmd $modules_shell \$*\`; }"
unset modules_shell
#----------------------------------------------------------------------#
# set this if bash exists on your system and to use it
# instead of sh - so per-process dot files
# will be sourced.
#----------------------------------------------------------------------#
sh() { bash "$@"; }
#----------------------------------------------------------------------#
# further system customizations can be added here
#
export MODULEPATH=<MODULE_INSTALL_PATH>/modulefiles:$MODULEPATH
```

配置文件的最后一行指明了 modulefiles 存放的路径，我将它们放在 module 的安装路径下了，当然也可以放入用户自定义的目录中。在当前账户的 .bashrc 里加入一行 source module_bashrc 使得每次登陆的时候会初始化 module。如果是用包管理器安装的，直接在 ~/.bashrc 里加上上面的最后一行来指定其他存储 modulefiles 的位置。

### module命令列表
```
$ module -h             
$ module avail               #查看可用模块avail
$ module list                #查看已加载模块list
$ module load MODULE_NAME    #加载模块load
$ module unload MODULE_NAME  #卸载模块unload
$ module switch OLD_MODULE NEW_MODULE  #切换模块switch
等价于：
$ module unload OLD_MODULE; module load NEW_MODULE
$ module purge                #卸载所有已加载的模块purge
$ module whatis MODULE_NAME    #显示模块说明whatis
$ module display MODULE_NAME    #显示该模块内容display
$ module avail                #显示可用模块
$ module show module_name     #查看模块的moudulefile内容
$ module key "search term"    #搜索模块添加modulefile
```

### modulefile的编写

安装完软件之后，我们需要为每一个软件及其依赖环境创建一个modulefile，modulefile指明了加载模块时需要执行的命令。
1. 一般来说这不比写环境变量脚本麻烦多少，主要就是标明这个库的冲突标识符（两个冲突标识符相同的 module 不能同时加载）和添加软件路径PATH 和依赖库LD_LIBRARY_PATH 和 MANPATH 路径。moudulefile参考示例如下：

```
#%Module1.0 #modulefile格式版本，必写
##
## MRO-3.4.0 modulefile
## 微软出的R语言，是商业软件，但可以免费使用，和普通R版本完全兼容，但是对其内存和多线程进行了优化，性能高很多
##proc ModulesHelp { } {
global dotversion
puts stderr "\tAdds `/home/hanmin/anaconda3/envs/mro/bin' to your PATH environment variable"
puts stderr "\n\tThis makes it easy to add the /BGA2017/R-3.4.0/bin directory"
puts stderr "\tto your PATH environment variable. This allows you to"
puts stderr "\trun executables from the /home/hanmin/anaconda/envs/mro/bin."}
module-whatis "adds `/home/hanmin/anaconda3/envs/mro/bin' to your PATH environment variable"
#添加软件的可执行文件路径
prepend-path PATH /home/hanmin/anaconda3/envs/mro/bin
#添加软件的依赖库
prepend-path -d " " CFLAGS "-I/home/hanmin/anaconda3/envs/mro/include"
prepend-path -d " " CPPFLAGS "-I/home/hanmin/anaconda3/envs/mro/include"
prepend-path -d " " LDFLAGS "-I/home/hanmin/anaconda3/envs/mro/lib"
prepend-path LD_RUN_PATH /home/hanmin/anaconda3/envs/mro/lib
prepend-path LD_LIBRARY_PATH /home/hanmin/anaconda3/envs/mro/lib
```
2. 将写好的 modulefiles 放到MODULEPATH中指定的目录中。同一个库不同版本的 modulefiles 应放在同一个文件夹中，以版本号作为文件名。现以python为例，事先通过conda在本地创建了两个python语言环境:py2.7和intel_py

```
$conda info -e
# conda environments:
#base * /home/mhan/anaconda3
intel_py /home/mhan/anaconda3/envs/
intel_pypy2.7 /home/mhan/anaconda3/envs/py2.7
#按照上一步modulefile的格式分别为py2.7和intel_py编写modulefile
$module avail #查看可用的module
-------------------------------------------------------------------------------------------/usr/share/modules/versions --------------------------------------------------------------------------------------------
                                                                                                    3.2.10
------------------------------------------------------------------------------------------/usr/share/modules/modulefiles ------------------------------------------------------------------------------------------
dot intelpy/3.5 module-git module-info modules null python/2.7 qiime2/2018.6 samtools/1.8 use.own
$module load intelpy/3.5
$which python/home/mhan/anaconda3/envs/intel_py/bin/python
$module load python/2.7
$which python/home/mhan/anaconda3/envs/py2.7/bin/python
```

3. 如果需要指定默认加载的版本，则需要在文件夹里多创建一个 .version 文件：

```
#%Module1.0
set ModulesVersion "<DEFAULT_VERSION_STRING>"
```

以上通过module加载使用conda安装的软件的可执行路径，每个软件执行所需要的依赖实际上是相互隔离在不同的conda环境下的，这样就基本解决了软件依赖冲突的问题。
在运行所需要的命令之前先加载该命令所在的模块，然后该模块所拥有的所有可执行程序执行时都只需要写相应的名称就可以了，不需要再写绝对路径。
```
$module load qiime2/2018.6
$which qiime/home/mhan/anaconda3/envs/qiime2-2018.6/bin/qiime
$qiimeUsage: qiime [OPTIONS] COMMAND [ARGS]...
QIIME 2 command-line interface (q2cli)
--------------------------------------
To get help with QIIME 2, visit https://qiime2.org.
To enable tab completion in Bash, run the following command or add it to
your .bashrc/.bash_profile:
source tab-qiime
To enable tab completion in ZSH, run the following commands or add them to
your .zshrc:
autoload bashcompinit && bashcompinit && source tab-qiime
Options:
--version Show the version and exit.
--help Show this message and exit.
Commands:
info Display information about current deployment.
tools Tools for working with QIIME 2 files.
dev Utilities for developers and advanced users.
alignment Plugin for generating and manipulating alignments.
composition Plugin for compositional data analysis.
cutadapt Plugin for removing adapter sequences, primers, and
  other unwanted sequence from sequence data.
dada2 Plugin for sequence quality control with DADA2.
deblur Plugin for sequence quality control with Deblur.
demux Plugin for demultiplexing & viewing sequence quality.
diversity Plugin for exploring community diversity.
emperor Plugin for ordination plotting with Emperor.
feature-classifier Plugin for taxonomic classification.
feature-table Plugin for working with sample by feature tables.
gneiss Plugin for building compositional models.
longitudinal Plugin for paired sample and time series analyses.
metadata Plugin for working with Metadata.
phylogeny Plugin for generating and manipulating phylogenies.
quality-control Plugin for quality control of feature and sequence data.
quality-filter Plugin for PHRED-based filtering and trimming.
sample-classifier Plugin for machine learning prediction of samplemetadata.
taxa Plugin for working with feature taxonomy annotations.
vsearch Plugin for clustering and dereplicating with vsearch.
$module unload qiime/2018.6
$which qiimeqiime not found
```