---
title: 文件整理工具 Move It
date: 2021/11/03 21:00:00
update: 2021/11/03 21:00:00
tags:
- shell
- linux
categories: Tool
excerpt: 一个由shell脚本编写的文件整理工具。
---

# 起因

在linux平台使用chrome浏览器的时候使用的是浏览器默认的下载器，所以所有的下载文件都放在了~/download 目录下面，久而久之这个目录下面充斥着各种文件。其中包含下载的图像、程序的源码包、各种电子书还有很多用来测试的视频序列。最开始的时候我是使用这样的命令来整理不同的类型文件的：

```bash
mkdir pdf compress images
mv *.pdf ./pdf
mv *.zip *.gz *.7z ./compress
mv *.jpg ./images
```

但是这样有一个问题，我要自己判断文件的类型，然后放到对应的位置，如果要整理其他路径下面的文件还要重新输入命令，所以就产生了自己写一个工具来整理文件的想法。

已经完成的脚本在 [https://github.com/diandianti/move_it](https://github.com/diandianti/move_it)

# 实现过程

实现按照类型分类文件的工具可以用c、python或者shell，权衡了一下实现的难度以及我的需求，最后使用shell来实现。

## 文件类型获取

文件类型使用文件的扩展名来判断，获取扩展名的方式如下：

```bash
file_path=/path/to/file.test
ext_name=${file_path##*.}
```

获得了扩展名之后需要判断文件的类型，是文档还是图片或者是压缩文档，我写了一个函数来实现这个功能。主要思路是创建了一个数组，然后数组里面包含了类型的名字，让后让输入的扩展名来和类型包含的名字来比对，如果一致就返回当前的类型，代码如下：

```bash
# mi 是 move_it的缩写

mi_compress=(zip tar gz 7z tgz arc arj taz \
    lha lz4 lzh lzma tlz tzo t7z z dz \
    lrz lz lzo xz zst tzst bz2 bz tbz tbz2 \
    iso)
mi_image=(jpg jpeg bmp png ppm\
    gif pbm pgm tga xbm tif tiff \
    svg svgz psd)
mi_video=(mkv webm mjpg mjpeg m2v ogm m4v \
    mp4v vob qt nuv wam rm rmvb flv avi\
    yuv mp4)
mi_audio=(mp3 flac aac mid midi wav)
mi_doc=(doc docx xps md txt ps diff)
mi_excel=(xls xlsx csv)
mi_ppt=(ppt pptx)
mi_font=(ttf otf)
mi_code=(sh bash py c cpp cc cxx h hpp s)
mi_web=(html css js)
mi_ebook=(mobi epub pdf)
mi_nn=(caffemodel onnx prototxt pb)
mi_bin=(deb appimage bin dat)
mi_apk=(apk)
mi_lib=(so a)

mi_all_ff=(mi_compress mi_image mi_video mi_audio mi_doc mi_excel \
    mi_ppt mi_font mi_code mi_web mi_ebook mi_nn mi_bin mi_apk mi_lib)

get_file_type() {

    if [ $# -ne 1 ];then
        echo "others"
        return 0 
    fi

    ext_name=${1}

    for ff in ${mi_all_ff[@]}
    do
        eval ffs=\( \${${ff}[@]} \)
        for i in ${ffs[@]}
            do
                if [ "${ext_name}" == "${i}" ]; then
                    echo "${ff:3}"
                    return 0
                fi
            done
    done

    echo "others"
    return 0 
}
```

## 文件处理

在核心的文件类型获取功能实现之后就可以处理文件了。主要思路是获取文件类型，判断结果目录是否存在，最后移动文件，代码如下：

```bash
mi_process_file() {

    fn=$(basename ${1})
    ext_name=${1##*.}
    ft=$(get_file_type ${ext_name,,})

    mi_info "${1} -> ${2}/${ft}"

    ls ${2}/${ft} > /dev/null 2>&1 || mkdir -p ${2}/${ft}
    mv ${1} ${2}/${ft}
}
```

## 自定义后处理脚本

在文件移动之后我还想做一些其他的操作，比如把所有的压缩文件解压缩，或者把所有的图像上传到图床，所以添加了一个可以后处理的操作。在处理之后脚本会检测名为 mi_${type}_post的函数是否存在，如果存在那么就用这个函数做后处理，代码如下

```bash
mi_process_file() {

    fn=$(basename ${1})
    ext_name=${1##*.}
    ft=$(get_file_type ${ext_name,,})

    mi_info "${1} -> ${2}/${ft}"

    ls ${2}/${ft} > /dev/null 2>&1 || mkdir -p ${2}/${ft}
    mv ${1} ${2}/${ft}

    if [ "$(type -t mi_${ft}_post)" == function ]; then

        pushd ${2}/${ft} > /dev/null 2>&1
            mi_info "Post process ${fn} with mi_${ft}_post!"
            mi_${ft}_post ${fn}
        popd > /dev/null 2>&1

    fi
}
```

## 参数解析

作为一个程序需要能够解析从终端输入的参数，我使用的是getopts命令：

```bash
parse_arg() {

    while getopts :vVhRnd: opt
    do
    case "$opt" in
        v) let log_level=log_level+1 ;;
        d) out_path=$OPTARG ;;
        n) let process_dir=0 ;;
        R) let recursion=1 ;;
        V) help ;;
        h) help ;;
        *) ;;
    esac
    done

    shift $[ $OPTIND - 1 ]

    for param in "$@"
    do
        all_todo+="${param} "
    done
}
```

# 如何配置

因为整个脚本才200多行，所以要修改配置直接修改文件即可。如果要添加一个新的类型和新的文件扩展名只需要这样添加即可：

```diff
mi_compress=(zip tar gz 7z tgz arc arj taz \
    lha lz4 lzh lzma tlz tzo t7z z dz \
    lrz lz lzo xz zst tzst bz2 bz tbz tbz2 \
    iso)
... ...
mi_lib=(so a)
+mi_test=(test1 test2)

mi_all_ff=(mi_compress mi_image mi_video mi_audio mi_doc mi_excel \
-    mi_ppt mi_font mi_code mi_web mi_ebook mi_nn mi_bin mi_apk mi_lib mi)
+    mi_ppt mi_font mi_code mi_web mi_ebook mi_nn mi_bin mi_apk mi_lib mi_test)
```

如果要实现某种类型的后处理，直接在文件中添加相关的函数即可：

```diff
mi_test_post() {
	echo "This post process of test type"
}
```

如果要修改默认的文件存放位置，直接修改变量config_def_path即可。
