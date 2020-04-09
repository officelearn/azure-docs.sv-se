---
title: Azure-tjänster som stöder Azure Data Lake Storage Gen2 | Microsoft-dokument
description: Lär dig mer om vilka Azure-tjänster som integreras med Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: edfcb1d0bfc87c84620b13ed26ec681fef32f1e3
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878331"
---
# <a name="azure-services-that-support-azure-data-lake-storage-gen2"></a>Azure-tjänster som stöder Azure Data Lake Storage Gen2

Du kan använda Azure-tjänster för att använda data, utföra analyser och skapa visuella representationer. Den här artikeln innehåller en lista över Azure-tjänster som stöds, avslöjar deras supportnivå och ger dig länkar till artiklar som hjälper dig att använda dessa tjänster med Azure Data Lake Storage Gen2.

## <a name="supported-azure-services"></a>Azure-tjänster som stöds

I den här tabellen visas de Azure-tjänster som du kan använda med Azure Data Lake Storage Gen2. De objekt som visas i dessa tabeller ändras med tiden när stödet fortsätter att expanderas.

> [!NOTE]
> Supportnivå avser endast hur tjänsten stöds med Data Lake Storage Gen 2.

|Azure-tjänst |Stödnivå |Relaterade artiklar |
|---------------|-------------------|---|
|Azure Data Factory|Allmänt tillgänglig|[Läsa in data i Azure Data Lake Storage Gen2 med Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure Databricks|Allmänt tillgänglig|[Använda med Azure Databricks](https://docs.azuredatabricks.net/data/data-sources/azure/azure-datalake-gen2.html) <br> [Snabbstart: Analysera data i Azure Data Lake Storage Gen2 med hjälp av Azure Databricks](data-lake-storage-quickstart-create-databricks-account.md) <br>[Självstudiekurs: Extrahera, transformera och läsa in data med hjälp av Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse) <br>[Självstudiekurs: Komma åt DataSjölagring Gen2-data med Azure Databricks med Spark](data-lake-storage-use-databricks-spark.md)|
|Azure Event Hub|Allmänt tillgänglig|[Samla in händelser via Azure Event Hubs i Azure Blob Storage eller Azure Data Lake Storage](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)|
|Azure Event Grid|Allmänt tillgänglig|[Självstudiekurs: Implementera datasjöinsamlingsmönstret för att uppdatera en Databricks Delta-tabell](data-lake-storage-events.md)|
|Azure Logic Apps|Allmänt tillgänglig|[Översikt – Vad är Azure Logic Apps?](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)|
|Azure Machine Learning|Allmänt tillgänglig|[Komma åt data i Azure-lagringstjänster](https://docs.microsoft.com/azure/machine-learning/how-to-access-data)|
|Azure Stream Analytics|Allmänt tillgänglig|[Snabbstart: Skapa ett Stream Analytics-jobb med hjälp av Azure-portalen](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-portal) <br> [Utgående till Azure Data Lake Gen2](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-define-outputs#blob-storage-and-azure-data-lake-gen2)|
|Data Box|Allmänt tillgänglig|[Använda Azure Data Box för att migrera data från ett lokalt HDFS-arkiv till Azure Storage](data-lake-storage-migrate-on-premises-hdfs-cluster.md)|
|HDInsight |Allmänt tillgänglig|[Använda Azure Data Lake Storage Gen2 med Azure HDInsight-kluster](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br>[Använda HDFS CLI med Data Lake Storage Gen2](data-lake-storage-use-hdfs-data-lake-storage.md) <br>[Självstudiekurs: Extrahera, transformera och läsa in data med hjälp av Apache Hive på Azure HDInsight](data-lake-storage-tutorial-extract-transform-load-hive.md)|
|IoT Hub |Allmänt tillgänglig|[Använda IoT Hub-meddelanderoutning för att skicka meddelanden från enhet till moln till olika slutpunkter](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)|
|Power BI|Allmänt tillgänglig|[Analysera data i Data Lake Storage Gen2 med Power BI](https://docs.microsoft.com/power-query/connectors/datalakestorage)|
|SQL Data Warehouse|Allmänt tillgänglig|[Använda med Azure SQL Data Warehouse](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#azure-sql-data-warehouse-polybase)|
|SQL Server Integration Services (SSIS)|Allmänt tillgänglig|[Azure Storage-anslutningshanterare](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-storage-connection-manager?view=sql-server-2017)|
|Azure Cognitive Search|Förhandsversion|[Indexera och söka i Azure Data Lake Storage Gen2-dokument (förhandsversion)](https://docs.microsoft.com/azure/search/search-howto-index-azure-data-lake-storage)|
|Azure-datautforskaren|Förhandsversion|[Fråga data i Azure Data Lake med Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/data-lake-query-data)|
|Azure Content Delivery Network|Ännu inte stöds|[Indexera och söka i Azure Data Lake Storage Gen2-dokument (förhandsversion)](https://docs.microsoft.com/azure/cdn/cdn-overview)|


## <a name="see-also"></a>Se även

- [Kända problem med Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Blob-lagringsfunktioner som är tillgängliga i Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md)
- [Plattformar med öppen källkod som stöder Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md)
- [Åtkomst till flera protokoll på Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)