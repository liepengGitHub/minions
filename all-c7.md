# CENTOS7
## nmcli dev wifi connect "ssid" password "psw" name "name"
nmcli dev wifi 后面可以接tab

之后就可以用nmcli connection up "name" 启动了
## sudo nmcli connection up liepengiphone
## 系统安装
1. 分个EXT2的分区，FAT32不行，一旦ISO文件大点，FAT32就装不下，ntfs文件系统也不行，linux内核识别不料ntfs。
2. 然后把iso文件复制到EXT2分区中,并且把vmlinuz和initrd.img复制出来
3. 然后用easyBCD工具创建winGrub，编写配置文件:
    linux (hd0,3)/vmlinuz repo=hd:/dev/sda4
    initrd (hd0,3)/initrd.img
## windows下安装grub2在mbr
因为centos7使用的文件系统是xfs，而且win下的easybcd识别不到xfs文件系统，所以只能手动安装grub2，用grub2来引导centos7
先从GNU官网下载grub for windows ，grub-install后面接的是设备ID，得用命令wmin来得到设备ID
重启进入GRUB后，手动引导CENTOS7
```
insmod xfs
set root=(hd0,6) 
linux16 /vmlinuz-xxxx root=/dev/mapper/centos-root
initrd16 /initramfs-xxx.img
boot
```
不用加16也可以

在grub中引导win7，用以下命令
insmod ntfs
set root=(hd0,1)
chainloader +1
boot
## 加阿里源
[root@localhost ~]# cd /etc/yum.repos.d/
[root@localhost yum.repos.d]# wget http://mirrors.aliyun.com/repo/epel-7.repo
显示源上有没有要找的软件
yum list ntfs*

安装Flash
配置 yum 源 
    sudo rpm -ivh http://linuxdownload.adobe.com/adobe-release/adobe-release-x86_64-1.0-1.noarch.rpm
    安装flash 
    sudo yum install flash-plugin

## Gnome的配置工具gconftool-2
### 列出目录和子目录的所有健值
gconftool-2 --recursive-list /desktop/gnome
### 删除终端健值
gconftool-2 --recursive-unset /apps/gnome-terminal

## rm -f /var/run/yum.pid

## 编译个程序，依赖问题搞到头大，还不如自己写一个呢，花那么多时间
## configure时，注意需要的是xxx-devel依赖，一般机子上装的是xxx，类似与java的jre和jdk
## dircolors
dircolors是一个配置ls命令的命令
eval `dircolors 配置文件`

## liunx目录
/opt   主机额外安装软件所摆放的目录。默认是空的。 一般安装软件的时候，可以自己指定安装到这个目录下，便于查找和管理


## gnome-terminal
ctrl + w 往回删除一个单词，光标放在最末尾
ctrl + u 删除光标以前的字符
ctrl + k 删除光标以后的字符
ctrl + a 移动光标至的字符头
ctrl + e 移动光标至的字符尾
ctrl + l 清屏


## 开放端口
iptables -L -n 查看目前开放端口情况
果是新 装的系统，应该只有22端口开放,而且没有/etc/sysconfig/iptables文件
办法如下
1、停止并屏蔽firewalld服务

systemctl stop firewalld
systemctl mask firewalld
2、安装iptables-services软件包

yum install iptables-services
3、在引导时启用iptables服务

systemctl enable iptables
4、启动iptables服务

systemctl start iptables
5、保存防火墙规则

service iptables save
或
/usr/libexec/iptables/iptables.init save

之后出现/etc/sysconfig/iptables文件



在 -A INPUT -p tcp -m state --state NEW -m tcp --dport 22 -j ACCEPT 后面追加需要开放端口的代码，比如8089:
-A INPUT -p tcp -m state --state NEW -m tcp --dport 8089 -j ACCEPT

保存后重启防火墙：

systemctl restart iptables


















          

# SHELL  
  
## 命令
 
### 将当前目录下所有文件按照索引从1开始递增重命名
i=1; for x in *; do mv $x $i.png; let i=i+1; done

### 去掉当前目录下文件名的空格
ls|while read i;do sudo mv "$i" $(echo $i|tr -d ' ') 2>/dev/null  ; done








# vim 
  
## 常用命令
  
### 复制
. 		重复上一次命令
N<command> 	重复命令N次
2dd		删除2行
3p		粘贴文本3次
100iprivate	写下"private private ..."100次

### 替换
```
:s/from/to/g	替换当前行所有的from为to
:.,$s/from/to/g 对当前行到最后一行的内容进行替换。
:1,.s/from/to/g 对第一行到当前行的内容进行替换。
:%s/from/to/g	针对全局
%s/^ *//	删除所有行首的多余空格
```

### 查找(一般是查找全文）
```
:/\v\<\/?\w+>	先匹配左尖括号 (\<)，再匹配可选的正斜杠 (\/?)，再匹配一个或多个单词型字符(\w+)，最后匹配分隔符 (>)，从而匹配到所有html标签。\v 表示使用正则表达式的 very magic 模式.

vim默认下正则是贪婪模式，想要非贪婪，可以把*代替为\{-}
```
### 删除
```
:g//d		由于 :g//d 命令没有指定命令要匹配的目标模式，因此将默认使用上一次用到的搜索目标 /\v\<\/?\w+>。
:v/href/d	可实现只保存包含 URL的所有文本行,v是反向选择
:g/^\s*$/d	删除所有空白行 
```

### 一般是先 /\v^\s* 查找到,然后再 :%s///g替换


### 移动光标
0		移动到行头，^ 移动到本行第一个非BLANK的字符
$		移动到行尾，g_
f		移动到下一个为a的字符处，t为字符前。大写FT为反方向
3fa		在当前行查找第三个出现的a
dt"		删除直到"字符前的所有内容
dT"		反方向删除知道"字符后的yyyy
NG		移动到第N行
gg		移动到第一行
G		移动到最后一行
w		移动到下一个单词开头
e		移动到下一个单词结尾,大写WE视空格分割单词
%		匹配括号移动，包括( { [ <
ctrl+O		回到上一次编辑后保存的地方，反则ctrl+I
<sp><cmd><ep>	移动光标和命令连动
0y$		从行头复制到行为
ye		从当前复制到下一个单词结尾
gUe		从当前大写到下一个单词结尾，u则是小写
v		在可视化里也可以使用移动光标命令选择字符窜

### 选择
针对字符串 (map (+) ("foo"))，光标在第一个o处
vi" → 会选择 foo.
va" → 会选择 "foo".
v2i) → 会选择 map (+) ("foo")
v2a) → 会选择 (map (+) ("foo"))

ctrl+v		进入块级选择模式，可通过hjkl选择，之后I（注意是大写）写入东西,之后按下ESC键，所选择的区域同时生效。
J		选择之后，J把所有选择的行连接起来，行与行之间空格
>		选择之后，缩进
=		自动缩进

### 录制宏
在一个只有一行且这一行只有“1”的文本中，键入如下命令：

qaYp<C-a>q→
	qa 开始录制
	Yp 复制行.
	<C-a> 增加1.
	q 停止录制.
@a → 在1下面写下 2
@@ → 在2 正面写下3
现在做 100@@ 会创建新的100行，并把数据增加到 103.

### 分屏
:split 		 创建上下分屏 (:vsplit创建左右分屏)
<C-w><dir> 	 dir就是方向，可以是 hjkl 或是 ←↓↑→ 中的一个，其用来切换分屏。
<C-w>_ (或 <C-w>|)  最大化尺寸 (<C-w>| 垂直分屏)
<C-w>+ (或 <C-w>-)  增加尺寸



### 提示ctrl+p
### ctrl+a 数字增加1




## 光标
i 	在当前光标处进行编辑
I 	在行首插入
A 	在行末插入
a 	在光标后插入编辑
o 	在当前行后插入一个新行
O 	在当前行前插入一个新行
cw 	替换从光标所在位置后到一个单词结尾的字符
## 剪切复制粘贴
1. 按v，移动光标选中
2. d是剪切，y是复制
3. p粘贴

从系统ctrl+c的，得安shift+insert

u是撤销

普通模式下输入Shift+zz即可保存退出vim

## vim 文本编码
1 :e ++enc=gb2312
2 :set noreadonly
```
  encoding  ：Vim 内部编码，例如 buffer、寄存器、文本等。这个值一般用户不要设置，另外打开 Vim 之后再设置这个值也是没有意义的。大家可以将这个值看作是 Vim 程序自己的变量，如果在工作中遇到文件的编码问题，和 encoding  这个变量是万万没有关系的。

fileencoding  ：顾名思义了，就是文件的编码。

此外还有一个值，叫 fileencodings 是个复数。一般我们将这个值在 vimrc 中设置，Vim 打开一个文件的时候回根据 fileencodings 里面设置的顺序来猜测文件的编码。比如这样设置：
		
	set fileencodings=ucs-bom,utf-8,cp936,gb18030,big5,euc-jp,euc-kr,latin1

	那么有时候 Vim 猜错了，打开的文件显示乱码怎么办呢？（ps：通常 Vim 打开文件的时候乱码是因为你的 fileencodings 里面没有写某个编码，所以 Vim 没有猜对。例如从上面的设置中删掉 gb18030 ，那么打开这种编码的文件的时候你会发现 fileencoding 的值是 latin1 ，而文件的显示是乱码）
	这时候你可能想到设置 fileencoding 的值，但是此时我们的文件已经打开了，你设置后会发现 Vim buffer 的状态变成了 Edited 。而文件依然显示乱码，没有变化。具体的原因后文会详细解释。

	正确的做法是 以特定编码重新打开文件 ，例如在 Vim 中使用重新打开命令 :e ++enc=gb2312 ，其中 ++enc 是一个选项，可以指定使用的编码。打开后你会发现 Vim 按照你指定的形式打开了文件，但是文件变成了 readonly 状态，如果要修改，设置 :set noreadonly 就好。
```

## vim自定义键盘映射  
  
使用:map命令，可以将键盘上的某个按键与Vim的命令绑定起来。例如使用以下命令，可以通过F5键将单词用花括号括起来：
:map <F5> i{e<Esc>a}<Esc>
其中：i{将插入字符{，然后使用Esc退回到命令状态；接着用e移到单词结尾，a}增加字符}，最后退至命令状态。在执行以上命令之后，光标定位在一个单词上（例如amount），按下F5键，这时字符就会变成{amount}的形式。


## VIM的配置文件路径在哪？
vim --version
系统 vimrc 文件: "$VIM/vimrc"
用户 vimrc 文件: "$HOME/.vimrc"
第二用户 vimrc 文件: "~/.vim/vimrc"
这三个配置文件，前面的会覆盖掉后面的，而 ~/.vimrc 和 ~/.vim/vimrc 都是用户配置文件。

*:reg 字符寄存器

对于网卡接受到的帧数据，系统是不会直接输出到一个文件中的，因为安全原因吧，内核1、分配/初始化/注册网络设备；
2、完成PCI设备的I/O区域的分配和映射，以及完成硬件的其它初始化工作 后，只暴露函数API给外界访问

## vim插件
  
### 安装
1. git clone https://github.com/VundleVim/Vundle.vim.git ~/.vim/bundle/Vundle.vim
2. 写进vimrc：
	set nocompatible " be iMproved, required
	filetype off " required
	set rtp+=~/.vim/bundle/Vundle.vim
	call vundle#begin()
	Plugin 'VundleVim/Vundle.vim'

	call vundle#end()
	filetype plugin indent on
3. 在VIM中运行:
:PluginInstall
### plasticboy/vim-markdown md折叠、导航工具
```
zr: reduces fold level throughout the buffer
zR: opens all folds
zm: increases fold level throughout the buffer
zM: folds everything all the way
za: open a fold your cursor is on
zA: open a fold your cursor is on recursively
zc: close a fold your cursor is on
zC: close a fold your cursor is on recursively

]]	跳到下一个header
[[	跳到上一个header
]c	跳到当前header

```
### iamcco/markdown-preview.vim md预览工具
:MarkdownPreview
" 在打开 markdown 文件后，使用该命令可以打开预览窗口
:MarkdownPreviewStop
" 关闭 markdown 预览窗口，并停止开启的服务进程

可选配置：
markdown-preview.vim

let g:mkdp_auto_start= 1   "打开.md文件时 自动打开预览窗口

### sickill/vim-monokai vim的sublime配色
1、把配色文件放到.vim/colors下
2、vimrc :
syntax enable
colorscheme monokai

对颜色不满意可以自己改
背景色：将ctermbg改为256,变为纯黑色

系统自带的配色方案在以下目录
/usr/share/vim/vim74/colors/
blue.vim      default.vim  desert.vim   evening.vim  morning.vim  pablo.vim      README.txt  shine.vim  torte.vim
darkblue.vim  delek.vim    elflord.vim  koehler.vim  murphy.vim   peachpuff.vim  ron.vim     slate.vim  zellner.vim

跟改配色:colorscheme xxx

### scrooloose/nerdtree vim 目录树
目录树图

### artur-shaik/vim-javacomplete2 vim java补全工具
java 补全














# 开源站
东软 有eclipse
http://mirrors.neusoft.edu.cn/


# net
完成中继功能的节点通常称为中继系统


一个设备工作在哪一层，关键看它工作时
利用哪一层的数据头部信息。
网桥工作时，是以MAC头部来决定转发端口的，
因此显然它是数据链路层的设备。

物理层：
网卡，网线，集线器，中继器，调制解调器

数据链路层：网桥，交换机

网络层：路由器

网关工作在第四层传输层及其以上

## 网卡的数据接收

内核如何从网卡接受数据，传统的经典过程：
1、数据到达网卡；
2、网卡产生一个中断给内核；
3、内核使用I/O指令，从网卡I/O区域中去读取数据；

我们在许多网卡驱动中，都可以在网卡的中断函数中见到这一过程。

但是，这一种方法，有一种重要的问题，就是大流量的数据来到，网卡会产生大量的中断，内核在中断上下文中，会浪费大量的资源来处理中断本身。所以，一个问 题是，“可不可以不使用中断”，这就是轮询技术，所谓NAPI技术，说来也不神秘，就是说，内核屏蔽中断，然后隔一会儿就去问网卡，“你有没有数据 啊？”……

从这个描述本身可以看到，哪果数据量少，轮询同样占用大量的不必要的CPU资源，大家各有所长吧，呵呵……

OK，另一个问题，就是从网卡的I/O区域，包括I/O寄存器或I/O内存中去读取数据，这都要CPU去读，也要占用CPU资源，“CPU从I/O区域 读，然后把它放到内存（这个内存指的是系统本身的物理内存，跟外设的内存不相干，也叫主内存）中”。于是自然地，就想到了DMA技术——让网卡直接从主内 存之间读写它们的I/O数据，CPU，这儿不干你事，自己找乐子去：
1、首先，内核在主内存中为收发数据建立一个环形的缓冲队列（通常叫DMA环形缓冲区）。
2、内核将这个缓冲区通过DMA映射，把这个队列交给网卡；
3、网卡收到数据，就直接放进这个环形缓冲区了——也就是直接放进主内存了；然后，向系统产生一个中断；
4、内核收到这个中断，就取消DMA映射，这样，内核就直接从主内存中读取数据；





——呵呵，这一个过程比传统的过程少了不少工作，因为设备直接把数据放进了主内存，不需要CPU的干预，效率是不是提高不少？

对应以上4步，来看它的具体实现：
1、分配环形DMA缓冲区
Linux内核中，用skb来描述一个缓存，所谓分配，就是建立一定数量的skb，然后把它们组织成一个双向链表；

2、建立DMA映射
内核通过调用
dma_map_single(struct device \*dev,void \*buffer,size_t size,enum dma_data_direction direction)
建立映射关系。
struct device \*dev，描述一个设备；
buffer：把哪个地址映射给设备；也就是某一个skb——要映射全部，当然是做一个双向链表的循环即可；
size：缓存大小；
direction：映射方向——谁传给谁：一般来说，是“双向”映射，数据在设备和内存之间双向流动；

对于PCI设备而言（网卡一般是PCI的），通过另一个包裹函数pci_map_single，这样，就把buffer交给设备了！设备可以直接从里边读/取数据。

3、这一步由硬件完成；

4、取消映射
dma_unmap_single，对PCI而言，大多调用它的包裹函数pci_unmap_single，不取消的话，缓存控制权还在设备手里，要调用它，把主动权掌握在CPU手里——因为我们已经接收到数据了，应该由CPU把数据交给上层网络栈；

当然，不取消之前，通常要读一些状态位信息，诸如此类，一般是调用
dma_sync_single_for_cpu()
让CPU在取消映射前，就可以访问DMA缓冲区中的内容。

关于DMA映射的更多内容，可以参考《Linux设备驱动程序》“内存映射和DMA”章节相关内容！

OK，有了这些知识，我们就可以来看e100的代码了，它跟上面讲的步骤基本上一样的——绕了这么多圈子，就是想绕到e100上面了，呵呵！


在e100_open函数中，调用e100_up，我们前面分析它时，略过了一个重要的东东，就是环形缓冲区的建立，这一步，是通过
e100_rx_alloc_list函数调用完成的





# javascript
## 基础
### function

函数是对象，是function类型,既然是对象就有属性、方法和原型。
函数能创造对象。
函数里面有 this。

typeof Object 输出的也是"function"

#### 匿名函数的执行
function(){
}
上面诗歌匿名函数，执行不了，要匿名函数执行，要像下面那样
(function(){})

#### 函数的方法 函数名.xx()
```javascript
function fun(){}
```
1 fun.string
输出的是 ƒ toString() { [native code] }
fun.string()
输出的是 "function fun(){}"

2 fun.name 属性 输出函数名

3 fun.arguments 属性 输出参数数组
引用 arguments[0]
赋值 arguments[0]="aa"
arguments对象不是一个 Array 。它类似于Array，但除了length属性和索引元素之外没有任何Array属性。
```javascript
console.log(typeof arguments);    // 'undefined'
// arguments 对象只能在函数内使用
function test(a){
    console.log(a,Object.prototype.toString.call(arguments));
    console.log(arguments[0],arguments[1]);
    console.log(typeof arguments[0]);
}
    test(1);
    /*
    1 "[object Arguments]"
    1 undefined
    number
    */
```

将arguments转为数组
var args = Array.prototype.slice.call(arguments);

4 apply call this bind
apply call bind都是用来改变函数体内this的指向
```javascript
var func = function(arg1, arg2) {
     
};

func.call(this, arg1, arg2);
func.apply(this, [arg1, arg2])

//指定this来替换func里的this，然后从头跑完函数
//apply和call的区别是接受参数的形式不一样

//应用
console.log();只能输出一个参数，如果要输出多个参数可以
function log(){
  console.log.apply(console, arguments);
  };
  log(1);    //1
  log(1,2);    //1 2
//如果要给log添加前缀，可以
function log(){
  var args = Array.prototype.slice.call(arguments);
    args.unshift('(app)');
     
       console.log.apply(console, args);
       };
```

bind
```
var altwrite = document.write;
altwrite("hello");
```
这样会报错，因为此时altwrite里边的this指向了window,应该
```
altwrite.bind(document)("hello")
```

应用
在常见的单体模式中，通常我们会使用 \_this , that , self 等保存 this ，这样我们可以在改变了上下文之后继续引用到它
```
var foo = {
    bar : 1,
        eventBind: function(){
	        var _this = this;
		        $('.someClass').on('click',function(event) {
			            /* Act on the event */
				                console.log(_this.bar);     //1
						        });
							    }
							    }
```
也没有什么问题。当然使用 bind() 可以更加优雅的解决这个问题
```
var foo = {
    bar : 1,
        eventBind: function(){
	        $('.someClass').on('click',function(event) {
		            /* Act on the event */
			                console.log(this.bar);      //1
					        }.bind(this));
						    }
						    }

```

apply call bind 的异同
```
var obj = {
    x: 81,
    };
     
     var foo = {
         getX: function() {
	         return this.x;
		     }
		     }
		      
		      console.log(foo.getX.bind(obj)());  //81
		      console.log(foo.getX.call(obj));    //81
		      console.log(foo.getX.apply(obj));   //81
```
apply 、 call 、bind 三者都是用来改变函数的this对象的指向的；
apply 、 call 、bind 三者第一个参数都是this要指向的对象，也就是想指定的上下文；
apply 、 call 、bind 三者都可以利用后续参数传参；
bind 是返回对应函数，便于稍后调用；apply 、call 则是立即调用 。


#### 函数和对象的区别
```javascript
function Test (word) {
    console.log (word);
    }
     
     Test('哈哈，我是函数');
      
      new Test('哈哈，我是对象');
       
        
	//将以上的调用方式换种通俗易懂的方式
	 
	 Test.call("哈哈，我是函数");     //相当于Test();
	  
	  //相当于new Test();
	  var obj = {};
	  obj._proto_ = Test.prototype;
	  Test.call(obj);
```
本质的区别就是，两次调用之中的this不同。调用Test('...');的时候，里面的this是顶级对象window，返回值是undefined。调用new Test('...');的时候，它会先new一个对象，置类型为Test，之后把它作为this执行Test函数，最后再把对象返回。

按自己的话来讲，只是上下文不同。
函数既可以作为一个部分存在与世界中（widnow），也可以作为一个世界装下许多部分。

对象是一个地址，指向一片有意义的内存区域。
内存区域中有指令，指令放到cpu中可以执行。

客观世界里，所有静态的物质都是对象，空间坐标就是地址，主体通过地址取得对象的指令放到主体中即可在主体中运行。



## typeof instanceof 

typeof运算符可以判断表达式的类型，根据表达式返回七种结果：number,string,boolean,undefined,object,function,symbol

数组返回的是object

正是由于typeof运算符对于任何对象都统一返回object，因此我们无法知道某个对象是否是数组，或者说是对象的一个实例。instanceof运算符用来弥补这一缺点







# 工具集

shell 
vim : markdown NERDTree javacomplete2
chrome
