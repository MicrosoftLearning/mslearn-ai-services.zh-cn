---
lab:
  title: 实验室环境设置
  module: Setup
---

# 实验室环境设置

练习应在托管实验室环境中完成。 如果要在你自己的计算机上完成它们，可以通过安装以下软件来完成。 使用自己的环境时，可能会遇到意外的对话和行为。 由于可能的本地配置范围广泛，课程团队无法支持你在自己的环境中可能遇到的问题。

> **注意**：以下说明适用于 Windows 11 计算机。 也可使用 Linux 或 MacOS。 可能需要根据所选 OS 调整实验室说明。

### 基本操作系统 (Windows 11)

#### Windows 11

安装 Windows 11 并应用所有更新。

#### Microsoft Edge

安装 [Microsoft Edge (Chromium)](https://microsoft.com/edge)

### .NET Core SDK

1. 从 https://dotnet.microsoft.com/download 下载并安装（下载 .NET Core SDK - 不仅是运行时）。 如果在自己的计算机上运行本课程中的实验室，则应当具有 .NET 7.0。 实验室已针对 .NET 7.0 进行了测试，但 7.0 目前不受支持。 你可以使用 8.0，但可能存在一些微小的问题。 强烈建议使用托管环境。

### C++ Redistributable

1. 从 https://aka.ms/vs/16/release/vc_redist.x64.exe 下载并安装 Visual C++ 可再发行程序包 (x64)。

### Node.JS

1. 从 https://nodejs.org/en/download/ 下载最新的 LTS 版本 
2. 使用默认选项进行安装

### Python（和必需的包）

1. 从 https://docs.conda.io/en/latest/miniconda.html 下载版本 3.11 
2. 运行安装程序进行安装 - 注意事项：选择“将 Miniconda 添加到 PATH 变量”和“将 Miniconda 注册为默认 Python 环境”选项。
3. 安装后，打开 Anaconda 提示符并输入以下命令来安装包： 

```
pip install flask requests python-dotenv pylint matplotlib pillow
pip install --upgrade numpy
```

### Azure CLI

1. 从 https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest 下载 
2. 使用默认选项进行安装

### Git

1. 使用默认选项从 https://git-scm.com/download.html 下载并安装


### Visual Studio Code（和扩展）

1. 从 https://code.visualstudio.com/Download 下载 
2. 使用默认选项进行安装 
3. 安装后，启动 Visual Studio Code，然后在“扩展”选项卡 (Ctrl+Shift+X) 上，搜索并安装以下 Microsoft 扩展：
    - Python
    - C#
    - Azure Functions
    - PowerShell
