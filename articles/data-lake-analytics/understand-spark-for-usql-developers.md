---
title: Förstå Apache Spark för Azure Data Lake Analytics U-SQL-utvecklare.
description: I den här artikeln beskrivs Apache Spark-begrepp som hjälper dig att skilja mellan U-SQL-utvecklare.
author: guyhay
ms.author: guyhay
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: conceptual
ms.custom: understand-apache-spark-for-usql-developers
ms.date: 10/15/2019
ms.openlocfilehash: 594e1055c4c063e4e151fefa3e183e6e799c90b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73648433"
---
# <a name="understand-apache-spark-for-u-sql-developers"></a>Förstå Apache Spark för U-SQL-utvecklare

Microsoft stöder flera Analytics-tjänster som [Azure Databricks](../azure-databricks/what-is-azure-databricks.md) och [Azure HDInsight](../hdinsight/hdinsight-overview.md) samt Azure Data Lake Analytics. Vi hör från utvecklare att de har en tydlig preferens för lösningar med öppen källkod när de bygger analyspipelpipelor. För att hjälpa U-SQL-utvecklare att förstå Apache Spark och hur du kan omvandla dina U-SQL-skript till Apache Spark har vi skapat den här vägledningen.

Den innehåller ett antal steg du kan vidta, och flera alternativ.

## <a name="steps-to-transform-u-sql-to-apache-spark"></a>Åtgärder för att omvandla U-SQL till Apache Spark

1. Förvandla dina pipelines för jobborkestrering.

   Om du använder [Azure Data Factory](../data-factory/introduction.md) för att dirigera dina Azure Data Lake Analytics-skript måste du justera dem för att dirigera de nya Spark-programmen.
2. Förstå skillnaderna mellan hur U-SQL och Spark hanterar data

   Om du vill flytta dina data från [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) till Azure Data Lake Storage [Gen2](../storage/blobs/data-lake-storage-introduction.md)måste du kopiera både fildata och katalogunderhållna data. Observera att Azure Data Lake Analytics endast stöder Azure Data Lake Storage Gen1. Se [Förstå Spark-dataformat](understand-spark-data-formats.md)
3. Förvandla dina U-SQL-skript till Spark

   Innan du omvandlar dina U-SQL-skript måste du välja en analystjänst. Några av de tillgängliga beräkningstjänsterna är:
      - [Azure Data Factory-dataflöde](../data-factory/concepts-data-flow-overview.md) Mappning av dataflöden är visuellt utformade dataomvandlingar som gör det möjligt för datatekniker att utveckla en grafisk dataomvandlingslogik utan att skriva kod. Även om de inte är lämpade för att köra komplex användarkod, kan de enkelt representera traditionella SQL-liknande dataflödesomvandlingar
      - [Azure HDInsight Hive](../hdinsight/hadoop/apache-hadoop-using-apache-hive-as-an-etl-tool.md) Apache Hive på HDInsight är lämplig för utvinning, transformering och belastning (ETL) åtgärder. Det innebär att du ska översätta dina U-SQL-skript till Apache Hive.
      - Apache Spark-motorer som [Azure HDInsight Spark](../hdinsight/spark/apache-spark-overview.md) eller [Azure Databricks](../azure-databricks/what-is-azure-databricks.md) Det innebär att du kommer att översätta dina U-SQL-skript till Spark. Mer information finns i [Förstå spark-dataformat](understand-spark-data-formats.md)

> [!CAUTION]
> Både [Azure Databricks](../azure-databricks/what-is-azure-databricks.md) och [Azure HDInsight Spark](../hdinsight/spark/apache-spark-overview.md) är klustertjänster och inte serverlösa jobb som Azure Data Lake Analytics. Du måste överväga hur du etablerar kluster för att få rätt kostnads- och prestandaförhållande och hur du hanterar deras livstid för att minimera dina kostnader. Dessa tjänster har olika prestandaegenskaper med användarkod skriven i .NET, så du måste antingen skriva omslag eller skriva om koden på ett språk som stöds. Mer information finns i [Förstå Spark-dataformat](understand-spark-data-formats.md), [Förstå Apache Spark-kodbegrepp för U-SQL-utvecklare](understand-spark-code-concepts.md), [.Net för Apache Spark](https://dotnet.microsoft.com/apps/data/spark)

## <a name="next-steps"></a>Nästa steg

- [Förstå Spark-dataformat för U-SQL-utvecklare](understand-spark-data-formats.md)
- [Förstå Spark-kodkoncept för U-SQL-utvecklare](understand-spark-code-concepts.md)
- [Uppgradera dina lösningar för stordataanalys från Azure Data Lake Storage Gen1 till Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-upgrade.md)
- [.NET för Apache Spark](https://docs.microsoft.com/dotnet/spark/what-is-apache-spark-dotnet)
- [Omvandla data med Hadoop Hive-aktivitet i Azure Data Factory](../data-factory/transform-data-using-hadoop-hive.md)
- [Omvandla data med Spark-aktivitet i Azure Data Factory](../data-factory/transform-data-using-spark.md)
- [Vad är Apache Spark i Azure HDInsight](../hdinsight/spark/apache-spark-overview.md)
