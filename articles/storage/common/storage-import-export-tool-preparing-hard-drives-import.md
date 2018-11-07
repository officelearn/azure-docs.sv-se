---
title: Förbereda hårddiskar för ett importjobb för Azure Import/Export | Microsoft Docs
description: Lär dig att förbereda hårddiskar med verktyget WAImportExport för att skapa ett importjobb för tjänsten Azure Import/Export.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 06/29/2017
ms.author: muralikk
ms.component: common
ms.openlocfilehash: b16a476f1960c79c378cd3aa18eae789c289eb54
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51244040"
---
# <a name="preparing-hard-drives-for-an-import-job"></a>Förbereda hårddiskar för ett importjobb

Verktyget WAImportExport är den enhet förberedelser och reparera verktyg som du kan använda med den [Microsoft Azure Import/Export-tjänsten](../storage-import-export-service.md). Du kan använda det här verktyget för att kopiera data till hårddiskar som du ska skicka till ett Azure-datacenter. När importen har slutförts kan använda du det här verktyget för att reparera alla blobbar som var skadade saknades eller är i konflikt med andra blobar. Du kan använda det här verktyget för att reparera alla filer som var skadad eller saknas på enheter när du har fått enheterna från en slutförd export-jobbet. I den här artikeln ska gå vi över användningen av det här verktyget.

## <a name="prerequisites"></a>Förutsättningar

### <a name="requirements-for-waimportexportexe"></a>Krav för WAImportExport.exe

- **Datorkonfiguration**
  - Windows 7, Windows Server 2008 R2 eller ett nyare Windows-operativsystem
  - .NET framework 4 måste installeras. Se [vanliga frågor och svar](#faq) om hur du kontrollerar om .net Framework är installerat på datorn.
- **Lagringskontonyckel** -du behöver minst en av kontonycklar för storage-konto.

### <a name="preparing-disk-for-import-job"></a>Förbereda disk för importjobb

- **BitLocker -** BitLocker måste vara aktiverat på den dator som kör verktyget WAImportExport. Se den [vanliga frågor och svar](#faq) för hur du aktiverar BitLocker.
- **Diskar** tillgängliga från datorn där WAImportExport verktyget körs. Se [vanliga frågor och svar](#faq) för disk-specifikationen.
- **Källfiler** -filer som du tänker importera måste kunna nås från kopia-dator, oavsett om de är på en nätverksresurs eller en lokal hårddisk.

### <a name="repairing-a-partially-failed-import-job"></a>Reparera en delvis misslyckade importjobb

- **Kopiera loggfilen** som genereras när tjänsten Azure Import/Export kopierar data mellan Storage-konto och Disk. Finns den i din mål-lagringskontot.

### <a name="repairing-a-partially-failed-export-job"></a>Reparera ett exportjobb som resursflytten misslyckades delvis

- **Kopiera loggfilen** som genereras när tjänsten Azure Import/Export kopierar data mellan Storage-konto och Disk. Finns den i din källagringskontot.
- **Manifestfilen** -[valfritt] Located på exporterade enhet som returnerades av Microsoft.

## <a name="download-and-install-waimportexport"></a>Ladda ned och installera WAImportExport

Ladda ned den [senaste versionen av WAImportExport.exe](https://www.microsoft.com/download/details.aspx?id=55280). Extrahera det komprimerade innehållet till en katalog på datorn.

Nästa uppgift är att skapa CSV-filer.

## <a name="prepare-the-dataset-csv-file"></a>Förbereda datauppsättning CSV-fil

### <a name="what-is-dataset-csv"></a>Vad är datauppsättning CSV

DataSet CSV-filen är värdet för /dataset flaggan är en CSV-fil som innehåller en lista över kataloger och/eller en lista över filer som ska kopieras till målet enheter. Det första steget för att skapa ett importjobb är att avgöra vilka kataloger och filer som du tänker importera. Detta kan vara en lista över kataloger, en lista med unika filer eller en kombination av dessa två. När en katalog finns kommer alla filer i katalogen och dess underkataloger att ingå i importjobbet.

För varje katalog eller filer som ska importeras måste du identifiera en virtuell katalog för mål eller en blob i Azure Blob service. Du använder dessa mål som indata för WAImportExport-verktyget. Kataloger bör vara avgränsade med snedstreck tecknet ”/”.

I följande tabell visas några exempel på blob-mål:

| Käll-filen eller katalogen | Målblobben eller virtuell katalog |
| --- | --- |
| H:\Video | https://mystorageaccount.blob.core.windows.net/video |
| H:\Photo | https://mystorageaccount.blob.core.windows.net/photo |
| K:\Temp\FavoriteVideo.ISO | https://mystorageaccount.blob.core.windows.net/favorite/FavoriteVideo.ISO |
| \\myshare\john\music | https://mystorageaccount.blob.core.windows.net/music |

### <a name="sample-datasetcsv"></a>Exempel på dataset.csv

```
BasePath,DstBlobPathOrPrefix,BlobType,Disposition,MetadataFile,PropertiesFile
"F:\50M_original\100M_1.csv.txt","containername/100M_1.csv.txt",BlockBlob,rename,"None",None
"F:\50M_original\","containername/",BlockBlob,rename,"None",None
```

### <a name="dataset-csv-file-fields"></a>Fälten för DataSet CSV-filen

| Fält | Beskrivning |
| --- | --- |
| BasePath | **[Krävs]**<br/>Värdet för den här parametern representerar källan där data som ska importeras finns. Verktyget kommer rekursivt kopiera alla data som finns under den här sökvägen.<br><br/>**Tillåtna värden**: detta måste vara en giltig sökväg på lokal dator eller en giltig resurssökväg och ska vara tillgängliga för användaren. Katalogsökvägen måste vara en absolut sökväg (inte en relativ sökväg). Om sökvägen som slutar med ”\\”, en annan katalog representerar en sökväg som slutar utan ”\\” representerar en fil.<br/>Inga regex tillåts i det här fältet. Om sökvägen innehåller blanksteg, placerar du den i ””.<br><br/>**Exempel**: ”c:\Directory\c\Directory\File.txt”<br>”\\\\FBaseFilesharePath.domain.net\sharename\directory\"  |
| DstBlobPathOrPrefix | **[Krävs]**<br/> Sökvägen till den virtuella målkatalogen i Windows Azure storage-kontot. Den virtuella katalogen kanske eller kanske inte redan finns. Om det inte finns, skapas en Import/Export-tjänsten.<br/><br/>Glöm inte att använda giltiga behållarnamn när du anger målet virtuella kataloger eller blobar. Tänk på att behållarnamn måste vara gemener. Namngivningsregler för behållaren, se [namngivning och referens av behållare, Blobar och Metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata). Om det bara rot har angetts, replikeras katalogstrukturen på källan i mål-blob-behållare. Om en annan katalogstrukturen är det önskade än som i källan, flera rader med mappning i CSV<br/><br/>Du kan ange en behållare eller ett blob-prefix som musik/70-talet /. Målkatalogen måste börja med behållarens namn följt av ett snedstreck ”/”, och du kan också omfatta en virtuell blob-katalog som slutar med ”/”.<br/><br/>När till Målbehållaren är Rotbehållare, måste du uttryckligen ange root-behållaren, inklusive snedstreck som $root /. Eftersom blobar i behållaren för roten inte får innehålla ”/” i sina namn, kopieras inte eventuella underkataloger i källkatalogen när målkatalogen är Rotbehållare.<br/><br/>**Exempel**<br/>Om målblobbsökvägen är https://mystorageaccount.blob.core.windows.net/video, värdet för det här fältet kan vara video /  |
| BlobType | **[Valfritt]**  blockera &#124; sidan<br/>Import/Export-tjänsten stöder för närvarande 2 typer av Blobar. Sidan blobbar och blockera BlobsBy standard alla filer som ska importeras som Blockblobar. Och \*.vhd och \*vhdx kommer att importeras eftersom Page BlobsThere finns en gräns för blockblob och sidblob tillåtna storleken. Se [Storage skalbarhetsmål](storage-scalability-targets.md) för mer information.  |
| Disposition | **[Valfritt]**  Byt namn på &#124; nr skriva över &#124; skriva över <br/> Det här fältet anger kopieringsbeteendet-under importen dvs När data överförs till storage-kontot från disken. Alternativen är: Byt namn på&#124;säkerhetslagring&#124;nr skrivs över. Som standard ”Byt namn på” om inget angavs. <br/><br/>**Byt namn på**: om ett objekt med samma namn finns skapar du en kopia på målet.<br/>Skriv över: ersätts filen med nyare fil. Senaste ändring wins filen.<br/>**Nej, skriva över**: hoppar över skrivning till filen om den redan finns.|
| MetadataFile | **[Valfritt]** <br/>Värdet för det här fältet är metadatafilen som kan anges om den måste bevara metadata för objekt eller ange anpassade metadata. Sökvägen till metadatafilen för mål-BLOB. Se [Import/Export-tjänsten Metadata och egenskaper filformat](../storage-import-export-file-format-metadata-and-properties.md) för mer information |
| PropertiesFile | **[Valfritt]** <br/>Sökvägen till filen egenskapen för mål-BLOB. Se [Import/Export-tjänsten Metadata och egenskaper filformat](../storage-import-export-file-format-metadata-and-properties.md) för mer information. |

## <a name="prepare-initialdriveset-or-additionaldriveset-csv-file"></a>Förbereda InitialDriveSet eller AdditionalDriveSet CSV-fil

### <a name="what-is-driveset-csv"></a>Vad är driveset CSV

Värdet för flaggan /InitialDriveSet eller /AdditionalDriveSet är en CSV-fil som innehåller listan över diskar som enhetsbeteckningarna mappas till så att verktyget korrekt kan välja listan över diskar som ska förberedas. Om data är större än en enda diskstorlek, kommer verktyget WAImportExport distribuera data över flera diskar som är registrerad i den här CSV-filen i ett optimerat sätt.

Det finns ingen gräns för antalet diskar som data kan skrivas till i en enda session. Verktyget kommer att distribuera data baserat på diskens storlek och Mappstorlek. Väljs den disk som är de flesta optimerade för objekt-storlek. Data när överförs till storage-kontot ska vara konvergerat tillbaka till katalogstrukturen som angavs i datamängdfil. Följ stegen nedan för att skapa en driveset CSV.

### <a name="create-basic-volume-and-assign-drive-letter"></a>Skapa enkel volym och tilldela enhetsbeteckning

För att skapa en enkel volym och tilldela en enhetsbeteckning genom att följa anvisningarna för ”enklare att skapa partitioner” beroende på [översikt över Diskhantering](https://technet.microsoft.com/library/cc754936.aspx).

### <a name="sample-initialdriveset-and-additionaldriveset-csv-file"></a>Exemplet InitialDriveSet och AdditionalDriveSet CSV-fil

```
DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631
H,Format,SilentMode,Encrypt,
```

### <a name="driveset-csv-file-fields"></a>Fälten för Driveset CSV-filen

| Fält | Värde |
| --- | --- |
| Enhetsbeteckning | **[Krävs]**<br/> Varje enhet som anges för verktyget målet måste ha en enkel NTFS-volym på den och en enhetsbeteckning.<br/> <br/>**Exempel**: R eller r |
| FormatOption | **[Krävs]**  Format &#124; AlreadyFormatted<br/><br/> **Formatet**: ange detta formateras alla data på disken. <br/>**AlreadyFormatted**: verktyget hoppar över formateringen när det här värdet har angetts. |
| SilentOrPromptOnFormat | **[Krävs]**  SilentMode &#124; PromptOnFormat<br/><br/>**SilentMode**: det här värdet kommer att användare ska kunna köra verktyget i tyst läge. <br/>**PromptOnFormat**: verktyget uppmanas användaren att bekräfta om åtgärden är avsedd vid varje format.<br/><br/>Om inte har angetts kommandot Avbryt och visa felmeddelande ”: Ogiltigt värde för SilentOrPromptOnFormat: ingen” |
| Kryptering | **[Krävs]**  Kryptera &#124; AlreadyEncrypted<br/> Värdet för det här fältet avgör vilka disken för att kryptera och som inte till. <br/><br/>**Kryptera**: verktyget att formateras. Om värdet för fältet ”FormatOption” är ”Format” krävs det här värdet ska vara ”kryptera”. Om ”AlreadyEncrypted” anges i det här fallet det kommer att orsaka ett fel uppstod under ”när du anger formatet Encrypt måste också anges”.<br/>**AlreadyEncrypted**: verktyget dekrypterar enheten med BitLockerKey som anges i fältet ”ExistingBitLockerKey”. Om värdet för fältet ”FormatOption” är ”AlreadyFormatted” kan kan sedan det här värdet vara antingen ”kryptera” eller ”AlreadyEncrypted” |
| ExistingBitLockerKey | **[Krävs]**  Om värdet för fältet ”kryptering” är ”AlreadyEncrypted”<br/> Värdet för det här fältet är BitLocker-nyckel som är associerad med en viss disk. <br/><br/>Det här fältet bör lämnas tom om värdet för fältet ”kryptering” är ”kryptera”.  Om BitLocker Key anges i det här fallet är det kommer att orsaka ett fel uppstod under ”Bitlocker Key ska inte anges”.<br/>  **Exempel**: 060456-014509-132033-080300-252615-584177-672089-411631|

##  <a name="preparing-disk-for-import-job"></a>Förbereda disk för importjobb

För att förbereda enheter för ett importjobb, anropa WAImportExport verktyget med den **PrepImport** kommando. Vilka parametrar som du inkluderar beror på om det här är den första kopia sessionen eller en efterföljande kopia session.

### <a name="first-session"></a>Första sessionen

Första kopia sessionen att kopiera en Single/Multiple katalog till ett enda/flera Disk (beroende på vad som anges i CSV-fil) WAImportExport verktyg PrepImport-kommando för den första kopia sessionen att kopiera kataloger och/eller filer med en ny kopia-session:

```
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] /DataSet:<dataset.csv>
```

**Exempel:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:\*\*\*\*\*\*\*\*\*\*\*\*\* /InitialDriveSet:driveset-1.csv /DataSet:dataset-1.csv /logdir:F:\logs
```

### <a name="add-data-in-subsequent-session"></a>Lägga till data i efterföljande session

I efterföljande kopia sessioner behöver du inte ange inledande parametrar. Du måste använda samma journalfil för verktyget för att komma ihåg där det kvar i den tidigare sessionen. Tillståndet för sessionen kopia skrivs till filen ändringsjournalen. Här är syntaxen för en efterföljande kopia-session för att kopiera ytterligare kataloger och eller-filer:

```
WAImportExport.exe PrepImport /j:<SameJournalFile> /id:<DifferentSessionId>  [DataSet:<differentdataset.csv>]
```

**Exempel:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
```

### <a name="add-drives-to-latest-session"></a>Lägga till enheter i senaste sessionen

Om data inte passade in i enheter i InitialDriveset, använda en verktyget för att lägga till ytterligare enheter i samma session för kopiering. 

>[!NOTE] 
>Sessions-id måste matcha tidigare sessions-id. Journalfil måste matcha den som angetts i tidigare sessionen.
>
```
WAImportExport.exe PrepImport /j:<SameJournalFile> /id:<SameSessionId> /AdditionalDriveSet:<newdriveset.csv>
```

**Exempel:**

```
WAImportExport.exe PrepImport /j:SameJournalTest.jrn /id:session#2  /AdditionalDriveSet:driveset-2.csv
```

### <a name="abort-the-latest-session"></a>Avbryt den senaste sessionen:

Om en kopia-sessionen avbryts och det går inte att återuppta (till exempel om en källkatalog visat sig vara otillgängligt), måste du avbryta den aktuella sessionen så att den kan återställas och den nya kopian sessioner kan startas:

```
WAImportExport.exe PrepImport /j:<SameJournalFile> /id:<SameSessionId> /AbortSession
```

**Exempel:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /AbortSession
```

Om avslutades oväntat, kan bara den senaste kopian sessionen, avbrytas. Observera att du inte kan avbryta den första kopia sessionen för en enhet. I stället måste du starta om sessionen kopia med en ny journalfil.

### <a name="resume-a-latest-interrupted-session"></a>Återuppta en senaste avbrutna session

Om en kopia-sessionen avbryts av någon anledning, kan du återuppta den genom att köra verktyget med endast journalfil anges:

```
WAImportExport.exe PrepImport /j:<SameJournalFile> /id:<SameSessionId> /ResumeSession
```

**Exempel:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2 /ResumeSession
```

> [!IMPORTANT] 
> När du återuppta en session för kopiera, ändra inte Källdatafiler och kataloger genom att lägga till eller ta bort filer.

## <a name="waimportexport-parameters"></a>WAImportExport parametrar

| Parametrar | Beskrivning |
| --- | --- |
|     /j:&lt;JournalFile&gt;  | **Krävs**<br/> Sökvägen till filen ändringsjournalen. En journalfil spårar en uppsättning enheter och registrerar förloppet i att förbereda dessa enheter. Journalfilen måste alltid anges.  |
|     /logdir:&lt;LogDirectory&gt;  | **Valfritt**. Loggkatalogen.<br/> Utförliga loggfiler samt vissa temporära filer skrivs till den här katalogen. Om inte angivna, aktuell katalog ska användas som log-katalogen. Loggkatalogen kan anges endast en gång för samma journalfil.  |
|     /ID:&lt;sessions-ID&gt;  | **Krävs**<br/> Sessionen Id används för att identifiera en kopia-session. Den används för att säkerställa att korrekt återställning av en session avbryts kopia.  |
|     / ResumeSession  | Valfri. Om den senaste kopia-sessionen har avslutats onormalt, kan den här parametern anges för att fortsätta sessionen.   |
|     / AbortSession  | Valfri. Om den senaste kopia-sessionen har avslutats onormalt, kan den här parametern anges för att avbryta sessionen.  |
|     /SN:&lt;StorageAccountName&gt;  | **Krävs**<br/> Gäller endast för RepairImport och RepairExport. Namnet på lagringskontot.  |
|     /Sk:&lt;StorageAccountKey&gt;  | **Krävs**<br/> Nyckeln för lagringskontot. |
|     / InitialDriveSet:&lt;driveset.csv&gt;  | **Krävs** när du kör den första kopia-sessionen<br/> En CSV-fil som innehåller en lista med enheter för att förbereda.  |
|     / AdditionalDriveSet:&lt;driveset.csv&gt; | **Krävs**. När du lägger till enheter till den aktuella kopian sessionen. <br/> En CSV-fil som innehåller en lista över ytterligare enheter som ska läggas till.  |
|      / r:&lt;RepairFile&gt; | **Krävs** gäller endast för RepairImport och RepairExport.<br/> Sökvägen till filen för spårning av reparation pågår. Varje enhet måste ha en repair-fil.  |
|     / d:&lt;TargetDirectories&gt; | **Krävs**. Gäller endast för RepairImport och RepairExport. För RepairImport, en eller flera semikolonavgränsad kataloger att reparera; För RepairExport, en katalog att reparera, t.ex. rotkatalogen på enheten.  |
|     / CopyLogFile:&lt;DriveCopyLogFile&gt; | **Krävs** gäller endast för RepairImport och RepairExport. Sökvägen till loggfilen enhet kopia (utförlig eller fel).  |
|     / ManifestFile:&lt;DriveManifestFile&gt; | **Krävs** gäller endast för RepairExport.<br/> Sökväg till enheten manifestfilen.  |
|     / PathMapFile:&lt;DrivePathMapFile&gt; | **Valfritt**. Gäller endast för RepairImport.<br/> Sökvägen till filen som innehåller mappningar av sökvägar i förhållande till rotenheten till platserna för faktiska filer (tabbavgränsad). När du först det fylls i med sökvägar med tomt mål, vilket innebär att de inte ingår i TargetDirectories, åtkomst nekad med ogiltigt namn eller de finns i flera kataloger. Mappningsfilen sökväg kan redigeras med rätt målsökvägar manuellt och angetts igen för verktyget för att matcha sökvägarna korrekt.  |
|     / ExportBlobListFile:&lt;ExportBlobListFile&gt; | **Krävs**. Gäller endast för PreviewExport.<br/> Sökväg till XML-Datatypen filen som innehåller listan över blob-sökvägar eller blob-prefix som sökväg för BLOB-objekt som ska exporteras. Filformatet är samma som blobbformatet för blob i placera jobbet användningen av REST-API för Import/Export-tjänsten.  |
|     / DriveSize:&lt;DriveSize&gt; | **Krävs**. Gäller endast för PreviewExport.<br/>  Storleken på enheter som ska användas för export. Till exempel 500 GB, 1,5 TB. Obs: 1 GB = 1 000 000 000 bytes1 TB = 1,000,000,000,000 byte  |
|     / Datauppsättning:&lt;dataset.csv&gt; | **Krävs**<br/> En CSV-fil som innehåller en lista över kataloger och/eller en lista över filer som ska kopieras till målet enheter.  |
|     /silentmode  | **Valfritt**.<br/> Om inte anges kommer den påminna dig om krav på enheter och din bekräftelse att fortsätta.  |

## <a name="tool-output"></a>Verktyget utdata

### <a name="sample-drive-manifest-file"></a>Manifest exempelfilen för enhet

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

### <a name="sample-journal-file-xml-for-each-drive"></a>Exemplet journalfil (XML) för varje enhet

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

### <a name="sample-journal-file-jrn-for-session-that-records-the-trail-of-sessions"></a>Exemplet journalfil (JRN) för sessionen som registrerar spår efter sessioner

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

#### <a name="what-is-waimportexport-tool"></a>Vad är WAImportExport verktyg?

Verktyget WAImportExport är enheten förberedelser och reparera verktyg som du kan använda med Microsoft Azure Import/Export-tjänsten. Du kan använda det här verktyget för att kopiera data till hårddiskar som du ska skicka till ett Azure-datacenter. När importen har slutförts kan använda du det här verktyget för att reparera alla blobbar som var skadade saknades eller är i konflikt med andra blobar. Du kan använda det här verktyget för att reparera alla filer som var skadad eller saknas på enheter när du har fått enheterna från en slutförd export-jobbet.

#### <a name="how-does-the-waimportexport-tool-work-on-multiple-source-dir-and-disks"></a>Hur fungerar verktyget WAImportExport på flera källa dir och diskar?

Om data är större än den, distribuera verktyget WAImportExport data på diskarna på ett optimerat sätt. Datakopieringen till flera diskar kan göras parallell eller sekventiellt. Det finns ingen gräns för antalet diskar som data kan skrivas till samtidigt. Verktyget kommer att distribuera data baserat på diskens storlek och Mappstorlek. Väljs den disk som är de flesta optimerade för objekt-storlek. Data när överförs till storage-kontot ska vara konvergerat tillbaka till den angivna katalogstrukturen.

#### <a name="where-can-i-find-previous-version-of-waimportexport-tool"></a>Var hittar jag tidigare version av WAImportExport tool?

WAImportExport verktyget har alla funktioner som hade WAImportExport V1-verktyget. WAImportExport verktyget kan du ange flera källor och skriva till flera enheter. Dessutom kan ett enkelt hantera flera källplatser där data ska kopieras i en enda CSV-fil. Om du behöver support för SAS- eller vill du kopiera en enda källa till enskild disk, du kan dock [hämta WAImportExport V1 verktyget](https://go.microsoft.com/fwlink/?LinkID=301900&amp;clcid=0x409) och referera till [WAImportExport V1 referens](storage-import-export-tool-how-to-v1.md) hjälp med WAImportExport V1-användning .

#### <a name="what-is-a-session-id"></a>Vad är ett sessions-ID?

Verktyget förväntar sig att kopiera sessionen (/ id) parameter ska vara samma om avsikten är att sprida data över flera diskar. Upprätthålla samma namn som kopia sessionen kan användaren att kopiera data från en eller flera källplatser till en eller flera diskar/kataloger. Upprätthålla samma sessions-id kan verktyget för att ta vid kopiering av filer från där den lämnades senast.

Samma session för kopiering kan inte användas att importera data till olika lagringskonton.

När kopia-sessionsnamnet är samma från de många körningarna av verktyget, loggfilen (/ logdir) och lagringskontonyckeln (/ sk) förväntas också vara samma.

Sessions-ID kan bestå av bokstäver, 0 ~ 9, understore (\_), bindestreck (-) eller hash (#), och längden måste vara 3 ~ 30.

t.ex. sessions-1 eller session #1 eller session\_1

#### <a name="what-is-a-journal-file"></a>Vad är en journalfil?

Varje gång du kör verktyget WAImportExport för att kopiera filer till hårddisken, skapar verktyget en kopia-session. Tillståndet för sessionen kopia skrivs till filen ändringsjournalen. Om en kopia-sessionen avbryts (till exempel på grund av strömavbrott system), kan den återupptas genom att köra verktyget igen och ange journalfil på kommandoraden.

För varje hårddisk som du förbereder med verktyget Azure Import/Export-verktyget skapar en enda journal-fil med namnet ”&lt;EnhetsID&gt;XML” där enhet Id är serienumret som är kopplad till den enhet som läser verktyget från disken. Du behöver journalfilerna från alla enheter att skapa importjobbet. Journalfilen kan också användas för att återuppta förberedelsen av enheten om verktyget avbryts.

#### <a name="what-is-a-log-directory"></a>Vad är en loggningskatalog?

Loggkatalogen anger en katalog som används för att lagra utförliga loggar samt tillfällig manifestfiler. Om den inte anges används den aktuella katalogen som log-katalogen. Loggarna finns utförliga loggar.

### <a name="prerequisites"></a>Förutsättningar

#### <a name="what-are-the-specifications-of-my-disk"></a>Vilka är specifikationerna av disken?

En eller flera tomma 2,5 tum eller 3,5-tums SATA II eller III eller SSD hårddiskar anslutna till kopia-datorn.

#### <a name="how-can-i-enable-bitlocker-on-my-machine"></a>Hur kan jag aktivera BitLocker på min dator?

Det är enkelt sätt att kontrollera genom att högerklicka på systemenheten. Den visar alternativ för Bitlocker om funktionen är aktiverad. Om den är avstängd, visas inte den.

![Kontrollera BitLocker](./media/storage-import-export-tool-preparing-hard-drives-import/BitLocker.png)

Här är en artikel om [hur du aktiverar BitLocker](https://technet.microsoft.com/library/cc766295.aspx)

Det är möjligt att din dator inte har TPM-kretsen. Om du inte får utdata med tpm.msc, titta på nästa vanliga frågor och svar.

#### <a name="how-to-disable-trusted-platform-module-tpm-in-bitlocker"></a>Så här inaktiverar du Trusted Platform Module (TPM) i BitLocker?
> [!NOTE]
> Endast om det finns inga TPM i sina servrar, behöver du inaktiverar TPM-principen. Du behöver inte inaktivera TPM om det finns betrodda TPM i användarens server. 
> 

Gå igenom följande steg för att kunna inaktivera TPM i BitLocker:<br/>
1. Starta **redigeraren** genom att skriva gpedit.msc på en kommandotolk. Om **redigeraren** verkar vara otillgänglig för att aktivera BitLocker först. Se föregående vanliga frågor och svar.
2. Öppna **lokal datorprincip &gt; Datorkonfiguration &gt; Administrationsmallar &gt; Windows-komponenter&gt; BitLocker-diskkryptering &gt; operativsystemsenheter**.
3. Redigera **kräver ytterligare autentisering vid start** princip.
4. Ställa in principen **aktiverad** och se till att **Tillåt BitLocker utan en kompatibel TPM** kontrolleras.

####  <a name="how-to-check-if-net-4-or-higher-version-is-installed-on-my-machine"></a>Så här att kontrollera om .NET 4 eller senare version är installerad på min dator?

Alla Microsoft .NET Framework-versioner är installerade i följande katalog: %windir%\Microsoft.NET\Framework\

Navigera till den ovan nämnda delen på måldatorn där verktyget ska köras. Leta efter namnet på mappen som börjar med ”v4”. Avsaknad av sådana directory innebär .NET 4 inte är installerat på datorn. Du kan ladda ned .net 4 på datorn med hjälp av [Microsoft .NET Framework 4 (Webbinstallationsprogram)](https://www.microsoft.com/download/details.aspx?id=17851).

### <a name="limits"></a>Begränsningar

#### <a name="how-many-drives-can-i-preparesend-at-the-same-time"></a>Hur många enheter kan jag förbereda/skicka på samma gång?

Det finns ingen gräns för antalet diskar som kan förbereda verktyget. Verktyget förväntas men enhetsbeteckningar som indata. Som begränsar den till 25 samtidiga förberedelse. Ett enskilt jobb kan hantera upp till 10 diskar i taget. Om du förbereder fler än 10 diskar som riktar in sig på samma lagringskonto, kan diskarna distribueras på flera jobb.

#### <a name="can-i-target-more-than-one-storage-account"></a>Kan jag rikta mer än en storage-konto?

Endast en storage-konto kan skickas per jobb och i en enda kopia session.

### <a name="capabilities"></a>Funktioner

#### <a name="does-waimportexportexe-encrypt-my-data"></a>Krypterar WAImportExport.exe Mina data?

Ja. BitLocker-kryptering aktiveras och krävs för den här processen.

#### <a name="what-will-be-the-hierarchy-of-my-data-when-it-appears-in-the-storage-account"></a>Vad blir hierarkin för Mina data när den visas i storage-konto?

Även om data fördelas över diskar, kommer du konvergerat data när överförs till lagringskontot tillbaka till katalogstrukturen som anges i datauppsättningen CSV-fil.

#### <a name="how-many-of-the-input-disks-will-have-active-io-in-parallel-when-copy-is-in-progress"></a>Hur många av indata diskar har active-i/o parallellt, när kopia pågår?

Verktyget distribuerar data i de inkommande diskar baserat på storleken på indatafilerna. Det finns dock antalet aktiva diskar parallellt delends helt på typen av indata. Beroende på storleken på enskilda filer i den inkommande datauppsättningen kan indikera en eller flera diskar active-i/o parallellt. Se nästa fråga för mer information.

#### <a name="how-does-the-tool-distribute-the-files-across-the-disks"></a>Hur verktyget distribuerar filerna på diskarna?

WAImportExport verktyget läser och skriver filer parti, en batch innehåller max för 100000 filer. Det innebär att högst 100000 går att skriva filer parallellt. Flera diskar skrivs till samtidigt om dessa 100000 filer ska distribueras till flera enheter. Men om verktyget skriver till flera diskar samtidigt eller en enskild disk beror på den sammanlagda storleken för batchen. Till exempel vid mindre filer skriver om alla 10,0000 filer kan rymmas i en enda enhet, verktyget du bara en enda disk under bearbetning av den här batchen.

### <a name="waimportexport-output"></a>WAImportExport utdata

#### <a name="there-are-two-journal-files-which-one-should-i-upload-to-azure-portal"></a>Det finns två journalfiler, vilken bör jag laddar upp till Azure-portalen?

**.XML** -för varje hårddisk som du förbereder med verktyget WAImportExport verktyget skapar en enda journal-fil med namnet `<DriveID>.xml` där enhets-ID är det tal som är kopplad till den enhet som läser verktyget från disken. Du behöver journalfilerna från alla enheter att skapa importjobbet på Azure-portalen. Den här journalfil kan också användas för att återuppta förberedelsen av enheten om verktyget avbryts.

**.jrn** -journalfil med suffixet `.jrn` innehåller statusen för alla kopia sessioner för en hårddisk. Den innehåller också information som behövs för att skapa importjobbet. Du måste alltid ange en journalfil när du kör verktyget WAImportExport, samt kopiera sessions-ID.

## <a name="next-steps"></a>Nästa steg

* [Konfigurera Azure Import/Export-verktyget](storage-import-export-tool-setup.md)
* [Konfigurera egenskaper och metadata under importeringsprocessen](storage-import-export-tool-setting-properties-metadata-import.md)
* [Exempelarbetsflöde för att förbereda hårddiskar för ett importjobb](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow.md)
* [Snabbreferens för ofta använda kommandon](storage-import-export-tool-quick-reference.md) 
* [Granska jobbstatus med kopiera loggfiler](storage-import-export-tool-reviewing-job-status-v1.md)
* [Reparera ett importjobb](storage-import-export-tool-repairing-an-import-job-v1.md)
* [Reparera ett exportjobb](storage-import-export-tool-repairing-an-export-job-v1.md)
* [Felsökning av Azures import-/exportverktyg](storage-import-export-tool-troubleshooting-v1.md)
