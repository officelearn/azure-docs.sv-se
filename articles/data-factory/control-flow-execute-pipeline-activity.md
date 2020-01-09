---
title: Kör pipeline-aktivitet i Azure Data Factory
description: Lär dig hur du kan använda aktiviteten kör pipeliner för att anropa en Data Factory pipeline från en annan Data Factory-pipeline.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 26dd7c4f33360030b13ddbfc1516396436724c40
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75440441"
---
# <a name="execute-pipeline-activity-in-azure-data-factory"></a>Kör pipeline-aktivitet i Azure Data Factory
Med aktiviteten kör pipeliner kan en Data Factory pipeline anropa en annan pipeline.

## <a name="syntax"></a>Syntax

```json
{
    "name": "MyPipeline",
    "properties": {
        "activities": [
            {
                "name": "ExecutePipelineActivity",
                "type": "ExecutePipeline",
                "typeProperties": {
                    "parameters": {                        
                        "mySourceDatasetFolderPath": {
                            "value": "@pipeline().parameters.mySourceDatasetFolderPath",
                            "type": "Expression"
                        }
                    },
                    "pipeline": {
                        "referenceName": "<InvokedPipelineName>",
                        "type": "PipelineReference"
                    },
                    "waitOnCompletion": true
                 }
            }
        ],
        "parameters": [
            {
                "mySourceDatasetFolderPath": {
                    "type": "String"
                }
            }
        ]
    }
}
```

## <a name="type-properties"></a>Typ egenskaper

Egenskap | Beskrivning | Tillåtna värden | Krävs
-------- | ----------- | -------------- | --------
namn | Namnet på aktiviteten kör pipeliner. | String | Ja
typ | Måste vara inställt på: **ExecutePipeline**. | String | Ja
pipeline | Pipeline-referens till den beroende pipelinen som den här pipelinen anropar. Ett pipeline-referens objekt har två egenskaper: **referenceName** och **Type**. Egenskapen referenceName anger namnet på referens pipelinen. Egenskapen Type måste anges till PipelineReference. | PipelineReference | Ja
parameters | Parametrar som ska skickas till den anropade pipelinen | Ett JSON-objekt som mappar parameter namn till argument värden | Inga
waitOnCompletion | Definierar om aktivitets körningen väntar på att körningen av beroende pipelinen ska slutföras. Standardvärdet är false. | Boolean | Inga

## <a name="sample"></a>Exempel
Det här scenariot har två pipeliner:

- **Huvud pipeline** – den här pipelinen har en utför pipeline-aktivitet som anropar den anropade pipelinen. Huvud-pipeline tar två parametrar: `masterSourceBlobContainer``masterSinkBlobContainer`.
- **Anropad pipeline** – den här pipelinen har en kopierings aktivitet som kopierar data från en Azure Blob-källa till Azure Blob-mottagare. Den anropade pipelinen tar två parametrar: `sourceBlobContainer``sinkBlobContainer`.

### <a name="master-pipeline-definition"></a>Huvud pipeline-definition

```json
{
  "name": "masterPipeline",
  "properties": {
    "activities": [
      {
        "type": "ExecutePipeline",
        "typeProperties": {
          "pipeline": {
            "referenceName": "invokedPipeline",
            "type": "PipelineReference"
          },
          "parameters": {
            "sourceBlobContainer": {
              "value": "@pipeline().parameters.masterSourceBlobContainer",
              "type": "Expression"
            },
            "sinkBlobContainer": {
              "value": "@pipeline().parameters.masterSinkBlobContainer",
              "type": "Expression"
            }
          },
          "waitOnCompletion": true
        },
        "name": "MyExecutePipelineActivity"
      }
    ],
    "parameters": {
      "masterSourceBlobContainer": {
        "type": "String"
      },
      "masterSinkBlobContainer": {
        "type": "String"
      }
    }
  }
}

```

### <a name="invoked-pipeline-definition"></a>Definition av anropad pipeline

```json
{
  "name": "invokedPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
        "name": "CopyBlobtoBlob",
        "inputs": [
          {
            "referenceName": "SourceBlobDataset",
            "type": "DatasetReference"
          }
        ],
        "outputs": [
          {
            "referenceName": "sinkBlobDataset",
            "type": "DatasetReference"
          }
        ]
      }
    ],
    "parameters": {
      "sourceBlobContainer": {
        "type": "String"
      },
      "sinkBlobContainer": {
        "type": "String"
      }
    }
  }
}

```

**Länkad tjänst**

```json
{
    "name": "BlobStorageLinkedService",
    "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=*****;AccountKey=*****"
    }
  }
}
```

**Käll data uppsättning**
```json
{
    "name": "SourceBlobDataset",
    "properties": {
    "type": "AzureBlob",
    "typeProperties": {
      "folderPath": {
        "value": "@pipeline().parameters.sourceBlobContainer",
        "type": "Expression"
      },
      "fileName": "salesforce.txt"
    },
    "linkedServiceName": {
      "referenceName": "BlobStorageLinkedService",
      "type": "LinkedServiceReference"
    }
  }
}
```

**Data uppsättning för mottagare**
```json
{
    "name": "sinkBlobDataset",
    "properties": {
    "type": "AzureBlob",
    "typeProperties": {
      "folderPath": {
        "value": "@pipeline().parameters.sinkBlobContainer",
        "type": "Expression"
      }
    },
    "linkedServiceName": {
      "referenceName": "BlobStorageLinkedService",
      "type": "LinkedServiceReference"
    }
  }
}
```

### <a name="running-the-pipeline"></a>Köra pipelinen

För att köra huvud pipelinen i det här exemplet skickas följande värden för parametrarna masterSourceBlobContainer och masterSinkBlobContainer: 

```json
{
  "masterSourceBlobContainer": "executetest",
  "masterSinkBlobContainer": "executesink"
}
```

Huvud pipelinen vidarebefordrar dessa värden till den anropade pipelinen som visas i följande exempel: 

```json
{
    "type": "ExecutePipeline",
    "typeProperties": {
      "pipeline": {
        "referenceName": "invokedPipeline",
        "type": "PipelineReference"
      },
      "parameters": {
        "sourceBlobContainer": {
          "value": "@pipeline().parameters.masterSourceBlobContainer",
          "type": "Expression"
        },
        "sinkBlobContainer": {
          "value": "@pipeline().parameters.masterSinkBlobContainer",
          "type": "Expression"
        }
      },

      ....
}

```
## <a name="next-steps"></a>Nästa steg
Se andra kontroll flödes aktiviteter som stöds av Data Factory: 

- [För varje aktivitet](control-flow-for-each-activity.md)
- [GetMetadata-aktivitet](control-flow-get-metadata-activity.md)
- [Lookup-aktivitet](control-flow-lookup-activity.md)
- [Webb aktivitet](control-flow-web-activity.md)
