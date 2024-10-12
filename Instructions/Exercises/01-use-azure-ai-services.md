---
lab:
  title: Azure AI 服务入门
  module: Module 2 - Developing AI Apps with Azure AI Services
---

# Azure AI 服务入门

在此练习中，你将开始使用 Azure AI 服务，在 Azure 订阅中创建 Azure AI 服务**** 资源并在客户端应用程序中使用。 此练习的目的不是获得任意特定服务的专业知识，而是让作为开发人员的你熟悉预配和使用 Azure AI 服务的一般模式。

## 在 Visual Studio Code 中克隆存储库

你将使用 Visual Studio Code 开发代码。 应用程序的代码文件已在 GitHub repo 中提供。

> **提示**：如果已克隆 **mslearn-ai-services** 存储库，请在 Visual Studio Code 中打开它。 否则，请按照以下步骤将其克隆到开发环境中。

1. 启动 Visual Studio Code。
2. 打开面板 (SHIFT+CTRL+P) 并运行“**Git：克隆**”命令，以将 `https://github.com/MicrosoftLearning/mslearn-ai-services` 存储库克隆到本地文件夹（任意文件夹均可）。
3. 克隆存储库后，在 Visual Studio Code 中打开文件夹。
4. 如有必要，等待其他文件安装完毕，以支持存储库中的 C# 代码项目

    > **注意**：如果系统提示你添加生成和调试所需的资产，请选择**以后再说**。

5. 展开 `Labfiles/01-use-azure-ai-services` 文件夹。

已提供适用于 C# 和 Python 的代码。 展开首选语言的文件夹。

## 预配 Azure AI 服务资源

Azure AI 服务是基于云的服务，其中封装了可合并到应用程序的人工智能功能。 可为“语言”**** 或“视觉”**** 等特定 API 预配单独的 Azure AI 服务**** 资源，也可以预配单个 Azure AI 服务资源来通过单一终结点和密钥提供对多个 Azure AI 服务 API 的访问  。 在本案例中，你将使用一个 Azure AI 服务**** 资源。

1. 打开 Azure 门户 (`https://portal.azure.com`)，然后使用与你的 Azure 订阅关联的 Microsoft 帐户登录。
2. 在顶部搜索栏中，搜索 *Azure AI 服务*，选择Azure AI 服务****，并使用以下设置创建 Azure AI 服务多服务帐户资源：
    - **订阅**：*Azure 订阅*
    - **资源组**：*选择或创建一个资源组（如果使用受限制的订阅，你可能无权创建新的资源组 - 请使用提供的资源组）*
    - **区域**：*选择任何可用区域*
    - **名称**：*输入唯一名称*
    - **定价层**：标准版 S0
3. 选中所需的复选框并创建资源。
4. 等待部署完成，然后查看部署详细信息。
5. 前往该资源并查看其“密钥和终结点”页面。 此页面中包含连接资源并在开发的应用程序中使用该资源所需的信息。 尤其是在下列情况下：
    - 客户端应用程序可向其发送请求的 HTTP 终结点。
    - 可用于身份验证的两个密钥（客户端应用程序可使用任一密钥进行身份验证）。
    - 托管资源的位置。 对于针对某些（但不是全部）API 的请求，这是必需的。

## 使用 REST 接口

Azure AI 服务 API 基于 REST，因此可以通过 HTTP 提交 JSON 请求来使用它们。 在本示例中，你将探索使用“语言”**** REST API 进行语言检测的控制台应用程序。但对于 Azure AI 服务资源支持的所有 API 来说，基本原理相同。

> **注意**：在此练习中，可以选择在 C# 或 Python 中使用 REST API 。 在下面的步骤中，请执行适用于你的语言首选项的操作。

1. 在 Visual Studio Code 中，根据语言首选项展开 C-Sharp 或 Python 文件夹********。
2. 查看 rest-client 文件夹的内容，并注意其中包含一个配置设置文件：

    - **C#** ：appsettings.json
    - **Python**：.env

    打开配置文件，然后更新其中包含的配置值，以反映 Azure AI 服务资源的**终结点**和身份验证**密钥**。 保存所做更改。

3. 请注意，rest-client 文件夹中包含客户端应用程序的代码文件：

    - **C#** ：Program.cs
    - **Python**：rest-client.py

    打开代码文件并查看其中包含的代码，并注意以下详细信息：
    - 导入了各种命名空间，以启用 HTTP 通信
    - Main**** 函数中的代码可检索 Azure AI 服务资源的终结点和密钥 - 这些终结点和密钥将用于向文本分析服务发送 REST 请求。
    - 该程序接受用户输入，并使用 GetLanguage**** 函数为 Azure AI 服务终结点调用“文本分析”语言检测 REST API，以检测文本输入的语言。
    - 发送到 API 的请求包含含输入数据的 JSON 对象（在本例中是一组 document 对象，每个 document 对象都具有 id 和 text）  。
    - 服务密钥附在请求标头中，用于对客户端应用程序进行身份验证。
    - 来自服务的响应是 JSON 对象，客户端应用程序可对其进行解析。

4. 右键单击 rest-client 文件夹，选择“在集成终端中打开”，然后运行以下命令******：

    **C#**

    ```
    dotnet run
    ```

    **Python**

    ```
    pip install python-dotenv
    python rest-client.py
    ```

5. 系统提示时，输入一些文本并查看服务检测到的语言，返回的 JSON 响应中将包含该语言的信息。 例如，尝试输入“Hello”、“Bonjour”和“Gracias”。
6. 测试应用程序后，输入“quit”以停止程序。

## 使用 SDK

可以编写可直接使用 Azure AI 服务 REST API 的代码，也可以使用 Microsoft C#、Python、Java 和 Node.js 等许多常见编程语言的软件开发工具包 (SDK)。 使用 SDK 可以大幅简化使用 Azure AI 服务的应用程序的开发过程。

1. 在 Visual Studio Code 中，根据语言首选项，展开 C-Sharp 或 Python 文件夹下的 sdk-client 文件夹************。 然后运行 `cd ../sdk-client` 以更改为相关的 sdk-client 文件夹****。

2. 然后运行适用于你的语言首选项的命令，安装文本分析 SDK 包：

    **C#**

    ```
    dotnet add package Azure.AI.TextAnalytics --version 5.3.0
    ```

    **Python**

    ```
    pip install azure-ai-textanalytics==5.3.0
    ```

3. 查看 sdk-client 文件夹的内容，并注意其中包含一个配置设置文件：

    - **C#** ：appsettings.json
    - **Python**：.env

    打开配置文件，然后更新其中包含的配置值，以反映 Azure AI 服务资源的**终结点**和身份验证**密钥**。 保存所做的更改。
    
4. 请注意，sdk-client 文件夹中包含客户端应用程序的代码文件：

    - **C#** ：Program.cs
    - **Python**：sdk-client.py

    打开代码文件并查看其中包含的代码，并注意以下详细信息：
    - 已导入安装的 SDK 的命名空间
    - Main**** 函数中的代码可检索 Azure AI 服务资源的终结点和密钥 - 这些终结点和密钥将与 SDK 结合使用，以创建文本分析服务的客户端。
    - GetLanguage 函数使用 SDK 来创建服务的客户端，然后使用该客户端检测文本的输入语言。

5. 返回到终端，确保位于 sdk-client 文件夹，然后输入以下命令来运行程序****：

    **C#**

    ```
    dotnet run
    ```

    **Python**

    ```
    python sdk-client.py
    ```

6. 系统提示时，输入一些文本并查看服务检测到的语言。 例如，尝试输入“Goodbye”、“Au revoir”和“Hasta la vista”。
7. 测试应用程序后，输入“quit”以停止程序。

> **注意**：这个简单的控制台应用程序可能无法识别某些需要使用 Unicode 字符集的语言。

## 清理资源

如果不将本实验室创建的 Azure 资源用于其他培训模块，则可以删除这些资源以避免产生更多费用。

1. 打开 Azure 门户网站 `https://portal.azure.com`，在顶部搜索栏中搜索在本实验室中创建的资源。

2. 在资源页面上，选择**删除**，然后按照说明删除资源。 或者，也可以删除整个资源组，同时清理所有资源。

## 更多信息

有关 Azure AI 服务的详细信息，请参阅 [Azure AI 服务文档](https://docs.microsoft.com/azure/ai-services/what-are-ai-services)。