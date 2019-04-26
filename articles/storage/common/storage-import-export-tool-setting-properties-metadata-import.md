---
title: Konfigurera egenskaper och metadata med hjälp av Azure Import/Export | Microsoft Docs
description: Lär dig mer om att ange egenskaper och metadata för att vara inställd på mål-blobbar när du kör verktyget Azure Import/Export förbereda dina enheter.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 3e2796d943fbc4a4ce99576de74b2fc5c77bcca9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60320541"
---
# <a name="setting-properties-and-metadata-during-the-import-process"></a>Konfigurera egenskaper och metadata under importeringsprocessen

När du kör verktyget Microsoft Azure Import/Export för att förbereda dina enheter, kan du ange egenskaper och metadata på blobarna som mål. Följ de här stegen:

1.  Ange blobegenskaper genom att skapa en textfil på den lokala datorn som anger egenskapsnamn och värden.
2.  Skapa en textfil på den lokala datorn som anger metadata namn och värden för att ange blob-metadata.
3.  Skicka den fullständiga sökvägen till en eller båda av dessa filer för Azure Import/Export-verktyget som en del av den `PrepImport` igen.

> [!NOTE]
>  När du anger en egenskaper eller metadata-fil som en del av en kopia session, ange dessa egenskaper eller metadata för varje blob som importeras som en del av den aktuella sessionen kopia. Om du vill ange en annan uppsättning egenskaper eller metadata för några av de blobar som importeras måste du att skapa en separat kopia-session med olika egenskaper eller -metadatafiler.

## <a name="specify-blob-properties-in-a-text-file"></a>Ange blobegenskaper i en textfil

Skapa en lokal textfil för att ange egenskaper för blob, och även XML som anger egenskapsnamn som element och egenskapsvärden som värden. Här är ett exempel som anger vissa egenskapsvärden:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Properties>
    <Content-Type>application/octet-stream</Content-Type>
    <Content-MD5>Q2hlY2sgSW50ZWdyaXR5IQ==</Content-MD5>
    <Cache-Control>no-cache</Cache-Control>
</Properties>
```

Spara filen på en lokal plats som `C:\WAImportExport\ImportProperties.txt`.

## <a name="specify-blob-metadata-in-a-text-file"></a>Ange blob-metadata i en textfil

På samma sätt för att ange blob-metadata, skapa en lokal textfil som anger metadatanamnen som element och metadatavärdena som värden. Här är ett exempel som anger vissa metadatavärden:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Metadata>
    <UploadMethod>Windows Azure Import/Export service</UploadMethod>
    <DataSetName>SampleData</DataSetName>
    <CreationDate>10/1/2013</CreationDate>
</Metadata>
```

Spara filen på en lokal plats som `C:\WAImportExport\ImportMetadata.txt`.

## <a name="add-the-path-to-properties-and-metadata-files-in-datasetcsv"></a>Lägg till sökvägen egenskaper och för metadatafiler i dataset.csv

```
BasePath,DstBlobPathOrPrefix,BlobType,Disposition,MetadataFile,PropertiesFile
H:\Video\,https://mystorageaccount.blob.core.windows.net/video/,BlockBlob,rename,None,H:\mydirectory\properties.xml
H:\Photo\,https://mystorageaccount.blob.core.windows.net/photo/,BlockBlob,rename,None,H:\mydirectory\properties.xml
K:\Temp\FavoriteVideo.ISO,https://mystorageaccount.blob.core.windows.net/favorite/FavoriteVideo.ISO,BlockBlob,rename,None,H:\mydirectory\properties.xml
\\myshare\john\music\,https://mystorageaccount.blob.core.windows.net/music/,BlockBlob,rename,None,H:\mydirectory\properties.xml
```

## <a name="next-steps"></a>Nästa steg

* [Import/Export-tjänstens metadata och egenskapers filformat](../storage-import-export-file-format-metadata-and-properties.md)
