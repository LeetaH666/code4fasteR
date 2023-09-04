# 在 VSCode 上使用 R 遇到的问题和解决方案

## 安装

### 如何在服务器（Ubuntu）上配置 R 环境

使用 `conda create -n envName r-essentials r-base` 即可创建新的 R 环境，在这个 R 环境下键入 `R` 即可进入 R 终端，使用 `Rscript fileName` 即可执行 R 文件。

### 如何在 VSCode 上写 R 程序

需要安装 languageserver 包以及 R 插件，可以使用 `conda install -c r r-languageserver` 安装，也可以在 R 终端中通过 `install.packages("languageserver")` 安装（优先前者）。而 R 插件需要修改设置中的 `R > Rpath: Linux` 和 `R > Rterm: Linux` 为 conda 中的 R，通常路径为 `/home/userName/anaconda3/envs/envName/bin/R`，注意不能使用 `~/anaconda3/...` 这种相对路径，亲测无效。

#### R LSP 报错

完成以上工作后也许还是有问题，R Language Server 可能会报错，说 icu 没有找到，这是因为系统上自带的 icu 版本与 stringi 这个包不匹配，只需要在 R 终端中重新安装 stringi 即可。

### 如何在 conda 环境中安装 R 包

在命令行中键入 `conda install -c r r-RPackageName`，其中 `RPackageName` 为你想装的包的名字。

#### 下载 R 包很慢或者下载不了

使用国内镜像，比如清华镜像：

- https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge
- https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/msys2/
- https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/r/
- https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/

命令行键入 `conda config --add channels URL`，即可添加镜像源，`URL` 就是上面的各个链接。

## 运行

### 如何运行 R 代码

运行 R 代码只需在右上角找到三角符号（Run Source）点击即可，注意需要在代码文件所在的目录下运行。在这种运行方式下，左边的 R Workspace 才可以查看变量和图片。

如果不需要用 R Workspace，在命令行直接键入 `Rscript yourRCode.R` 也是可以的。

如果习惯用 jupyter，则需要在 R 所在的 conda 环境下安装 `r-irkernel`，然后键入 `R` 进入 R 终端，在终端中输入 `IRkernel::installspec()`，此时才能在新建的 jupyter notebook 中找到对应的 R kernel。

### 如何取消 lintr（取消代码中各种波浪线提示）

在用户文件夹（`~`）下新建一个 `.lintr` 文件，声明一个空的 linters：

```c
linters: list()
```

修改完 `.lintr` 文件需要 reload 一下窗口才会生效。

如果不想去掉所有 linter，还想保留一些，或者自定义一些，可以按如下方式声明默认 linters：

```c
linters: linters_with_defaults(
            line_length_linter(999),
            infix_spaces_linter = NULL,
            single_quotes_linter = NULL,
            trailing_blank_lines_linter = NULL
                    )
```

> 其中 `line_length_linter(999)` 是设置每行代码最大长度为 `999`，也可以像其他 linter 那样直接设成 `NULL`，具体的函数说明见官网。

> ！注意：不要像写 python 那样在最后还加个 `,`，这样会导致报错。

### 在 Jupyter 中输出变量的格式很奇怪

在 Jupyter 中写 R 代码时，可以只写变量名然后运行，达到输出变量的效果。然而不同的 VSCode presentation 模式会导致不同的输出结果，想要得到正常的输出结果应该使用 `text/plain` 模式（图片的话是 `image/png` 模式），在 Jupyter 中随便一个 cell 的 `···` 处修改即可。

但是 VSCode 默认是 `text/html` 优先，每次打开文件需要重新修改 presentation 的模式未免太过麻烦，一个解决方案是使用 `Ctrl + ,` 打开设置，搜索 `displayOrder`，根据你想设置的优先级设置，比如

- `image/png`
- `text/plain`
- `text/html`