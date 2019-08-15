---
title: Hämta metadata-aktivitet i Azure Data Factory | Microsoft Docs
description: Lär dig hur du kan använda GetMetadata-aktiviteten i en Data Factory pipeline.
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
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: 320e92e45f319e394b5a38b3f1e8ef3f314920b8
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2019
ms.locfileid: "68966335"
---
# <a name="get-metadata-activity-in-azure-data-factory"></a>Hämta metadata-aktivitet i Azure Data Factory

GetMetadata-aktivitet kan användas för att hämta **metadata** för alla data i Azure Data Factory. Den här aktiviteten kan användas i följande scenarier:

- Verifiera metadatainformation för alla data
- Utlös en pipeline när data är klara/tillgängliga

Följande funktioner är tillgängliga i kontroll flödet:

- Resultatet från GetMetadata-aktiviteten kan användas i villkors uttryck för att utföra verifieringen.
- En pipeline kan utlösas när villkoret är uppfyllt genom att göra-tills-loop

## <a name="supported-capabilities"></a>Funktioner som stöds

GetMetadata-aktiviteten tar en data uppsättning som krävs och matar ut metadatainformation som är tillgänglig som aktivitets utdata. För närvarande stöds följande kopplingar med motsvarande hämtnings bara metadata och den maximala metadata-storlek som stöds är upp till **1 MB**.

>[!NOTE]
>Om du kör GetMetadata-aktivitet på en egen värd Integration Runtime, stöds den senaste funktionen på version 3,6 eller senare. 

### <a name="supported-connectors"></a>Anslutningar som stöds

**Fil lagring:**

| Koppling/metadata | itemName<br>(fil/mapp) | itemType<br>(fil/mapp) | size<br>Arkiv | skapad<br>(fil/mapp) | lastModified<br>(fil/mapp) |childItems<br>projektbevakningsmappen |contentMD5<br>Arkiv | structure<br/>Arkiv | Antal<br>Arkiv | finns<br>(fil/mapp) |
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| [Amazon S3](connector-amazon-simple-storage-service.md) | √/√ | √/√ | √ | x/x | √/√ * | √ | x | √ | √ | √/√ * |
| [Google Cloud Storage](connector-google-cloud-storage.md) | √/√ | √/√ | √ | x/x | √/√ * | √ | x | √ | √ | √/√ * |
| [Azure Blob](connector-azure-blob-storage.md) | √/√ | √/√ | √ | x/x | √/√ * | √ | √ | √ | √ | √/√ |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Azure File Storage](connector-azure-file-storage.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| [Filsystem](connector-file-system.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| [SFTP](connector-sftp.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [FTP](connector-ftp.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |

- För Amazon S3 och Google Cloud Storage `lastModified` gäller Bucket och Key, men inte Virtual folder;, `exists` och tillämpas på Bucket och Key, men inte prefixet eller den virtuella mappen.
- För Azure Blob `lastModified` gäller för behållare och blob, men inte virtuell mapp.

**Relations databas:**

| Koppling/metadata | structure | Antal | finns |
|:--- |:--- |:--- |:--- |
| [Azure SQL Database](connector-azure-sql-database.md) | √ | √ | √ |
| [Azure SQL Database Managed Instance](connector-azure-sql-database-managed-instance.md) | √ | √ | √ |
| [Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md) | √ | √ | √ |
| [SQL Server](connector-sql-server.md) | √ | √ | √ |

### <a name="metadata-options"></a>Metadata-alternativ

Följande typer av metadata kan anges i GetMetadata aktivitets fält lista för att hämta:

| Typ av metadata | Beskrivning |
|:--- |:--- |
| itemName | Filens eller mappens namn. |
| itemType | Typ av fil eller mapp. Värdet för utdata `File` är `Folder`eller. |
| size | Storleken på filen i byte. Gäller endast för fil. |
| skapad | Datum/tid för filen eller mappen har skapats. |
| lastModified | Datum och tid då filen eller mappen senast ändrades. |
| childItems | Lista över undermappar och filer inuti den aktuella mappen. Gäller endast för mapp. Utgående värde är en lista över namn och typ för varje underordnat objekt. |
| contentMD5 | MD5 av filen. Gäller endast för fil. |
| structure | Data strukturen i fil-eller Relations databas tabellen. Utgående värde är en lista över kolumn namn och kolumn typ. |
| Antal | Antalet kolumner i filen eller Relations tabellen. |
| finns| Om en fil/mapp/tabell finns eller inte. OBS! om "finns" anges i fält listan GetaMetadata, kommer aktiviteten inte att fungera även om objektet (filen/mappen/tabellen) inte finns. i stället returneras `exists: false` i utdata. |

>[!TIP]
>När du vill validera om en fil/mapp/tabell finns eller inte anger `exists` du i getMetaData aktivitets fält listan kan du `exists: true/false` kontrol lera resultatet från aktivitetens utdata. Om `exists` inte har kon figurer ATS i fält listan kommer getMetaData-aktiviteten inte att fungera om objektet inte hittas.

>[!NOTE]
>När du hämtar metadata från fil Arkiv och konfigurerar `modifiedDatetimeStart` och/eller `modifiedDatetimeEnd`, `childItems` returnerar utdata bara filer under den angivna sökvägen med senast ändrad tid mellan intervallet, men inga undermappar.

## <a name="syntax"></a>Syntax

**GetMetadata-aktivitet:**

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

**Data uppsättning**

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

## <a name="type-properties"></a>Typ egenskaper

För närvarande kan GetMetadata-aktivitet hämta följande typer av metadatainformation.

Egenskap | Beskrivning | Obligatorisk
-------- | ----------- | --------
Fält lista | Listar de typer av metadatainformation som krävs. Se information i avsnittet [metadata-alternativ](#metadata-options) om metadata som stöds. | Ja 
data uppsättning | Referens data uppsättningen vars metadata-aktivitet ska hämtas av GetMetadata-aktiviteten. Mer information om vilka [funktioner](#supported-capabilities) som stöds finns i avsnittet om anslutningar som stöds och finns i avsnittet om information om data uppsättning. | Ja
formatSettings | Använd när du använder data uppsättning för format typ. | Nej
storeSettings | Använd när du använder data uppsättning för format typ. | Nej

## <a name="sample-output"></a>Exempel på utdata

GetMetadata-resultatet visas i aktivitets utdata. Nedan visas två exempel med uttömmade metadata-alternativ som är markerade i fält listan som referens. Använd mönstret i om `@{activity('MyGetMetadataActivity').output.itemName}`du vill använda resultatet i efterföljande aktivitet.

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
Se andra kontroll flödes aktiviteter som stöds av Data Factory: 

- [Execute Pipeline-aktivitet](control-flow-execute-pipeline-activity.md)
- [För varje aktivitet](control-flow-for-each-activity.md)
- [Lookup-aktivitet](control-flow-lookup-activity.md)
- [Webb aktivitet](control-flow-web-activity.md)
