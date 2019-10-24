# nmcli dev wifi connect "ssid" password "psw" name "name"
nmcli dev wifi 后面可以接tab

# 系统安装
1. 分个EXT2的分区，FAT32不行，一旦ISO文件大点，FAT32就装不下，ntfs文件系统也不行，linux内核识别不料ntfs。
2. 然后把iso文件复制到EXT2分区中,并且把vmlinuz和initrd.img复制出来
3. 然后用easyBCD工具创建winGrub，编写配置文件:
    linux (hd0,3)/vmlinuz repo=hd:/dev/sda4
    initrd (hd0,3)/initrd.img


# vim
## 剪切复制粘贴
1. 按v，移动光标选中
2. d是剪切，y是复制
3. p粘贴

从系统ctrl+c的，得安shift+insert

u是撤销

*:reg 字符寄存器

