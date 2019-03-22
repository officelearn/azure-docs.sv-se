---
title: Förbereda hårddiskar för ett Azure Import/Export-importjobb - v1 | Microsoft Docs
description: Lär dig att förbereda hårddiskar använda WAImportExport v1-verktyget för att skapa ett importjobb för tjänsten Azure Import/Export.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 03b504524b2f489f1ee042c6e825ccffe0a60bb3
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/21/2019
ms.locfileid: "58315070"
---
# <a name="preparing-hard-drives-for-an-import-job"></a>Förbereda hårddiskar för ett importjobb
Följ dessa steg för att förbereda en eller flera hårddiskar för ett importjobb:

- Identifiera data som ska importeras till Blob-tjänsten

- Identifiera mål virtuella kataloger och blobar i Blob-tjänsten

- Fastställa hur många enheter som du behöver

- Kopiera data till var och en av dina hårddiskar

  Läs ett Exempelarbetsflöde [Exempelarbetsflöde för att förbereda hårddiskar för ett importjobb](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow-v1.md).

## <a name="identify-the-data-to-be-imported"></a>Identifiera vilka data som ska importeras
 Det första steget för att skapa ett importjobb är att avgöra vilka kataloger och filer som du tänker importera. Detta kan vara en lista över kataloger, en lista med unika filer eller en kombination av dessa två. När en katalog finns kommer alla filer i katalogen och dess underkataloger att ingå i importjobbet.

> [!NOTE]
>  Eftersom underkataloger finns inkluderade rekursivt när en överordnad katalog finns, ange endast den överordnade katalogen. Också ange inte någon av dess underkataloger.
>
>  För närvarande Microsoft Azure Import/Export-verktyget har följande begränsningar: om en katalog innehåller mer data än vad en hårddisk kan innehålla, katalogen måste delas i mindre kataloger. Till exempel om en katalog innehåller 2,5 TB data och -hårddiskkapacitet är endast 2TB, måste du dela katalogen 2,5 TB i mindre kataloger. Den här begränsningen kommer att åtgärdas i en senare version av verktyget.

## <a name="identify-the-destination-locations-in-the-blob-service"></a>Identifiera målplatserna i blobtjänsten
 Du behöver identifiera en virtuell katalog för mål eller en blob i Azure Blob service för varje katalog eller en fil som ska importeras. Du använder dessa mål som indata för Azure Import/Export-verktyget. Observera att kataloger bör vara avgränsade med snedstreck tecknet ”/”.

 I följande tabell visas några exempel på blob-mål:

|Käll-filen eller katalogen|Målblobben eller virtuell katalog|
|------------------------------|-------------------------------------------|
|H:\Video|https:\//mystorageaccount.blob.core.windows.net/video|
|H:\Photo|https:\//mystorageaccount.blob.core.windows.net/photo|
|K:\Temp\FavoriteVideo.ISO|https:\//mystorageaccount.blob.core.windows.net/favorite/FavoriteVideo.ISO|
|\\\myshare\john\music|https:\//mystorageaccount.blob.core.windows.net/music|

## <a name="determine-how-many-drives-are-needed"></a>Avgör hur många enheter som krävs
 Därefter måste du fastställa:

- Antalet hårddiskar som behövs för att lagra data.

- Den kataloger och/eller fristående filer som ska kopieras till var och en på hårddisken.

  Kontrollera att antalet hårddiskar som du behöver för att lagra de data som överförs.

## <a name="copy-data-to-your-hard-drive"></a>Kopieringsdata på hårddisken
 Det här avsnittet beskrivs hur du anropar Azure Import/Export-verktyget för att kopiera dina data till en eller flera hårddiskar. Varje gång du anropar Azure Import/Export-verktyget kan du skapa en ny *kopiera session*. Du skapar minst en kopia session för varje enhet som du kopiera data. i vissa fall kan behöva mer än en kopia session att kopiera alla dina data till en enda enhet. Här följer några orsaker till att du kan behöva flera kopia sessioner:

-   Du måste skapa en separat kopia session för varje enhet som du kopierar till.

-   En session för kopiering kan kopiera en enskild katalog eller en enda blob till enheten. Om du kopierar flera kataloger, flera BLOB-objekt eller en kombination av båda kommer du behöva skapa flera kopia sessioner.

-   Du kan ange egenskaper och metadata som kan ställas in på de blobar som har importerats som en del av ett importjobb. De egenskaper eller metadata som du anger för en session för kopiering gäller för alla blobar som anges av den aktuella sessionen kopia. Om du vill ange olika egenskaper eller metadata för vissa BLOB-objekt behöver du skapa en separat kopia-session. Se [egenskaper och Metadata under importeringsprocessen](storage-import-export-tool-setting-properties-metadata-import-v1.md)för mer information.

> [!NOTE]
>  Om du har flera datorer som uppfyller kraven som anges i [inställningen in the Azure Import/Export-verktyget](storage-import-export-tool-setup-v1.md), du kan kopiera data till flera hårddiskar parallellt genom att köra en instans av det här verktyget på varje dator.

 För varje hårddisk som du förbereder med verktyget Azure Import/Export-skapar verktyget en enda journalfil. Du behöver journalfilerna från alla enheter att skapa importjobbet. Journalfilen kan också användas för att återuppta förberedelsen av enheten om verktyget avbryts.

### <a name="azure-importexport-tool-syntax-for-an-import-job"></a>Azure Import/Export-verktyget syntax för ett importjobb
 Förbered enheter för ett importjobb genom att anropa Azure Import/Export-verktyget med den **PrepImport** kommando. Vilka parametrar som du inkluderar beror på om det här är den första kopia sessionen eller en efterföljande kopia session.

 Den första kopia sessionen för en enhet kräver vissa ytterligare parametrar för att ange lagringskontonyckeln; mål-enhetsbeteckning; Om enheten måste vara formaterad; Om enheten måste krypteras och i så fall BitLocker-nyckel. och till loggkatalogen. Här är syntaxen för en inledande kopia-session att kopiera en katalog eller en enskild fil:

 **Först kopiera session för att kopiera en enskild katalog**

 `WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]`

 **Först kopiera session för att kopiera en fil**

 `WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcfile:<SourceFile> /dstblob:<DestinationBlobPath> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]`

 I efterföljande kopia sessioner behöver du inte ange inledande parametrar. Här är syntaxen för en efterföljande kopia-session för att kopiera en katalog eller en enda fil:

 **Efterföljande kopia sessioner att kopiera en enskild katalog**

 `WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]`

 **Efterföljande kopia sessioner att kopiera en fil**

 `WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /srcfile:<SourceFile> /dstblob:<DestinationBlobPath> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]`

### <a name="parameters-for-the-first-copy-session-for-a-hard-drive"></a>Parametrar för den första kopia sessionen för en hårddisk
 Varje gång du kör verktyget Azure Import/Export för att kopiera filer till hårddisken, skapar verktyget en kopia-session. Varje kopia-sessionen kopierar en enskild katalog eller en enda fil till en hårddisk. Tillståndet för sessionen kopia skrivs till filen ändringsjournalen. Om en kopia-sessionen avbryts (till exempel på grund av strömavbrott system), kan den återupptas genom att köra verktyget igen och ange journalfil på kommandoraden.

> [!WARNING]
>  Om du anger den **/formatera** parametern för den första kopia sessionen enheten formateras och alla data på enheten raderas. Du rekommenderas att du använder tomma enheter endast för din session för kopiering.

 Det kommando som används för den första kopia sessionen för varje enhet kräver olika parametrar än kommandona för efterföljande kopia sessioner. I följande tabell visas de ytterligare parametrar som är tillgängliga för den första kopia-sessionen:

|Kommandoradsparametern|Beskrivning|
|-----------------------------|-----------------|
|**/sk:**<StorageAccountKey\>|`Optional.` Lagringskontots åtkomstnyckel för lagringskontot som data ska importeras. Du måste innehålla antingen **/sk:**< StorageAccountKey\> eller **/csas:**< ContainerSas\> i kommandot.|
|**/csas:**< ContainerSas\>|`Optional`. Behållaren SAS du använder för att importera data till lagringskontot. Du måste innehålla antingen **/sk:**< StorageAccountKey\> eller **/csas:**< ContainerSas\> i kommandot.<br /><br /> Värdet för den här parametern måste börja med behållarens namn följt av ett frågetecken (?) och SAS-token. Exempel:<br /><br /> `mycontainer?sv=2014-02-14&sr=c&si=abcde&sig=LiqEmV%2Fs1LF4loC%2FJs9ZM91%2FkqfqHKhnz0JM6bqIqN0%3D&se=2014-11-20T23%3A54%3A14Z&sp=rwdl`<br /><br /> Behörighet, om anges i URL: en eller i en lagrad åtkomstprincip, måste innehålla läsa, skriva och ta bort för importjobb, och Läs-, Skriv- och listan av export-jobb.<br /><br /> När den här parametern anges, måste alla BLOB-och importeras eller exporteras vara inom den behållare som angavs i signaturen för delad åtkomst.|
|**/t:**<TargetDriveLetter\>|`Required.` Enhetsbeteckningen för target hårddisken för den aktuella kopia-sessionen utan avslutande kolon.|
|**/ format**|`Optional.` Ange den här parametern när enheten måste vara formaterad; i annat fall utelämna den. Innan verktyget formaterar enheten, uppmanas du en bekräftelse från konsolen. Ange parametern /silentmode för att ignorera bekräftelsen.|
|**/silentmode**|`Optional.` Ange den här parametern om du vill ignorera bekräftelse för att formatera målenheten.|
|**/ kryptera**|`Optional.` Ange den här parametern när enheten inte har ännu krypterats med BitLocker och måste krypteras av verktyget. Om enheten har redan har krypterats med BitLocker, utelämnar den här parametern och ange den `/bk` parameter, vilket ger befintliga BitLocker-nyckel.<br /><br /> Om du anger den `/format` parametern och du måste också ange den `/encrypt` parametern.|
|**/bk:**<BitLockerKey\>|`Optional.` Om `/encrypt` är anges utelämna den här parametern. Om `/encrypt` är utelämnas, måste du ha har redan krypterat enheten med BitLocker. Använd den här parametern för att ange BitLocker-nyckel. BitLocker-kryptering krävs för alla hårddiskar för importjobb.|
|**/logdir:**< LogDirectory\>|`Optional.` Loggkatalogen anger en katalog som används för att lagra utförliga loggar samt tillfällig manifestfiler. Om den inte anges används den aktuella katalogen som log-katalogen.|

### <a name="parameters-required-for-all-copy-sessions"></a>Parametrar som krävs för alla kopia sessioner
 Journal-fil innehåller statusen för alla kopia sessioner för en hårddisk. Den innehåller också information som behövs för att skapa importjobbet. Du måste alltid ange en journalfil när du kör verktyget Azure Import/Export, samt en kopia sessions-ID:

|||
|-|-|
|Kommandoradsparametern|Beskrivning|
|**/j:**<JournalFile\>|`Required.` Sökvägen till filen ändringsjournalen. Varje enhet måste ha exakt en journalfil. Observera att journalfilen inte måste finnas på målenheten. Filnamnstillägget journalen `.jrn`.|
|**/id:**<SessionId\>|`Required.` Sessions-ID identifierar en kopia-session. Den används för att säkerställa att korrekt återställning av en session avbryts kopia. Filer som kopieras i en session för kopiering lagras i en katalog med namnet efter sessions-ID på målenheten.|

### <a name="parameters-for-copying-a-single-directory"></a>Parametrar för att kopiera en enskild katalog
 När du kopierar en enskild katalog, gäller följande obligatoriska och valfria parametrar:

|Kommandoradsparametern|Beskrivning|
|----------------------------|-----------------|
|**/srcdir:**< SourceDirectory\>|`Required.` Källkatalogen som innehåller filer som ska kopieras till målenheten. Katalogsökvägen måste vara en absolut sökväg (inte en relativ sökväg).|
|**/dstdir:**<DestinationBlobVirtualDirectory\>|`Required.` Sökvägen till den virtuella målkatalogen i Windows Azure storage-kontot. Den virtuella katalogen kanske eller kanske inte redan finns.<br /><br /> Du kan ange en behållare eller ett blob-prefix som `music/70s/`. Målkatalogen måste börja med behållarens namn följt av ett snedstreck ”/”, och du kan också omfatta en virtuell blob-katalog som slutar med ”/”.<br /><br /> När till Målbehållaren är Rotbehållare, måste du uttryckligen ange root-behållaren, inklusive snedstreck, som `$root/`. Eftersom blobar i behållaren för roten inte får innehålla ”/” i sina namn, kopieras inte eventuella underkataloger i källkatalogen när målkatalogen är Rotbehållare.<br /><br /> Glöm inte att använda giltiga behållarnamn när du anger målet virtuella kataloger eller blobar. Tänk på att behållarnamn måste vara gemener. Namngivningsregler för behållaren, se [namngivning och referens av behållare, Blobar och Metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).|
|**/ Disposition:**< Byt namn på&#124;nr skriva över&#124;skriva över >|`Optional.` Anger beteendet när en blob med den angivna adressen finns redan. Giltiga värden för den här parametern är: `rename`, `no-overwrite` och `overwrite`. Observera att dessa värden är skiftlägeskänsliga. Om inget värde anges är standardvärdet `rename`.<br /><br /> Värdet som angetts för den här parametern påverkar alla filer i katalogen som anges av den `/srcdir` parametern.|
|**/BlobType:**<BlockBlob&#124;PageBlob>|`Optional.` Anger blobbtypen för mål-BLOB. Giltiga värden är: `BlockBlob` och `PageBlob`. Observera att dessa värden är skiftlägeskänsliga. Om inget värde anges är standardvärdet `BlockBlob`.<br /><br /> I de flesta fall `BlockBlob` rekommenderas. Om du anger `PageBlob`, längden på varje fil i katalogen måste vara en multipel av 512, storleken på en sida för sidblobar.|
|**/ PropertyFile:**< PropertyFile\>|`Optional.` Sökvägen till filen egenskapen för mål-BLOB. Se [Import/Export-tjänsten Metadata och egenskaper filformat](../storage-import-export-file-format-metadata-and-properties.md) för mer information.|
|**/ MetadataFile:**< MetadataFile\>|`Optional.` Sökvägen till metadatafilen för mål-BLOB. Se [Import/Export-tjänsten Metadata och egenskaper filformat](../storage-import-export-file-format-metadata-and-properties.md) för mer information.|

### <a name="parameters-for-copying-a-single-file"></a>Parametrar för att kopiera en fil
 När du kopierar en fil, gäller följande obligatoriska och valfria parametrar:

|Kommandoradsparametern|Beskrivning|
|----------------------------|-----------------|
|**/srcfile:**<SourceFile\>|`Required.` Den fullständiga sökvägen till filen som ska kopieras. Katalogsökvägen måste vara en absolut sökväg (inte en relativ sökväg).|
|**/dstblob:**<DestinationBlobPath\>|`Required.` Sökvägen till målblobben i Windows Azure storage-kontot. Blobben kanske eller kanske inte redan finns.<br /><br /> Ange den blob namn som början med behållarens namn. Blob-namnet får inte börja med ”/” eller namnet på lagringskontot. Regler för namngivning blob, se [namngivning och referens av behållare, Blobar och Metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).<br /><br /> När till Målbehållaren är Rotbehållare, måste du uttryckligen ange `$root` som behållare, till exempel `$root/sample.txt`. Observera att BLOB-objekt i root-behållaren får inte innehålla ”/” i sina namn.|
|**/ Disposition:**< Byt namn på&#124;nr skriva över&#124;skriva över >|`Optional.` Anger beteendet när en blob med den angivna adressen finns redan. Giltiga värden för den här parametern är: `rename`, `no-overwrite` och `overwrite`. Observera att dessa värden är skiftlägeskänsliga. Om inget värde anges är standardvärdet `rename`.|
|**/BlobType:**<BlockBlob&#124;PageBlob>|`Optional.` Anger blobbtypen för mål-BLOB. Giltiga värden är: `BlockBlob` och `PageBlob`. Observera att dessa värden är skiftlägeskänsliga. Om inget värde anges är standardvärdet `BlockBlob`.<br /><br /> I de flesta fall `BlockBlob` rekommenderas. Om du anger `PageBlob`, längden på varje fil i katalogen måste vara en multipel av 512, storleken på en sida för sidblobar.|
|**/ PropertyFile:**< PropertyFile\>|`Optional.` Sökvägen till filen egenskapen för mål-BLOB. Se [Import/Export-tjänsten Metadata och egenskaper filformat](../storage-import-export-file-format-metadata-and-properties.md) för mer information.|
|**/ MetadataFile:**< MetadataFile\>|`Optional.` Sökvägen till metadatafilen för mål-BLOB. Se [Import/Export-tjänsten Metadata och egenskaper filformat](../storage-import-export-file-format-metadata-and-properties.md) för mer information.|

### <a name="resuming-an-interrupted-copy-session"></a>Återuppta en session avbryts kopia
 Om en kopia-sessionen avbryts av någon anledning, kan du återuppta den genom att köra verktyget med endast journalfil anges:

```
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /ResumeSession
```

 Endast den senaste kopia sessionen, kan om avslutades oväntat, återupptas.

> [!IMPORTANT]
>  När du återuppta en session för kopiera, ändra inte Källdatafiler och kataloger genom att lägga till eller ta bort filer.

### <a name="aborting-an-interrupted-copy-session"></a>Avbryter en session avbryts kopia
 Om en kopia-sessionen avbryts och det går inte att återuppta (till exempel om en källkatalog visat sig vara otillgängligt), måste du avbryta den aktuella sessionen så att den kan återställas och den nya kopian sessioner kan startas:

```
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AbortSession
```

 Om avslutades oväntat, kan bara den senaste kopian sessionen, avbrytas. Observera att du inte kan avbryta den första kopia sessionen för en enhet. I stället måste du starta om sessionen kopia med en ny journalfil.

## <a name="next-steps"></a>Nästa steg

* [Konfigurera Azure Import/Export-verktyget](storage-import-export-tool-setup-v1.md)
* [Konfigurera egenskaper och metadata under importeringsprocessen](storage-import-export-tool-setting-properties-metadata-import-v1.md)
* [Exempelarbetsflöde för att förbereda hårddiskar för ett importjobb](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow-v1.md)
* [Snabbreferens för ofta använda kommandon](storage-import-export-tool-quick-reference-v1.md) 
* [Granska jobbstatus med kopiera loggfiler](storage-import-export-tool-reviewing-job-status-v1.md)
* [Reparera ett importjobb](storage-import-export-tool-repairing-an-import-job-v1.md)
* [Reparera ett exportjobb](storage-import-export-tool-repairing-an-export-job-v1.md)
* [Felsökning av Azures import-/exportverktyg](storage-import-export-tool-troubleshooting-v1.md)
