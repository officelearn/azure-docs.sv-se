---
title: "Exempelarbetsflöde till Förbered dig hårddiskar för ett Azure Import/Export-importjobb - v1 | Microsoft Docs"
description: "Se en genomgång för fullständig processen med att förbereda enheter för ett importjobb i tjänsten Azure Import/Export."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 6eb1b1b7-c69f-4365-b5ef-3cd5e05eb72a
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: 66e85bd3e9e43ae360d0507f5bdf3596abbeb7d1
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2017
---
# <a name="sample-workflow-to-prepare-hard-drives-for-an-import-job"></a>Exempelarbetsflöde för att förbereda hårddiskar för ett importjobb
Det här avsnittet vägleder dig genom processen förbereder enheter för importen.  
  
Det här exemplet importerar följande data till en Windows Azure-lagringskonto med namnet `mystorageaccount`:  
  
|Plats|Beskrivning|  
|--------------|-----------------|  
|H:\Video|En samling videor, 5 TB totalt.|  
|H:\Photo|En samling foton, 30 GB totalt.|  
|K:\Temp\FavoriteMovie.ISO|A Blu-ray™ diskavbildning, 25 GB.|  
|\\\bigshare\john\music|En samling av musik på en nätverksresurs, 10 GB totalt.|  
  
Importjobbet importerar data till med följande mål i storage-konto:  
  
|Källa|Mål virtuell katalog eller blob|  
|------------|-------------------------------------------|  
|H:\Video|https://mystorageaccount.BLOB.Core.Windows.NET/video|  
|H:\Photo|https://mystorageaccount.BLOB.Core.Windows.NET/Photo|  
|K:\Temp\FavoriteMovie.ISO|https://mystorageaccount.BLOB.Core.Windows.NET/favorite/FavoriteMovies.ISO|  
|\\\bigshare\john\music|https://mystorageaccount.BLOB.Core.Windows.NET/Music|  
  
Med den här mappningen filen `H:\Video\Drama\GreatMovie.mov` importeras till blob `https://mystorageaccount.blob.core.windows.net/video/Drama/GreatMovie.mov`.  
  
Sedan beräkna storleken på data för att avgöra hur många hårddiskar behövs:  
  
`5TB + 30GB + 25GB + 10GB = 5TB + 65GB`  
  
I det här exemplet är två 3 TB-hårddiskar tillräckliga. Eftersom källkatalogen `H:\Video` har 5 TB data och den enda hårddisken kapacitet är 3 TB, är det nödvändigt att bryta `H:\Video` i två mindre kataloger: `H:\Video1` och `H:\Video2`, innan du kör verktyget Microsoft Azure Import/Export. Det här steget ger följande källa kataloger:  
  
|Plats|Storlek|Mål virtuell katalog eller blob|  
|--------------|----------|-------------------------------------------|  
|H:\Video1|2,5 TB|https://mystorageaccount.BLOB.Core.Windows.NET/video|  
|H:\Video2|2,5 TB|https://mystorageaccount.BLOB.Core.Windows.NET/video|  
|H:\Photo|30 GB|https://mystorageaccount.BLOB.Core.Windows.NET/Photo|  
|K:\Temp\FavoriteMovies.ISO|25 GB|https://mystorageaccount.BLOB.Core.Windows.NET/favorite/FavoriteMovies.ISO|  
|\\\bigshare\john\music|10 GB|https://mystorageaccount.BLOB.Core.Windows.NET/Music|  
  
 Även om den `H:\Video`directory har delats till två kataloger som de pekar på samma virtuella målkatalogen i storage-konto. På så sätt kan alla videofiler bevaras under en enda `video` behållare i lagringskontot.  
  
 Nästa, föregående källa kataloger fördelas jämnt till två hårddiskar:  
  
||||  
|-|-|-|  
|Hårddisk|Källan|Total storlek|  
|Första enhet|H:\Video1|2,5 TB + 30 GB|  
||H:\Photo||  
|Andra enhet|H:\Video2|2,5 TB + 35 GB|  
||K:\Temp\BlueRay.ISO||  
||\\\bigshare\john\music||  
  
Dessutom kan du ange följande metadata för alla filer:  
  
-   **UploadMethod:** Windows Azure Import/Export service  
  
-   **DataSetName:** SampleData  
  
-   **CreationDate:** 10/1/2013  
  
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
  
-   **Content-Type:** program/oktett-ström  
  
-   **Content-MD5:** Q2hlY2sgSW50ZWdyaXR5IQ ==  
  
-   **Cache-Control:** no-cache  
  
Att ange dessa egenskaper, skapa en textfil `c:\WAImportExport\SampleProperties.txt`:  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Properties>  
    <Content-Type>application/octet-stream</Content-Type>  
    <Content-MD5>Q2hlY2sgSW50ZWdyaXR5IQ==</Content-MD5>  
    <Cache-Control>no-cache</Cache-Control>  
</Properties>  
```
  
Nu är du redo att köra verktyget Azure Import/Export för att förbereda två hårddiskar. Tänk på följande:  
  
-   Den första enheten är monterad enhet X.  
  
-   Den andra enheten är monterad enhet Y.  
  
-   Nyckeln för lagringskontot `mystorageaccount` är `8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg==`.  

## <a name="preparing-disk-for-import-when-data-is-pre-loaded"></a>Förbereda disk för import när data har redan lästs in
 
 Om data som ska importeras finns redan på disken, använder du flaggan /skipwrite. Värdet för /t och /srcdir ska båda pekar på den disk som förbereds för import. Om alla data som ska importeras inte ska samma mål virtuell katalog eller roten för lagringskontot, köra samma kommando för varje målkatalogen separat, hålla värdet för /id samma över alla körs.

>[!NOTE] 
>Ange inte/format som den kommer att radera data på disken. Du kan ange / kryptera eller /bk beroende på om disken redan är krypterad eller inte. 
>

```
    When data is already present on the disk for each drive run the following command.
    WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:Video1 /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:x /format /encrypt /srcdir:x:\Video1 /dstdir:video/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt /skipwrite
```

## <a name="copy-sessions---first-drive"></a>Kopiera sessioner - enheten först

Kör verktyget Azure Import/Export två gånger för att kopiera kataloger för två källa för den första enheten:  

**Först kopiera session**
  
```
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:Video1 /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:x /format /encrypt /srcdir:H:\Video1 /dstdir:video/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt  
```

**Den andra kopian sessionen**

```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:Photo /srcdir:H:\Photo /dstdir:photo/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt
```

## <a name="copy-sessions---second-drive"></a>Kopiera sessioner - andra enhet
 
Den andra enheten kör du verktyget Azure Import/Export tre gånger, en gång var och en för käll-kataloger och en gång för avbildningsfilen fristående Blu-Ray™):  
  
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

När kopiera sessioner har slutfört du koppla de två enheterna från datorn som kopia och skicka dem till lämpliga Windows Azure-datacentret. Ladda upp två journalfiler `FirstDrive.jrn` och `SecondDrive.jrn`när du skapar importjobbet i den [Azure-portalen](https://portal.azure.com).  
  
## <a name="next-steps"></a>Nästa steg

* [Förbereda hårddiskar för ett importjobb](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [Snabbreferens för vanliga kommandon](../storage-import-export-tool-quick-reference-v1.md) 
