---
title: Använda Apache Hive som ETL-verktyg – Azure HDInsight
description: Använd Apache Hive för att extrahera, transformera och läsa in (ETL) data i Azure HDInsight.
ms.service: hdinsight
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/22/2019
ms.openlocfilehash: be331f36a6305b05ce83a2b2d5fdfb73a154ce3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77623121"
---
# <a name="use-apache-hive-as-an-extract-transform-and-load-etl-tool"></a>Använda Apache Hive som etl-verktyg (Extract, Transform, and Load)

Du behöver vanligtvis rensa och omvandla inkommande data innan du läser in dem till en destination som lämpar sig för analys. Extrahera, transformera och läsa in (ETL) används för att förbereda data och läsa in dem till ett datamål.  Apache Hive på HDInsight kan läsa i ostrukturerade data, bearbeta data efter behov och sedan läsa in data i ett relationsdatalager för beslutsstödsystem. I den här metoden extraheras data från källan och lagras i skalbar lagring, till exempel Azure Storage-blobbar eller Azure Data Lake Storage. Data omvandlas sedan med hjälp av en sekvens av Hive-frågor och är slutligen iscensatta i Hive som förberedelse för massinläsning i måldatalagret.

## <a name="use-case-and-model-overview"></a>Översikt över användningsfall och modell

Följande bild visar en översikt över användningsfall och modell för ETL automation. Indata omvandlas för att generera rätt utdata.  Under den omvandlingen kan data ändra form, datatyp och till och med språk.  ETL-processer kan konvertera Imperial till mått, ändra tidszoner och förbättra precisionen så att den stämmer överens med befintliga data i målet.  ETL-processer kan också kombinera nya data med befintliga data för att hålla rapporteringen uppdaterad eller för att ge ytterligare insikt i befintliga data.  Program som rapporteringsverktyg och -tjänster kan sedan använda dessa data i önskat format.

![Apache Hive som ETL-arkitektur](./media/apache-hadoop-using-apache-hive-as-an-etl-tool/hdinsight-etl-architecture.png)

Hadoop används vanligtvis i ETL-processer som importerar antingen ett stort antal textfiler (som CSV: er) eller en mindre men ofta ändra antal textfiler, eller båda.  Hive är ett bra verktyg att använda för att förbereda data innan du läser in dem i datamålet.  Hive kan du skapa ett schema över CSV och använda ett SQL-liknande språk för att generera MapReduce program som interagerar med data.

De typiska stegen för att använda Hive för att utföra ETL är följande:

1. Läs in data i Azure Data Lake Storage eller Azure Blob Storage.
2. Skapa en metadatalagringsdatabas (med Azure SQL Database) för användning av Hive när du lagrar dina scheman.
3. Skapa ett HDInsight-kluster och anslut datalagret.
4. Definiera schemat som ska tillämpas vid lästid över data i datalagret:

    ```
    DROP TABLE IF EXISTS hvac;

    --create the hvac table on comma-separated sensor data stored in Azure Storage blobs

    CREATE EXTERNAL TABLE hvac(`date` STRING, time STRING, targettemp BIGINT,
        actualtemp BIGINT,
        system BIGINT,
        systemage BIGINT,
        buildingid BIGINT)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
    STORED AS TEXTFILE LOCATION 'wasbs://{container}@{storageaccount}.blob.core.windows.net/HdiSamples/SensorSampleData/hvac/';
    ```

5. Omvandla data och ladda in dem till målet.  Det finns flera sätt att använda Hive under omvandlingen och inläsningen:

    * Fråga och förbereda data med Hive och spara dem som en CSV i Azure Data Lake Storage eller Azure blob storage.  Använd sedan ett verktyg som SQL Server Integration Services (SSIS) för att hämta dessa CSV:er och läsa in data i en målrelationsdatabas som SQL Server.
    * Fråga data direkt från Excel eller C# med hive ODBC-drivrutinen.
    * Använd [Apache Sqoop](apache-hadoop-use-sqoop-mac-linux.md) för att läsa de förberedda platta CSV-filerna och läsa in dem i målrelationsdatabasen.

## <a name="data-sources"></a>Datakällor

Datakällor är vanligtvis externa data som kan matchas med befintliga data i ditt datalager, till exempel:

* Sociala medier data, loggfiler, sensorer och program som genererar datafiler.
* Datauppsättningar som erhållits från dataleverantörer, till exempel väderstatistik eller leverantörsförsäljningsnummer.
* Strömmande data som samlas in, filtreras och bearbetas via ett lämpligt verktyg eller ramverk.

<!-- TODO: (see Collecting and loading data into HDInsight). -->

## <a name="output-targets"></a>Utgående mål

Du kan använda Hive för att mata ut data till en mängd olika mål, inklusive:

* En relationsdatabas, till exempel SQL Server eller Azure SQL Database.
* Ett informationslager, till exempel Azure SQL Data Warehouse.
* Excel.
* Azure-tabell- och blob-lagring.
* Program eller tjänster som kräver att data bearbetas i specifika format, eller som filer som innehåller specifika typer av informationsstruktur.
* En JSON Document Store som [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

## <a name="considerations"></a>Överväganden

ETL-modellen används vanligtvis när du vill:

* Läs in data eller stora volymer halvstrukturerade eller ostrukturerade data från externa källor i en befintlig databas eller ett befintligt informationssystem.
* Rensa, transformera och validera data innan du läser in dem, kanske genom att använda mer än en omvandling som passerar genom klustret.
* Generera rapporter och visualiseringar som uppdateras regelbundet. Om rapporten till exempel tar för lång tid att generera under dagen kan du schemalägga rapporten så att den körs nattetid. Om du vill köra en Hive-fråga automatiskt kan du använda [Azure Logic Apps](../../logic-apps/logic-apps-overview.md) och PowerShell.

Om målet för data inte är en databas kan du generera en fil i rätt format i frågan, till exempel en CSV. Den här filen kan sedan importeras till Excel eller Power BI.

Om du behöver utföra flera åtgärder på data som en del av ETL-processen bör du överväga hur du hanterar dem. Om åtgärderna styrs av ett externt program, i stället för som ett arbetsflöde i lösningen, måste du bestämma om vissa åtgärder kan utföras parallellt och identifiera när varje jobb slutförs. Det kan vara enklare att använda en arbetsflödesmekanism som Oozie i Hadoop än att försöka dirigera en sekvens av åtgärder med externa skript eller anpassade program. Mer information om Oozie finns i [Arbetsflöde och jobborkestrering](https://msdn.microsoft.com/library/dn749829.aspx).

## <a name="next-steps"></a>Nästa steg

* [ETL i stor skala](apache-hadoop-etl-at-scale.md)
* [Operationalisera en datapipeline](../hdinsight-operationalize-data-pipeline.md)

<!-- * [ETL Deep Dive](../hdinsight-etl-deep-dive.md) -->
