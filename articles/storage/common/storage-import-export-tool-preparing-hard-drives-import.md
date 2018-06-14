---
title: Förbereda hårddiskar för ett Azure Import/Export-importjobb | Microsoft Docs
description: Lär dig hur du förbereder hårddiskar med hjälp av verktyget WAImportExport för att skapa ett importjobb för tjänsten Azure Import/Export.
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: ''
ms.assetid: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: muralikk
ms.openlocfilehash: 2854822907e818297c8d2f74cab48b0afa0d646c
ms.sourcegitcommit: b723436807176e17e54f226fe00e7e977aba36d5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2017
ms.locfileid: "23931725"
---
# <a name="preparing-hard-drives-for-an-import-job"></a>Förbereda hårddiskar för ett importjobb

Verktyget WAImportExport är enheten förberedelse och reparera verktyget som du kan använda med den [Microsoft Azure Import/Export service](../storage-import-export-service.md). Du kan använda det här verktyget för att kopiera data till hårddiskar som du kommer att leverera till ett Azure-datacenter. När importen har slutförts kan använda du det här verktyget för att reparera alla blobbar som skadades saknades eller stod i konflikt med andra blobs. Du kan använda det här verktyget för att reparera alla filer som var skadad eller saknas på enheter när du får enheterna från en slutförd exportjobb. I den här artikeln går vi användningen av det här verktyget.

## <a name="prerequisites"></a>Krav

### <a name="requirements-for-waimportexportexe"></a>Krav för WAImportExport.exe

- **Konfigurationen av datorn**
  - Windows 7, Windows Server 2008 R2 eller ett nyare Windows-operativsystem
  - .NET framework 4 måste installeras. Se [vanliga frågor och svar](#faq) om hur du kontrollerar om .net Framework är installerat på datorn.
- **Lagringskontonyckel** -du behöver minst en av nycklarna för lagringskontot.

### <a name="preparing-disk-for-import-job"></a>Förbereda disk för importjobb

- **BitLocker -** BitLocker måste vara aktiverat på datorn som kör verktyget WAImportExport. Finns det [vanliga frågor och svar](#faq) för hur du aktiverar BitLocker.
- **Diskar** nås från datorn där WAImportExport verktyget körs. Se [vanliga frågor och svar](#faq) för disk-specifikationen.
- **Källfiler** -filer som du tänker importera måste vara tillgänglig från datorn kopia om de finns på en nätverksresurs eller en lokal hårddisk.

### <a name="repairing-a-partially-failed-import-job"></a>Reparera felaktiga importjobb

- **Kopiera loggfilen** som genereras när Azure Import/Export service kopierar data mellan Lagringskontot och Disk. Det finns i mål-lagringskontot.

### <a name="repairing-a-partially-failed-export-job"></a>Reparera felaktiga exportjobb

- **Kopiera loggfilen** som genereras när Azure Import/Export service kopierar data mellan Lagringskontot och Disk. Det finns i ditt lagringskonto för källa.
- **Manifestfilen** -[valfritt] Located på exporterade enhet som returnerades av Microsoft.

## <a name="download-and-install-waimportexport"></a>Hämta och installera WAImportExport

Hämta den [senaste versionen av WAImportExport.exe](https://www.microsoft.com/download/details.aspx?id=55280). Extrahera det komprimerade innehållet till en katalog på datorn.

Nästa uppgift är att skapa CSV-filer.

## <a name="prepare-the-dataset-csv-file"></a>Förbereda dataset CSV-fil

### <a name="what-is-dataset-csv"></a>Vad är dataset CSV

DataSet CSV-fil är värdet för /dataset flaggan är en CSV-fil som innehåller en lista över kataloger och/eller en lista över filer som ska kopieras till mål-enheter. Det första steget att skapa ett importjobb är att avgöra vilka kataloger och filer som du ska importera. Detta kan vara en lista över kataloger, en lista med unika filer eller en kombination av dessa två. När en katalog finns ska alla filer i katalogen och dess underkataloger ingå i importjobbet.

Du måste identifiera en destination virtuell katalog eller en blobb i Azure Blob-tjänsten för varje katalog eller fil som ska importeras. Du använder dessa mål som indata till verktyget WAImportExport. Kataloger ska avgränsas med snedstreck tecknet ”/”.

I följande tabell visas några exempel på blob mål:

| Käll-filen eller katalogen | Mål-blob eller virtuell katalog |
| --- | --- |
| H:\Video | https://mystorageaccount.BLOB.Core.Windows.NET/video |
| H:\Photo | https://mystorageaccount.BLOB.Core.Windows.NET/Photo |
| K:\Temp\FavoriteVideo.ISO | https://mystorageaccount.BLOB.Core.Windows.NET/favorite/FavoriteVideo.ISO |
| \\myshare\john\music | https://mystorageaccount.BLOB.Core.Windows.NET/Music |

### <a name="sample-datasetcsv"></a>Exempel på dataset.csv

```
BasePath,DstBlobPathOrPrefix,BlobType,Disposition,MetadataFile,PropertiesFile
"F:\50M_original\100M_1.csv.txt","containername/100M_1.csv.txt",BlockBlob,rename,"None",None
"F:\50M_original\","containername/",BlockBlob,rename,"None",None
```

### <a name="dataset-csv-file-fields"></a>Fälten för DataSet CSV-filen

| Fält | Beskrivning |
| --- | --- |
| BasePath | **[Krävs]**<br/>Värdet för den här parametern representerar källan där data ska importeras finns. Verktyget kommer rekursivt kopiera alla data som finns under den här sökvägen.<br><br/>**Tillåtna värden**: detta måste vara en giltig sökväg på lokal dator eller en giltig resurssökväg och ska vara tillgängliga för användaren. Sökvägen måste vara en absolut sökväg (inte en relativ sökväg). Om sökvägen som slutar med ”\\”, en annan katalog representerar en sökväg utan avslutande ”\\” representerar en fil.<br/>Inga regex tillåts i det här fältet. Om sökvägen innehåller blanksteg, placera den i ””.<br><br/>**Exempel**: ”c:\Directory\c\Directory\File.txt”<br>”\\\\FBaseFilesharePath.domain.net\sharename\directory\"  |
| DstBlobPathOrPrefix | **[Krävs]**<br/> Sökvägen till den virtuella målkatalogen i ditt Windows Azure storage-konto. Den virtuella katalogen kanske eller kanske inte redan finns. Om det inte finns, skapas en Import/Export service.<br/><br/>Se till att använda giltig behållarnamn när du anger målet virtuella kataloger eller BLOB. Tänk på att behållarnamn måste vara gemener. Behållaren namngivningsregler, se [namnge och referera till behållare, Blobbar och Metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata). Om endast roten anges replikeras katalogstrukturen på källdatorn i mål-blob-behållaren. Om en annan katalogstruktur önskas än det i källan, flera rader med mappning i CSV<br/><br/>Du kan ange en behållare eller ett blob-prefix som musik/70-talet /. Målkatalogen måste börja med behållarens namn följt av ett snedstreck ”/”, och du kan också omfatta en virtuell blob-katalog som slutar med ”/”.<br/><br/>När Målbehållaren är Rotbehållare, måste du uttryckligen ange Rotbehållare, inklusive snedstrecket som $root /. Eftersom blobbar i behållaren roten inte får innehålla ”/” i sina namn, kopieras inte eventuella underkataloger i källkatalogen när målkatalogen är Rotbehållare.<br/><br/>**Exempel**<br/>Om blob målsökväg är https://mystorageaccount.blob.core.windows.net/video, värdet för det här fältet kan vara video /  |
| BlobType | **[Valfritt]**  block &#124; sidan<br/>Import/Export service stöder för närvarande 2 typer av Blobbar. Sidan blobbar och blockera BlobsBy standard importeras alla filer som Blockblobar. Och \*.vhd och \*.vhdx importeras som Page BlobsThere finns en gräns på blockblob och sidblob tillåtna storleken. Se [lagring skalbarhetsmål](storage-scalability-targets.md) för mer information.  |
| Disposition | **[Valfritt]**  Byt namn på &#124; Nej skriva över &#124; skriva över <br/> Det här fältet anger beteendet kopiera under import engångsfaktorautentisering När data överförs till lagringskontot från disken. Alternativen är: Byt namn på &#124; Skriv över &#124; Nej skriva över. Som standard ”Byt namn på” om inget anges. <br/><br/>**Byt namn på**: om ett objekt med samma namn finns, skapas en kopia i målet.<br/>Skriv över: skriver över filen med nyare fil. Filen med last-modified wins.<br/>**Nej, skriva över**: hoppar över skrivning till filen om den redan finns.|
| MetadataFile | **[Valfritt]** <br/>Värdet för det här fältet är metadatafilen som kan anges om den måste behålla metadata för objekt eller ange anpassade metadata. Sökvägen till metadatafilen för mål-BLOB. Se [Import/Export service Metadata och egenskaper filformatet](../storage-import-export-file-format-metadata-and-properties.md) mer information |
| PropertiesFile | **[Valfritt]** <br/>Sökvägen till filen egenskap för mål-BLOB. Se [Import/Export service Metadata och egenskaper filformatet](../storage-import-export-file-format-metadata-and-properties.md) för mer information. |

## <a name="prepare-initialdriveset-or-additionaldriveset-csv-file"></a>Förbereda InitialDriveSet eller AdditionalDriveSet CSV-fil

### <a name="what-is-driveset-csv"></a>Vad är driveset CSV

Värdet för flaggan /InitialDriveSet eller /AdditionalDriveSet är en CSV-fil som innehåller listan över diskar enhetsbeteckningarna mappas till så att verktyget korrekt kan hämta listan över diskar förberedas. Om storleken på data är större än storleken för en enskild disk, kommer verktyget WAImportExport distribuera data över flera diskar som är registrerad i CSV-filen på ett optimerat sätt.

Det finns ingen gräns för antalet diskar som data kan skrivas till under en session. Verktyget ska distribuera data baserat på diskens storlek och mappstorleken. Väljs den disk som är de flesta optimerade för objekt-storlek. Data när har överförts till lagringskontot kommer att konvergerat tillbaka till katalogstrukturen som angavs i dataset-filen. Följ stegen nedan för att skapa en driveset CSV.

### <a name="create-basic-volume-and-assign-drive-letter"></a>Skapa en enkel volym och tilldela enhetsbeteckning

För att skapa en enkel volym och tilldela en enhetsbeteckning genom att följa anvisningarna för ”enklare att skapa partitioner” anges i [översikt över Diskhantering](https://technet.microsoft.com/library/cc754936.aspx).

### <a name="sample-initialdriveset-and-additionaldriveset-csv-file"></a>Exempel InitialDriveSet och AdditionalDriveSet CSV-fil

```
DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631
H,Format,SilentMode,Encrypt,
```

### <a name="driveset-csv-file-fields"></a>Fälten för Driveset CSV-filen

| Fält | Värde |
| --- | --- |
| Enhetsbeteckning | **[Krävs]**<br/> Varje enhet som tillhandahålls för verktyget som mål måste ha en enkel NTFS-volym på den och en enhetsbeteckning.<br/> <br/>**Exempel**: R eller r |
| FormatOption | **[Krävs]**  Format &#124; AlreadyFormatted<br/><br/> **Formatet**: ange detta formaterar alla data på disken. <br/>**AlreadyFormatted**: verktyget hoppar över formatering när det här värdet anges. |
| SilentOrPromptOnFormat | **[Krävs]**  SilentMode &#124; PromptOnFormat<br/><br/>**SilentMode**: det här värdet kommer att användare ska kunna köra verktyget i tyst läge. <br/>**PromptOnFormat**: verktyget uppmanas användaren att bekräfta om åtgärden verkligen avsedda vid varje format.<br/><br/>Om inte har angetts kommandot kommer att avbryta och visar felmeddelande ”: Ogiltigt värde för SilentOrPromptOnFormat: ingen” |
| Kryptering | **[Krävs]**  Kryptera &#124; AlreadyEncrypted<br/> Värdet för det här fältet bestämmer vilka disken för att kryptera och som inte. <br/><br/>**Kryptera**: verktyget att formateras. Om värdet för fältet ”FormatOption” är ”Format” krävs det här värdet ska vara ”kryptera”. Om ”AlreadyEncrypted” anges i det här fallet, leder till ett fel ”när formatet anges kryptera måste du även ange”.<br/>**AlreadyEncrypted**: verktyget dekrypterar enheten med BitLockerKey som anges i fältet ”ExistingBitLockerKey”. Om värdet för fältet ”FormatOption” är ”AlreadyFormatted”, kan sedan det här värdet vara antingen ”kryptera” eller ”AlreadyEncrypted” |
| ExistingBitLockerKey | **[Krävs]**  Om värdet för fältet ”kryptering” är ”AlreadyEncrypted”<br/> Värdet för det här fältet är BitLocker-nyckel som är associerad med en viss disk. <br/><br/>Fältet får lämnas tom om värdet för fältet ”kryptering” är ”kryptera”.  Om BitLocker Key anges i det här fallet är resulterar det i ett fel ”Bitlocker Key ska inte anges”.<br/>  **Exempel**: 060456-014509-132033-080300-252615-584177-672089-411631|

##  <a name="preparing-disk-for-import-job"></a>Förbereda disk för importjobb

För att förbereda enheter för ett importjobb anropa WAImportExport verktyget med den **PrepImport** kommando. Vilka parametrar du inkludera beror på om detta är den första kopia sessionen eller en efterföljande kopiera session.

### <a name="first-session"></a>Första sessionen

Första kopian sessionen att kopiera en Single/Multiple katalog till ett enda/flera Disk (beroende på vad som anges i CSV-fil) WAImportExport verktyg PrepImport kommando för den första kopia sessionen att kopiera kataloger och/eller filer med en ny kopia-session:

```
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
```

**Exempel:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:\*\*\*\*\*\*\*\*\*\*\*\*\* /InitialDriveSet:driveset-1.csv /DataSet:dataset-1.csv /logdir:F:\logs
```

### <a name="add-data-in-subsequent-session"></a>Lägg till data i efterföljande sessionen

Du behöver inte ange inledande parametrar i efterföljande kopiera sessioner. Du måste använda samma journal-filen för verktyget komma ihåg där det kvar i den senaste sessionen. Kopiera sessionens status skrivs till journalfilen. Här följer syntaxen för en efterföljande kopia-session för att kopiera ytterligare kataloger och eller filer:

```
WAImportExport.exe PrepImport /j:<SameJournalFile> /id:<DifferentSessionId>  [DataSet:<differentdataset.csv>]
```

**Exempel:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
```

### <a name="add-drives-to-latest-session"></a>Lägga till enheter i senaste sessionen

Om data inte fick plats i enheter i InitialDriveset, använda en verktyget för att lägga till ytterligare enheter i samma kopia session. 

>[!NOTE] 
>Sessions-id ska matcha tidigare sessions-id. Journal-fil måste matcha det som anges i föregående session.
>
```
WAImportExport.exe PrepImport /j:<SameJournalFile> /id:<SameSessionId> /AdditionalDriveSet:<newdriveset.csv>
```

**Exempel:**

```
WAImportExport.exe PrepImport /j:SameJournalTest.jrn /id:session#2  /AdditionalDriveSet:driveset-2.csv
```

### <a name="abort-the-latest-session"></a>Avbryt den senaste sessionen:

Om en kopia sessionen avbryts och det går inte att återuppta (till exempel om en källkatalog visat tillgänglig), måste du avbryta den aktuella sessionen så att den kan återställas och nya kopiera sessioner kan startas:

```
WAImportExport.exe PrepImport /j:<SameJournalFile> /id:<SameSessionId> /AbortSession
```

**Exempel:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /AbortSession
```

Endast den senaste kopia sessionen, kan om avslutats onormalt, avbrytas. Observera att du inte kan avbryta den första kopia sessionen för en enhet. I stället måste du starta om kopia-session med en ny journalfil.

### <a name="resume-a-latest-interrupted-session"></a>Återuppta en senaste avbruten session

Om en kopia sessionen avbryts av någon anledning, kan du återuppta den genom att köra verktyget bara journal filen som angetts:

```
WAImportExport.exe PrepImport /j:<SameJournalFile> /id:<SameSessionId> /ResumeSession
```

**Exempel:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2 /ResumeSession
```

> [!IMPORTANT] 
> När du återupptar en kopia session ändra inte Källdatafiler och kataloger genom att lägga till eller ta bort filer.

## <a name="waimportexport-parameters"></a>WAImportExport parametrar

| Parametrar | Beskrivning |
| --- | --- |
|     /j:&lt;JournalFile&gt;  | **Krävs**<br/> Sökvägen till journalfilen. En journal-fil följer en uppsättning enheter och registrerar förloppet förbereder dessa enheter. Journal-fil måste alltid anges.  |
|     /logdir:&lt;LogDirectory&gt;  | **Valfritt**. Loggkatalogen.<br/> Utförlig loggfiler samt vissa temporära filer kommer att skrivas till den här katalogen. Om inte angivna, aktuell katalog kommer att användas som loggkatalogen. Loggkatalogen som kan anges endast en gång för samma journalfil.  |
|     /ID:&lt;sessions-ID&gt;  | **Krävs**<br/> Sessionen Id används för att identifiera en kopia-session. Den används för att se till att korrekt återställning av en session avbryts kopia.  |
|     / ResumeSession  | Valfri. Om den senaste kopia-sessionen har avslutats onormalt, kan den här parametern anges för att fortsätta sessionen.   |
|     / AbortSession  | Valfri. Om den senaste kopia-sessionen har avslutats onormalt, kan den här parametern anges för att avsluta sessionen.  |
|     /SN:&lt;StorageAccountName&gt;  | **Krävs**<br/> Gäller endast för RepairImport och RepairExport. Namnet på lagringskontot.  |
|     /Sk:&lt;StorageAccountKey&gt;  | **Krävs**<br/> Nyckeln för lagringskontot. |
|     / InitialDriveSet:&lt;driveset.csv&gt;  | **Krävs** när du kör den första kopia-sessionen<br/> En CSV-fil som innehåller en lista över enheter att förbereda.  |
|     / AdditionalDriveSet:&lt;driveset.csv&gt; | **Krävs**. När du lägger till enheter till den aktuella kopian sessionen. <br/> En CSV-fil som innehåller en lista över ytterligare enheter som ska läggas till.  |
|      / r:&lt;RepairFile&gt; | **Krävs** gäller endast för RepairImport och RepairExport.<br/> Sökvägen till filen för spårning av reparation pågår. Varje enhet måste ha en repair-fil.  |
|     / d:&lt;TargetDirectories&gt; | **Krävs**. Gäller endast för RepairImport och RepairExport. För RepairImport, en eller flera semikolonavgränsad kataloger att reparera; För RepairExport, en katalog att reparera, t.ex. rotkatalogen på enheten.  |
|     / CopyLogFile:&lt;DriveCopyLogFile&gt; | **Krävs** gäller endast för RepairImport och RepairExport. Sökvägen till loggfilen för enheten kopia (utförlig eller fel).  |
|     / ManifestFile:&lt;DriveManifestFile&gt; | **Krävs** gäller bara för RepairExport.<br/> Sökvägen till enheten manifestfilen.  |
|     / PathMapFile:&lt;DrivePathMapFile&gt; | **Valfritt**. Gäller endast för RepairImport.<br/> Sökvägen till filen som innehåller mappningar av sökvägar i förhållande till rotenheten till platser faktiska filer (tabbavgränsad). Om du först fylls den med sökvägar med tomt mål, vilket innebär att de inte hittades i TargetDirectories åtkomst nekad med ogiltigt namn eller de finns i flera kataloger. Mappningsfilen sökvägen kan redigeras för att inkludera rätt målsökvägar manuellt och angett igen att lösa sökvägarna korrekt-verktyget.  |
|     / ExportBlobListFile:&lt;ExportBlobListFile&gt; | **Krävs**. Gäller endast för PreviewExport.<br/> Sökvägen till XML-fil som innehåller listan över blob-sökvägar eller blob sökväg prefix för blob som ska exporteras. Filformatet är samma som blobbformatet för blob placera jobbet driften av tjänsten Import/Export REST API.  |
|     / DriveSize:&lt;DriveSize&gt; | **Krävs**. Gäller endast för PreviewExport.<br/>  Storleken på enheter som ska användas för export. Till exempel 500 GB 1,5 TB. Obs: 1 GB = 1 000 000 000 bytes1 TB = 1,000,000,000,000 byte  |
|     / Datauppsättning:&lt;dataset.csv&gt; | **Krävs**<br/> En CSV-fil som innehåller en lista över kataloger och/eller en lista över filer som ska kopieras till mål-enheter.  |
|     /silentmode  | **Valfritt**.<br/> Om den inte anges kommer den påminna dig om behovet av enheter och din bekräftelse för att fortsätta.  |

## <a name="tool-output"></a>Verktyget utdata

### <a name="sample-drive-manifest-file"></a>Manifestet exempelfil för enhet

```xml
<?xml version="1.0" encoding="UTF-8"?>
<DriveManifest Version="2011-MM-DD">
   <Drive>
      <DriveId>drive-id</DriveId>
      <StorageAccountKey>storage-account-key</StorageAccountKey>
      <ClientCreator>client-creator</ClientCreator>
      <!-- First Blob List -->
      <BlobList Id="session#1-0">
         <!-- Global properties and metadata that applies to all blobs -->
         <MetadataPath Hash="md5-hash">global-metadata-file-path</MetadataPath>
         <PropertiesPath Hash="md5-hash">global-properties-file-path</PropertiesPath>
         <!-- First Blob -->
         <Blob>
            <BlobPath>blob-path-relative-to-account</BlobPath>
            <FilePath>file-path-relative-to-transfer-disk</FilePath>
            <ClientData>client-data</ClientData>
            <Length>content-length</Length>
            <ImportDisposition>import-disposition</ImportDisposition>
            <!-- page-range-list-or-block-list -->
            <!-- page-range-list -->
            <PageRangeList>
               <PageRange Offset="1073741824" Length="512" Hash="md5-hash" />
               <PageRange Offset="1073741824" Length="512" Hash="md5-hash" />
            </PageRangeList>
            <!-- block-list -->
            <BlockList>
               <Block Offset="1073741824" Length="4194304" Id="block-id" Hash="md5-hash" />
               <Block Offset="1073741824" Length="4194304" Id="block-id" Hash="md5-hash" />
            </BlockList>
            <MetadataPath Hash="md5-hash">metadata-file-path</MetadataPath>
            <PropertiesPath Hash="md5-hash">properties-file-path</PropertiesPath>
         </Blob>
      </BlobList>
   </Drive>
</DriveManifest>
```

### <a name="sample-journal-file-xml-for-each-drive"></a>Ändringsjournalen exempelfilen (XML) för varje enhet

```xml
[BeginUpdateRecord][2016/11/01 21:22:25.379][Type:ActivityRecord]
ActivityId: DriveInfo
DriveState: [BeginValue]
<?xml version="1.0" encoding="UTF-8"?>
<Drive>
   <DriveId>drive-id</DriveId>
   <BitLockerKey>*******</BitLockerKey>
   <ManifestFile>\DriveManifest.xml</ManifestFile>
   <ManifestHash>D863FE44F861AE0DA4DCEAEEFFCCCE68</ManifestHash> </Drive>
[EndValue]
SaveCommandOutput: Completed
[EndUpdateRecord]
```

### <a name="sample-journal-file-jrn-for-session-that-records-the-trail-of-sessions"></a>Ändringsjournalen exempelfilen (JRN) för session som registrerar spår efter sessioner

```
[BeginUpdateRecord][2016/11/02 18:24:14.735][Type:NewJournalFile]
VocabularyVersion: 2013-02-01
[EndUpdateRecord]
[BeginUpdateRecord][2016/11/02 18:24:14.749][Type:ActivityRecord]
ActivityId: PrepImportDriveCommandContext
LogDirectory: F:\logs
[EndUpdateRecord]
[BeginUpdateRecord][2016/11/02 18:24:14.754][Type:ActivityRecord]
ActivityId: PrepImportDriveCommandContext
StorageAccountKey: *******
[EndUpdateRecord]
```

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

### <a name="general"></a>Allmänt

#### <a name="what-is-waimportexport-tool"></a>Vad är WAImportExport tool?

Verktyget WAImportExport är enheten förberedelse och reparera verktyget som du kan använda med Microsoft Azure Import/Export-tjänsten. Du kan använda det här verktyget för att kopiera data till hårddiskar som du ska levereras till en Azure-datacenter. När importen har slutförts kan använda du det här verktyget för att reparera alla blobbar som skadades saknades eller stod i konflikt med andra blobs. Du kan använda det här verktyget för att reparera alla filer som var skadad eller saknas på enheter när du får enheterna från en slutförd exportjobb.

#### <a name="how-does-the-waimportexport-tool-work-on-multiple-source-dir-and-disks"></a>Hur fungerar verktyget WAImportExport på flera källa dir och diskar?

Om storleken på data är större än diskens storlek, distribuera verktyget WAImportExport data på diskarna på ett optimerat sätt. Kopiera data till flera diskar kan göras parallell eller sekventiellt. Det finns ingen gräns för antalet diskar som data kan skrivas till samtidigt. Verktyget ska distribuera data baserat på diskens storlek och mappstorleken. Väljs den disk som är de flesta optimerade för objekt-storlek. Data när har överförts till lagringskontot kommer att konvergerat tillbaka till den angivna katalogstrukturen.

#### <a name="where-can-i-find-previous-version-of-waimportexport-tool"></a>Där hittar du en tidigare version av WAImportExport verktyget?

WAImportExport verktyg har alla funktioner som hade WAImportExport V1-verktyget. Verktyget WAImportExport tillåter användare att ange flera källor och skriva till flera enheter. Dessutom kan ett enkelt hantera flera källplatser där data måste kopieras i en enskild CSV-fil. I fallet behöver du dock stöd för SAS eller om du vill kopiera en källa på en enda disk du [hämta WAImportExport V1 verktyg] (http://go.microsoft.com/fwlink/?LinkID=301900&amp;clcid = 0x409) och referera till [WAImportExport V1 referens](storage-import-export-tool-how-to-v1.md) hjälp med WAImportExport V1 användning.

#### <a name="what-is-a-session-id"></a>Vad är ett sessions-ID?

Verktyget förväntar kopiera session (/ id) parameter ska vara samma om avsikten är att sprida data över flera diskar. Upprätthålla samma namn som den kopia sessionen kan användaren kopiera data från en eller flera källplatser i en eller flera diskar/kataloger. Upprätthålla samma sessions-id kan verktyget för att hämta kopiering av filer från när den har lämnat senast.

Samma kopia session kan inte användas för att importera data till olika lagringskonton.

När kopiera sessionsnamnet är samma över flera körningar av verktyget loggfilen (/ logdir) och lagringskontonyckel (/ sk) förväntas också vara samma.

Sessions-ID kan bestå av bokstäver, 0 ~ 9, understore (\_), bindestreck (-) eller hash (#), och längden måste vara 3 ~ 30.

t.ex. sessions-1 eller session #1 eller session\_1

#### <a name="what-is-a-journal-file"></a>Vad är en journal-fil?

Varje gång du kör verktyget WAImportExport för att kopiera filer till hårddisken, skapar verktyget en kopia-session. Kopiera sessionens status skrivs till journalfilen. Om en kopia sessionen avbryts (t.ex, på grund av strömavbrott system), kan du återuppta den genom att köra verktyget igen och ange journal-fil på kommandoraden.

För varje hårddisk som du förbereder med verktyget Azure Import/Export verktyget skapar en enda journal-fil med namnet ”&lt;enhets-ID&gt;XML” där enheten Id är det tal som är kopplade till den enhet som läser verktyget från disken. Du behöver journal-filer från alla enheter att skapa importjobbet. Journal-fil kan också användas för att återuppta förberedelse av enheten om verktyget avbryts.

#### <a name="what-is-a-log-directory"></a>Vad är en loggkatalog?

Loggkatalogen som anger en katalog som används för att lagra utförliga loggar som tillfällig manifest-filer. Om inget anges används den aktuella katalogen som loggkatalogen. Loggarna är detaljerade loggarna.

### <a name="prerequisites"></a>Krav

#### <a name="what-are-the-specifications-of-my-disk"></a>Vilka är specifikationerna av disken?

En eller flera tom 2,5-tums eller 3,5-tums SATA II eller III eller SSD hårddiskar anslutna till copy-datorn.

#### <a name="how-can-i-enable-bitlocker-on-my-machine"></a>Hur kan jag aktivera BitLocker på min dator?

Det är enkelt sätt att kontrollera genom att högerklicka på systemenheten. Den visar alternativ för Bitlocker om kapaciteten är aktiverat. Om det är inaktiverat kan du inte se den.

![Kontrollera BitLocker](./media/storage-import-export-tool-preparing-hard-drives-import/BitLocker.png)

Här är en artikel om [hur du aktiverar BitLocker](https://technet.microsoft.com/library/cc766295.aspx)

Det är möjligt att din dator inte har TPM-chip. Om du inte får utdata med tpm.msc titta på nästa vanliga frågor och svar.

#### <a name="how-to-disable-trusted-platform-module-tpm-in-bitlocker"></a>Så här inaktiverar du Trusted Platform Module (TPM) i BitLocker?
> [!NOTE]
> Endast om det finns ingen TPM i sina servrar, måste du inaktiverar TPM-principen. Det är inte nödvändigt att inaktivera TPM om det finns en betrodd TPM i användarens server. 
> 

Gå igenom följande steg för att kunna inaktivera TPM i BitLocker:<br/>
1. Starta **Redigeraren för** genom att skriva gpedit.msc i en kommandotolk. Om **Redigeraren för** ser ut att vara tillgänglig för att aktivera BitLocker först. Se föregående vanliga frågor och svar.
2. Öppna **lokal datorprincip &gt; Datorkonfiguration &gt; Administrationsmallar &gt; Windows-komponenter&gt; BitLocker-diskkryptering &gt; operativsystemsenheter**.
3. Redigera **kräver ytterligare autentisering vid start** princip.
4. Ange principen som **aktiverad** och se till att **Tillåt BitLocker utan en kompatibel TPM** är markerad.

####  <a name="how-to-check-if-net-4-or-higher-version-is-installed-on-my-machine"></a>Hur ska kontrollera om .NET 4 eller högre version är installerat på datorn?

Alla versioner av Microsoft .NET Framework är installerat i följande katalog: %windir%\Microsoft.NET\Framework\

Navigera till den ovan nämnda del på din måldatorn där verktyget måste köras. Sök efter namn börjar med ”v4”. Frånvaron av sådana directory innebär .NET 4 inte har installerats på datorn. Du kan hämta .net 4 på datorn med hjälp av [Microsoft .NET Framework 4 (Webbinstallationsprogram)](https://www.microsoft.com/download/details.aspx?id=17851).

### <a name="limits"></a>Begränsningar

#### <a name="how-many-drives-can-i-preparesend-at-the-same-time"></a>Hur många enheter kan jag förbereda skickar samtidigt?

Det finns ingen gräns för antalet diskar som kan förbereda verktyget. Verktyget förväntas dock enhetsbeteckningar som indata. Som begränsar den till 25 samtidiga förberedelser. Ett enda jobb kan hantera högst 10 diskar i taget. Om du förbereder fler än 10 diskar målobjekt för samma lagringskonto fördelas diskarna på flera jobb.

#### <a name="can-i-target-more-than-one-storage-account"></a>Kan jag använda mer än en storage-konto?

Endast ett lagringskonto kan skickas per jobb och kopia session.

### <a name="capabilities"></a>Funktioner

#### <a name="does-waimportexportexe-encrypt-my-data"></a>Krypterar WAImportExport.exe Mina data?

Ja. BitLocker-kryptering har aktiverats och krävs för den här processen.

#### <a name="what-will-be-the-hierarchy-of-my-data-when-it-appears-in-the-storage-account"></a>Vad är hierarkin data när det visas i lagringskontot?

Även om data distribueras över diskar ska du konvergerat data när har överförts till lagringskontot tillbaka till katalogstrukturen som anges i dataset CSV-fil.

#### <a name="how-many-of-the-input-disks-will-have-active-io-in-parallel-when-copy-is-in-progress"></a>Hur många av indata diskar har aktiva IO parallellt, när kopiera pågår?

Verktyget distribuerar data i de inkommande diskar baserat på storleken på inkommande filer. Namn, antalet aktiva diskar parallellt delends helt på typ av indata. Beroende på storleken på enskilda filer i den inkommande dataset, kanske en eller flera diskar visas active-i/o parallellt. Nästa fråga mer information finns i.

#### <a name="how-does-the-tool-distribute-the-files-across-the-disks"></a>Hur verktyget distribuerar filerna över diskar?

WAImportExport verktyget läser och skriver filer batch av batch en batch innehåller max 100000 filer. Detta innebär att max 100000 filer kan skrivas parallellt. Flera diskar skrivs till samtidigt om dessa 100000 filer ska distribueras till flera enheter. Men om verktyget skriver till flera diskar samtidigt eller en enskild disk är beroende av den totala storleken för gruppen. Till exempel vid mindre filer skriver om alla 10,0000 filer ska kunna få plats i en enda enhet, verktyget till bara en disk under bearbetning av den här batchen.

### <a name="waimportexport-output"></a>WAImportExport utdata

#### <a name="there-are-two-journal-files-which-one-should-i-upload-to-azure-portal"></a>Det finns två journalfiler vilken ska överföra till Azure-portalen?

**XML** -för varje hårddisk som du förbereder med verktyget WAImportExport verktyget skapar en enda journal-fil med namnet `<DriveID>.xml` där enhets-ID är det tal som är kopplade till den enhet som läser verktyget från disken. Du behöver journal-filer från alla enheter att skapa importjobbet i Azure-portalen. Den här journalfilen kan också användas för att återuppta förberedelse av enheten om verktyget avbryts.

**.jrn** -journal-fil med suffixet `.jrn` innehåller statusen för alla sessioner med kopia för en hårddisk. Det innehåller även information som behövs för att skapa importjobbet. Du måste alltid ange en journal-fil när du kör verktyget WAImportExport, samt en kopia sessions-ID.

## <a name="next-steps"></a>Nästa steg

* [Konfigurera verktyget Azure Import/Export](storage-import-export-tool-setup.md)
* [Konfigurera egenskaper och metadata under importeringsprocessen](storage-import-export-tool-setting-properties-metadata-import.md)
* [Exempelarbetsflöde för att förbereda hårddiskar för ett importjobb](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow.md)
* [Snabbreferens för vanliga kommandon](storage-import-export-tool-quick-reference.md) 
* [Granska jobbstatus med kopiera loggfiler](storage-import-export-tool-reviewing-job-status-v1.md)
* [Reparera ett importjobb](storage-import-export-tool-repairing-an-import-job-v1.md)
* [Reparera ett exportjobb](storage-import-export-tool-repairing-an-export-job-v1.md)
* [Felsökning av Azures import-/exportverktyg](storage-import-export-tool-troubleshooting-v1.md)
