<properties
   pageTitle="使用 Azure 数据工厂加载数据 | Azure"
   description="了解如何使用 Azure 数据工厂加载数据"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""
   tags="azure-sql-data-warehouse"/>
<tags
   ms.service="sql-data-warehouse"
   ms.date="03/03/2016"
   wacn.date=""/>

# 使用 Azure 数据工厂加载数据

> [AZURE.SELECTOR]
- [数据工厂](sql-data-warehouse-get-started-load-with-azure-data-factory.md)
- [PolyBase](sql-data-warehouse-get-started-load-with-polybase.md)
- [BCP](sql-data-warehouse-load-with-bcp.md)

 此教程说明如何在 Azure 数据工厂中创建管道以将数据从 Azure 存储 Blob 移动到 SQL 数据仓库。使用以下步骤可以：

+ 在 Azure 存储 Blob 中设置示例数据。
+ 将资源连接到 Azure 数据工厂。
+ 创建管道用于将数据从存储 Blob 移到 SQL 数据仓库。



## 开始之前

若要熟悉 Azure 数据工厂，请参阅 [Azure 数据工厂简介](../data-factory/data-factory-introduction.md)。

### 创建或标识资源

在开始此教程之前，你需要拥有以下资源。

   + **Azure 存储 Blob**：此教程使用 Azure 存储 Blob 作为 Azure 数据工厂管道的数据源，因此，你需要拥有一个可用于存储示例数据的 Azure 存储 Blob。如果还没有，请学习如何[创建一个存储帐户](/documentation/articles/storage-create-storage-account/#create-a-storage-accoun/)。

   + **SQL 数据仓库**：此教程将 Azure 存储 Blob 的数据移动到 SQL 数据仓库，因此需要一个加载有 AdventureWorksDW 示例数据的联机数据仓库。如果还没有数据仓库，请学习如何[预配一个](/documentation/articles/sql-data-warehouse-get-started-provision)。如果拥有数据仓库但未用示例数据进行预配，你可以[手动加载](/documentation/articles/sql-data-warehouse-get-started-manually-load-samples)。

   + **Azure 数据工厂**：Azure 数据工厂将完成实际加载，因此你需要有一个可用于构建数据移动管道的 Azure 数据工厂。如果还没有，请在 [Azure 数据工厂入门（数据工厂编辑器）](/documentation/articles/data-factory-build-your-first-pipeline-using-editor)的步骤 1 中学习如何创建一个 Azure 数据工厂。

   + **AZCopy**：你需要 AZCopy 以便将示例数据从你的本地客户端复制到你的 Azure 存储 Blob。有关安装说明，请参阅 [AZCopy 文档](/documentation/articles/storage-use-azcopy)。

## 步骤 1：将示例数据复制到 Azure 存储 Blob

一旦所有部分准备就绪，你就可以将示例数据复制到 Azure 存储 Blob。

1. [下载示例数据](https://migrhoststorage.blob.core.windows.net/adfsample/FactInternetSales.csv)。此数据将添加另三年的销售数据到你的 AdventureWorksDW 示例数据。

2. 使用此 AZCopy 命令将三年的数据复制到 Azure 存储 Blob。

````
        AzCopy /Source:<Sample Data Location>  /Dest:https://<storage account>.blob.core.chinacloudapi.cn/<container name> /DestKey:<storage key> /Pattern:FactInternetSales.csv
````


## 步骤 2：将资源连接到 Azure 数据工厂

现在数据已就位，我们可以创建 Azure 数据工厂管道以将数据从 Azure Blob 存储移到 SQL 数据仓库。

若要开始使用，请打开 [Azure 门户](https://manage.windowsazure.cn/)并从左侧菜单中选择你的数据工厂。

### 步骤 2.1：创建链接的服务

将你的 Azure 存储帐户和 SQL 数据仓库链接到数据工厂。

1. 首先，通过单击数据工厂的“链接的服务”部分开始注册过程，然后单击“新建数据存储”。 选择要在其下注册 Azure 存储空间的名称，选择 Azure 存储空间作为类型，然后输入你的帐户名和帐户密钥。

2. 若要注册 SQL 数据仓库，请导航到“创作和部署”部分，然后依次选择“新建数据存储”和“Azure SQL 数据仓库”。在此模板中进行复制和粘贴，然后填写你的特定信息。

    ````
    {
        "name": "<Linked Service Name>",
	    "properties": {
	        "description": "",
		    "type": "AzureSqlDW",
		    "typeProperties": {
		            "connectionString": "Data Source=tcp:<server name>.database.chinacloudapi.cn,1433;Initial Catalog=<server name>;Integrated Security=False;User ID=<user>@<servername>;Password=<password>;Connect Timeout=30;Encrypt=True"
	         }
        }
    }
    ````

### 步骤 2.2：定义数据集

创建链接的服务后，我们需要定义数据集，即，定义要从你的存储转移到数据仓库的数据的结构。你可以阅读有关创建过程的信息

1. 若要开始此过程，请导航到数据工厂的“创作和部署”部分。

2. 依次单击“新建数据集”和“Azure Blob 存储”，以将你的存储链接到你的数据工厂。可以使用以下脚本，在 Azure Blob 存储中定义数据：

    ````
	{
	    "name": "<Dataset Name>",
		"properties": {
		    "type": "AzureBlob",
			"linkedServiceName": "<linked storage name>",
			"typeProperties": {
			    "folderPath": "<containter name>",
				"fileName": "FactInternetSales.csv",
				"format": {
				"type": "TextFormat",
				"columnDelimiter": ",",
				"rowDelimiter": "\n"
                }
            },
		    "external": true,
		    "availability": {
			    "frequency": "Hour",
			    "interval": 1
		    },
		    "policy": {
		        "externalData": {
			        "retryInterval": "00:01:00",
			        "retryTimeout": "00:10:00",
			        "maximumRetry": 3
		        }
            }
		}
	}
    ````


3. 现在，还要为 SQL 数据仓库定义我们的数据集。我们以相同的方式开始，即依次单击“新建数据集”和“Azure SQL 数据仓库”。

    ````
    {
	    "name": "DWDataset",
		"properties": {
		    "type": "AzureSqlDWTable",
		    "linkedServiceName": "AzureSqlDWLinkedService",
		    "typeProperties": {
			    "tableName": "FactInternetSales"
			},
		    "availability": {
		        "frequency": "Hour",
			    "interval": 1
	        }
        }
    }
    ````

## 步骤 3：创建并运行管道

最后，我们将在 Azure 数据工厂中设置并运行管道。此操作将完成实际的数据移动。可以在[此处](/documentation/articles/data-factory-azure-sql-data-warehouse-connector)找到可以使用 SQL 数据仓库和 Azure 数据工厂完成的操作的完整视图。

在“创作和部署”部分中，依次单击“更多命令”和“新建管道”。创建管道后，可以使用以下代码将数据传送到数据仓库：

````
{
    "name": "<Pipeline Name>",
    "properties": {
        "description": "<Description>",
        "activities": [
          {
            "type": "Copy",
    		"typeProperties": {
    		    "source": {
	    		    "type": "BlobSource",
	    			"skipHeaderLineCount": 1
	    	    },
	    		"sink": {
	    		    "type": "SqlDWSink",
	    		    "writeBatchSize": 0,
	    			"writeBatchTimeout": "00:00:10"
	    		}
	    	},
	    	"inputs": [
	    	  {
	    		"name": "<Storage Dataset>"
	    	  }
	    	],
	    	"outputs": [
	    	  {
	    	    "name": "<Data Warehouse Dataset>"
	    	  }
	    	],
	    	"policy": {
	            "timeout": "01:00:00",
	    	    "concurrency": 1
	    	},
	    	"scheduler": {
	    	    "frequency": "Hour",
	    		"interval": 1
	    	},
	    	"name": "Sample Copy",
	    	"description": "Copy Activity"
	      }
	    ],
	    "start": "<Date YYYY-MM-DD>",
	    "end": "<Date YYYY-MM-DD>",
	    "isPaused": false
    }
}
````

## 后续步骤

要了解详细信息，请先查看：

- [Azure SQL 数据仓库连接器](/documentation/articles/data-factory-azure-sql-data-warehouse-connector)。这是有关将 Azure 数据工厂和 Azure SQL 数据仓库配合使用的核心参考主题。


这些主题提供有关 Azure 数据工厂的详细信息。它们讨论 Azure SQL 数据库或 HDinsight，但该信息也适用于 Azure SQL 数据仓库。

- [教程：Azure 数据工厂入门](/documentation/articles/data-factory-build-your-first-pipeline)。这是使用 Azure 数据工厂处理数据的核心教程。在此教程中，你将构建使用 HDInsight 每月转换和分析 Web 日志的第一个管道。请注意，在此教程中没有复制活动。
- [教程：将数据从 Azure 存储 Blob 复制到 Azure SQL 数据库](/documentation/articles/data-factory-get-started)。在此教程中，你将在 Azure 数据工厂中创建管道以将数据从 Azure 存储 Blob 复制到 Azure SQL 数据库。
- [实际方案教程](/documentation/articles/data-factory-tutorial)。这是深入了解使用 Azure 数据工厂的教程。

<!---HONumber=Mooncake_0405_2016-->