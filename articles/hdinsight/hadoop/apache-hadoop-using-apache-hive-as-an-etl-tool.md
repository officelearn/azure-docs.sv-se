---
title: "Med Apache Hive som ett verktyg för ETL - Azure HDInsight | Microsoft Docs"
description: "Använd Apache Hive för att extrahera, transformering och laddning (ETL) data i Azure HDInsight."
services: hdinsight
documentationcenter: 
author: ashishthaps
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/14/2017
ms.author: ashishth
ms.openlocfilehash: 1ccbfe23e9c887a98a0dbfa8031078a15c6e41b6
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2018
---
# <a name="use-apache-hive-as-an-extract-transform-and-load-etl-tool"></a>Använda Apache Hive som ett verktyg för extrahering, transformering och inläsning (ETL)

Du behöver normalt rensa och transformera inkommande data innan du läser in den i ett mål som är lämplig för analys. Extrahering, transformering och Load (ETL) åtgärder för att förbereda data och läsa in den i ett datamål.  Hive i HDInsight kan du läsa i Ostrukturerade data, bearbeta data efter behov och läsa in data till en relationella data warehouse för beslut supportsystem. I den här metoden data extraheras från källan och lagras i skalbar lagring, till exempel Azure Storage-blobbar eller Azure Data Lake Store. Data omvandlas sedan med hjälp av en sekvens med Hive-frågor och slutligen mellanlagras i Hive inför bulk lästes in i datalagret mål.

## <a name="use-case-and-model-overview"></a>Använd case och modellen: översikt

Följande bild visar en översikt över användningsfall och modell för ETL-automatisering. Indata omvandlas för att generera lämplig utdata.  Under omvandlingen, kan data ändra form, datatyp och även språk.  ETL-rutiner kan konvertera Imperial måtten, ändra tidszoner och förbättra precision korrekt justeras med befintliga data i målet.  ETL-rutiner kan också kombinera nya data med befintliga data att hålla reporting uppdaterade eller ger ytterligare information om befintliga data.  Program, till exempel reporting verktyg och tjänster kan sedan använda dessa data i det önskade formatet.

![Apache Hive som ETL](./media/apache-hadoop-using-apache-hive-as-an-etl-tool/hdinsight-etl-architecture.png)

Hadoop används vanligtvis i ETL-rutiner som importerar antingen ett stort antal textfiler (till exempel CSV) eller en mindre, men ofta ändras antalet textfiler, eller båda.  Hive är ett bra verktyg du använder för att förbereda data innan du läser in den i data destinationen.  Hive kan du skapa ett schema över CSV-filen och använder ett SQL-liknande språk för att generera MapReduce-program som samverkar med data. 

Vanliga stegen för att använda Hive för ETL är följande:

1. Läs in data i Azure Data Lake Store eller Azure Blob Storage.
2. Skapa en Metadata Store-databas (med Azure SQL Database) för användning av Hive i lagra dina scheman.
3. Skapa ett HDInsight-kluster och Anslut lagringen.
4. Definiera schemat ska gälla för närvarande Läs data i datalagret:

    ```
    DROP TABLE IF EXISTS hvac;

    --create the hvac table on comma-separated sensor data stored in Azure Storage blobs
    
    CREATE EXTERNAL TABLE hvac(`date` STRING, time STRING, targettemp BIGINT,
        actualtemp BIGINT, 
        system BIGINT, 
        systemage BIGINT, 
        buildingid BIGINT)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
    STORED AS TEXTFILE LOCATION 'wasb://{container}@{storageaccount}.blob.core.windows.net/HdiSamples/SensorSampleData/hvac/';
    ```

5. Transformera data och läsa in det i målet.  Det finns flera sätt att använda Hive under omvandling och inläsning:

    * Fråga och förbereda data med Hive och spara den som en CSV-fil i Azure Data Lake Store eller Azure-blobblagring.  Sedan använda ett verktyg som SQL Server Integration Services (SSIS) att hämta de CSV: er och läsa in data i en relationsdatabas för mål, till exempel SQL Server.
    * Fråga data direkt från Excel- eller C# med hjälp av ODBC-drivrutinen.
    * Använd [Apache Sqoop](apache-hadoop-use-sqoop-mac-linux.md) läsa förberedda flat CSV-filer och läsa in dem i relationella måldatabasen.

## <a name="data-sources"></a>Datakällor

Datakällor är vanligtvis externa data som kan anpassas till befintliga data i datalager, till exempel:

* Sociala mediedata, loggfiler, sensorer och program som skapar datafiler.
* Datauppsättningar som hämtas från dataleverantörer, till exempel väder statistik eller leverantören försäljningssiffrorna.
* Strömmande data avbildas, filtreras och bearbetas via ett lämpligt verktyg eller framework.

<!-- TODO: (see Collecting and loading data into HDInsight). -->

## <a name="output-targets"></a>Mål för utdata

Du kan använda Hive för att överföra data till en mängd olika mål inklusive:

* En relationsdatabas, till exempel SQL Server eller Azure SQL Database.
* Datalager, till exempel Azure SQL Data Warehouse.
* Excel.
* Azure table och blob-lagring.
* Program eller tjänster som kräver att data som ska bearbetas till specifika format eller som filer som innehåller vissa typer av information.
* Lagra en JSON-dokumentet som <a href="https://azure.microsoft.com/services/cosmos-db/">CosmosDB</a>.

## <a name="considerations"></a>Överväganden

ETL-modellen används vanligtvis när du vill:

* Läs in dataströmmen data eller stora volymer av delvis strukturerade eller Ostrukturerade data från externa källor i en befintlig databas eller informationssystem.
* Rensa, transformera, och verifiera data innan du läser in den, kanske genom att använda mer än en omvandling passera klustret.
* Skapa rapporter och visualiseringar som uppdateras regelbundet.  Om rapporten tar för lång tid att generera under dagen, kan du schemalägga rapporten ska köras över natten.  Du kan använda Azure Schemaläggaren och PowerShell för att automatiskt köra en Hive-fråga.

Om målet för data som inte är en databas, kan du generera en fil i rätt format i frågan, till exempel en CSV-fil. Den här filen kan sedan importeras till Excel eller Power BI.

Om du behöver utföra flera åtgärder på data som en del av ETL-processen kan du överväga hur du hanterar. Om åtgärderna styrs av ett externt program måste snarare än som ett arbetsflöde inne i lösningen att besluta om vissa åtgärder kan utföras parallellt och identifiera när varje jobbet har slutförts. Kan vara enklare än om du försöker att samordna en sekvens med åtgärder med hjälp av externa skript eller anpassade program med hjälp av en mekanism för arbetsflödet, till exempel Oozie inom Hadoop. Läs mer om Oozie [arbetsflödes- och orchestration](https://msdn.microsoft.com/library/dn749829.aspx).

<!-- ## Next steps -->
<!-- * [ETL at scale](../hdinsight-etl-at-scale.md): Learn more about performing ETL at scale. -->
<!-- * [Operationalize Data Pipelines with Oozie](hdinsight-operationalize-data-pipeline.md): Learn how to build a data pipeline that uses Hive to summarize CSV flight delay data, stage the prepared data in Azure Storage blobs, and then use Sqoop to load the summarized data into Azure SQL Database. -->
<!-- * [ETL Deep Dive](../hdinsight-etl-deep-dive.md): Walk through an end-to-end ETL pipeline.  -->
