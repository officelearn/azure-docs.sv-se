---
title: Förstå Apache Spark-dataformat för Azure Data Lake Analytics U-SQL-utvecklare.
description: I den här artikeln beskrivs Apache Spark-begrepp som hjälper U_SQL utvecklare att förstå skillnader mellan U-SQL- och Spark-dataformat.
author: guyhay
ms.author: guyhay
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: conceptual
ms.custom: understand-apache-spark-data-formats
ms.date: 01/31/2019
ms.openlocfilehash: 36f39503ca32f1ee4b422ae7b1cf9abf48716f07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73648446"
---
# <a name="understand-differences-between-u-sql-and-spark-data-formats"></a>Förstå skillnader mellan U-SQL- och Spark-dataformat

Om du vill använda antingen [Azure Databricks](../azure-databricks/what-is-azure-databricks.md) eller [Azure HDInsight Spark](../hdinsight/spark/apache-spark-overview.md)rekommenderar vi att du migrerar dina data från Azure Data Lake Storage [Gen1](../data-lake-store/data-lake-store-overview.md) till [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md).

Förutom att flytta dina filer, vill du också göra dina data, lagras i U-SQL-tabeller, tillgängliga för Spark.

## <a name="move-data-stored-in-azure-data-lake-storage-gen1-files"></a>Flytta data som lagras i Azure Data Lake Storage Gen1-filer

Data som lagras i filer kan flyttas på olika sätt:

- Skriv en [Azure Data Factory-pipeline](../data-factory/introduction.md) för att kopiera data från [Azure Data Lake Storage Gen1-kontot](../data-lake-store/data-lake-store-overview.md) till [Azure Data Lake Storage Gen2-kontot.](../storage/blobs/data-lake-storage-introduction.md)
- Skriv ett Spark-jobb som läser data från [Azure Data Lake Storage Gen1-kontot](../data-lake-store/data-lake-store-overview.md) och skriver det till Azure Data Lake Storage [Gen2-kontot.](../storage/blobs/data-lake-storage-introduction.md) Baserat på ditt användningsfall kanske du vill skriva det i ett annat format, till exempel Parkett om du inte behöver bevara det ursprungliga filformatet.

Vi rekommenderar att du granskar artikeln [Uppgradera dina lösningar för stordataanalys från Azure Data Lake Storage Gen1 till Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-upgrade.md)

## <a name="move-data-stored-in-u-sql-tables"></a>Flytta data som lagras i U-SQL-tabeller

U-SQL-tabeller förstås inte av Spark. Om du har data som lagras i U-SQL-tabeller kör du ett U-SQL-jobb som extraherar tabelldata och sparar dem i ett format som Spark förstår. Det lämpligaste formatet är att skapa en uppsättning parquet-filer efter Hive-metabutikens mapplayout.

Utdata kan uppnås i U-SQL med den inbyggda Parquet outputter och med hjälp av den dynamiska utdatapartitioneringen med filuppsättningar för att skapa partitionsmapparna. [Bearbeta fler filer än någonsin och använda Parkett](https://blogs.msdn.microsoft.com/azuredatalake/2018/06/11/process-more-files-than-ever-and-use-parquet-with-azure-data-lake-analytics) ger ett exempel på hur man skapar sådana Spark förbrukningsbara data.

Efter den här omvandlingen kopierar du data som beskrivs i kapitlet [Flytta data som lagras i Azure Data Lake Storage Gen1-filer](#move-data-stored-in-azure-data-lake-storage-gen1-files).

## <a name="caveats"></a>Varningar

- Datasenmantik Vid kopiering av filer sker kopian på bytenivå. Så samma data bör visas i [Azure Data Lake Storage Gen2-kontot.](../storage/blobs/data-lake-storage-introduction.md) Spark kan dock tolka vissa tecken på olika sätt. Den kan till exempel använda en annan standard för en radavgränsare i en CSV-fil.
    Om du kopierar maskinskrivna data (från tabeller) kan parett och Spark dessutom ha olika precision och skala för vissa av de maskinskrivna värdena (till exempel en flottör) och kan behandla null-värden på olika sätt. U-SQL har till exempel C#-semantiken för null-värden, medan Spark har en trevärdeslogik för null-värden.

- U-SQL-tabeller för dataorganisation (partitionering) ger två nivåpartitionering. Den yttre`PARTITIONED BY`nivån ( ) är efter värde och mappar mestadels till hive/spark-partitioneringsschemat med hjälp av mapphierarkier. Du måste se till att null-värdena mappas till rätt mappning. Den inre`DISTRIBUTED BY`nivån ( ) i U-SQL erbjuder 4 distributionsscheman: round robin, intervall, hash och direkt hash.
    Hive/Spark-tabeller stöder endast värdepartitionering eller hash-partitionering, med hjälp av en annan hash-funktion än U-SQL. När du matar ut dina U-SQL-tabelldata kan du förmodligen bara mappa in i värdepartitioneringen för Spark och kan behöva justera datalayouten ytterligare beroende på dina slutliga Spark-frågor.

## <a name="next-steps"></a>Nästa steg

- [Förstå Spark-kodkoncept för U-SQL-utvecklare](understand-spark-code-concepts.md)
- [Uppgradera dina lösningar för stordataanalys från Azure Data Lake Storage Gen1 till Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-upgrade.md)
- [.NET för Apache Spark](https://docs.microsoft.com/dotnet/spark/what-is-apache-spark-dotnet)
- [Omvandla data med Spark-aktivitet i Azure Data Factory](../data-factory/transform-data-using-spark.md)
- [Omvandla data med Hadoop Hive-aktivitet i Azure Data Factory](../data-factory/transform-data-using-hadoop-hive.md)
- [Vad är Apache Spark i Azure HDInsight](../hdinsight/spark/apache-spark-overview.md)
