---
title: Integrera Azure Data Lake Storage Gen1 med andra Azure-tjänster | Microsoft Docs
description: Förstå hur Azure Data Lake Storage Gen1 kan integreras med andra Azure-tjänster
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
ms.openlocfilehash: e28863f9980d6403bef1f88de01b7a9b5271b444
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2019
ms.locfileid: "58879314"
---
# <a name="integrating-azure-data-lake-storage-gen1-with-other-azure-services"></a>Integrera Azure Data Lake Storage Gen1 med andra Azure-tjänster
Azure Data Lake Storage Gen1 kan användas tillsammans med andra Azure-tjänster för att aktivera ett bredare spektrum av scenarier. I följande artikel listas de tjänster som Data Lake Storage Gen1 kan integreras med.

## <a name="use-data-lake-storage-gen1-with-azure-hdinsight"></a>Använda Data Lake Storage Gen1 med Azure HDInsight
Du kan etablera en [Azure HDInsight](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/) kluster som använder Data Lake Storage Gen1 som HDFS-kompatibel lagring. Den här versionen kan för Hadoop- och Storm-kluster i Windows och Linux, du använda Data Lake Storage Gen1 endast som ett ytterligare lagringsutrymme. Sådana kluster kan du fortfarande använda Azure Storage (WASB) som standardlagring. HBase-kluster i Windows och Linux kan använda du Data Lake Storage Gen1 som standardlagring eller den ytterligare lagringsutrymme.

Mer information om hur du etablerar ett HDInsight-kluster med Data Lake Storage Gen1 finns i:

* [Etablera ett HDInsight-kluster med Data Lake Storage Gen1 med hjälp av Azure Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Etablera ett HDInsight-kluster med Data Lake Storage Gen1 som standard storage med hjälp av Azure PowerShell](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [Etablera ett HDInsight-kluster med Data Lake Storage Gen1 som ytterligare lagringsutrymme med hjälp av Azure PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md)

## <a name="use-data-lake-storage-gen1-with-azure-data-lake-analytics"></a>Använda Data Lake Storage Gen1 med Azure Data Lake Analytics
[Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-overview.md) kan du arbeta med Stordata i molnskala. Den resurser etableras dynamiskt och kan utföra analyser på terabyte eller även exabyte av data som kan lagras på flera olika datakällor som stöds, en av dem som Data Lake Storage Gen1. Data Lake Analytics är speciellt optimerade för att arbeta med Data Lake Storage Gen1 - ger högsta prestanda, dataflöde och parallellisering för big data-arbetsbelastningar.

Instruktioner om hur du använder Data Lake Analytics med Data Lake Storage Gen1 finns [Kom igång med Data Lake Analytics med Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md).

## <a name="use-data-lake-storage-gen1-with-azure-data-factory"></a>Använda Data Lake Storage Gen1 med Azure Data Factory
Du kan använda [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) att mata in data från Azure-tabeller, Azure SQL Database, Azure SQL DataWarehouse, Azure Storage-Blobbar och lokala databaser. Som en första klassens medborgare i Azure-ekosystemet, kan Azure Data Factory vara används för att samordna inmatningen av data från dessa källa till Data Lake Storage Gen1.

Instruktioner om hur du använder Azure Data Factory med Data Lake Storage Gen1 finns [flytta data till och från Data Lake Storage Gen1 med Data Factory](../data-factory/connector-azure-data-lake-store.md).

## <a name="copy-data-from-azure-storage-blobs-into-data-lake-storage-gen1"></a>Kopiera data från Azure Storage BLOB till Data Lake Storage Gen1
Azure Data Lake Storage Gen1 ger ett kommandoradsverktyg, AdlCopy, där du kan kopiera data från Azure Blob Storage till en Gen1 för Data Lake Storage-konto. Mer information finns i [kopiera data från Azure Storage BLOB till Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md).

## <a name="copy-data-between-azure-sql-database-and-data-lake-storage-gen1"></a>Kopiera data mellan Azure SQL Database och Data Lake Storage Gen1
Du kan använda Apache Sqoop för att importera och exportera data mellan Azure SQL Database och Data Lake Storage Gen1. Mer information finns i [kopiera data mellan Data Lake Storage Gen1 och Azure SQL database med Sqoop](data-lake-store-data-transfer-sql-sqoop.md).

## <a name="use-data-lake-storage-gen1-with-stream-analytics"></a>Använda Data Lake Storage Gen1 med Stream Analytics
Du kan använda Data Lake Storage Gen1 som en av utdata för att lagra data som strömmas med direktuppspelning med Azure Stream Analytics. Mer information finns i [Stream data från Azure Storage Blob till Data Lake Storage Gen1 med Azure Stream Analytics](data-lake-store-stream-analytics.md).

## <a name="use-data-lake-storage-gen1-with-power-bi"></a>Använda Data Lake Storage Gen1 med Powerbi
Du kan använda Power BI för att importera data från ett Data Lake Storage Gen1-konto för att analysera och visualisera data. Mer information finns i [analysera data i Data Lake Storage Gen1 med Power BI](data-lake-store-power-bi.md).

## <a name="use-data-lake-storage-gen1-with-data-catalog"></a>Använda Data Lake Storage Gen1 med Data Catalog
Du kan registrera data från Data Lake Storage Gen1 i Azure Data Catalog att göra data synlig i hela organisationen. Mer information finns i [registrera data från Data Lake Storage Gen1 i Azure Data Catalog](data-lake-store-with-data-catalog.md).

## <a name="use-data-lake-storage-gen1-with-sql-server-integration-services-ssis"></a>Använda Data Lake Storage Gen1 med SQL Server Integration Services (SSIS)
Du kan använda Data Lake Storage Gen1 Anslutningshanteraren i SSIS för att ansluta ett SSIS-paket med Data Lake Storage Gen1. Mer information finns i [Använd Data Lake Storage Gen1 med SSIS](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager).

## <a name="use-data-lake-storage-gen1-with-sql-data-warehouse"></a>Använda Data Lake Storage Gen1 med SQL Data Warehouse
Du kan använda PolyBase för att läsa in data från Data Lake Storage Gen1 till SQL Data Warehouse. Mer information finns i [Använd Data Lake Storage Gen1 med SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store.md).

## <a name="use-data-lake-storage-gen1-with-azure-event-hubs"></a>Använda Data Lake Storage Gen1 med Azure Event Hubs
Du kan använda Azure Data Lake Storage Gen1 till arkivet och avbilda data som tas emot av Händelsehubbar i Azure. Mer information finns i [Använd Data Lake Storage Gen1 med Azure Event Hubs](data-lake-store-archive-eventhub-capture.md).

## <a name="see-also"></a>Se också
* [Översikt över Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Komma igång med Data Lake Storage Gen1 med hjälp av portalen](data-lake-store-get-started-portal.md)
* [Kom igång med Data Lake Storage Gen1 med hjälp av PowerShell](data-lake-store-get-started-powershell.md)  

