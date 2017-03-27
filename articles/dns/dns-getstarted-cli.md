---
title: "通过 Azure CLI 2.0 开始使用 Azure DNS | Microsoft 文档"
description: "了解如何在 Azure DNS 中创建 DNS 区域和记录。 这是有关使用 Azure CLI 2.0 创建和管理你的第一个 DNS 区域和记录的分步指南。"
services: dns
documentationcenter: na
author: jtuliani
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: fb0aa0a6-d096-4d6a-b2f6-eda1c64f6182
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/10/2017
ms.author: jonatul
translationtype: Human Translation
ms.sourcegitcommit: 24d86e17a063164c31c312685c0742ec4a5c2f1b
ms.openlocfilehash: 981275da75adb11e8fff16f77e31d4ff86affe1f
ms.lasthandoff: 03/11/2017

---

# <a name="get-started-with-azure-dns-using-azure-cli-20"></a>通过 Azure CLI 2.0 开始使用 Azure DNS

> [!div class="op_single_selector"]
> * [Azure 门户](dns-getstarted-portal.md)
> * [PowerShell](dns-getstarted-powershell.md)
> * [Azure CLI 1.0](dns-getstarted-cli-nodejs.md)
> * [Azure CLI 2.0](dns-getstarted-cli.md)

本文将指导你完成使用跨平台的 Azure CLI 2.0（适用于 Windows、Mac 和 Linux）创建你的第一个 DNS 区域和记录的步骤。 也可以使用 Azure 门户或 Azure PowerShell 执行这些步骤。

DNS 区域用来托管某个特定域的 DNS 记录。 若要开始在 Azure DNS 中托管域，需要为该域名创建 DNS 区域。 随后将在此 DNS 区域内为每个 DNS 记录创建域。 最后，若要将 DNS 区域发布到 Internet，需要为域配置名称服务器。 以下描述了上述每一个步骤。

这些说明假定你已安装并登录到 Azure CLI 1.0。 若需帮助，请参阅[如何使用 Azure CLI 2.0 管理 DNS 区域](dns-operations-dnszones-cli.md)。


## <a name="create-a-dns-zone"></a>创建 DNS 区域

使用 `az network dns zone create` 命令创建 DNS 区域。 若要查看此命令的帮助，请键入 `az network dns zone create -h`。

以下示例在名为 *MyResourceGroup* 的资源组中创建名为 *contoso.com* 的 DNS 区域。 使用该示例创建 DNS 区域，将相应的值替换为你自己的值。

```azurecli
az network dns zone create -g MyResourceGroup -n contoso.com
```


## <a name="create-a-dns-record"></a>创建 DNS 记录

若要创建 DNS 记录，请使用 `az network dns record-set [record type] add-record` 命令。 若需帮助，例如需要 A 记录的帮助信息，请参阅 `azure network dns record-set A add-record -h`。

下面的示例在资源组“MyResourceGroup”中在 DNS 区域“contoso.com”中创建相对名称为“www”的一个记录集。 记录集的完全限定名称为“www.contoso.com”。 记录类型为“A”，IP 地址为“1.2.3.4”，并且使用了默认 TTL（3600 秒，1 小时）。

```azurecli
az network dns record-set A add-record -g MyResourceGroup -z contoso.com -n www -a 1.2.3.4
```

对于其他记录类型，对于具有一条以上记录的记录集，若要使用其他 TTL 值并修改现有记录，请参阅[使用 Azure CLI 2.0 管理 DNS 记录和记录集](dns-operations-recordsets-cli.md)。


## <a name="view-records"></a>查看记录

若要列出你的区域中的 DNS 记录，请使用：

```azurecli
az network dns record-set list -g MyResourceGroup -z contoso.com
```


## <a name="update-name-servers"></a>更新名称服务器

在你认为你的 DNS 区域和记录都已正确设置后，需要将你的域名配置为使用 Azure DNS 名称服务器。 这使得 Internet 上的其他用户可以发现你的 DNS 记录。

你的区域的名称服务器是通过 `az network dns zone show` 命令指定的。 若要查看名称服务器名称，请使用 JSON 输出，如以下示例中所示。

```azurecli
az network dns zone show -g MyResourceGroup -n contoso.com -o json

{
  "etag": "00000003-0000-0000-b40d-0996b97ed101",
  "id": "/subscriptions/a385a691-bd93-41b0-8084-8213ebc5bff7/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com",
  "location": "global",
  "maxNumberOfRecordSets": 5000,
  "name": "contoso.com",
  "nameServers": [
    "ns1-01.azure-dns.com.",
    "ns2-01.azure-dns.net.",
    "ns3-01.azure-dns.org.",
    "ns4-01.azure-dns.info."
  ],
  "numberOfRecordSets": 3,
  "resourceGroup": "myresourcegroup",
  "tags": {},
  "type": "Microsoft.Network/dnszones"
}
```

这些名称服务器应当配置有域名注册机构（你向其购买域名的机构）。 你的域名注册机构将提供选项来为域设置名称服务器。 有关详细信息，请参阅[将域委托给 Azure DNS](dns-domain-delegation.md)。


## <a name="next-steps"></a>后续步骤

若要了解 Azure DNS 的详细信息，请参阅 [Azure DNS 概述](dns-overview.md)。

若要了解有关管理 Azure DNS 中的 DNS 区域的详细信息，请参阅[使用 Azure CLI 2.0 管理 Azure DNS 中的 DNS 区域](dns-operations-dnszones-cli.md)。

若要了解有关管理 Azure DNS 中的 DNS 记录的详细信息，请参阅[使用 Azure CLI 2.0 管理 Azure DNS 中的 DNS 记录和记录集](dns-operations-recordsets-cli.md)。
