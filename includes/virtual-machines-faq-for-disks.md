# <a name="frequently-asked-questions-about-azure-iaas-vm-disks-and-managed-and-unmanaged-premium-disks"></a>有关 Azure IaaS VM 磁盘以及托管和非托管高级磁盘的常见问题解答

本文将对有关 Azure 托管磁盘和 Azure 高级存储的一些常见问题进行解答。

## <a name="managed-disks"></a>托管磁盘

**什么是 Azure 托管磁盘？**

托管磁盘是一种通过处理存储帐户管理来简化 Azure IaaS VM 的磁盘管理的功能。 有关详细信息，请参阅[托管磁盘概述](../articles/virtual-machines/windows/managed-disks-overview.md)。

**如果从现有的 VHD（80 GB）创建标准托管磁盘，需要多少费用？**

从 80 GB VHD 创建的标准托管磁盘被视为下一个可用的标准磁盘大小（S10 磁盘）。 我们按 S10 磁盘定价收费。 有关详细信息，请参阅[定价页](https://azure.microsoft.com/pricing/details/storage)。

**标准托管磁盘是否存在任何事务成本？**

是的。 我们针对每个事务进行收费。 有关详细信息，请参阅[定价页](https://azure.microsoft.com/pricing/details/storage)。

**对于标准托管磁盘，是对磁盘上的数据实际大小收费还是对磁盘的预配容量收费？**

我们根据磁盘的预配容量收费。 有关详细信息，请参阅[定价页](https://azure.microsoft.com/pricing/details/storage)。

**高级托管磁盘与非托管磁盘的定价有何不同？**

高级托管磁盘的定价与高级非托管磁盘的定价相同。

**是否可以更改托管磁盘的存储帐户类型（标准或高级）？**

是的。 可以使用 Azure 门户、PowerShell 或 Azure CLI 更改托管磁盘的存储帐户类型。

**是否可将托管磁盘复制或导出到专用存储帐户？**

是的。 可使用 Azure 门户、PowerShell 或 Azure CLI 导出托管磁盘。

**是否可以使用 Azure 存储帐户中的 VHD 文件以不同的订阅创建托管磁盘？**

不会。

**是否可以使用 Azure 存储帐户中的 VHD 文件在不同的区域中创建托管磁盘？**

不会。

**客户使用托管磁盘是否存在任何规模限制？**

托管磁盘取消了与存储帐户相关的限制。 但是，每个订阅的托管磁盘数默认限制为 2,000 个。 你可以致电支持人员增加此限制数。

**是否可以生成托管磁盘的增量快照？**

不会。 当前的快照功能可提供托管磁盘的完整副本。 但我们计划在将来支持增量快照。

**可用性集中的 VM 是否可以同时包含托管和非托管磁盘？**

不会。 可用性集中的 VM 必须全部使用托管磁盘或全部使用非托管磁盘。 创建可用性集时，可以选择要使用的磁盘类型。

**托管磁盘是否是 Azure 门户中的默认选项？**

是的。 

**是否可以创建一个空托管磁盘？**

是的。 可创建空磁盘。 可独立于 VM 创建托管磁盘，例如，不需要将磁盘附加到 VM。

**什么是使用托管磁盘的可用性集的支持容错域计数？**

使用托管磁盘的可用性集的支持容错域计数为 2 或 3，具体取决于它所在的区域。

**如何设置用于诊断的标准存储帐户？**

设置 VM 诊断的专用存储帐户。 我们计划将来也将诊断切换到托管磁盘。

**托管磁盘支持哪类基于角色的访问控制？**

托管磁盘支持三个密钥默认角色：

* 所有者：可管理所有内容，包括访问权限
* 参与者：可管理除访问权限以外的所有内容
* 读者：可查看所有内容，但不能进行更改

**是否可将托管磁盘复制或导出到专用存储帐户？**

可以为托管磁盘获取只读共享访问签名 URI，使用它将内容复制到专用存储帐户或本地存储。

**是否可以创建托管磁盘副本？**

客户可以生成托管磁盘的快照，并使用快照创建另一个托管磁盘。

**是否仍支持非托管磁盘？**

是的。 我们支持非托管磁盘和托管磁盘。 我们建议你对新的工作负荷使用托管磁盘，并将当前的工作负荷迁移到托管磁盘。


**如果创建 128 GB 磁盘，然后将大小增加到 130 GB，是否会针对下一磁盘大小 (512 GB) 进行收费？**

是的。

**是否可以创建本地冗余存储、异地冗余存储和区域冗余存储托管磁盘？**

Azure 托管磁盘当前仅支持本地冗余存储托管磁盘。

**是否可以收缩或缩小托管磁盘？**

不会。 目前，不支持此功能。 

**是否可以在磁盘上中断租用？**

不会。 目前不支持此功能，因为租用的作用是防止磁盘在使用时被意外删除。

**当使用专用（未使用系统准备工具创建或未通用化）操作系统磁盘预配 VM 时，是否可以更改计算机名称属性？**

不会。 无法更新计算机名称属性。 新 VM 从创建操作系统磁盘时所用的父 VM 继承该属性。 

**在哪里可找到用于使用托管磁盘创建 VM 的示例 Azure 资源管理器模板？**
* [使用托管磁盘的模板列表](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md)
* https://github.com/chagarw/MDPP

## <a name="migrate-to-managed-disks"></a>迁移到托管磁盘 

**迁移到托管磁盘之前/之后，需要在现有的 Azure 备份服务配置中进行哪些更改？**

不需要进行任何更改。 

**在迁移之前通过 Azure 备份服务创建的 VM 备份是否可继续工作？**

是的，备份可以顺利工作。

**迁移到托管磁盘之前/之后，需要在现有的 Azure 磁盘加密配置中进行哪些更改？**

不需要进行任何更改。 

**是否支持将现有 VM 规模集 (VMSS) 从非托管磁盘自动迁移到托管磁盘？**

不会。 可以使用包含非托管磁盘的旧 VMSS 中的映像创建包含托管磁盘的新 VMSS。 

**是否可以通过迁移到托管磁盘之前创建的页 Blob 快照创建托管磁盘？**

不会。 可将页 Blob 快照导出为页 Blob，然后从导出的页 Blob 创建托管磁盘。 

**是否可将 Azure Site Recovery 保护的本地计算机故障转移到包含托管磁盘的 VM？**

是的，可以选择故障转移到包含托管磁盘的 VM。

**迁移是否影响 Azure Site Recovery (ASR) 通过 Azure 到 Azure 复制保护的 Azure VM？**

是的。 包含托管磁盘的 VM 不支持 ASR Azure 到 Azure 保护。 2018 日历年第一季度末将会支持这种保护。 

**是否可以迁移位于存储帐户中现在或以前已加密的 VM 的非托管磁盘迁移到托管磁盘？**

是

## <a name="managed-disks-and-storage-service-encryption"></a>托管磁盘和存储服务加密 

**创建托管磁盘时，是否会默认启用 Azure 存储服务加密？**

是的。

**加密密钥由谁管理？**

Microsoft 管理加密密钥。

**是否可以为托管磁盘禁用存储服务加密？**

不会。

**存储服务加密是否仅适用于特定区域？**

不会。 它适用于托管磁盘可用的所有区域。 托管磁盘适用于所有公共区域和德国。

**如何确定我的托管磁盘是否已加密？**

你可以从 Azure 门户、Azure CLI 和 PowerShell 确定托管磁盘的创建时间。 如果时间是在 2017 年 6 月 9 日之后，则磁盘已加密。 

**如何对 2017 年 6 月 10 日之前创建的现有磁盘加密？**

自 2017 年 6 月 10 日起，写入到现有托管磁盘的新数据会自动加密。 我们还打算对现有数据进行加密，且在后台以异步方式加密。 如果必须立即对现有数据进行加密，请创建磁盘的副本。 将对新磁盘进行加密。

* [使用 Azure CLI 复制托管磁盘](../articles/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json)
* [使用 PowerShell 复制托管磁盘](../articles/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json)

**是否已加密托管快照和映像？**

是的。 2017 年 6 月 9 日之后创建的所有托管快照和映像均会自动加密。 

**是否可以将 VM 的位于存储帐户且现在或以前已加密的非托管磁盘转换为托管磁盘？**

是

**是否同时会加密从托管磁盘或快照导出的 VHD？**

不会。 但如果将 VHD 从加密托管磁盘或快照导出到加密存储帐户，则会对其进行加密。 

## <a name="premium-disks-managed-and-unmanaged"></a>高级磁盘：托管和非托管

**如果 VM 使用支持高级存储的大小系列（比如 DSv2），是否可以同时附加高级和标准数据磁盘？** 

是的。

**是否可以同时将高级和标准数据磁盘附加到不支持高级存储的大小系列，例如 D、Dv2、G 或 F 系列？**

不会。 只可以将标准数据磁盘附加到不使用支持高级存储的大小系列的 VM。

**如果从现有的 VHD (80 GB) 创建高级数据磁盘，需要多少费用？**

从 80 GB VHD 创建的高级数据磁盘被视为下一个可用的高级磁盘大小（P10 磁盘）。 我们按 P10 磁盘定价收费。

**使用高级存储时是否存在事务成本？**

每个磁盘大小都有固定成本，其根据 IOPS 和吞吐量的特定限制进行预配。 其他成本包括出站带宽和快照容量（如果适用）。 有关详细信息，请参阅[定价页](https://azure.microsoft.com/pricing/details/storage)。

**可从磁盘缓存获取的 IOPS 和吞吐量限制是多少？**

DS 系列的缓存和本地 SSD 合并限制是每个核心 4,000 IOPS，以及每个核心每秒 33 MB。 GS 系列提供每个核心 5,000 IOPS，以及每个核心每秒 50 MB。

**托管磁盘 VM 是否支持本地 SSD？**

本地 SSD 是托管磁盘 VM 随附的临时存储。 临时存储不需要额外的成本。 建议不要使用此本地 SSD 来存储应用程序数据，因为这些数据不会永久保存在 Azure Blob 存储中。

**在高级磁盘上使用 TRIM 是否有任何影响？**

在高级或标准磁盘的 Azure 磁盘上使用 TRIM 没有负面影响。

## <a name="new-disk-sizes-managed-and-unmanaged"></a>新磁盘大小：托管和非托管

**操作系统和数据磁盘支持的最大磁盘大小是多少？**

Azure 支持的操作系统磁盘的分区类型是主启动记录 (MBR)。 MBR 格式支持的磁盘最大大小为 2 TB。 Azure 支持的操作系统磁盘的最大大小为 2 TB。 Azure 支持的数据磁盘最大大小为 4 TB。 

**支持的最大页 blob 大小是多少？**

Azure 支持的最大页 blob 大小是 8 TB (8,191 GB)。 我们不支持将大于 4 TB (4,095 GB) 的页 blob 作为数据或操作系统磁盘附加到 VM。

**是否需要使用新版本的 Azure 工具来创建、附加、上传大于 1 TB 的磁盘并重设其大小？**

无需升级现有 Azure 工具即可创建、附加大于 1 TB 的磁盘或重设其大小。 若要直接从本地将 VHD 文件作为页 blob 或非托管磁盘上传到到 Azure，需要使用最新工具集：

|Azure 工具      | 支持的版本                                |
|-----------------|---------------------------------------------------|
|Azure PowerShell | 版本号 4.1.0：2017 年 6 月版本或更高版本|
|Azure CLI v1     | 版本号 0.10.13：2017 年 5 月版本或更高版本|
|AzCopy           | 版本号 6.1.0：2017 年 6 月版本或更高版本|

即将提供 Azure CLI v2 和 Azure 存储资源管理器支持。 

**非托管磁盘或页 blob 是否支持 P4 和 P6 磁盘大小？**

不会。 仅托管磁盘支持 P4 (32 GB) 和 P6 (64 GB) 磁盘大小。 即将提供非托管磁盘和页 blob 支持。

**如果在支持较小磁盘（约 2017 年 6 月 15 日）之前创建了小于 64 GB 的高级托管磁盘，将如何计费？**

根据 P10 定价层继续对小于 64 GB 的现有高级小磁盘计费。 

**如何将小于 64 GB 的高级小磁盘的磁盘层级从 P10 切换到 P4 或 P6？**

你可以拍摄小磁盘的快照，然后创建磁盘以自动根据预配大小将定价层切换到 P4 或 P6。 


## <a name="what-if-my-question-isnt-answered-here"></a>如果未在此处找到相关问题怎么办？

如果未在此处找到相关问题，请联系我们获取帮助。 你可以在本文末尾的评论中发布问题。 若要与 Azure 存储团队和其他社区成员就本文进行沟通，请使用 MSDN [Azure 存储论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata)。

若要提出功能请求，请将请求和想法提交到 [Azure 存储反馈论坛](https://feedback.azure.com/forums/217298-storage)。
