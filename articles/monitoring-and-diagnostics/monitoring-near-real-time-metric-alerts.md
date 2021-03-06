---
title: "Azure Monitor 中的准实时指标警报 | Microsoft 文档"
description: "通过准实时指标警报，你可以 1 分钟一次的频率监视 Azure 资源指标。"
author: snehithm
manager: kmadnani1
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2017
ms.author: snmuvva
ms.custom: 
ms.openlocfilehash: d3e88a98e0ba93a630d131c25ca4dd5cb16f1b1a
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/19/2018
---
# <a name="near-real-time-metric-alerts-preview"></a>准实时指标警报（预览版）
Azure Monitor 现在支持一种称为准实时指标警报（预览版）的新型指标警报。 此功能目前处于公开预览状态。
这些警报与常规指标警报有一些不同

- 改进了延迟 - 准实时指标警报可以在 1 分钟内监控指标值的变化。
- 更好地控制指标条件 - 准实时指标警报使用户能够定义更丰富的警报规则。 警报现在支持监视指标的最大值、最小值、平均值和总值。
- 综合监控多个指标 - 准实时指标警报可以使用单个规则监控多个指标（目前为两个）。 如果两个指标在指定时间段内违反其各自的阈值，则会触发警报。
- 模块化通知系统 - 准实时指标警报使用[操作组](monitoring-action-groups.md)。 此功能允许用户以模块化方式创建操作，并将其重复用于诸多警报规则。

> [!NOTE]
> 准实时指标警报功能目前处于公开预览状态。 功能和用户体验可能会发生变化。
>

## <a name="what-resources-can-i-create-near-real-time-metric-alerts-for"></a>可以为哪些资源创建准实时指标警报？
准实时指标警报支持的完整资源类型列表：

* Microsoft.ApiManagement/service
* Microsoft.Automation/automationAccounts
* Microsoft.Batch/batchAccounts
* Microsoft.Cache/Redis
* Microsoft.Compute/virtualMachines
* Microsoft.Compute/virtualMachineScaleSets
* Microsoft.DataFactory/factories
* Microsoft.DBforMySQL/servers
* Microsoft.DBforPostgreSQL/servers
* Microsoft.EventHub/namespaces
* Microsoft.Logic/workflows
* Microsoft.Network/applicationGateways
* Microsoft.Network/publicipaddresses
* Microsoft.Search/searchServices
* Microsoft.ServiceBus/namespaces
* Microsoft.Storage/storageAccounts
* Microsoft.Storage/storageAccounts/services
* Microsoft.StreamAnalytics/streamingjobs
* Microsoft.CognitiveServices/accounts

## <a name="near-real-time-metric-alerts-on-metrics-with-dimensions"></a>基于多维指标的准实时指标警报
准实时指标警报支持根据多维指标发出警报。 通过选择维度可将指标筛选到适当级别。 以下资源类型支持基于多维指标的准实时指标警报

* Microsoft.ApiManagement/service
* Microsoft.Storage/storageAccounts（仅美国区域中的存储帐户支持）
* Microsoft.Storage/storageAccounts/services（仅美国区域中的存储帐户支持）


## <a name="create-a-near-real-time-metric-alert"></a>创建准实时指标警报
目前，准实时指标警报只能通过 Azure 门户创建。 即将推出通过 PowerShell、命令行接口 (CLI) 和 Azure Monitor REST API 配置准实时指标警报的支持功能。

准实时指标警报的创建警报体验已移动到新的“警报(预览)”体验。 即使当前“警报”页显示了“添加准实时指标警报”，也会将你重定向到新体验。

按照[此处](monitor-alerts-unified-usage.md#create-an-alert-rule-with-the-azure-portal)所述的步骤可以创建准实时指标警报。

## <a name="managing-near-real-time-metric-alerts"></a>管理准实时指标警报
创建了**准实时指标警报**后，可以按照[此处](monitor-alerts-unified-usage.md#managing-your-alerts-in-azure-portal)所述的步骤进行管理。

## <a name="payload-schema"></a>负载架构

POST 操作对于所有准实时指标警报包含以下 JSON 有效负载和架构。

```json
{
    "WebhookName": "Alert1510875839452",
    "RequestBody": {
        "status": "Activated",
        "context": {
            "condition": {
                "metricName": "Percentage CPU",
                "metricUnit": "Percent",
                "metricValue": "17.7654545454545",
                "threshold": "1",
                "windowSize": "10",
                "timeAggregation": "Average",
                "operator": "GreaterThan"
            },
            "resourceName": "ContosoVM1",
            "resourceType": "microsoft.compute/virtualmachines",
            "resourceRegion": "westus",
            "portalLink": "https://portal.azure.com/#resource/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/automationtest/providers/Microsoft.Compute/virtualMachines/ContosoVM1",
            "timestamp": "2017-11-16T23:54:03.9517451Z",
            "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoVM/providers/microsoft.insights/alertrules/VMMetricAlert1",
            "name": "VMMetricAlert1",
            "description": "A metric alert for the VM Win2012R2",
            "conditionType": "Metric",
            "subscriptionId": "00000000-0000-0000-0000-000000000000",
            "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoVM/providers/Microsoft.Compute/virtualMachines/ContosoVM1",
            "resourceGroupName": "ContosoVM"
        },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
    },
    "RequestHeader": {
        "Connection": "Keep-Alive",
        "Host": "s1events.azure-automation.net",
        "User-Agent": "azure-insights/0.9",
        "x-ms-request-id": "00000000-0000-0000-0000-000000000000"
    }
}
```

## <a name="next-steps"></a>后续步骤

* [了解有关新的“警报(预览)”体验的详细信息](monitoring-overview-unified-alerts.md)
* [了解 Azure 警报 (预览) 中的日志警报](monitor-alerts-unified-log.md)
* [了解 Azure 中的警报](monitoring-overview-alerts.md)