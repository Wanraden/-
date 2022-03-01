# 打造vim成为一个强大的IDE

## 一、实现效果(强大IDE，如同sourceinsight)

* **自动列出一个文件的函数和变量**  
* **查找函数和变量的定义**  
* **查找哪些函数调用了该函数和变量**  
* **高亮显示**  
* **自动补全**  

## 二、搭建步骤

&ensp;&ensp;搭建过程主要是插件的安装、配置及对应快捷键的定义和使用  

### 1.Vundle  

* 插件管理器[Vundle]  
  * 1.在.vimrc中跟踪和管理插件  
  * 2.安装特定格式的插件
  * 3.更新特定格式插件
  * 4.通过插件名称搜索Vim scripts中的插件
  * 5.清理未使用的插件
  * 6.可以通过单一按键完成以上操作
  * **注：Vundle是基于git的，所以需要首先安装git**
* 安装  

```vim
  $ git clone https://github.com/VundleVim/Vundle.vim.git ~/.vim/bundle/Vundle.vim  
```

* 配置  

```vim
set nocompatible              " be iMproved, required
filetype off                  " required

" set the runtime path to include Vundle and initialize
" 设置运行时路径
set rtp+=~/.vim/bundle/Vundle.vim
call vundle#begin()
" alternatively, pass a path where Vundle should install plugins
" 也可以通过一个路径来确定安装插件的位置
"call vundle#begin('~/some/path/here')
" 设置安装插件的路径

" let Vundle manage Vundle, required
" 使用Vundle管理
Plugin 'VundleVim/Vundle.vim'

" The following are examples of different formats supported.
" 支持不同方式的安装插件
" Keep Plugin commands between vundle#begin/end.
" plugin on GitHub repo
" 方式1，插件在github上非vim-scripts用户下的仓库，"用户名/repo名"
" 安装vim中的git插件
Plugin 'tpope/vim-fugitive'

" plugin from http://vim-scripts.org/vim/scripts.html
" 方式2，插件在github上vim-scripts仓库，"repo名"
" Plugin 'L9'

" Git plugin not hosted on GitHub
" 方式3，插件在其它git库上
Plugin 'git://git.wincent.com/command-t.git'

" git repos on your local machine (i.e. when working on your own plugin)
" 方式4，自己开发的插件
Plugin 'file:///home/gmarik/path/to/plugin'

" The sparkup vim script is in a subdirectory of this repo called vim.
" Pass the path to set the runtimepath properly.
" 插件在子目录下
" 修改运行时的路径
Plugin 'rstacruz/sparkup', {'rtp':'vim/'}
" Install L9 and avoid a Naming conflict if you've already installed a
" different version somewhere else.
" 修改名字
" Plugin 'ascenator/L9', {'name':'newL9'}

" All of your Plugins must be added before the following line
" 添加Plugin必须在调用下面行之前
call vundle#end()            " required
filetype plugin indent on    " required
" To ignore plugin indent changes, instead use:
"filetype plugin on
"
" Brief help
" 简要帮助
" :PluginList       - lists configured plugins - 列出已配置的插件
" :PluginInstall    - installs plugins; append `!` to update or just :PluginUpdate - 安装或更新插件
" :PluginSearch foo - searches for foo; append `!` to refresh local cache - 查找插件
" :PluginClean      - confirms removal of unused plugins; append `!` to auto-approve removal - 移除未使用的插件
"
" see :h vundle for more details or wiki for FAQ
" Put your non-Plugin stuff after this line
" 将非插件内容放在这一行后面
```

### 2.ctags

* 查找定义工具[ctags]  
  ctags全称：Generate tag files for source code  
  扫描指定的源文件，找出其中包含的语法元素，并把找到的相关内容记录下来，这样在代码浏览和查找时就可以利用这些记录实现查找和跳转功能  
* 安装  

```vim
  $ sudo apt-get install ctags  
```

* 使用  

```vim
  $ ctags -R . //递归扫描源代码根目录和所有子目录的文件并生成索引文件,生成tags文件
```

>**启动vim，加载tags**  
>:set tags=tags    //加载tags  
>:Tlist            //打开列表  
>**快捷键**  
>Ctrl+]：跳转到光标处的函数或者变量的定义所在的地方  
>Ctrl+T：返回到跳转之前的地方  

### 3.cscope

* 查找被调用的地方或者引用的地方[cscope]  
  ctags可以跳转到标签定义的地方，但是如果想查找函数在哪里被调用过或者标签在哪些地方出现过，那么ctags就无能为力了  
* 安装  

```vim
  $ sudo apt-get install cscope
```

* 使用  

```vim
  $ cscope -Rbq //生成索引库cscope.out
  R:表示把所有子目录里的文件也建立索引
  b:表示cscope不启动自带的用户界面，而仅仅建立符号数据库
  q:生成cscope.in.out和cscope.po.out文件，加快cscope的索引速度
```

>**启动vim，加载cscope**  
>:cscope add cscope.out  
>**帮助**  
>:cs help  
>**搜索**  
>:cs find option search  
>s:查找C语言符号，即查找函数名、宏、枚举值等出现的地方  
>g:查找函数、宏、枚举等定义的位置，类似ctags所提供的功能  
>d:查找本函数调用的函数  
>c:查找调用本函数的函数  
>t:查找指定的字符串  
>e:查找egrep模式，相当于egrep功能，但查找速度快多了  
>f:查找并打开文件，类似vim的find功能  
>i:查找包含本文件的文件  

>**.vimrc配置**  

```vim
    if has("cscope")
        set csprg=/usr/bin/cscope
        set csto=1
        set cst
        set nocsverb
        " 在当前路径添加数据库
        if filereadable("cscope.out")
        cs add cscope.out
        endif
        set csverb
    endif
    :set cscopequickfix=s-,c-,d-,i-,t-,e-
```

>**快捷键**  

```vim
    " 查找c符号，即查找函数名、宏、枚举值等出现的地方
    nmap <silent> <F5> :cs find s <C-R>=expand("<cword>")<CR><CR> 
    " 查找指定字符串
    nmap <silent> <F6> :cs find t <C-R>=expand("<cword>")<CR><CR>
    " 查找调用本函数的函数
    nmap <silent> <F7> :cs find c <C-R>=expand("<cword>")<CR><CR> 
```

### 4.Tagbar  

* 生成大纲[Tagbar]  
  Tagbar插件可以把源代码文件生成一个大纲，包括类、方法、变量以及函数名等，可以选中并快速跳转到目标位置  
  <font color=red>*注：Tagbar依赖ctags</font>*
* 安装  

```vim
    Plugin 'majustsushi/tagbar' " 重启vim，执行:PluginInstall，完成安装
    " 设置tagbar位置在左边
    let g:tagbar_left=1
    " 设置tagbar宽度
    let g:tagbar_width=25
    " 打开常见的源代码文件时会自动打开Tagbar插件
    auto BufReadPost *.cpp,*.c,*.h,*.cc,*.cxx call tagbar#autoopen()
```

* 使用  

 ```
   :TagbarToggle //反转Tagbar开关
   " -------- general ----------
   " <CR>:回车跳到定义位置
   " p:跳到定义位置，但光标仍然在tagbar原位置
   " P:打开一个预览窗口显示标签内容，如果在标签处回车跳到vim编辑页面内定义处，则预览窗口关闭
   " <C-N>:跳到下一个标签页的顶端
   " <C-P>:跳到上一个（或当前）标签页的顶端
   " <Space>:底行显示标签原型
   "
   " ---------- Folds ----------
   " +:展开标签内容
   " -:折叠标签内容
   " *:展开所有标签
   " =:折叠所有标签
   " o:在折叠与展开间切换，按o键，折叠标签，再按一次o，则展开标签，如此来回切换
   "
   " ---------- Misc -----------
   " s:切换排序，一次s，则让标签按字典排序，再一次s，则按照文件内出现 顺序排序
   " x:是否展开tagbar标签栏，x展开，再一次x，则缩小标签栏
   " <F1>:切换快捷键帮助页面，F1一次出现快捷键帮助页面，在一次F1，快捷键帮助页面隐藏
 ```

### 5.NerdTree

* 显示树形目录[NerdTree]  
* 安装  

```vim
  Plugin 'scrooloose/nerdtree' " 重启vim，执行:PluginInstall，完成安装
  autocmd StdinReadPre * let s:std_in=1
  autocmd VimEnter * if argc()==0 && !exists("s:std_in")  |  NERDTree | endif
  let NERDTreeWinSize=15
  let NERDTreeShowLineNumbers=1
  let NERDTreeAutoCenter=1
  let NERDTreeShowBookMarks=1
```

* 使用  

```vim
  :NERDTree //打开目录
  :q //在目录中执行，关闭目录
```

### 6.ale

* 动态语法检测插件[ale]  

```
动态语法检测插件可以在编写代码的过程中检测出语法错误，不用等到编译或者运行，这个工具对编写代码者非常有用  
ALE(Asynchronization Lint Engine)在发现有错误的地方会实时提醒，在vim的侧边会标注哪一行有错误，光标移动到这一行时，下面会显示错误的原因  
```

* 安装  

```vim
  Plugin 'w0rp/ale' " 重启vim，执行:PluginInstall，完成安装
  " 配置  
  let g:ale_sign_column_always=1
  let g:ale_sign_error='?'
  let g:ale_sign_warning='w'
  let g:ale_statusline_format=['? %d', '? %d', '? OK']
  let g:ale_echo_msg_format='[%linter%] %code: %%s'
  let g:ale_lint_on_text_changed='normal'
  let g:ale_lint_on_insert_leave=1
  let g:ale_c_gcc_options = '-Wall -O2 -std=c99'
  let g:ale_cpp_gcc_options = '-Wall -O2 -std=c++14'
  let g:ale_c_cppcheck_options=''
  let g:ale_cpp_cppcheck_options=''
  " 普通模式下，sp前往上一个错误或警告，sn前往下一个错误或警告
  nmap sp <Plug>(ale_previous_wrap)
  nmap sn <Plug>(ale_next_wrap)

```

### 7.YouCompleteMe

* 自动补全插件[YouCompleteMe]  
  YouCompleteMe插件需要vim的版本高于7.4.1578并且安装python2或者python3安装  
* 安装  

> * 安装

```vim
  Plugin 'Valloric/YouCompleteMe' " 重启vim，执行:PluginInstall，完成安装
```

> * 安装完成，需要重新编译，在编译之前需要保证已经安装如下软件包

```vim
  $ sudo apt-get install build-essential cmake python-dev python3-dev
  $ cd ~/.vim/bundle/YouCompleteMe  
  $ ./install.py --clang-completer //--clang-completer表示对c/c++的支持
```

> * 编译完成之后，还需要一些配置工作  

```vim
  $ cp ~/.vim/bundle/YouCompleteMe/third_party/ycmd/examples/.ycm_extra_conf.py ~/.vim
  let g:ycm_server_python_interpreter='usr/bin/python'
  let g:ycm_global_ycm_extra_conf='~/.vim/.ycm_extra_conf.py'
```

### 8.vim-gutentags

* 异步生成tags索引  
  在旧版本vim中不支持异步模式，因此每次写一部分代码需要手动运行ctags命令，来生成索引，这是vim的一大痛点，这个问题在vim8之后得到了改善  
* 安装  

```vim
  Plugin 'ludovicchabant/vim-gutentags' " 重启vim，执行:PluginInstall，完成安装
```

* 配置  

```vim
  " 搜索工程目录的标志，碰到这些文件/目录停止向上一级目录递归
  let g:gutentags_project_root=['.root', '.svn', '.git', '.hg', '.project']
  " 配置ctags参数
  let g:gutentags_ctags_extra_args=['--fields=+niazS', '--extra=+q']
  let g:gutentags_ctags_extra_args=['--c++-kinds=+px']
  let g:gutentags_ctags_extra_args=['--c-kinds=+px']
```

### 9.编译内核  

* 内核支持ctags和cscope生成索引文件，而且会根据编译的config文件选择需要扫描的文件

```vim
$ make tags cscope TAGS  //生成tags，cscope.out等索引文件
```

### 10.折叠代码

* 使用  

```
vim提供6种方法来选定折叠：
  1.manual  手工定义折叠         
  2.indent  更多的缩进表示更高级别的折叠         
  3.expr    用表达式来定义折叠         
  4.syntax  用语法高亮来定义折叠         
  5.diff    对没有更改的文本进行折叠         
  6.marker  对文中的标志折叠
  注:需在普通模式下折叠

设置折叠(foldmethod)方式
set fdm=indent
```

```
indent 对应的折叠代码:
  zc  折叠
  zC  对所在范围内所有嵌套的折叠点进行折叠
  zo  展开折叠
  zO  对所在范围内所有嵌套的折叠点展开
  [z  到当前打开的折叠的开始处。
  ]z  到当前打开的折叠的末尾处。
  zj  向下移动。到达下一个折叠的开始处。关闭的折叠也被计入。
  zk  向上移动到前一折叠的结束处。关闭的折叠也被计入
  注：使用时在大括号中间输入以上命令
```

```
marker对应的折叠代码：
  需要用标计来标识代码的折叠，系统默认是{{{和}}}，最好不要改动
  可以使用下面的命令来创建和删除折叠：
  zf  创建折叠，比如在marker方式下：
    zf56G，创建从当前行起到56行的代码折叠；
    10zf或10zf+或zf10↓，创建从当前行起到后10行的代码折叠。
    10zf-或zf10↑，创建从当前行起到之前10行的代码折叠。
    在括号处zf%，创建从当前行起到对应的匹配的括号上去（（），{}，[]，等）。
  zd  删除 (delete) 在光标下的折叠。仅当 'foldmethod' 设为"manual" 或 "marker" 时有效。
  zD  循环删除 (Delete) 光标下的折叠，即嵌套删除折叠。仅当 'foldmethod' 设为 "manual" 或 "marker" 时有效。
  zE  除去 (Eliminate) 窗口里“所有”的折叠。仅当 'foldmethod' 设为 "manual" 或 "marker" 时有效。 
```

### 11.buffers

* 多窗格显示  

```vim
buffers操作
  :buffers 查看缓冲区
  :buffer 1(文件名) 切换缓冲区
  :bnext
  :bprevious
  :blast
  :bfirst
  :badd test 添加到缓冲区
  :bdelete test 从缓冲区中移除bv
  :buffer 1（test 文件名）多窗口显示（在NERDTree窗口）  
状态
  - （非活动的缓冲区）
  a （当前被激活缓冲区）    #指光标所在的缓冲区
  h （隐藏的缓冲区）
  % （当前的缓冲区）        #是指当前窗口可见的缓冲区，因为可以分割窗口，可能有多个
  # （交换缓冲区）          # 代表轮换文件，按 <C - ^> 可以在当前，怎么指定轮换？
  = （只读缓冲区）
  + （已经更改的缓冲区）
```

* 配置  

```vim
  " vimrc中配置
  nmap <C-b>n  :bnext<CR> 同时按下Ctrl+b+n
  nmap <C-b>p  :bprev<CR> 同时按下Ctrl+b+p
```

### 12.bufexplorer

* 打开历史文件列表以达到快速切换文件的目的  
* 配置  

```
  let g:bufExplorerDefaultHelp=0       " Do not show default help
  let g:bufExplorerShowRelativePath=1  " Show relative paths
  let g:bufExplorerSortBy='mru'        " Sort by most recently used
  let g:bufExplorerSplitRight=0        " Split left
  let g:bufExplorerSplitVertical=1     " Split vertically
  let g:bufExplorerSplitVertSize = 30  " Split width
  let g:bufExplorerUseCurrentWindow=1  " Open in new window
```

### 13.WinManager

* 管理文件浏览器、缓冲区、taglist等窗口  
* TBD

### 14.NerdCommenter

* 注释插件  
* TBD

### 15.Molokai

* 配色插件  
* TBD

### 16.Ctrlp/LeaderF

* 模糊搜索  

### 17.taglist

* TagList插件,是一款基于ctags,在vim代码窗口旁以分割窗口形式显示当前的代码结构概览,增加代码浏览的便利程度的vim插件  
  *注：taglist依赖ctags*
* 安装

```vim
  Plugin 'vim-scripts/taglist.vim' " 重启vim，执行:PluginInstall，完成安装
```

* 使用  

```
  " vimrc中配置
  let Tlist_Show_One_File=1     " 不同时显示多个文件的tag，只显示当前文件的
  let Tlist_Exit_OnlyWindow=1   " 如果taglist窗口是最后一个窗口，则退出vim
  let Tlist_Use_Left_Window=1   " 在左侧侧窗口中显示taglist窗口
  let Tlist_Auto_Open=1         " 默认是开启

  :Tlist //打开taglist
  :q //在taglist中，关闭taglist
```

```
  <CR>          跳到光标下tag所定义的位置，用鼠标双击此tag功能也一样
  o             在一个新打开的窗口中显示光标下tag
  <Space>       显示光标下tag的原型定义
  u             更新taglist窗口中的tag
  s             更改排序方式，在按名字排序和按出现顺序排序间切换
  x             taglist窗口放大和缩小，方便查看较长的tag
  +             打开一个折叠，同zo
  -             将tag折叠起来，同zc
  *             打开所有的折叠，同zR
  =             将所有tag折叠起来，同zM
  [[            跳到前一个文件
  ]]            跳到后一个文件
  q             关闭taglist窗口
  <F1>          显示帮助
```

### 18.其它

* TBD  


[Vundle]:http://github.com/VundleVim/Vundle.vim
[ctags]:\\
[cscope]:\\
[Tagbar]:\\
[NerdTree]:\\
[ale]:\\
[YouCompleteMe]:\\

