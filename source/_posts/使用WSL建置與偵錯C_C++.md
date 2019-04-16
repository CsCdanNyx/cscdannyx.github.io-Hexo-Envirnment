---
tags: 書頁, C++語言學習筆記本
---
使用 WSL 建置與偵錯 C\C++
===
[:arrow_up: 回到「C++ 語言學習筆記本」目錄](/Cm9OtXmDT8OdtVXIqsx4aw)

> 本篇文章使用已架設好的 Windows Subsystem for Linux ( WSL ) 安裝 C\C++ 的建置環境。
> 
> 若尚未設定好 WSL，請參考「[Unix-Like Windows ( 3 ) - Windows Subsystem for Linux ( WSL )](/ootZugPuTOKlLiXgrSp8Xw)」。
> [color=steelblue]

如果你跟我一樣，用了 MinGW 的 gcc 跟 gdb 沒有問題。但是有一天，想要用個 make 的時候卻發現找不到指令，找了一下 MSYS 的目錄後發現：

![Where make?](https://i.imgur.com/7jqkHHj.png "Where make?")

哇賽！那麼多 make 我要用哪一個呢？

找了一下網頁，好像大家都把 `mingw32-make.exe` alias 為 `make` 使用 ( 雖然還是不知道是在 mingw64 下的還是 32 )。

那問題來了，剛剛建置用的 `mingw64\bin\g++.exe` 呢？

![Where g++?](https://i.imgur.com/KrhAfIb.png "Where g++?")

哇賽！也那麼多耶～

照理講，要配合 `mingw32-make.exe` 使用的應該也要是個某個開頭的 `...mingw32-g++.exe` 才對呀！而不是剛剛用的 `g++.exe`。那到底。。。？

痾，我也還正在研究中～

那那那，我XX還不然直接來用 WSL 比較快！


安裝 *gcc*、*clang* 及 *llvm*
---
開啟 ubuntu shell 以後，先下 `sudo apt update`、`sudo apt upgrade` 將系統更新。

### *gcc*
更新完畢後，裝上開發的必要套件 `sudo apt install build-essential`。

檢查 gcc 版本：
```shell
$ gcc --version
gcc (Ubuntu 7.3.0-27ubuntu1~18.04) 7.3.0
Copyright (C) 2017 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
```
> 目前最新的 gcc 版本是 8.3.0，預計要推出 9。而在 WSL 的 Ubuntu 上照範例安裝的 gcc 是 7.3 的版本，在 WSL 的 Ubuntu 上最新的我只查到 8.2.0 的版本。
> 要安裝 gcc-8，請下 `sudo apt install gcc-8 g++-8`。
> 
> 先查詢目前有哪些版本的 gcc 及 g++ 安裝：
> ```shell
> ls /usr/bin/gcc* /usr/bin/g++*
> ```
> 要將預設 gcc 指令版本設為 gcc-8，請下：
> ```shell
> sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-7 700 --slave /usr/bin/g++ g++ /usr/bin/g++-7
> sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-8 800 --slave /usr/bin/g++ g++ /usr/bin/g++-8
> ```
> 接著
> ```shell
> sudo update-alternatives --config gcc
> ```
> `update-alternatives` 會自動挑選 Priority 較高的。
> 
> 參考資料：
> - [Install gcc-8 only on Ubuntu 18.04? - Ask Ubuntu](https://askubuntu.com/questions/1028601/install-gcc-8-only-on-ubuntu-18-04/1028656)
>
> [time=Fri, Apr 5, 2019] [color=green]


### *clang* 及 *llvm*

```shell
sudo apt install llvm
sudo apt install clang
```
測試 clang 版本
```shell
$ clang --version
clang version 6.0.0-1ubuntu2 (tags/RELEASE_600/final)
Target: x86_64-pc-linux-gnu
Thread model: posix
InstalledDir: /usr/bin
```
> 如同 *gcc* ，Ubuntu 上的版本會更新的較慢，WSL 的 Ubuntu 上搜尋到最新的 clang 版本是 7.0.0。
> 要安裝 clang-7，請下 `sudo apt install clang-7` ( 會同時安裝上 clang++\-7 )。
> 
> 先查詢目前有哪些版本的 clang 安裝：
> ```shell
> ls /usr/bin/clang*
> ```
> 將預設 clang 指令版本設為 clang-7，請下：
> ```shell
> sudo update-alternatives --install /usr/bin/clang clang /usr/bin/clang-6.0 700 --slave /usr/bin/clang++ clang++ /usr/bin/clang++-6.0
> sudo update-alternatives --install /usr/bin/clang clang /usr/bin/clang-7 800 --slave /usr/bin/clang++ clang++ /usr/bin/clang++-7
> ```
> 接著
> ```shell
> sudo update-alternatives --config clang
> ```
> `update-alternatives` 會自動挑選 Priority 較高的。
> 
> 參考資料：
> - [How to install latest clang (6.0) on Ubuntu 16.04 (xenial) / WSL](https://blog.kowalczyk.info/article/k/how-to-install-latest-clang-6.0-on-ubuntu-16.04-xenial-wsl.html)
>
> [time=Fri, Apr 5, 2019] [color=green]

設定 C、C++ 指令預設編譯器
---
使用 `update-alternatives` 來指定使用 `c` 以及 `c++` 指令時會呼叫的編譯器。

### C
```shell
$ sudo update-alternatives --config cc
There are 2 choices for the alternative cc (providing /usr/bin/cc).

  Selection    Path            Priority   Status
------------------------------------------------------------
* 0            /usr/bin/gcc     20        auto mode
  1            /usr/bin/clang   10        manual mode
  2            /usr/bin/gcc     20        manual mode

Press <enter> to keep the current choice[*], or type selection number:
```
可以輸入數字選擇想要綁定的編譯器。我沒特別綁定，直接分別以各自的指令呼叫即可。

### C++
```shell
$ sudo update-alternatives --config c++
There are 2 choices for the alternative c++ (providing /usr/bin/c++).

  Selection    Path              Priority   Status
------------------------------------------------------------
* 0            /usr/bin/g++       20        auto mode
  1            /usr/bin/clang++   10        manual mode
  2            /usr/bin/g++       20        manual mode

Press <enter> to keep the current choice[*], or type selection number:
```

> #### 參考資料：
> - [C/C++: clang 代替 gcc](https://fzheng.me/2016/03/15/clang-gcc/)
> - [Linux中使用update-alternatives切换默认程序 - 简书](https://www.jianshu.com/p/08d08713f0d1)


安裝 *gdb*
---
```shell
sudo apt install gdb
```



*make* 及 *cmake*
---

### *make*
make 已經在 *build-essential* 安裝時同步安裝完畢。

```shell
$ make --version
GNU Make 4.1
Built for x86_64-pc-linux-gnu
Copyright (C) 1988-2014 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.
```

### *cmake*
```shell
sudo apt install cmake
```




