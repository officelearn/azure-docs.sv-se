---
title: Hämta metadataaktivitet i Azure Data Factory | Microsoft Docs
description: Lär dig hur du kan använda GetMetadata-aktiviteten i Data Factory-pipeline.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: ''
ms.assetid: 1c46ed69-4049-44ec-9b46-e90e964a4a8e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: jingwang
ms.openlocfilehash: 78f63b4f46fe5479d4d0fd5849ad80536d8a137c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61346924"
---
# <a name="get-metadata-activity-in-azure-data-factory"></a>Hämta metadataaktivitet i Azure Data Factory

GetMetadata-aktiviteten kan användas för att hämta **metadata** för alla data i Azure Data Factory. Den här aktiviteten kan användas i följande scenarier:

- Verifiera metadata-information för alla data
- Utlösa en pipeline när data är klar / tillgängliga

Följande funktioner är tillgängliga i kontrollflödet:

- Utdata från GetMetadata-aktiviteten kan användas i villkorsuttryck för att utföra verifiering.
- En pipeline kan utlösas när villkoret är uppfyllt via gör-tills slingor

## <a name="supported-capabilities"></a>Funktioner som stöds

GetMetadata-aktiviteten tar en datauppsättning som en obligatorisk indata och utdata metadatainformation som är tillgängliga som utdata. För närvarande stöds följande kopplingar med motsvarande hämtningsbar metadata och högsta stöds metadata är upp till **1MB**.

>[!NOTE]
>Om du kör GetMetadata-aktiviteten på en lokal Integration Runtime stöds den senaste kapaciteten på version 3.6 eller senare. 

### <a name="supported-connectors"></a>Kopplingar som stöds

**Fillagring:**

| Anslutning/Metadata | itemName<br>(fil/mapp) | ItemType<br>(fil/mapp) | storlek<br>(fil) | skapad<br>(fil/mapp) | lastModified<br>(fil/mapp) |childItems<br>(mapp) |contentMD5<br>(fil) | struktur<br/>(fil) | Antal kolumner<br>(fil) | Det finns<br>(fil/mapp) |
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| Amazon S3 | √/√ | √/√ | √ | x/x | √/√* | √ | x | √ | √ | √/√* |
| Google Cloud Storage | √/√ | √/√ | √ | x/x | √/√* | √ | x | √ | √ | √/√* |
| Azure-blobb | √/√ | √/√ | √ | x/x | √/√* | √ | √ | √ | √ | √/√ |
| Azure Data Lake Storage Gen1 | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| Azure Data Lake Storage Gen2 | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| Azure File Storage | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| Filsystem | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| SFTP | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| FTP | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |

- För Amazon S3 och Google Sloud Storage den `lastModified` gäller bucket och nyckel, men inte virtuell mapp; och `exists` gäller bucket och nyckeln men inte prefix eller virtuell mapp.
- För Azure Blob den `lastModified` gäller för behållare och blob men inte virtuell mapp.

**Relationsdatabas:**

| Anslutning/Metadata | struktur | Antal kolumner | Det finns |
|:--- |:--- |:--- |:--- |
| Azure SQL Database | √ | √ | √ |
| Hanterad Azure SQL Database-instans | √ | √ | √ |
| Azure SQL Data Warehouse | √ | √ | √ |
| SQL Server | √ | √ | √ |

### <a name="metadata-options"></a>Alternativ för metadata

Följande typer av metadata kan anges i fältlistan GetMetadata-aktivitet att hämta:

| Metadatatyp | Beskrivning |
|:--- |:--- |
| itemName | Namnet på filen eller mappen. |
| ItemType | Typ av filen eller mappen. Utdatavärdet `File` eller `Folder`. |
| storlek | Storleken på filen i byte. Gäller för bara fil. |
| skapad | Skapad datum/tid för filen eller mappen. |
| lastModified | Senast ändrad datum/tid för filen eller mappen. |
| childItems | Lista över undermappar och filer i den angivna mappen. Gäller endast på mappen. Utdatavärdet är en lista med namn och typ för varje underordnade objekt. |
| contentMD5 | MD5 för filen. Gäller för bara fil. |
| struktur | Datastruktur i filen eller relationsdatabas tabell. Utdatavärdet är en lista med kolumnnamn och Kolumntyp. |
| Antal kolumner | Antalet kolumner i filen eller relationstabell. |
| Det finns| Om en fil/mapp/table finns eller inte. Tänk om ”finns” anges i fältlistan GetaMetadata aktiviteten inte misslyckas trots att det inte finns objektet (fil/mapp/table); i stället returnerar `exists: false` i utdata. |

>[!TIP]
>När du vill kontrollera om det finns en fil/mapp/tabell eller inte kan ange `exists` i fältlistan för GetMetadata-aktiviteten kan du kontrollera den `exists: true/false` resultera i utdata för aktiviteten. Om `exists` har inte konfigurerats i fältlistan i GetMetadata-aktiviteten att misslyckas när objektet inte hittas.

## <a name="syntax"></a>Syntax

**GetMetadata-aktiviteten:**

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

**Datauppsättning:**

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
            "filename": "file.json",
            "format":{
                "type":"JsonFormat"
            }
        }
    }
}
```

## <a name="type-properties"></a>Egenskaperna för anslutningstypen

GetMetadata-aktiviteten kan för närvarande hämta följande typer av metadatainformation.

Egenskap  | Beskrivning | Obligatoriskt
-------- | ----------- | --------
fieldList | Visar typerna av metadatainformation som krävs. Mer information finns i [Metadata alternativ](#metadata-options) avsnittet om metadata som stöds. | Ja 
dataset | Referens-datauppsättning vars GetMetaData-aktivitet är kan hämtas av GetMetadata-aktiviteten. Se [funktioner som stöds](#supported-capabilities) avsnittet på kopplingar som stöds och referera till avsnitt om anslutningsprogram på datauppsättningen syntax information. | Ja

## <a name="sample-output"></a>Exempel på utdata

GetMetadata-resultatet visas i utdata. Nedan visas två exempel med fullständig metadata-alternativ som valts i fältlistan som referens. Om du vill använda resultatet i efterföljande aktivitet använder du mönstret för `@{activity('MyGetMetadataActivity').output.itemName}`.

### <a name="get-a-files-metadata"></a>Hämta metadata för en fil

```json
{
  "exists": true,
  "itemName": "test.csv",
  "itemType": "File",
  "size": 104857600,
  "lastModified": "2017-02-23T06:17:09Z",
  "created": "2017-02-23T06:17:09Z",
  "contentMD5": "cMauY+Kz5zDm3eWa9VpoyQ==",
  "structure": [
    {
        "name": "id",
        "type": "Int64"
    },
    {
        "name": "name",
        "type": "String"
    }
  ],
  "columnCount": 2
}
```

### <a name="get-a-folders-metadata"></a>Hämta metadata för en mapp

```json
{
  "exists": true,
  "itemName": "testFolder",
  "itemType": "Folder",
  "lastModified": "2017-02-23T06:17:09Z",
  "created": "2017-02-23T06:17:09Z",
  "childItems": [
    {
      "name": "test.avro",
      "type": "File"
    },
    {
      "name": "folder hello",
      "type": "Folder"
    }
  ]
}
```

## <a name="next-steps"></a>Nästa steg
Se andra kontrollflödesaktiviteter som stöds av Data Factory: 

- [Execute Pipeline-aktivitet](control-flow-execute-pipeline-activity.md)
- [För varje aktivitet](control-flow-for-each-activity.md)
- [Lookup-aktivitet](control-flow-lookup-activity.md)
- [Webbaktivitet](control-flow-web-activity.md)
