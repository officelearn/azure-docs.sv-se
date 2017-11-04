---
title: "Exempelarbetsflöde till Förbered dig hårddiskar för ett Azure Import/Export-importjobb | Microsoft Docs"
description: "Se en genomgång för fullständig processen med att förbereda enheter för ett importjobb i tjänsten Azure Import/Export."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/07/2017
ms.author: muralikk
ms.openlocfilehash: 60139ff36b66432620591ceaf201e046ad30217f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="sample-workflow-to-prepare-hard-drives-for-an-import-job"></a>Exempelarbetsflöde för att förbereda hårddiskar för ett importjobb

Den här artikeln vägleder dig genom processen förbereder enheter för importen.

## <a name="sample-data"></a>Exempeldata

Det här exemplet importerar följande data till ett Azure storage-konto med namnet `mystorageaccount`:

|Plats|Beskrivning|Datastorlek|
|--------------|-----------------|-----|
|H:\Video\ |En samling videor|12 TB|
|H:\Photo\ |En samling bilder|30 GB|
|K:\Temp\FavoriteMovie.ISO|A Blu-ray™ diskavbildning|25 GB|
|\\\bigshare\john\music\ |En samling musikfiler på en nätverksresurs|10 GB|

## <a name="storage-account-destinations"></a>Mål för Storage-konto

Importjobbet importerar data till med följande mål i storage-konto:

|Källa|Mål virtuell katalog eller blob|
|------------|-------------------------------------------|
|H:\Video\ |video /|
|H:\Photo\ |foto /|
|K:\Temp\FavoriteMovie.ISO|favorite/FavoriteMovies.ISO|
|\\\bigshare\john\music\ |Musik|

Med den här mappningen filen `H:\Video\Drama\GreatMovie.mov` ska importeras till blob `https://mystorageaccount.blob.core.windows.net/video/Drama/GreatMovie.mov`.

## <a name="determine-hard-drive-requirements"></a>Ange krav för hårddisk

Sedan beräkna storleken på data för att avgöra hur många hårddiskar behövs:

`12TB + 30GB + 25GB + 10GB = 12TB + 65GB`

I det här exemplet är två 8TB-hårddiskar tillräckliga. Eftersom källkatalogen `H:\Video` har 12TB data och den enda hårddisken kapacitet är 8TB, du kommer att kunna ange detta på följande sätt i den **driveset.csv** fil:

```
DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
X,Format,SilentMode,Encrypt,
Y,Format,SilentMode,Encrypt,
```
Verktyget distribuera data på två hårddiskar på ett optimerat sätt.

## <a name="attach-drives-and-configure-the-job"></a>Koppla enheter och konfigurera jobbet
Du ska koppla båda diskarna till datorn och skapa volymer. Sedan redigerar **dataset.csv** fil:
```
BasePath,DstBlobPathOrPrefix,BlobType,Disposition,MetadataFile,PropertiesFile
H:\Video\,video/,BlockBlob,rename,None,H:\mydirectory\properties.xml
H:\Photo\,photo/,BlockBlob,rename,None,H:\mydirectory\properties.xml
K:\Temp\FavoriteVideo.ISO,favorite/FavoriteVideo.ISO,BlockBlob,rename,None,H:\mydirectory\properties.xml
\\myshare\john\music\,music/,BlockBlob,rename,None,H:\mydirectory\properties.xml
```

Dessutom kan du ange följande metadata för alla filer:

* **UploadMethod:** Windows Azure Import/Export service
* **DataSetName:** SampleData
* **CreationDate:** 10/1/2013

Skapa en textfil, om du vill ange metadata för de importerade filerna `c:\WAImportExport\SampleMetadata.txt`, med följande innehåll:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Metadata>
    <UploadMethod>Windows Azure Import/Export service</UploadMethod>
    <DataSetName>SampleData</DataSetName>
    <CreationDate>10/1/2013</CreationDate>
</Metadata>
```

Du kan också ange vissa egenskaper för den `FavoriteMovie.ISO` blob:

* **Content-Type:** program/oktett-ström
* **Content-MD5:** Q2hlY2sgSW50ZWdyaXR5IQ ==
* **Cache-Control:** no-cache

Att ange dessa egenskaper, skapa en textfil `c:\WAImportExport\SampleProperties.txt`:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Properties>
    <Content-Type>application/octet-stream</Content-Type>
    <Content-MD5>Q2hlY2sgSW50ZWdyaXR5IQ==</Content-MD5>
    <Cache-Control>no-cache</Cache-Control>
</Properties>
```

## <a name="run-the-azure-importexport-tool-waimportexportexe"></a>Kör verktyget Azure Import/Export (WAImportExport.exe)

Nu är du redo att köra verktyget Azure Import/Export för att förbereda två hårddiskar.

**För den första sessionen:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:************* /InitialDriveSet:driveset-1.csv /DataSet:dataset-1.csv /logdir:F:\logs
```

Om fler data måste du lägga till, skapar du en annan dataset-fil (samma format som Initialdataset).

**För den andra sessionen:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
```

När kopiera sessioner har slutfört kan du koppla de två enheterna från datorn som kopia och leverera dem till lämpliga Azure-datacentret. Du måste ladda upp två journalfiler `<FirstDriveSerialNumber>.xml` och `<SecondDriveSerialNumber>.xml`när du skapar importjobbet i Azure-portalen.

## <a name="next-steps"></a>Nästa steg

* [Förbereda hårddiskar för ett importjobb](../storage-import-export-tool-preparing-hard-drives-import.md)
* [Snabbreferens för vanliga kommandon](../storage-import-export-tool-quick-reference.md)
