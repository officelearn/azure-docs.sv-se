---
title: Azure-tjänster som stöder Azure Data Lake Storage Gen2 | Microsoft Docs
description: Lär dig mer om vilka Azure-tjänster som integreras med Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 71df31562971af8bcdb38be76894c1d0abeccc3f
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2020
ms.locfileid: "77925584"
---
# <a name="azure-services-that-support-azure-data-lake-storage-gen2"></a>Azure-tjänster som stöder Azure Data Lake Storage Gen2

Du kan använda Azure-tjänster för att mata in data, utföra analyser och skapa visuella representationer. Den här artikeln innehåller en lista över Azure-tjänster som stöds, utvärderar support nivån och innehåller länkar till artiklar som hjälper dig att använda de här tjänsterna med Azure Data Lake Storage Gen2.

## <a name="supportedazureservices"></a>Azure-tjänster som stöds

I den här tabellen visas de Azure-tjänster som du kan använda med Azure Data Lake Storage Gen2. De objekt som visas i tabellerna ändras med tiden då stödet fortsätter att expandera.

> [!NOTE]
> Support nivån avser endast hur tjänsten stöds med Data Lake Storage gen 2.

|Azure-tjänst |Support nivå |Relaterade artiklar |
|---------------|-------------------|---|
|Azure Data Factory|Allmänt tillgänglig|[Läs in data i Azure Data Lake Storage Gen2 med Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure Databricks|Allmänt tillgänglig|[Använd med Azure Databricks](https://docs.azuredatabricks.net/data/data-sources/azure/azure-datalake-gen2.html) <br> [Snabb start: analysera data i Azure Data Lake Storage Gen2 med Azure Databricks](data-lake-storage-quickstart-create-databricks-account.md) <br>[Självstudie: extrahera, transformera och läsa in data med hjälp av Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse) <br>[Självstudie: komma åt Data Lake Storage Gen2 data med Azure Databricks med Spark](data-lake-storage-use-databricks-spark.md)|
|Samling av Azure-Event Hubs| Allmänt tillgänglig|[Avbilda händelser via Azure Event Hubs i Azure Blob Storage eller Azure Data Lake Storage](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)|
|Azure Logic Apps|Allmänt tillgänglig|[Översikt – vad är Azure Logic Apps?](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)|
|Azure Machine Learning|Allmänt tillgänglig|[Få åtkomst till data i Azure Storage-tjänster](https://docs.microsoft.com/azure/machine-learning/how-to-access-data)|
|Azure Stream Analytics|Allmänt tillgänglig|[Snabb start: skapa ett Stream Analytics jobb genom att använda Azure Portal](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-portal) <br> [Utgående till Azure Data Lake Gen2](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-define-outputs#blob-storage-and-azure-data-lake-gen2)|
|Data Box| Allmänt tillgänglig|[Använd Azure Data Box för att migrera data från en lokal HDFS-lagring till Azure Storage](data-lake-storage-migrate-on-premises-hdfs-cluster.md)|
|HDInsight | Allmänt tillgänglig|[Använda Azure Data Lake Storage Gen2 med Azure HDInsight-kluster](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br>[Använda HDFS CLI med Data Lake Storage Gen2](data-lake-storage-use-hdfs-data-lake-storage.md) <br>[Självstudie: extrahera, transformera och läsa in data med Apache Hive på Azure HDInsight](data-lake-storage-tutorial-extract-transform-load-hive.md)|
|IoT Hub | Allmänt tillgänglig|[Använd IoT Hub meddelanderoutning för att skicka meddelanden från enheten till molnet till olika slut punkter](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)|
|Power BI| Allmänt tillgänglig|[Analysera data i Data Lake Storage Gen2 med Power BI](https://docs.microsoft.com/power-query/connectors/datalakestorage)|
|SQL Data Warehouse|Allmänt tillgänglig|[Använd med Azure SQL Data Warehouse](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#azure-sql-data-warehouse-polybase)|
|SQL Server Integration Services (SSIS)|Allmänt tillgänglig|[Azure Storage anslutnings hanteraren](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-storage-connection-manager?view=sql-server-2017)|
|Azure Cognitive Search| Förhandsversion|[Index och Sök Azure Data Lake Storage Gen2 dokument (förhands granskning)](https://docs.microsoft.com/azure/search/search-howto-index-azure-data-lake-storage)|
|Azure Content Delivery Network|Stöds inte ännu|[Index och Sök Azure Data Lake Storage Gen2 dokument (förhands granskning)](https://docs.microsoft.com/azure/cdn/cdn-overview)|


## <a name="see-also"></a>Se även

- [Kända problem med Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Blob Storage-funktioner som är tillgängliga i Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md)
- [Plattformar med öppen källkod som stöder Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md)
- [Åtkomst till flera protokoll på Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)