---
title: Omvandla data med grisaktivitet i Azure Data Factory
description: Lär dig hur du kan använda Grisaktiviteten i en Azure-datafabrik för att köra Pig-skript på ett on-demand/ditt eget HDInsight-kluster.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.assetid: 5af07a1a-2087-455e-a67b-a79841b4ada5
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 5b8e7201a6239ef1fe83fb89d4b361995e305bbf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74703200"
---
# <a name="transform-data-using-pig-activity-in-azure-data-factory"></a>Omvandla data med grisaktivitet i Azure Data Factory
> [!div class="op_single_selector" title1="Omvandlingsaktiviteter"]
> * [Hive-aktivitet](data-factory-hive-activity.md) 
> * [Grisaktivitet](data-factory-pig-activity.md)
> * [MapReduce-aktivitet](data-factory-map-reduce.md)
> * [Hadoop streaming aktivitet](data-factory-hadoop-streaming-activity.md)
> * [Spark-aktivitet](data-factory-spark.md)
> * [Machine Learning Batch-körningsaktivitet](data-factory-azure-ml-batch-execution-activity.md)
> * [Machine Learning-uppdateringsresursaktivitet](data-factory-azure-ml-update-resource-activity.md)
> * [Lagrad proceduraktivitet](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL-aktivitet](data-factory-usql-activity.md)
> * [.NET anpassad aktivitet](data-factory-use-custom-activities.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av datafabrikstjänsten läser du [transformera data med Grisaktivitet i Data Factory](../transform-data-using-hadoop-pig.md).


HDInsight [Pig-aktiviteten](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) i en Pipeline för [datafabrik](data-factory-create-pipelines.md) kör Grisfrågor på [egen hand](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) eller på begäran Windows/Linux-baserade HDInsight-kluster. Den här artikeln bygger på artikeln [om dataomvandlingsaktiviteter,](data-factory-data-transformation-activities.md) som ger en allmän översikt över dataomvandling och de omvandlingsaktiviteter som stöds.

> [!NOTE] 
> Om du inte har gjort det tidigare i Azure Data Factory läser du [in introduktion till Azure Data Factory](data-factory-introduction.md) och gör självstudien: Skapa din första [datapipeline](data-factory-build-your-first-pipeline.md) innan du läser den här artikeln. 

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

## <a name="syntax-details"></a>Syntaxinformation

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| namn |Aktivitetens namn |Ja |
| description |Text som beskriver vad aktiviteten används för |Inga |
| typ |HDinsightPig |Ja |
| Ingångar |En eller flera ingångar som förbrukas av grisaktiviteten |Inga |
| Utgångar |En eller flera resultat som produceras av grisverksamheten |Ja |
| linkedServiceName |Referens till HDInsight-klustret som registrerats som en länkad tjänst i Data Factory |Ja |
| -skriptet |Ange inline för Pig-skript |Inga |
| scriptPath |Lagra Pig-skriptet i en Azure blob-lagring och ange sökvägen till filen. Använd egenskapen script eller 'scriptPath'. Båda kan inte användas tillsammans. Filnamnet är skiftlägeskänsligt. |Inga |
| Definierar |Ange parametrar som nyckel-/värdepar för refererande i pigskriptet |Inga |

## <a name="example"></a>Exempel
Låt oss överväga ett exempel på spelloggar analys där du vill identifiera den tid som spelare spelar spel som lanserades av ditt företag.

Följande exempel spellogg är en komma (,) separerad fil. Den innehåller följande fält – ProfileID, SessionStart, Duration, SrcIPAddress och GameType.

```
1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
.....
```

**Pig-skriptet** för att bearbeta dessa data:

```
PigSampleIn = LOAD 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/samplein/' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);

GroupProfile = Group PigSampleIn all;

PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);

Store PigSampleOut into 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/sampleoutpig/' USING PigStorage (',');
```

Så här kör du pig-skriptet i en Data Factory-pipeline:

1. Skapa en länkad tjänst för att registrera [ditt eget HDInsight-beräkningskluster](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) eller konfigurera [HDInsight-beräkningskluster på begäran](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Låt oss anropa den länkade tjänsten **HDInsightLinkedService**.
2. Skapa en [länkad tjänst](data-factory-azure-blob-connector.md) för att konfigurera anslutningen till Azure Blob-lagring som är värd för data. Låt oss anropa den länkade tjänsten **StorageLinkedService**.
3. Skapa [datauppsättningar](data-factory-create-datasets.md) som pekar på indata och utdata. Låt oss anropa indatauppsättningen **PigSampleIn** och utdatadatauppsättningen **PigSampleOut**.
4. Kopiera grisfrågan i en fil som Azure Blob Storage konfigurerat i steg #2. Om Azure-lagring som är värd för data skiljer sig från den som är värd för frågefilen, skapa en separat Azure Storage-länkad tjänst. Se den länkade tjänsten i aktivitetskonfigurationen. Använd **scriptPath** för att ange sökvägen till grisskriptfil och **scriptLinkedService**. 
   
   > [!NOTE]
   > Du kan också ange inline-skriptet Pig **script** i aktivitetsdefinitionen med hjälp av skriptegenskapen. Vi rekommenderar dock inte den här metoden eftersom alla specialtecken i skriptet måste fly och kan orsaka felsökningsproblem. Den bästa metoden är att följa steg #4.
   >
   >
5. Skapa pipelinen med HDInsightPig-aktiviteten. Den här aktiviteten bearbetar indata genom att köra Pig-skriptet i HDInsight-klustret.

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
6. Distribuera pipelinen. Mer information finns i artikeln Skapa [pipelines.](data-factory-create-pipelines.md) 
7. Övervaka pipelinen med hjälp av datafabrikens övervaknings- och hanteringsvyer. Mer information finns i artikeln [Övervakning och hantering av datafabrikspipelor.](data-factory-monitor-manage-pipelines.md)

## <a name="specifying-parameters-for-a-pig-script"></a>Ange parametrar för ett Pig-skript
Tänk på följande exempel: spelloggar förtärs dagligen i Azure Blob Storage och lagras i en mapp som partitioneras baserat på datum och tid. Du vill parameterisera Pig-skriptet och skicka indatamappens plats dynamiskt under körning och även producera utdata som partitioneras med datum och tid.

Så här använder du parameteriserat Pig-skript:

* Definiera parametrarna i **definierar**.

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
* I Pig Script, se parametrarna med "**$parameterName**' som visas i följande exempel:

    ```
    PigSampleIn = LOAD '$Input' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);
    GroupProfile = Group PigSampleIn all;
    PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);
    Store PigSampleOut into '$Output' USING PigStorage (','); 
    ```

## <a name="see-also"></a>Se även
* [Hive-aktivitet](data-factory-hive-activity.md)
* [MapReduce-aktivitet](data-factory-map-reduce.md)
* [Hadoop streaming aktivitet](data-factory-hadoop-streaming-activity.md)
* [Anropa Spark-program](data-factory-spark.md)
* [Anropa R-skript](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)
