---
title: Integrera Data Lake Store med andra Azure-tjänster | Microsoft Docs
description: Förstå hur Data Lake Store integreras med andra Azure-tjänster
documentationcenter: ''
services: data-lake-store
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 48a5d1f4-3850-4c22-bbc4-6d1d394fba8a
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: 3fd1b03d2ce0b814f453ae4d87a136f28479662d
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34624105"
---
# <a name="integrating-data-lake-store-with-other-azure-services"></a>Integrera Data Lake Store med andra Azure-tjänster
Azure Data Lake Store kan användas tillsammans med andra Azure-tjänster för att aktivera ett bredare spektrum av scenarier. I följande artikel Listar de tjänster som Data Lake Store kan integreras med.

## <a name="use-data-lake-store-with-azure-hdinsight"></a>Använd Data Lake Store med Azure HDInsight
Du kan etablera ett [Azure HDInsight](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/) kluster som använder Data Lake Store som HDFS-kompatibla lagring. Den här versionen kan för Hadoop och Storm-kluster i Windows och Linux, du använda Data Lake Store enbart som ett ytterligare lagringsutrymme. Sådana kluster kan du fortfarande använda Azure Storage (WASB) som standardlagring. Dock gäller HBase-kluster i Windows och Linux, kan du använda Data Lake Store som standard lagringen, eller den ytterligare lagringsutrymme.

Anvisningar för hur du etablerar ett HDInsight-kluster med Data Lake Store, se:

* [Etablera ett HDInsight-kluster med Data Lake Store med hjälp av Azure Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Tillhandahålla ett HDInsight-kluster med Data Lake Store som standardlagring med hjälp av Azure PowerShell](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [Tillhandahålla ett HDInsight-kluster med Data Lake Store som ytterligare lagringsutrymme med hjälp av Azure PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md)

## <a name="use-data-lake-store-with-azure-data-lake-analytics"></a>Använd Data Lake Store med Azure Data Lake Analytics
[Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-overview.md) kan du arbeta med Stordata i molnskala. Den resurser etableras dynamiskt och du kan utföra analyser på terabyte eller även exabyte av data som kan lagras i ett antal datakällor som stöds, ett av dem som Data Lake Store. Data Lake Analytics är speciellt optimerade för att fungera med Azure Data Lake Store - ger högsta prestanda, genomströmning och parallellisering för dina arbetsbelastningar med stordata.

Instruktioner om hur du använder Data Lake Analytics med Data Lake Store finns [Kom igång med Data Lake Analytics med hjälp av Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md).

## <a name="use-data-lake-store-with-azure-data-factory"></a>Använd Data Lake Store med Azure Data Factory
Du kan använda [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) att mata in data från Azure-tabeller, Azure SQL Database, Azure SQL DataWarehouse, Azure Storage-Blobbar och lokala databaser. Som en förstklassigt allmänheten i Azure-ekosystemet kan Azure Data Factory användas till att dirigera införandet av data från dessa källa till Azure Data Lake Store.

Instruktioner om hur du använder Azure Data Factory med Data Lake Store finns [flytta data till och från Data Lake Store med hjälp av Data Factory](../data-factory/connector-azure-data-lake-store.md).

## <a name="copy-data-from-azure-storage-blobs-into-data-lake-store"></a>Kopiera data från Azure Storage-Blobbar till Data Lake Store
Azure Data Lake Store ger ett kommandoradsverktyg, AdlCopy som gör det möjligt att kopiera data från Azure Blob Storage till ett Data Lake Store-konto. Mer information finns i [kopiera data från Azure Storage-Blobbar till Data Lake Store](data-lake-store-copy-data-azure-storage-blob.md).

## <a name="copy-data-between-azure-sql-database-and-data-lake-store"></a>Kopiera data mellan Azure SQL Database och Data Lake Store
Du kan använda Apache Sqoop för att importera och exportera data mellan Azure SQL Database och Data Lake Store. Mer information finns i [kopiera data mellan Data Lake Store och Azure SQL database med Sqoop](data-lake-store-data-transfer-sql-sqoop.md).

## <a name="use-data-lake-store-with-stream-analytics"></a>Använd Data Lake Store med Stream Analytics
Du kan använda Data Lake Store som ett av utdata för att lagra data som direktuppspelas med Azure Stream Analytics. Mer information finns i [strömma data från Azure Storage Blob till Data Lake Store med Azure Stream Analytics](data-lake-store-stream-analytics.md).

## <a name="use-data-lake-store-with-power-bi"></a>Använd Data Lake Store med Powerbi
Du kan använda Power BI för att importera data från ett Data Lake Store-konto för att analysera och visualisera data. Mer information finns i [analysera data i Data Lake Store med hjälp av Power BI](data-lake-store-power-bi.md).

## <a name="use-data-lake-store-with-data-catalog"></a>Använd Data Lake Store med Data Catalog
Du kan registrera data från Data Lake Store i Azure Data Catalog att göra data synlig i hela organisationen. Mer information finns i [registrera data från Data Lake Store i Azure Data Catalog](data-lake-store-with-data-catalog.md).

## <a name="use-data-lake-store-with-sql-server-integration-services-ssis"></a>Använd Data Lake Store med SQL Server Integration Services (SSIS)
Du kan använda Anslutningshanteraren för Azure Data Lake Store i SSIS för att ansluta ett SSIS-paket med Azure Data Lake Store. Mer information finns i [Använd Data Lake Store med SSIS](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager).

## <a name="use-data-lake-store-with-sql-data-warehouse"></a>Använd Data Lake Store med SQL Data Warehouse
Du kan använda PolyBase för att läsa in data från Azure Data Lake Store till SQL Data Warehouse. Mer information finns i [Använd Data Lake Store med SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store.md).

## <a name="use-data-lake-store-with-azure-event-hubs"></a>Använd Data Lake Store med Azure Event Hubs
Du kan använda Azure Data Lake Store att arkivera och avbilda data som tagits emot av Händelsehubbar i Azure. Mer information finns i [Använd Data Lake Store med Azure Event Hubs](data-lake-store-archive-eventhub-capture.md).

## <a name="see-also"></a>Se också
* [Översikt över Azure Data Lake Store](data-lake-store-overview.md)
* [Kom igång med Data Lake Store med hjälp av portalen](data-lake-store-get-started-portal.md)
* [Kom igång med Data Lake Store med hjälp av PowerShell](data-lake-store-get-started-powershell.md)  

