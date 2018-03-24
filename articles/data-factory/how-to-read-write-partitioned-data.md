---
title: Hur att läsa eller skriva partitionerad data i Azure Data Factory | Microsoft Docs
description: Lär dig mer om att läsa eller skriva partitionerade data i Azure Data Factory version 2.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: shlo
ms.openlocfilehash: e3b6ccd1e7066ed86b3d6d2d85228688b06931c4
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="how-to-read-or-write-partitioned-data-in-azure-data-factory-version-2"></a>Hur att läsa eller skriva partitionerad data i Azure Data Factory version 2
Azure Data Factory stöds läsning eller skrivning partitionerade data med hjälp av SliceStart/SliceEnd/WindowStart/WindowEnd systemvariabler i version 1. Du kan åstadkomma detta genom att använda en pipeline-parameter och utlösarens schemalagd tid/starttid som ett värde för parametern i version 2. 

## <a name="use-a-pipeline-parameter"></a>Använda en pipeline-parameter 
I version 1, kunde du använda partitionedBy egenskap och SliceStart systemvariabeln som visas i följande exempel: 

```json
"folderPath": "adfcustomerprofilingsample/logs/marketingcampaigneffectiveness/yearno={Year}/monthno={Month}/dayno={Day}/",
"partitionedBy": [
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "%M" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "%d" } }
],
```

Läs mer om egenskapen partitonedBy [version 1 Azure Blob-koppling](v1/data-factory-azure-blob-connector.md#dataset-properties) artikel. 

I version 2 är ett sätt att uppnå det här beteendet att göra följande: 

1. Definiera en **pipeline parametern** av typen string. I följande exempel pipeline-parameternamnet är **scheduledRunTime**. 
2. Ange **folderPath** i datauppsättningsdefinitionen till värdet för pipeline-parametern. 
3. Skicka en hårdkodad värde för parametern innan du kör pipeline. Eller skicka en utlösare start tid eller schemalagd tid dynamiskt vid körning. 

```json
"folderPath": {
      "value": "@concat(pipeline().parameters.blobContainer, '/logs/marketingcampaigneffectiveness/yearno=', formatDateTime(pipeline().parameters.scheduledRunTime, 'yyyy'), '/monthno=', formatDateTime(pipeline().parameters.scheduledRunTime, '%M'), '/dayno=', formatDateTime(pipeline().parameters.scheduledRunTime, '%d'), '/')",
      "type": "Expression"
},
```

## <a name="pass-in-value-from-a-trigger"></a>Ange värdet från en utlösare
I följande utlösardefinition schemalagda tiden för utlösaren skickas som ett värde för den **scheduledRunTime** pipeline-parameter: 

```json
{
    "name": "MyTrigger",
    "properties": {
       ...
        },
        "pipeline": {
            "pipelineReference": {
                "type": "PipelineReference",
                "referenceName": "MyPipeline"
            },
            "parameters": {
                "scheduledRunTime": "@trigger().scheduledTime"
            }
        }
    }
}
```

## <a name="example"></a>Exempel

Här är ett exempel datauppsättningsdefinitionen (som använder en parameter med namnet: `date`):

```json
{
  "type": "AzureBlob",
  "typeProperties": {
    "folderPath": {
      "value": "@concat(pipeline().parameters.blobContainer, '/logs/marketingcampaigneffectiveness/yearno=', formatDateTime(pipeline().parameters.scheduledRunTime, 'yyyy'), '/monthno=', formatDateTime(pipeline().parameters.scheduledRunTime, '%M'), '/dayno=', formatDateTime(pipeline().parameters.scheduledRunTime, '%d'), '/')",
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
                    "PARTITIONEDOUTPUT": {
                        "value": "@concat('wasb://', pipeline().parameters.blobContainer, '@', pipeline().parameters.blobStorageAccount, '.blob.core.windows.net/logs/partitionedgameevents/')",
                        "type": "Expression"
                    },
                    "Year": {
                        "value": "@formatDateTime(pipeline().parameters.scheduledRunTime, 'yyyy')",
                        "type": "Expression"
                    },
                    "Month": {
                        "value": "@formatDateTime(pipeline().parameters.scheduledRunTime, '%M')",
                        "type": "Expression"
                    },
                    "Day": {
                        "value": "@formatDateTime(pipeline().parameters.scheduledRunTime, '%d')",
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
            "scheduledRunTime": {
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
            },
            "partitionHiveScriptFile": {
                "type": "String"
            }
        }
    }
}
```

## <a name="next-steps"></a>Nästa steg
För att skapa en datafabrik med en rörledning för fullständig genomgång, se [Snabbstart: skapa en datafabrik](quickstart-create-data-factory-powershell.md). 