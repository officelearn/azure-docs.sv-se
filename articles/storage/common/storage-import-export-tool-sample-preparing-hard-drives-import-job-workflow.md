---
title: Exempelarbetsflöde för att Förbered hårddiskar för ett importjobb för Azure Import/Export | Microsoft Docs
description: Se en genomgång för fullständig processen med att förbereda enheter för ett importjobb i tjänsten Azure Import/Export.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 04/07/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: de2cfbf2b4119daf965c95d8973b8528799c9502
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55454637"
---
# <a name="sample-workflow-to-prepare-hard-drives-for-an-import-job"></a>Exempelarbetsflöde för att förbereda hårddiskar för ett importjobb

Den här artikeln vägleder dig genom processen med att förbereda enheter för ett importjobb.

## <a name="sample-data"></a>Exempeldata

Det här exemplet importerar följande data till ett Azure storage-konto med namnet `mystorageaccount`:

|Plats|Beskrivning|Datastorlek|
|--------------|-----------------|-----|
|H:\Video\ |En samling av videor|12 TB|
|H:\Photo\ |En samling foton|30 GB|
|K:\Temp\FavoriteMovie.ISO|A Blu-ray™ diskavbildning|25 GB|
|\\\bigshare\john\music\ |En samling av musik på en nätverksresurs|10 GB|

## <a name="storage-account-destinations"></a>Mål för Storage-konto

Importjobbet importerar data till med följande mål i storage-konto:

|Källa|Virtuell katalog för mål- eller blob|
|------------|-------------------------------------------|
|H:\Video\ |video/|
|H:\Photo\ |foto /|
|K:\Temp\FavoriteMovie.ISO|favorite/FavoriteMovies.ISO|
|\\\bigshare\john\music\ |Musik|

Med den här mappningen filen `H:\Video\Drama\GreatMovie.mov` kommer att importeras till bloben `https://mystorageaccount.blob.core.windows.net/video/Drama/GreatMovie.mov`.

## <a name="determine-hard-drive-requirements"></a>Fastställa krav för hårddisk

Därefter för att avgöra hur många hårddiskar behövs, beräkna storleken på data:

`12TB + 30GB + 25GB + 10GB = 12TB + 65GB`

Två 8TB-hårddiskar bör vara tillräckliga för det här exemplet. Eftersom källkatalogen `H:\Video` har 12TB data och den enda hårddisken kapaciteten är 8TB, du kommer att kunna ange detta på följande sätt i den **driveset.csv** fil:

```
DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
X,Format,SilentMode,Encrypt,
Y,Format,SilentMode,Encrypt,
```
Verktyget kommer att distribuera data mellan två hårddiskar på ett optimerat sätt.

## <a name="attach-drives-and-configure-the-job"></a>Anslut enheter och konfigurerar jobbet
Du bifoga båda diskarna till datorn och skapa volymer. Sedan skapar **dataset.csv** fil:
```
BasePath,DstBlobPathOrPrefix,BlobType,Disposition,MetadataFile,PropertiesFile
H:\Video\,video/,BlockBlob,rename,None,H:\mydirectory\properties.xml
H:\Photo\,photo/,BlockBlob,rename,None,H:\mydirectory\properties.xml
K:\Temp\FavoriteVideo.ISO,favorite/FavoriteVideo.ISO,BlockBlob,rename,None,H:\mydirectory\properties.xml
\\myshare\john\music\,music/,BlockBlob,rename,None,H:\mydirectory\properties.xml
```

Du kan dessutom ange att följande metadata för alla filer:

* **UploadMethod:** Windows Azure Import/Export-tjänsten
* **DataSetName:** SampleData
* **CreationDate:** 10/1/2013

Ange metadata för de importerade filerna genom att skapa en textfil, `c:\WAImportExport\SampleMetadata.txt`, med följande innehåll:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Metadata>
    <UploadMethod>Windows Azure Import/Export service</UploadMethod>
    <DataSetName>SampleData</DataSetName>
    <CreationDate>10/1/2013</CreationDate>
</Metadata>
```

Du kan också ange vissa egenskaper för den `FavoriteMovie.ISO` blob:

* **Content-Type:** application/octet-ström
* **Innehåll MD5:** Q2hlY2sgSW50ZWdyaXR5IQ==
* **Cache-Control:** no-cache

Skapa en textfil, om du vill ange att egenskaperna `c:\WAImportExport\SampleProperties.txt`:

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

Om några mer data måste du lägga till, skapar du en annan datauppsättning fil (samma format som Initialdataset).

**För den andra sessionen:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
```

När kopia sessioner har slutfört du koppla de två enheterna från datorn som kopia och skickar dem till lämplig Azure-datacentret. Du överföra två journalfiler `<FirstDriveSerialNumber>.xml` och `<SecondDriveSerialNumber>.xml`, när du skapar importjobbet på Azure-portalen.

## <a name="next-steps"></a>Nästa steg

* [Förbereda hårddiskar för ett importjobb](../storage-import-export-tool-preparing-hard-drives-import.md)
* [Snabbreferens för ofta använda kommandon](../storage-import-export-tool-quick-reference.md)
