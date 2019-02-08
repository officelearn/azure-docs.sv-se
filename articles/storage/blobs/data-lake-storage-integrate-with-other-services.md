---
title: Integrera Azure Data Lake Storage Gen2 med andra Azure-tjänster | Microsoft Docs
description: Förstå hur Azure Data Lake Storage Gen2 kan integreras med andra Azure-tjänster
documentationcenter: ''
services: storage
author: normesta
ms.component: data-lake-storage-gen2
ms.service: storage
ms.devlang: na
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: nitinme
ms.openlocfilehash: 7bc4889403e1d52cfa3b18792a554f0faf605132
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55885690"
---
# <a name="integrate-azure-data-lake-storage-gen2-with-other-azure-services"></a>Integrera Azure Data Lake Storage Gen2 med andra Azure-tjänster

Du kan använda Azure-tjänster för att mata in, analysera och visualisera data i ditt lagringskonto i Azure Data Lake Storage Gen2. Välj tjänster som bäst passar de uppgifter som du försöker utföra.

## <a name="ingest-data-into-your-data-lake"></a>Mata in data i ditt data lake

De här tjänsterna kan du fylla i ditt data lake med data.

### <a name="azure-data-factory"></a>Azure Data Factory

Du kan använda [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) att mata in data från dessa källor:

* Azure-tabeller
* Azure SQL-databaser
* Azure SQL DataWarehouse
* Azure Storage-Blobbar
* Lokala databaser

Se [flytta data till och från Data Lake Storage Gen2 med Data Factory](../../data-factory/connector-azure-data-lake-store.md).

## <a name="analyze-and-visualize-data-in-your-data-lake"></a>Analysera och visualisera data i data lake

De här tjänsterna kan använda Data Lake Storage Gen2 som en slutpunkt för lagring.

### <a name="azure-hdinsight"></a>Azure HDInsight

Du kan etablera en [Azure HDInsight](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/) kluster som använder Data Lake Storage Gen2 som HDFS-kompatibel lagring. Den här versionen kan för Hadoop- och Storm-kluster i Windows och Linux, du använda Data Lake Storage Gen2 endast som ett ytterligare lagringsutrymme. Sådana kluster kan du fortfarande använda Azure Storage (WASB) som standardlagring. HBase-kluster i Windows och Linux, kan du dock använda Data Lake Storage Gen2 som standardlagring och som ytterligare lagringsutrymme.

Se [Använd Azure Data Lake Storage Gen2 med Azure HDInsight-kluster](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2)

### <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics

Du kan använda [Azure Data Lake Analytics](../../data-lake-analytics/data-lake-analytics-overview.md) att arbeta med Stordata i molnskala. Den resurser etableras dynamiskt, och kan du analysera exabyte med data. Data Lake Analytics är optimerat för att använda Data Lake Storage Gen2 som en datakälla. 

Se [Kom igång med Data Lake Analytics med Data Lake Storage Gen2](../../data-lake-analytics/data-lake-analytics-get-started-portal.md).

## <a name="copy-data-to-other-repositories"></a>Kopieringsdata till andra databaser

Du kan använda dessa tjänster för att kopiera data från dina data lake och placera dem i andra databaser.

### <a name="sql-data-warehouse"></a>SQL Data Warehouse

Du kan använda PolyBase för att läsa in data från Data Lake Storage Gen2 till SQL Data Warehouse. Mer information finns i [Använd Data Lake Storage Gen2 med SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store.md).

## <a name="see-also"></a>Se också

* [Översikt över Azure Data Lake Storage Gen2](data-lake-storage-introduction.md)
* [Med hjälp av Azure Data Lake Storage Gen2 för stordatakrav](data-lake-storage-data-scenarios.md)
