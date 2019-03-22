---
title: Exempelarbetsflöde för att Förbered hårddiskar för ett Azure Import/Export-importjobb - v1 | Microsoft Docs
description: Se en genomgång för fullständig processen med att förbereda enheter för ett importjobb i tjänsten Azure Import/Export.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: b80ba1cbe168270ec591bdd38859408eae387bbf
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/21/2019
ms.locfileid: "58311721"
---
# <a name="sample-workflow-to-prepare-hard-drives-for-an-import-job"></a>Exempelarbetsflöde för att förbereda hårddiskar för ett importjobb
Den här artikeln vägleder dig genom processen med att förbereda enheter för ett importjobb.  
  
Det här exemplet importerar följande data till en Windows Azure-lagringskonto med namnet `mystorageaccount`:  
  
|Plats|Beskrivning|  
|--------------|-----------------|  
|H:\Video|En samling av videor, 5 TB totalt.|  
|H:\Photo|En samling av foton, 30 GB totalt.|  
|K:\Temp\FavoriteMovie.ISO|A Blu-ray™ diskavbildning, 25 GB.|  
|\\\bigshare\john\music|En samling av musik på en nätverksresurs, 10 GB totalt.|  
  
Importjobbet importerar data till med följande mål i storage-konto:  
  
|Källa|Virtuell katalog för mål- eller blob|  
|------------|-------------------------------------------|  
|H:\Video|https:\//mystorageaccount.blob.core.windows.net/video|  
|H:\Photo|https:\//mystorageaccount.blob.core.windows.net/photo|  
|K:\Temp\FavoriteMovie.ISO|https:\//mystorageaccount.blob.core.windows.net/favorite/FavoriteMovies.ISO|  
|\\\bigshare\john\music|https:\//mystorageaccount.blob.core.windows.net/music|  
  
Med den här mappningen filen `H:\Video\Drama\GreatMovie.mov` har importerats till blob-https:\//mystorageaccount.blob.core.windows.net/video/Drama/GreatMovie.mov.  
  
Därefter för att avgöra hur många hårddiskar behövs, beräkna storleken på data:  
  
`5TB + 30GB + 25GB + 10GB = 5TB + 65GB`  
  
I det här exemplet bör två 3 TB-hårddiskar vara tillräckliga. Eftersom källkatalogen `H:\Video` har 5 TB data och den enda hårddisken kapaciteten är bara 3 TB, det är nödvändigt att bryta `H:\Video` i två mindre kataloger: `H:\Video1` och `H:\Video2`, innan du kör programmet Microsoft Azure Import/Export-verktyget. Det här steget ger följande källa kataloger:  
  
|Plats|Storlek|Virtuell katalog för mål- eller blob|  
|--------------|----------|-------------------------------------------|  
|H:\Video1|2,5 TB|https:\//mystorageaccount.blob.core.windows.net/video|  
|H:\Video2|2,5 TB|https:\//mystorageaccount.blob.core.windows.net/video|  
|H:\Photo|30 GB|https:\//mystorageaccount.blob.core.windows.net/photo|  
|K:\Temp\FavoriteMovies.ISO|25 GB|https:\//mystorageaccount.blob.core.windows.net/favorite/FavoriteMovies.ISO|  
|\\\bigshare\john\music|10 GB|https:\//mystorageaccount.blob.core.windows.net/music|  
  
 Även om den `H:\Video`directory har delats till två kataloger som de pekar på samma virtuella målkatalogen i lagringskontot. På så sätt kan alla videofiler bevaras under en enda `video` behållare i lagringskontot.  
  
 Sedan jämnt de tidigare källkatalogerna till två hårddiskar:  
  
||||  
|-|-|-|  
|Hårddisk|Käll-kataloger|Total storlek|  
|Första enhet|H:\Video1|2,5 TB + 30 GB|  
||H:\Photo||  
|Sekund-enhet|H:\Video2|2,5 TB + 35 GB|  
||K:\Temp\BlueRay.ISO||  
||\\\bigshare\john\music||  
  
Du kan dessutom ange att följande metadata för alla filer:  
  
-   **UploadMethod:** Windows Azure Import/Export-tjänsten  
  
-   **DataSetName:** SampleData  
  
-   **CreationDate:** 10/1/2013  
  
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
  
-   **Content-Type:** application/octet-ström  
  
-   **Innehåll MD5:** Q2hlY2sgSW50ZWdyaXR5IQ==  
  
-   **Cache-Control:** no-cache  
  
Skapa en textfil, om du vill ange att egenskaperna `c:\WAImportExport\SampleProperties.txt`:  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Properties>  
    <Content-Type>application/octet-stream</Content-Type>  
    <Content-MD5>Q2hlY2sgSW50ZWdyaXR5IQ==</Content-MD5>  
    <Cache-Control>no-cache</Cache-Control>  
</Properties>  
```
  
Nu är du redo att köra verktyget Azure Import/Export för att förbereda två hårddiskar. Tänk på följande:  
  
-   Den första enheten monteras som enhet X.  
  
-   Den andra enheten monteras som enhet Y.  
  
-   Nyckeln för lagringskontot `mystorageaccount` är `8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg==`.  

## <a name="preparing-disk-for-import-when-data-is-pre-loaded"></a>Förbereder import disk när data har redan lästs in
 
 Om data som ska importeras finns redan på disken, använder du flaggan-/skipwrite. Värdet för /t och /srcdir ska båda pekar på den disk som förbereds för import. Om alla data som ska importeras inte ska till samma mål virtuell katalog eller roten för storage-kontot, köra samma kommando för varje målkatalogen separat, som värdet för /id samma över alla körningar.

>[!NOTE] 
>Ange inte/format eftersom den kommer att radera data på disken. Du kan ange / kryptera eller /bk beroende på om disken redan är krypterade eller inte. 
>

```
    When data is already present on the disk for each drive run the following command.
    WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:Video1 /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:x /format /encrypt /srcdir:x:\Video1 /dstdir:video/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt /skipwrite
```

## <a name="copy-sessions---first-drive"></a>Kopiera sessioner - först enhet

Kör verktyget Azure Import/Export två gånger för att kopiera de två källkatalogerna för den första enheten:  

**Först kopiera session**
  
```
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:Video1 /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:x /format /encrypt /srcdir:H:\Video1 /dstdir:video/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt  
```

**Den andra kopian sessionen**

```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:Photo /srcdir:H:\Photo /dstdir:photo/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt
```

## <a name="copy-sessions---second-drive"></a>Kopiera sessioner - andra enhet
 
Den andra enheten kör du verktyget Azure Import/Export tre gånger, en gång var och en för käll-kataloger och en gång för fristående Blu-Ray™ bildfil):  
  
**Först kopiera session** 

```
WAImportExport.exe PrepImport /j:SecondDrive.jrn /id:Video2 /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:y /format /encrypt /srcdir:H:\Video2 /dstdir:video/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt  
```
  
**Den andra kopian sessionen**

```
WAImportExport.exe PrepImport /j:SecondDrive.jrn /id:Music /srcdir:\\bigshare\john\music /dstdir:music/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt  
```  
  
**Den tredje kopia session**  

```
WAImportExport.exe PrepImport /j:SecondDrive.jrn /id:BlueRayIso /srcfile:K:\Temp\BlueRay.ISO /dstblob:favorite/BlueRay.ISO /MetadataFile:c:\WAImportExport\SampleMetadata.txt /PropertyFile:c:\WAImportExport\SampleProperties.txt  
```

## <a name="copy-session-completion"></a>Kopiera session slutförande

När kopia sessioner har slutfört du koppla de två enheterna från datorn som kopia och skicka dem till lämplig Windows Azure-datacentret. Ladda upp två journalfiler `FirstDrive.jrn` och `SecondDrive.jrn`, när du skapar importjobbet i den [Azure-portalen](https://portal.azure.com).  
  
## <a name="next-steps"></a>Nästa steg

* [Förbereda hårddiskar för ett importjobb](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [Snabbreferens för ofta använda kommandon](../storage-import-export-tool-quick-reference-v1.md) 
