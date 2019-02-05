---
title: Azure Import/Export-loggfilsformatet | Microsoft Docs
description: Läs mer om formatet för loggfilerna som skapades när steg utförs för ett jobb för Import/Export-tjänsten.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 00e226134039d29efd744290c4bc63abd50adc89
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2019
ms.locfileid: "55697840"
---
# <a name="azure-importexport-service-log-file-format"></a>Azure Import/Export-tjänsten loggfilsformat
När Microsoft Azure Import/Export-tjänsten utför en åtgärd på en enhet som en del av ett importjobb eller ett exportjobb, skrivs loggarna till blockblobbar i lagringskontot som associerats med jobbet.  
  
Det finns två loggar som kan skrivas av Import/Export-tjänsten:  
  
-   Felloggen skapas alltid i händelse av ett fel.  
  
-   Utförlig loggning är inte aktiverat som standard, men kan aktiveras genom att ange den `EnableVerboseLog` egenskapen på en [placera jobbet](/rest/api/storageimportexport/jobs) eller [uppdatering jobbegenskaper](/rest/api/storageimportexport/jobs) igen.  
  
## <a name="log-file-location"></a>Plats för loggfil  
Loggarna skrivs till blockblobbar i behållare eller virtuella katalogen som anges av den `ImportExportStatesPath` som du kan ange en `Put Job` igen. Den plats där loggarna skrivs beror på hur autentisering har angetts för jobbet, tillsammans med det angivna värdet för `ImportExportStatesPath`. Autentisering för jobbet kan anges via en lagringskontonyckel eller en behållare SAS (signatur för delad åtkomst).  
  
Namnet på behållaren eller virtuell katalog kan antingen vara standardnamn `waimportexport`, eller en annan behållare eller virtuella katalognamn som du anger.  
  
Tabellen nedan visar möjliga alternativ:  
  
|Autentiseringsmetod|Värdet på `ImportExportStatesPath`Element|Platsen för Loggblobarna|  
|---------------------------|----------------------------------------------|---------------------------|  
|Lagringskontonyckel|Standardvärde|En behållare med namnet `waimportexport`, vilket är behållare som standard. Exempel:<br /><br /> `https://myaccount.blob.core.windows.net/waimportexport`|  
|Lagringskontonyckel|Användardefinierade värde|En behållare med namnet av användaren. Exempel:<br /><br /> `https://myaccount.blob.core.windows.net/mylogcontainer`|  
|Behållaren SAS|Standardvärde|En virtuell katalog med namnet `waimportexport`, vilket är standardnamnet, under den behållare som angavs i SAS.<br /><br /> Till exempel om SAS som angetts för jobbet är `https://myaccount.blob.core.windows.net/mylogcontainer?sv=2012-02-12&se=2015-05-22T06%3A54%3A55Z&sr=c&sp=wl&sig=sigvalue`, och sedan loggens plats blir `https://myaccount.blob.core.windows.net/mylogcontainer/waimportexport`|  
|Behållaren SAS|Användardefinierade värde|En virtuell katalog med namnet av användaren under den behållare som angavs i SAS.<br /><br /> Till exempel om SAS som angetts för jobbet är `https://myaccount.blob.core.windows.net/mylogcontainer?sv=2012-02-12&se=2015-05-22T06%3A54%3A55Z&sr=c&sp=wl&sig=sigvalue`, och den angivna virtuella katalogen heter `mylogblobs`, loggens plats skulle vara `https://myaccount.blob.core.windows.net/mylogcontainer/waimportexport/mylogblobs`.|  
  
Du kan hämta URL: en för fel och utförliga loggar genom att anropa den [Get Job](/rest/api/storageimportexport/jobs) igen. Loggarna är tillgängliga när bearbetningen av enheten har slutförts.  
  
## <a name="log-file-format"></a>Loggfilsformat  
Formatet för båda loggar är detsamma: en blob som innehåller XML-beskrivningar av de händelser som inträffade vid kopiering av BLOB-objekt mellan hårddisken och på kundens konto.  
  
Utförliga loggen innehåller fullständig information om status för kopieringsåtgärden för varje blob (för ett importjobb) eller en fil (för ett exportjobb), medan felloggen innehåller endast information för BLOB-objekt eller filer som påträffade fel under importen eller export-jobbet.  
  
Utförlig loggformatet visas nedan. Felloggen har samma struktur, men filtrerar ut lyckade åtgärder.  

```xml
<DriveLog Version="2014-11-01">  
  <DriveId>drive-id</DriveId>  
  [<Blob Status="blob-status">  
   <BlobPath>blob-path</BlobPath>  
   <FilePath>file-path</FilePath>  
   [<Snapshot>snapshot</Snapshot>]  
   <Length>length</Length>  
   [<LastModified>last-modified</LastModified>]  
   [<ImportDisposition Status="import-disposition-status">import-disposition</ImportDisposition>]  
   [page-range-list-or-block-list]  
   [metadata-status]  
   [properties-status]  
  </Blob>]  
  [<Blob>  
    . . .  
  </Blob>]  
  <Status>drive-status</Status>  
</DriveLog>  
  
page-range-list-or-block-list ::= 
  page-range-list | block-list  
  
page-range-list ::=   
<PageRangeList>  
      [<PageRange Offset="page-range-offset" Length="page-range-length"   
       [Hash="md5-hash"] Status="page-range-status"/>]  
      [<PageRange Offset="page-range-offset" Length="page-range-length"   
       [Hash="md5-hash"] Status="page-range-status"/>]  
</PageRangeList>  
  
block-list ::=  
<BlockList>  
      [<Block Offset="block-offset" Length="block-length" [Id="block-id"]  
       [Hash="md5-hash"] Status="block-status"/>]  
      [<Block Offset="block-offset" Length="block-length" [Id="block-id"]   
       [Hash="md5-hash"] Status="block-status"/>]  
</BlockList>  
  
metadata-status ::=  
<Metadata Status="metadata-status">  
   [<GlobalPath Hash="md5-hash">global-metadata-file-path</GlobalPath>]  
   [<Path Hash="md5-hash">metadata-file-path</Path>]  
</Metadata>  
  
properties-status ::=  
<Properties Status="properties-status">  
   [<GlobalPath Hash="md5-hash">global-properties-file-path</GlobalPath>]  
   [<Path Hash="md5-hash">properties-file-path</Path>]  
</Properties>  
```

I följande tabell beskriver elementen i loggfilen.  
  
|XML Element|Type|Beskrivning|  
|-----------------|----------|-----------------|  
|`DriveLog`|XML Element|Representerar en logg för enheten.|  
|`Version`|Attribut, sträng|Versionen av loggformatet.|  
|`DriveId`|String|Enhetens serienummer för maskinvara.|  
|`Status`|String|Status för enhet-bearbetning. Se den `Drive Status Codes` tabellen nedan för mer information.|  
|`Blob`|Kapslade XML-element|Representerar en blob.|  
|`Blob/BlobPath`|String|URI för blob.|  
|`Blob/FilePath`|String|Den relativa sökvägen till filen på enheten.|  
|`Blob/Snapshot`|DateTime|Versionen ögonblicksbild av blob för ett exportjobb endast.|  
|`Blob/Length`|Integer|Den totala längden på blob i byte.|  
|`Blob/LastModified`|DateTime|Tidsvärdet blob senast ändrades, för ett exportjobb endast.|  
|`Blob/ImportDisposition`|String|Importera disposition av blob för ett importjobb endast.|  
|`Blob/ImportDisposition/@Status`|Attribut, sträng|Status för import-disposition.|  
|`PageRangeList`|Kapslade XML-element|Representerar en lista över sidintervall för en sidblob.|  
|`PageRange`|XML-element|Representerar ett sidintervall.|  
|`PageRange/@Offset`|Integer-attribut|Starta en förskjutning av sidintervallet i blobben.|  
|`PageRange/@Length`|Integer-attribut|Längden i byte på sidintervallet.|  
|`PageRange/@Hash`|Attribut, sträng|Base16-kodad MD5-hashen för sidintervallet.|  
|`PageRange/@Status`|Attribut, sträng|Status för bearbetning av sidintervallet.|  
|`BlockList`|Kapslade XML-element|Representerar en lista över block för en blockblob.|  
|`Block`|XML-element|Representerar ett block.|  
|`Block/@Offset`|Integer-attribut|Starta en förskjutning av block i blobben.|  
|`Block/@Length`|Integer-attribut|Längden i byte på blocket.|  
|`Block/@Id`|Attribut, sträng|Block-ID.|  
|`Block/@Hash`|Attribut, sträng|Base16-kodad MD5-hashen för blocket.|  
|`Block/@Status`|Attribut, sträng|Status för bearbetning av blocket.|  
|`Metadata`|Kapslade XML-element|Representerar den blob-metadata.|  
|`Metadata/@Status`|Attribut, sträng|Status för bearbetning av blobmetadata.|  
|`Metadata/GlobalPath`|String|Relativ sökväg till globala metadatafilen.|  
|`Metadata/GlobalPath/@Hash`|Attribut, sträng|Base16-kodad MD5-hashen för global metadatafilen.|  
|`Metadata/Path`|String|Relativ sökväg till metadatafilen.|  
|`Metadata/Path/@Hash`|Attribut, sträng|Base16-kodad MD5-hash filens metadata.|  
|`Properties`|Kapslade XML-element|Representerar blobegenskaper.|  
|`Properties/@Status`|Attribut, sträng|Status för bearbetning av blobegenskaper, t.ex. inte att hitta filen, har slutförts.|  
|`Properties/GlobalPath`|String|Relativ sökväg till filen globala egenskaper.|  
|`Properties/GlobalPath/@Hash`|Attribut, sträng|Base16-kodad MD5-hashen för den globala egenskaper-filen.|  
|`Properties/Path`|String|Relativ sökväg till filen egenskaper.|  
|`Properties/Path/@Hash`|Attribut, sträng|Base16-kodad MD5-hash filens egenskaper.|  
|`Blob/Status`|String|Status för bearbetning av blobben.|  
  
## <a name="drive-status-codes"></a>Statuskoder för enhet  
I följande tabell visas statuskoder för bearbetning av en enhet.  
  
|Statuskod|Beskrivning|  
|-----------------|-----------------|  
|`Completed`|Enheten har bearbetats utan fel.|  
|`CompletedWithWarnings`|Enheten har bearbetats med varningar i en eller flera blobbar per import-disposition som angetts för blobar.|  
|`CompletedWithErrors`|Enheten har slutförts med fel i en eller flera blobbar eller segment.|  
|`DiskNotFound`|Ingen disk hittas på enheten.|  
|`VolumeNotNtfs`|Första datavolym på disken är inte i NTFS-format.|  
|`DiskOperationFailed`|Ett okänt fel uppstod när du utför åtgärder på enheten.|  
|`BitLockerVolumeNotFound`|Ingen encryptable volym BitLocker finns.|  
|`BitLockerNotActivated`|BitLocker är inte aktiverat på volymen.|  
|`BitLockerProtectorNotFound`|Nyckelskydd för numeriskt lösenord finns inte på volymen.|  
|`BitLockerKeyInvalid`|Det angivna numeriska lösenordet inte låsa upp volymen.|  
|`BitLockerUnlockVolumeFailed`|Okänt fel uppstod vid försök att låsa upp volymen.|  
|`BitLockerFailed`|Ett okänt fel uppstod vid BitLocker-åtgärder.|  
|`ManifestNameInvalid`|Manifestfilens filnamn är ogiltig.|  
|`ManifestNameTooLong`|Manifestfilens filnamn är för långt.|  
|`ManifestNotFound`|Manifestfilen hittades inte.|  
|`ManifestAccessDenied`|Åtkomst till manifestfilen nekas.|  
|`ManifestCorrupted`|Manifestfilen är skadad (innehållet matchar inte dess hash).|  
|`ManifestFormatInvalid`|Manifest innehållet stämmer inte överens med formatet som krävs.|  
|`ManifestDriveIdMismatch`|Enhets-ID i manifestfilen matchar inte en läsning från enheten.|  
|`ReadManifestFailed`|En disk-i/o-fel uppstod vid läsning från manifestet.|  
|`BlobListFormatInvalid`|Exportera blob lista blob stämmer inte överens med formatet som krävs.|  
|`BlobRequestForbidden`|Åtkomst till blobar i lagringskontot är förbjuden. Detta kan bero på ogiltiga lagringskontonyckel eller SAS-behållare.|  
|`InternalError`|Och internt fel inträffade vid bearbetning av enheten.|  
  
## <a name="blob-status-codes"></a>BLOB-statuskoder  
I följande tabell visas statuskoder för bearbetning av en blob.  
  
|Statuskod|Beskrivning|  
|-----------------|-----------------|  
|`Completed`|Blobben har bearbetats utan fel.|  
|`CompletedWithErrors`|Blobben har bearbetats med fel i en eller flera sidor eller block, metadata eller egenskaper.|  
|`FileNameInvalid`|Filnamnet är ogiltigt.|  
|`FileNameTooLong`|Filnamnet är för långt.|  
|`FileNotFound`|Filen hittades inte.|  
|`FileAccessDenied`|Åtkomst till filen nekas.|  
|`BlobRequestFailed`|Blob service-begäran att få åtkomst till bloben har misslyckats.|  
|`BlobRequestForbidden`|Blob serviceförfrågan om att få åtkomst till bloben är förbjuden. Detta kan bero på ogiltiga lagringskontonyckel eller SAS-behållare.|  
|`RenameFailed`|Det gick inte att byta namn på blob (för ett importjobb) eller filen (för ett exportjobb).|  
|`BlobUnexpectedChange`|Oväntade ändringar har uppstått med blob (för ett exportjobb).|  
|`LeasePresent`|Det finns ett lån som finns på blob.|  
|`IOFailed`|En felaktig disk eller i/o-fel inträffade när bloben.|  
|`Failed`|Ett okänt fel uppstod under bearbetning av blobben.|  
  
## <a name="import-disposition-status-codes"></a>Importera disposition statuskoder  
I följande tabell visas statuskoder för att lösa en disposition för import.  
  
|Statuskod|Beskrivning|  
|-----------------|-----------------|  
|`Created`|Blobben har skapats.|  
|`Renamed`|Blobben har bytt per Byt namn på import disposition. Den `Blob/BlobPath` elementet innehåller URI för omdöpt blob.|  
|`Skipped`|Blobben har hoppats över `no-overwrite` importera disposition.|  
|`Overwritten`|Blobben har över en befintlig blob per `overwrite` importera disposition.|  
|`Cancelled`|Ett fel som tidigare har stoppats vidare bearbetning av import-disposition.|  
  
## <a name="page-rangeblock-status-codes"></a>Statuskoder för sidan intervall/block  
I följande tabell visas statuskoder för bearbetning av ett sidintervall eller ett block.  
  
|Statuskod|Beskrivning|  
|-----------------|-----------------|  
|`Completed`|Sidintervall eller blockera har bearbetats utan fel.|  
|`Committed`|Blocket har allokerats, men i blocket fullständiga listan eftersom andra block har misslyckats eller placera fullständig Blockeringslista själva misslyckades inte.|  
|`Uncommitted`|Blocket laddat upp men inte allokerad.|  
|`Corrupted`|Sidan intervall eller block är skadad (innehållet matchar inte dess hash).|  
|`FileUnexpectedEnd`|Ett oväntat filslut påträffades.|  
|`BlobUnexpectedEnd`|Det inträffade ett oväntat slut på blob.|  
|`BlobRequestFailed`|Blob service-begäran att komma åt sidan intervall eller block har misslyckats.|  
|`IOFailed`|En felaktig disk eller i/o-fel inträffade när sidan intervall eller block.|  
|`Failed`|Ett okänt fel uppstod vid bearbetning av sidan intervall eller block.|  
|`Cancelled`|Ett tidigare fel har stoppats vidare bearbetning av sidan intervall eller block.|  
  
## <a name="metadata-status-codes"></a>Statuskoder för metadata  
I följande tabell visas statuskoder för bearbetning av blob-metadata.  
  
|Statuskod|Beskrivning|  
|-----------------|-----------------|  
|`Completed`|Metadata har bearbetats utan fel.|  
|`FileNameInvalid`|Metadata-filnamnet är ogiltigt.|  
|`FileNameTooLong`|Metadata-filnamnet är för långt.|  
|`FileNotFound`|Metadatafilen hittades inte.|  
|`FileAccessDenied`|Åtkomst till metadatafilen nekas.|  
|`Corrupted`|Metadatafilen är skadad (innehållet matchar inte dess hash).|  
|`XmlReadFailed`|Metadata-innehållet följer inte formatet som krävs.|  
|`XmlWriteFailed`|Skriva metadata misslyckades XML.|  
|`BlobRequestFailed`|Blob service-begäran att få åtkomst till metadata har misslyckats.|  
|`IOFailed`|En felaktig disk eller i/o-fel inträffade när metadata.|  
|`Failed`|Ett okänt fel uppstod vid bearbetning av metadata.|  
|`Cancelled`|Ett fel som tidigare har stoppats vidare bearbetning av metadata.|  
  
## <a name="properties-status-codes"></a>Statuskoder för egenskaper  
I följande tabell visas statuskoder för bearbetning av blobegenskaper.  
  
|Statuskod|Beskrivning|  
|-----------------|-----------------|  
|`Completed`|Egenskaperna har bearbetats utan fel.|  
|`FileNameInvalid`|Egenskaper för filnamnet är ogiltigt.|  
|`FileNameTooLong`|Egenskaper för filnamnet är för långt.|  
|`FileNotFound`|Egenskaper för filen hittades inte.|  
|`FileAccessDenied`|Åtkomst till filen egenskaper nekas.|  
|`Corrupted`|Egenskaper för filen är skadad (innehållet matchar inte dess hash).|  
|`XmlReadFailed`|Egenskaper för innehållet stämmer inte överens med formatet som krävs.|  
|`XmlWriteFailed`|Skriva egenskaper misslyckades XML.|  
|`BlobRequestFailed`|Blob service-begäran till egenskaperna har misslyckats.|  
|`IOFailed`|En felaktig disk eller i/o-fel uppstod vid bearbetning av egenskaperna.|  
|`Failed`|Ett okänt fel uppstod vid bearbetning av egenskaperna.|  
|`Cancelled`|Ett fel som tidigare har stoppats vidare bearbetning av egenskaperna.|  
  
## <a name="sample-logs"></a>Exempelloggar  
Följande är ett exempel på utförlig loggning.  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<DriveLog Version="2014-11-01">  
    <DriveId>WD-WMATV123456</DriveId>  
    <Blob Status="Completed">  
       <BlobPath>pictures/bob/wild/desert.jpg</BlobPath>  
       <FilePath>\Users\bob\Pictures\wild\desert.jpg</FilePath>  
       <Length>98304</Length>  
       <ImportDisposition Status="Created">overwrite</ImportDisposition>  
       <BlockList>  
          <Block Offset="0" Length="65536" Id="AAAAAA==" Hash=" 9C8AE14A55241F98533C4D80D85CDC68" Status="Completed"/>  
          <Block Offset="65536" Length="32768" Id="AQAAAA==" Hash=" DF54C531C9B3CA2570FDDDB3BCD0E27D" Status="Completed"/>  
       </BlockList>  
       <Metadata Status="Completed">  
          <GlobalPath Hash=" E34F54B7086BCF4EC1601D056F4C7E37">\Users\bob\Pictures\wild\metadata.xml</GlobalPath>  
       </Metadata>  
    </Blob>  
    <Blob Status="CompletedWithErrors">  
       <BlobPath>pictures/bob/animals/koala.jpg</BlobPath>  
       <FilePath>\Users\bob\Pictures\animals\koala.jpg</FilePath>  
       <Length>163840</Length>  
       <ImportDisposition Status="Overwritten">overwrite</ImportDisposition>  
       <PageRangeList>  
          <PageRange Offset="0" Length="65536" Hash="19701B8877418393CB3CB567F53EE225" Status="Completed"/>  
          <PageRange Offset="65536" Length="65536" Hash="AA2585F6F6FD01C4AD4256E018240CD4" Status="Corrupted"/>  
          <PageRange Offset="131072" Length="4096" Hash="9BA552E1C3EEAFFC91B42B979900A996" Status="Completed"/>  
       </PageRangeList>  
       <Properties Status="Completed">  
          <Path Hash="38D7AE80653F47F63C0222FEE90EC4E7">\Users\bob\Pictures\animals\koala.jpg.properties</Path>  
       </Properties>  
    </Blob>  
    <Status>CompletedWithErrors</Status>  
</DriveLog>  
```  
  
Motsvarande felloggen visas nedan.  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<DriveLog Version="2014-11-01">  
    <DriveId>WD-WMATV6965824</DriveId>  
    <Blob Status="CompletedWithErrors">  
       <BlobPath>pictures/bob/animals/koala.jpg</BlobPath>  
       <FilePath>\Users\bob\Pictures\animals\koala.jpg</FilePath>  
       <Length>163840</Length>  
       <ImportDisposition Status="Overwritten">overwrite</ImportDisposition>  
       <PageRangeList>  
          <PageRange Offset="65536" Length="65536" Hash="AA2585F6F6FD01C4AD4256E018240CD4" Status="Corrupted"/>  
       </PageRangeList>  
    </Blob>  
    <Status>CompletedWithErrors</Status>  
</DriveLog>  
```

 Följ felloggen för ett importjobb innehåller ett fel om en fil inte hittas på enheten för import. Observera att statusen för efterföljande komponenter är `Cancelled`.  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveLog Version="2014-11-01">  
  <DriveId>9WM35C2V</DriveId>  
  <Blob Status="FileNotFound">  
    <BlobPath>pictures/animals/koala.jpg</BlobPath>  
    <FilePath>\animals\koala.jpg</FilePath>  
    <Length>30310</Length>  
    <ImportDisposition Status="Cancelled">rename</ImportDisposition>  
    <BlockList>  
      <Block Offset="0" Length="6062" Id="MD5/cAzn4h7VVSWXf696qp5Uaw==" Hash="700CE7E21ED55525977FAF7AAA9E546B" Status="Cancelled" />  
      <Block Offset="6062" Length="6062" Id="MD5/PEnGwYOI8LPLNYdfKr7kAg==" Hash="3C49C6C18388F0B3CB35875F2ABEE402" Status="Cancelled" />  
      <Block Offset="12124" Length="6062" Id="MD5/FG4WxqfZKuUWZ2nGTU2qVA==" Hash="146E16C6A7D92AE5166769C64D4DAA54" Status="Cancelled" />  
      <Block Offset="18186" Length="6062" Id="MD5/ZzibNDzr3IRBQENRyegeXQ==" Hash="67389B343CEBDC8441404351C9E81E5D" Status="Cancelled" />  
      <Block Offset="24248" Length="6062" Id="MD5/ZzibNDzr3IRBQENRyegeXQ==" Hash="67389B343CEBDC8441404351C9E81E5D" Status="Cancelled" />  
    </BlockList>  
  </Blob>  
  <Status>CompletedWithErrors</Status>  
</DriveLog>  
```

I följande felloggen för ett exportjobb anger att blobinnehållet har skrivits till enheten, men att ett fel uppstod vid export av den blob-egenskaper.  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveLog Version="2014-11-01">  
  <DriveId>9WM35C3U</DriveId>  
  <Blob Status="CompletedWithErrors">  
    <BlobPath>pictures/wild/canyon.jpg</BlobPath>  
    <FilePath>\pictures\wild\canyon.jpg</FilePath>  
    <LastModified>2012-09-18T23:47:08Z</LastModified>  
    <Length>163840</Length>  
    <BlockList />  
    <Properties Status="Failed" />  
  </Blob>  
  <Status>CompletedWithErrors</Status>  
</DriveLog>  
```
  
## <a name="next-steps"></a>Nästa steg
 
* [REST-API för Storage Import/Export](/rest/api/storageimportexport/)
