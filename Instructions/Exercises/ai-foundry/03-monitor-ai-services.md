---
lab:
  title: 监视 Azure AI 服务
  module: Module 2 - Developing AI Apps with Azure AI Services
---

# 监视 Azure AI 服务

Azure AI 服务是整个应用程序基础结构的关键部分。 能够监视活动，并对可能需要注意的问题发出警报，这一点很重要。

## 在 Visual Studio Code 中克隆存储库

你将使用 Visual Studio Code 开发代码。 应用程序的代码文件已在 GitHub repo 中提供。

> **提示**：如果已克隆 **mslearn-ai-services** 存储库，请在 Visual Studio Code 中打开它。 否则，请按照以下步骤将其克隆到开发环境中。

1. 启动 Visual Studio Code。
2. 打开面板 (SHIFT+CTRL+P) 并运行“**Git：Clone**”命令，以将 `https://github.com/MicrosoftLearning/mslearn-ai-services` 存储库克隆到本地文件夹（任意文件夹均可）。
3. 克隆存储库后，在 Visual Studio Code 中打开文件夹。
4. 如有必要，等待其他文件安装完毕，以支持存储库中的 C# 代码项目

    > **注意**：如果系统提示你添加生成和调试所需的资产，请选择**以后再说**。

5. 展开 `Labfiles/03-monitor-ai-services` 文件夹。

## 预配 Azure AI 服务资源

如果订阅中还没有，则需要预配 **Azure AI 服务**资源。

1. 打开 Azure 门户 (`https://portal.azure.com`)，然后使用与你的 Azure 订阅关联的 Microsoft 帐户登录。
2. 在顶部搜索栏中，搜索 *Azure AI 服务*，选择Azure AI 服务****，并使用以下设置创建 Azure AI 服务多服务帐户资源：
    - **订阅**：*Azure 订阅*
    - **资源组**：*选择或创建一个资源组（如果使用受限制的订阅，你可能无权创建新的资源组 - 请使用提供的资源组）*
    - **区域**：*选择任何可用区域*
    - **名称**：*输入唯一名称*
    - **定价层**：标准 S0
3. 选中任何所需复选框并创建资源。
4. 等待部署完成，然后查看部署详细信息。
5. 部署资源后，转到该资源并查看其“密钥和终结点”页面。 记下终结点 URI，稍后你会用到它。

## 配置警报

通过定义预警规则开始监视，以便可以检测 Azure AI 服务资源中的活动。

1. 在 Azure 门户中，前往 Azure AI 服务资源并查看其“警报”**** 页（在“监视”**** 部分中）。
2. 选择“+ 创建”下拉菜单，然后选择“警报规则”
3. 在“创建警报规则”**** 页的“范围”**** 下，验证是否列出了 Azure AI 服务资源。 （如果打开了“选择信号”窗格，请将其关闭）
4. 选择“**条件**”选项卡，然后选择“**查看所有信号**”链接以在右侧显示“**选择信号**”窗格，你可以在其中选择要监视的信号类型。
5. 在“**信号类型**”列表中，向下滚动到“**活动日志**”部分，然后选择“**列表密钥（认知服务 API 帐户）**”。 然后选择**应用**。
6. 查看过去 6 小时的活动。
7. 选择“操作”选项卡。请注意，可以指定一个操作组。 这样，你可以配置触发警报时的自动化操作，例如发送电子邮件通知。 在本练习中，我们不会执行此操作；但在生产环境中，执行此操作可能会很有用。
8. 在“详细信息”选项卡中，将“预警规则名称”设置为“密钥列表警报”  。
9. 选择“查看 + 创建”。 
10. 查看警报的配置。 选择“创建”，等待预警规则创建完成。
11. 现在，可以使用以下命令来获取 Azure AI 服务密钥的列表，将“&lt;resourceName&gt;”** 替换为 Azure AI 服务资源的名称，并将“&lt;resourceGroup&gt;”** 替换为在其中创建资源的资源组名称。

    ```
    az cognitiveservices account keys list --name <resourceName> --resource-group <resourceGroup>
    ```

    该命令返回 Azure AI 服务资源的密钥列表。

    > **请注意**，如果尚未登录到 Azure CLI，可能需要先运行 `az login`，列表密钥命令才能正常运作。

12. 切换回包含 Azure 门户的浏览器，并刷新“警报”页面。 表中应会出现一个 Sev 4 警报（如果没有，请等待五分钟，然后再次刷新）。
13. 选择警报以查看其详细信息。

## 可视化指标

除了定义警报之外，还可以查看 Azure AI 服务资源的指标以监视其利用率。

1. 在 Azure 门户的 Azure AI 服务资源页中，选择“指标”****（在“监视”**** 部分中）。
2. 如果没有现有图表，请选择“+ 新建图表”。 然后在“指标”列表中，查看可以可视化的可能指标，然后选择“总调用次数” 。
3. 在“聚合”列表中，选择“计数”。  通过此设置，你可以监视对 Azure AI 服务资源的总调用次数，这对于确定一段时间内使用的服务数量非常有用。
4. 要生成对 Azure AI 服务的一些请求，可以使用 curl****，这是一个用于处理 HTTP 请求的命令行工具。 在编辑器中，打开 **rest-test.cmd** 并编辑其中包含的 **curl** 命令（如下所示），并将 *&lt;yourEndpoint&gt;* 和 *&lt;yourKey&gt;* 分别替换为终结点 URI 和 **Key1** 密钥，以在 Azure AI 服务资源中使用文本分析 API。

    ```
    curl -X POST "<your-endpoint>/language/:analyze-text?api-version=2023-04-01" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <your-key>" --data-ascii "{'analysisInput':{'documents':[{'id':1,'text':'hello'}]}, 'kind': 'LanguageDetection'}"
    ```

5. 保存所有更改，然后运行以下命令：

    ```
    ./rest-test.cmd
    ```

    该命令返回一个 JSON 文档，其中包含有关在输入数据中检测到的语言（该语言应为英语）的信息。

6. 多次重新运行 rest-test 命令，以生成一些调用活动（可以使用 ^ 键循环显示先前的命令） 。
7. 返回到 Azure 门户中的“指标”页面，并刷新“总调用次数”计数图表 。 使用 curl 进行的调用可能需要几分钟的时间才能反映在图表中，请不断刷新图表，直到图表更新为包含这些调用为止。

## 清理资源

如果不将本实验室创建的 Azure 资源用于其他培训模块，则可以删除这些资源以避免产生更多费用。

1. 打开 Azure 门户网站 `https://portal.azure.com`，在顶部搜索栏中搜索在本实验室中创建的资源。

2. 在资源页面上，选择**删除**，然后按照说明删除资源。 或者，也可以删除整个资源组，同时清理所有资源。

## 更多信息

用于监视 Azure AI 服务的选项之一是使用诊断日志记录**。 启用后，诊断日志记录将捕获有关 Azure AI 服务资源使用情况的丰富信息，是一个有用的监视和调试工具。 设置诊断日志记录后可能需要超过一个小时才会生成信息，这就是我们在本练习中没有对其进行探讨的原因，但你可以在 [Azure AI 服务文档](https://docs.microsoft.com/azure/ai-services/diagnostic-logging)中了解关于它的更多信息。
