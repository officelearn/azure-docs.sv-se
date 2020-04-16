---
title: Hämta metadataaktivitet i Azure Data Factory
description: Lär dig hur du använder aktiviteten Hämta metadata i en Data Factory-pipeline.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: ''
ms.assetid: 1c46ed69-4049-44ec-9b46-e90e964a4a8e
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: jingwang
ms.openlocfilehash: 344ad8e106c119c1de59570d1ec4e3df5e1cc8af
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417112"
---
# <a name="get-metadata-activity-in-azure-data-factory"></a>Hämta metadataaktivitet i Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Du kan använda aktiviteten Hämta metadata för att hämta metadata för data i Azure Data Factory. Du kan använda den här aktiviteten i följande scenarier:

- Verifiera metadata för alla data.
- Utlösa en pipeline när data är klara/tillgängliga.

Följande funktioner är tillgängliga i kontrollflödet:

- Du kan använda utdata från aktiviteten Hämta metadata i villkorsuttryck för att utföra validering.
- Du kan utlösa en pipeline när ett villkor är uppfyllt via Do Until-loopning.

## <a name="capabilities"></a>Funktioner

Aktiviteten Hämta metadata tar en datauppsättning som indata och returnerar metadatainformation som utdata. För närvarande stöds följande kopplingar och motsvarande metadata för hämtning. Den maximala storleken på returnerade metadata är 2 MB.

>[!NOTE]
>Om du kör aktiviteten Hämta metadata på en självvärderad integrationskörning stöds de senaste funktionerna i version 3.6 eller senare.

### <a name="supported-connectors"></a>Kopplingar som stöds

**Fillagring**

| Anslutning/Metadata | itemName (artikelnamn)<br>(fil/mapp) | itemType (artikeltyp)<br>(fil/mapp) | size<br>(fil) | Skapad<br>(fil/mapp) | lastModified<br>(fil/mapp) |childItems<br>-Jag har inte tid med det här. |contentMD5<br>(fil) | Struktur<br/>(fil) | kolumnCount<br>(fil) | Finns<br>(fil/mapp) |
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| [Amazon S3](connector-amazon-simple-storage-service.md) | √/√ | √/√ | √ | x/x | √/√* | √ | x | √ | √ | √/√* |
| [Google Cloud Storage](connector-google-cloud-storage.md) | √/√ | √/√ | √ | x/x | √/√* | √ | x | √ | √ | √/√* |
| [Azure Blob-lagring](connector-azure-blob-storage.md) | √/√ | √/√ | √ | x/x | √/√* | √ | √ | √ | √ | √/√ |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Azure-filer](connector-azure-file-storage.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| [Filsystem](connector-file-system.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| [SFTP](connector-sftp.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [FTP](connector-ftp.md) | √/√ | √/√ | √ | x/x | x/x | √ | x | √ | √ | √/√ |

- När du använder Hämta metadataaktivitet mot en mapp kontrollerar du att du har behörigheten LIST/EXECUTE till den angivna mappen.
- För Amazon S3 och `lastModified` Google Cloud Storage, gäller för bucket och `exists` nyckeln men inte till den virtuella mappen, och gäller för skopan och nyckeln men inte prefixet eller virtuell mapp.
- För Azure Blob-lagring `lastModified` gäller behållaren och bloben men inte för den virtuella mappen.
- `lastModified`filter gäller för närvarande för filter underordnade objekt men inte den angivna mappen/själva filen.
- Jokerteckenfilter på mappar/filer stöds inte för Hämta metadataaktivitet.

**Relationsdatabas**

| Anslutning/Metadata | Struktur | kolumnCount | Finns |
|:--- |:--- |:--- |:--- |
| [Azure SQL Database](connector-azure-sql-database.md) | √ | √ | √ |
| [Hanterad azure SQL-databas-instans](connector-azure-sql-database-managed-instance.md) | √ | √ | √ |
| [Azure SQL-datalager](connector-azure-sql-data-warehouse.md) | √ | √ | √ |
| [SQL Server](connector-sql-server.md) | √ | √ | √ |

### <a name="metadata-options"></a>Metadataalternativ

Du kan ange följande metadatatyper i aktivitetsfältslistan Hämta metadata för att hämta motsvarande information:

| Typ av metadata | Beskrivning |
|:--- |:--- |
| itemName (artikelnamn) | Filens eller mappens namn. |
| itemType (artikeltyp) | Typ av fil eller mapp. Returnerat värde `File` `Folder`är eller . |
| size | Filens storlek, i byte. Gäller endast för filer. |
| Skapad | Skapade datetime för filen eller mappen. |
| lastModified | Senast ändrad datetime för filen eller mappen. |
| childItems | Lista över undermappar och filer i den angivna mappen. Gäller endast för mappar. Returnerat värde är en lista över namn och typ av varje underordnat objekt. |
| contentMD5 | MD5 för filen. Gäller endast för filer. |
| Struktur | Datastrukturen i filen eller relationsdatabastabellen. Returnerat värde är en lista med kolumnnamn och kolumntyper. |
| kolumnCount | Antal kolumner i filen eller relationstabellen. |
| Finns| Om det finns en fil, mapp eller en tabell. Observera att `exists` om det anges i listan Hämta metadata misslyckas aktiviteten inte även om filen, mappen eller tabellen inte finns. `exists: false` Returneras i stället i utdata. |

>[!TIP]
>När du vill verifiera att det finns en `exists` fil, mapp eller tabell anger du i listan Hämta metadataaktivitetsfält. Du kan sedan `exists: true/false` kontrollera resultatet i aktivitetsutdata. Om `exists` inte anges i fältlistan misslyckas aktiviteten Hämta metadata om objektet inte hittas.

>[!NOTE]
>När du hämtar metadata från `modifiedDatetimeStart` `modifiedDatetimeEnd`filarkiv och konfigurerar eller innehåller `childItems` utdata endast filer i den angivna sökvägen som har en senast ändrad tid inom det angivna intervallet. I kommer inte att inkludera objekt i undermappar.

## <a name="syntax"></a>Syntax

**Hämta metadataaktivitet**

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

**Datamängd**

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

## <a name="type-properties"></a>Egenskaper för typ

För närvarande kan aktiviteten Hämta metadata returnera följande typer av metadatainformation:

Egenskap | Beskrivning | Krävs
-------- | ----------- | --------
fieldList (fältLista) | De typer av metadatainformation som krävs. Mer information om metadata som stöds finns i avsnittet [Metadataalternativ](#metadata-options) i den här artikeln. | Ja 
Datamängd | Referensdatauppsättningen vars metadata ska hämtas av aktiviteten Hämta metadata. Se avsnittet [Funktioner](#capabilities) för information om anslutningsappar som stöds. Mer information om datauppsättningssyntaxinformation finns i de specifika kopplingsavsnitten. | Ja
formateraInställningar | Använd när du använder formattypsdatauppsättning. | Inga
storeInställningar | Använd när du använder formattypsdatauppsättning. | Inga

## <a name="sample-output"></a>Exempel på utdata

Resultatet hämta metadata visas i aktivitetsutdata. Följande är två exempel som visar omfattande metadata alternativ. Om du vill använda resultaten i en `@{activity('MyGetMetadataActivity').output.itemName}`efterföljande aktivitet använder du det här mönstret: .

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
Lär dig mer om andra kontrollflödesaktiviteter som stöds av Data Factory:

- [Kör pipeline-aktivitet](control-flow-execute-pipeline-activity.md)
- [ForEach-aktivitet](control-flow-for-each-activity.md)
- [Uppslagsaktivitet](control-flow-lookup-activity.md)
- [Webbaktivitet](control-flow-web-activity.md)
