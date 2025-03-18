---
lab:
  title: 管理 Azure AI 服务安全性
  module: Module 2 - Developing AI Apps with Azure AI Services
---

# 管理 Azure AI 服务安全性

安全性是任何应用程序的关键考虑因素，作为一名开发人员，你应该确保只有需要 Azure AI 服务等资源的人才能访问这些资源。

对 Azure AI 服务的访问通常通过身份验证密钥来控制，身份验证密钥在最初创建 Azure AI 服务资源时生成。

## 在 Visual Studio Code 中克隆存储库

你将使用 Visual Studio Code 开发代码。 应用程序的代码文件已在 GitHub repo 中提供。

> **提示**：如果已克隆 **mslearn-ai-services** 存储库，请在 Visual Studio Code 中打开它。 否则，请按照以下步骤将其克隆到开发环境中。

1. 启动 Visual Studio Code。
2. 打开面板 (SHIFT+CTRL+P) 并运行“**Git：Clone**”命令，以将 `https://github.com/MicrosoftLearning/mslearn-ai-services` 存储库克隆到本地文件夹（任意文件夹均可）。
3. 克隆存储库后，在 Visual Studio Code 中打开文件夹。
4. 如有必要，等待其他文件安装完毕，以支持存储库中的 C# 代码项目

    > **注意**：如果系统提示你添加生成和调试所需的资产，请选择**以后再说**。

5. 展开 `Labfiles/02-ai-services-security` 文件夹。

已提供适用于 C# 和 Python 的代码。 展开首选语言的文件夹。

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

## 管理身份验证密钥

创建 Azure AI 服务资源时，生成了两个身份验证密钥。 可以在 Azure 门户中或使用 Azure 命令行接口 (CLI) 来管理这两个密钥。 

1. 选择一种获取身份验证密钥和终结点的方法： 

    **使用 Azure 门户**：在 Azure 门户中，前往 Azure AI 服务资源并查看其“**密钥和终结点**”页。 此页面中包含连接资源并在开发的应用程序中使用该资源所需的信息。 尤其是在下列情况下：
    - 客户端应用程序可向其发送请求的 HTTP 终结点。
    - 可用于身份验证的两个密钥（客户端应用程序可以使用其中任何一个密钥。 一种常见的做法是将其中一个密钥用于开发，而将另一个用于生产。 你可以在开发人员完成其工作之后轻松地重新生成开发密钥，以防止继续访问。）
    - 托管资源的位置。 对于针对某些（但不是全部）API 的请求，这是必需的。

    **使用命令行**：或者，可以使用以下命令获取 Azure AI 服务密钥的列表。 在 Visual Studio Code 中，打开一个新的终端。 然后粘贴以下命令；将“*&lt;resourceName&gt;*”替换为 Azure AI 服务资源的名称，并将“*&lt;resourceGroup&gt;*”替换为在其中创建资源的资源组的名称。

    ```
    az cognitiveservices account keys list --name <resourceName> --resource-group <resourceGroup>
    ```

    此命令返回 Azure AI 服务资源的密钥列表，其中包含两个密钥，分别名为“key1”**** 和“key2”****。

    > **提示**：如果尚未对 Azure CLI 进行身份验证，请先运行 `az login` 并登录到帐户。

2. 若要测试 Azure AI 服务，可以使用 curl****，这是一个用于处理 HTTP 请求的命令行工具。 在 **02-ai-services-security** 文件夹中，打开 **rest-test.cmd** 并编辑其中包含的 **curl** 命令（如下所示），并将 *&lt;yourEndpoint&gt;* 和 *&lt;yourKey&gt;* 分别替换为终结点 URI 和 **Key1** 密钥，以在 Azure AI 服务资源中使用文本分析 API。

    ```bash
    curl -X POST "<yourEndpoint>/language/:analyze-text?api-version=2023-04-01" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <your-key>" --data-ascii "{'analysisInput':{'documents':[{'id':1,'text':'hello'}]}, 'kind': 'LanguageDetection'}"
    ```

3. 保存所做更改。 在终端中，导航到“02-ai-services-security”文件夹。 （**备注**：可以通过右键单击资源管理器中的“02-ai-services-security”文件夹，然后选择“*在集成终端中打开*”来执行此操作）。 然后，运行以下命令：

    ```
    ./rest-test.cmd
    ```

该命令返回一个 JSON 文档，其中包含有关在输入数据中检测到的语言（该语言应为英语）的信息。

4. 如果密钥已泄露，或者拥有密钥的开发人员不再需要访问权限，你可以在门户中或使用 Azure CLI 重新生成密钥。 运行以下命令以重新生成 key1 密钥（替换资源的 &lt;resourceName&gt; 和 &lt;resourceGroup&gt;） 。

    ```
    az cognitiveservices account keys regenerate --name <resourceName> --resource-group <resourceGroup> --key-name key1
    ```

系统将返回 Azure AI 服务资源的密钥列表 - 请注意，自上次检索密钥后，key1**** 已更改。

5. 使用旧密钥重新运行 **rest-test** 命令（可以使用键盘上的 **^** 键循环显示以前的命令），并验证它现在是否失败。
6. 在 rest-test.cmd 中编辑 curl 命令，并将密钥替换为新的 key1 值，然后保存更改 。 然后重新运行 rest-test 命令并验证它是否成功。

> **提示**：在本练习中，你使用了 Azure CLI 参数的全名，例如 --resource-group。  你也可以使用更短的其他名称（例如 -g），以使命令更简洁（但更难理解）。  [Azure AI 服务 CLI 命令引用](https://docs.microsoft.com/cli/azure/cognitiveservices?view=azure-cli-latest)列出了每个 Azure AI 服务 CLI 命令的参数选项。

## 使用 Azure 密钥保管库保护密钥访问

可以通过使用身份验证密钥来开发使用 Azure AI 服务的应用程序。 但是，这意味着应用程序代码必须能够获取密钥。 一种选择是将密钥存储在部署了应用程序的环境变量或配置文件中，但是这种方法使密钥容易受到未经授权的访问。 在 Azure 上开发应用程序时，更好的方法是将密钥通过安全方式存储在 Azure 密钥保管库中，并通过托管标识（也就是应用程序本身使用的用户帐户）提供对密钥的访问。

### 创建密钥保管库并添加机密

首先，需要创建密钥保管库并为 Azure AI 服务密钥添加机密**。

1. 记下 Azure AI 服务资源的 key1**** 值（或将其复制到剪贴板）。
2. 在 Azure 门户的“主页”上，选择“&#65291; 创建资源”按钮，搜索密钥保管库，然后使用以下设置创建“密钥保管库”资源 ：

    - “基本”选项卡
        - **订阅**：Azure 订阅
        - **资源组**：与你的 Azure AI服务资源相同的资源组**
        - Key Vault 名称：输入唯一名称
        - **区域**：Azure AI 服务资源所在的同一区域**
        - **定价层**：标准
    
    - “访问配置”选项卡
        -  **权限模型**：保管库访问策略
        - 向下滚动到“访问策略”**** 部分，然后使用左侧的复选框选择用户。 然后，依次选择“**查看 + 创建**”、“**创建**”，以创建你的资源。
     
3. 等待部署完成，然后转到密钥保管库资源。
4. 在左侧导航窗格中，选择“对象”部分中的“机密”。
5. 选择“+ 生成/导入”并使用以下设置添加新机密：
    - **上传选项**：手动
    - **名称**：AI-Services-Key *（必须准确匹配该名称，因为稍后你将运行基于该名称检索机密的代码）*
    - **机密值**：***key1** Azure AI 服务密钥*
6. 选择**创建**。

### 创建服务主体

要访问密钥保管库中的机密，应用程序必须使用拥有对机密的访问权限的服务主体。 你将使用 Azure 命令行接口 (CLI) 创建服务主体，查找其对象 ID 并授予其对 Azure 保管库中机密的访问权限。

1. 运行以下 Azure CLI 命令，将 *&lt;spName&gt;* 替换为应用程序标识的唯一合适名称（例如，*ai-app*，末尾追加你的首字母缩写；该名称在租户中必须是唯一的）。 此外，将“&lt;subscriptionId&gt;”** 和“&lt;resourceGroup&gt;”** 分别替换为订阅 ID 和资源组（包含 Azure AI 服务和密钥保管库资源）的正确值：

    > **提示**：如果不确定订阅 Id，请使用 az account show 命令检索订阅信息 - 订阅 ID 是输出中的 ID 属性 。 如果看到有关对象已存在的错误，请选择其他唯一名称。

    ```
    az ad sp create-for-rbac -n "api://<spName>" --role owner --scopes subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>
    ```

该命令的输出包括有关新服务主体的信息。 应如下所示：

    ```
    {
        "appId": "abcd12345efghi67890jklmn",
        "displayName": "api://ai-app-",
        "password": "1a2b3c4d5e6f7g8h9i0j",
        "tenant": "1234abcd5678fghi90jklm"
    }
    ```

记下 **appId**、**密码**和**租户**值 - 稍后你将用到这些值（如果关闭此终端，将无法检索密码，因此，务必立刻记下这些值。你可以将输出粘贴到本地计算机的新文本文件中，以确保以后可以找到所需的值！）

2. 若要获取服务主体的对象 ID，请运行以下 Azure CLI 命令，将 &lt;appId&gt; 替换为服务主体的应用程序 ID 的值。

    ```
    az ad sp show --id <appId>
    ```

3. 复制响应中返回的 json 中的 `id` 值。 
3. 若要为新服务主体分配访问密钥保管库中机密的权限，请运行以下 Azure CLI 命令，将 *&lt;keyVaultName&gt;* 替换为 Azure 密钥保管库资源的名称，并将 *&lt;objectId&gt;* 替换为你刚刚复制的服务主体的 ID 值。

    ```
    az keyvault set-policy -n <keyVaultName> --object-id <objectId> --secret-permissions get list
    ```

### 在应用程序中使用服务主体

现在，你可以在应用程序中使用服务主体标识，使其能够访问密钥保管库中的机密 Azure AI 服务密钥，并使用该密钥连接到 Azure AI 服务资源。

> **注意**：在本练习中，我们将服务主体凭据存储在应用程序配置中，并使用这些凭据在应用程序代码中对 ClientSecretCredential 标识进行身份验证。 这样做适用于开发和测试，但在实际的生产应用程序中，管理员将为应用程序分配托管标识，以便它使用服务主体标识来访问资源，而无需缓存或存储密码。

1. 在终端中，通过运行 `cd C-Sharp` 或 `cd Python`，切换到 **C-Sharp** 或 **Python** 文件夹，具体取决于语言首选项。 然后运行 `cd keyvault_client` 以导航到应用文件夹中。
2. 通过运行针对你的语言首选项的适当命令，在 Azure AI 服务资源中安装使用 Azure 密钥保管库和文本分析 API 所需的包：

    **C#**

    ```
    dotnet add package Azure.AI.TextAnalytics --version 5.3.0
    dotnet add package Azure.Identity --version 1.12.0
    dotnet add package Azure.Security.KeyVault.Secrets --version 4.6.0
    ```

    **Python**

    ```
    pip install azure-ai-textanalytics==5.3.0
    pip install azure-identity==1.17.1
    pip install azure-keyvault-secrets==4.8.0
    ```

3. 查看 keyvault-client 文件夹的内容，并注意其中包含一个配置设置文件：
    - **C#** ：appsettings.json
    - **Python**：.env

    打开配置文件并更新其中包含的配置值以反映以下设置：
    
    - Azure AI 服务资源终结点****
    - Azure 密钥保管库资源的名称
    - 服务主体的租户
    - 服务主体的 appId
    - 服务主体的密码

     通过按 **CTRL+S** 保存所做的更改。
4. 请注意，keyvault-client 文件夹中包含客户端应用程序的代码文件：

    - **C#** ：Program.cs
    - **Python**：keyvault-client.py

    打开代码文件并查看其中包含的代码，并注意以下详细信息：
    - 已导入安装的 SDK 的命名空间
    - Main**** 函数中的代码会检索应用程序配置设置，然后使用服务主体凭据从密钥保管库获取 Azure AI 服务密钥。
    - GetLanguage 函数使用 SDK 来创建服务的客户端，然后使用该客户端检测文本的输入语言。
5. 输入以下命令以运行程序：

    **C#**

    ```
    dotnet run
    ```

    **Python**

    ```
    python keyvault-client.py
    ```

6. 系统提示时，输入一些文本并查看服务检测到的语言。 例如，尝试输入“Hello”、“Bonjour”和“Gracias”。
7. 测试应用程序后，输入“quit”以停止程序。

## 清理资源

如果不将本实验室创建的 Azure 资源用于其他培训模块，则可以删除这些资源以避免产生更多费用。

1. 打开 Azure 门户网站 `https://portal.azure.com`，在顶部搜索栏中搜索在本实验室中创建的资源。

2. 在资源页面上，选择**删除**，然后按照说明删除资源。 或者，也可以删除整个资源组，同时清理所有资源。

## 更多信息

要详细了解如何保护 Azure AI 服务，请参见 [Azure AI 服务安全性文档](https://docs.microsoft.com/azure/ai-services/security-features)。
