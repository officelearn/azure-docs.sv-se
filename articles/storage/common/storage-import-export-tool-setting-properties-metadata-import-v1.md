---
title: "Ange egenskaper och metadata med hjälp av Azure Import/Export - v1 | Microsoft Docs"
description: "Lär dig mer om att ange egenskaper och metadata anges på mål-blobbar när du kör verktyget Azure Import/Export till förbereda dina enheter. Detta refererar till v1 i verktyget Import/Export."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: e8541695-bcfb-4bf0-84d9-72c9de32a0a8
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: 77bdaa5559de86cd1de9f30e70656e47fd5719e2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="setting-properties-and-metadata-during-the-import-process"></a>Konfigurera egenskaper och metadata under importeringsprocessen
När du kör verktyget Microsoft Azure Import/Export för att förbereda dina enheter, kan du ange egenskaper och metadata på mål-BLOB. Följ de här stegen:  
  
1.  Skapa en textfil på den lokala datorn som anger egenskapsnamn och värden för att ange egenskaper för blob.  
  
2.  Skapa blobbmetadata, skapa en textfil på den lokala datorn som anger metadata namn och värden.  
  
3.  Skicka den fullständiga sökvägen till en eller båda av dessa filer till verktyget Azure Import/Export som en del av den `PrepImport` igen.  
  
> [!NOTE]
>  När du anger en egenskaper eller metadata-fil som en del av en kopia session ange dessa egenskaper eller metadata för varje blob som importeras som en del av den aktuella sessionen kopia. Om du vill ange en annan uppsättning egenskaper eller metadata för några av de blobbar som importeras behöver du skapa en separat kopia-session med olika egenskaper eller metadatafiler.  
  
## <a name="specify-blob-properties-in-a-text-file"></a>Ange Blob-egenskaper i en textfil  
Skapa en lokal textfil för att ange blob-egenskaper, och innehåller XML som anger egenskapsnamn som element och egenskapsvärden som värden. Här är ett exempel som anger vissa egenskapsvärden:  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Properties>  
    <Content-Type>application/octet-stream</Content-Type>  
    <Content-MD5>Q2hlY2sgSW50ZWdyaXR5IQ==</Content-MD5>  
    <Cache-Control>no-cache</Cache-Control>  
</Properties>  
```
  
Spara filen på en lokal plats som `C:\WAImportExport\ImportProperties.txt`.  
  
## <a name="specify-blob-metadata-in-a-text-file"></a>Ange Blobbmetadata i en textfil  
På samma sätt för att ange blobbmetadata, skapa en lokal textfil som anger metadatanamnen som element och metadatavärden som värden. Här är ett exempel som anger vissa metadatavärden:  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Metadata>  
    <UploadMethod>Windows Azure Import/Export service</UploadMethod>  
    <DataSetName>SampleData</DataSetName>  
    <CreationDate>10/1/2013</CreationDate>  
</Metadata>  
```
  
Spara filen på en lokal plats som `C:\WAImportExport\ImportMetadata.txt`.  
  
## <a name="create-a-copy-session-including-the-properties-or-metadata-files"></a>Skapa en kopia Session inklusive egenskaper eller metadatafiler  
När du kör verktyget Azure Import/Export för att förbereda importjobbet ange egenskaper för filen på kommandoraden med den `PropertyFile` parameter. Ange metadatafil på kommandoraden med den `/MetadataFile` parameter. Här är ett exempel som anger båda filerna:  
  
```
WAImportExport.exe PrepImport /j:SecondDrive.jrn /id:BlueRayIso /srcfile:K:\Temp\BlueRay.ISO /dstblob:favorite/BlueRay.ISO /MetadataFile:c:\WAImportExport\SampleMetadata.txt /PropertyFile:c:\WAImportExport\SampleProperties.txt  
```
  
## <a name="next-steps"></a>Nästa steg

* [Import/Export-tjänstens metadata och egenskapers filformat](../storage-import-export-file-format-metadata-and-properties.md)
