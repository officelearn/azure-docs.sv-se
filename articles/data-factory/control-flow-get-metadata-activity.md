---
title: Hämta metadata för aktiviteten i Azure Data Factory | Microsoft Docs
description: Lär dig hur du kan använda lagrade Proceduraktiviteten för SQL Server för att anropa en lagrad procedur i en Azure SQL Database eller Azure SQL Data Warehouse från Data Factory-pipelinen.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.assetid: 1c46ed69-4049-44ec-9b46-e90e964a4a8e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: shlo
ms.openlocfilehash: 4698f2e4c75456de7387ee7fe3bfa9b2ab4dd406
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="get-metadata-activity-in-azure-data-factory"></a>Hämta metadata för aktiviteten i Azure Data Factory
GetMetadata-aktiviteten kan användas för att hämta **metadata** alla data i Azure Data Factory. Den här aktiviteten stöds endast för datafabriker för version 2. Den kan användas i följande scenarier:

- Validera metadatainformation för alla data
- Utlös en pipeline när data är klar / tillgängliga

Följande funktioner är tillgängliga i kontrollflödet:

- Utdata från GetMetadata aktiviteten kan användas i villkorsuttryck för att utföra verifiering.
- En pipeline kan utlösas när villkoret är uppfyllt via-tills slingor

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig (GA), se [Data Factory V1 dokumentationen](v1/data-factory-introduction.md).

## <a name="supported-capabilities"></a>Funktioner som stöds

Aktiviteten GetMetadata tar en datamängd som en obligatorisk indata och utdata metadata-information som är tillgängliga som aktivitetsutdata. För närvarande stöds följande kopplingar med motsvarande strängfält meatadata:

>[!NOTE]
>Om du kör GetMetadata aktivitet på en Self-hosted integrering Runtime stöds den senaste kapaciteten på versionen 3,6 eller senare. 

### <a name="supported-connectors"></a>Kopplingar som stöds

**Fillagring:**

| Connector-Metadata | itemName<br>(filen/mappen) | itemType<br>(filen/mappen) | storlek<br>(fil) | skapad<br>(filen/mappen) | Senast ändrad<br>(filen/mappen) |childItems<br>(mapp) |contentMD5<br>(fil) | struktur<br/>(fil) | Antal kolumner<br>(fil) | Det finns<br>(filen/mappen) |
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| Azure-blobb | √/√ | √/√ | √ | x/x | √/√ | √ | √ | √ | √ | √/√ |
| Azure Data Lake Store | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| Azure File Storage | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| Filsystem | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| SFTP | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| FTP | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |

**Relationsdatabas:**

| Connector-Metadata | struktur | Antal kolumner | Det finns |
|:--- |:--- |:--- |:--- |
| Azure SQL Database | √ | √ | √ |
| Azure SQL Data Warehouse | √ | √ | √ |
| SQL Server | √ | √ | √ |

### <a name="metadata-options"></a>Metadata-alternativ

Följande typer av metadata kan anges i fältlistan GetMetadata aktiviteten att hämta:

| Metadatatyp | Beskrivning |
|:--- |:--- |
| itemName | Namnet på filen eller mappen. |
| itemType | Typ av filen eller mappen. Utdatavärdet för är `File` eller `Folder`. |
| storlek | Storlek i byte. Gäller endast filen. |
| skapad | Skapad datum/tid för filen eller mappen. |
| Senast ändrad | Senast ändrad datum/tid för filen eller mappen. |
| childItems | Lista över undermappar och filer i den angivna mappen. Gäller endast mappen. Utdatavärdet är en lista över namn och typ för varje underordnade objekt. |
| contentMD5 | MD5 för filen. Gäller endast filen. |
| struktur | Datastruktur i filen eller relationsdatabas tabell. Utdatavärdet är en lista med kolumnnamn och kolumntypen. |
| Antal kolumner | Antalet kolumner i filen eller relationella tabell. |
| Det finns| Om filen/mappen/det finns en tabell eller inte. Observera om ”finns” anges i fältlistan GetaMetadata aktiviteten inte inte även om objektet (tabell-filen/mappen) inte finns. i stället returneras `exists: false` i utdata. |

>[!TIP]
>När du vill kontrollera om det finns en fil/mapp/tabell eller inte kan ange `exists` i fältlistan GetMetadata aktivitet kan du kontrollera den `exists: true/false` fås aktivitetsutdata. Om `exists` har inte konfigurerats i fältlistan GetMetadata aktiviteten misslyckas när objektet inte finns.

## <a name="syntax"></a>Syntax

**GetMetadata aktivitet:**

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

**DataSet:**

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

## <a name="type-properties"></a>Typegenskaper

GetMetadata aktivitet kan för närvarande hämta följande typer av metadata-information.

Egenskap  | Beskrivning | Krävs
-------- | ----------- | --------
Fältlista | Visar typerna av metadatainformation som krävs. Mer information finns i [Metadata alternativ](#metadata-options) avsnitt på stöds metadata. | Ja 
DataSet | Referensdatauppsättningen vars metadata aktivitet är att hämtas av GetMetadata-aktiviteten. Se [funktioner som stöds](#supported-capabilities) avsnittet på kopplingar som stöds och referera till koppling avsnittet dataset syntax detaljer. | Ja

## <a name="sample-output"></a>Exempel på utdata

GetMetadata-resultatet visas i aktivitetsutdata. Nedan visas två exempel med fullständig metadata-alternativ som valts i fältlistan som referens. Om du vill använda resultatet i efterföljande aktiviteten använder mönstret för `@{activity('MyGetMetadataActivity').output.itemName}`.

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
Se annan kontrollflödesaktiviteter som stöds av Data Factory: 

- [Execute Pipeline-aktivitet](control-flow-execute-pipeline-activity.md)
- [För varje aktivitet](control-flow-for-each-activity.md)
- [Lookup-aktivitet](control-flow-lookup-activity.md)
- [Webbaktivitet](control-flow-web-activity.md)