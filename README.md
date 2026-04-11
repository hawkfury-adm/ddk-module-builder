# DDK 构建 GKI 内核的树内模块

## 用法

1. fork 本仓库，启用 actions  
2. 运行 Build Module ，填写你需要的内核分支（必须是 ddk 支持的）、要构建的树内模块的相对路径
   （如 net/netfilter/ipset）、需要传给 scripts/config 的选项（如 `-m CONFIG_IP_SET` 表示 `CONFIG_IP_SET=m`） 
3. 运行，等待成功后从 actions run 下载 modules ，包含构建的模块 .ko 的压缩包。

[DDK](https://github.com/Ylarod/ddk) 支持的内核分支：android12-5.10, android13-5.10, android13-5.15, android14-5.15, android14-6.1, android15-6.6, android16-6.12

## 加载模块

由于 GKI 内核存在[导出符号裁剪](https://blog.xzr.moe/archives/236/#section-3:~:text=CONFIG_TRIM_UNUSED_KSYMS)，因此不一定能直接用 insmod 加载。可以使用 `ksud debug insmod` 进行加载。非 KernelSU 用户可下载 KernelSU 最新 apk ，解压 lib/arm64-v8a/libksud.so 获取 ksud 。

## TODO

目前只能构建 ddk 中的源码的模块，可以考虑增加从其他仓库拉取源码，或者构建树外模块的支持。

## 使用该脚本构建内核模块主要需要修改以下三处

      module_path:
        description: 'Module relative path in kernel tree (e.g., net/unix or net/netfilter/ipset)'
        required: true
        default: ''

      configs:
        description: 'Options pass to scripts/config, e.g., -m CONFIG_UNIX_DIAG or -m CONFIG_IP_SET' # ./scripts/config --set-val CONFIG_IP_SET_MAX 65534
        required: false
        default: ''

make -C $SRC O=$OUT M=$SRC/net/netfilter/ipset M=$SRC/net/netfilter modules
