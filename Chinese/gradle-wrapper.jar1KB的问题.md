
在遇到gradle-wrapper.jar文件仅显示为1KB且导致工程编译失败的问题时，根本原因已被查明：这通常是由于[Git Large File Storage (LFS)](https://docs.github.com/zh/repositories/working-with-files/managing-large-files/about-git-large-file-storage) 未被恰当安装与配置所引发的。当Git仓库启用了LFS，并且.gitattributes文件中定义了如下的规则来管理大型文件时：
```
1*.jpg filter=lfs diff=lfs merge=lfs -text
2*.png filter=lfs diff=lfs merge=lfs -text
3*.jar filter=lfs diff=lfs merge=lfs -text
```
若用户通过ZIP方式下载或在本地未安装Git LFS，原本应由LFS管理的大文件（如gradle-wrapper.jar）将会以一个1KB的指针文件形式存在，而非实际的完整内容。<br />为了解决这一问题并确保能够获取到完整的大文件，遵循以下步骤安装并配置Git LFS显得至关重要：

1. **配置PackageCloud存储库**：首先，根据您的操作系统，通过终端运行相应的脚本以添加Git LFS的PackageCloud仓库。对于基于Debian的系统（如Ubuntu），使用如下命令：
```
1curl -s https://packagecloud.io/install/repositories/github/git-lfs/script.deb.sh | sudo bash
```
而对于基于RPM的系统（如CentOS），则执行：
```
1curl -s https://packagecloud.io/install/repositories/github/git-lfs/script.rpm.sh | sudo bash
```

2. **安装Git LFS**：完成仓库配置后，通过系统的包管理器安装Git LFS：
   - 对于Debian系：
   - 对于RPM系：
```
1sudo apt-get install git-lfs
```
```
1sudo yum install git-lfs
```

3. **初始化Git LFS**：安装完成后，通过运行git lfs install命令来设置Git LFS的钩子，确保它能与Git协同工作。成功执行后，你将看到如下信息：
```
1Updated git hooks.
2Git LFS initialized.
```

4. **验证文件完整性**：最后，通过检查gradle-wrapper.jar的大小或尝试打开它，来确认文件是否已恢复至正常状态。正确的安装与配置后，该文件应当不再是一个指针文件，而是包含实际数据的完整文件。
