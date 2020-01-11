---
title: Integrera Azure Data Lake Storage med Azure-tjänster | Microsoft Docs
description: Lär dig mer om vilka Azure-tjänster som integreras med Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/01/2019
ms.author: normesta
ms.openlocfilehash: de57776ea3bcc7486c5c26182c7e2a65e643d81c
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2020
ms.locfileid: "75863010"
---
# <a name="integrate-azure-data-lake-storage-with-azure-services"></a>Integrera Azure Data Lake Storage med Azure-tjänster

Du kan använda Azure-tjänster för att mata in data, utföra analyser och skapa visuella representationer. Den här artikeln innehåller en lista över Azure-tjänster som stöds och innehåller länkar till artiklar som hjälper dig att använda de här tjänsterna med Azure Data Lake Storage Gen2.

## <a name="azure-services-that-support-azure-data-lake-storage-gen2"></a>Azure-tjänster som stöder Azure Data Lake Storage Gen2

I följande tabell visas de Azure-tjänster som stöder Azure Data Lake Storage Gen2.

| Azure-tjänst |  Relaterade artiklar |
|---------------|-------------------|
|Azure Data Factory | [Läs in data i Azure Data Lake Storage Gen2 med Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure Databricks | [Använd med Azure Databricks](https://docs.azuredatabricks.net/data/data-sources/azure/azure-datalake-gen2.html) <br> [Snabb start: analysera data i Azure Data Lake Storage Gen2 med Azure Databricks](data-lake-storage-quickstart-create-databricks-account.md) <br>[Självstudie: extrahera, transformera och läsa in data med hjälp av Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse) <br>[Självstudie: komma åt Data Lake Storage Gen2 data med Azure Databricks med Spark](data-lake-storage-use-databricks-spark.md) |
|Samling av Azure-Event Hubs| [Avbilda händelser via Azure Event Hubs i Azure Blob Storage eller Azure Data Lake Storage](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)|
|Azure Logic Apps | [Översikt – vad är Azure Logic Apps?](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)|
|Azure Machine Learning|[Få åtkomst till data i Azure Storage-tjänster](https://docs.microsoft.com/azure/machine-learning/how-to-access-data)|
|Kognitiv sökning i Azure | [Index och Sök Azure Data Lake Storage Gen2 dokument (förhands granskning)](https://docs.microsoft.com/azure/search/search-howto-index-azure-data-lake-storage)|
|Azure Stream Analytics| [Snabbstart: Skapa ett Stream Analytics-jobb med hjälp av Azure portal](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-portal) <br> [Utgående till Azure Data Lake Gen2](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-define-outputs#blob-storage-and-azure-data-lake-gen2) |
|Data Box|  [Använd Azure Data Box för att migrera data från en lokal HDFS-lagring till Azure Storage](data-lake-storage-migrate-on-premises-hdfs-cluster.md)|
|HDInsight | [Använda Azure Data Lake Storage Gen2 med Azure HDInsight-kluster](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br>[Använda HDFS CLI med Data Lake Storage Gen2](data-lake-storage-use-hdfs-data-lake-storage.md) <br>[Självstudie: extrahera, transformera och läsa in data med Apache Hive på Azure HDInsight](data-lake-storage-tutorial-extract-transform-load-hive.md) |
|IoT Hub | [Använd IoT Hub meddelanderoutning för att skicka meddelanden från enheten till molnet till olika slut punkter](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)|
|Power BI|  [Analysera data i Data Lake Storage Gen2 med Power BI](data-lake-storage-use-power-bi.md) |
|SQL Data Warehouse | [Använd med Azure SQL Data Warehouse](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#azure-sql-data-warehouse-polybase)|
|SQL Server Integration Services (SSIS) | [Azure Storage anslutnings hanteraren](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-storage-connection-manager?view=sql-server-2017)|

## <a name="next-steps"></a>Nästa steg

- Läs mer om vilka verktyg du kan använda för att bearbeta data i data Lake. Se [använda Azure Data Lake Storage Gen2 för Big data-krav](data-lake-storage-data-scenarios.md).

- Läs mer om Data Lake Storage Gen2 och hur du kommer igång med det. Se [Introduktion till Azure Data Lake Storage Gen2](data-lake-storage-introduction.md)
