---
title: Kopiera eller flytta data till Azure Storage med AzCopy i Windows | Microsoft Docs
description: "Använd AzCopy på Windows-verktyg för att flytta eller kopiera data till och från blob-, tabell- och innehåll. Kopiera data till Azure Storage från lokala filer eller kopiera data inom eller mellan lagringskonton. Enkelt migrera dina data till Azure Storage."
services: storage
documentationcenter: 
author: seguler
manager: jahogg
editor: tysonn
ms.assetid: aa155738-7c69-4a83-94f8-b97af4461274
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2018
ms.author: seguler
ms.openlocfilehash: 13e09a3081c9dfa2d88625489a82c687d6722f20
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="transfer-data-with-the-azcopy-on-windows"></a>Överföra data med AzCopy i Windows
AzCopy är ett kommandoradsverktyg som utformats för att kopiera data till och från Microsoft Azure Blob-, fil- och Table storage med hjälp av enkla kommandon som utformats för optimala prestanda. Du kan kopiera data mellan ett filsystem och ett lagringskonto eller mellan lagringskonton.  

Det finns två versioner av AzCopy som du kan hämta. AzCopy i Windows har skapats med .NET Framework och erbjuder kommandoradsalternativ för Windows-formatet. [AzCopy på Linux](storage-use-azcopy-linux.md) har byggts med .NET Core Framework som riktar sig till Linux-plattformar som erbjuder POSIX format kommandoradsalternativ. Den här artikeln beskriver AzCopy i Windows.

## <a name="download-and-install-azcopy-on-windows"></a>Hämta och installera AzCopy i Windows

Hämta den [senaste versionen av AzCopy på Windows](http://aka.ms/downloadazcopy).

När du har installerat AzCopy i Windows med installationsprogrammet, öppna ett kommandofönster och gå till installationskatalogen för AzCopy på din dator - där den `AzCopy.exe` körbara finns. Om du vill kan du lägga till installationsplatsen AzCopy systemsökvägen. Som standard installeras AzCopy på `%ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy` eller `%ProgramFiles%\Microsoft SDKs\Azure\AzCopy`.

## <a name="writing-your-first-azcopy-command"></a>Skriva ditt första AzCopy-kommandot

Den grundläggande syntaxen för AzCopy kommandon är:

```azcopy
AzCopy /Source:<source> /Dest:<destination> [Options]
```

Följande exempel visar en mängd olika scenarier för att kopiera data till och från Microsoft Azure-Blobbar, filer och tabeller. Referera till den [AzCopy parametrar](#azcopy-parameters) avsnittet för en detaljerad förklaring av de parametrar som används i varje prov.

## <a name="download-blobs-from-blob-storage"></a>Ladda ned blobbar från Blob storage

Nu ska vi titta på flera sätt att hämta blobar med hjälp av AzCopy.

### <a name="download-a-single-blob"></a>Hämta en enda blob

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:"abc.txt"
```

Observera att om mappen `C:\myfolder` finns inte, AzCopy skapar den och ladda ned `abc.txt ` till den nya mappen.

### <a name="download-a-single-blob-from-the-secondary-region"></a>Hämta en enda blob från den sekundära regionen

```azcopy
AzCopy /Source:https://myaccount-secondary.blob.core.windows.net/mynewcontainer /Dest:C:\myfolder /SourceKey:key /Pattern:"abc.txt"
```

Observera att du måste ha läsbehörighet geo-redundant lagring aktiverad för att få åtkomst till den sekundära regionen.

### <a name="download-all-blobs-in-a-container"></a>Hämta alla blobbar i en behållare

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /S
```

Anta följande blobbar finns i den angivna behållaren:  

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

Om du inte anger alternativet `/S`, hämtas inga blobbar.

### <a name="download-blobs-with-a-specific-prefix"></a>Ladda ned blobbar med ett specifikt prefix

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:a /S
```

Anta att följande blobbar finns i den angivna behållaren. Alla blobbar som börjar med prefixet `a` hämtas:

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

Prefixet som gäller för den virtuella katalogen, som utgör den första delen av blobbnamnet. I exemplet ovan, matchar den virtuella katalogen inte det angivna prefixet så inte laddas ned. Dessutom, om alternativet `/S` anges AzCopy inte hämta alla blobbar.

### <a name="set-the-last-modified-time-of-exported-files-to-be-same-as-the-source-blobs"></a>Ange last-modified-tid med exporterade filer som ska vara densamma som källan BLOB

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT
```

Du kan också utesluta blobbar från hämtningen baserat på deras tid för senaste ändring. Till exempel om du vill undanta blobbar vars senast ändrad är samma som eller nyare än målfilen och lägga till den `/XN` alternativ:

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT /XN
```

Om du vill undanta blobbar vars senast ändrad är samma eller äldre än målfilen och lägga till den `/XO` alternativ:

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT /XO
```

## <a name="upload-blobs-to-blob-storage"></a>Överför blobbar till Blob storage

Nu ska vi titta på flera olika sätt att överföra blobar med hjälp av AzCopy.

### <a name="upload-a-single-blob"></a>Överför en enda blob

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:"abc.txt"
```

Om den angivna Målbehållaren inte finns, skapar den AzCopy och överför filen till den.

### <a name="upload-a-single-blob-to-a-virtual-directory"></a>Ladda upp en enda blob till en virtuell katalog

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer/vd /DestKey:key /Pattern:abc.txt
```

Om den angivna virtuella katalogen inte finns, AzCopy överför filen så att den virtuella katalogen i sitt namn (*t.ex.*, `vd/abc.txt` i exemplet ovan).

### <a name="upload-all-blobs-in-a-folder"></a>Ladda upp alla blobbar i en mapp

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /S
```

Om du anger alternativet `/S` Överför innehållet i den angivna katalogen till Blob storage rekursivt, vilket innebär att alla undermappar och filer överförs också. Anta exempelvis att följande filer finns i mappen `C:\myfolder`:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\subfolder\a.txt
    C:\myfolder\subfolder\abcd.txt

När överföringen innehåller behållaren följande filer:

    abc.txt
    abc1.txt
    abc2.txt
    subfolder\a.txt
    subfolder\abcd.txt

Om du inte anger alternativet `/S`, AzCopy inte överföra rekursivt. När överföringen innehåller behållaren följande filer:

    abc.txt
    abc1.txt
    abc2.txt

### <a name="upload-blobs-matching-a-specific-pattern"></a>Överför blobbar som matchar ett specifikt mönster

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

När överföringen innehåller behållaren följande filer:

    abc.txt
    abc1.txt
    abc2.txt
    subfolder\a.txt
    subfolder\abcd.txt

Om du inte anger alternativet `/S`, AzCopy Överför bara blob som inte finns i en virtuell katalog:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt

### <a name="specify-the-mime-content-type-of-a-destination-blob"></a>Ange MIME content-type för en mål-blob

Som standard anges AzCopy innehållstypen för en mål-blob till `application/octet-stream`. Från och med version 3.1.0, du kan uttryckligen ange content-type via alternativet `/SetContentType:[content-type]`. Den här syntaxen anger content-type för alla blobbar i en överföringen.

```azcopy
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.blob.core.windows.net/myContainer/ /DestKey:key /Pattern:ab /SetContentType:video/mp4
```

Om du anger `/SetContentType` utan värde, AzCopy anger varje blob eller filens innehållstyp enligt dess filnamnstillägg.

```azcopy
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.blob.core.windows.net/myContainer/ /DestKey:key /Pattern:ab /SetContentType
```

## <a name="copy-blobs-in-blob-storage"></a>Kopiera BLOB i Blob storage

Nu ska vi titta på flera olika sätt att kopiera BLOB från en plats till en annan med hjälp av AzCopy.

### <a name="copy-a-single-blob-from-one-container-to-another-within-the-same-storage-account"></a>Kopiera en enda blob från en behållare till en annan inom samma lagringskonto 

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1 /Dest:https://myaccount.blob.core.windows.net/mycontainer2 /SourceKey:key /DestKey:key /Pattern:abc.txt
```

När du kopierar en blobb inom ett lagringskonto, en [serversidan kopiera](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) åtgärden utförs.

### <a name="copy-a-single-blob-from-one-storage-account-to-another"></a>Kopiera en enda blob från ett lagringskonto till en annan

```azcopy
AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt
```

När du kopierar en blobb mellan lagringskonton, en [serversidan kopiera](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) åtgärden utförs.

### <a name="copy-a-single-blob-from-the-secondary-region-to-the-primary-region"></a>Kopiera en enda blob från den sekundära regionen till den primära regionen

```azcopy
AzCopy /Source:https://myaccount1-secondary.blob.core.windows.net/mynewcontainer1 /Dest:https://myaccount2.blob.core.windows.net/mynewcontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt
```

Observera att du måste ha läsbehörighet geo-redundant lagring aktiverat åtkomst till sekundär lagring.

### <a name="copy-a-single-blob-and-its-snapshots-from-one-storage-account-to-another"></a>Kopiera en enda blob och dess ögonblicksbilder från ett lagringskonto

```azcopy
AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt /Snapshot
```

Efter kopieringen innehåller Målbehållaren blob och dess ögonblicksbilder. Under förutsättning att blob i exemplet ovan har två ögonblicksbilder, innehåller behållaren följande blob och ögonblicksbilder:

    abc.txt
    abc (2013-02-25 080757).txt
    abc (2014-02-21 150331).txt

### <a name="copy-all-blobs-in-a-container-to-another-storage-account"></a>Kopiera alla blobbar i en behållare till ett annat lagringskonto 

```azcopy
AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 
/Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /S
```

Om du anger alternativet överför /S innehållet i den angivna behållare rekursivt. Se [överföra alla blobbar i en mapp](#upload-all-blobs-in-a-folder) mer information och exempel.

### <a name="synchronously-copy-blobs-from-one-storage-account-to-another"></a>Kopiera synkront blobbar från ett lagringskonto till en annan

AzCopy som standard kopierar data mellan två slutpunkter för lagring asynkront. Därför körs kopieringen bakgrunden med hjälp av ledig bandbreddskapacitet som har inga SLA vad gäller hur snabbt en blob kopieras och AzCopy söker regelbundet kopian status tills kopiering är slutförd eller misslyckad.

Den `/SyncCopy` alternativet ser du till att kopieringen hämtar konsekvent hastighet. AzCopy utför synkron kopian genom att hämta blobbarna att kopiera från den angivna källan till lokalt minne och överför dem till Blob-lagringsplats.

```azcopy
AzCopy /Source:https://myaccount1.blob.core.windows.net/myContainer/ /Dest:https://myaccount2.blob.core.windows.net/myContainer/ /SourceKey:key1 /DestKey:key2 /Pattern:ab /SyncCopy
```

`/SyncCopy` kan skapa ytterligare utgång kostnaden jämfört med asynkron kopia, den rekommenderade metoden är att använda det här alternativet i en virtuell Azure-dator som är i samma region som ditt källa storage-konto för att undvika kostnader för utgående trafik.

## <a name="download-files-from-file-storage"></a>Hämta filer från File storage

Nu ska vi titta på flera sätt att hämta filer med hjälp av AzCopy.

### <a name="download-a-single-file"></a>Hämta en fil

```azcopy
AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/myfolder1/ /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt
```

Om den angivna källan är en Azure-filresurs och sedan måste du antingen ange det exakta filnamnet (*t.ex.* `abc.txt`) att hämta en fil eller ange alternativet `/S` att ladda ned alla filer i resursen rekursivt. Försök att ange både filmönstret och alternativet `/S` tillsammans resulterar i ett fel.

### <a name="download-all-files-in-a-directory"></a>Hämta alla filer i en katalog

```azcopy
AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/ /Dest:C:\myfolder /SourceKey:key /S
```

Observera att inte hämtas tomma mappar.

## <a name="upload-files-to-an-azure-file-share"></a>Överföra filer till en filresurs på Azure

Nu ska vi titta på flera olika sätt att överföra filer med hjälp av AzCopy.

### <a name="upload-a-single-file"></a>Överför en fil

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /Pattern:abc.txt
```

### <a name="upload-all-files-in-a-folder"></a>Ladda upp alla filer i en mapp

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /S
```

Observera att inte överförs tomma mappar.

### <a name="upload-files-matching-a-specific-pattern"></a>Överföra filer som matchar ett specifikt mönster

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /Pattern:ab* /S
```

## <a name="copy-files-in-file-storage"></a>Kopiera filerna i File storage

Nu ska vi titta på flera olika sätt att kopiera filer i en Azure-filresurs med hjälp av AzCopy.

### <a name="copy-from-one-file-share-to-another"></a>Kopiera från en filresurs till en annan

```azcopy
AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare1/ /Dest:https://myaccount2.file.core.windows.net/myfileshare2/ /SourceKey:key1 /DestKey:key2 /S
```
När du kopierar en fil på filresurser, en [serversidan kopiera](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) åtgärden utförs.

### <a name="copy-from-an-azure-file-share-to-blob-storage"></a>Kopiera från en Azure-filresurs till Blob storage

```azcopy
AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare/ /Dest:https://myaccount2.blob.core.windows.net/mycontainer/ /SourceKey:key1 /DestKey:key2 /S
```
När du kopierar en fil från filresursen till blob, en [serversidan kopiera](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) åtgärden utförs.

### <a name="copy-a-blob-from-blob-storage-to-an-azure-file-share"></a>Kopiera en blobb från Blob storage till en filresurs på Azure

```azcopy
AzCopy /Source:https://myaccount1.blob.core.windows.net/mycontainer/ /Dest:https://myaccount2.file.core.windows.net/myfileshare/ /SourceKey:key1 /DestKey:key2 /S
```
När du kopierar en fil från en blobb till en filresurs, en [serversidan kopiera](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) åtgärden utförs.

### <a name="synchronously-copy-files"></a>Synkront kopiera filer

Du kan ange den `/SyncCopy` alternativet för att kopiera data från fillagring för lagring, lagring av filer till Blob Storage och Blob Storage till File Storage synkront, AzCopy gör detta genom att hämta källdata till lokalt minne och ladda upp den igen till mål. Standard utgång kostnaden gäller.

```azcopy
AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare1/ /Dest:https://myaccount2.file.core.windows.net/myfileshare2/ /SourceKey:key1 /DestKey:key2 /S /SyncCopy
```

När du kopierar från fillagring till Blob storage är-blob standardtypen blockblob; användaren kan ange alternativ för `/BlobType:page` att ändra typen av mål-blob.

Observera att `/SyncCopy` kan skapa ytterligare utgång kostnader, jämfört med asynkron kopia. Den rekommenderade metoden är att använda det här alternativet i den virtuella Azure-datorn som är i samma region som ditt källa storage-konto för att undvika kostnader för utgående trafik.

## <a name="export-data-from-table-storage"></a>Exportera data från tabellagring

Låt oss ta en titt på Exportera data från Azure Table storage med hjälp av AzCopy.

### <a name="export-a-table"></a>Exportera en tabell

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key
```

AzCopy skriver en manifestfil till angiven målmapp. Manifestfilen används i importen för att hitta de nödvändiga filerna och utföra dataverifiering. Manifestfilen använder följande namngivningskonvention som standard:

    <account name>_<table name>_<timestamp>.manifest

Användaren kan även ange alternativet `/Manifest:<manifest file name>` att ange namnet på manifestfilen.

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /Manifest:abc.manifest
```

### <a name="split-an-export-from-table-storage-into-multiple-files"></a>Dela en export från tabellagring i flera filer

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/mytable/ /Dest:C:\myfolder /SourceKey:key /S /SplitSize:100
```

AzCopy använder en *volymindex* i filnamnen dela data att skilja flera filer. Volymindexet består av två delar, en *partition viktiga områdesindex* och en *dela filen index*. Både index är nollbaserade.

Index för partitionen viktiga intervallet är 0 om användaren inte anger alternativet `/PKRS`.

Anta exempelvis AzCopy genererar två datafiler när användaren anger alternativet `/SplitSize`. De resulterande filnamn data kan vara:

    myaccount_mytable_20140903T051850.8128447Z_0_0_C3040FE8.json
    myaccount_mytable_20140903T051850.8128447Z_0_1_0AB9AC20.json

Observera att minsta möjliga värde för alternativet `/SplitSize` 32 MB. Om det angivna målet är Blob storage, AzCopy delar upp datafilen när dess storlek når storleksbegränsning för blob (200GB), oavsett om alternativet `/SplitSize` har angetts av användaren.

### <a name="export-a-table-to-json-or-csv-data-file-format"></a>Exportera en tabell till JSON eller CSV-filformat för data

Som standard exporterar AzCopy tabeller till datafiler i JSON. Du kan ange alternativet `/PayloadFormat:JSON|CSV` att exportera tabellerna som JSON- eller CSV.

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /PayloadFormat:CSV
```

När du anger nyttolastformatet CSV, AzCopy också genererar en schemafil med filnamnstillägget `.schema.csv` för varje datafil.

### <a name="export-table-entities-concurrently"></a>Exportera tabellentiteter samtidigt

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /PKRS:"aa#bb"
```

AzCopy startar samtidiga åtgärder om du vill exportera entiteter när användaren anger alternativet `/PKRS`. Varje åtgärd exporterar en partitionsnyckelintervallet.

Observera att antalet samtidiga åtgärder också styrs av alternativet `/NC`. AzCopy använder antalet kärnprocessorer som standardvärdet `/NC` när du kopierar tabellen enheter, även om `/NC` har inte angetts. När användaren anger alternativet `/PKRS`, AzCopy använder mindre av de två värdena - partition nyckelintervall jämfört med implicit eller explicit anges samtidiga åtgärder – ta reda på antalet samtidiga åtgärder att starta. Mer information skriver du `AzCopy /?:NC` på kommandoraden.

### <a name="export-a-table-to-blob-storage"></a>Exportera en tabell till Blob storage

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:https://myaccount.blob.core.windows.net/mycontainer/ /SourceKey:key1 /Destkey:key2
```

AzCopy genererar en JSON-fil för data i blob-behållaren med följande namngivningskonvention:

    <account name>_<table name>_<timestamp>_<volume index>_<CRC>.json

Den genererade JSON-datafilen följer nyttolastformat för minimal metadata. Mer information om den här nyttolastformatet finns [Nyttolastformat för tabellen tjänståtgärder](http://msdn.microsoft.com/library/azure/dn535600.aspx).

Observera att när du exporterar tabeller till blobbar, AzCopy hämtar tabellentiteter till lokala temporära datafiler och sedan överför dessa enheter till blob. Dessa temporära datafiler placeras i mappen journal filen med standardsökvägen ”<code>%LocalAppData%\Microsoft\Azure\AzCopy</code>”, du kan ange alternativet/Z: [journal-filmapp] att ändra journalen filen mapp och därmed ändra platsen för tillfälliga data-filer. Tillfällig data filernas storlek bestäms av din tabellentiteter och storlek som du har angett med alternativet-/SplitSize även om den tillfälliga datafilen i lokal disk tas bort omedelbart när den har överförts till blob, kontrollera att du har tillräckligt med lokala diskutrymme för att lagra filerna tillfälliga data innan de tas bort.

## <a name="import-data-into-table-storage"></a>Importera data till Table storage

Låt oss ta en titt på Importera data till Azure Table storage med hjälp av AzCopy.

### <a name="import-a-table"></a>Importera en tabell

```azcopy
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.table.core.windows.net/mytable1/ /DestKey:key /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:InsertOrReplace
```

Alternativet `/EntityOperation` visar hur du infogar entiteter i tabellen. Möjliga värden:

* `InsertOrSkip`: Hoppar över en befintlig entitet eller infogar en ny entitet om den inte finns i tabellen.
* `InsertOrMerge`: Sammanfogar en befintlig entitet eller infogar en ny entitet om den inte finns i tabellen.
* `InsertOrReplace`: Ersätter en befintlig entitet eller infogar en ny entitet om den inte finns i tabellen.

Observera att du inte kan ange alternativet `/PKRS` i import-scenariot. Till skillnad från export-scenariot, där du måste ange alternativet `/PKRS` om du vill starta samtidiga åtgärder AzCopy samtidiga åtgärder som standard startar när du importerar en tabell. Standardvärdet för antalet samtidiga åtgärder igång är lika med antalet kärnprocessorer; Du kan dock ange ett annat antal samtidiga med alternativet `/NC`. Mer information skriver du `AzCopy /?:NC` på kommandoraden.

Observera att AzCopy endast stöder import för JSON inte CSV. AzCopy har inte stöd för import av tabell från användarskapade JSON och manifest filer. Båda dessa filer måste komma från en tabell AzCopy-export. För att undvika fel kan du inte ändra exporterade JSON eller manifestfil.

### <a name="import-entities-into-a-table-from-blob-storage"></a>Importera entiteter i en tabell från Blob storage

Anta en Blob-behållare innehåller följande: en JSON-fil som representerar en Azure-tabellen och dess tillhörande manifestfilen.

    myaccount_mytable_20140103T112020.manifest
    myaccount_mytable_20140103T112020_0_0_0AF395F1DC42E952.json

Du kan köra följande kommando för att importera entiteter i en tabell med manifestfilen i den blob-behållaren:

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:https://myaccount.table.core.windows.net/mytable /SourceKey:key1 /DestKey:key2 /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:"InsertOrReplace"
```

## <a name="other-azcopy-features"></a>Andra AzCopy-funktioner

Låt oss ta en titt på några andra funktioner för AzCopy.

### <a name="only-copy-data-that-doesnt-exist-in-the-destination"></a>Endast kopiera data som inte finns i målet

Den `/XO` och `/XN` parametrar kan du exkludera resurser som tidigare eller senare från att kopieras respektive. Om du bara vill kopiera käll-resurser som inte finns i målet kan du ange båda parametrarna i AzCopy-kommandot:

    /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /XO /XN

    /Source:C:\myfolder /Dest:http://myaccount.file.core.windows.net/myfileshare /DestKey:<destkey> /S /XO /XN

    /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:http://myaccount.blob.core.windows.net/mycontainer1 /SourceKey:<sourcekey> /DestKey:<destkey> /S /XO /XN

Observera att detta inte stöds när källan eller målet är en tabell.

### <a name="use-a-response-file-to-specify-command-line-parameters"></a>Använda en svarsfil för att ange kommandoradsparametrar

```azcopy
AzCopy /@:"C:\responsefiles\copyoperation.txt"
```

Du kan inkludera eventuella kommandoradsparametrar för AzCopy i en svarsfil. AzCopy bearbetar parametrarna i filen som om de hade angetts på kommandoraden, utför en direkt ersättning med innehållet i filen.

Anta en svarsfil med namnet `copyoperation.txt`, som innehåller följande rader. Varje AzCopy-parameter kan anges på en enda rad

    /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /Y

eller på separata rader:

    /Source:http://myaccount.blob.core.windows.net/mycontainer
    /Dest:C:\myfolder
    /SourceKey:<sourcekey>
    /S
    /Y

AzCopy misslyckas om du vill dela parametern på två rader som visas här för den `/sourcekey` parameter:

    http://myaccount.blob.core.windows.net/mycontainer
     C:\myfolder
    /sourcekey:
    <sourcekey>
    /S
    /Y

### <a name="use-multiple-response-files-to-specify-command-line-parameters"></a>Använda flera svarsfiler för att ange kommandoradsparametrar

Anta en svarsfil med namnet `source.txt` som anger en käll-behållare:

    /Source:http://myaccount.blob.core.windows.net/mycontainer

Och en svarsfil med namnet `dest.txt` som anger en målmapp i filsystemet:

    /Dest:C:\myfolder

Och en svarsfil med namnet `options.txt` som anger alternativ för AzCopy:

    /S /Y

För att anropa AzCopy med dessa svarsfiler, som finns i en katalog `C:\responsefiles`, använder du följande kommando:

```azcopy
AzCopy /@:"C:\responsefiles\source.txt" /@:"C:\responsefiles\dest.txt" /SourceKey:<sourcekey> /@:"C:\responsefiles\options.txt"   
```

AzCopy bearbetar det här kommandot precis som om du har inkluderat alla enskilda parametrar på kommandoraden:

```azcopy
AzCopy /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /Y
```

### <a name="specify-a-shared-access-signature-sas"></a>Ange en signatur för delad åtkomst (SAS)

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1 /Dest:https://myaccount.blob.core.windows.net/mycontainer2 /SourceSAS:SAS1 /DestSAS:SAS2 /Pattern:abc.txt
```

Du kan också ange en SAS för URI-behållaren:

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1/?SourceSASToken /Dest:C:\myfolder /S
```

### <a name="journal-file-folder"></a>Ändringsjournalen mapp

Varje gång du utfärda ett kommando till AzCopy, kontrollerar den om en journal-fil finns i standardmappen eller om den finns i en mapp som du har angett via det här alternativet. Om journalfilen inte finns på någon plats, behandlar åtgärden som nya AzCopy och genererar en ny journalfil.

Om journalfilen finns kontrollerar AzCopy om den kommandorad som du matar in matchar kommandoraden i journal-fil. Om två kommandorader matchar återupptar AzCopy ofullständiga igen. Om de inte matchar, uppmanas du att antingen skriva över journalfilen att starta en ny åtgärd eller Avbryt den aktuella åtgärden.

Om du vill använda standardplatsen för journalfilen:

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Z
```

Om du utelämnar alternativet `/Z`, eller ange alternativet `/Z` utan sökvägen till mappen som du ser ovan, AzCopy journalfilen skapas i standardplatsen, som är `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`. Om ändringsjournalen filen redan finns återupptas AzCopy igen baserat på journal-fil.

Om du vill ange en anpassad plats för journalfilen:

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Z:C:\journalfolder\
```

Det här exemplet skapar journal-fil om den inte redan finns. Om den finns, återupptar AzCopy igen baserat på journal-fil.

Om du vill fortsätta AzCopy:

```azcopy
AzCopy /Z:C:\journalfolder\
```

Det här exemplet fortsätter med den senaste åtgärden som inte kunde slutföras.

### <a name="generate-a-log-file"></a>Generera en loggfil

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /V
```

Om du anger alternativet `/V` utan att ange en sökväg till den detaljerade loggen AzCopy skapar sedan loggfilen på standardplatsen, vilket är `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`.

Annars kan du skapa en loggfil på en annan plats:

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /V:C:\myfolder\azcopy1.log
```

Observera att om du anger en relativ sökväg efter alternativet `/V`, som `/V:test/azcopy1.log`, och sedan den detaljerade loggen skapas i den aktuella arbetskatalogen i en undermapp som heter `test`.

### <a name="specify-the-number-of-concurrent-operations-to-start"></a>Ange antalet samtidiga åtgärder att starta

Alternativet `/NC` anger antalet samtidiga kopieringsåtgärd. Som standard startar AzCopy antalet samtidiga åtgärder att öka genomflödet data transfer. Antalet samtidiga åtgärder är lika med antalet processorer som du har för tabellåtgärder. För Blob- och fil-åtgärder, antalet samtidiga åtgärder är lika med 8 gånger antalet processorer som du har. Om du använder AzCopy över ett nätverk med låg bandbredd, kan du ange ett lägre värde /NC att undvika misslyckades på grund av konkurrens om resurser.

### <a name="run-azcopy-against-the-azure-storage-emulator"></a>Köra AzCopy mot Azure Storage-emulatorn

Du kan köra AzCopy mot den [Azure Storage-emulatorn](storage-use-emulator.md) för BLOB:

```azcopy
AzCopy /Source:https://127.0.0.1:10000/myaccount/mycontainer/ /Dest:C:\myfolder /SourceKey:key /SourceType:Blob /S
```

Du kan också köra det för tabeller:

```azcopy
AzCopy /Source:https://127.0.0.1:10002/myaccount/mytable/ /Dest:C:\myfolder /SourceKey:key /SourceType:Table
```

## <a name="azcopy-parameters"></a>AzCopy-parametrar

Parametrar för AzCopy beskrivs nedan. Du kan också ange något av följande kommandon från kommandoraden för att få hjälp med AzCopy:

* För detaljerad hjälp för AzCopy: `AzCopy /?`
* För detaljerad hjälp om någon AzCopy-parameter: `AzCopy /?:SourceKey`
* Kommandoradsverktyget exempel: `AzCopy /?:Sample`

### <a name="sourcesource"></a>/ Source: ”källa”

Anger källdata från som ska kopieras. Källan kan vara en katalog i filsystemet, en blob-behållare, en virtuell katalog för blob, en lagringsfilresurs, en katalog i lagring eller en Azure-tabellen.

**Gäller för:** Blobbar, filer, tabeller

### <a name="destdestination"></a>/ Dest: ”mål”

Anger att kopiera till målet. Målet kan vara en katalog i filsystemet, en blob-behållare, en virtuell katalog för blob, en lagringsfilresurs, en katalog i lagring eller en Azure-tabellen.

**Gäller för:** Blobbar, filer, tabeller

### <a name="patternfile-pattern"></a>/ Mönstret: ”filmönstret”

Anger ett fil-mönster som anger vilka filer som ska kopieras. Beteendet för parametern /Pattern bestäms av platsen för datakällan och förekomsten av alternativet rekursiv läge. Rekursiva läge har angetts via alternativet/s.

Om den angivna källan är en katalog i filsystemet, sedan standard jokertecken är aktiverat och det angivna mönstret för filen matchas mot filer i katalogen. Om alternativet /S anges sedan AzCopy också matchar det angivna mönstret mot alla filer i alla undermappar under katalogen.

Om den angivna källan är en blob-behållare eller virtuell katalog, tillämpas inte jokertecken. Om alternativet /S anges sedan AzCopy tolkas det angivna mönstret som ett blob-prefix. Om alternativet inte anges /S sedan AzCopy matchar mönstret filen mot exakt blob-namn.

Om den angivna källan är en Azure-filresurs, så du måste antingen ange exakt filnamnet, (t.ex. abc.txt) om du vill kopiera en fil eller ange alternativet /S att kopiera alla filer i resursen rekursivt. Försök att ange både en fil mönstret och alternativet /S tillsammans resulterar i ett fel.

AzCopy använder skiftlägeskänsliga matchar när den/Source är en blob-behållare eller blob virtuell katalog och använder icke skiftlägeskänslig matchning i andra fall.

Det standardmönster för filen som används när inga filmönstret har angetts är *.* för en plats för system eller ett tomt prefix för en Azure-lagringsplats. Ange flera filen mönster stöds inte.

**Gäller för:** Blobbar, filer

### <a name="destkeystorage-key"></a>/DestKey:"storage-key"

Anger lagringskontonyckel för målresurs.

**Gäller för:** Blobbar, filer, tabeller

### <a name="destsassas-token"></a>/DestSAS:"sas-token"

Anger en delad signatur åtkomst (SAS) med behörigheter för Läs- och skrivbehörighet för mål (om tillämpligt). Omge SAS med dubbla citattecken, eftersom det kanske innehåller kommandoradsverktyget specialtecken.

Om mål-resursen är en blob-behållare, en filresurs eller en tabell, du kan antingen ange det här alternativet följt av SAS-token eller du kan ange SAS som en del av mål-blob-behållare, filresurser eller tabellens URI, utan det här alternativet.

Om käll- och båda blobbar, måste mål-blob finnas inom samma lagringskonto som käll-blob.

**Gäller för:** Blobbar, filer, tabeller

### <a name="sourcekeystorage-key"></a>/SourceKey:"storage-key"

Anger lagringskontonyckel för käll-resurs.

**Gäller för:** Blobbar, filer, tabeller

### <a name="sourcesassas-token"></a>/ SourceSAS: ”sas-token”

Anger en signatur för delad åtkomst med läs- och behörighet för källan (om tillämpligt). Omge SAS med dubbla citattecken, eftersom det kanske innehåller kommandoradsverktyget specialtecken.

Om källa resursen är en blob-behållare och varken en nyckel eller en SAS anges, läses blob-behållaren via anonym åtkomst.

Om källan är en filresurs eller tabell, en nyckel eller en SAS måste anges.

**Gäller för:** Blobbar, filer, tabeller

### <a name="s"></a>/S

Anger rekursiv kopieringsåtgärd. AzCopy kopierar alla blobbar eller filer som matchar det angivna mönstret, inklusive de på undermappar i rekursiva läge.

**Gäller för:** Blobbar, filer

### <a name="blobtypeblock--page--append"></a>/ BlobType: ”block” | ”sidan” | ”Lägg till”

Anger om mål-blob är en blockblobb, en sidblob eller en tilläggsblobb. Det här alternativet gäller bara när du laddar upp en blob. Annars genereras ett fel. Om målet är en blob och det här alternativet inte anges som standard skapas en blockblobb AzCopy.

**Gäller för:** Blobbar

### <a name="checkmd5"></a>/ CheckMD5

Beräknar en MD5-hash för hämtade data och verifierar att MD5-hash lagras i blob eller filens Content-MD5-egenskap stämmer med den beräknade hashen. MD5-kontrollen är inaktiverad som standard, så du måste ange det här alternativet för att kontrollera MD5 när du hämtar data.

Observera att Azure Storage inte garantera att den MD5-hash som lagrats till filen eller blob är uppdaterad. Det är klientens ansvar att uppdatera MD5 när blob eller fil ändras.

AzCopy egenskapen alltid Content-MD5 för ett Azure blob eller en fil efter överföring till tjänsten.  

**Gäller för:** Blobbar, filer

### <a name="snapshot"></a>/Snapshot

Anger om du vill överföra ögonblicksbilder. Det här alternativet gäller endast om källan är en blob.

Överförda blob-ögonblicksbilder ändras i det här formatet: .extension blobbnamnet (snapshot-time)

Som standard kopieras inte ögonblicksbilder.

**Gäller för:** Blobbar

### <a name="vverbose-log-file"></a>/V:[verbose-log-file]

Utdata utförlig statusmeddelanden till en loggfil.

Som standard heter den utförliga loggfilen AzCopyVerbose.log i `%LocalAppData%\Microsoft\Azure\AzCopy`. Om du anger en befintlig plats för det här alternativet läggs den detaljerade loggen till filen.  

**Gäller för:** Blobbar, filer, tabeller

### <a name="zjournal-file-folder"></a>/Z:[journal-file-folder]

Anger en mapp för att återuppta en åtgärd journalen.

AzCopy stöder alltid fortsätter om en åtgärd har avbrutits.

Om det här alternativet inte har angetts eller om den har angetts utan en mappsökväg, skapar AzCopy journal-fil på standardplatsen är % LocalAppData%\Microsoft\Azure\AzCopy.

Varje gång du utfärda ett kommando till AzCopy, kontrollerar den om en journal-fil finns i standardmappen eller om den finns i en mapp som du har angett via det här alternativet. Om journalfilen inte finns på någon plats, behandlar åtgärden som nya AzCopy och genererar en ny journalfil.

Om journalfilen finns kontrollerar AzCopy om den kommandorad som du matar in matchar kommandoraden i journal-fil. Om två kommandorader matchar återupptar AzCopy ofullständiga igen. Om de inte matchar, uppmanas du att antingen skriva över journalfilen att starta en ny åtgärd eller Avbryt den aktuella åtgärden.

Journalfilen tas bort vid slutförande av åtgärden.

Observera att återuppta en åtgärd från en journal-fil som skapats av en tidigare version av AzCopy inte stöds.

**Gäller för:** Blobbar, filer, tabeller

### <a name="parameter-file"></a>/@:"parameter-file"

Anger en fil som innehåller parametrar. AzCopy bearbetar parametrarna i filen, precis som om de hade angetts på kommandoraden.

I en svarsfil, kan du antingen ange flera parametrar på en enda rad eller ange varje parameter på en egen rad. Observera att en enskild parameter inte kan innehålla flera rader.

Svarsfiler kan innehålla kommentarer rader som börjar med symbolen #.

Du kan ange flera svarsfiler. Observera dock att AzCopy inte stöder kapslade svarsfiler.

**Gäller för:** Blobbar, filer, tabeller

### <a name="y"></a>/Y

Förhindrar att alla meddelanden för bekräftelse av AzCopy. Det här alternativet kan också använda lässkyddad SAS-token för scenarion för överföring av data när /XO och /XN inte har angetts.

**Gäller för:** Blobbar, filer, tabeller

### <a name="l"></a>/L

Anger en åtgärd. Inga data kopieras.

AzCopy tolkar den med hjälp av det här alternativet som en simulering för att köra kommandoraden utan det här alternativet /L och antal hur många objekt har kopierats kan du ange alternativet /V samtidigt för att kontrollera vilka objekt kopieras i den detaljerade loggen.

Beteendet för det här alternativet bestäms också av platsen för datakällan och förekomsten av rekursiva läge alternativet /S och filen alternativet mönster /Pattern.

AzCopy kräver behörighet att lista och läsa den här källplatsen när du använder det här alternativet.

**Gäller för:** Blobbar, filer

### <a name="mt"></a>/MT

Anger den hämtade filen modifierades senast vara densamma som källan blob eller filen.

**Gäller för:** Blobbar, filer

### <a name="xn"></a>/XN

Undantar en nyare käll-resurs. Resursen kopieras inte om den senaste ändringstiden på källan är samma eller senare än målet.

**Gäller för:** Blobbar, filer

### <a name="xo"></a>/XO
Undantar en äldre käll-resurs. Resursen kopieras inte om den senaste ändringstiden på källan är samma eller äldre än målet.

**Gäller för:** Blobbar, filer

### <a name="a"></a>/A

Överför bara filer som har attributet Arkiv.

**Gäller för:** Blobbar, filer

### <a name="iarashcnetoi"></a>/IA:[RASHCNETOI]

Överför bara filer med någon av de angivna attributen.

Tillgängliga attribut inkluderar:

* R = skrivskyddade filer
* A = filer som är klara för arkivering
* S = systemfiler
* H = dolda filer
* C = komprimerade filer
* N = normala filer
* E = krypterade filer
* T = temporära filer
* O = Offline-filer
* Jag = icke-indexerat filer

**Gäller för:** Blobbar, filer

### <a name="xarashcnetoi"></a>/XA:[RASHCNETOI]

Undantar filer med någon av de angivna attributen.

Tillgängliga attribut inkluderar:

* R = skrivskyddade filer
* A = filer som är klara för arkivering
* S = systemfiler
* H = dolda filer
* C = komprimerade filer
* N = normala filer
* E = krypterade filer
* T = temporära filer
* O = Offline-filer
* Jag = icke-indexerat filer

**Gäller för:** Blobbar, filer

### <a name="delimiterdelimiter"></a>/Delimiter:"delimiter"

Anger ett avgränsningstecken som används för att avgränsa virtuella kataloger i ett blob-namn.

Som standard använder AzCopy / som avgränsningstecken. AzCopy stöder dock använda alla vanliga tecken (t ex @, # eller %) som avgränsare. Om du måste inkludera en av dessa specialtecken på kommandoraden, omger du filnamnet med citattecken.

Det här alternativet gäller endast för att ladda ned blobbar.

**Gäller för:** Blobbar

### <a name="ncnumber-of-concurrent-operations"></a>/NC:"number-of-concurrent-operations"

Anger antalet samtidiga åtgärder.

AzCopy som standard startar ett visst antal samtidiga åtgärder att öka genomflödet data transfer. Observera att många samtidiga åtgärder i en miljö med låg bandbredd kan överväldigande nätverksanslutningen och förhindra operations fullständigt slutförs. Begränsning samtidiga åtgärder baserat på faktiska tillgänglig nätverksbandbredd.

Den övre gränsen för samtidiga åtgärder är 512.

**Gäller för:** Blobbar, filer, tabeller

### <a name="sourcetypeblob--table"></a>/ SourceType: ”Blob” | ”Table”

Anger att den `source` resursen är en blob som är tillgängliga i den lokala utvecklingsmiljö som körs i storage-emulatorn.

**Gäller för:** Blobbar, tabeller

### <a name="desttypeblob--table"></a>/ DestType: ”Blob” | ”Table”

Anger att den `destination` resursen är en blob som är tillgängliga i den lokala utvecklingsmiljö som körs i storage-emulatorn.

**Gäller för:** Blobbar, tabeller

### <a name="pkrskey1key2key3"></a>/PKRS:"key1#key2#key3#..."

Delar partitionsnyckelintervallet om du vill aktivera Exportera tabelldata parallellt, vilket ökar hastigheten för exporten.

Om det här alternativet inte anges använder AzCopy en tråd för att exportera tabellentiteter. Om användaren anger /PKRS till exempel: ”aa #bb” och sedan AzCopy startar tre samtidiga åtgärder.

Varje åtgärd exporterar en av tre partition nyckelintervall, enligt nedan:

  [första partitionsnyckel, aa)

  [aa, bb)

  [bb senaste partitionsnyckel]

**Gäller för:** tabeller

### <a name="splitsizefile-size"></a>/ SplitSize: ”filstorlek”

Anger den exporterade filen dela storlek i MB, det minsta tillåtna värdet är 32.

Om det här alternativet inte anges, exporterar AzCopy tabelldata till en enda fil.

Om tabelldata exporteras till en blobb, och den exporterade filen storleken uppnår 200 GB gränsen för blob-storlek, sedan AzCopy delar upp den exporterade filen även om det här alternativet inte har angetts.

**Gäller för:** tabeller

### <a name="entityoperationinsertorskip--insertormerge--insertorreplace"></a>/ EntityOperation: ”InsertOrSkip” | ”InsertOrMerge” | ”InsertOrReplace”

Anger Tabellfunktioner för import av data.

* InsertOrSkip - hoppar över en befintlig entitet eller infogar en ny entitet om den inte finns i tabellen.
* InsertOrMerge - sammanfogar en befintlig entitet eller infogar en ny entitet om den inte finns i tabellen.
* InsertOrReplace - ersätter en befintlig entitet eller infogar en ny entitet om den inte finns i tabellen.

**Gäller för:** tabeller

### <a name="manifestmanifest-file"></a>/ Manifest: ”manifest-fil”

Anger manifestfilen för tabellen exportera och importera igen.

Det här alternativet är valfritt under exportåtgärden, AzCopy genererar en manifestfil med fördefinierade namnet om det här alternativet inte anges.

Det här alternativet krävs under importen för att hitta datafilerna.

**Gäller för:** tabeller

### <a name="synccopy"></a>/SyncCopy

Anger om du vill kopiera synkront blobbar eller filer mellan två Azure Storage-slutpunkter.

AzCopy som standard använder serversidan asynkron kopia. Ange det här alternativet för att utföra en synkron kopia som laddar ned filer eller BLOB till lokalt minne och överför dem till Azure Storage.

Du kan använda det här alternativet när du kopierar filer i Blob storage fillagring, eller från Blob storage till File storage och vice versa.

**Gäller för:** Blobbar, filer

### <a name="setcontenttypecontent-type"></a>/SetContentType:"content-type"

Anger MIME content-type för mål-BLOB eller filer.

AzCopy anger content-type för en blob eller en fil till program/oktett-ström som standard. Du kan ange content-type för alla blobbar eller filer genom att ange ett värde för det här alternativet.

Om du anger det här alternativet utan ett värde som anger AzCopy varje blob eller filens innehållstyp enligt dess filnamnstillägg.

**Gäller för:** Blobbar, filer

### <a name="payloadformatjson--csv"></a>/ PayloadFormat: ”JSON” | ”CSV”

Anger formatet för den exporterade datafilen i tabellen.

Om det här alternativet inte anges, exporterar AzCopy tabell datafilen i JSON-format som standard.

**Gäller för:** tabeller

## <a name="known-issues-and-best-practices"></a>Kända problem och bästa praxis

Låt oss ta en titt på några kända problem och bästa praxis.

### <a name="limit-concurrent-writes-while-copying-data"></a>Begränsa samtidiga skrivningar vid kopiering av data

När du kopierar blobar eller filer med AzCopy, Tänk på att ett annat program kan ändra data när du kopierar den. Kontrollera om det är möjligt att du kopierar data inte ändras under kopieringen. Till exempel när du kopierar en virtuell Hårddisk som är kopplad till en virtuell Azure-dator, kontrollera att inga andra program för tillfället skrivning till den virtuella Hårddisken. Ett bra sätt att göra detta är genom leasing resurs som ska kopieras. Alternativt kan du skapa en ögonblicksbild av den virtuella Hårddisken först och sedan kopiera ögonblicksbilden.

Om du inte hindra andra program från att skriva till filer eller blobbar när de kopieras sedan Kom ihåg att när jobbet har slutförts, kopiera resurserna kan inte längre har fullständig paritet med resurserna som källa.

### <a name="run-one-azcopy-instance-on-one-machine"></a>Köra en AzCopy-instans på en dator.

AzCopy är utformat för att maximera användningen av din datorresurser som påskyndar dataöverföringen rekommenderar vi du kör endast en AzCopy-instans på en dator och ange alternativet `/NC` om du behöver fler samtidiga åtgärder. Mer information skriver du `AzCopy /?:NC` på kommandoraden.

### <a name="enable-fips-compliant-md5-algorithms-for-azcopy-when-you-use-fips-compliant-algorithms-for-encryption-hashing-and-signing"></a>Aktivera MD5 FIPS-kompatibla algoritmer för AzCopy när du ”Använd FIPS-kompatibla algoritmer för kryptering, hashing och signering”.

AzCopy som standard använder .NET MD5-implementering för att beräkna MD5 när du kopierar objekt, men det finns vissa säkerhetskrav som behöver AzCopy för att aktivera inställningen för FIPS-kompatibla MD5.

Du kan skapa en app.config-fil `AzCopy.exe.config` med egenskapen `AzureStorageUseV1MD5` och placera den reservera med AzCopy.exe.

    <?xml version="1.0" encoding="utf-8" ?>
    <configuration>
      <appSettings>
        <add key="AzureStorageUseV1MD5" value="false"/>
      </appSettings>
    </configuration>

För egenskapen ”AzureStorageUseV1MD5”:

* True - använder standardvärdet AzCopy .NET MD5-implementering.
* Falsk – använder AzCopy FIPS-kompatibla MD5-algoritmen.

FIPS-kompatibla algoritmer är inaktiverade som standard i Windows. Du kan ändra den här inställningen på din dator. I fönstret kör (Windows + R) Skriv secpol.msc att öppna den **lokal säkerhetsprincip** fönster. I den **säkerhetsinställningar** och navigera till **säkerhetsinställningar** > **lokala principer** > **säkerhetsalternativ**. Leta upp den **Systemkryptografi: Använd FIPS-kompatibla algoritmer för kryptering, hashing och signering** princip. Dubbelklicka på principen för att se värdet som visas i den **säkerhetsinställningen** kolumn.

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Storage och AzCopy finns i följande resurser:

### <a name="azure-storage-documentation"></a>Azure Storage-dokumentation:
* [Introduktion till Azure Storage](../storage-introduction.md)
* [Använda Blob storage från .NET](../blobs/storage-dotnet-how-to-use-blobs.md)
* [Hur du använder File storage från .NET](../storage-dotnet-how-to-use-files.md)
* [Använda Table storage från .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Skapa, hantera eller ta bort ett lagringskonto](../storage-create-storage-account.md)
* [Överföra data med AzCopy på Linux](storage-use-azcopy-linux.md)

### <a name="azure-storage-blog-posts"></a>Azure Storage blogginlägg:
* [Introduktion till Azure Storage Data Movement Library Preview](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/)
* [AzCopy: Introduktion till synkron kopia och anpassade innehållstyp](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/01/13/azcopy-introducing-synchronous-copy-and-customized-content-type.aspx)
* [AzCopy: Om allmän tillgänglighet AzCopy 3.0 plus förhandsversionen av AzCopy 4.0 med tabell- och support](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/10/29/azcopy-announcing-general-availability-of-azcopy-3-0-plus-preview-release-of-azcopy-4-0-with-table-and-file-support.aspx)
* [AzCopy: Optimerade för storskaliga kopiera scenarier](http://go.microsoft.com/fwlink/?LinkId=507682)
* [AzCopy: Stöd för geo-redundant lagring med läsbehörighet](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/04/07/azcopy-support-for-read-access-geo-redundant-account.aspx)
* [AzCopy: Överföra data med omstartsläge och SAS-token](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/09/07/azcopy-transfer-data-with-re-startable-mode-and-sas-token.aspx)
* [AzCopy: Använder mellan konto kopiera Blob](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/04/01/azcopy-using-cross-account-copy-blob.aspx)
* [AzCopy: Överför/hämta filer för Azure-BLOB](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/12/03/azcopy-uploading-downloading-files-for-windows-azure-blobs.aspx)
