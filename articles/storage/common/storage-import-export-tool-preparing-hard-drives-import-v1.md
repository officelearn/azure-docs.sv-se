---
title: "Förbereda hårddiskar för ett Azure Import/Export-importjobb - v1 | Microsoft Docs"
description: "Lär dig hur du förbereder hårddiskar med hjälp av verktyget WAImportExport v1 för att skapa ett importjobb för tjänsten Azure Import/Export."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 3d818245-8b1b-4435-a41f-8e5ec1f194b2
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.openlocfilehash: 361e16262e528c7dea1bab4b9d945a28af8be399
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="preparing-hard-drives-for-an-import-job"></a>Förbereda hårddiskar för ett importjobb
Följ dessa steg för att förbereda en eller flera hårddiskar för importen:

-   Identifiera vilka data som ska importeras till Blob-tjänsten

-   Identifiera mål virtuella kataloger respektive blobbar i Blob-tjänsten

-   Avgöra hur många enheter som du behöver

-   Kopiera data till var och en av dina hårddiskar

 För ett Exempelarbetsflöde finns [Exempelarbetsflöde att förbereda hårddiskar för ett importjobb](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow-v1.md).

## <a name="identify-the-data-to-be-imported"></a>Identifiera vilka data som ska importeras
 Det första steget att skapa ett importjobb är att avgöra vilka kataloger och filer som du ska importera. Detta kan vara en lista över kataloger, en lista med unika filer eller en kombination av dessa två. När en katalog finns ska alla filer i katalogen och dess underkataloger ingå i importjobbet.

> [!NOTE]
>  Eftersom underkataloger är inkluderade rekursivt när en överordnad katalog finns, ange bara den överordnade katalogen. Även ange inte någon av dess underkataloger.
>
>  För närvarande Microsoft Azure Import/Export-verktyget har följande begränsningar: om en katalog innehåller fler data än en hårddisk kan innehålla, katalogen måste delas i mindre kataloger. Till exempel om en katalog innehåller 2,5 TB data och -hårddiskkapacitet är endast 2TB, måste du dela upp katalogen 2,5 TB i mindre kataloger. Den här begränsningen åtgärdas i en senare version av verktyget.

## <a name="identify-the-destination-locations-in-the-blob-service"></a>Identifiera målplatser i blob-tjänsten
 Du behöver identifiera en destination virtuell katalog eller en blobb i Azure Blob-tjänsten för varje katalog eller fil som ska importeras. Du använder dessa mål som indata till verktyget Azure Import/Export. Observera att kataloger ska avgränsas med snedstreck tecknet ”/”.

 I följande tabell visas några exempel på blob mål:

|Käll-filen eller katalogen|Mål-blob eller virtuell katalog|
|------------------------------|-------------------------------------------|
|H:\Video|https://mystorageaccount.BLOB.Core.Windows.NET/video|
|H:\Photo|https://mystorageaccount.BLOB.Core.Windows.NET/Photo|
|K:\Temp\FavoriteVideo.ISO|https://mystorageaccount.BLOB.Core.Windows.NET/favorite/FavoriteVideo.ISO|
|\\\myshare\john\music|https://mystorageaccount.BLOB.Core.Windows.NET/Music|

## <a name="determine-how-many-drives-are-needed"></a>Avgör hur många enheter som krävs
 Därefter måste du fastställa:

-   Antalet hårddiskar som behövs för att lagra data.

-   Den kataloger och/eller fristående filer som ska kopieras till var och en av hårddisken.

 Kontrollera att antalet hårddiskar som du behöver för att lagra data som överförs.

## <a name="copy-data-to-your-hard-drive"></a>Kopiera data till hårddisken
 Det här avsnittet beskrivs hur du anropar verktyget Azure Import/Export för att kopiera data till en eller flera hårddiskar. Varje gång du anropar verktyget Azure Import/Export kan du skapa en ny *kopiera session*. Du skapar minst en kopia sessionen för varje enhet som du kopierar data. i vissa fall kan behöva du mer än en kopia session att kopiera alla data till en enhet. Här följer några orsaker till att du kanske måste flera kopiera sessioner:

-   Du måste skapa en separat kopia-session för varje enhet som du kopierar till.

-   En kopia session kan kopiera en katalog eller en enda blob till enheten. Om du kopierar flera kataloger, flera blobbar eller en kombination av båda behöver du skapa flera kopiera sessioner.

-   Du kan ange egenskaper och metadata som ställs in på blobbar importeras som en del av ett importjobb. De egenskaper eller metadata som du anger för en kopia session gäller för alla BLOB som anges av den aktuella sessionen kopia. Om du vill ange olika egenskaper eller metadata för vissa BLOB behöver du skapa en separat kopia-session. Se [egenskaper och Metadata under importen](storage-import-export-tool-setting-properties-metadata-import-v1.md)för mer information.

> [!NOTE]
>  Om du har flera datorer som uppfyller kraven som anges i [ställa in Azure Import/Export verktyget](storage-import-export-tool-setup-v1.md), du kan kopiera data till flera hårddiskar parallellt genom att köra en instans av det här verktyget på varje dator.

 För varje hårddisk som du förbereder med verktyget Azure Import/Export skapar verktyget en enda journal-fil. Du behöver journal-filer från alla enheter att skapa importjobbet. Journal-fil kan också användas för att återuppta förberedelse av enheten om verktyget avbryts.

### <a name="azure-importexport-tool-syntax-for-an-import-job"></a>Azure Import/Export-verktyget syntaxen för ett importjobb
 Om du vill förbereda enheter för ett importjobb anropa verktyget Azure Import/Export med den **PrepImport** kommando. Vilka parametrar du inkludera beror på om detta är den första kopia sessionen eller en efterföljande kopiera session.

 Den första kopia sessionen för en enhet kräver vissa ytterligare parametrar för att ange lagringskontonyckel; mål-enhetsbeteckning; Om enheten måste vara formaterad; Om enheten måste krypteras och i så fall BitLocker-nyckel. och log-katalogen. Här följer syntaxen för en inledande kopia-session att kopiera en katalog eller en enskild fil:

 **Först kopiera sessionen att kopiera en katalog**

 `WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]`

 **Först kopiera sessionen att kopiera en fil**

 `WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcfile:<SourceFile> /dstblob:<DestinationBlobPath> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]`

 Du behöver inte ange inledande parametrar i efterföljande kopiera sessioner. Här följer syntaxen för en efterföljande kopia-session att kopiera en katalog eller en enskild fil:

 **Efterföljande kopiera sessioner att kopiera en katalog**

 `WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]`

 **Efterföljande kopiera sessioner att kopiera en fil**

 `WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /srcfile:<SourceFile> /dstblob:<DestinationBlobPath> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]`

### <a name="parameters-for-the-first-copy-session-for-a-hard-drive"></a>Parametrar för den första kopia sessionen för en hårddisk
 Varje gång du kör verktyget Azure Import/Export för att kopiera filer till hårddisken, skapar verktyget en kopia-session. Varje kopia session kopierar en katalog eller en fil till en hårddisk. Kopiera sessionens status skrivs till journalfilen. Om en kopia sessionen avbryts (t.ex, på grund av strömavbrott system), kan du återuppta den genom att köra verktyget igen och ange journal-fil på kommandoraden.

> [!WARNING]
>  Om du anger den **/formatera** parameter för den första kopia sessionen enheten formateras och raderas alla data på enheten. Det rekommenderas att du använder tomma enheter endast för kopian sessionen.

 Det kommando som används för den första kopia sessionen för varje enhet kräver olika parametrar än kommandona för efterföljande kopiera sessioner. I följande tabell visas de ytterligare parametrar som är tillgängliga för den första kopia sessionen:

|Kommandoradsparametern|Beskrivning|
|-----------------------------|-----------------|
|**/Sk:**< StorageAccountKey\>|`Optional.`Lagringskontots åtkomstnyckel för lagringskontot som data ska importeras. Du måste inkludera antingen **/sk:**< StorageAccountKey\> eller **/csas:**< ContainerSas\> i kommandot.|
|**/csas:**< ContainerSas\>|`Optional`. Behållaren SAS du använder för att importera data till lagringskontot. Du måste inkludera antingen **/sk:**< StorageAccountKey\> eller **/csas:**< ContainerSas\> i kommandot.<br /><br /> Värdet för den här parametern måste börja med behållarens namn följt av ett frågetecken (?) och SAS-token. Exempel:<br /><br /> `mycontainer?sv=2014-02-14&sr=c&si=abcde&sig=LiqEmV%2Fs1LF4loC%2FJs9ZM91%2FkqfqHKhnz0JM6bqIqN0%3D&se=2014-11-20T23%3A54%3A14Z&sp=rwdl`<br /><br /> Behörigheterna om anges på URL: en eller i en lagrad åtkomstprincip, måste innehålla läsa, skriva och ta bort för importjobb och läsa, skriva och lista för exportjobb.<br /><br /> När den här parametern anges måste alla BLOB importeras eller exporteras ligga inom den behållare som angavs i signatur för delad åtkomst.|
|**/ t:**< TargetDriveLetter\>|`Required.`Enhetsbeteckningen för målet hårddisk för den aktuella kopia sessionen, utan avslutande kolon.|
|**/ format**|`Optional.`Ange den här parametern när enheten måste vara formaterad; Annars utelämna den. Innan verktyget loggfilsformat enheten uppmanas en bekräftelse från konsolen. Ange parametern /silentmode för att ignorera bekräftelsen.|
|**/silentmode**|`Optional.`Ange den här parametern om du vill ignorera bekräftelse för att formatera targert enhet.|
|**/ encrypt**|`Optional.`Ange den här parametern när enheten har ännu inte har krypterats med BitLocker och måste krypteras av verktyget. Om enheten redan har krypterats med BitLocker utelämna parametern och ange den `/bk` parameter för att tillhandahålla den befintliga nyckeln för BitLocker.<br /><br /> Om du anger den `/format` parameter och du måste också ange den `/encrypt` parameter.|
|**/BK:**< BitLockerKey\>|`Optional.`Om `/encrypt` har angetts utesluter den här parametern. Om `/encrypt` är utelämnas, måste du ha har redan krypterat enheten med BitLocker. Använd den här parametern om du vill ange BitLocker-nyckel. BitLocker-kryptering krävs för alla hårddiskar för importjobb.|
|**/logdir:**< LogDirectory\>|`Optional.`Loggkatalogen som anger en katalog som används för att lagra utförliga loggar som tillfällig manifest-filer. Om inget anges används den aktuella katalogen som loggkatalogen.|

### <a name="parameters-required-for-all-copy-sessions"></a>Parametrar som krävs för alla kopiera sessioner
 Journal-fil innehåller statusen för alla sessioner med kopia för en hårddisk. Det innehåller även information som behövs för att skapa importjobbet. Du måste alltid ange en journal-fil när du kör verktyget Azure Import/Export, samt en kopia sessions-ID:

|||
|-|-|
|Kommandoradsparameter|Beskrivning|
|**/j:**< JournalFile\>|`Required.`Sökvägen till journalfilen. Varje enhet måste ha exakt en journal-fil. Observera att journal-fil inte måste finnas på målenheten. Filnamnstillägget journal är `.jrn`.|
|**/ID:**< sessions-ID\>|`Required.`Sessions-ID identifierar en kopia-session. Den används för att se till att korrekt återställning av en session avbryts kopia. Filer som kopieras i en kopia session lagras i katalogen efter sessions-ID på målenheten.|

### <a name="parameters-for-copying-a-single-directory"></a>Parametrar för att kopiera en katalog
 När du kopierar en katalog, gäller följande obligatoriska och valfria parametrar:

|Kommandoradsparameter|Beskrivning|
|----------------------------|-----------------|
|**/srcdir:**< SourceDirectory\>|`Required.`Källkatalog som innehåller filer som ska kopieras till målenheten. Sökvägen måste vara en absolut sökväg (inte en relativ sökväg).|
|**/dstdir:**< DestinationBlobVirtualDirectory\>|`Required.`Sökvägen till den virtuella målkatalogen i ditt Windows Azure storage-konto. Den virtuella katalogen kanske eller kanske inte redan finns.<br /><br /> Du kan ange en behållare eller ett blob-prefix som `music/70s/`. Målkatalogen måste börja med behållarens namn följt av ett snedstreck ”/”, och du kan också omfatta en virtuell blob-katalog som slutar med ”/”.<br /><br /> När Målbehållaren är Rotbehållare, måste du uttryckligen ange Rotbehållare, inklusive snedstreck, som `$root/`. Eftersom blobbar i behållaren roten inte får innehålla ”/” i sina namn, kopieras inte eventuella underkataloger i källkatalogen när målkatalogen är Rotbehållare.<br /><br /> Se till att använda giltig behållarnamn när du anger målet virtuella kataloger eller BLOB. Tänk på att behållarnamn måste vara gemener. Behållaren namngivningsregler, se [namnge och referera till behållare, Blobbar och Metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).|
|**/ Disposition:**< Byt namn på &#124; Nej skriva över &#124; skriva över >|`Optional.`Anger beteendet när det finns redan en blob med den angivna adressen. Giltiga värden för den här parametern är: `rename`, `no-overwrite` och `overwrite`. Observera att dessa värden är skiftlägeskänsliga. Om inget värde anges används standard `rename`.<br /><br /> Det angivna värdet för den här parametern påverkar alla filer i katalogen som anges av den `/srcdir` parameter.|
|**/ BlobType:**< BlockBlob &#124; PageBlob >|`Optional.`Anger blob för mål-BLOB. Giltiga värden är: `BlockBlob` och `PageBlob`. Observera att dessa värden är skiftlägeskänsliga. Om inget värde anges används standard `BlockBlob`.<br /><br /> I de flesta fall `BlockBlob` rekommenderas. Om du anger `PageBlob`, längden på varje fil i katalogen måste vara delbar med 512, storleken på en sida av sidblobar.|
|**/ PropertyFile:**< PropertyFile\>|`Optional.`Sökvägen till filen egenskap för mål-BLOB. Se [Import/Export service Metadata och egenskaper filformatet](../storage-import-export-file-format-metadata-and-properties.md) för mer information.|
|**/ MetadataFile:**< MetadataFile\>|`Optional.`Sökvägen till metadatafilen för mål-BLOB. Se [Import/Export service Metadata och egenskaper filformatet](../storage-import-export-file-format-metadata-and-properties.md) för mer information.|

### <a name="parameters-for-copying-a-single-file"></a>Parametrar för att kopiera en fil
 När du kopierar en fil, gäller följande obligatoriska och valfria parametrar:

|Kommandoradsparameter|Beskrivning|
|----------------------------|-----------------|
|**/srcfile:**< SourceFile\>|`Required.`Den fullständiga sökvägen till filen som ska kopieras. Sökvägen måste vara en absolut sökväg (inte en relativ sökväg).|
|**/dstblob:**< DestinationBlobPath\>|`Required.`Sökvägen till mål-blob i ditt Windows Azure storage-konto. Blob kanske eller kanske inte redan finns.<br /><br /> Ange den blob namn som början med behållarens namn. Blob-namnet får inte börja med ”/” eller namnet på lagringskontot. Blob namngivningsregler, se [namnge och referera till behållare, Blobbar och Metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).<br /><br /> När Målbehållaren är Rotbehållare, måste du uttryckligen ange `$root` som behållaren som `$root/sample.txt`. Observera att blobbar i behållaren roten får inte innehålla ”/” i sina namn.|
|**/ Disposition:**< Byt namn på &#124; Nej skriva över &#124; skriva över >|`Optional.`Anger beteendet när det finns redan en blob med den angivna adressen. Giltiga värden för den här parametern är: `rename`, `no-overwrite` och `overwrite`. Observera att dessa värden är skiftlägeskänsliga. Om inget värde anges används standard `rename`.|
|**/ BlobType:**< BlockBlob &#124; PageBlob >|`Optional.`Anger blob för mål-BLOB. Giltiga värden är: `BlockBlob` och `PageBlob`. Observera att dessa värden är skiftlägeskänsliga. Om inget värde anges används standard `BlockBlob`.<br /><br /> I de flesta fall `BlockBlob` rekommenderas. Om du anger `PageBlob`, längden på varje fil i katalogen måste vara delbar med 512, storleken på en sida av sidblobar.|
|**/ PropertyFile:**< PropertyFile\>|`Optional.`Sökvägen till filen egenskap för mål-BLOB. Se [Import/Export service Metadata och egenskaper filformatet](../storage-import-export-file-format-metadata-and-properties.md) för mer information.|
|**/ MetadataFile:**< MetadataFile\>|`Optional.`Sökvägen till metadatafilen för mål-BLOB. Se [Import/Export service Metadata och egenskaper filformatet](../storage-import-export-file-format-metadata-and-properties.md) för mer information.|

### <a name="resuming-an-interrupted-copy-session"></a>Återupptar en session avbryts kopia
 Om en kopia sessionen avbryts av någon anledning, kan du återuppta den genom att köra verktyget bara journal filen som angetts:

```
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /ResumeSession
```

 Endast den senaste kopia sessionen, kan om avslutats onormalt, återupptas.

> [!IMPORTANT]
>  När du återupptar en kopia session ändra inte Källdatafiler och kataloger genom att lägga till eller ta bort filer.

### <a name="aborting-an-interrupted-copy-session"></a>Avbryter en session avbryts kopia
 Om en kopia sessionen avbryts och det går inte att återuppta (till exempel om en källkatalog visat tillgänglig), måste du avbryta den aktuella sessionen så att den kan återställas och nya kopiera sessioner kan startas:

```
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AbortSession
```

 Endast den senaste kopia sessionen, kan om avslutats onormalt, avbrytas. Observera att du inte kan avbryta den första kopia sessionen för en enhet. I stället måste du starta om kopia-session med en ny journalfil.

## <a name="next-steps"></a>Nästa steg

* [Konfigurera verktyget Azure Import/Export](storage-import-export-tool-setup-v1.md)
* [Konfigurera egenskaper och metadata under importeringsprocessen](storage-import-export-tool-setting-properties-metadata-import-v1.md)
* [Exempelarbetsflöde för att förbereda hårddiskar för ett importjobb](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow-v1.md)
* [Snabbreferens för vanliga kommandon](storage-import-export-tool-quick-reference-v1.md) 
* [Granska jobbstatus med kopiera loggfiler](storage-import-export-tool-reviewing-job-status-v1.md)
* [Reparera ett importjobb](storage-import-export-tool-repairing-an-import-job-v1.md)
* [Reparera ett exportjobb](storage-import-export-tool-repairing-an-export-job-v1.md)
* [Felsökning av Azures import-/exportverktyg](storage-import-export-tool-troubleshooting-v1.md)
