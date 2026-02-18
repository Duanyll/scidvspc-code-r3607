# scidvspc-code-r3607 (macOS arm64 fork)

这个仓库基于 Scid vs. PC r3607，主要针对 **macOS arm64 + 中文本地化** 做了可复现的构建修复。

## 这个 fork 改了什么

1. `configure` 在 macOS 上固定生成 Darwin 模板 Makefile  
   避免回退到通用模板导致的目标/规则不匹配问题。

2. `configure` 优先使用 Homebrew 的 `tcl-tk@8`  
   在 macOS 上优先探测以下路径并写入 `TCL_INCLUDE/TCL_LIBRARY/TK_LIBRARY`：
   - `/opt/homebrew/opt/tcl-tk@8`
   - `/usr/local/opt/tcl-tk@8`
   - `brew --prefix tcl-tk@8` 返回路径  
   这样可以避免错误链接到系统自带 Tcl/Tk（通常版本过旧）。

3. 运行时资源目录解析修复（`./scid` 与 `.app` 双兼容）  
   `tcl/config.tcl` 现在会：
   - 优先使用 `.app` 的 `Contents/Resources`
   - 若不存在则回退到源码/构建目录  
   解决了源码目录直接运行时资源找不到的问题。

4. 语言文件目录回退修复（含中文）  
   `tcl/language.tcl` 现在支持：
   - `$scidShareDir/lang`
   - `$scidShareDir/tcl/lang`  
   解决了只显示 English、不显示 Chinese 的问题。

## 典型构建流程（macOS arm64）

首先使用 Homebrew 安装 Tcl/Tk 8：

```bash
brew install tcl-tk@8
```

然后在项目根目录执行：

```bash
./configure
make clean
make
```

如果需要打 macOS App：

```bash
make -f Makefile.macOS mac_app
open dist/ScidvsMac.app
```
