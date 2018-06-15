---
title: Azure Import/Export-loggfilsformatet | Microsoft Docs
description: Mer information om formatet för loggfilerna som skapades när åtgärder utförs för ett jobb med Import/Export.
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: ''
ms.assetid: 38cc16bd-ad55-4625-9a85-e1726c35fd1b
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: 16234ccaf13ce1d85cfd207ed4734e683070faa6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23874103"
---
# <a name="azure-importexport-service-log-file-format"></a>Azure Import/Export service loggfilsformat
När tjänsten Microsoft Azure Import/Export utför en åtgärd på en enhet som en del av ett importjobb eller ett exportjobb skrivs loggarna till blockblobbar i storage-konto som är associerade med jobbet.  
  
Det finns två loggar som kan skrivas av tjänsten Import/Export:  
  
-   Felloggen skapas alltid i händelse av ett fel.  
  
-   Utförlig loggen är inte aktiverad som standard, men kan aktiveras genom att ange den `EnableVerboseLog` egenskapen på en [placera jobbet](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) eller [uppdatering jobbegenskaper](/rest/api/storageimportexport/jobs#Jobs_Update) igen.  
  
## <a name="log-file-location"></a>Plats för loggfilen  
Loggarna skrivs till blockblobbar i behållare eller virtuella katalogen som anges av den `ImportExportStatesPath` inställning som du kan ange för en `Put Job` igen. Den plats som loggarna skrivs beror på hur autentisering har angetts för jobbet, tillsammans med det angivna värdet för `ImportExportStatesPath`. Autentisering för jobbet kan anges via en lagringskontonyckel eller en behållare SAS (signatur för delad åtkomst).  
  
Namnet på behållaren eller virtuell katalog kan antingen vara standardnamnet `waimportexport`, eller en annan behållare eller en virtuell katalog som du anger.  
  
Tabellen nedan visar möjliga alternativ:  
  
|Autentiseringsmetod|Värdet för `ImportExportStatesPath`Element|Plats för logg-BLOB|  
|---------------------------|----------------------------------------------|---------------------------|  
|Lagringskontonyckel|Standardvärde|En behållare med namnet `waimportexport`, vilket är standardbehållaren. Exempel:<br /><br /> `https://myaccount.blob.core.windows.net/waimportexport`|  
|Lagringskontonyckel|Värdet som angetts av användaren|En behållare med namnet av användaren. Exempel:<br /><br /> `https://myaccount.blob.core.windows.net/mylogcontainer`|  
|Behållaren SAS|Standardvärde|En virtuell katalog med namnet `waimportexport`, vilket är standardnamnet under den behållare som angavs i SAS.<br /><br /> Till exempel om SAS som angetts för jobbet är `https://myaccount.blob.core.windows.net/mylogcontainer?sv=2012-02-12&se=2015-05-22T06%3A54%3A55Z&sr=c&sp=wl&sig=sigvalue`, loggplatsen skulle vara`https://myaccount.blob.core.windows.net/mylogcontainer/waimportexport`|  
|Behållaren SAS|Värdet som angetts av användaren|En virtuell katalog med namnet av användaren under den behållare som angavs i SAS.<br /><br /> Till exempel om SAS som angetts för jobbet är `https://myaccount.blob.core.windows.net/mylogcontainer?sv=2012-02-12&se=2015-05-22T06%3A54%3A55Z&sr=c&sp=wl&sig=sigvalue`, och den angivna virtuella katalogen heter `mylogblobs`, loggplatsen skulle vara `https://myaccount.blob.core.windows.net/mylogcontainer/waimportexport/mylogblobs`.|  
  
Du kan hämta Webbadressen för fel och utförlig loggar genom att anropa den [Get Job](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) igen. Loggarna är tillgängliga när bearbetningen av enheten har slutförts.  
  
## <a name="log-file-format"></a>Loggfilsformat  
Formatet för båda loggarna är samma: en blob som innehåller XML-beskrivningar av de händelser som inträffade vid kopiering av BLOB mellan hårddisken och på kundens konto.  
  
Den detaljerade loggen innehåller fullständig information om status för kopieringen för varje blob (för ett importjobb) eller filen (för ett exportjobb), felloggen innehåller endast information för blobbar eller filer som påträffade fel under importen eller Exportera jobb.  
  
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

I följande tabell beskrivs elementen i loggfilen.  
  
|XML-Element|Typ|Beskrivning|  
|-----------------|----------|-----------------|  
|`DriveLog`|XML-Element|Representerar en logg för enheten.|  
|`Version`|Attributet sträng|Versionen av loggformatet.|  
|`DriveId`|Sträng|Enhetens serienummer för maskinvara.|  
|`Status`|Sträng|Status för enheten bearbetning. Finns det `Drive Status Codes` tabellen nedan för mer information.|  
|`Blob`|Kapslade XML-element|Representerar en blob.|  
|`Blob/BlobPath`|Sträng|Blob-URI.|  
|`Blob/FilePath`|Sträng|Den relativa sökvägen till filen på enheten.|  
|`Blob/Snapshot`|Datum och tid|Versionen av blob för ett exportjobb endast ögonblicksbild.|  
|`Blob/Length`|Integer|Den totala längden på blob i byte.|  
|`Blob/LastModified`|Datum och tid|Tidsvärdet som blob ändrades senast, för ett exportjobb endast.|  
|`Blob/ImportDisposition`|Sträng|Importera disposition på blob för en importjobb.|  
|`Blob/ImportDisposition/@Status`|Attributet sträng|Status för import-disposition.|  
|`PageRangeList`|Kapslade XML-element|Representerar en lista över sidintervall för en sidblobb.|  
|`PageRange`|XML-element|Representerar ett sidintervall.|  
|`PageRange/@Offset`|Attributet heltal|Första förskjutningen av sidintervallet i blob.|  
|`PageRange/@Length`|Attributet heltal|Längden i byte på sidintervallet.|  
|`PageRange/@Hash`|Attributet sträng|Base16-kodade MD5-hash för sidintervallet.|  
|`PageRange/@Status`|Attributet sträng|Status för bearbetning av sidintervallet.|  
|`BlockList`|Kapslade XML-element|Representerar en lista över block för en blockblob.|  
|`Block`|XML-element|Representerar ett block.|  
|`Block/@Offset`|Attributet heltal|Första förskjutningen av block i blob.|  
|`Block/@Length`|Attributet heltal|Längden i byte av blocket.|  
|`Block/@Id`|Attributet sträng|Block-ID.|  
|`Block/@Hash`|Attributet sträng|Base16-kodade MD5-hash av blocket.|  
|`Block/@Status`|Attributet sträng|Status för bearbetning av blocket.|  
|`Metadata`|Kapslade XML-element|Representerar det blob-metadata.|  
|`Metadata/@Status`|Attributet sträng|Status för bearbetning av blobmetadata.|  
|`Metadata/GlobalPath`|Sträng|Relativ sökväg till filen globala metadata.|  
|`Metadata/GlobalPath/@Hash`|Attributet sträng|Base16-kodade MD5-hash för den globala metadatafilen.|  
|`Metadata/Path`|Sträng|Relativ sökväg till metadatafilen.|  
|`Metadata/Path/@Hash`|Attributet sträng|Base16-kodade MD5-hash för metadatafilen.|  
|`Properties`|Kapslade XML-element|Representerar blob-egenskaper.|  
|`Properties/@Status`|Attributet sträng|Status för bearbetning av blob-egenskaper, t.ex. inte att hitta filen, slutförts.|  
|`Properties/GlobalPath`|Sträng|Relativ sökväg till filen globala egenskaper.|  
|`Properties/GlobalPath/@Hash`|Attributet sträng|Base16-kodade MD5 hash för den globala egenskaper för filen.|  
|`Properties/Path`|Sträng|Relativ sökväg till filen egenskaper.|  
|`Properties/Path/@Hash`|Attributet sträng|Base16-kodade MD5-hash för filen egenskaper.|  
|`Blob/Status`|Sträng|Status för bearbetning av blob.|  
  
# <a name="drive-status-codes"></a>Statuskoder för enhet  
I följande tabell visas statuskoder för bearbetning av en enhet.  
  
|Statuskod|Beskrivning|  
|-----------------|-----------------|  
|`Completed`|Enheten har bearbetat utan fel.|  
|`CompletedWithWarnings`|Enheten har slutförts med varningar i en eller flera blobbar per import-disposition som angetts för den BLOB-objekt.|  
|`CompletedWithErrors`|Enheten har slutförts med fel i en eller flera blobbar segment.|  
|`DiskNotFound`|Ingen disk finns på enheten.|  
|`VolumeNotNtfs`|Den första datavolymen på disken är inte i NTFS-format.|  
|`DiskOperationFailed`|Ett okänt fel uppstod när du utför åtgärder på enheten.|  
|`BitLockerVolumeNotFound`|Ingen encryptable volym BitLocker finns.|  
|`BitLockerNotActivated`|BitLocker är inte aktiverat på volymen.|  
|`BitLockerProtectorNotFound`|Nyckelskydd för numeriskt lösenord finns inte på volymen.|  
|`BitLockerKeyInvalid`|Det angivna numeriska lösenordet går inte att låsa upp volymen.|  
|`BitLockerUnlockVolumeFailed`|Okänt fel uppstod vid försök att låsa upp volymen.|  
|`BitLockerFailed`|Ett okänt fel uppstod vid BitLocker-åtgärder.|  
|`ManifestNameInvalid`|Manifestfilens namn är ogiltigt.|  
|`ManifestNameTooLong`|Manifestfilens filnamn är för långt.|  
|`ManifestNotFound`|Manifestfilen hittades inte.|  
|`ManifestAccessDenied`|Åtkomst till manifestfilen nekas.|  
|`ManifestCorrupted`|Manifestfilen är skadad (innehållet matchar inte dess hash).|  
|`ManifestFormatInvalid`|Manifestet innehållet överensstämmer inte med formatet som krävs.|  
|`ManifestDriveIdMismatch`|Enhetsidentitet i manifestfilen överensstämmer inte med en läsningen från enheten.|  
|`ReadManifestFailed`|En disk-i/o-fel uppstod vid läsning från manifestet.|  
|`BlobListFormatInvalid`|Exportera blob listan blob stämmer inte överens med formatet som krävs.|  
|`BlobRequestForbidden`|Åtkomst till blobarna i lagringskontot är förbjuden. Detta kan bero på ogiltiga lagringskontonyckel eller SAS-behållare.|  
|`InternalError`|Och internt fel uppstod under bearbetning av enheten.|  
  
## <a name="blob-status-codes"></a>BLOB-statuskoder  
I följande tabell visas statuskoder för bearbetning av en blob.  
  
|Statuskod|Beskrivning|  
|-----------------|-----------------|  
|`Completed`|Blobben har bearbetat utan fel.|  
|`CompletedWithErrors`|Blobben har bearbetat med fel i en eller flera sidintervall block, metadata eller egenskaper.|  
|`FileNameInvalid`|Filnamnet är ogiltigt.|  
|`FileNameTooLong`|Filnamnet är för långt.|  
|`FileNotFound`|Filen hittades inte.|  
|`FileAccessDenied`|Åtkomst till filen nekades.|  
|`BlobRequestFailed`|Det gick inte att tjänstbegäran Blob för att få åtkomst till blob.|  
|`BlobRequestForbidden`|Blob-tjänsten åtkomstbegäran till blob tillåts inte. Detta kan bero på ogiltiga lagringskontonyckel eller SAS-behållare.|  
|`RenameFailed`|Det gick inte att byta namn på blob (för ett importjobb) eller filen (för ett exportjobb).|  
|`BlobUnexpectedChange`|En oväntad ändring uppstod med blobben (för ett exportjobb).|  
|`LeasePresent`|Det finns ett lån som finns på blob.|  
|`IOFailed`|Ett disk- eller i/o-fel uppstod under bearbetning av blobben.|  
|`Failed`|Ett okänt fel uppstod under bearbetning av blobben.|  
  
## <a name="import-disposition-status-codes"></a>Importera disposition statuskoder  
I följande tabell visas statuskoder för att lösa ett import-disposition.  
  
|Statuskod|Beskrivning|  
|-----------------|-----------------|  
|`Created`|Blobben har skapats.|  
|`Renamed`|Blobben har bytt per Byt namn på import disposition. Den `Blob/BlobPath` elementet innehåller URI för bytt namn till blob.|  
|`Skipped`|Blobben har hoppats över `no-overwrite` importera disposition.|  
|`Overwritten`|Blobben har skrivit över en befintlig blob per `overwrite` importera disposition.|  
|`Cancelled`|Ett tidigare fel har stoppats vidare bearbetning av disposition för import.|  
  
## <a name="page-rangeblock-status-codes"></a>Statuskoder för sidan intervall /-block  
I följande tabell visas statuskoder för bearbetning av ett sidintervall eller ett block.  
  
|Statuskod|Beskrivning|  
|-----------------|-----------------|  
|`Completed`|Sidan intervall eller block har bearbetat utan fel.|  
|`Committed`|Blocket har allokerats, men i fullständig blocket listan eftersom andra block har misslyckats eller placera fullständig Blockeringslista själva misslyckades inte.|  
|`Uncommitted`|Blocket har överförts men inte allokerad.|  
|`Corrupted`|Sidan intervall eller block är skadad (innehållet matchar inte dess hash).|  
|`FileUnexpectedEnd`|Ett oväntat filslut påträffades.|  
|`BlobUnexpectedEnd`|Det inträffade ett oväntat slut på blob.|  
|`BlobRequestFailed`|Det gick inte att tjänstbegäran Blob för att komma åt sidan intervall eller block.|  
|`IOFailed`|Ett disk- eller i/o-fel uppstod vid bearbetningen av sidan intervall eller block.|  
|`Failed`|Ett okänt fel uppstod under bearbetning av sidan intervall eller block.|  
|`Cancelled`|Ett tidigare fel har stoppats vidare bearbetning av sidan intervall eller block.|  
  
## <a name="metadata-status-codes"></a>Statuskoder för metadata  
I följande tabell visas statuskoder för bearbetning av blobbmetadata.  
  
|Statuskod|Beskrivning|  
|-----------------|-----------------|  
|`Completed`|Metadata har bearbetat utan fel.|  
|`FileNameInvalid`|Metadata-filnamnet är ogiltigt.|  
|`FileNameTooLong`|Metadata-filnamnet är för långt.|  
|`FileNotFound`|Metadatafilen hittades inte.|  
|`FileAccessDenied`|Åtkomst till metadatafilen nekas.|  
|`Corrupted`|Metadatafilen är skadad (innehållet matchar inte dess hash).|  
|`XmlReadFailed`|Metadata-innehållet överensstämmer inte med formatet som krävs.|  
|`XmlWriteFailed`|Skriva metadata misslyckades XML.|  
|`BlobRequestFailed`|Det gick inte att tjänstbegäran Blob för att komma åt metadata.|  
|`IOFailed`|Ett disk- eller i/o-fel uppstod under bearbetning av metadata.|  
|`Failed`|Ett okänt fel uppstod under bearbetning av metadata.|  
|`Cancelled`|Ett tidigare fel har stoppats vidare bearbetning av metadata.|  
  
## <a name="properties-status-codes"></a>Statuskoder för egenskaper  
I följande tabell visas statuskoder för bearbetning av blob-egenskaper.  
  
|Statuskod|Beskrivning|  
|-----------------|-----------------|  
|`Completed`|Egenskaperna är klar bearbetning utan fel.|  
|`FileNameInvalid`|Egenskaper för filnamnet är ogiltigt.|  
|`FileNameTooLong`|Egenskaper för filnamn är för lång.|  
|`FileNotFound`|Egenskaper för filen hittades inte.|  
|`FileAccessDenied`|Nekad åtkomst till filen egenskaper.|  
|`Corrupted`|Egenskaper för filen är skadad (innehållet matchar inte dess hash).|  
|`XmlReadFailed`|Egenskaper för innehållet överensstämmer inte med formatet som krävs.|  
|`XmlWriteFailed`|Skriva egenskaperna misslyckades XML.|  
|`BlobRequestFailed`|Det gick inte att tjänstbegäran Blob för att komma åt egenskaperna.|  
|`IOFailed`|Ett disk- eller i/o-fel uppstod under bearbetning av egenskaperna.|  
|`Failed`|Ett okänt fel uppstod under bearbetning av egenskaperna.|  
|`Cancelled`|Ett tidigare fel har stoppats ytterligare bearbetning av egenskaperna.|  
  
## <a name="sample-logs"></a>Exempel loggar  
Följande är ett exempel på detaljerade loggen.  
  
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

 Följ felloggen för importen innehåller ett fel om en fil inte hittas på enheten för import. Observera att status för efterföljande komponenter är `Cancelled`.  
  
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

Följande felloggen för ett exportjobb anger att blob-innehållet har skrivits till enheten, utan att ett fel inträffade vid export av blob-egenskaper.  
  
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
