---
title: "CLI 示例 - 创建 Azure SQL 数据库 | Microsoft Docs"
description: "使用此 Azure CLI 示例脚本创建 SQL 数据库。"
services: sql-database
documentationcenter: sql-database
author: janeng
manager: jstrauss
editor: carlrab
tags: azure-service-management
ms.assetid: 
ms.service: sql-database
ms.custom: DBs & servers, mvc
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 10/11/2017
ms.author: janeng
ms.openlocfilehash: 404d43a6f2fa38276b9517e9542f1e50a4b1980b
ms.sourcegitcommit: 4ac89872f4c86c612a71eb7ec30b755e7df89722
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/07/2017
---
# <a name="use-cli-to-create-a-single-azure-sql-database-and-configure-a-firewall-rule"></a>使用 CLI 创建单一 Azure SQL 数据库并配置防火墙规则

以下 Azure CLI 脚本示例创建 Azure SQL 数据库，并配置服务器级防火墙规则。 成功运行该脚本后，可以通过所有 Azure 服务和配置的 IP 地址访问 SQL 数据库。 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本主题要求运行 Azure CLI 2.0 版或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 

## <a name="sample-script"></a>示例脚本

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/create-and-configure-database/create-and-configure-database.sh?highlight=9-10 "Create SQL Database")]

## <a name="clean-up-deployment"></a>清理部署

运行脚本示例后，可以使用以下命令删除资源组以及与其关联的所有资源。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | 创建用于存储所有资源的资源组。 |
| [az sql server create](/cli/azure/sql/server#az_sql_server_create) | 创建用于托管 SQL 数据库的逻辑服务器。 |
| [az sql server firewall create](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create) | 创建一个防火墙规则，以允许从输入的 IP 地址范围访问服务器上的所有 SQL 数据库。 |
| [az sql db create](/cli/azure/sql/db#az_sql_db_create) | 在逻辑服务器中创建 SQL 数据库。 |
| [az group delete](/cli/azure/resource#delete) | 删除资源组，包括所有嵌套的资源。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.microsoft.com/cli/azure/overview)。

其他 SQL 数据库 CLI 脚本示例可以在 [Azure SQL 数据库文档](../sql-database-cli-samples.md)中找到。

