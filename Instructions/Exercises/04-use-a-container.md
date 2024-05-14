---
lab:
  title: 使用 Azure AI 服务容器
  module: Module 2 - Developing AI Apps with Azure AI Services
---

# 使用 Azure AI 服务容器

使用 Azure 中托管的 Azure AI 服务，应用程序开发人员可以专注于自己代码的基础结构，同时享受 Microsoft 管理的可缩放服务带来的好处。 但是，在许多情况下，组织需要对其服务基础结构以及服务之间传递的数据进行更多的控制。

许多 Azure AI 服务 API 可以打包并部署在容器** 中，从而使组织可以在自己的基础结构（例如，在本地 Docker 服务器、Azure 容器实例或 Azure Kubernetes 服务群集中）中托管 Azure AI 服务。 容器化 Azure AI 服务需要与基于 Azure 的 Azure AI 服务帐户进行通信以支持计费；但是应用程序数据不会传递到后端服务，组织可以更好地控制其容器的部署配置，从而实现用于身份验证、可伸缩性和其他考虑因素的自定义解决方案。

> 注意：目前正在调查的一个问题是，一些用户遇到容器无法正确部署的情况，并且对这些容器的调用失败。 解决此问题后，我们将立即对此实验室进行更新。

## 在 Visual Studio Code 中克隆存储库

你将使用 Visual Studio Code 开发代码。 应用程序的代码文件已在 GitHub repo 中提供。

> **提示**：如果已克隆 **mslearn-ai-services** 存储库，请在 Visual Studio Code 中打开它。 否则，请按照以下步骤将其克隆到开发环境中。

1. 启动 Visual Studio Code。
2. 打开面板 (SHIFT+CTRL+P) 并运行“**Git：克隆**”命令，以将 `https://github.com/MicrosoftLearning/mslearn-ai-services` 存储库克隆到本地文件夹（任意文件夹均可）。
3. 克隆存储库后，在 Visual Studio Code 中打开文件夹。
4. 如有必要，等待其他文件安装完毕，以支持存储库中的 C# 代码项目

    > **注意**：如果系统提示你添加生成和调试所需的资产，请选择**以后再说**。

5. 展开 `Labfiles/04-use-a-container` 文件夹。

## 预配 Azure AI 服务资源

如果订阅中还没有，则需要预配 **Azure AI 服务**资源。

1. 打开 Azure 门户 (`https://portal.azure.com`)，然后使用与你的 Azure 订阅关联的 Microsoft 帐户登录。
2. 在顶部搜索栏中，搜索 *Azure AI 服务*，选择Azure AI 服务****，并使用以下设置创建 Azure AI 服务多服务帐户资源：
    - **订阅**：*Azure 订阅*
    - **资源组**：*选择或创建一个资源组（如果使用受限制的订阅，你可能无权创建新的资源组 - 请使用提供的资源组）*
    - **区域**：*选择任何可用区域*
    - **名称**：*输入唯一名称*
    - **定价层**：标准版 S0
3. 选中所需的复选框并创建资源。
4. 等待部署完成，然后查看部署详细信息。
5. 部署资源后，转到该资源并查看其**密钥和终结点**页面。 你将在下一个过程中用到此页面中的终结点和其中一个密钥。

## 安装和运行文本分析容器

容器映像中提供了许多常用的 Azure AI 服务 API。 如需完整列表，请参见 [Azure AI 服务文档](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-container-support#container-availability-in-azure-cognitive-services)。 在本练习中，你将使用容器映像作为文本分析语言检测 API，但原理对于所有可用映像都是相同的。

1. 在 Azure 门户的“主页”上，选择“&#65291; 创建资源”按钮，搜索容器实例，然后使用以下设置创建“容器实例”资源 ：

    - 基本信息：
        - **订阅**：Azure 订阅
        - **资源组**：选择包含 Azure AI 服务资源的资源组**
        - **容器名称**：输入唯一名称
        - **区域**：选择任何可用区域
        - **映像源**：其他注册表
        - **映像类型**：公有
        - **映像**：`mcr.microsoft.com/azure-cognitive-services/textanalytics/language:latest`
        - **OS 类型**：Linux
        - **大小**：1 个 vCPU，12 GB 内存
    - **网络**：
        - **网络类型**：公有
        - **DNS 名称标签**：输入容器终结点的唯一名称
        - **端口**：将 TCP 端口从 80 更改为 5000
    - **高级**：
        - **重启策略**：失败时
        - **环境变量**：

            | 标记为安全 | 键 | Value |
            | -------------- | --- | ----- |
            | 是 | `ApiKey` | Azure AI 服务资源的任一密钥** |
            | 是 | `Billing` | Azure AI 服务资源的终结点 URI** |
            | 否 | `Eula` | `accept` |

        - **命令重写**：[ ]
    - **标记**：
        - 请勿添加任何标记

2. 依次选择“**查看 + 创建**”、“**创建**”。 等待部署完成，然后转到部署的资源。
    > **备注**：请注意，将 Azure AI 容器部署到 Azure 容器实例通常需要 5-10 分钟（预配）才能使用。
3. 在“概述”页面上观察容器实例资源的以下属性：
    - 状态：状态应为“正在运行”。
    - **IP 地址**：该地址是可用于访问容器实例的公共 IP 地址。
    - **FQDN：** 这是容器实例资源的完全限定的域名，你可以使用它来访问容器实例而不是 IP 地址。

    > **备注**：在本练习中，你已将用于文本翻译的 Azure AI 服务容器映像部署到了 Azure 容器实例 (ACI) 资源。 可以使用类似的方法将其部署到自己计算机或网络上的 [Docker](https://www.docker.com/products/docker-desktop)** 主机上，方法是运行以下命令（在一行上），以将语言检测容器部署到本地 Docker 实例，并将“&lt;yourEndpoint&gt;”** 和“&lt;yourKey&gt;”** 分别替换为终结点 URI 和 Azure AI 服务资源的任一密钥。
    > 该命令将在本地计算机上查找映像，如果找不到映像，它将从 mcr.microsoft.com 映像注册表中拉取映像，并将其部署到 Docker 实例。 部署完成后，容器将启动并侦听端口 5000 上的传入请求。

    ```
    docker run --rm -it -p 5000:5000 --memory 12g --cpus 1 mcr.microsoft.com/azure-cognitive-services/textanalytics/language:latest Eula=accept Billing=<yourEndpoint> ApiKey=<yourKey>
    ```

## 使用容器

1. 在编辑器中，打开 rest-test.cmd 并编辑其中包含的 curl 命令（如下所示），将 *&lt;your_ACI_IP_address_or_FQDN&gt;* 替换为容器的 IP 地址或 FQDN********。

    ```
    curl -X POST "http://<your_ACI_IP_address_or_FQDN>:5000/text/analytics/v3.0/languages" -H "Content-Type: application/json" --data-ascii "{'documents':[{'id':1,'text':'Hello world.'},{'id':2,'text':'Salut tout le monde.'}]}"
    ```

2. 按 Ctrl+S 保存对脚本做出的更改****。 请注意，无需指定 Azure AI 服务终结点或密钥，请求将由容器化服务处理。 容器依次与 Azure 中的服务定期通信，以报告计费使用情况，但不发送请求数据。
3. 输入以下命令以运行该脚本：

    ```
    ./rest-test.cmd
    ```

4. 验证该命令是否返回一个 JSON 文档，其中包含有关在两个输入文档中检测到的语言（该语言应为英语和法语）的信息。

## 清理

如果你已完成对容器实例的试验，则应将其删除。

1. 在 Azure 门户中，打开用于创建本练习资源的资源组。
2. 选择容器实例资源并将其删除。

## 清理资源

如果不将本实验室创建的 Azure 资源用于其他培训模块，则可以删除这些资源以避免产生更多费用。

1. 打开 Azure 门户网站 `https://portal.azure.com`，在顶部搜索栏中搜索在本实验室中创建的资源。

2. 在资源页面上，选择**删除**，然后按照说明删除资源。 或者，也可以删除整个资源组，同时清理所有资源。

## 更多信息

有关 Azure AI 服务容器化的详细信息，请参见 [Azure AI 服务容器文档](https://learn.microsoft.com/azure/ai-services/cognitive-services-container-support)。
