#! https://zhuanlan.zhihu.com/p/517479311
# 基于Multipass创建M1下的Linux开发环境

1. clang编译出现异常：使用M1 Mac Mini编译CPP项目，出现构造函数重载不确定的错误（ambiguous），而过往基于GNU GCC编译则不会出现该问题。因而考虑使用GCC进行编译。
2. M1安装GCC失败：brew install gcc最后出现依赖安装错误；考虑到可能的依赖问题，放弃从源代码安装的方式。
   ```
   ==> Installing dependencies for gcc: isl, mpfr, libmpc and zstd
   ==> Installing gcc dependency: isl
   fatal: not in a git directory
   Error: Command failed with exit 128: git
   ```
3. 已知的创建虚拟机的方法
   1. VirtualBox, VMWare PlayStation:不支持M1芯片
   2. Parallel Desktop：非免费
   3. [Multipass](https://multipass.run/docs)：简洁易用，社区支持未知
      1. 安装：`brew install multipass`
      2. 拉取镜像创建虚拟机对象：`multipass launch --name ins_name --name mem_size --cpus cpu_nums iamge_identifier`
      3. 共享本地数据：`multipass mount source_path insn_name:dest_path`
         1. 取消共享：`multipass unmount insn_name`
         2. 无法访问数据问题的解决：[macOS文件访问权限问题](https://askubuntu.com/questions/1281224/multipass-mount-source-is-not-readable), enabling **Full Disk Access** for multipass in *Settings -> Security & Privacy -> Privacy*