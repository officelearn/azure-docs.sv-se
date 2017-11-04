---
title: "Kopiera eller flytta data till Azure Storage med AzCopy på Linux | Microsoft Docs"
description: "Använd AzCopy på Linux-verktyget för att flytta eller kopiera data till och från blob- och innehåll. Kopiera data till Azure Storage från lokala filer eller kopiera data inom eller mellan lagringskonton. Enkelt migrera dina data till Azure Storage."
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
ms.date: 28/9/2017
ms.author: seguler
ms.openlocfilehash: d77ac39b7fcf6a23ebc58a2bbf9dc7de664edbb3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="transfer-data-with-azcopy-on-linux"></a>Överföra data med AzCopy på Linux
AzCopy på Linux är ett kommandoradsverktyg som utformats för att kopiera data till och från Microsoft Azure-Blob och fillagring med enkla kommandon med optimal prestanda. Du kan kopiera data från ett objekt till en annan inom ditt lagringskonto eller mellan lagringskonton.

Det finns två versioner av AzCopy som du kan hämta. AzCopy på Linux är byggd med .NET Core Framework, som riktar sig till Linux-plattformar som erbjuder POSIX format kommandoradsalternativ. [AzCopy på Windows](../storage-use-azcopy.md) har skapats med .NET Framework och erbjuder kommandoradsalternativ för Windows-formatet. Den här artikeln beskriver AzCopy på Linux.

## <a name="download-and-install-azcopy"></a>Hämta och installera AzCopy
### <a name="installation-on-linux"></a>Installation på Linux

Artikeln innehåller kommandon för olika versioner av Ubuntu.  Använd den `lsb_release -a` kommando för att bekräfta distributionsversion och kodnamnet. 

AzCopy på Linux kräver .NET Core framework (version 1.1.x) på plattformen. Finns i installationsanvisningarna på den [.NET Core](https://www.microsoft.com/net/download/linux) sidan.

Exempelvis kan vi installera .NET Core på Ubuntu 16.04. Senaste installationsguiden finns [.NET Core på Linux](https://www.microsoft.com/net/download/linux) installationssidan.


```bash
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-xenial-prod xenial main" > /etc/apt/sources.list.d/dotnetdev.list'
sudo apt-get update
sudo apt-get install dotnet-dev-1.1.4
```

När du har installerat .NET Core, hämta och installera AzCopy.

```bash
wget -O azcopy.tar.gz https://aka.ms/downloadazcopyprlinux
tar -xf azcopy.tar.gz
sudo ./install.sh
```

Du kan ta bort de extraherade filerna när AzCopy på Linux har installerats. Du kan också om du inte har superanvändare, kan du också köra AzCopy med kommandoskript 'azcopy' i den extraherade mappen. 


## <a name="writing-your-first-azcopy-command"></a>Skriva ditt första AzCopy-kommandot
Den grundläggande syntaxen för AzCopy kommandon är:

```azcopy
azcopy --source <source> --destination <destination> [Options]
```

Följande exempel visar olika scenarier för att kopiera data till och från Microsoft Azure-BLOB och filer. Referera till den `azcopy --help` menyn för en detaljerad förklaring av de parametrar som används i varje prov.

## <a name="blob-download"></a>BLOB: ladda ned
### <a name="download-single-blob"></a>Hämta en enda blob

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --include "abc.txt"
```

Om mappen `/mnt/myfiles` finns inte, AzCopy skapar den och hämtar `abc.txt ` till den nya mappen.

### <a name="download-single-blob-from-secondary-region"></a>Hämta en enda blob från sekundär region

```azcopy
azcopy \
    --source https://myaccount-secondary.blob.core.windows.net/mynewcontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --include "abc.txt"
```

Observera att du måste ha läsbehörighet geo-redundant lagring aktiverad.

### <a name="download-all-blobs"></a>Hämta alla BLOB

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --recursive
```

Anta följande blobbar finns i den angivna behållaren:  

```
abc.txt
abc1.txt
abc2.txt
vd1/a.txt
vd1/abcd.txt
```

När hämtningen katalogen `/mnt/myfiles` innehåller följande filer:

```
/mnt/myfiles/abc.txt
/mnt/myfiles/abc1.txt
/mnt/myfiles/abc2.txt
/mnt/myfiles/vd1/a.txt
/mnt/myfiles/vd1/abcd.txt
```

Om du inte anger alternativet `--recursive`, ingen blob som ska hämtas.

### <a name="download-blobs-with-specified-prefix"></a>Ladda ned blobbar med angivna prefix

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --include "a" \
    --recursive
```

Anta att följande blobbar finns i den angivna behållaren. Alla blobbar som börjar med prefixet `a` hämtas.

```
abc.txt
abc1.txt
abc2.txt
xyz.txt
vd1\a.txt
vd1\abcd.txt
```

När hämtningen mappen `/mnt/myfiles` innehåller följande filer:

```
/mnt/myfiles/abc.txt
/mnt/myfiles/abc1.txt
/mnt/myfiles/abc2.txt
```

Prefixet som gäller för den virtuella katalogen, som utgör den första delen av blobbnamnet. I exemplet ovan, matchar den virtuella katalogen inte det angivna prefixet, så inga blob hämtas. Dessutom, om alternativet `--recursive` anges AzCopy inte hämta alla blobbar.

### <a name="set-the-last-modified-time-of-exported-files-to-be-same-as-the-source-blobs"></a>Ange last-modified-tid med exporterade filer som ska vara densamma som källan BLOB

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination "/mnt/myfiles" \
    --source-key <key> \
    --preserve-last-modified-time
```

Du kan också utesluta blobbar från hämtningen baserat på deras tid för senaste ändring. Till exempel om du vill undanta blobbar vars senast ändrad är samma som eller nyare än målfilen och lägga till den `--exclude-newer` alternativ:

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --preserve-last-modified-time \
    --exclude-newer
```

Eller om du vill undanta blobbar vars senast ändrad är samma eller äldre än målfilen och lägga till den `--exclude-older` alternativ:

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --preserve-last-modified-time \
    --exclude-older
```

## <a name="blob-upload"></a>BLOB: Överför
### <a name="upload-single-file"></a>Överför en fil

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --include "abc.txt"
```

Om den angivna Målbehållaren inte finns, skapar den AzCopy och överför filen till den.

### <a name="upload-single-file-to-virtual-directory"></a>Överför en fil till virtuell katalog

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --include "abc.txt"
```

Om den angivna virtuella katalogen inte finns, AzCopy överför filen så att den virtuella katalogen i blob-namnet (*t.ex.*, `vd/abc.txt` i exemplet ovan).

### <a name="upload-all-files"></a>Ladda upp alla filer

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --recursive
```

Om du anger alternativet `--recursive` Överför innehållet i den angivna katalogen till Blob storage rekursivt, vilket innebär att alla undermappar och filer överförs också. Anta exempelvis att följande filer finns i mappen `/mnt/myfiles`:

```
/mnt/myfiles/abc.txt
/mnt/myfiles/abc1.txt
/mnt/myfiles/abc2.txt
/mnt/myfiles/subfolder/a.txt
/mnt/myfiles/subfolder/abcd.txt
```

När överföringen innehåller behållaren följande filer:

```
abc.txt
abc1.txt
abc2.txt
subfolder/a.txt
subfolder/abcd.txt
```

När alternativet `--recursive` anges endast följande tre filer överförs:

```
abc.txt
abc1.txt
abc2.txt
```

### <a name="upload-files-matching-specified-pattern"></a>Överföra filer som matchar angivna mönstret

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --include "a*" \
    --recursive
```

Anta att följande filer finns i mappen `/mnt/myfiles`:

```
/mnt/myfiles/abc.txt
/mnt/myfiles/abc1.txt
/mnt/myfiles/abc2.txt
/mnt/myfiles/xyz.txt
/mnt/myfiles/subfolder/a.txt
/mnt/myfiles/subfolder/abcd.txt
```

När överföringen innehåller behållaren följande filer:

```
abc.txt
abc1.txt
abc2.txt
subfolder/a.txt
subfolder/abcd.txt
```

När alternativet `--recursive` anges AzCopy hoppar över filer som finns i underkataloger:

```
abc.txt
abc1.txt
abc2.txt
```

### <a name="specify-the-mime-content-type-of-a-destination-blob"></a>Ange MIME content-type för en mål-blob
Som standard anges AzCopy innehållstypen för en mål-blob till `application/octet-stream`. Du kan dock uttryckligen ange content-type via alternativet `--set-content-type [content-type]`. Den här syntaxen anger content-type för alla blobbar i en överföringen.

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/myContainer/ \
    --dest-key <key> \
    --include "ab" \
    --set-content-type "video/mp4"
```

Om alternativet `--set-content-type` anges utan värde, och sedan AzCopy anger varje blob eller filens innehållstyp enligt dess filnamnstillägg.

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/myContainer/ \
    --dest-key <key> \
    --include "ab" \
    --set-content-type
```

## <a name="blob-copy"></a>BLOB: kopiera
### <a name="copy-single-blob-within-storage-account"></a>Kopiera enda blob inom lagringskonto

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer1 \
    --destination https://myaccount.blob.core.windows.net/mycontainer2 \
    --source-key <key> \
    --dest-key <key> \
    --include "abc.txt"
```

När du kopierar en blobb utan--synkroniserad kopia alternativet en [serversidan kopiera](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) åtgärden utförs.

### <a name="copy-single-blob-across-storage-accounts"></a>Kopiera enda blob på Storage-konton

```azcopy
azcopy \
    --source https://sourceaccount.blob.core.windows.net/mycontainer1 \
    --destination https://destaccount.blob.core.windows.net/mycontainer2 \
    --source-key <key1> \
    --dest-key <key2> \
    --include "abc.txt"
```

När du kopierar en blobb utan--synkroniserad kopia alternativet en [serversidan kopiera](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) åtgärden utförs.

### <a name="copy-single-blob-from-secondary-region-to-primary-region"></a>Kopiera enda blob från sekundär region till primär region

```azcopy
azcopy \
    --source https://myaccount1-secondary.blob.core.windows.net/mynewcontainer1 \
    --destination https://myaccount2.blob.core.windows.net/mynewcontainer2 \
    --source-key <key1> \
    --dest-key <key2> \
    --include "abc.txt"
```

Observera att du måste ha läsbehörighet geo-redundant lagring aktiverad.

### <a name="copy-single-blob-and-its-snapshots-across-storage-accounts"></a>Kopiera enda blob och dess ögonblicksbilder på Storage-konton

```azcopy
azcopy \
    --source https://sourceaccount.blob.core.windows.net/mycontainer1 \
    --destination https://destaccount.blob.core.windows.net/mycontainer2 \
    --source-key <key1> \
    --dest-key <key2> \
    --include "abc.txt" \
    --include-snapshot
```

Efter kopieringen innehåller Målbehållaren blob och dess ögonblicksbilder. Behållaren innehåller följande blob och dess ögonblicksbilder:

```
abc.txt
abc (2013-02-25 080757).txt
abc (2014-02-21 150331).txt
```

### <a name="synchronously-copy-blobs-across-storage-accounts"></a>Kopiera synkront blobar på lagringskonton
AzCopy som standard kopierar data mellan två slutpunkter för lagring asynkront. Kopiera åtgärden körs i bakgrunden med hjälp av ledig bandbreddskapacitet som har inga SLA vad gäller hur snabbt en blob kopieras. 

Den `--sync-copy` alternativet ser du till att kopieringen hämtar konsekvent hastighet. AzCopy utför synkron kopian genom att hämta blobbarna att kopiera från den angivna källan till lokalt minne och överför dem till Blob-lagringsplats.

```azcopy
azcopy \
    --source https://myaccount1.blob.core.windows.net/myContainer/ \
    --destination https://myaccount2.blob.core.windows.net/myContainer/ \
    --source-key <key1> \
    --dest-key <key2> \
    --include "ab" \
    --sync-copy
```

`--sync-copy`kan skapa ytterligare utgång kostnaden jämfört med asynkron kopia. Den rekommenderade metoden är att använda det här alternativet i en Azure VM, som finns i samma region som ditt källa storage-konto för att undvika kostnader för utgående trafik.

## <a name="file-download"></a>Fil: ladda ned
### <a name="download-single-file"></a>Hämta en fil

```azcopy
azcopy \
    --source https://myaccount.file.core.windows.net/myfileshare/myfolder1/ \
    --destination /mnt/myfiles \
    --source-key <key> \
    --include "abc.txt"
```

Om den angivna källan är en Azure-filresurs och sedan måste du antingen ange det exakta filnamnet (*t.ex.* `abc.txt`) att hämta en fil eller ange alternativet `--recursive` att ladda ned alla filer i resursen rekursivt. Försök att ange både filmönstret och alternativet `--recursive` tillsammans resulterar i ett fel.

### <a name="download-all-files"></a>Hämta alla filer

```azcopy
azcopy \
    --source https://myaccount.file.core.windows.net/myfileshare/ \
    --destination /mnt/myfiles \
    --source-key <key> \
    --recursive
```

Observera att alla tomma mappar inte laddas ned.

## <a name="file-upload"></a>Fil: Överför
### <a name="upload-single-file"></a>Överför en fil

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.file.core.windows.net/myfileshare/ \
    --dest-key <key> \
    --include abc.txt
```

### <a name="upload-all-files"></a>Ladda upp alla filer

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.file.core.windows.net/myfileshare/ \
    --dest-key <key> \
    --recursive
```

Observera att alla tomma mappar inte upp.

### <a name="upload-files-matching-specified-pattern"></a>Överföra filer som matchar angivna mönstret

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.file.core.windows.net/myfileshare/ \
    --dest-key <key> \
    --include "ab*" \
    --recursive
```

## <a name="file-copy"></a>Fil: kopiera
### <a name="copy-across-file-shares"></a>Kopiera på filresurser

```azcopy
azcopy \
    --source https://myaccount1.file.core.windows.net/myfileshare1/ \
    --destination https://myaccount2.file.core.windows.net/myfileshare2/ \
    --source-key <key1> \
    --dest-key <key2> \
    --recursive
```
När du kopierar en fil på filresurser, en [serversidan kopiera](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) åtgärden utförs.

### <a name="copy-from-file-share-to-blob"></a>Kopiera från filresursen till blob

```azcopy
azcopy \ 
    --source https://myaccount1.file.core.windows.net/myfileshare/ \
    --destination https://myaccount2.blob.core.windows.net/mycontainer/ \
    --source-key <key1> \
    --dest-key <key2> \
    --recursive
```
När du kopierar en fil från filresursen till blob, en [serversidan kopiera](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) åtgärden utförs.

### <a name="copy-from-blob-to-file-share"></a>Kopiera från blob till filresurs

```azcopy
azcopy \
    --source https://myaccount1.blob.core.windows.net/mycontainer/ \
    --destination https://myaccount2.file.core.windows.net/myfileshare/ \
    --source-key <key1> \
    --dest-key <key2> \
    --recursive
```
När du kopierar en fil från blob till filresurs, en [serversidan kopiera](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) åtgärden utförs.

### <a name="synchronously-copy-files"></a>Synkront kopiera filer
Du kan ange den `--sync-copy` alternativet för att kopiera data från lagring av filer till File Storage, från fillagring till Blob Storage och från Blob Storage till File Storage synkront. AzCopy körs åtgärden genom att hämta källdata till lokalt minne och överföra den till målet. I det här fallet gäller standard utgång kostnaden.

```azcopy
azcopy \
    --source https://myaccount1.file.core.windows.net/myfileshare1/ \
    --destination https://myaccount2.file.core.windows.net/myfileshare2/ \
    --source-key <key1> \
    --dest-key <key2> \
    --recursive \
    --sync-copy
```

När du kopierar från fillagring till Blob Storage blob standardtypen är blockblob, kan användaren ange alternativet `--blob-type page` att ändra typen av mål-blob. Tillgängliga typer är `page | block | append`.

Observera att `--sync-copy` kan skapa ytterligare utgång kostnad jämföra den asynkrona kopia. Den rekommenderade metoden är att använda det här alternativet i en Azure VM, som finns i samma region som ditt källa storage-konto för att undvika kostnader för utgående trafik.

## <a name="other-azcopy-features"></a>Andra AzCopy-funktioner
### <a name="only-copy-data-that-doesnt-exist-in-the-destination"></a>Endast kopiera data som inte finns i målet
Den `--exclude-older` och `--exclude-newer` parametrar kan du exkludera resurser som tidigare eller senare från att kopieras respektive. Om du bara vill kopiera käll-resurser som inte finns i målet kan du ange båda parametrarna i AzCopy-kommandot:

    --source http://myaccount.blob.core.windows.net/mycontainer --destination /mnt/myfiles --source-key <sourcekey> --recursive --exclude-older --exclude-newer

    --source /mnt/myfiles --destination http://myaccount.file.core.windows.net/myfileshare --dest-key <destkey> --recursive --exclude-older --exclude-newer

    --source http://myaccount.blob.core.windows.net/mycontainer --destination http://myaccount.blob.core.windows.net/mycontainer1 --source-key <sourcekey> --dest-key <destkey> --recursive --exclude-older --exclude-newer

### <a name="use-a-configuration-file-to-specify-command-line-parameters"></a>Använd en konfigurationsfil för att ange kommandoradsparametrar

```azcopy
azcopy --config-file "azcopy-config.ini"
```

Du kan inkludera eventuella kommandoradsparametrar för AzCopy i en konfigurationsfil. AzCopy bearbetar parametrarna i filen som om de hade angetts på kommandoraden, utför en direkt ersättning med innehållet i filen.

Anta en fil med namnet `copyoperation`, som innehåller följande rader. Varje AzCopy-parameter kan anges på en enda rad.

    --source http://myaccount.blob.core.windows.net/mycontainer --destination /mnt/myfiles --source-key <sourcekey> --recursive --quiet

eller på separata rader:

    --source http://myaccount.blob.core.windows.net/mycontainer
    --destination /mnt/myfiles
    --source-key<sourcekey>
    --recursive
    --quiet

AzCopy misslyckas om du vill dela parametern på två rader som visas här för den `--source-key` parameter:

    http://myaccount.blob.core.windows.net/mycontainer
    /mnt/myfiles
    --sourcekey
    <sourcekey>
    --recursive
    --quiet

### <a name="specify-a-shared-access-signature-sas"></a>Ange en signatur för delad åtkomst (SAS)

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer1 \
    --destination https://myaccount.blob.core.windows.net/mycontainer2 \
    --source-sas <SAS1> \
    --dest-sas <SAS2> \
    --include abc.txt
```

Du kan också ange en SAS för URI-behållaren:

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer1/?SourceSASToken \
    --destination /mnt/myfiles \
    --recursive
```

Observera att AzCopy stöder för närvarande endast den [kontots SAS](https://docs.microsoft.com/en-us/azure/storage/storage-dotnet-shared-access-signature-part-1).

### <a name="journal-file-folder"></a>Ändringsjournalen mapp
Varje gång du utfärda ett kommando till AzCopy, kontrollerar den om en journal-fil finns i standardmappen eller om den finns i en mapp som du har angett via det här alternativet. Om journalfilen inte finns på någon plats, behandlar åtgärden som nya AzCopy och genererar en ny journalfil.

Om journalfilen finns kontrollerar AzCopy om den kommandorad som du matar in matchar kommandoraden i journal-fil. Om två kommandorader matchar återupptar AzCopy ofullständiga igen. Om de inte matchar uppmanas AzCopy användaren att antingen skriva över journalfilen att starta en ny åtgärd eller Avbryt den aktuella åtgärden.

Om du vill använda standardplatsen för journalfilen:

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --resume
```

Om du utelämnar alternativet `--resume`, eller ange alternativet `--resume` utan sökvägen till mappen som du ser ovan, AzCopy journalfilen skapas i standardplatsen, som är `~\Microsoft\Azure\AzCopy`. Om ändringsjournalen filen redan finns återupptas AzCopy igen baserat på journal-fil.

Om du vill ange en anpassad plats för journalfilen:

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key key \
    --resume "/mnt/myjournal"
```

Det här exemplet skapar journal-fil om den inte redan finns. Om den finns, återupptar AzCopy igen baserat på journal-fil.

Upprepa samma kommando om du vill fortsätta AzCopy. AzCopy på Linux sedan uppmanas att bekräfta:

```azcopy
Incomplete operation with same command line detected at the journal directory "/home/myaccount/Microsoft/Azure/AzCopy", do you want to resume the operation? Choose Yes to resume, choose No to overwrite the journal to start a new operation. (Yes/No)
```

### <a name="output-verbose-logs"></a>Detaljerade loggarna för utdata

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --verbose
```

### <a name="specify-the-number-of-concurrent-operations-to-start"></a>Ange antalet samtidiga åtgärder att starta
Alternativet `--parallel-level` anger antalet samtidiga kopieringsåtgärd. Som standard startar AzCopy antalet samtidiga åtgärder att öka genomflödet data transfer. Antalet samtidiga åtgärder är lika åtta gånger antalet processorer som du har. Om du använder AzCopy över ett nätverk med låg bandbredd, kan du ange ett lägre värde--parallell-nivå för att undvika misslyckades på grund av konkurrens om resurser.

[!TIP]
>Om du vill visa en fullständig lista över AzCopy parametrar kolla 'azcopy--Hjälp-menyn.

## <a name="known-issues-and-best-practices"></a>Kända problem och bästa praxis
### <a name="error-net-core-is-not-found-in-the-system"></a>Fel: .NET Core finns inte i systemet.
Om du får ett felmeddelande om att .NET Core inte är installerat i systemet, SÖKVÄGEN till .NET Core binär `dotnet` kanske saknas.

Hitta .NET Core binära i systemet för att lösa problemet:
```bash
sudo find / -name dotnet
```

Detta returnerar sökvägen till dotnet binär. 

    /opt/rh/rh-dotnetcore11/root/usr/bin/dotnet
    /opt/rh/rh-dotnetcore11/root/usr/lib64/dotnetcore/dotnet
    /opt/rh/rh-dotnetcore11/root/usr/lib64/dotnetcore/shared/Microsoft.NETCore.App/1.1.2/dotnet

Lägg nu till denna sökväg i PATH-variabeln. För sudo, redigera secure_path innehåller sökvägen till den binära dotnet:
```bash 
sudo visudo
### Append the path found in the preceding example to 'secure_path' variable
```

I det här exemplet läser secure_path variabeln som:

```
secure_path = /sbin:/bin:/usr/sbin:/usr/bin:/opt/rh/rh-dotnetcore11/root/usr/bin/
```

Redigera.bash_profile/.profile om du vill ta med sökvägen till dotnet binära i PATH-variabeln för den aktuella användaren 
```bash
vi ~/.bash_profile
### Append the path found in the preceding example to 'PATH' variable
```

Kontrollera att .NET Core finns i SÖKVÄGEN:
```bash
which dotnet
sudo which dotnet
```

### <a name="error-installing-azcopy"></a>Fel vid installation av AzCopy
Om du får problem med installation av AzCopy kan du försöker köra AzCopy använda bash-skript på den extraherade `azcopy` mapp.

```bash
cd azcopy
./azcopy
```

### <a name="limit-concurrent-writes-while-copying-data"></a>Begränsa samtidiga skrivningar vid kopiering av data
När du kopierar blobar eller filer med AzCopy, Tänk på att ett annat program kan ändra data när du kopierar den. Kontrollera om det är möjligt att du kopierar data inte ändras under kopieringen. Till exempel när du kopierar en virtuell Hårddisk som är kopplad till en virtuell Azure-dator, kontrollera att inga andra program för tillfället skrivning till den virtuella Hårddisken. Ett bra sätt att göra detta är genom leasing resurs som ska kopieras. Alternativt kan du skapa en ögonblicksbild av den virtuella Hårddisken först och sedan kopiera ögonblicksbilden.

Om du inte hindra andra program från att skriva till filer eller blobbar när de kopieras sedan Kom ihåg att när jobbet har slutförts, kopiera resurserna kan inte längre har fullständig paritet med resurserna som källa.

### <a name="run-one-azcopy-instance-on-one-machine"></a>Köra en AzCopy-instans på en dator.
AzCopy är utformat för att maximera användningen av din datorresurser som påskyndar dataöverföringen rekommenderar vi du kör endast en AzCopy-instans på en dator och ange alternativet `--parallel-level` om du behöver fler samtidiga åtgärder. Mer information skriver du `AzCopy --help parallel-level` på kommandoraden.

## <a name="next-steps"></a>Nästa steg
Mer information om Azure Storage och AzCopy finns i följande resurser:

### <a name="azure-storage-documentation"></a>Azure Storage-dokumentation:
* [Introduktion till Azure Storage](../storage-introduction.md)
* [Skapa ett lagringskonto](../storage-create-storage-account.md)
* [Hantera blobbar med Lagringsutforskaren](https://docs.microsoft.com/en-us/azure/vs-azure-tools-storage-explorer-blobs)
* [Med hjälp av Azure CLI 2.0 med Azure Storage](../storage-azure-cli.md)
* [Hur du använder Blob storage från C++](../blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [Använda Blob Storage från Java](../blobs/storage-java-how-to-use-blob-storage.md)
* [Använda Blob Storage från Node.js](../blobs/storage-nodejs-how-to-use-blob-storage.md)
* [Använda Blob Storage från Python](../blobs/storage-python-how-to-use-blob-storage.md)

### <a name="azure-storage-blog-posts"></a>Azure Storage blogginlägg:
* [Om AzCopy på Linux-Preview](https://azure.microsoft.com/en-in/blog/announcing-azcopy-on-linux-preview/)
* [Introduktion till Azure Storage Data Movement Library Preview](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/)
* [AzCopy: Introduktion till synkron kopia och anpassade innehållstyp](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/01/13/azcopy-introducing-synchronous-copy-and-customized-content-type.aspx)
* [AzCopy: Om allmän tillgänglighet AzCopy 3.0 plus förhandsversionen av AzCopy 4.0 med tabell- och support](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/10/29/azcopy-announcing-general-availability-of-azcopy-3-0-plus-preview-release-of-azcopy-4-0-with-table-and-file-support.aspx)
* [AzCopy: Optimerade för storskaliga kopiera scenarier](http://go.microsoft.com/fwlink/?LinkId=507682)
* [AzCopy: Stöd för geo-redundant lagring med läsbehörighet](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/04/07/azcopy-support-for-read-access-geo-redundant-account.aspx)
* [AzCopy: Överföra data med omstartsläge och SAS-token](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/09/07/azcopy-transfer-data-with-re-startable-mode-and-sas-token.aspx)
* [AzCopy: Använder mellan konto kopiera Blob](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/04/01/azcopy-using-cross-account-copy-blob.aspx)
* [AzCopy: Överför/hämta filer för Azure-BLOB](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/12/03/azcopy-uploading-downloading-files-for-windows-azure-blobs.aspx)

