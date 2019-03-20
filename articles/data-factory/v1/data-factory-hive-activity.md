---
title: Transformera data med Hive-aktivitet – Azure | Microsoft Docs
description: Lär dig hur du kan använda Hive-aktivitet i Azure data factory för att köra Hive-frågor på ett på-begäran/your own HDInsight-kluster.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 80083218-743e-4da8-bdd2-60d1c77b1227
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 4b622a5925aebd140fed2ac74eaf7cc186803b90
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58113759"
---
# <a name="transform-data-using-hive-activity-in-azure-data-factory"></a>Transformera data med Hive-aktivitet i Azure Data Factory 
> [!div class="op_single_selector" title1="Transformation Activities"]
> * [Hive-aktivitet](data-factory-hive-activity.md) 
> * [Piggningsåtgärd](data-factory-pig-activity.md)
> * [MapReduce-aktivitet](data-factory-map-reduce.md)
> * [Hadoop Streaming Activity](data-factory-hadoop-streaming-activity.md)
> * [Spark-aktivitet](data-factory-spark.md)
> * [Machine Learning Batch-körningsaktivitet](data-factory-azure-ml-batch-execution-activity.md)
> * [Machine Learning-uppdateringsresursaktivitet](data-factory-azure-ml-update-resource-activity.md)
> * [Lagrad proceduraktivitet](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL-aktivitet](data-factory-usql-activity.md)
> * [.NET-anpassad aktivitet](data-factory-use-custom-activities.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av Data Factory-tjänsten finns i [Transformera data med Hive-aktivitet i Data Factory](../transform-data-using-hadoop-hive.md).

HDInsight Hive-aktivitet i en Datafabrik [pipeline](data-factory-create-pipelines.md) kör Hive-frågor på [egna](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) eller [på begäran](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) Windows/Linux-baserat HDInsight-kluster. Den här artikeln bygger vidare på den [datatransformeringsaktiviteter](data-factory-data-transformation-activities.md) artikel som anger en allmän översikt över Dataomvandling och stöds transformeringsaktiviteter.

> [!NOTE] 
> Om du är nybörjare på Azure Data Factory, Läs igenom [introduktion till Azure Data Factory](data-factory-introduction.md) och igenom självstudien: [Skapa din första datapipeline](data-factory-build-your-first-pipeline.md) innan du läser den här artikeln. 

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
| namn |Namn på aktiviteten |Ja |
| beskrivning |Text som beskriver vad aktiviteten används till |Nej |
| typ |HDinsightHive |Ja |
| inmatningar |Indata som används av Hive-aktivitet |Nej |
| utdata |Utdata som produceras av Hive-aktivitet |Ja |
| linkedServiceName |Referens till HDInsight-kluster som är registrerad som en länkad tjänst i Data Factory |Ja |
| skript |Ange infogat för Hive-skript |Nej |
| skriptets sökväg |Store Hive-skriptet i Azure blob storage och ange sökvägen till filen. Använd ”skript” eller ”scriptPath-egenskapen. Båda kan inte användas tillsammans. Filnamnet är skiftlägeskänsligt. |Nej |
| definierar |Ange parametrar som nyckel/värde-par för refererar till Hive-skript med hjälp av ”hiveconf” |Nej |

## <a name="example"></a>Exempel
Anta att du har ett exempel på spel loggar analytics där du vill identifiera den tid som krävs av användare som spelar spel som startas av ditt företag. 

Följande loggen är en game exempellogg, kommatecken (`,`) avgränsade och innehåller följande fält – profil-ID, SessionStart, varaktighet, SrcIPAddress och GameType.

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

Du behöver göra följande för att köra den här Hive-skript i en Data Factory-pipeline

1. Skapa en länkad tjänst för att registrera [ditt eget HDInsight-kluster för beräkningar](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) eller konfigurera [på begäran HDInsight-kluster](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Vi kan kalla den här länkade tjänsten ”HDInsightLinkedService”.
2. Skapa en [länkad tjänst](data-factory-azure-blob-connector.md) att konfigurera anslutningen till Azure Blob storage som är värd för data. Vi kan kalla den här länkade tjänsten ”StorageLinkedService”
3. Skapa [datauppsättningar](data-factory-create-datasets.md) som pekar på indata och utdata. Vi kan kalla den inkommande datauppsättningen ”HiveSampleIn” och datamängd för utdata ”HiveSampleOut”
4. Kopiera Hive-frågan som en fil till Azure Blob Storage som är konfigurerade i steg #2. Om lagring för som är värd för data skiljer sig från den som är värd för den här frågefilen, skapa en separat länkad Azure Storage-tjänst och referera till det i aktiviteten. Använd **scriptPath** att ange sökvägen för att hive-fråga fil och **scriptLinkedService** att ange Azure-lagring som innehåller skriptfilen. 
   
   > [!NOTE]
   > Du kan också ange infogade för Hive-skript i aktivitetsdefinitionen med hjälp av den **skriptet** egenskapen. Vi rekommenderar inte den här metoden som alla specialtecken i ett skript i JSON-dokument måste undantas och kan orsaka problem för felsökning. Det bästa sättet är att följa steg #4.
   > 
   > 
5. Skapa en pipeline med HDInsightHive-aktiviteten. Aktiviteten processer/omvandlingar data.

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
6. Distribuera pipelinen. Se [skapa pipelines](data-factory-create-pipelines.md) nedan för information. 
7. Övervaka pipeline med hjälp av data factory övervakning och hantering av vyer. Se [övervakning och hantera Data Factory-pipelines](data-factory-monitor-manage-pipelines.md) nedan för information. 

## <a name="specifying-parameters-for-a-hive-script"></a>Ange parametrar för Hive-skript
I det här exemplet spel loggar är varje dag matas in i Azure Blob Storage och lagras i en mapp som är partitionerad med datum och tid. Du vill Parameterisera Hive-skriptet och skicka den inkommande mapplatsen dynamiskt under körning och även producerar utdata som är partitionerad med datum och tid.

Gör följande för att använda parametriserade Hive-skript

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
* Referera till en parameter med hjälp av Hive-skriptet **${hiveconf:parameterName}**. 
  
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
* [Piggningsåtgärd](data-factory-pig-activity.md)
* [MapReduce-aktivitet](data-factory-map-reduce.md)
* [Hadoop Streaming Activity](data-factory-hadoop-streaming-activity.md)
* [Anropa Spark-program](data-factory-spark.md)
* [Anropa R-skript](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)

