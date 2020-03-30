---
title: Integrera Azure Data Lake Storage Gen1 med andra Azure-tjänster | Microsoft-dokument
description: Förstå hur Azure Data Lake Storage Gen1 integreras med andra Azure-tjänster
documentationcenter: ''
services: data-lake-store
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: 48a5d1f4-3850-4c22-bbc4-6d1d394fba8a
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 43024b63a355646f607adbb6623cc6c349374ea8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535603"
---
# <a name="integrating-azure-data-lake-storage-gen1-with-other-azure-services"></a>Integrera Azure Data Lake Storage Gen1 med andra Azure-tjänster
Azure Data Lake Storage Gen1 kan användas tillsammans med andra Azure-tjänster för att aktivera ett bredare spektrum av scenarier. I följande artikel visas de tjänster som Data Lake Storage Gen1 kan integreras med.

## <a name="use-data-lake-storage-gen1-with-azure-hdinsight"></a>Använda DataSjölagringgend1 med Azure HDInsight
Du kan etablera ett [Azure HDInsight-kluster](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/) som använder Data Lake Storage Gen1 som HDFS-kompatibel lagring. För den här versionen, för Hadoop- och Storm-kluster på Windows och Linux, kan du endast använda Data Lake Storage Gen1 som ytterligare lagring. Sådana kluster använder fortfarande Azure Storage (WASB) som standardlagring. För HBase-kluster på Windows och Linux kan du dock använda Data Lake Storage Gen1 som standardlagring, eller ytterligare lagring, eller båda.

Instruktioner om hur du etablerar ett HDInsight-kluster med Gen1 för lagring av datasjö, finns i:

* [Etablera ett HDInsight-kluster med Data Lake Storage Gen1 med Azure Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Etablera ett HDInsight-kluster med Data Lake Storage Gen1 som standardlagring med Azure PowerShell](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [Etablera ett HDInsight-kluster med Data Lake Storage Gen1 som ytterligare lagringsutrymme med Azure PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md)

## <a name="use-data-lake-storage-gen1-with-azure-data-lake-analytics"></a>Använda DataSjölagringgend1 med Azure Data Lake Analytics
[Med Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-overview.md) kan du arbeta med Big Data i molnskala. Det etablerar dynamiskt resurser och låter dig göra analyser på terabyte eller till och med exabyte data som kan lagras i ett antal datakällor som stöds, varav en är DataSjölagring Gen1. DataSjöanalys är speciellt optimerad för att fungera med Data Lake Storage Gen1 – vilket ger högsta prestanda, dataflöde och parallellisering för dig av stordataarbetsbelastningar.

Instruktioner om hur du använder DataSjöanalys med Data Lake Storage Gen1 finns [i Komma igång med DataSjöanalys med Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md).

## <a name="use-data-lake-storage-gen1-with-azure-data-factory"></a>Använda DataSjölagringgend1 med Azure Data Factory
Du kan använda [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) för att använda data från Azure-tabeller, Azure SQL Database, Azure SQL DataWarehouse, Azure Storage Blobbar och lokala databaser. Azure Data Factory är första klassmedborgare i Azure-ekosystemet och kan användas för att dirigera inmatning av data från dessa källor till Data Lake Storage Gen1.

Instruktioner om hur du använder Azure Data Factory med Data Lake Storage Gen1 finns i [Flytta data till och från Data Lake Storage Gen1 med Data Factory](../data-factory/connector-azure-data-lake-store.md).

## <a name="copy-data-from-azure-storage-blobs-into-data-lake-storage-gen1"></a>Kopiera data från Azure Storage Blobbar till Data Lake Storage Gen1
Azure Data Lake Storage Gen1 innehåller ett kommandoradsverktyg, AdlCopy, som gör att du kan kopiera data från Azure Blob Storage till ett Data Lake Storage Gen1-konto. Mer information finns i [Kopiera data från Azure Storage Blobbar till Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md).

## <a name="copy-data-between-azure-sql-database-and-data-lake-storage-gen1"></a>Kopiera data mellan Azure SQL Database och Data Lake Storage Gen1
Du kan använda Apache Sqoop för att importera och exportera data mellan Azure SQL Database och Data Lake Storage Gen1. Mer information finns i [Kopiera data mellan Data Lake Storage Gen1 och Azure SQL-databas med Sqoop](data-lake-store-data-transfer-sql-sqoop.md).

## <a name="use-data-lake-storage-gen1-with-stream-analytics"></a>Använda DataSjölagring Gen1 med Stream Analytics
Du kan använda Data Lake Storage Gen1 som en av utdata för att lagra data som strömmas med Azure Stream Analytics. Mer information finns i [Strömma data från Azure Storage Blob till Data Lake Storage Gen1 med Azure Stream Analytics](data-lake-store-stream-analytics.md).

## <a name="use-data-lake-storage-gen1-with-power-bi"></a>Använda DataSjölagringGeny1 med Power BI
Du kan använda Power BI för att importera data från ett Data Lake Storage Gen1-konto för att analysera och visualisera data. Mer information finns [i Analysera data i DataSjölagring Gen1 med Power BI](data-lake-store-power-bi.md).

## <a name="use-data-lake-storage-gen1-with-data-catalog"></a>Använda Datasjölagringsgenm1 med datakatalog
Du kan registrera data från Data Lake Storage Gen1 i Azure Data Catalog för att göra data upptäckbara i hela organisationen. Mer information finns [i Registrera data från Data Lake Storage Gen1 i Azure Data Catalog](data-lake-store-with-data-catalog.md).

## <a name="use-data-lake-storage-gen1-with-sql-server-integration-services-ssis"></a>Använda DataSjölagringsgend1 med SQL Server Integration Services (SSIS)
Du kan använda anslutningshanteraren För datasjölagring Gen1 i SSIS för att ansluta ett SSIS-paket till Data Lake Storage Gen1. Mer information finns i [Använda datasjölagring gen1 med SSIS](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager).

## <a name="use-data-lake-storage-gen1-with-sql-data-warehouse"></a>Använda DataSjölagringsgeny1 med SQL Data Warehouse
Du kan använda PolyBase för att läsa in data från Data Lake Storage Gen1 i SQL Data Warehouse. Mer information finns i [Använda datasjölagringgend1 med SQL Data Warehouse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store.md).

## <a name="use-data-lake-storage-gen1-with-azure-event-hubs"></a>Använda DataSjölagringgend1 med Azure-händelsehubbar
Du kan använda Azure Data Lake Storage Gen1 för att arkivera och samla in data som tas emot av Azure Event Hubs. Mer information finns i [Använda DataSjölagringgend1 med Azure Event Hubs](data-lake-store-archive-eventhub-capture.md).

## <a name="see-also"></a>Se även
* [Översikt över Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Komma igång med Data Lake Storage Gen1 med Portal](data-lake-store-get-started-portal.md)
* [Komma igång med Data Lake Storage Gen1 med PowerShell](data-lake-store-get-started-powershell.md)  

