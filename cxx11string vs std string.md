# 因std::string与std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >不一致而导致的`undefined reference to`链接问题

## 问题描述

Linux环境下，使用gnu gcc/g++编译，出现`undefined reference to`即找不到调用的函数原型的问题。

```shell
testbench_model.o: In function `model_fn(void*)':
testbench_model.cpp:(.text+0x57f): undefined reference to `Emulator::Simulator::Pass_InsnPkg(std::vector<std::shared_ptr<Emulator::DynInsn>, std::allocator<std::shared_ptr<Emulator::DynInsn> > >&, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >)'
testbench_model.o: In function `Emulator::Mixed_Model::Mixed_Model(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, unsigned long)':
testbench_model.cpp:(.text._ZN8Emulator11Mixed_ModelC2ENSt7__cxx1112basic_stringIcSt11char_traitsIcESaIcEEES6_m[_ZN8Emulator11Mixed_ModelC5ENSt7__cxx1112basic_stringIcSt11char_traitsIcESaIcEEES6_m]+0x97): undefined reference to `YAML::LoadFile(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&)'
testbench_model.cpp:(.text._ZN8Emulator11Mixed_ModelC2ENSt7__cxx1112basic_stringIcSt11char_traitsIcESaIcEEES6_m[_ZN8Emulator11Mixed_ModelC5ENSt7__cxx1112basic_stringIcSt11char_traitsIcESaIcEEES6_m]+0x32d): undefined reference to `ELFParser::ELFParser(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >)'
testbench_model.cpp:(.text._ZN8Emulator11Mixed_ModelC2ENSt7__cxx1112basic_stringIcSt11char_traitsIcESaIcEEES6_m[_ZN8Emulator11Mixed_ModelC5ENSt7__cxx1112basic_stringIcSt11char_traitsIcESaIcEEES6_m]+0x7b0): undefined reference to `YAML::detail::node_data::set_scalar(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&)'
collect2: error: ld returned 1 exit status

```

## 问题分析

- 根据[文档](https://gcc.gnu.org/onlinedocs/gcc-5.2.0/libstdc++/manual/manual/using_dual_abi.html)描述，GCC5.1开始对于`std::string` `std::list`有了新的实现库ABI，为了保证向后兼容性，仍旧保留了原始实现。

- 可以通过定义添加`_GLIBCXX_USE_CXX11_ABI`宏来选择使用哪一个：
  - `#define _GLIBCXX_USE_CXX11_ABI 0`选择旧实现
  - `#define _GLIBCXX_USE_CXX11_ABI 1`选择新实现

## 问题解决

参考此篇[问答](https://stackoverflow.com/questions/33394934/converting-std-cxx11string-to-stdstring)

Defining the following macro before including any standard library headers should fix your problem: `#define _GLIBCXX_USE_CXX11_ABI 0`