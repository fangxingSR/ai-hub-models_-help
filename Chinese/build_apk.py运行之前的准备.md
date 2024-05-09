**已测试成功的环境配置和推荐：**

- 操作系统：Windows WSL2 Ubuntu 20.24
- Python版本：python3.8。建议使用3.8至3.10之间的版本。使用不使用AI HUB下载文件，以本地模型文件构建的APK，可能对Python版本无特定要求。
- JDK版本：jdk17。推荐使JDK 11或JDK 17。
- 使用过的工具和SDK： 
   - Android Studio Jellyfish | 2023.3.1
   - Qualcomm AI Engine Direct SDK 2.21.0.240401
   - 已测试的硬件：高通8gen3处理器，运行Android 14系统。
   - 注意带有"-Quantized"标签的量化版本模型不能直接于此工程。

**Windows和环境注意事项：**

- 在Windows环境下，未能成功获取到`qtld-release.aar`文件，因此推荐在Linux系统中操作。
- Android Studio主要用于获取SDK和NDK。若从网页端下载模型，

**运行Python脚本`build_apk.py`前的准备工作：**

1.  **Python环境：** 确保系统上安装了Python 3.8至3.10版本。若遇到兼容性问题，可以使用如[Miniconda](https://docs.anaconda.com/free/miniconda/)的工具进行管理。 
2.  **高通开发环境配置：** 
   - 注册并填写高通的相关协议[Agreements](https://www.qualcomm.com/agreements)。
   - 从[Qualcomm Package Manager](https://qpm.qualcomm.com/#/main/tools/details/QPM3)下载QPM3，并在Linux上安装。
      1. 安装   
         1. `sudo dpkg -i QualcommPackageManager3.3.0.101.3.Linux-x86.deb`
      2. 运行以下命令激活开发环境并下载包含了**QNN SDK**的Qualcomm AI Engine Direct SDK：
         1. `qpm-cli --login <用户名>`
         2. `qpm-cli --license-activate qualcomm_ai_engine_direct`
         3. `qpm-cli -- 提取 qualcomm_ai_engine_direct `（或）<br />`qpm-cli --extract <下载的 .qik 文件的完整路径>`
            1. qualcomm_ai_engine_direct[下载地址](https://qpm.qualcomm.com/#/main/tools/details/qualcomm_ai_engine_direct)<br />（或）<br />从 QPM3 桌面应用程序：
               1. 在桌面上打开 QPM3 应用程序
               2. 转到“工具”页面，然后选择“Qualcomm AI Engine Direct SDK”（高通® AI Engine Direct SDK）
      - 验证`/opt/qcom/aistack/qairt/2.21.0.240401/lib/android`目录即是下是否获取到了`qtld-release.aar`文件。
3.  **QNN SDK：** 脚本运行时需指定QNN SDK路径，该SDK应包含构建Android应用所需的库和工具。通常路径为`/opt/qcom/aistack/qairt/2.21.0.240401`。 
4.  **Android Studio安装与环境变量配置：** 
   - 在[linux下AndroidStudio的安装配置](https://github.com/fangxingSR/ai-hub-models_-help/blob/main/Chinese/linux%E4%B8%8BAndroidStudio%E7%9A%84%E5%AE%89%E8%A3%85%E9%85%8D%E7%BD%AE.md)
   - 确保ADB工具环境变量配置正确，以便与Android设备交互，包括安装和运行APK。
5.  **Android设备：** 如果选择在设备上安装和运行APK，确保有一台Android设备连接到电脑。 
6.  **模型文件：** 如果不打算在代码运行时从AI HUB下载模型，可以提前下载[MobileNet-v3-Small](https://aihub.qualcomm.com/mobile/models/mobilenet_v3_small)模型，并在`build_apk.py`使用时提供其路径。 
7.  **文件权限：** 确保`./gradlew`文件具有执行权限，以避免PermissionError错误。 
   1. 可以使用`chmod +x gradlew` 提升权限

为了确保顺畅执行命令行脚本，请遵循以下详细步骤：

### 准备阶段
1. **检查Git LFS**: 确认已安装`git lfs`，这是为了能够处理大型文件。如果尚未安装会导致[文件1KB](https://github.com/fangxingSR/ai-hub-models_-help/blob/main/Chinese/gradle-wrapper.jar1KB%E7%9A%84%E9%97%AE%E9%A2%98.md)
      i.请参照[git-lfs/README.md](https://github.com/git-lfs/git-lfs/blob/main/README.md)中的指南操作。
3. **设置Gradle镜像源**: 遇到`gradle-7.4-bin.zip`下载速度缓慢时，可更改为更快的镜像源。
   1. 例如，对于中国大陆用户，可采用[腾讯云的镜像源](https://mirrors.cloud.tencent.com/gradle/)。找到对应的`gradle-7.4-bin.zip`，复制其链接，<br />
   并在目录`gradle/wrapper/gradle-wrapper.properties`文件中替换`distributionUrl=`的链接部分。   
       修改后的`distributionUrl=`文件应如下所示：
         ```  
         distributionUrl=https://mirrors.cloud.tencent.com/gradle/gradle-7.4-bin.zip
         ```
4. **修正脚本格式**: 如果遭遇`/usr/bin/env: ‘sh\r’: No such file or directory`错误，表明脚本首行存在格式问题（Windows风格的换行符）。使用命令`sed -i -e 's/\r$//' gradlew`将其转换为Unix格式。

### 克隆项目
从GitHub克隆`quic/ai-hub-models`仓库至适当位置：
```bash
git clone https://github.com/quic/ai-hub-models.git
cd ai-hub-models/apps/android/ImageClassification
```

### 构建APK

运行`python build_apk.py -h`查看参数列表：

```
build_apk.py [-h] -q QNNSDK [-m MODEL_NAME] [-path MODEL_PATH]
```
   - **QNNSDK路径** (`-q`, `--qnnsdk`): 指向QNN SDK的安装路径。
   - **模型名称** (`-m`, `--model_name`): 使用的模型名称，应与脚本支持的模型枚举匹配。
   - **模型文件路径** (`-path`, `--model_path`): 指向`.tflite`模型文件的具体路径。

必须输入参数“-q”。QNNSDK可以通过设置环境变量或直接指定路径来使用。<br />
输入参数有三种方式<br />
      1.仅输入参数 “-path”<br />
      2.仅输入参数“-m” <br />
      3.既不输入参数“-m”也不输入“-path”<br />


### Example

使用 -path，给出您的 tflite 模型路径，脚本会将模型文件复制到 assets 文件夹以构建 andoid 应用程序。*.tflite file
```
    python build_apk.py -q "<QNN_SDK_PATH>" -path "Path/to/TFLITE/Model"
```


此外，您还可以使用模型目录中提到的 `AI-HUB`模型名称，直接从`AI-Hub`导出模型并将其复制到应用资产中
<br /> [网页端AI HUB](https://aihub.qualcomm.com/mobile/models/sam)下载的模型名称不可以直接套用到这里，请比对qai_hub_models/models文件夹下的名称后再使用。
```
    python build_apk.py -q "<QNN_SDK_PATH>" -m <Model Name>
```

您也可以在执行build_apk.py时选择列表菜单中提供的模型，而无需指定模型名称和模型路径。

```
    python build_apk.py -q "<QNN_SDK_PATH>" 
```


### 完成与后续步骤

build_apk.py构建成功后会出现：
```
BUILD SUCCESSFUL in 50s
33 actionable tasks: 33 executed
APK Is copied at current Working Directory
Do you want to install this apk in connected device
```

提示APK已复制到当前工作目录。此时，你可以选择是否立即将APK安装到已连接的Android设备上。<br />
对于WSL2用户，由于默认不支持USB连接，可能需要参考微软官方文档配置USB设备访问。可以参考微软的官方文档[连接 USB 设备](https://learn.microsoft.com/zh-cn/windows/wsl/connect-usb)<br />
文件在ai-hub-models目录下的/apps/android/ImageClassification/classification/build/outputs/apk/debug下找到。

### 注意事项
- 开发者选项需在Android设备上启用，并授权电脑进行USB调试。
- 如果首次安装外部应用，设备上可能会有安全提示，需手动允许安装未知来源应用。

通过上述步骤，你将能够顺利构建并部署AI模型驱动的Android应用程序。
