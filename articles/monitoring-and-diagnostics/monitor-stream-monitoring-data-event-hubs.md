---
title: "将 Azure 监视数据流式传输到事件中心 | Microsoft Docs"
description: "了解如何将所有 Azure 监视数据流式传输到事件中心，以将数据获取到合作伙伴 SIEM 或分析工具。"
author: johnkemnetz
manager: robb
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/11/2018
ms.author: johnkem
ms.openlocfilehash: b2813035b4665a36b475e791965d395b84ddb3f1
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/12/2018
---
# <a name="stream-azure-monitoring-data-to-an-event-hub-for-consumption-by-an-external-tool"></a>将 Azure 监视数据流式传输到事件中心以便外部工具使用

Azure Monitor 提供了获取 Azure 环境中所有监视数据访问权限的单一管道，让你能够轻松设置合作伙伴 SIEM 和监视工具以使用该数据。 本文将演练如何将 Azure 环境中的不同数据层设置为发送到单个事件中心命名空间或事件中心，以便由外部工具收集。

## <a name="what-data-can-i-send-into-an-event-hub"></a>可将哪些数据发送到事件中心？ 

在 Azure 环境中，有多“层”监视数据，访问每层数据的方法略有不同。 通常情况下，这些层可描述为：

- **应用程序监视数据：**有关已编写并在 Azure 上运行的代码的性能和功能的数据。 应用程序监视数据的示例包括性能跟踪、应用程序日志及用户遥测。 通常以下列的一种方式收集应用程序监视数据：
  - 用 [Application Insights SDK](../application-insights/app-insights-overview.md) 等 SDK 检测代码。
  - 运行侦听应用程序运行于的计算机上的新应用程序日志的监视代理，如 [Windows Azure 诊断代理](./azure-diagnostics.md)或 [Linux Azure 诊断代理](../virtual-machines/linux/diagnostic-extension.md)。
- **来宾 OS 监视数据：**有关应用程序运行于的操作系统的数据。 来宾 OS 监视数据的示例有 Linux syslog 或 Windows 系统日志。 若要收集此类型的数据，需安装代理，如[ Windows Azure 诊断代理](./azure-diagnostics.md)或 [Linux Azure 诊断代理](../virtual-machines/linux/diagnostic-extension.md)。
- **Azure 资源监视数据：**有关 Azure 资源操作的数据。 对于某些 Azure 资源类型（如虚拟机），该 Azure 服务中会监视来宾 OS 和应用程序。 对于其他 Azure 资源（如网络安全组），资源监视数据是可用数据的最高层（因为没有 来宾 OS 或应用程序在这些资源中运行）。 可以使用[资源诊断设置](./monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings)收集这些数据。
- **Azure 平台监视数据：**有关 Azure 订阅或租户操作和管理的数据，以及有关 Azure 本身运行状况和操作的数据。 [活动日志](./monitoring-overview-activity-logs.md)，包括服务运行状况数据，Active Directory 审核是平台监视数据的示例。 也可使用诊断设置收集这些数据。

可将任何层的数据发送到事件中心，以便将其拉取到合作伙伴工具。 以下各节描述了如何将每层数据配置为流式传输到事件中心。 这些步骤假定你拥有处于要监视的层的资产。

开始之前，需[创建事件中心命名空间和事件中心](../event-hubs/event-hubs-create.md)。 此命名空间和事件中心是所有监视数据的目标。

## <a name="how-do-i-set-up-azure-platform-monitoring-data-to-be-streamed-to-an-event-hub"></a>如何将 Azure 平台监视数据设置为流式传输到事件中心？

Azure 平台监视数据来自两个主要源：
1. [Azure 活动日志](./monitoring-overview-activity-logs.md)，其中包含来自资源管理器的创建、更新和删除操作；[Azure 服务运行状况](../service-health/service-health-overview.md)中可能影响订阅中资源的更改；[资源运行状况](../service-health/resource-health-overview.md)状态转换；以及若干其他类型的订阅级别事件。 [本文详细介绍了 Azure 活动日志中显示的所有事件类别](./monitoring-activity-log-schema.md)。
2. [Azure Active Directory 报告](../active-directory/active-directory-reporting-azure-portal.md)，其中包含特定租户中的登录活动历史记录和更改审核日志。 尚不可将 Azure Active Directory 数据流式传输到事件中心。

### <a name="stream-azure-activity-log-data-into-an-event-hub"></a>将 Azure 活动日志数据流式传输到事件中心

若要将数据从 Azure 活动日志发送到事件中心命名空间，请在订阅上设置日志配置文件。 [按照本指南](./monitoring-stream-activity-logs-event-hubs.md)在订阅上设置日志配置文件。 对要监视每个订阅执行一次此操作。

> [!TIP]
> 日志配置当前仅允许选择一个事件中心命名空间，并将在其中创建名为“insights-operational-logs”的事件日志。 尚不可在日志配置文件中指定自己的事件中心名称。

## <a name="how-do-i-set-up-azure-resource-monitoring-data-to-be-streamed-to-an-event-hub"></a>如何将 Azure 资源监视数据设置为流式传输到事件中心？

Azure 资源将发出两种类型的监视数据：
1. [资源诊断日志](./monitoring-overview-of-diagnostic-logs.md)
2. [指标](monitoring-overview-metrics.md)

使用资源诊断设置将两种类型的数据发送到事件中心。 [按照本指南](./monitoring-stream-diagnostic-logs-to-event-hubs.md)在特定资源上设置资源诊断设置。 在要从其收集日志的每个资源上设置资源诊断设置。

> [!TIP]
> 可使用 Azure 策略，[在策略规则中使用 DeployIfNotExists 效果](../azure-policy/policy-definition.md#policy-rule)，确保特定范围内的每个资源始终设置了诊断设置。 DeployIfNotExists 现仅支持内置策略。

## <a name="how-do-i-set-up-guest-os-monitoring-data-to-be-streamed-to-an-event-hub"></a>如何将来宾 OS 监视数据设置为流式传输到事件中心？

需要安装代理以将来宾 OS 监视数据发送到事件中心。 对于 Windows 或 Linux，请在配置文件中指定要发送到事件中心的数据，以及应将数据发送到的事件中心，并将该配置文件传递给在 VM 上运行的代理。

### <a name="stream-linux-data-to-an-event-hub"></a>将 Linux 数据流式传输到事件中心

[Linux Azure 诊断代理](../virtual-machines/linux/diagnostic-extension.md)用于将来自 Linux 计算机的监视数据发送到事件中心。 在 LAD 配置文件保护的设置 JSON 中添加事件中心作为接收器，即可完成此操作。 [参阅此文章，详细了解如何向 Linux Azure 诊断代理添加事件中心接收器](../virtual-machines/linux/diagnostic-extension.md#protected-settings)。

> [!NOTE]
> 不能在门户中将来宾 OS 监视数据设置为流式传输到事件中心。 相反，必须手动编辑配置文件。

### <a name="stream-windows-data-to-an-event-hub"></a>将 Windows 数据流式传输到事件中心

[Windows Azure 诊断代理](./azure-diagnostics.md)用于将来自 Windows 计算机的监视数据发送到事件中心。 在 WAD 配置文件的 privateConfig 部分添加事件中心作为接收器，即可完成此操作。 [参阅此文章，详细了解如何向 Windows Azure 诊断代理添加事件中心接收器](./azure-diagnostics-streaming-event-hubs.md)。

> [!NOTE]
> 不能在门户中将来宾 OS 监视数据设置为流式传输到事件中心。 相反，必须手动编辑配置文件。

## <a name="how-do-i-set-up-application-monitoring-data-to-be-streamed-to-event-hub"></a>如何将应用程序监视数据设置为流式传输到事件中心？

应用程序监视数据要求代码经过 SDK 检测，因此没有将应用程序监视数据路由到 Azure 中事件中心的通用解决方案。 但是，[Azure Application Insights](../application-insights/app-insights-overview.md) 是一项可用于收集 Azure 应用程序级数据的服务。 如果使用 Application Insights，可通过执行以下操作，将监视数据流式传输到事件中心：

1. 将 Application Insights 数据[设置为连续导出](../application-insights/app-insights-export-telemetry.md)到存储帐户。

2. 设置计时器触发逻辑应用，[从 blob 存储拉取数据](../connectors/connectors-create-api-azureblobstorage.md#use-an-action)并[将其作为消息推送到事件中心](../connectors/connectors-create-api-azure-event-hubs.md#send-events-to-your-event-hub-from-your-logic-app)。

## <a name="what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub"></a>可对发送到事件中心的监视数据执行什么操作？

通过 Azure Monitor 将监视数据路由到事件中心，可与合作伙伴 SIEM 和监视工具轻松集成。 大多数工具需要事件中心连接字符串和对 Azure 订阅的某些权限，才能从事件中心读取数据。 下面是与 Azure Monitor 集成的工具的不完整列表：

* **IBM QRadar** -Microsoft Azure DSM 和 Microsoft Azure 事件中心协议均可从 [IBM 支持网站](http://www.ibm.com/support)下载。 可以[在此处了解 Azure 集成](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/c_dsm_guide_microsoft_azure_overview.html?cp=SS42VS_7.3.0)。
* **Splunk** - [适用于 Splunk 的 Azure Monitor 加载项](https://splunkbase.splunk.com/app/3534/) 可在 Splunkbase 中找到，它是一个开源项目。 [文档见此处](https://github.com/Microsoft/AzureMonitorAddonForSplunk/wiki/Azure-Monitor-Addon-For-Splunk)。
* **SumoLogic** - 将 SumoLogic 设置为使用来自事件中心的数据的说明[见此处](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure-Audit/02Collect-Logs-for-Azure-Audit-from-Event-Hub)

## <a name="next-steps"></a>后续步骤
* [将活动日志存档到存储帐户](monitoring-archive-activity-log.md)
* [阅读 Azure 活动日志概述](monitoring-overview-activity-logs.md)
* [根据活动日志事件设置警报](insights-auditlog-to-webhook-email.md)

