---
title: Transformera data med hjälp av Pig-aktiviteten i Azure Data Factory | Microsoft Docs
description: Lär dig hur du kan använda Pig-aktivitet i ett Azure data factory för att köra Pig-skript på en på-begäran/din egen HDInsight-kluster.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 5af07a1a-2087-455e-a67b-a79841b4ada5
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 5abd0b07037559b14158a3c314b6ca6ce30ab655
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37045124"
---
# <a name="transform-data-using-pig-activity-in-azure-data-factory"></a>Transformera data med hjälp av Pig-aktiviteten i Azure Data Factory
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
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av Data Factory-tjänsten finns [Transformera data med Pig aktivitet från Data Factory](../transform-data-using-hadoop-pig.md).


HDInsight Pig-aktiviteten i en Datafabrik [pipeline](data-factory-create-pipelines.md) Pig frågor körs på [egna](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) eller [på begäran](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) Windows/Linux-baserade HDInsight-kluster. Den här artikeln bygger på den [data transformation aktiviteter](data-factory-data-transformation-activities.md) artikel som presenterar en allmän översikt över data transformation och stöds omvandling aktiviteter.

> [!NOTE] 
> Om du har använt Azure Data Factory, Läs igenom [introduktion till Azure Data Factory](data-factory-introduction.md) och gör kursen: [skapa din första pipeline data](data-factory-build-your-first-pipeline.md) innan du läser den här artikeln. 

## <a name="syntax"></a>Syntax

```JSON
{
    "name": "HiveActivitySamplePipeline",
      "properties": {
    "activities": [
        {
            "name": "Pig Activity",
            "description": "description",
            "type": "HDInsightPig",
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
                  "script": "Pig script",
                  "scriptPath": "<pathtothePigscriptfileinAzureblobstorage>",
                  "defines": {
                    "param1": "param1Value"
                  }
            },
               "scheduler": {
                  "frequency": "Day",
                  "interval": 1
            }
          }
    ]
  }
}
```
## <a name="syntax-details"></a>Information om syntax
| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| namn |Namnet på aktiviteten |Ja |
| description |Text som beskriver aktiviteten är det som används för |Nej |
| typ |HDinsightPig |Ja |
| inmatningar |En eller flera inmatningar som används av Pig-aktiviteten |Nej |
| utdata |En eller flera av de utdata som produceras av aktiviteten Pig |Ja |
| linkedServiceName |Referens till HDInsight-kluster som är registrerat som en länkad tjänst i Data Factory |Ja |
| skriptet |Ange infogat för Pig-skript |Nej |
| sökvägen för skriptet |Lagra Pig-skriptet i en Azure blob storage och ange sökvägen till filen. Använd egenskapen 'script' eller 'scriptPath'. Båda kan inte användas tillsammans. Filnamnet är skiftlägeskänslig. |Nej |
| definierar |Ange parametrar som nyckel/värde-par för refererar till i Pig-skriptet |Nej |

## <a name="example"></a>Exempel
Nu ska vi titta ett exempel på spel loggar analytics där du vill identifiera den tid som användes av spelare spelar spel startas av ditt företag.

Följande exempel spel loggen är en fil åt med kommatecken (,). Den innehåller följande fält – profil-ID, SessionStart, varaktighet, SrcIPAddress och GameType.

```
1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
.....
```

Den **svin skriptet** att bearbeta dessa data:

```
PigSampleIn = LOAD 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/samplein/' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);

GroupProfile = Group PigSampleIn all;

PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);

Store PigSampleOut into 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/sampleoutpig/' USING PigStorage (',');
```

Om du vill köra Pig-skriptet i en Data Factory-pipelinen, gör du följande steg:

1. Skapa en länkad tjänst för att registrera [egna HDInsight-kluster för beräkningar](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) eller konfigurera [på begäran HDInsight beräkningskluster](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Vi ska anropa den här länkade tjänsten **HDInsightLinkedService**.
2. Skapa en [länkade tjänsten](data-factory-azure-blob-connector.md) att konfigurera anslutningen till Azure Blob storage med data. Vi ska anropa den här länkade tjänsten **StorageLinkedService**.
3. Skapa [datauppsättningar](data-factory-create-datasets.md) pekar på indata och utdata. Vi ringer inkommande datauppsättningen **PigSampleIn** och datamängd för utdata **PigSampleOut**.
4. Kopiera Pig-frågan i en fil i Azure Blob Storage som konfigurerats i steg #2. Om Azure-lagring som är värd för data skiljer sig från det som är värd för frågefilen, skapar du en separat länkad Azure Storage-tjänst. Referera till den länkade tjänsten i konfigurationen för aktiviteten. Använd ** scriptPath ** ange sökväg till skriptfilen för pig och **scriptLinkedService**. 
   
   > [!NOTE]
   > Du kan också tillhandahålla Pig-skriptet infogad i aktivitetsdefinitionen med hjälp av den **skriptet** egenskapen. Men vi rekommenderar inte den här metoden som alla specialtecken i skriptet behöver hoppas och kan orsaka problem för felsökning. Det bästa sättet är att följa steg #4.
   > 
   > 
5. Skapa pipeline med aktiviteten HDInsightPig. Den här aktiviteten bearbetar inkommande data genom att köra Pig-skriptet på HDInsight-kluster.

    ```JSON   
    {
      "name": "PigActivitySamplePipeline",
      "properties": {
        "activities": [
          {
            "name": "PigActivitySample",
            "type": "HDInsightPig",
            "inputs": [
              {
                "name": "PigSampleIn"
              }
            ],
            "outputs": [
              {
                "name": "PigSampleOut"
              }
            ],
            "linkedServiceName": "HDInsightLinkedService",
            "typeproperties": {
              "scriptPath": "adfwalkthrough\\scripts\\enrichlogs.pig",
              "scriptLinkedService": "StorageLinkedService"
            },
               "scheduler": {
                  "frequency": "Day",
                  "interval": 1
            }
          }
        ]
      }
    } 
    ```
6. Distribuera sedan pipelinen. Se [skapar pipelines](data-factory-create-pipelines.md) artikeln för information. 
7. Övervaka pipeline med hjälp av data factory övervakning och hantering av vyer. Se [övervakning och hantera Data Factory pipelines](data-factory-monitor-manage-pipelines.md) artikeln för information.

## <a name="specifying-parameters-for-a-pig-script"></a>Ange parametrar för Pig-skriptet
Exempel: spel loggar inhämtas dagligen i Azure Blob Storage och lagras i en mapp partitionerade utifrån datum och tid. Vill du parameterstyra Pig-skriptet och skicka inkommande mappen dynamiskt under körning och även producerar utdata partitionerad med datum och tid.

Gör följande om du vill använda parametriserade Pig-skriptet:

* Definiera parametrar i **definierar**.

    ```JSON  
    {
        "name": "PigActivitySamplePipeline",
          "properties": {
        "activities": [
            {
                "name": "PigActivitySample",
                "type": "HDInsightPig",
                "inputs": [
                      {
                        "name": "PigSampleIn"
                      }
                ],
                "outputs": [
                      {
                        "name": "PigSampleOut"
                      }
                ],
                "linkedServiceName": "HDInsightLinkedService",
                "typeproperties": {
                      "scriptPath": "adfwalkthrough\\scripts\\samplepig.hql",
                      "scriptLinkedService": "StorageLinkedService",
                      "defines": {
                        "Input": "$$Text.Format('wasb: //adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0: yyyy}/monthno={0:MM}/dayno={0: dd}/',SliceStart)",
                        "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)"
                      }
                },
                   "scheduler": {
                      "frequency": "Day",
                      "interval": 1
                }
              }
        ]
      }
    }
    ```  
* I Pig-skriptet avser parametrar med '**$parameterName**' som visas i följande exempel:

    ```  
    PigSampleIn = LOAD '$Input' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);    
    GroupProfile = Group PigSampleIn all;        
    PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);        
    Store PigSampleOut into '$Output' USING PigStorage (','); 
    ```
## <a name="see-also"></a>Se även
* [Hive-aktivitet](data-factory-hive-activity.md)
* [MapReduce Activity](data-factory-map-reduce.md)
* [Hadoop Streaming Activity](data-factory-hadoop-streaming-activity.md)
* [Anropa Spark-program](data-factory-spark.md)
* [Anropa R-skript](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)

