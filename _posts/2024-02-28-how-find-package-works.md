---
layout: post
title: CMake 中 find_package 是如何工作的？
categories: Toolbox
tags: ["CMake"]
---

用 CMake 稍不留神就会出现这样的报错：

```
Could not find a package configuration file provided by "<package>" with any of the following names
```

或者因为找到的包不对，出现各种各样的编译错误。然而，CMake 包搜索机制已经在[文档](https://cmake.org/cmake/help/latest/command/find_package.html)里有详细的说明。简单来说，CMake 包搜索机制分为两种模式，一种是模块模式（Module mode），一种是配置模式（Config mode）。

模块模式下，CMake 会搜索名为 `Find<PackageName>.cmake` 的文件，先搜索 `CMAKE_MODULE_PATH` 变量中的目录，然后再搜索 CMake 的安装目录。在我的系统里它是 `/usr/share/cmake/Modules`，如果想额外添加自定义的模块，可以将目录添加到 `CMAKE_MODULE_PATH`：
   ```cmake
   list(APPEND CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/cmake/Modules/")
   ```


配置模式下，CMake 会搜索名为 `<lowercasePackageName>-config.cmake` 或者 `<PackageName>Config.cmake`，以及如果指定了版本也会搜索名为 `<lowercasePackageName>-config-version.cmake` 或 `<PackageName>ConfigVersion.cmake` 的文件。搜索目录根据操作系统的不同而有些许差别，例如 macOS 下还会支持搜索 Frameworks（`.framework`）和 Application bundles（`.app`），完整目录在 [Config Mode Search Procedure](https://cmake.org/cmake/help/latest/command/find_package.html#config-mode-search-procedure)。不过，find-grep 一下基本上知道配置文件在哪，以 OpenCV 为例，它的 CMake 配置文件在 `/usr/lib/x86_64-linux-gnu/cmake/opencv4/OpenCVConfig.cmake`。


如果不想使用这些机制，`find_package` 本身也支持用 `PATHS` 指定目录，以 OpenCV 为例可以用如下方式指定版本和搜索目录：
```cmake
find_package(OpenCV 4.0.1 EXACT REQUIRED PATHS $ENV{HOME}/root)
```

一般来说，配置模式下的文件是由包自带的，而模块模式是包没有自带配置而由 CMake 或者其他库提供的，在 CMake 仓库里就有一个[模块目录](https://github.com/Kitware/CMake/tree/master/Modules)存放这些文件。

## Further reading

- [find_package - CMake Documentation](https://cmake.org/cmake/help/latest/command/find_package.html)
- [default search paths for CMake include() vs. find_package() - Stack Overflow](https://stackoverflow.com/questions/42027646/default-search-paths-for-cmake-include-vs-find-package)
