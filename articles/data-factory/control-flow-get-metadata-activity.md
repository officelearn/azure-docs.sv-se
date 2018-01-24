---
title: "Hämta metadata för aktiviteten i Azure Data Factory | Microsoft Docs"
description: "Lär dig hur du kan använda lagrade Proceduraktiviteten för SQL Server för att anropa en lagrad procedur i en Azure SQL Database eller Azure SQL Data Warehouse från Data Factory-pipelinen."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.assetid: 1c46ed69-4049-44ec-9b46-e90e964a4a8e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: shlo
ms.openlocfilehash: 20f3d4bb876a46b67385dd4435296e149641149e
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2018
---
# <a name="get-metadata-activity-in-azure-data-factory"></a>Hämta metadata för aktiviteten i Azure Data Factory
GetMetadata-aktiviteten kan användas till att hämta metadata för data i Azure Data Factory. Den här aktiviteten stöds endast för datafabriker för version 2. Den kan användas i följande scenarier:

- Validera metadatainformation för alla data
- Utlös en pipeline när data är klar / tillgängliga

Följande funktioner är tillgängliga i kontrollflödet:
- Utdata från GetMetadata aktiviteten kan användas i villkorsuttryck för att utföra verifiering.
- En pipeline kan utlösas när villkoret är uppfyllt via-tills slingor

GetMetadata aktiviteten använder ett dataset som en obligatorisk indata och utdata metadata-information som är tillgängliga som utdata. För närvarande stöds endast Azure blob-datauppsättningen. Metadatafälten stöds är storlek, struktur och lastModified tid.  

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig (GA), se [Data Factory V1 dokumentationen](v1/data-factory-introduction.md).


## <a name="syntax"></a>Syntax

### <a name="get-metadata-activity-definition"></a>Hämta Metadata aktivitetsdefinition:
I följande exempel returnerar aktiviteten GetMetadata metadata om de data som representeras av MyDataset. 

```json
{
    "name": "MyActivity",
    "type": "GetMetadata",
    "typeProperties": {
        "fieldList" : ["size", "lastModified", "structure"],
        "dataset": {
            "referenceName": "MyDataset",
            "type": "DatasetReference"
        }
    }
}
```
### <a name="dataset-definition"></a>Datauppsättningsdefinitionen:

```json
{
    "name": "MyDataset",
    "properties": {
    "type": "AzureBlob",
        "linkedService": {
            "referenceName": "StorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath":"container/folder",
            "Filename": "file.json",
            "format":{
                "type":"JsonFormat"
                "nestedSeperator": ","
            }
        }
    }
}
```

### <a name="output"></a>Resultat
```json
{
    "size": 1024,
    "structure": [
        {
            "name": "id",
            "type": "Int64"
        }, 
    ],
    "lastModified": "2016-07-12T00:00:00Z"
}
```

## <a name="type-properties"></a>Typegenskaper
För närvarande hämta GetMetadata aktiviteten följande typer av metadata-information från ett Azure storage-datamängd.

Egenskap | Beskrivning | Tillåtna värden | Krävs
-------- | ----------- | -------------- | --------
Fältlista | Visar typerna av metadatainformation som krävs.  | <ul><li>storlek</li><li>struktur</li><li>Senast ändrad</li></ul> |    Nej<br/>Om den är tom returnerar alla 3 stöds metadata-information för aktiviteten. 
DataSet | Referensdatauppsättningen vars metadata aktivitet är att hämtas av GetMetadata-aktiviteten. <br/><br/>Typen av som stöds är Azure Blob. Två sub egenskaper är: <ul><li><b>Referensnamn</b>: referens till en befintlig Azure-Blobbdatauppsättning</li><li><b>typen</b>: eftersom datamängden hänvisas är av typen ”DatasetReference”</li></ul> |    <ul><li>Sträng</li><li>DatasetReference</li></ul> | Ja

## <a name="next-steps"></a>Nästa steg
Se annan kontrollflödesaktiviteter som stöds av Data Factory: 

- [Execute Pipeline-aktivitet](control-flow-execute-pipeline-activity.md)
- [För varje aktivitet](control-flow-for-each-activity.md)
- [Lookup-aktivitet](control-flow-lookup-activity.md)
- [Webbaktivitet](control-flow-web-activity.md)