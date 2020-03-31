---
title: Omvandla data med Hive-aktivitet - Azure
description: Lär dig hur du kan använda Hive-aktiviteten i en Azure-datafabrik för att köra Hive-frågor på ett on-demand/ditt eget HDInsight-kluster.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.assetid: 80083218-743e-4da8-bdd2-60d1c77b1227
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: d153f8c316cbb76e063f07f7f823c8d9c4a21f87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74703362"
---
# <a name="transform-data-using-hive-activity-in-azure-data-factory"></a>Omvandla data med Hive-aktivitet i Azure Data Factory 
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
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av datafabrikstjänsten läser du [transformera data med Hive-aktivitet i Data Factory](../transform-data-using-hadoop-hive.md).

HDInsight Hive-aktiviteten i en Data [Factory-pipeline](data-factory-create-pipelines.md) kör Hive-frågor på [ditt eget](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) eller on-demand Windows/Linux-baserade HDInsight-kluster. [on-demand](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) Den här artikeln bygger på artikeln [om dataomvandlingsaktiviteter,](data-factory-data-transformation-activities.md) som ger en allmän översikt över dataomvandling och de omvandlingsaktiviteter som stöds.

> [!NOTE] 
> Om du inte har gjort det tidigare i Azure Data Factory läser du [in introduktion till Azure Data Factory](data-factory-introduction.md) och gör självstudien: Skapa din första [datapipeline](data-factory-build-your-first-pipeline.md) innan du läser den här artikeln. 

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
## <a name="syntax-details"></a>Syntaxinformation
| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| namn |Aktivitetens namn |Ja |
| description |Text som beskriver vad aktiviteten används för |Inga |
| typ |HDinsightHive (HDinsightHive) |Ja |
| Ingångar |Indata som förbrukas av Hive-aktiviteten |Inga |
| Utgångar |Resultat som produceras av Hive-aktiviteten |Ja |
| linkedServiceName |Referens till HDInsight-klustret som registrerats som en länkad tjänst i Data Factory |Ja |
| -skriptet |Ange inline-skriptet för Hive-skript |Inga |
| scriptPath |Lagra Hive-skriptet i en Azure blob-lagring och ange sökvägen till filen. Använd egenskapen script eller 'scriptPath'. Båda kan inte användas tillsammans. Filnamnet är skiftlägeskänsligt. |Inga |
| Definierar |Ange parametrar som nyckel-/värdepar för refererande i Hive-skriptet med hjälp av hiveconf |Inga |

## <a name="example"></a>Exempel
Låt oss överväga ett exempel på spelloggar analys där du vill identifiera den tid som användare spelar spel som lanserats av ditt företag. 

Följande logg är en exempelspelslogg,`,`som är komma ( ) separerad och innehåller följande fält – ProfileID, SessionStart, Duration, SrcIPAddress och GameType.

```
1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
.....
```

**Hive-skriptet** för att bearbeta dessa data:

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

Om du vill köra hive-skriptet i en Data Factory-pipeline måste du göra följande

1. Skapa en länkad tjänst för att registrera [ditt eget HDInsight-beräkningskluster](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) eller konfigurera [HDInsight-beräkningskluster på begäran](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Låt oss kalla den länkade tjänsten "HDInsightLinkedService".
2. Skapa en [länkad tjänst](data-factory-azure-blob-connector.md) för att konfigurera anslutningen till Azure Blob-lagring som är värd för data. Låt oss kalla den länkade tjänsten "StorageLinkedService"
3. Skapa [datauppsättningar](data-factory-create-datasets.md) som pekar på indata och utdata. Låt oss kalla indatauppsättningen "HiveSampleIn" och utdatauppsättningen "HiveSampleOut"
4. Kopiera Hive-frågan som en fil till Azure Blob Storage som konfigurerats i steg #2. Om lagringen för att vara värd för data skiljer sig från den som är värd för den här frågefilen skapar du en separat Azure Storage-länkad tjänst och refererar till den i aktiviteten. Använd **scriptPath** för att ange sökvägen till hive-frågefilen och **scriptLinkedService** för att ange Azure-lagring som innehåller skriptfilen. 
   
   > [!NOTE]
   > Du kan också ange inline-skriptet Hive **script** i aktivitetsdefinitionen med hjälp av skriptegenskapen. Vi rekommenderar inte den här metoden eftersom alla specialtecken i skriptet i JSON-dokumentet måste fly och kan orsaka felsökningsproblem. Den bästa metoden är att följa steg #4.
   > 
   > 
5. Skapa en pipeline med HDInsightHive-aktiviteten. Aktiviteten bearbetar/omvandlar data.

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
6. Distribuera pipelinen. Mer information finns i artikeln Skapa [pipelines.](data-factory-create-pipelines.md) 
7. Övervaka pipelinen med hjälp av datafabrikens övervaknings- och hanteringsvyer. Mer information finns i artikeln [Övervakning och hantering av datafabrikspipelor.](data-factory-monitor-manage-pipelines.md) 

## <a name="specifying-parameters-for-a-hive-script"></a>Ange parametrar för ett Hive-skript
I det här exemplet intas spelloggar dagligen i Azure Blob Storage och lagras i en mapp som partitioneras med datum och tid. Du vill parameterisera Hive-skriptet och skicka indatamappens plats dynamiskt under körning och även producera utdata som partitioneras med datum och tid.

Om du vill använda parameteriserat Hive-skript gör du följande

* Definiera parametrarna i **definierar**.

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
* I Hive-skriptet läser du parametern med **${hiveconf:parameterName}**. 
  
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
* [Grisaktivitet](data-factory-pig-activity.md)
* [MapReduce-aktivitet](data-factory-map-reduce.md)
* [Hadoop streaming aktivitet](data-factory-hadoop-streaming-activity.md)
* [Anropa Spark-program](data-factory-spark.md)
* [Anropa R-skript](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)

