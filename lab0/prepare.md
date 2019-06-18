在ubuntu14.04 32bit上实验,安装以下工具：
  - 安装nasm，用来编译汇编文件
  - 安装bochs，模拟器

实现书上的第一个例子
- 在bochs安装文件夹下使用./bximage创建a.img
  - 软盘fd
  - 其他选项默认
- 创建文件夹/home/cheng/oscheng，作为存储实验代码的文件夹；
- 创建boot.asm文件
```
  org	07c00h			; 告诉编译器程序加载到7c00处
	mov	ax, cs
	mov	ds, ax
	mov	es, ax
	call	DispStr			; 调用显示字符串例程
	jmp	$			; 无限循环
DispStr:
	mov	ax, BootMessage
	mov	bp, ax			; ES:BP = 串地址
	mov	cx, 16			; CX = 串长度
	mov	ax, 01301h		; AH = 13,  AL = 01h
	mov	bx, 000ch		; 页号为0(BH = 0) 黑底红字(BL = 0Ch,高亮)
	mov	dl, 0
	int	10h			; 10h 号中断
	ret
BootMessage:		db	"Hello, OS world!"
times 	510-($-$$)	db	0	; 填充剩下的空间，使生成的二进制代码恰好为512字节
dw 	0xaa55				; 结束标志
```
- 使用命令编译汇编文件：nasm boot.asm -o boot.bin
- 将创建的a.img文件复制到当前文件夹下，并编写bochsrc文件，注意，其中romimage，vgaromimage的路径与自己的bochs编译结果有关，可观察其Makefile或者去相关路径下查找
```
###############################################################
# Configuration file for Bochs
###############################################################

# how much memory the emulated machine will have
megs: 32

# filename of ROM images
romimage: file=/usr/local/share/bochs/BIOS-bochs-latest
vgaromimage: file=/usr/local/share/bochs/VGABIOS-lgpl-latest

# what disk images will be used
floppya: 1_44=a.img, status=inserted

# choose the boot disk.
boot: floppy

# where do we send log messages?
# log: bochsout.txt

# disable the mouse
mouse: enabled=0

# enable key mapping, using US layout as default.
#keyboard_mapping: enabled=1, map=/usr/share/bochs/keymaps/x11-pc-us.map
```
- 运行bochs -f bochsrc

！[]（lab0/1.png）
