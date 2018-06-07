---
title: Transformera data med hjälp av Hive aktivitet – Azure | Microsoft Docs
description: Lär dig hur du kan använda Hive-aktivitet i ett Azure data factory för att köra Hive-frågor på en på-begäran/din egen HDInsight-kluster.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 80083218-743e-4da8-bdd2-60d1c77b1227
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 6826d87e5613ce4892e9fd839c66ec26282ad188
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34622133"
---
# <a name="transform-data-using-hive-activity-in-azure-data-factory"></a>Transformera data med hjälp av Hive aktivitet i Azure Data Factory 
> [!div class="op_single_selector" title1="Transformation Activities"]
> * [Hive-aktivitet](data-factory-hive-activity.md) 
> * [Pig-aktivitet](data-factory-pig-activity.md)
> * [MapReduce Activity](data-factory-map-reduce.md)
> * [Hadoop Streaming Activity](data-factory-hadoop-streaming-activity.md)
> * [Spark-aktivitet](data-factory-spark.md)
> * [Machine Learning Batch-körningsaktivitet](data-factory-azure-ml-batch-execution-activity.md)
> * [Machine Learning-uppdateringsresursaktivitet](data-factory-azure-ml-update-resource-activity.md)
> * [Lagrad proceduraktivitet](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL-aktivitet](data-factory-usql-activity.md)
> * [Anpassad aktivitet för .NET](data-factory-use-custom-activities.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory, som är allmänt tillgänglig (GA). Om du använder version 2 av Data Factory-tjänsten, som finns i förhandsgranskningen, se [Transformera data med Hive aktivitet från Data Factory version 2](../transform-data-using-hadoop-hive.md).

HDInsight Hive-aktivitet i en Datafabrik [pipeline](data-factory-create-pipelines.md) kör Hive-frågor på [egna](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) eller [på begäran](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) Windows/Linux-baserade HDInsight-kluster. Den här artikeln bygger på den [data transformation aktiviteter](data-factory-data-transformation-activities.md) artikel som presenterar en allmän översikt över data transformation och stöds omvandling aktiviteter.

> [!NOTE] 
> Om du har använt Azure Data Factory, Läs igenom [introduktion till Azure Data Factory](data-factory-introduction.md) och gör kursen: [skapa din första pipeline data](data-factory-build-your-first-pipeline.md) innan du läser den här artikeln. 

## <a name="syntax"></a>Syntax

```JSON
{
    "name": "Hive Activity",
    "description": "description",
    "type": "HDInsightHive",
    "inputs": [
      {
        "name": "input tables"
      }
    ],
    "outputs": [
      {
        "name": "output tables"
      }
    ],
    "linkedServiceName": "MyHDInsightLinkedService",
    "typeProperties": {
      "script": "Hive script",
      "scriptPath": "<pathtotheHivescriptfileinAzureblobstorage>",
      "defines": {
        "param1": "param1Value"
      }
    },
   "scheduler": {
      "frequency": "Day",
      "interval": 1
    }
}
```
## <a name="syntax-details"></a>Information om syntax
| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| namn |Namnet på aktiviteten |Ja |
| description |Text som beskriver aktiviteten är det som används för |Nej |
| typ |HDinsightHive |Ja |
| inmatningar |Indata som används av Hive-aktiviteten |Nej |
| utdata |Utdata som produceras av aktiviteten Hive |Ja |
| linkedServiceName |Referens till HDInsight-kluster som är registrerat som en länkad tjänst i Data Factory |Ja |
| Skriptet |Ange infogat för Hive-skript |Nej |
| sökvägen för skriptet |Lagra Hive-skript i ett Azure blob storage och ange sökvägen till filen. Använd egenskapen 'script' eller 'scriptPath'. Båda kan inte användas tillsammans. Filnamnet är skiftlägeskänslig. |Nej |
| definierar |Ange parametrar som nyckel/värde-par för refererar till i Hive-skript med hjälp av 'hiveconf' |Nej |

## <a name="example"></a>Exempel
Nu ska vi titta ett exempel på spel loggar analytics där du vill identifiera den tid som krävs av användare spelar spel startas av ditt företag. 

Följande loggen är en spel exempellogg, kommatecken (`,`) avgränsade och innehåller följande fält – profil-ID, SessionStart, varaktighet, SrcIPAddress och GameType.

```
1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
.....
```

Den **registreringsdatafilen** att bearbeta dessa data:

```
DROP TABLE IF EXISTS HiveSampleIn; 
CREATE EXTERNAL TABLE HiveSampleIn 
(
    ProfileID        string, 
    SessionStart     string, 
    Duration         int, 
    SrcIPAddress     string, 
    GameType         string
) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION 'wasb://adfwalkthrough@<storageaccount>.blob.core.windows.net/samplein/'; 

DROP TABLE IF EXISTS HiveSampleOut; 
CREATE EXTERNAL TABLE HiveSampleOut 
(    
    ProfileID     string, 
    Duration     int
) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION 'wasb://adfwalkthrough@<storageaccount>.blob.core.windows.net/sampleout/';

INSERT OVERWRITE TABLE HiveSampleOut
Select 
    ProfileID,
    SUM(Duration)
FROM HiveSampleIn Group by ProfileID
```

Om du vill köra Hive-skript i Data Factory-pipelinen, måste du göra följande

1. Skapa en länkad tjänst för att registrera [egna HDInsight-kluster för beräkningar](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) eller konfigurera [på begäran HDInsight beräkningskluster](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Vi ska anropa den här länkade tjänsten ”HDInsightLinkedService”.
2. Skapa en [länkade tjänsten](data-factory-azure-blob-connector.md) att konfigurera anslutningen till Azure Blob storage med data. Vi ska anropa den här länkade tjänsten ”StorageLinkedService”
3. Skapa [datauppsättningar](data-factory-create-datasets.md) pekar på indata och utdata. Vi ringer inkommande datauppsättningen ”HiveSampleIn” och datamängd för utdata ”HiveSampleOut”
4. Kopiera Hive-fråga som en fil till Azure Blob Storage konfigurerade i steg #2. Om lagringsutrymme som värd för data skiljer sig från den som värd för den här frågefilen, skapa en separat länkad Azure Storage-tjänst och referera till det i aktiviteten. Använd **scriptPath** att ange sökvägen för att hive frågefilen och **scriptLinkedService** ange Azure-lagring som innehåller skriptfilen. 
   
   > [!NOTE]
   > Du kan också tillhandahålla Hive-skript infogad i aktivitetsdefinitionen med hjälp av den **skriptet** egenskapen. Vi rekommenderar inte den här metoden som alla specialtecken i ett skript i JSON-dokument måste hoppas och kan orsaka problem för felsökning. Det bästa sättet är att följa steg #4.
   > 
   > 
5. Skapa en pipeline med aktiviteten HDInsightHive. Aktiviteten processer/transformeringar data.

    ```JSON   
    {   
        "name": "HiveActivitySamplePipeline",
        "properties": {
        "activities": [
            {
                "name": "HiveActivitySample",
                "type": "HDInsightHive",
                "inputs": [
                {
                    "name": "HiveSampleIn"
                }
                ],
                "outputs": [
                {
                    "name": "HiveSampleOut"
                }
                ],
                "linkedServiceName": "HDInsightLinkedService",
                "typeproperties": {
                    "scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
                    "scriptLinkedService": "StorageLinkedService"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                }
            }
            ]
        }
    }
    ```
6. Distribuera sedan pipelinen. Se [skapar pipelines](data-factory-create-pipelines.md) artikeln för information. 
7. Övervaka pipeline med hjälp av data factory övervakning och hantering av vyer. Se [övervakning och hantera Data Factory pipelines](data-factory-monitor-manage-pipelines.md) artikeln för information. 

## <a name="specifying-parameters-for-a-hive-script"></a>Ange parametrar för en Hive-skript
I det här exemplet spel loggar är inhämtas dagligen i Azure Blob Storage och lagras i en mapp som är partitionerad med datum och tid. Vill du parameterstyra Hive-skript och skicka inkommande mappen dynamiskt under körning och även producerar utdata partitionerad med datum och tid.

Gör följande för att använda parametrar Hive-skript

* Definiera parametrar i **definierar**.

    ```JSON  
    {
        "name": "HiveActivitySamplePipeline",
          "properties": {
        "activities": [
             {
                "name": "HiveActivitySample",
                "type": "HDInsightHive",
                "inputs": [
                      {
                        "name": "HiveSampleIn"
                      }
                ],
                "outputs": [
                      {
                        "name": "HiveSampleOut"
                    }
                ],
                "linkedServiceName": "HDInsightLinkedService",
                "typeproperties": {
                      "scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
                      "scriptLinkedService": "StorageLinkedService",
                      "defines": {
                        "Input": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)",
                        "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)"
                      },
                       "scheduler": {
                          "frequency": "Hour",
                          "interval": 1
                    }
                }
              }
        ]
      }
    }
    ```
* I Hive-skript, referera till en parameter med hjälp av **${hiveconf:parameterName}**. 
  
    ```
    DROP TABLE IF EXISTS HiveSampleIn; 
    CREATE EXTERNAL TABLE HiveSampleIn 
    (
        ProfileID     string, 
        SessionStart     string, 
        Duration     int, 
        SrcIPAddress     string, 
        GameType     string
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:Input}'; 

    DROP TABLE IF EXISTS HiveSampleOut; 
    CREATE EXTERNAL TABLE HiveSampleOut 
    (
        ProfileID     string, 
        Duration     int
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:Output}';

    INSERT OVERWRITE TABLE HiveSampleOut
    Select 
        ProfileID,
        SUM(Duration)
    FROM HiveSampleIn Group by ProfileID
    ```
## <a name="see-also"></a>Se även
* [Pig-aktivitet](data-factory-pig-activity.md)
* [MapReduce Activity](data-factory-map-reduce.md)
* [Hadoop Streaming Activity](data-factory-hadoop-streaming-activity.md)
* [Anropa Spark-program](data-factory-spark.md)
* [Anropa R-skript](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)

