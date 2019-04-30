---
title: Med Apache Hive som ett ETL-verktyg – Azure HDInsight
description: Använda Apache Hive gällande extrahering, transformering och inläsning (ETL) data i Azure HDInsight.
ms.service: hdinsight
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/14/2017
ms.openlocfilehash: e21f3eed7e631c974d34f148b85843b055deaf60
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62123973"
---
# <a name="use-apache-hive-as-an-extract-transform-and-load-etl-tool"></a>Använda Apache Hive som ett verktyg för extrahering, transformering och inläsning (ETL)

Vanligtvis måste du rensa och transformera inkommande data innan du läser in dem i ett mål som är lämplig för analys. Åtgärder för extrahering, transformering och läsa in (ETL) används för att förbereda data och läsa in den i ett datamål.  Apache Hive på HDInsight kan läsa in Ostrukturerade data, bearbeta data efter behov och sedan läsa in data i en relationsinformationslager för beslut support system. Den här metoden kan data extraheras från källan och lagras i skalbar lagring, till exempel Azure Storage-blobbar eller Azure Data Lake Storage. Data sedan omvandlas med hjälp av en sekvens av Hive-frågor och slutligen mellanlagras i Hive som förberedelse för inläsning av i måldatalagret.

## <a name="use-case-and-model-overview"></a>Använda användningsfall och modeller: översikt

Följande bild visar en översikt över användningsfall och modell för ETL-automatisering. Indata omvandlas för att generera rätt utdata.  Under omvandlingen, kan data ändra form, datatyp och även språk.  ETL-processer kan konvertera Imperial till mått, ändra tidszoner och förbättra precision för att korrekt anpassas till befintliga data i målet.  ETL-processer kan också kombinera nya data med befintliga data att hålla reporting uppdaterade eller ger ytterligare information om befintliga data.  Program, till exempel reporting verktyg och tjänster kan sedan använda dessa data i det önskade formatet.

![Apache Hive som ETL](./media/apache-hadoop-using-apache-hive-as-an-etl-tool/hdinsight-etl-architecture.png)

Hadoop är vanligt i ETL-processer som importerar antingen ett stort antal textfiler (till exempel CSV: er) eller en mindre men ofta ändra antalet textfiler, eller båda.  Hive är ett bra verktyg du använder för att förbereda data innan du läser in dem i datamålet.  Hive kan du skapa ett schema över CSV-filen och använda ett SQL-liknande språk för att generera MapReduce-program som interagerar med data. 

Vanliga stegen för att använda Hive för att utföra ETL är följande:

1. Läs in data i Azure Data Lake Storage eller Azure Blob Storage.
2. Skapa en Metadata Store-databas (med Azure SQL Database) för användning av Hive vid lagring av dina scheman.
3. Skapa ett HDInsight-kluster och Anslut datalagret.
4. Definiera scheman som ska tillämpas på Läs tid över data i datalagret:

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

5. Omvandla data och läser in dem till målet.  Det finns flera sätt att använda Hive under transformering och inläsning:

    * Fråga och förbereda data med Hive och spara den som en CSV-fil i Azure Data Lake Storage eller Azure blob storage.  Sedan använda ett verktyg som SQL Server Integration Services (SSIS) att hämta de CSV: er och läsa in data i en relationsdatabas för mål, till exempel SQL Server.
    * Fråga data direkt från Excel- eller C# med hjälp av Hive ODBC-drivrutinen.
    * Använd [Apache Sqoop](apache-hadoop-use-sqoop-mac-linux.md) att läsa förberedd flat CSV-filer och läsa in dem i mål-relationsdatabas.

## <a name="data-sources"></a>Datakällor

Datakällor är vanligtvis externa data som kan matchas med befintliga data i ditt datalager, till exempel:

* Sociala data, loggfiler, sensorer och program som generar datafiler.
* Datauppsättningar som hämtas från-dataleverantörer, till exempel väder statistik eller leverantören försäljningssiffrorna.
* Strömmande data hämtas, filtreras och bearbetats med ett lämpligt verktyg eller ramverk.

<!-- TODO: (see Collecting and loading data into HDInsight). -->

## <a name="output-targets"></a>Mål för utdata

Du kan använda Hive för att mata ut data till en mängd olika mål inklusive:

* En relationsdatabas, till exempel SQL Server eller Azure SQL Database.
* Ett datalager, till exempel Azure SQL Data Warehouse.
* Excel.
* Azure table- och blob storage.
* Program eller tjänster som kräver data som ska bearbetas till specifika format eller som filer som innehåller vissa typer av information.
* Som en JSON-dokument Store <a href="https://azure.microsoft.com/services/cosmos-db/">CosmosDB</a>.

## <a name="considerations"></a>Överväganden

ETL-modellen används vanligtvis när du vill:

* Läs in dataströmmen eller stora mängder delvis strukturerade eller Ostrukturerade data från externa källor i en befintlig databas eller informationssystem.
* Rensa, omvandla och validera data innan de läses in, kanske genom att använda mer än en omvandling passera klustret.
* Skapa rapporter och visualiseringar som uppdateras regelbundet.  Om rapporten tar för lång tid att generera under dagen kan kan du schemalägga rapporten ska köras på natten.  Du kan använda Azure Scheduler och PowerShell för att automatiskt köra en Hive-fråga.

Om målet för data som inte är en databas kan du generera en fil i lämpligt format i fråga, till exempel en CSV-fil. Den här filen kan sedan importeras till Excel eller Power BI.

Om du behöver utföra flera åtgärder på data som en del av ETL-processen kan du överväga hur du hanterar. Om åtgärderna styrs av ett externt program, måste snarare än som ett arbetsflöde inne i lösningen avgöra om vissa åtgärder kan utföras parallellt och att upptäcka när varje jobbet har slutförts. Använda en mekanism för arbetsflödet, till exempel Oozie inom Hadoop kan vara enklare än att försöka dirigera en sekvens med åtgärder med externa skript eller anpassade program. Läs mer om Oozie [arbetsflöde och jobbet orchestration](https://msdn.microsoft.com/library/dn749829.aspx).

## <a name="next-steps"></a>Nästa steg

* [ETL i stor skala](apache-hadoop-etl-at-scale.md)
* [Operationalisera en datapipeline](../hdinsight-operationalize-data-pipeline.md)

<!-- * [ETL Deep Dive](../hdinsight-etl-deep-dive.md) -->
