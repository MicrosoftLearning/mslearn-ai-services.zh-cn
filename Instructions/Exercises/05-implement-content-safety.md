---
lab:
  title: 实现 Azure AI 内容安全
---

# 实现 Azure AI 内容安全

在本练习中，你将预配内容安全资源、在 Azure AI Studio 中测试资源，并在代码中测试资源。

## 预配*内容安全*资源

如果还没有，则需要在 Azure 订阅中预配**内容安全**资源。

1. 打开 Azure 门户 (`https://portal.azure.com`)，然后使用与你的 Azure 订阅关联的 Microsoft 帐户登录。
1. 选择“创建资源”。
1. 在搜索字段中，搜索“**内容安全**”。 然后，在结果中，选择“**Azure AI 内容安全**”下的“**创建**”。
1. 使用以下设置预配资源：
    - **订阅**：Azure 订阅。
    - **资源组**：*创建或选择资源组*
    - **区域**：选择“**美国东部**”
    - **名称**：输入唯一名称。
    - **定价层**：选择 **F0**（*免费*层）；如果 F0 不可用，请选择 **S**（*标准*层）。
1. 选择“查看 + 创建”，再选择“创建”以预配资源。
1. 等待部署完成，然后转到资源。
1. 在左侧导航栏中选择“**访问控制**”，然后选择“**+ 添加**”和“**添加角色分配**”。
1. 向下滚动选择“**认知服务用户**”角色，然后选择“**下一步**”。
1. 将帐户添加到此角色，然后选择“**查看 + 分配**”。
1. 在左侧导航栏中选择“**资源管理**”，然后选择“**密钥和终结点**”。 使此页保持打开状态，以便稍后复制密钥。

## 使用 Azure AI 内容安全提示盾牌

在本练习中，使用 Azure AI Studio 通过两个示例输入来测试内容安全提示盾牌。 其中一个模拟用户提示，另一个模拟嵌入其中可能不安全的文本的文档。

1. 在另一个浏览器标签页中，打开 [Azure AI Studio](https://ai.azure.com/explore/contentsafety) 的内容安全页并登录。
1. 在“**审查文本内容**”下选择“**试用**”。
1. 在“**审查文本内容**”页上，在”**Azure AI 服务**“下选择之前创建的内容安全资源。
1. 选择“**一个句子中的多个风险类别**”。 查看文档文本，了解潜在问题。
1. 选择“**运行测试**”并查看结果。
1. （可选）更改阈值级别，并再次选择“**运行测试**”。
1. 在左侧导航栏上，选择“**文本的受保护材料检测**”。
1. 选择“**受保护的歌词**”，并注意这些是已发布歌曲的歌词。
1. 选择“**运行测试**”并查看结果。
1. 在左侧导航栏上，选择“**审查图像内容**”。
1. 选择“**自残内容**”。
1. 请注意，默认情况下，AI Studio 中的所有图像都会进行模糊处理。 还应注意，样本中的性内容非常温和。
1. 选择“**运行测试**”并查看结果。
1. 在左侧导航栏中，选择“**提示盾牌**”。
1. 在**提示盾牌页**上的“**Azure AI 服务**”下，选择之前创建的内容安全资源。
1. 选择“**提示和文档攻击内容**”。 查看用户提示和文档文本，了解潜在问题。
1. 选择“运行测试”。
1. 在“**查看结果**”中，验证是否在用户提示和文档中检测到越狱攻击。

    > [!TIP]
    > 代码可用于 AI Studio 中的所有示例。

1. 在“**后续步骤**”下，在“**查看代码**”下选择“**查看代码**”。 显示“**示例代码**”窗口。
1. 使用向下箭头选择 Python 或 C#，然后选择“**复制**”将示例代码复制到剪贴板。
1. 关闭“**示例代码**”屏幕。

### 配置应用程序

现在，将在 C# 或 Python 中创建应用程序。

#### C#

##### 先决条件

* 安装在某个[受支持的平台](https://code.visualstudio.com/docs/supporting/requirements#_platforms)上的 [Visual Studio Code](https://code.visualstudio.com/)。
* [.NET 8](https://dotnet.microsoft.com/en-us/download/dotnet/8.0) 是用于本练习的目标框架。
* Visual Studio Code 的 [C# 扩展](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)。

##### 设置

执行以下步骤，为练习准备 Visual Studio Code。

1. 启动 Visual Studio Code 并在资源管理器视图中单击“**创建 .NET 项目**”，选择“**控制台应用**”。
1. 在计算机上选择一个文件夹，并为项目指定一个名称。 选择“**创建项目**”并确认警告消息。
1. 在“资源管理器”窗格中，展开解决方案资源管理器并选择 **Program.cs**。
1. 通过选择“**运行**” -> “**运行但不调试**”来构建并运行项目。 
1. 在“解决方案资源管理器”中，右键单击 C# 项目，然后选择“**添加 NuGet 包**”。
1. 搜索 **Azure.AI.TextAnalytics** 并选择最新版本。
1. 搜索第二个 NuGet 包：**Microsoft.Extensions.Configuration.Json 8.0.0**。 项目文件现在应列出两个 NuGet 包。

##### 添加代码

1. 将之前复制的示例代码粘贴到 **ItemGroup** 部分下。
1. 向下滚动以查找“*替换为自己的订阅密钥和终结点*”。
1. 在 Azure 门户的“密钥和终结点”页上，复制其中一个密钥（1 或 2）。 将 **<your_subscription_key>** 替换为该值。
1. 在 Azure 门户的“密钥和终结点”页上，复制终结点。 将此值粘贴到代码中以替换 **<your_endpoint_value>**。
1. 在 **Azure AI Studio** 中，复制**用户提示**值。 将此值粘贴到代码中以替换 **<test_user_prompt>**。
1. 向下滚动到 **<this_is_a_document_source>** 并删除此代码行。
1. 在 **Azure AI Studio** 中，复制**文档**值。
1. 向下滚动到 **<this_is_another_document_source>** 并粘贴文档值。
1. 选择“**运行**” -> “**运行但不调试**”，并验证是否已检测到攻击。 

#### Python

##### 先决条件

* 安装在某个[受支持的平台](https://code.visualstudio.com/docs/supporting/requirements#_platforms)上的 [Visual Studio Code](https://code.visualstudio.com/)。

* 已安装适用于 Visual Studio Code 的 [Python 扩展](https://marketplace.visualstudio.com/items?itemName=ms-python.python)。

* [请求模块](https://pypi.org/project/requests/)已安装。

1. 创建具有 **.py** 扩展名的新 Python 文件，并为其指定合适的名称。
1. 粘贴之前复制的示例代码。
1. 向下滚动以查找标题为“*替换为自己的订阅密钥和终结点*”的部分。
1. 在 Azure 门户的“密钥和终结点”页上，复制其中一个密钥（1 或 2）。 将 **<your_subscription_key>** 替换为该值。
1. 在 Azure 门户的“密钥和终结点”页上，复制终结点。 将此值粘贴到代码中以替换 **<your_endpoint_value>**。
1. 在 **Azure AI Studio** 中，复制**用户提示**值。 将此值粘贴到代码中以替换 **<test_user_prompt>**。
1. 向下滚动到 **<this_is_a_document_source>** 并删除此代码行。
1. 在 **Azure AI Studio** 中，复制**文档**值。
1. 向下滚动到 **<this_is_another_document_source>** 并粘贴文档值。
1. 在文件的集成终端中，运行程序，例如：

    - `.\prompt-shield.py`

1. 验证是否检测到攻击。
1. （可选）可以试验不同的测试内容和文档值。
