---
title: Kopiera eller flytta data till Azure Storage med AzCopy på Windows | Microsoft Docs
description: Använda AzCopy på Windows-verktyget för att flytta eller kopiera data till och från blob-, tabell- och filinnehåll. Kopiera data till Azure Storage från lokala filer eller kopiera data inom eller mellan lagringskonton. Migrera enkelt dina data till Azure Storage.
services: storage
author: seguler
ms.service: storage
ms.topic: article
ms.date: 01/03/2019
ms.author: seguler
ms.subservice: common
ms.openlocfilehash: 6ae3ec566c05d2460747439d61c87c995a90b19c
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "58881740"
---
# <a name="transfer-data-with-the-azcopy-on-windows"></a>Överföra data med AzCopy i Windows
AzCopy är ett kommandoradsverktyg som utformats för att kopiera data till och från Microsoft Azure-Blob, File och Table storage med hjälp av enkla kommandon som är utformad för bästa prestanda. Du kan kopiera data mellan ett filsystem och ett lagringskonto, eller mellan lagringskonton.  

Det finns två versioner av AzCopy som du kan hämta. AzCopy i Windows erbjuder Windows style kommandoradsalternativ. [AzCopy i Linux](storage-use-azcopy-linux.md) riktar sig till Linux-plattformar och erbjuder POSIX style kommandoradsalternativ. Den här artikeln beskriver AzCopy i Windows.

## <a name="download-and-install-azcopy-on-windows"></a>Hämta och installera AzCopy på Windows

### <a name="latest-version-v81"></a>Senaste versionen (v8.1)
Ladda ned den [senaste versionen av AzCopy på Windows](https://aka.ms/downloadazcopy).

#### <a name="azcopy-on-windows-81-release-notes"></a>AzCopy i Windows 8.1 viktig information
- Tabelltjänsten stöds inte längre i den senaste versionen. Om du använder tabell exportfunktionen hämta AzCopy 7.3 versionen.
- Byggas med .NET Core 2.1 och alla beroenden i .NET Core är nu paketeras i installationen.
- Tillagt stöd för OAuth-autentisering. Använd ```azcopy login``` att logga in med Azure Active Directory.

### <a name="azcopy-with-table-support-v73"></a>Azcopy med stöd för tabellen (v7.3)
Ladda ned den [AzCopy 7.3 med stöd för tabellen](https://aka.ms/downloadazcopynet).

### <a name="post-installation-step"></a>Steg efter installation

När du har installerat AzCopy på Windows med hjälp av installationsprogrammet, öppna ett kommandofönster och navigera till installationskatalogen för AzCopy på din dator - där den `AzCopy.exe` körbara finns. Om du vill kan du lägga till installationsplatsen AzCopy systemsökvägen. Som standard installeras AzCopy på `%ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy` eller `%ProgramFiles%\Microsoft SDKs\Azure\AzCopy`.

## <a name="writing-your-first-azcopy-command"></a>Skriva din första AzCopy-kommandot

Grundläggande syntax för AzCopy-kommandon är:

```azcopy
AzCopy /Source:<source> /Dest:<destination> [Options]
```

Följande exempel visar en mängd olika scenarier för att kopiera data till och från Microsoft Azure-Blobar, filer och tabeller. Referera till den [AzCopy parametrar](#azcopy-parameters) för en detaljerad förklaring av de parametrar som används i varje exempel.

## <a name="download-blobs-from-blob-storage"></a>Ladda ned blobar från Blob storage

Nu ska vi titta på flera sätt att ladda ned blobar med hjälp av AzCopy.

### <a name="download-a-single-blob"></a>Hämta en enda blob

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:"abc.txt"
```

Observera att om mappen `C:\myfolder` finns inte, AzCopy skapar den och ladda ned `abc.txt` till den nya mappen.

### <a name="download-a-single-blob-from-the-secondary-region"></a>Hämta en enda blob från den sekundära regionen

```azcopy
AzCopy /Source:https://myaccount-secondary.blob.core.windows.net/mynewcontainer /Dest:C:\myfolder /SourceKey:key /Pattern:"abc.txt"
```

Observera att du måste ha läsåtkomst till geografiskt redundant lagring aktiverad för att få åtkomst till den sekundära regionen.

### <a name="download-all-blobs-in-a-container"></a>Ladda ned alla blobar i en behållare

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /S
```

Anta för följande blobar finns i den angivna behållaren:  

    abc.txt
    abc1.txt
    abc2.txt
    vd1\a.txt
    vd1\abcd.txt

När hämtningen katalogen `C:\myfolder` innehåller följande filer:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\vd1\a.txt
    C:\myfolder\vd1\abcd.txt

Om du inte anger alternativet `/S`, inga blobar laddas ned.

### <a name="download-blobs-with-a-specific-prefix"></a>Ladda ned blobar med ett specifikt prefix

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:a /S
```

Anta för följande blobar finns i den angivna behållaren. Alla blobar som börjar med prefixet `a` hämtas:

    abc.txt
    abc1.txt
    abc2.txt
    xyz.txt
    vd1\a.txt
    vd1\abcd.txt

När hämtningen mappen `C:\myfolder` innehåller följande filer:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt

Prefixet gäller för den virtuella katalogen, som utgör den första delen av blobbnamnet. I exemplet som visas ovan, matchar den virtuella katalogen inte det angivna prefixet så inte laddas ned. Dessutom, om alternativet `/S` har angetts, AzCopy inte ladda ned alla blobbar.

### <a name="set-the-last-modified-time-of-exported-files-to-be-same-as-the-source-blobs"></a>Ange tid för senaste ändring av exporterade filerna vara samma som käll-blobar

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT
```

Du kan också utesluta blobar från hämtningen utifrån deras tid för senaste ändring. Till exempel om du vill undanta blobar vars senast ändrad är samma eller nyare än målfilen och lägga till den `/XN` alternativet:

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT /XN
```

Om du vill undanta blobar vars senast ändrad är samma eller äldre än målfilen och lägga till den `/XO` alternativet:

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT /XO
```

## <a name="upload-blobs-to-blob-storage"></a>Ladda upp blobar till Blob storage

Nu ska vi titta på flera sätt att ladda upp blobar med hjälp av AzCopy.

### <a name="upload-a-single-blob"></a>Ladda upp en enda blob

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:"abc.txt"
```

Om den angivna målcontainern inte finns, så skapar AzCopy den och överför filen till den.

### <a name="upload-a-single-blob-to-a-virtual-directory"></a>Ladda upp en enda blob till en virtuell katalog

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer/vd /DestKey:key /Pattern:abc.txt
```

Om den angivna virtuella katalogen inte finns, AzCopy Överför fil för att inkludera den virtuella katalogen i sitt namn (*t.ex.*, `vd/abc.txt` i exemplet ovan).

### <a name="upload-all-blobs-in-a-folder"></a>Överför alla blobar i en mapp

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /S
```

Om du anger alternativet `/S` Överför innehållet i den angivna katalogen till Blob storage rekursivt, vilket innebär att alla undermappar och filer överförs även. Anta exempelvis att följande filer finns i mappen `C:\myfolder`:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\subfolder\a.txt
    C:\myfolder\subfolder\abcd.txt

Efter överföringen innehåller följande filer i behållaren:

    abc.txt
    abc1.txt
    abc2.txt
    subfolder\a.txt
    subfolder\abcd.txt

Om du inte anger alternativet `/S`, AzCopy överför inte rekursivt. Efter överföringen innehåller följande filer i behållaren:

    abc.txt
    abc1.txt
    abc2.txt

### <a name="upload-blobs-matching-a-specific-pattern"></a>Ladda upp BLOB-objekt som matchar ett visst mönster

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:a* /S
```

Anta att följande filer finns i mappen `C:\myfolder`:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\xyz.txt
    C:\myfolder\subfolder\a.txt
    C:\myfolder\subfolder\abcd.txt

Efter överföringen innehåller följande filer i behållaren:

    abc.txt
    abc1.txt
    abc2.txt
    subfolder\a.txt
    subfolder\abcd.txt

Om du inte anger alternativet `/S`, AzCopy Överför bara blobar som inte finns i en virtuell katalog:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt

### <a name="specify-the-mime-content-type-of-a-destination-blob"></a>Ange MIME-innehållstyp en mål-blob

Som standard AzCopy anger innehållstypen för en mål-blob till `application/octet-stream`. Från och med version 3.1.0, du kan uttryckligen ange innehållstyp via alternativet `/SetContentType:[content-type]`. Den här syntaxen anger innehållstypen för alla blobbar i en överföringen.

```azcopy
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.blob.core.windows.net/myContainer/ /DestKey:key /Pattern:ab /SetContentType:video/mp4
```

Om du anger `/SetContentType` utan ett värde, AzCopy anger varje blob eller filens innehållstyp enligt dess filnamnstillägg.

```azcopy
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.blob.core.windows.net/myContainer/ /DestKey:key /Pattern:ab /SetContentType
```

## <a name="copy-blobs-in-blob-storage"></a>Kopiera blobar i Blob storage

Låt oss titta på flera olika sätt att kopiera blobar från en plats till en annan med hjälp av AzCopy.

### <a name="copy-a-single-blob-from-one-container-to-another-within-the-same-storage-account"></a>Kopiera en enda blob från en behållare till ett annat inom samma lagringskonto 

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1 /Dest:https://myaccount.blob.core.windows.net/mycontainer2 /SourceKey:key /DestKey:key /Pattern:abc.txt
```

När du kopierar en blob i ett lagringskonto, en [serversidan kopia](https://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) åtgärden utförs.

### <a name="copy-a-single-blob-from-one-storage-account-to-another"></a>Kopiera en enda blob från ett lagringskonto till en annan

```azcopy
AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt
```

När du kopierar en blob mellan lagringskonton, en [serversidan kopia](https://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) åtgärden utförs.

### <a name="copy-a-single-blob-from-the-secondary-region-to-the-primary-region"></a>Kopiera en enda blob från den sekundära regionen till den primära regionen

```azcopy
AzCopy /Source:https://myaccount1-secondary.blob.core.windows.net/mynewcontainer1 /Dest:https://myaccount2.blob.core.windows.net/mynewcontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt
```

Observera att du måste ha läsåtkomst till geografiskt redundant lagring aktiverad för att få åtkomst till sekundär lagring.

### <a name="copy-a-single-blob-and-its-snapshots-from-one-storage-account-to-another"></a>Kopiera en enda blob och dess ögonblicksbilder från ett lagringskonto till en annan

```azcopy
AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt /Snapshot
```

Efter kopieringen innehåller en målbehållare blob och dess ögonblicksbilder. Förutsatt att bloben i exemplet ovan har två ögonblicksbilder är behållaren som innehåller följande blob och ögonblicksbilder:

    abc.txt
    abc (2013-02-25 080757).txt
    abc (2014-02-21 150331).txt

### <a name="copy-all-blobs-in-a-container-to-another-storage-account"></a>Kopiera alla blobbar i en behållare till ett annat lagringskonto 

```azcopy
AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 
/Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /S
```

Om du anger alternativet överför /S innehållet i den angivna behållare rekursivt. Se [överför alla blobar i en mapp](#upload-all-blobs-in-a-folder) för mer information och exempel.

### <a name="synchronously-copy-blobs-from-one-storage-account-to-another"></a>Synkront kopiera blobar från ett lagringskonto till ett annat

AzCopy som standard kopierar data mellan två storage slutpunkter asynkront. Därför körs kopieringen i bakgrunden med hjälp av ledig bandbredd kapaciteten som har inget serviceavtal när det gäller hur snabbt en blob kopieras och AzCopy söker regelbundet kopian status tills kopieringen har slutförts eller misslyckades.

Den `/SyncCopy` alternativet ser du till att kopieringen hämtar konsekvent hastighet. AzCopy utför synkrona kopian genom att hämta BLOB att kopiera från den angivna källan till lokalt minne och överför dem till Blob storage-mål.

```azcopy
AzCopy /Source:https://myaccount1.blob.core.windows.net/myContainer/ /Dest:https://myaccount2.blob.core.windows.net/myContainer/ /SourceKey:key1 /DestKey:key2 /Pattern:ab /SyncCopy
```

`/SyncCopy` kan skapa ytterligare utgående kostnader jämfört med asynkron kopia, den rekommenderade metoden är att använda det här alternativet i en Azure virtuell dator som är i samma region som din källagringskontot för att undvika kostnader för utgående trafik.

## <a name="download-files-from-file-storage"></a>Ladda ned filer från fillagring

Nu ska vi titta på flera olika sätt att hämta filer med hjälp av AzCopy.

### <a name="download-a-single-file"></a>Ladda ned en fil

```azcopy
AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/myfolder1/ /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt
```

Om den angivna källan är en Azure-filresurs så måste du antingen ange det exakta filnamnet (*t.ex.* `abc.txt`) att hämta en enda fil eller ange alternativet `/S` att ladda ned alla filer i filresursen rekursivt. Försök att ange ett filmönster och ett alternativet `/S` tillsammans uppstår ett fel.

### <a name="download-all-files-in-a-directory"></a>Ladda ned alla filer i en katalog

```azcopy
AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/ /Dest:C:\myfolder /SourceKey:key /S
```

Observera att tomma mappar inte laddas ned.

## <a name="upload-files-to-an-azure-file-share"></a>Ladda upp filer till en Azure-filresurs

Nu ska vi titta på flera olika sätt att överföra filer med hjälp av AzCopy.

### <a name="upload-a-single-file"></a>Ladda upp en fil

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /Pattern:abc.txt
```

### <a name="upload-all-files-in-a-folder"></a>Överför alla filer i en mapp

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /S
```

Observera att tomma mappar inte laddas upp.

### <a name="upload-files-matching-a-specific-pattern"></a>Ladda upp filer som matchar ett visst mönster

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /Pattern:ab* /S
```

## <a name="copy-files-in-file-storage"></a>Kopiera filer till File storage

Nu ska vi titta på flera olika sätt att kopiera filer till en Azure-filresurs med hjälp av AzCopy.

### <a name="copy-from-one-file-share-to-another"></a>Kopiera från en filresurs till en annan

```azcopy
AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare1/ /Dest:https://myaccount2.file.core.windows.net/myfileshare2/ /SourceKey:key1 /DestKey:key2 /S
```
När du kopierar en fil i filresurser, en [serversidan kopia](https://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) åtgärden utförs.

### <a name="copy-from-an-azure-file-share-to-blob-storage"></a>Kopiera från en Azure-filresurs till Blob storage

```azcopy
AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare/ /Dest:https://myaccount2.blob.core.windows.net/mycontainer/ /SourceKey:key1 /DestKey:key2 /S
```
När du kopierar en fil från filresursen till blob, en [serversidan kopia](https://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) åtgärden utförs.

### <a name="copy-a-blob-from-blob-storage-to-an-azure-file-share"></a>Kopiera en blob från Blob storage till en Azure-filresurs

```azcopy
AzCopy /Source:https://myaccount1.blob.core.windows.net/mycontainer/ /Dest:https://myaccount2.file.core.windows.net/myfileshare/ /SourceKey:key1 /DestKey:key2 /S
```
När du kopierar en fil från en blob till en filresurs, en [serversidan kopia](https://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) åtgärden utförs.

### <a name="synchronously-copy-files"></a>Synkront kopiera filer

Du kan ange den `/SyncCopy` alternativet för att kopiera data från lagring av filer till File Storage, File Storage till Blob Storage och Blob Storage och File Storage synkront, AzCopy gör detta genom att hämta källdata till lokalt minne och ladda upp den igen till mål. Standard utgående kostnad läggs till.

```azcopy
AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare1/ /Dest:https://myaccount2.file.core.windows.net/myfileshare2/ /SourceKey:key1 /DestKey:key2 /S /SyncCopy
```

När du kopierar från fillagring till Blob storage, är blob standardtypen blockblob; användaren kan ange alternativet `/BlobType:page` ändra blob måltypen.

Observera att `/SyncCopy` kan generera ytterligare utgående kostnader jämfört med asynkron kopia. Den rekommenderade metoden är att använda det här alternativet i Azure-VM som finns i samma region som din källagringskontot för att undvika kostnader för utgående trafik.

## <a name="export-data-from-table-storage"></a>Exportera data från Table storage

Låt oss ta en titt på export av data från Azure Table storage med hjälp av AzCopy.

### <a name="export-a-table"></a>Exportera en tabell

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key
```

AzCopy skriver en manifestfil till angivna målmappen. Manifestfilen används i importen för att hitta de nödvändiga filerna och utföra dataverifieringen. Manifestfilen använder följande namngivningskonvention som standard:

    <account name>_<table name>_<timestamp>.manifest

Användaren kan också ange alternativet `/Manifest:<manifest file name>` att ange namnet på manifestfilen.

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /Manifest:abc.manifest
```

### <a name="split-an-export-from-table-storage-into-multiple-files"></a>Dela upp en export från Table storage i flera filer

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/mytable/ /Dest:C:\myfolder /SourceKey:key /S /SplitSize:100
```

AzCopy använder en *volymindex* i filnamn för delade data att skilja flera filer. Volymindexet består av två delar, en *nyckelintervall Partitionsindex* och en *dela filen index*. Båda index är nollbaserat.

Nyckelintervall Partitionsindex är 0 om användaren inte anger alternativet `/PKRS`.

Anta exempelvis att AzCopy genererar två filer när användaren anger alternativet `/SplitSize`. De resulterande filnamn för data kan vara:

    myaccount_mytable_20140903T051850.8128447Z_0_0_C3040FE8.json
    myaccount_mytable_20140903T051850.8128447Z_0_1_0AB9AC20.json

Observera att minsta möjliga värde för alternativet `/SplitSize` är 32 MB. Om det angivna målet är Blob-lagring, AzCopy delar upp datafilen när dess storlekar når storleksbegränsning för blob (200GB), oavsett om alternativet `/SplitSize` har angetts av användaren.

### <a name="export-a-table-to-json-or-csv-data-file-format"></a>Exportera en tabell till JSON- eller CSV-fil dataformat

Som standard exporterar AzCopy tabeller till datafiler i JSON. Du kan ange alternativet `/PayloadFormat:JSON|CSV` att exportera tabellerna som JSON- eller CSV.

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /PayloadFormat:CSV
```

När du anger nyttolastformatet CSV, AzCopy också genererar en schemafil med filtillägget `.schema.csv` för varje datafil.

### <a name="export-table-entities-concurrently"></a>Exportera tabellenheter samtidigt

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /PKRS:"aa#bb"
```

AzCopy startar samtidiga åtgärder om du vill exportera entiteter när användaren anger alternativet `/PKRS`. Varje åtgärd exporterar en partitionsnyckelintervall.

Observera att antalet samtidiga åtgärder styrs även av alternativet `/NC`. AzCopy använder antalet kärnprocessorer som standardvärdet `/NC` när du kopierar tabellenheter, även om `/NC` har inte angetts. När användaren anger alternativet `/PKRS`, AzCopy använder det mindre av de två värdena – viktiga partitionsintervall jämfört med implicit eller explicit anges samtidiga åtgärder – till avgöra hur många samtidiga åtgärder för att starta. Mer information skriver du in `AzCopy /?:NC` på kommandoraden.

### <a name="export-a-table-to-blob-storage"></a>Exportera en tabell till Blob storage

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:https://myaccount.blob.core.windows.net/mycontainer/ /SourceKey:key1 /Destkey:key2
```

AzCopy genererar en JSON-fil för data i blob-behållaren med följande namngivningskonvention:

    <account name>_<table name>_<timestamp>_<volume index>_<CRC>.json

Den genererade JSON-datafilen följer nyttolastformatet för minimal metadata. Mer information om den här nyttolastformatet finns [Nyttolastformatet för tabellen tjänståtgärder](https://msdn.microsoft.com/library/azure/dn535600.aspx).

Observera att när du exporterar tabeller till BLOB-, AzCopy hämtar tabellentiteter till lokala temporära filer och överför sedan dessa entiteter till blob. Dessa tillfälliga datafilerna sätts i journalen filmapp med standardsökvägen ”<code>%LocalAppData%\Microsoft\Azure\AzCopy</code>”, du kan ange alternativet/Z: [journal-filer och mappar] att ändra journalen filplats mapp och därmed ändra platsen för tillfälliga data-filer. Tillfälliga data storlek avgörs av din tabellenheter och storlek som du angav med alternativet-/SplitSize även om den tillfälliga data-filen i lokal disk tas bort omedelbart när den har överförts till blob, kontrollera att du har tillräckligt med lokal diskutrymme för att lagra filerna för tillfälliga data innan de tas bort.

## <a name="import-data-into-table-storage"></a>Importera data till tabellagring

Låt oss ta en titt på import av data till Azure Table storage med hjälp av AzCopy.

### <a name="import-a-table"></a>Importera en tabell

```azcopy
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.table.core.windows.net/mytable1/ /DestKey:key /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:InsertOrReplace
```

Alternativet `/EntityOperation` visar hur du infogar entiteter i tabellen. Möjliga värden:

* `InsertOrSkip`: Hoppar över en befintlig entitet eller infogar en ny entitet om det inte finns i tabellen.
* `InsertOrMerge`: Sammanfogar en befintlig entitet eller infogar en ny entitet om det inte finns i tabellen.
* `InsertOrReplace`: Ersätter en befintlig entitet eller infogar en ny entitet om det inte finns i tabellen.

Observera att du inte kan ange alternativet `/PKRS` i scenariot för import. Till skillnad från export-scenario, där du måste ange alternativet `/PKRS` för att starta samtidiga åtgärder, AzCopy startar samtidiga åtgärder som standard när du importerar en tabell. Standardvärdet för antal samtidiga åtgärder som är igång är lika med antalet kärnprocessorer; Du kan dock ange ett annat antal samtidiga med alternativet `/NC`. Mer information skriver du in `AzCopy /?:NC` på kommandoraden.

Observera att AzCopy endast stöder import för JSON, inte CSV. AzCopy inte stöd för import av tabell från användarskapade JSON och manifest filer. Båda dessa filer måste komma från en tabell AzCopy-export. För att undvika fel kan du inte ändra den exporterade JSON eller manifestfil.

### <a name="import-entities-into-a-table-from-blob-storage"></a>Importera entiteter i en tabell från Blob storage

Anta en Blob-behållare som innehåller följande: En JSON-fil som representerar en Azure-tabell och dess tillhörande manifestfilen.

    myaccount_mytable_20140103T112020.manifest
    myaccount_mytable_20140103T112020_0_0_0AF395F1DC42E952.json

Du kan köra följande kommando för att importera entiteter till en tabell med manifestfilen i den blobbehållaren:

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:https://myaccount.table.core.windows.net/mytable /SourceKey:key1 /DestKey:key2 /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:"InsertOrReplace"
```

## <a name="other-azcopy-features"></a>Andra AzCopy-funktioner

Låt oss ta en titt på några andra funktioner för AzCopy.

### <a name="only-copy-data-that-doesnt-exist-in-the-destination"></a>Kopiera endast data som inte finns i målet

Den `/XO` och `/XN` parametrar kan du exkludera resurser som är äldre eller nyare källa från att kopieras respektive. Om du bara vill kopiera källresurser som inte finns i målet, kan du ange båda parametrarna i AzCopy-kommandot:

    /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /XO /XN

    /Source:C:\myfolder /Dest:http://myaccount.file.core.windows.net/myfileshare /DestKey:<destkey> /S /XO /XN

    /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:http://myaccount.blob.core.windows.net/mycontainer1 /SourceKey:<sourcekey> /DestKey:<destkey> /S /XO /XN

Observera att detta inte stöds när källan eller målet är en tabell.

### <a name="use-a-response-file-to-specify-command-line-parameters"></a>Använda en svarsfil för att ange kommandoradsparametrar

```azcopy
AzCopy /@:"C:\responsefiles\copyoperation.txt"
```

Du kan inkludera eventuella kommandoradsparametrar med AzCopy i en svarsfil. AzCopy bearbetar parametrarna i filen som om de hade angetts på kommandoraden, utföra en direkt ersättning med innehållet i filen.

Anta att en svarsfil med namnet `copyoperation.txt`, som innehåller följande rader. Varje AzCopy-parameter kan anges på en enda rad

    /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /Y

eller på separata rader:

    /Source:http://myaccount.blob.core.windows.net/mycontainer
    /Dest:C:\myfolder
    /SourceKey:<sourcekey>
    /S
    /Y

AzCopy misslyckas om du dela upp parametern på två rader som visas här för den `/sourcekey` parameter:

    http://myaccount.blob.core.windows.net/mycontainer
     C:\myfolder
    /sourcekey:
    <sourcekey>
    /S
    /Y

### <a name="use-multiple-response-files-to-specify-command-line-parameters"></a>Använd flera svarsfiler för att ange kommandoradsparametrar

Anta att en svarsfil med namnet `source.txt` som anger en källbehållare:

    /Source:http://myaccount.blob.core.windows.net/mycontainer

Och en svarsfil med namnet `dest.txt` som anger en målmapp i filsystemet:

    /Dest:C:\myfolder

Och en svarsfil med namnet `options.txt` som anger alternativ för AzCopy:

    /S /Y

För att anropa AzCopy med filerna svar, som finns i en katalog `C:\responsefiles`, Använd det här kommandot:

```azcopy
AzCopy /@:"C:\responsefiles\source.txt" /@:"C:\responsefiles\dest.txt" /SourceKey:<sourcekey> /@:"C:\responsefiles\options.txt"   
```

AzCopy bearbetar det här kommandot precis som om du har lagt till alla av de enskilda parametrarna på kommandoraden:

```azcopy
AzCopy /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /Y
```

### <a name="specify-a-shared-access-signature-sas"></a>Ange en signatur för delad åtkomst (SAS)

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1 /Dest:https://myaccount.blob.core.windows.net/mycontainer2 /SourceSAS:SAS1 /DestSAS:SAS2 /Pattern:abc.txt
```

Du kan också ange en SAS URI-behållaren:

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1/?SourceSASToken /Dest:C:\myfolder /S
```

### <a name="journal-file-folder"></a>Ändringsjournalen filmapp

Varje gång du utfärda ett kommando till AzCopy, kontrollerar den om en journalfil finns i standardmappen eller om den finns i en mapp som du har angett via det här alternativet. Om journalfilen inte finns på någon plats, AzCopy behandlar åtgärden som nya och genererar en ny journalfil.

Om journalfilen finns kontrollerar AzCopy om den kommandorad som du anger matchar kommandoraden i journal-fil. Om två kommandorader matchar, återupptar AzCopy i åtgärden slutfördes inte. Om de inte matchar, uppmanas du att antingen skriva över journalfil att starta en ny åtgärd eller för att avbryta den aktuella åtgärden.

Om du vill använda standardplatsen för journalfilen:

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Z
```

Om du tar bort alternativet `/Z`, eller ange alternativet `/Z` utan mappsökvägen, enligt ovan, AzCopy skapar journal-fil på standardplatsen, vilket är `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`. Om ändringsjournalen filen redan finns, fortsätter igen baserat på journalfilen med AzCopy.

Om du vill ange en anpassad plats för journalfilen:

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Z:C:\journalfolder\
```

Det här exemplet skapar journal-fil om den inte redan finns. Om den finns, fortsätter igen baserat på journalfilen med AzCopy.

Om du vill återuppta en AzCopy-åtgärd:

```azcopy
AzCopy /Z:C:\journalfolder\
```

Det här exemplet fortsätter med den senaste åtgärden som kan ha misslyckats att slutföra.

### <a name="generate-a-log-file"></a>Generera en loggfil

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /V
```

Om du anger alternativet `/V` utan att ange en sökväg till utförlig loggning, AzCopy skapar sedan loggfilen på standardplatsen, vilket är `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`.

I annat fall kan du skapa en loggfil på en annan plats:

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /V:C:\myfolder\azcopy1.log
```

Observera att om du anger en relativ sökväg som följer alternativet `/V`, till exempel `/V:test/azcopy1.log`, och sedan den utförliga loggen skapas i den aktuella arbetskatalogen i en undermapp med namnet `test`.

### <a name="specify-the-number-of-concurrent-operations-to-start"></a>Ange hur många samtidiga åtgärder för att starta

Alternativet `/NC` anger hur många samtidiga kopieringsåtgärder. Som standard startar AzCopy ett visst antal samtidiga åtgärder för att öka dataflödet för överföring. För Table-åtgärderna är antalet samtidiga åtgärder lika med antalet processorer som du har. För Blob- och åtgärder, antalet samtidiga åtgärder är lika med 8 gånger antalet processorer som du har. Om du kör AzCopy i ett nätverk med låg bandbredd, kan du ange ett lägre värde /NC att undvika misslyckades på grund av en resurs.

### <a name="run-azcopy-against-the-azure-storage-emulator"></a>Kör AzCopy mot Azure Storage-emulatorn

Du kan köra AzCopy mot den [Azure Storage-emulatorn](storage-use-emulator.md) för BLOB-objekt:

```azcopy
AzCopy /Source:https://127.0.0.1:10000/myaccount/mycontainer/ /Dest:C:\myfolder /SourceKey:key /SourceType:Blob /S
```

Du kan också köra det för tabeller:

```azcopy
AzCopy /Source:https://127.0.0.1:10002/myaccount/mytable/ /Dest:C:\myfolder /SourceKey:key /SourceType:Table
```

### <a name="automatically-determine-content-type-of-a-blob"></a>Fastställa automatiskt innehållstypen för en Blob

AzCopy anger innehållstypen för en blob som baseras på en JSON-fil som lagrar innehållstyp som ska filmappning för tillägget. Den här JSON-filen heter AzCopyConfig.json och finns i katalogen AzCopy. Om du har en filtyp som inte finns med i listan kan du lägga till mappningen till JSON-fil:

```
{
  "MIMETypeMapping": {
    ".myext": "text/mycustomtype",
    .
    .
  }
}
```     

## <a name="azcopy-parameters"></a>AzCopy-parametrar

Parametrar för AzCopy beskrivs nedan. Du kan också skriva något av följande kommandon från kommandoraden för att få hjälp i med hjälp av AzCopy:

* För detaljerad hjälp för AzCopy: `AzCopy /?`
* För detaljerad hjälp med någon AzCopy-parameter: `AzCopy /?:SourceKey`
* För exemplen: `AzCopy /?:Sample`

### <a name="sourcesource"></a>/ Source: ”källa”

Anger källdata som du vill kopiera från. Källan kan vara en katalog i filsystemet, en blob-behållare, en blob virtuell katalog, en lagringsfilresurs, en katalog i lagring eller en Azure-tabell.

**Gäller:** Blobar, filer, tabeller

### <a name="destdestination"></a>/ Dest: ”mål”

Anger att kopiera till målet. Målet kan vara en katalog i filsystemet, en blob-behållare, en blob virtuell katalog, en lagringsfilresurs, en katalog i lagring eller en Azure-tabell.

**Gäller:** Blobar, filer, tabeller

### <a name="patternfile-pattern"></a>/Pattern:"file-pattern"

Anger ett filmönster som anger vilka filer som ska kopieras. Beteendet för parametern /Pattern bestäms av platsen för datakällan och förekomsten av alternativ för rekursiv-läge. Rekursiva läge har angetts via alternativet/s.

Om den angivna källan är en katalog i filsystemet, sedan standard jokertecken tillämpas och det angivna mönstret för filen matchas mot filer i katalogen. Om alternativet /S anges sedan AzCopy dessutom överensstämmer med det angivna mönstret mot alla filer i undermappar under katalogen.

Om den angivna källan är en blob-behållare eller virtuella katalogen, tillämpas inte jokertecken. Om alternativet /S anges sedan AzCopy tolkar det angivna mönstret som en blob-prefix. Om alternativet inte anges /S sedan AzCopy matchar mönstret filen mot exakta blobnamn.

Om den angivna källan är en Azure-filresurs, så du måste ange det exakta filnamnet, (t.ex. abc.txt) för att kopiera en fil, eller ange alternativet /S att kopiera alla filer i filresursen rekursivt. Försök att ange både en fil mönstret och alternativet /S tillsammans resulterar i ett fel.

AzCopy använder skiftlägeskänsliga matchar när den/Source är en blob-behållare eller blob virtuell katalog och använder icke skiftlägeskänslig matchning i alla andra fall.

Det standardmönster för filen som används när inga filmönster anges är *.* för en plats för system eller ett tomt adressprefix för en Azure-lagringsplats. Att ange flera-filmönster stöds inte.

**Gäller:** Blobar, filer

### <a name="destkeystorage-key"></a>/ DestKey: ”lagringsnyckeln”

Anger lagringskontots åtkomstnyckel för målresursen.

**Gäller:** Blobar, filer, tabeller

### <a name="destsassas-token"></a>/DestSAS:"sas-token"

Anger en signatur för delad åtkomst (SAS) med läsning och skrivning behörigheter för mål (om tillämpligt). Omge SAS med dubbla citattecken, eftersom den kan innehåller specialtecken för kommandoraden.

Du kan antingen ange det här alternativet följt av SAS-token om målresursen är en blob-behållare, filresurs eller tabell, eller kan du ange SAS som en del av mål-blob-behållare, filresurs eller tabellens URI, utan det här alternativet.

Om källan och målet är båda blobar, måste målblobben finnas i samma lagringskonto som källbloben.

**Gäller:** Blobar, filer, tabeller

### <a name="sourcekeystorage-key"></a>/ SourceKey: ”lagringsnyckeln”

Anger lagringskontots åtkomstnyckel för käll-resursen.

**Gäller:** Blobar, filer, tabeller

### <a name="sourcesassas-token"></a>/ SourceSAS: ”sas-token”

Anger en signatur för delad åtkomst med läs- och behörighet för källan (om tillämpligt). Omge SAS med dubbla citattecken, eftersom den kan innehåller specialtecken för kommandoraden.

Om käll-resursen är en blob-behållare och varken en nyckel eller en SAS tillhandahålls, läses blob-behållare via anonym åtkomst.

Om källan är en filresurs eller -tabell, en nyckel eller en SAS måste anges.

**Gäller:** Blobar, filer, tabeller

### <a name="s"></a>/ S

Anger rekursiv läge för kopieringsåtgärder. I läget för rekursiv kopierar AzCopy alla BLOB-objekt eller filer som matchar den angivna filen mönstret, inklusive de som finns i undermappar.

**Gäller:** Blobar, filer

### <a name="blobtypeblock--page--append"></a>/BlobType:"block" | "page" | "append"

Anger om målblobben är en blockblob, en sidblobb eller en tilläggsblobb. Det här alternativet gäller bara när du laddar upp en blob. Annars genereras ett fel. Om målet är en blob och det här alternativet inte anges som standard skapar AzCopy en blockblob.

**Gäller:** Blobar

### <a name="checkmd5"></a>/ CheckMD5

Beräknar en MD5-hash för hämtade data och verifierar att MD5-hash som lagras i blob eller filens innehåll MD5-egenskap stämmer med den beräknade hashen. Om värdena inte matchar att AzCopy kunna hämta data. MD5-kontrollen är inaktiverad som standard, så du måste ange detta alternativ för att utföra MD5-kontroll när du hämtar data.

Observera att Azure Storage inte garanterar att MD5-hash som lagras i blob eller fillagring är uppdaterad. Det är klientens ansvar att uppdatera MD5 blob eller fillagring ändras. När det gäller diskavbildningar (hanterade eller ohanterade diskar), Azure virtuella datorer uppdaterar inte MD5-värde som disken innehållet ändras, därför /CheckMD5 genereras ett fel när du laddar ned diskavbildningar.

AzCopy v8 anger alltid innehåll MD5-egenskapen för ett Azure blob eller en fil när du har överfört till tjänsten.  

**Gäller:** Blobar, filer

### <a name="snapshot"></a>/ Ögonblicksbild

Anger om du vill överföra ögonblicksbilder. Det här alternativet gäller endast om källan är en blob.

De överförda blobögonblicksbilderna får ett nytt namn i följande format: .extension blob-name (ögonblicksbild-time)

Som standard kopieras inte ögonblicksbilder.

**Gäller:** Blobar

### <a name="vverbose-log-file"></a>/V:[verbose-log-file]

Utdata utförlig statusmeddelanden till en loggfil.

Som standard heter den utförliga loggfilen AzCopyVerbose.log i `%LocalAppData%\Microsoft\Azure\AzCopy`. Om du anger en befintlig plats för det här alternativet läggs den utförliga loggen till filen.  

**Gäller:** Blobar, filer, tabeller

### <a name="zjournal-file-folder"></a>/ Z: [journal-fil / mapp]

Anger en journalmapp för att kunna återuppta en åtgärd.

AzCopy stöder alltid återupptas om en åtgärd har avbrutits.

Om det här alternativet inte anges, eller det anges utan en mappsökväg, skapar AzCopy journalfilen på standardplatsen, vilket är % LocalAppData%\Microsoft\Azure\AzCopy.

Varje gång du utfärda ett kommando till AzCopy, kontrollerar den om en journalfil finns i standardmappen eller om den finns i en mapp som du har angett via det här alternativet. Om journalfilen inte finns på någon plats, AzCopy behandlar åtgärden som nya och genererar en ny journalfil.

Om journalfilen finns kontrollerar AzCopy om den kommandorad som du anger matchar kommandoraden i journal-fil. Om två kommandorader matchar, återupptar AzCopy i åtgärden slutfördes inte. Om de inte matchar, uppmanas du att antingen skriva över journalfil att starta en ny åtgärd eller för att avbryta den aktuella åtgärden.

Journalfilen tas bort vid slutförande av åtgärden.

Observera att återuppta en åtgärd från en journalfil som skapats av en tidigare version av AzCopy inte stöds.

**Gäller:** Blobar, filer, tabeller

### <a name="parameter-file"></a>/@:"parameter-File”

Anger en fil som innehåller parametrar. AzCopy bearbetar parametrarna i filen, precis som om de hade angetts på kommandoraden.

I en svarsfil, kan du ange flera parametrar på en enda rad eller ange varje parameter på en egen rad. Observera att en enskild parameter inte kan omfatta flera rader.

Svarsfiler kan innehålla kommentarer rader som börjar med #-symbolen.

Du kan ange flera svarsfiler. Observera dock att AzCopy inte stöder kapslade svarsfiler.

**Gäller:** Blobar, filer, tabeller

### <a name="y"></a>/Y

Ignorerar alla bekräftelsemeddelanden för AzCopy. Det här alternativet kan också användningen av lässkyddad SAS-token för scenarier för överföring av data, när /XO och /XN inte har angetts.

**Gäller:** Blobar, filer, tabeller

### <a name="l"></a>/L

Anger en liståtgärden. Inga data har kopierats.

AzCopy tolkar den med hjälp av det här alternativet som en simulering för att köra kommandoraden utan det här alternativet/l och antal hur många objekt kopieras kan du ange alternativ /V på samma gång för att kontrollera vilka objekt kopieras i utförliga loggen.

Beteendet för det här alternativet bestäms också av platsen för datakällan och förekomsten av rekursiva läge alternativet /S och filen alternativet mönster /Pattern.

AzCopy kräver LIST och READ behörighet för den här källplatsen när du använder det här alternativet.

**Gäller:** Blobar, filer

### <a name="mt"></a>/MT

Anger den hämtade filen tid för senaste ändring vara samma som källbloben eller filens.

**Gäller:** Blobar, filer

### <a name="xn"></a>/XN

Utesluter en nyare käll-resurs. Resursen är inte kopieras om tid för senaste ändring av källan är samma eller senare än målet.

**Gäller:** Blobar, filer

### <a name="xo"></a>/XO
Utesluter en äldre käll-resurs. Resursen är inte kopieras om tid för senaste ändring av källan är samma eller äldre än målet.

**Gäller:** Blobar, filer

### <a name="a"></a>/A

Överför bara de filer som har attributet Arkiv.

**Gäller:** Blobar, filer

### <a name="iarashcnetoi"></a>/ IA: [RASHCNETOI]

Överför bara de filer som har någon av de angivna attributen.

Tillgängliga attribut är:

* R = skrivskyddade filer
* A = filer som är klara för arkivering
* S = systemfiler
* H = dolda filer
* C = filer som komprimerats filer
* N = normala filer
* E = krypterade filer
* T = temporära filer
* O = Offline-filer
* Jag = icke-indexerade filer

**Gäller:** Blobar, filer

### <a name="xarashcnetoi"></a>/ XA: [RASHCNETOI]

Utesluter filer som har någon av de angivna attributen.

Tillgängliga attribut är:

* R = skrivskyddade filer
* A = filer som är klara för arkivering
* S = systemfiler
* H = dolda filer
* C = filer som komprimerats filer
* N = normala filer
* E = krypterade filer
* T = temporära filer
* O = Offline-filer
* Jag = icke-indexerade filer

**Gäller:** Blobar, filer

### <a name="delimiterdelimiter"></a>/ Avgränsare: ”avgränsare”

Anger avgränsningstecken som används för att avgränsa virtuella kataloger i ett blobnamn.

Som standard använder AzCopy / som avgränsningstecken. Dock AzCopy stöder användning av alla vanliga tecken (till exempel @, #, eller %) som en avgränsare. Om du vill inkludera en av dessa specialtecken på kommandoraden kan du omger du filnamnet med dubbla citattecken.

Det här alternativet gäller endast för att ladda ned blobar.

**Gäller:** Blobar

### <a name="ncnumber-of-concurrent-operations"></a>/NC:"number-of-concurrent-operations"

Anger hur många samtidiga åtgärder.

AzCopy som standard startar ett visst antal samtidiga åtgärder för att öka dataflödet för överföring. Observera att många samtidiga åtgärder i en miljö med låg bandbredd kan utnyttjandet nätverksanslutningen och förhindra åtgärder från att slutföras helt. Begränsa samtidiga åtgärder baserat på faktiska tillgänglig nätverksbandbredd.

Övre gräns för samtidiga åtgärder är 512.

**Gäller:** Blobar, filer, tabeller

### <a name="sourcetypeblob--table"></a>/ SourceType: ”Blob” | ”Tabell”

Anger att den `source` resursen är en blob som är tillgängliga i den lokala utvecklingsmiljö, som körs i storage-emulatorn.

**Gäller:** Blobbar, tabeller

### <a name="desttypeblob--table"></a>/DestType:"Blob" | "Table"

Anger att den `destination` resursen är en blob som är tillgängliga i den lokala utvecklingsmiljö, som körs i storage-emulatorn.

**Gäller:** Blobbar, tabeller

### <a name="pkrskey1key2key3"></a>/PKRS:"key1#key2#key3#..."

Delar upp partitionsnyckelintervall om du vill aktivera dataexport tabell parallellt, vilket ökar hastigheten på exportåtgärden.

Om det här alternativet inte anges använder AzCopy en tråd för att exportera tabellenheter. Exempel: om användaren anger /PKRS: ”aa #bb” och sedan AzCopy startar tre samtidiga åtgärder.

Varje åtgärd exporterar en av tre viktiga partitionsintervall, enligt nedan:

  [första partitionsnyckel, aa)

  [aa, bb)

  [bb senaste partitionsnyckel]

**Gäller:** Tabeller

### <a name="splitsizefile-size"></a>/SplitSize:"file-size"

Anger den exporterade filen dela storlek i MB, det minsta tillåtna värdet är 32.

Om det här alternativet inte anges exporterar AzCopy tabelldata till en enda fil.

Om data exporteras till en blob och exporterade filens storlek når gränsen 200 GB för blobstorlek, sedan AzCopy delar upp den exporterade filen även om det här alternativet inte anges.

**Gäller:** Tabeller

### <a name="entityoperationinsertorskip--insertormerge--insertorreplace"></a>/EntityOperation:"InsertOrSkip" | "InsertOrMerge" | "InsertOrReplace"

Anger Tabellfunktioner för import av data.

* InsertOrSkip - hoppar över en befintlig entitet eller infogar en ny entitet om det inte finns i tabellen.
* InsertOrMerge - sammanfogar en befintlig entitet eller infogar en ny entitet om det inte finns i tabellen.
* InsertOrReplace – ersätter en befintlig entitet eller infogar en ny entitet om det inte finns i tabellen.

**Gäller:** Tabeller

### <a name="manifestmanifest-file"></a>/Manifest:"manifest-file"

Anger manifestfilen för tabellen exportera och importera igen.

Det här alternativet är valfri under exportåtgärden, AzCopy genererar en manifestfil med fördefinierade namn om det här alternativet inte anges.

Det här alternativet krävs under importen för att hitta datafilerna.

**Gäller:** Tabeller

### <a name="synccopy"></a>/SyncCopy

Anger om synkront kopiera blobar och filer mellan två Azure Storage-slutpunkter.

AzCopy som standard använder serversidan asynkrona kopia. Ange detta alternativ för att utföra en synkron kopia som hämtar BLOB-objekt eller filer till lokalt minne och överför dem till Azure Storage.

Du kan använda det här alternativet när du kopierar filer i Blob storage, File storage, eller från Blob storage till File storage och vice versa.

**Gäller:** Blobar, filer

### <a name="setcontenttypecontent-type"></a>/ SetContentType: ”content-type”

Anger MIME-innehållstyp för mål-blobbar eller -filer.

AzCopy anger innehållstypen för en blob eller fillagring till application/octet-ström som standard. Du kan ange innehållstypen för alla BLOB-objekt eller filer genom att ange ett värde för det här alternativet.

Om du anger det här alternativet om inget värde anger AzCopy varje blob eller filens innehållstyp enligt dess filnamnstillägg.

**Gäller:** Blobar, filer

### <a name="payloadformatjson--csv"></a>/PayloadFormat:"JSON" | "CSV"

Anger formatet för den exporterade datafilen i tabellen.

Om det här alternativet inte anges exporterar AzCopy tabell datafilen i JSON-format som standard.

**Gäller:** Tabeller

## <a name="known-issues-and-best-practices"></a>Kända problem och bästa praxis

Låt oss ta en titt på några av de kända problem och bästa praxis.

### <a name="limit-concurrent-writes-while-copying-data"></a>Gräns för samtidiga skrivningar vid kopiering av data

När du kopierar blobar eller filer med AzCopy, Kom ihåg att ett annat program kan ändra data när du kopierar den. Kontrollera om det är möjligt att du kopierar data inte ändras under kopieringen. Till exempel när du kopierar en virtuell Hårddisk som är associerade med virtuella Azure-datorer, se till att inga andra program för närvarande skrivning till den virtuella Hårddisken. Det är ett bra sätt att göra detta genom leasing resursen som ska kopieras. Alternativt kan du först skapa en ögonblicksbild av den virtuella Hårddisken och sedan kopiera ögonblicksbilden.

Om du inte hindra andra program från att skriva till BLOB-objekt eller filer när de kopieras sedan Kom ihåg att när jobbet har slutförts, de kopierade resurserna kan inte längre ha fullständig paritet med resurserna som källa.

### <a name="enable-fips-compliant-md5-algorithms-for-azcopy-when-you-use-fips-compliant-algorithms-for-encryption-hashing-and-signing"></a>Aktivera FIPS-kompatibla MD5 algoritmer för AzCopy när du ”Använd FIPS-kompatibla algoritmer för kryptering, hashing och signering”.

AzCopy som standard använder .NET MD5-implementering för att beräkna MD5 när du kopierar objekt, men det finns vissa krav på säkerhet som behöver AzCopy för att aktivera FIPS-kompatibla MD5-inställningen.

Du kan skapa en app.config-fil `AzCopy.exe.config` med egenskapen `AzureStorageUseV1MD5` och placera den aside med AzCopy.exe.

    <?xml version="1.0" encoding="utf-8" ?>
    <configuration>
      <appSettings>
        <add key="AzureStorageUseV1MD5" value="false"/>
      </appSettings>
    </configuration>

För egenskapen ”AzureStorageUseV1MD5”:

* True - använder standardvärde, AzCopy .NET MD5-implementering.
* Falsk – använder AzCopy FIPS-kompatibla MD5-algoritm.

FIPS-kompatibla algoritmer är inaktiverade som standard i Windows. Du kan ändra den här inställningen på din dator. I fönstret kör (Windows + R) Skriv secpol.msc att öppna den **lokal säkerhetsprincip** fönster. I den **säkerhetsinställningar** fönstret går du till **säkerhetsinställningar** > **lokala principer** > **säkerhetsalternativ**. Leta upp den **Systemkryptografi: Använd FIPS-kompatibla algoritmer för kryptering, hashing och signering** princip. Dubbelklicka på principen för att visa värdet som visas i den **säkerhetsinställningen** kolumn.

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Storage och AzCopy finns i följande resurser:

### <a name="azure-storage-documentation"></a>Dokumentation om Azure Storage:
* [Introduktion till Azure Storage](../storage-introduction.md)
* [Använda Blob storage från .NET](../blobs/storage-dotnet-how-to-use-blobs.md)
* [Hur du använder File storage från .NET](../storage-dotnet-how-to-use-files.md)
* [Använda Table storage från .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Skapa, hantera eller ta bort ett lagringskonto](../storage-create-storage-account.md)
* [Överföra data med AzCopy i Linux](storage-use-azcopy-linux.md)

### <a name="azure-storage-blog-posts"></a>Azure Storage-blogginlägg:
* [Introduktion till Azure Storage Data Movement Library förhandsversion](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/)
* [AzCopy: Introduktion till synkron kopia och anpassade innehållstyp](https://blogs.msdn.com/b/windowsazurestorage/archive/2015/01/13/azcopy-introducing-synchronous-copy-and-customized-content-type.aspx)
* [AzCopy: Meddelande om allmän tillgänglighet av AzCopy 3.0 plus förhandsversionen av AzCopy 4.0 med stöd för tabell och fil](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/10/29/azcopy-announcing-general-availability-of-azcopy-3-0-plus-preview-release-of-azcopy-4-0-with-table-and-file-support.aspx)
* [AzCopy: Optimerad för storskaliga kopia scenarier](https://go.microsoft.com/fwlink/?LinkId=507682)
* [AzCopy: Stöd för läsåtkomst till geografiskt redundant lagring](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/04/07/azcopy-support-for-read-access-geo-redundant-account.aspx)
* [AzCopy: Överföra data med omstartsläge och SAS-token](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/09/07/azcopy-transfer-data-with-re-startable-mode-and-sas-token.aspx)
* [AzCopy: Med hjälp av flera konto kopiering av Blob](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/04/01/azcopy-using-cross-account-copy-blob.aspx)
* [AzCopy: Ladda upp/ned filer för Azure-Blobar](https://blogs.msdn.com/b/windowsazurestorage/archive/2012/12/03/azcopy-uploading-downloading-files-for-windows-azure-blobs.aspx)
