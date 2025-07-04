## 项目说明

该脚本旨在简化基于 CMake 的项目构建流程，支持一次性选择 Release 或 Debug 模式进行编译并自动运行生成的可执行文件。

---

## 功能

1. **自动检测项目名称**：从 `CMakeLists.txt` 文件中提取 `add_executable` 的目标名。
2. **构建目录管理**：在当前目录下创建并使用 `build` 目录存放中间产物。
3. **编译模式选择**：支持通过交互式提示选择 Release (优化) 或 Debug (调试符号) 模式。
4. **清理旧构建**：在构建前删除 build 文件夹，来清理上次生成的文件。
5. **自动运行**：编译完成后自动进入对应输出目录（`output/Release` 或 `output/Debug`）并执行生成的可执行程序。

---

## 环境要求

* **系统**：Linux 或 macOS（已安装 Bash）
* **依赖**：

  * CMake
  * GNU Make

---

## 使用方法

1. 将脚本保存为 `build_and_run.sh`，并赋予可执行权限：

   ```bash
   chmod +x build_and_run.sh
   ```

2. 确保脚本所在目录包含 `CMakeLists.txt` 文件，并参考如下格式：

   ```cmake
   cmake_minimum_required(VERSION 3.10)
   project(App)

   set(SRC
       ${CMAKE_SOURCE_DIR}/src/main.cpp
   )

   set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_SOURCE_DIR}/output)
   set(CMAKE_RUNTIME_OUTPUT_DIRECTORY_DEBUG   ${CMAKE_SOURCE_DIR}/output/Debug)
   set(CMAKE_RUNTIME_OUTPUT_DIRECTORY_RELEASE ${CMAKE_SOURCE_DIR}/output/Release)

   include_directories(include)

   add_executable(App ${SRC})
   ```
>[!WARNING]
>`add_exectuable()` 必须写成一行。
3. 在终端中运行脚本：

   ```bash
   ./build_and_run.sh
   ```

4. 按提示输入要构建的版本编号：

   * `1`：Release 模式
   * `2`：Debug 模式

5. 脚本将自动执行以下步骤：

   * 删除 `build`目录并重新创建，或者创建 `build` 目录（如果不存在）
   * 进入 `build` 目录并执行 `make clean`
   * 调用 `cmake -DCMAKE_BUILD_TYPE=<Release|Debug> ..`
   * 执行 `make` 编译项目
   * 进入生成的 `output/Release` 或 `output/Debug` 目录
   * 运行生成的可执行文件

---

## 参数说明

| 参数  | 含义                      |
| --- | ----------------------- |
| `1` | Release 模式构建（带优化，无调试信息） |
| `2` | Debug 模式构建（包含调试符号）      |

---

## 示例

```bash
$ ./build_and_run.sh
项目名称：App
需要make什么版本？(输入编号)
1. Release
2. Debug
> 1

-- 清屏后显示编译信息 --
...
[100%] Building CXX object ...
编译成功！

尝试运行编译后的程序...

Hello, World!
```

---

## 注意事项

* 脚本默认查找当前目录下的 `CMakeLists.txt`，如需指定其他路径，可在脚本开头修改 `cmake_file` 变量。
* 输出可执行文件需位于 `output/Release` 或 `output/Debug` 目录，脚本会自动进入并执行该目录下与项目名称同名的文件。
* 如果项目的 `add_executable` 写法比较特殊，提取目标名的正则需做相应调整。
