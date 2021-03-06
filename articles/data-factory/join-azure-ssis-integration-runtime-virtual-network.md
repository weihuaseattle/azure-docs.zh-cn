---
title: "将 Azure-SSIS 集成运行时加入 VNET | Microsoft Docs"
description: "了解如何将 Azure-SSIS 集成运行时加入 Azure 虚拟网络。"
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: spelluru
ms.openlocfilehash: 2131aa75dcfb975f11cff9800087c3e4e7170378
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2018
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>将 Azure-SSIS 集成运行时加入虚拟网络
对于以下情况，请将 Azure-SSIS 集成运行时 (IR) 加入 Azure 虚拟网络 (VNet)： 

- 在属于 VNet 的 SQL Server 托管实例（个人预览版）上承载 SSIS 目录数据库。
- 想要从 Azure-SSIS 集成运行时中运行的 SSIS 包连接到本地数据存储。

 使用 Azure 数据工厂版本 2（预览版）可将 Azure-SSIS 集成运行时加入经典 VNet 或 Azure 资源管理器 VNet。 

 > [!NOTE]
> 本文适用于目前处于预览状态的数据工厂版本 2。 如果使用正式版 (GA) 1 版本的数据工厂服务，请参阅 [数据工厂版本 1 文档](v1/data-factory-introduction.md)。

## <a name="access-on-premises-data-stores"></a>访问本地数据存储
如果 SSIS 包仅访问公有云数据存储，则不需要将 Azure-SSIS IR 加入 VNet。 如果 SSIS 包访问本地数据存储，则必须将 Azure-SSIS IR 加入已连接到本地网络的 VNet。 如果 SSIS 目录承载于不在 VNet 上的 Azure SQL 数据库中，则需要打开相应的端口。 如果 SSIS 目录承载在 Azure 资源管理器 VNet 或经典 VNet 上的 Azure SQL 托管实例中，则可将 Azure-SSIS IR 加入到相同的 VNet 中，或加入到已与 Azure SQL 托管实例所在的 VNet 建立 VNet 到 VNet 连接的不同 VNet 中。 以下部分提供了更多详细信息。

下面是几个要点： 

- 如果已有现有的 VNet 连接到本地网络，请先创建 Azure-SSIS 集成运行时要加入到的 [Azure 资源管理器 VNet](../virtual-network/quick-create-portal.md#create-a-virtual-network) 或[经典 VNet](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)。 然后，配置从该 VNet 到本地网络的站点到站点 [VPN 网关连接](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md)/[ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) 连接。
- 如果已有现有的 Azure 资源管理器或经典 VNet 连接到 Azure-SSIS 集成运行时所在同一位置中的本地网络，则可将 IR 加入该 VNet。
- 如果已有现有的经典 VNet 连接到与 Azure-SSIS IR 所在位置不同的位置中的本地网络，可以先创建 Azure-SSIS IR 要加入到的[经典 VNet](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)。 然后，配置[经典到经典 VNet](../vpn-gateway/vpn-gateway-howto-vnet-vnet-portal-classic.md) 连接。 也可以创建 Azure-SSIS 集成运行时要加入到的 [Azure 资源管理器 VNet](../virtual-network/quick-create-portal.md#create-a-virtual-network)。 然后，配置[经典 VNet 到 Azure 资源管理器 VNet](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) 连接。
- 如果已有现有的 Azure 资源管理器 VNet 连接到与 Azure-SSIS IR 所在位置不同的位置中的本地网络，可以先创建 Azure-SSIS IR 要加入到的 [Azure 资源管理器 VNet](../virtual-network/quick-create-portal.md##create-a-virtual-network)。 然后，配置“Azure 资源管理器 VNet到 Azure 资源管理器 VNet”连接。 也可以创建 Azure-SSIS IR 要加入到的[经典 VNet](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)。 然后，配置[经典到 Azure 资源管理器 VNet](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) 连接。

## <a name="domain-name-services-server"></a>域名服务服务器 
如果需要在 Azure-SSIS 集成运行时加入的 VNet 中使用自己的域名服务 (DNS) 服务器，请遵循指导以[确保 VNet 中 Azure-SSIS 集成运行时的节点可以解析 Azure 终结点](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server)。

## <a name="network-security-group"></a>网络安全组
如果需要在 Azure-SSIS 集成运行时加入的 VNet 中实现网络安全组 (NSG)，请允许通过以下端口传送入站/出站流量：

| 端口 | 方向 | 传输协议 | 目的 | 入站源/出站目标 |
| ---- | --------- | ------------------ | ------- | ----------------------------------- |
| 10100、20100、30100（如果将 IR 加入经典 VNet）<br/><br/>29876、29877（如果将 IR 加入 Azure 资源管理器 VNet） | 入站 | TCP | Azure 服务使用这些端口来与 VNet 中 Azure-SSIS 集成运行时的节点通信。 | Internet | 
| 443 | 出站 | TCP | VNet 中 Azure-SSIS 集成运行时的节点使用此端口来访问 Azure 服务，例如 Azure 存储、事件中心等。 | INTERNET | 
| 1433<br/>11000-11999<br/>14000-14999  | 出站 | TCP | VNet 中 Azure-SSIS 集成运行时的节点使用这些端口来访问 Azure SQL 数据库服务器承载的 SSISDB（不适用于 Azure SQL 托管实例承载的 SSISDB）。 | Internet | 

## <a name="azure-portal-data-factory-ui"></a>Azure 门户（数据工厂 UI）
本部分介绍如何使用 Azure 门户和数据工厂 UI 如何将现有的 Azure SSIS 运行时加入 VNet（经典或 Azure 资源管理器）。 首先，在将 Azure SSIS IR 加入 VNet 之前，需要正确配置 VNet。 根据 VNet 的类型（经典或 Azure 资源管理器）完成以下两个部分之一。 然后，继续参阅第三部分，将 Azure SSIS IR 加入 VNet。 

### <a name="use-portal-to-configure-a-classic-vnet"></a>使用门户配置经典 VNet
首先需要配置 VNet，然后才能将 Azure-SSIS IR 加入该 VNet。

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 单击“更多服务”。 筛选并选择“虚拟网络(经典)”。
3. 在列表中筛选并选择自己的**虚拟网络**。 
4. 在“虚拟网络(经典)”页中选择“属性”。 

    ![经典 VNet 资源 ID](media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png)
5. 单击“资源 ID”对应的复制按钮，将经典网络的资源 ID 复制到剪贴板。 将剪贴板中的 ID 保存到 OneNote 或某个文件中。
6. 在左侧菜单中单击“子网”，确保**可用地址**的数目大于 Azure-SSIS 集成运行时中的节点数。

    ![VNet 中的可用地址数](media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png)
7. 将 **MicrosoftAzureBatch** 加入 VNet 的“经典虚拟机参与者”角色。 
    1. 在左侧菜单中单击“访问控制(IAM)”，并在工具栏中单击“添加”。
    
        ![“访问控制”->“添加”](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png) 
    2. 在“添加权限”页中，为“角色”选择“经典虚拟机参与者”。 在 **Select** 文本框中复制/粘贴 **ddbf3205-c6bd-46ae-8127-60eb93363864**，然后从搜索结果列表中选择 **Microsoft Azure Batch**。 
    
        ![添加权限 - 搜索](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)
    3. 单击“保存”以保存设置并关闭页面。
    
        ![保存访问设置](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)
    4. 确认参与者列表中出现了“MicrosoftAzureBatch”。
    
        ![确认 AzureBatch 访问权限](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)
5. 验证是否已将 Azure Batch 提供程序注册到包含 VNet 的 Azure 订阅中，或注册 Azure Batch 提供程序。 如果订阅中已包含 Azure Batch 帐户，则已经为 Azure Batch 注册了订阅。
    1. 在 Azure 门户上的左侧菜单中，单击“订阅”。 
    2. 选择**订阅**。 
    3. 在左侧单击“资源提供程序”，确认 `Microsoft.Batch` 是注册的提供程序。 
    
        ![confirmation-batch-registered](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

    如果列表中未出现 `Microsoft.Batch`，若要注册该提供程序，请在订阅中[创建一个空的 Azure Batch 帐户](../batch/batch-account-create-portal.md)。 稍后可以删除该帐户。 

### <a name="use-portal-to-configure-an-azure-resource-manager-vnet"></a>使用门户配置 Azure 资源管理器 VNet
首先需要配置 VNet，然后才能将 Azure-SSIS IR 加入该 VNet。

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 单击“更多服务”。 筛选并选择“虚拟网络”。
3. 在列表中筛选并选择自己的**虚拟网络**。 
4. 在“虚拟网络”页中选择“属性”。 
5. 单击“资源 ID”对应的复制按钮，将虚拟网络的资源 ID 复制到剪贴板。 将剪贴板中的 ID 保存到 OneNote 或某个文件中。
6. 在左侧菜单中单击“子网”，确保**可用地址**的数目大于 Azure-SSIS 集成运行时中的节点数。
5. 验证是否已将 Azure Batch 提供程序注册到包含 VNet 的 Azure 订阅中，或注册 Azure Batch 提供程序。 如果订阅中已包含 Azure Batch 帐户，则已经为 Azure Batch 注册了订阅。
    1. 在 Azure 门户上的左侧菜单中，单击“订阅”。 
    2. 选择**订阅**。 
    3. 在左侧单击“资源提供程序”，确认 `Microsoft.Batch` 是注册的提供程序。 
    
        ![confirmation-batch-registered](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

    如果列表中未出现 `Microsoft.Batch`，若要注册该提供程序，请在订阅中[创建一个空的 Azure Batch 帐户](../batch/batch-account-create-portal.md)。 稍后可以删除该帐户。

### <a name="join-the-azure-ssis-ir-to-a-vnet"></a>将 Azure SSIS IR 加入 VNet


1. 在 [Azure 门户](https://portal.azure.com)中，选择左侧菜单中的“数据工厂”。 如果菜单中未显示“数据工厂”，请选择“更多服务”，然后在“智能 + 分析”部分选择“数据工厂”。 
    
    ![数据工厂列表](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)
2. 在列表中选择包含 Azure SSIS 集成运行时的数据工厂。 随后会显示该数据工厂的主页。 选择“创作和部署”磁贴。 单独的选项卡中会显示数据工厂用户界面 (UI)。 

    ![数据工厂主页](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)
3. 在数据工厂 UI 中切换到“编辑”选项卡，选择“连接”，然后切换到“集成运行时”选项卡。 

    ![“集成运行时”选项卡](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)
4. 如果 Azure SSIS IR 正在运行，请在集成运行时列表的“操作”列中，选择与 Azure SSIS IR 对应的“停止”按钮。 只有先停止 IR 才能对其进行编辑。 

    ![停止 IR](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)
1. 在集成运行时列表的“操作”列中，选择与 Azure SSIS IR 对应的“编辑”按钮。

    ![编辑集成运行时](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)
5. 在“集成运行时安装”窗口的“常规设置”页中，选择“下一步”。 

    ![IR 安装 - 常规设置](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-general-settings.png)
6. 在“SQL 设置”页上输入管理员**密码**，然后选择“下一步”。

    ![IR 安装 - SQL 设置](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-sql-settings.png)
7. 在“高级设置”页上执行以下操作： 

    1. 选中“选择 Azure-SSIS 集成运行时要加入的 VNet，并允许 Azure 服务配置 VNet 权限/设置”复选框。 
    2. 对于“类型”，请指定 VNet 是经典 VNet 还是 Azure 资源管理器 VNet。 
    3. 对于“VNet 名称”，请选择 VNet。
    4. 对于“子网名称”，请选择 VNet 中的子网。 
    5. 选择“更新”。 

        ![IR 安装 - 高级设置](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-advanced-settings.png)
8. 现在，可以在“操作”中使用与 Azure SSIS IR 对应的“启动”按钮启动该 IR。 启动 Azure SSIS IR 大约需要 20 分钟。 


## <a name="azure-powershell"></a>Azure PowerShell

### <a name="configure-vnet"></a>配置 VNet
首先需要配置 VNet，然后才能将 Azure-SSIS IR 加入该 VNet。 添加以下脚本以自动配置将 Azure-SSIS 集成运行时加入 VNet 的 VNet 权限/设置。

```powershell
# Register to Azure Batch resource provider
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    $BatchObjectId = (Get-AzureRmADServicePrincipal -ServicePrincipalName "MicrosoftAzureBatch").Id
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign VM contributor role to Microsoft.Batch
        New-AzureRmRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-an-azure-ssis-ir-and-join-it-to-a-vnet"></a>创建 Azure-SSIS IR 并将其加入到 VNet
可以创建 Azure-SSIS IR，同时将其加入到 VNet。 有关创建 Azure-SSIS IR 并同时将其加入到 VNet 的完整脚本和说明，请参阅[创建 Azure-SSIS IR](create-azure-ssis-integration-runtime.md#azure-powershell)。

### <a name="join-an-existing-azure-ssis-ir-to-a-vnet"></a>将现有 Azure-SSIS IR 加入 VNet
[创建 Azure-SSIS 集成运行时](create-azure-ssis-integration-runtime.md)一文中的脚本显示如何在同一个脚本中创建 Azure-SSIS IR 并将其加入到 VNet。 如果已有 Azure-SSIS，请执行以下步骤将其加入到 VNet。 

1. 停止 Azure-SSIS IR。
2. 将 Azure-SSIS IR 配置为加入 VNet。 
3. 启动 Azure-SSIS IR。 

### <a name="define-the-variables"></a>定义变量

```powershell
$ResourceGroupName = "<Azure resource group name>"
$DataFactoryName = "<Data factory name>" 
$AzureSSISName = "<Specify Azure-SSIS IR name>"
## These two parameters apply if you are using a VNet and an Azure SQL Managed Instance (private preview) 
# Specify information about your classic or Azure Resource Manager virtual network (VNet).
$VnetId = "<Name of your Azure virtual netowrk>"
$SubnetName = "<Name of the subnet in VNet>"
```

### <a name="stop-the-azure-ssis-ir"></a>停止 Azure-SSIS IR
停止 Azure-SSIS 集成运行时，然后才能将其加入到 VNet。 此命令释放该运行时的所有节点并停止计费。

```powershell
Stop-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force 
```
### <a name="configure-vnet-settings-for-the-azure-ssis-ir-to-join"></a>为要加入的 Azure-SSIS IR 配置 VNet 设置
注册到 Azure Batch 资源提供程序：

```powershell
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    $BatchObjectId = (Get-AzureRmADServicePrincipal -ServicePrincipalName "MicrosoftAzureBatch").Id
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
        Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign VM contributor role to Microsoft.Batch
        New-AzureRmRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="configure-the-azure-ssis-ir"></a>配置 Azure-SSIS IR
运行 Set-AzureRmDataFactoryV2IntegrationRuntime 命令将 Azure-SSIS 集成运行时配置为加入 VNet： 

```powershell
Set-AzureRmDataFactoryV2IntegrationRuntime  -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Type Managed `
                                            -VnetId $VnetId `
                                            -Subnet $SubnetName
```

### <a name="start-the-azure-ssis-ir"></a>启动 Azure-SSIS IR
运行以下命令以启动 Azure-SSIS 集成运行时： 

```powershell
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

```
此命令将花费 **20 到 30 分钟**才能完成。

## <a name="next-steps"></a>后续步骤
有关 Azure-SSIS 运行时的详细信息，请参阅以下主题： 

- [Azure-SSIS 集成运行时](concepts-integration-runtime.md#azure-ssis-integration-runtime)。 此文提供有关集成运行时（包括 Azure-SSIS IR）的一般概念性信息。 
- [教程：将 SSIS 包部署到 Azure](tutorial-create-azure-ssis-runtime-portal.md)。 此文提供有关创建 Azure-SSIS IR，并使用 Azure SQL 数据库来承载 SSIS 目录的分步说明。 
- [如何创建 Azure-SSIS 集成运行时](create-azure-ssis-integration-runtime.md)。 此文延伸了教程的内容，提供有关使用 Azure SQL 托管实例（人预览版）以及将 IR 加入 VNet 的说明。 
- [监视 Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime)。 此文介绍如何检索有关 Azure-SSIS IR 的信息，以及返回的信息中的状态说明。 
- [管理 Azure-SSIS IR](manage-azure-ssis-integration-runtime.md)。 此文介绍如何停止、启动或删除 Azure-SSIS IR。 此外，介绍如何通过在 Azure-SSIS IR 中添加更多节点来扩展 IR。 
