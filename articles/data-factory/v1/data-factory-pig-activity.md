---
title: Transformera data med Pig-aktivitet i Azure Data Factory | Microsoft Docs
description: Lär dig hur du kan använda Pig-aktivitet i en Azure-datafabrik för att köra Pig-skript på ett på-begäran/your own HDInsight-kluster.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 5af07a1a-2087-455e-a67b-a79841b4ada5
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: fe1ef8c141c4a4daa443f800181f8e6e3199d0cc
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2019
ms.locfileid: "54331307"
---
# <a name="transform-data-using-pig-activity-in-azure-data-factory"></a>Transformera data med Pig-aktivitet i Azure Data Factory
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
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av Data Factory-tjänsten finns i [Transformera data med piggningsåtgärd i Data Factory](../transform-data-using-hadoop-pig.md).


HDInsight-piggningsåtgärd i en Datafabrik [pipeline](data-factory-create-pipelines.md) kör Pig frågor på [egna](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) eller [på begäran](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) Windows/Linux-baserat HDInsight-kluster. Den här artikeln bygger vidare på den [datatransformeringsaktiviteter](data-factory-data-transformation-activities.md) artikel som anger en allmän översikt över Dataomvandling och stöds transformeringsaktiviteter.

> [!NOTE] 
> Om du är nybörjare på Azure Data Factory, Läs igenom [introduktion till Azure Data Factory](data-factory-introduction.md) och igenom självstudien: [Skapa din första datapipeline](data-factory-build-your-first-pipeline.md) innan du läser den här artikeln. 

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
| namn |Namn på aktiviteten |Ja |
| beskrivning |Text som beskriver vad aktiviteten används till |Nej |
| typ |HDinsightPig |Ja |
| inmatningar |En eller flera inmatningar som används av Pig-aktivitet |Nej |
| utdata |En eller flera av de utdata som genereras av Pig-aktivitet |Ja |
| linkedServiceName |Referens till HDInsight-kluster som är registrerad som en länkad tjänst i Data Factory |Ja |
| skriptet |Ange infogat för Pig-skript |Nej |
| skriptets sökväg |Store Pig-skriptet i Azure blob storage och ange sökvägen till filen. Använd ”skript” eller ”scriptPath-egenskapen. Båda kan inte användas tillsammans. Filnamnet är skiftlägeskänsligt. |Nej |
| definierar |Ange parametrar som nyckel/värde-par för refererar till Pig-skript |Nej |

## <a name="example"></a>Exempel
Anta att du har ett exempel på spel loggar analytics där du vill identifiera den tid som användes av spelare spel som startas av ditt företag.

Följande spel exempellogg är en fil med kommaavgränsade (,). Den innehåller följande fält – profil-ID, SessionStart, varaktighet, SrcIPAddress och GameType.

```
1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
.....
```

Den **Apache Pig-skriptet** att bearbeta dessa data:

```
PigSampleIn = LOAD 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/samplein/' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);

GroupProfile = Group PigSampleIn all;

PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);

Store PigSampleOut into 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/sampleoutpig/' USING PigStorage (',');
```

Om du vill köra det här Pig-skriptet i en Data Factory-pipeline, gör du följande:

1. Skapa en länkad tjänst för att registrera [ditt eget HDInsight-kluster för beräkningar](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) eller konfigurera [på begäran HDInsight-kluster](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Vi kan kalla den här länkade tjänsten **HDInsightLinkedService**.
2. Skapa en [länkad tjänst](data-factory-azure-blob-connector.md) att konfigurera anslutningen till Azure Blob storage som är värd för data. Vi kan kalla den här länkade tjänsten **StorageLinkedService**.
3. Skapa [datauppsättningar](data-factory-create-datasets.md) som pekar på indata och utdata. Vi kan kalla den inkommande datauppsättningen **PigSampleIn** och datamängd för utdata **PigSampleOut**.
4. Kopiera Pig-frågan i en fil i Azure Blob Storage som konfigurerats i steg #2. Om Azure-lagring som är värd för data skiljer sig från det som är värd för filen, skapar du en separat länkad Azure Storage-tjänst. Avse den länkade tjänsten i konfigurationen av aktivitet. Använd ** scriptPath ** att ange sökvägen till skriptfilen för pig och **scriptLinkedService**. 
   
   > [!NOTE]
   > Du kan också ange Pig-skript direkt i aktivitetsdefinitionen med hjälp av den **skriptet** egenskapen. Men vi rekommenderar inte den här metoden som alla specialtecken i skriptet behov understrykningen och kan orsaka problem för felsökning. Det bästa sättet är att följa steg #4.
   >
   >
5. Skapar du pipelinen med aktiviteten HDInsightPig. Den här aktiviteten bearbetar indata genom att köra Pig-skriptet i HDInsight-kluster.

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
6. Distribuera pipelinen. Se [skapa pipelines](data-factory-create-pipelines.md) nedan för information. 
7. Övervaka pipeline med hjälp av data factory övervakning och hantering av vyer. Se [övervakning och hantera Data Factory-pipelines](data-factory-monitor-manage-pipelines.md) nedan för information.

## <a name="specifying-parameters-for-a-pig-script"></a>Ange parametrar för ett Pig-skript
Exempel: spel loggar samlas in varje dag i Azure Blob Storage och lagras i en mapp partitionerade baserat på datum och tid. Du vill Parameterisera Pig-skriptet och skicka den inkommande mapplatsen dynamiskt under körning och även producerar utdata som är partitionerad med datum och tid.

För att använda parametriserade Pig-skriptet, gör du följande:

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
* Pig-skriptet referera till parametrarna med hjälp av ”**$parameterName**” som visas i följande exempel:

    ```
    PigSampleIn = LOAD '$Input' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);
    GroupProfile = Group PigSampleIn all;
    PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);
    Store PigSampleOut into '$Output' USING PigStorage (','); 
    ```

## <a name="see-also"></a>Se även
* [Hive-aktivitet](data-factory-hive-activity.md)
* [MapReduce-aktivitet](data-factory-map-reduce.md)
* [Hadoop Streaming Activity](data-factory-hadoop-streaming-activity.md)
* [Anropa Spark-program](data-factory-spark.md)
* [Anropa R-skript](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)
