---
title: Så här att läsa eller skriva partitionerade data i Azure Data Factory | Microsoft Docs
description: Lär dig mer om att läsa eller skriva partitionerade data i Azure Data Factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: shlo
ms.openlocfilehash: f2d780900a0cd24f2d70499573a4055dc836ae0b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61466994"
---
# <a name="how-to-read-or-write-partitioned-data-in-azure-data-factory"></a>Så här att läsa eller skriva partitionerade data i Azure Data Factory

I Azure Data Factory version 1, kan du läsa eller skriva partitionerade data med hjälp av den **SliceStart**, **SliceEnd**, **WindowStart**, och **WindowEnd** systemvariabler. I den aktuella versionen av Data Factory kan du göra detta med hjälp av en pipeline-parameter och en utlösare start- eller schemalagd tid som ett värde för parametern. 

## <a name="use-a-pipeline-parameter"></a>Använd en pipeline-parameter 

I Data Factory version 1, kunde du använda den **partitionedBy** egenskapen och **SliceStart** systemvariabel som visas i följande exempel: 

```json
"folderPath": "adfcustomerprofilingsample/logs/marketingcampaigneffectiveness/{Year}/{Month}/{Day}/",
"partitionedBy": [
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "%M" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "%d" } }
],
```

Mer information om den **partitonedBy** egenskap, finns i [kopiera data till och från Azure Blob storage med hjälp av Azure Data Factory](v1/data-factory-azure-blob-connector.md#dataset-properties). 

Att uppnå det här beteendet i den aktuella versionen av Data Factory: 

1. Definiera en *pipeline-parametern* av typen **sträng**. I följande exempel visas namnet på parametern pipeline är **windowStartTime**. 
2. Ange **folderPath** i definitionen för datauppsättningen för att referera till värdet för parametern pipeline. 
3. Skicka det faktiska värdet för parametern när du anropar pipeline på begäran. Du kan även skicka en utlösare start- eller schema dynamiskt vid körning. 

```json
"folderPath": {
      "value": "adfcustomerprofilingsample/logs/marketingcampaigneffectiveness/@{formatDateTime(pipeline().parameters.windowStartTime, 'yyyy/MM/dd')}/",
      "type": "Expression"
},
```

## <a name="pass-in-a-value-from-a-trigger"></a>Skicka in ett värde från en utlösare

I utlösardefinitionen följande rullande fönster starttiden för utlösaren skickas som ett värde för parametern pipeline **windowStartTime**: 

```json
{
    "name": "MyTrigger",
    "properties": {
        "type": "TumblingWindowTrigger",
        "typeProperties": {
            "frequency": "Hour",
            "interval": "1",
            "startTime": "2018-05-15T00:00:00Z",
            "delay": "00:10:00",
            "maxConcurrency": 10
        },
        "pipeline": {
            "pipelineReference": {
                "type": "PipelineReference",
                "referenceName": "MyPipeline"
            },
            "parameters": {
                "windowStartTime": "@trigger().outputs.windowStartTime"
            }
        }
    }
}
```

## <a name="example"></a>Exempel

Här är en exempeldefinition för datauppsättning:

```json
{
  "name": "SampleBlobDataset",
  "type": "AzureBlob",
  "typeProperties": {
    "folderPath": {
      "value": "adfcustomerprofilingsample/logs/marketingcampaigneffectiveness/@{formatDateTime(pipeline().parameters.windowStartTime, 'yyyy/MM/dd')}/",
      "type": "Expression"
    },
    "format": {
      "type": "TextFormat",
      "columnDelimiter": ","
    }
  },
  "structure": [
    { "name": "ProfileID", "type": "String" },
    { "name": "SessionStart", "type": "String" },
    { "name": "Duration", "type": "Int32" },
    { "name": "State", "type": "String" },
    { "name": "SrcIPAddress", "type": "String" },
    { "name": "GameType", "type": "String" },
    { "name": "Multiplayer", "type": "String" },
    { "name": "EndRank", "type": "String" },
    { "name": "WeaponsUsed", "type": "Int32" },
    { "name": "UsersInteractedWith", "type": "String" },
    { "name": "Impressions", "type": "String" }
  ],
  "linkedServiceName": {
    "referenceName": "churnStorageLinkedService",
    "type": "LinkedServiceReference"
  }
}
```

Pipeline-definition: 

```json
{
    "properties": {
        "activities": [{
            "type": "HDInsightHive",
            "typeProperties": {
                "scriptPath": {
                    "value": "@concat(pipeline().parameters.blobContainer, '/scripts/', pipeline().parameters.partitionHiveScriptFile)",
                    "type": "Expression"
                },
                "scriptLinkedService": {
                    "referenceName": "churnStorageLinkedService",
                    "type": "LinkedServiceReference"
                },
                "defines": {
                    "RAWINPUT": {
                        "value": "@concat('wasb://', pipeline().parameters.blobContainer, '@', pipeline().parameters.blobStorageAccount, '.blob.core.windows.net/logs/', pipeline().parameters.inputRawLogsFolder, '/')",
                        "type": "Expression"
                    },
                    "Year": {
                        "value": "@formatDateTime(pipeline().parameters.windowStartTime, 'yyyy')",
                        "type": "Expression"
                    },
                    "Month": {
                        "value": "@formatDateTime(pipeline().parameters.windowStartTime, 'MM')",
                        "type": "Expression"
                    },
                    "Day": {
                        "value": "@formatDateTime(pipeline().parameters.windowStartTime, 'dd')",
                        "type": "Expression"
                    }
                }
            },
            "linkedServiceName": {
                "referenceName": "HdiLinkedService",
                "type": "LinkedServiceReference"
            },
            "name": "HivePartitionGameLogs"
        }],
        "parameters": {
            "windowStartTime": {
                "type": "String"
            },
            "blobStorageAccount": {
                "type": "String"
            },
            "blobContainer": {
                "type": "String"
            },
            "inputRawLogsFolder": {
                "type": "String"
            }
        }
    }
}
```

## <a name="next-steps"></a>Nästa steg

En fullständig genomgång av hur du skapar en datafabrik med en pipeline finns i [snabbstarten: Skapa en datafabrik](quickstart-create-data-factory-powershell.md). 

