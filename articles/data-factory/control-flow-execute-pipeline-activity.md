---
title: Kör Pipeline-aktivitet i Azure Data Factory | Microsoft Docs
description: Lär dig hur du kan använda Kör Pipeline-aktivitet för att anropa en Data Factory-pipeline från en annan Data Factory-pipeline.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
ms.openlocfilehash: a0ece499262464bc28f55c37188698a3313e2c04
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60808843"
---
# <a name="execute-pipeline-activity-in-azure-data-factory"></a>Kör Pipeline-aktivitet i Azure Data Factory
Kör Pipeline-aktiviteten kan Data Factory-pipeline anropa en annan pipeline.

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

## <a name="type-properties"></a>Egenskaperna för anslutningstypen

Egenskap  | Beskrivning | Tillåtna värden | Krävs
-------- | ----------- | -------------- | --------
namn | Namnet på execute pipeline-aktivitet. | String | Ja
typ | Måste anges till: **ExecutePipeline**. | String | Ja
pipelina | Pipelinereferens till beroende pipelinen som anropar denna pipeline. En pipeline-referensobjektet har två egenskaper: **referenceName** och **typ**. Egenskapen referenceName anger namnet på referens-pipeline. Type-egenskapen måste anges till PipelineReference. | PipelineReference | Ja
parameters | Parametrar som ska skickas till anropad pipeline | En JSON-objekt som motsvarar argumentvärden parameternamn | Nej
waitOnCompletion | Definierar om körningsmiljön för aktiviteten ska vänta tills beroende pipeline-åtgärd ska slutföras. Standardvärdet är false. | Boolean | Nej

## <a name="sample"></a>Exempel
Det här scenariot har du två pipelines:

- **Master pipeline** – den här pipelinen har en kör Pipeline-aktivitet som anropar anropad pipeline. Master pipelinen har två parametrar: `masterSourceBlobContainer`, `masterSinkBlobContainer`.
- **Anropad pipeline** – den här pipelinen har en Kopieringsaktivitet som kopierar data från en Azure Blob-källan till Azure Blob-mottagaren. Anropade pipelinen har två parametrar: `sourceBlobContainer`, `sinkBlobContainer`.

### <a name="master-pipeline-definition"></a>Master pipeline-definition

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

### <a name="invoked-pipeline-definition"></a>Anropad pipeline-definition

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
      "connectionString": {
        "value": "DefaultEndpointsProtocol=https;AccountName=*****",
        "type": "SecureString"
      }
    }
  }
}
```

**Källdatauppsättning**
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

**Datauppsättning för mottagare**
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

### <a name="running-the-pipeline"></a>Kör pipelinen

Om du vill köra master pipelinen i det här exemplet skickas följande värden för parametrarna masterSourceBlobContainer och masterSinkBlobContainer: 

```json
{
  "masterSourceBlobContainer": "executetest",
  "masterSinkBlobContainer": "executesink"
}
```

Master pipelinen vidarebefordrar dessa värden till anropad pipeline som du ser i följande exempel: 

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
Se andra kontrollflödesaktiviteter som stöds av Data Factory: 

- [För varje aktivitet](control-flow-for-each-activity.md)
- [GetMetadata-aktivitet](control-flow-get-metadata-activity.md)
- [Lookup-aktivitet](control-flow-lookup-activity.md)
- [Webbaktivitet](control-flow-web-activity.md)
