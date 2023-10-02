 目录里是hi3798mv3dmm_hi3798mv300编译好的ch341串口驱动，其他驱动大同小异

## 编译步骤

### 1. 下载https://github.com/JasonFreeLab/HiSTBLinuxV100R005C00SPC050

### 2. 修改source/kernel/linux-4.4.y/include/linux/vermagic.h

    例如:替换 UTS_RELEASE 为 4.4.35_ecoo_xxxx ,删除MODULE_VERMAGIC_PREEMPT，或者直接写死VERMAGIC_STRING。

### 3. 生成 .config 复制到 source/kernel/linux-4.4.y

- 3.1 可以直接复制 source/kernel/linux-4.4.y/arch/arm(64)/configs 目录下对应的默认配置
- 3.2 或者 参照说明编译

```sh
source $sdk_path/env.sh
cp configs/$chip_type/xx_cfg.mak cfg.mak
......
make linux
```
    这一步目的主要是拿到out/hi3798mvxxxx/hi3798xxx/obj(64)/source/kernel/linux-4.4.y/.config

    注意如果是arm64，要在make menuconfig中修改。

### 4. 编译linux内核
    可以交叉编译（没试过），我是直接复制到设备里编译的。
    提取 source\kernel\linux-4.4.y 这个目录，复制到设备里。
```sh
make -j$(nproc)
```
    估计会有一堆warning，不用管，耐心等待结束。

### 5. 编译驱动
    例如: 编译ch341驱动，就把 drivers/usb/serial/ch341.c 复制出来
```sh
make -C /xxxx/linux-4.4.y M=/xxxx/ch341.c
```
    也可以尝试去官网下载最新的驱动编译，我试过，虽然能成功发送指令，但是设备必然会死机，不知为何。

### 6. 加载驱动

```sh
insmod ch341.ko
```
    放到 /usr/lib/modules/4.4.35_xxxx/kernel/drivers
    depmod -a
    查看
    dmesg | tail

### 7. 其他
    如果查看驱动发现报错
    Unknown symbol __dynamic_dev_dbg (err 0)
    可以注释掉 .config 里的 CONFIG_DYNAMIC_DEBUG

