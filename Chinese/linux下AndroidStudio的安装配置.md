
### 1. **选择合适的JDK版本**

首先，根据你的Android开发目标选择兼容的Java Development Kit (JDK)。比如，如果你计划开发针对Android 14的应用，推荐安装JDK 17。Android Studio的最新版本可能已内置JDK，但确认这一点并按需单独安装总是明智之举。

### 2. **下载与安装Android Studio**

- 访问[Android开发者官网](https://developer.android.com/studio)下载Android Studio的最新版本，同时查阅系统需求以确保Linux环境的兼容性。
- 下载完成后，将文件解压至一个合适的位置，如`/usr/local`或`/opt`。使用命令行执行解压操作，例如：
  ```sh
  sudo tar -zxvf android-studio-<version>-linux.tar.gz -C /usr/local
  ```
  其中`<version>`应替换为实际的版本号。

- 进入解压后的`bin`目录，启动Android Studio：
  ```sh
  cd /usr/local/android-studio/bin
  ./studio.sh
  ```

### 3. **配置Android SDK、NDK及其他组件**

- 在首次启动Android Studio时，通过向导指引安装或更新Android SDK。根据项目需求，选择对应的Android API级别。
- 如需NDK支持，可在SDK Manager中单独下载相应的NDK版本。
- 遇到网络问题时，考虑配置镜像源以加速下载，具体镜像地址依据所在地区而定，建议查询相关教程。

### 4. **设置环境变量**

为了方便从命令行使用Android开发工具，需在bash配置文件中添加以下环境变量。打开终端，输入：
```sh
vim ~/.bashrc
```
在文件末尾添加如下内容（记得替换`user`为你的用户名：
```sh
export ANDROID_HOME=/home/user/Android/Sdk
export PATH=$ANDROID_HOME/tools:$PATH
export PATH=$ANDROID_HOME/platform-tools:$PATH
```
保存更改后，使配置生效：
```sh
source ~/.bashrc
```
