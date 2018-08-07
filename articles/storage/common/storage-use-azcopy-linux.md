---
title: Kopiera eller flytta data till Azure Storage med AzCopy i Linux | Microsoft Docs
description: Använda AzCopy på Linux-verktyget för att flytta eller kopiera data till och från blob- och innehåll. Kopiera data till Azure Storage från lokala filer eller kopiera data inom eller mellan lagringskonton. Migrera enkelt dina data till Azure Storage.
services: storage
author: seguler
ms.service: storage
ms.topic: article
ms.date: 04/26/2018
ms.author: seguler
ms.component: common
ms.openlocfilehash: 2a09f302be83f39f918d83c45309d5cab79f9429
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2018
ms.locfileid: "39526937"
---
# <a name="transfer-data-with-azcopy-on-linux"></a>Överföra data med AzCopy i Linux

AzCopy är ett kommandoradsverktyg som utformats för att kopiera data till och från Microsoft Azure Blob- och storage med hjälp av enkla kommandon som är utformad för bästa prestanda. Du kan kopiera data mellan ett filsystem och ett lagringskonto, eller mellan lagringskonton.  

Det finns två versioner av AzCopy som du kan hämta. AzCopy i Linux riktar sig till Linux-plattformar och erbjuder POSIX style kommandoradsalternativ. [AzCopy i Windows](../storage-use-azcopy.md) erbjuder kommandoradsalternativ för Windows-format. Den här artikeln beskriver AzCopy i Linux. 

> [!NOTE]  
> Från och med versionen av AzCopy 7.2, paketeras .NET Core-beroenden med AzCopy-paketet. Om du använder 7,2 versionen eller senare, behöver du inte längre installera .NET Core som ett krav.

## <a name="download-and-install-azcopy"></a>Ladda ned och installera AzCopy

### <a name="installation-on-linux"></a>Installation på Linux

> [!NOTE]
> Du kan behöva installera .NET Core 2.1 beroenden markerat i den här [förutsättningar för .NET Core artikeln](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x) beroende på din distribution. 
>
> Installera ICU och libunwind beroenden för RHEL 7 distributioner: ```yum install -y libunwind icu```

Installera AzCopy i Linux (v7.2 eller senare) är lika enkelt som extraherar tar-paketet och kör installationsskriptet. 

**RHEL 6 baserade distributioner**: [nedladdningslänk](https://aka.ms/downloadazcopylinuxrhel6)
```bash
wget -O azcopy.tar.gz https://aka.ms/downloadazcopylinuxrhel6
tar -xf azcopy.tar.gz
sudo ./install.sh
```

**Alla andra Linux-distributioner**: [nedladdningslänk](https://aka.ms/downloadazcopylinux64)
```bash
wget -O azcopy.tar.gz https://aka.ms/downloadazcopylinux64
tar -xf azcopy.tar.gz
sudo ./install.sh
```

Du kan ta bort de extrahera filerna när AzCopy i Linux har installerats. Du kan också om du inte har behörigheter för superanvändare du kan också köra `azcopy` med hjälp av azcopy för shell-skript i den extrahera mappen.

### <a name="alternative-installation-on-ubuntu"></a>Alternativ Installation på Ubuntu

**Ubuntu 14.04**

Lägg till apt-källa för Microsofts Linux produkten lagringsplats och installera AzCopy:

```bash
sudo echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-trusty-prod/ trusty main" > azure.list
sudo cp ./azure.list /etc/apt/sources.list.d/
apt-key adv --keyserver packages.microsoft.com --recv-keys B02C46DF417A0893
```

```bash
sudo apt-get update
sudo apt-get install azcopy
```

**Ubuntu 16.04**

Lägg till apt-källa för Microsofts Linux produkten lagringsplats och installera AzCopy:

```bash
echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-xenial-prod/ xenial main" > azure.list
sudo cp ./azure.list /etc/apt/sources.list.d/
apt-key adv --keyserver packages.microsoft.com --recv-keys B02C46DF417A0893
```

```bash
sudo apt-get update
sudo apt-get install azcopy
```

## <a name="writing-your-first-azcopy-command"></a>Skriva din första AzCopy-kommandot
Grundläggande syntax för AzCopy-kommandon är:

```azcopy
azcopy --source <source> --destination <destination> [Options]
```

Följande exempel visar olika scenarier för att kopiera data till och från Microsoft Azure-Blobbar och filer. Referera till den `azcopy --help` menyn för en detaljerad förklaring av de parametrar som används i varje exempel.

## <a name="blob-download"></a>BLOB: ladda ned
### <a name="download-single-blob"></a>Hämta en enda blob

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer/abc.txt \
    --destination /mnt/myfiles/abc.txt \
    --source-key <key> 
```

Om mappen `/mnt/myfiles` finns inte, AzCopy skapas den och laddar ned `abc.txt ` till den nya mappen. 

### <a name="download-single-blob-from-secondary-region"></a>Ladda ned en blob från sekundär region

```azcopy
azcopy \
    --source https://myaccount-secondary.blob.core.windows.net/mynewcontainer/abc.txt \
    --destination /mnt/myfiles/abc.txt \
    --source-key <key>
```

Observera att du måste ha läsåtkomst till geografiskt redundant lagring aktiveras.

### <a name="download-all-blobs"></a>Ladda ned alla blobar

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --recursive
```

Anta för följande blobar finns i den angivna behållaren:  

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

Om du inte anger alternativet `--recursive`, ingen blob laddas ned.

### <a name="download-blobs-with-specified-prefix"></a>Ladda ned blobar med angivet prefix

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --include "a" \
    --recursive
```

Anta för följande blobar finns i den angivna behållaren. Alla blobar som börjar med prefixet `a` laddas ned.

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

Prefixet gäller för den virtuella katalogen, som utgör den första delen av blobbnamnet. I exemplet som visas ovan, matchar den virtuella katalogen inte det angivna prefixet, så inga blob hämtas. Dessutom, om alternativet `--recursive` har angetts, AzCopy inte ladda ned alla blobbar.

### <a name="set-the-last-modified-time-of-exported-files-to-be-same-as-the-source-blobs"></a>Ange tid för senaste ändring av exporterade filerna vara samma som käll-blobar

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination "/mnt/myfiles" \
    --source-key <key> \
    --preserve-last-modified-time
```

Du kan också utesluta blobar från hämtningen utifrån deras tid för senaste ändring. Till exempel om du vill undanta blobar vars senast ändrad är samma eller nyare än målfilen och lägga till den `--exclude-newer` alternativet:

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --preserve-last-modified-time \
    --exclude-newer
```

Oavsett om du vill undanta blobar vars senast ändrad är samma eller äldre än målfilen och lägga till den `--exclude-older` alternativet:

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --preserve-last-modified-time \
    --exclude-older
```

## <a name="blob-upload"></a>BLOB: ladda upp
### <a name="upload-single-file"></a>Ladda upp en fil

```azcopy
azcopy \
    --source /mnt/myfiles/abc.txt \
    --destination https://myaccount.blob.core.windows.net/mycontainer/abc.txt \
    --dest-key <key>
```

Om den angivna målcontainern inte finns, så skapar AzCopy den och överför filen till den.

### <a name="upload-single-file-to-virtual-directory"></a>Ladda upp en fil till virtuell katalog

```azcopy
azcopy \
    --source /mnt/myfiles/abc.txt \
    --destination https://myaccount.blob.core.windows.net/mycontainer/vd/abc.txt \
    --dest-key <key>
```

Om den angivna virtuella katalogen inte finns, AzCopy Överför fil för att inkludera den virtuella katalogen i blobnamnet (*t.ex.*, `vd/abc.txt` i exemplet ovan).

### <a name="redirect-from-stdin"></a>Omdirigera från stdin

```azcopy
gzip myarchive.tar -c | azcopy \
    --destination https://myaccount.blob.core.windows.net/mycontainer/mydir/myarchive.tar.gz \
    --dest-key <key>
```

### <a name="upload-all-files"></a>Överför alla filer

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --recursive
```

Om du anger alternativet `--recursive` Överför innehållet i den angivna katalogen till Blob storage rekursivt, vilket innebär att alla undermappar och filer överförs även. Anta exempelvis att följande filer finns i mappen `/mnt/myfiles`:

```
/mnt/myfiles/abc.txt
/mnt/myfiles/abc1.txt
/mnt/myfiles/abc2.txt
/mnt/myfiles/subfolder/a.txt
/mnt/myfiles/subfolder/abcd.txt
```

Efter överföringen innehåller följande filer i behållaren:

```
abc.txt
abc1.txt
abc2.txt
subfolder/a.txt
subfolder/abcd.txt
```

När alternativet `--recursive` inte anges endast följande tre filer har laddats upp:

```
abc.txt
abc1.txt
abc2.txt
```

### <a name="upload-files-matching-specified-pattern"></a>Ladda upp filer som matchar angivna mönstret

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

Efter överföringen innehåller följande filer i behållaren:

```
abc.txt
abc1.txt
abc2.txt
subfolder/a.txt
subfolder/abcd.txt
```

När alternativet `--recursive` inte anges AzCopy hoppar över filer som finns i undermappar:

```
abc.txt
abc1.txt
abc2.txt
```

### <a name="specify-the-mime-content-type-of-a-destination-blob"></a>Ange MIME-innehållstyp en mål-blob
Som standard AzCopy anger innehållstypen för en mål-blob till `application/octet-stream`. Men du kan uttryckligen ange innehållstyp via alternativet `--set-content-type [content-type]`. Den här syntaxen anger innehållstypen för alla blobbar i en överföringen.

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/myContainer/ \
    --dest-key <key> \
    --include "ab" \
    --set-content-type "video/mp4"
```

Om alternativet `--set-content-type` har angetts utan ett värde, AzCopy anger varje blob eller filens innehållstyp enligt dess filnamnstillägg.

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/myContainer/ \
    --dest-key <key> \
    --include "ab" \
    --set-content-type
```

### <a name="customizing-the-mime-content-type-mapping"></a>Anpassa MIME-innehållstyp mappning
AzCopy använder en konfigurationsfil som innehåller en mappning av filnamnstillägg som innehållstyp. Du kan anpassa den här mappningen och lägga till nya par efter behov. Mappningen finns i  ```/usr/lib/azcopy/AzCopyConfig.json```

## <a name="blob-copy"></a>BLOB: kopiera
### <a name="copy-single-blob-within-storage-account"></a>Enkel kopiering av blob Storage-konto

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer1/abc.txt \
    --destination https://myaccount.blob.core.windows.net/mycontainer2/abc.txt \
    --source-key <key> \
    --dest-key <key>
```

När du kopierar en blob utan--synkroniserad kopia alternativet en [serversidan kopia](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) åtgärden utförs.

### <a name="copy-single-blob-across-storage-accounts"></a>Enkel kopiering av blob Storage-konton

```azcopy
azcopy \
    --source https://sourceaccount.blob.core.windows.net/mycontainer1/abc.txt \
    --destination https://destaccount.blob.core.windows.net/mycontainer2/abc.txt \
    --source-key <key1> \
    --dest-key <key2>
```

När du kopierar en blob utan--synkroniserad kopia alternativet en [serversidan kopia](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) åtgärden utförs.

### <a name="copy-single-blob-from-secondary-region-to-primary-region"></a>Kopiera enda blob från sekundär region till primär region

```azcopy
azcopy \
    --source https://myaccount1-secondary.blob.core.windows.net/mynewcontainer1/abc.txt \
    --destination https://myaccount2.blob.core.windows.net/mynewcontainer2/abc.txt \
    --source-key <key1> \
    --dest-key <key2>
```

Observera att du måste ha läsåtkomst till geografiskt redundant lagring aktiveras.

### <a name="copy-single-blob-and-its-snapshots-across-storage-accounts"></a>Kopiera enda blob och dess ögonblicksbilder på Storage-konton

```azcopy
azcopy \
    --source https://sourceaccount.blob.core.windows.net/mycontainer1/ \
    --destination https://destaccount.blob.core.windows.net/mycontainer2/ \
    --source-key <key1> \
    --dest-key <key2> \
    --include "abc.txt" \
    --include-snapshot
```

Efter kopieringen innehåller en målbehållare blob och dess ögonblicksbilder. Behållaren innehåller följande blob och dess ögonblicksbilder:

```
abc.txt
abc (2013-02-25 080757).txt
abc (2014-02-21 150331).txt
```

### <a name="synchronously-copy-blobs-across-storage-accounts"></a>Synkront kopiera blobar över lagringskonton
AzCopy som standard kopierar data mellan två storage slutpunkter asynkront. Därför kopieras kopia åtgärden körs i bakgrunden med hjälp av ledig bandbredd kapaciteten som har inget serviceavtal när det gäller hur snabbt en blob. 

Den `--sync-copy` alternativet ser du till att kopieringen hämtar konsekvent hastighet. AzCopy utför synkrona kopian genom att hämta BLOB att kopiera från den angivna källan till lokalt minne och överför dem till Blob storage-mål.

```azcopy
azcopy \
    --source https://myaccount1.blob.core.windows.net/myContainer/ \
    --destination https://myaccount2.blob.core.windows.net/myContainer/ \
    --source-key <key1> \
    --dest-key <key2> \
    --include "ab" \
    --sync-copy
```

`--sync-copy` kan generera ytterligare utgående kostnader jämfört med asynkron kopia. Den rekommenderade metoden är att använda det här alternativet i en Azure virtuell dator som är i samma region som din källagringskontot för att undvika kostnader för utgående trafik.

## <a name="file-download"></a>Fil: ladda ned
### <a name="download-single-file"></a>Hämta enstaka fil

```azcopy
azcopy \
    --source https://myaccount.file.core.windows.net/myfileshare/myfolder1/abc.txt \
    --destination /mnt/myfiles/abc.txt \
    --source-key <key>
```

Om den angivna källan är en Azure-filresurs så måste du antingen ange det exakta filnamnet (*t.ex.* `abc.txt`) att hämta en enda fil eller ange alternativet `--recursive` att ladda ned alla filer i filresursen rekursivt. Försök att ange ett filmönster och ett alternativet `--recursive` tillsammans uppstår ett fel.

### <a name="download-all-files"></a>Ladda ned alla filer

```azcopy
azcopy \
    --source https://myaccount.file.core.windows.net/myfileshare/ \
    --destination /mnt/myfiles \
    --source-key <key> \
    --recursive
```

Observera att alla tomma mappar inte laddas ned.

## <a name="file-upload"></a>Fil: ladda upp
### <a name="upload-single-file"></a>Ladda upp en fil

```azcopy
azcopy \
    --source /mnt/myfiles/abc.txt \
    --destination https://myaccount.file.core.windows.net/myfileshare/abc.txt \
    --dest-key <key>
```

### <a name="upload-all-files"></a>Överför alla filer

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.file.core.windows.net/myfileshare/ \
    --dest-key <key> \
    --recursive
```

Observera att alla tomma mappar inte laddas upp.

### <a name="upload-files-matching-specified-pattern"></a>Ladda upp filer som matchar angivna mönstret

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
När du kopierar en fil i filresurser, en [serversidan kopia](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) åtgärden utförs.

### <a name="copy-from-file-share-to-blob"></a>Kopiera från filresursen till blob

```azcopy
azcopy \ 
    --source https://myaccount1.file.core.windows.net/myfileshare/ \
    --destination https://myaccount2.blob.core.windows.net/mycontainer/ \
    --source-key <key1> \
    --dest-key <key2> \
    --recursive
```
När du kopierar en fil från filresursen till blob, en [serversidan kopia](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) åtgärden utförs.

### <a name="copy-from-blob-to-file-share"></a>Kopiera från blob till filresurs

```azcopy
azcopy \
    --source https://myaccount1.blob.core.windows.net/mycontainer/ \
    --destination https://myaccount2.file.core.windows.net/myfileshare/ \
    --source-key <key1> \
    --dest-key <key2> \
    --recursive
```
När du kopierar en fil från blob till filresurs, en [serversidan kopia](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) åtgärden utförs.

### <a name="synchronously-copy-files"></a>Synkront kopiera filer
Du kan ange den `--sync-copy` alternativet för att kopiera data från lagring av filer till File Storage, File Storage till Blob Storage och Blob Storage till fillagring synkront. AzCopy körs åtgärden genom att hämta källdata till lokalt minne och överföra den till målet. I det här fallet gäller standard utgående kostnaden.

```azcopy
azcopy \
    --source https://myaccount1.file.core.windows.net/myfileshare1/ \
    --destination https://myaccount2.file.core.windows.net/myfileshare2/ \
    --source-key <key1> \
    --dest-key <key2> \
    --recursive \
    --sync-copy
```

När du kopierar från fillagring till Blob Storage, blob standardtypen är blockblob, användaren kan ange alternativet `--blob-type page` ändra blob måltypen. Tillgängliga typer är `page | block | append`.

Observera att `--sync-copy` kan generera ytterligare utgående kostnad jämföra för asynkron kopia. Den rekommenderade metoden är att använda det här alternativet i en Azure virtuell dator som är i samma region som din källagringskontot för att undvika kostnader för utgående trafik.

## <a name="other-azcopy-features"></a>Andra AzCopy-funktioner
### <a name="only-copy-data-that-doesnt-exist-in-the-destination"></a>Kopiera endast data som inte finns i målet
Den `--exclude-older` och `--exclude-newer` parametrar kan du exkludera resurser som är äldre eller nyare källa från att kopieras respektive. Om du bara vill kopiera källresurser som inte finns i målet, kan du ange båda parametrarna i AzCopy-kommandot:

    --source http://myaccount.blob.core.windows.net/mycontainer --destination /mnt/myfiles --source-key <sourcekey> --recursive --exclude-older --exclude-newer

    --source /mnt/myfiles --destination http://myaccount.file.core.windows.net/myfileshare --dest-key <destkey> --recursive --exclude-older --exclude-newer

    --source http://myaccount.blob.core.windows.net/mycontainer --destination http://myaccount.blob.core.windows.net/mycontainer1 --source-key <sourcekey> --dest-key <destkey> --recursive --exclude-older --exclude-newer

### <a name="use-a-configuration-file-to-specify-command-line-parameters"></a>Använd en konfigurationsfil för att ange kommandoradsparametrar

```azcopy
azcopy --config-file "azcopy-config.ini"
```

Du kan inkludera eventuella kommandoradsparametrar med AzCopy i en konfigurationsfil. AzCopy bearbetar parametrarna i filen som om de hade angetts på kommandoraden, utföra en direkt ersättning med innehållet i filen.

Anta att en fil med namnet `copyoperation`, som innehåller följande rader. Varje AzCopy-parameter kan anges på en enda rad.

    --source http://myaccount.blob.core.windows.net/mycontainer --destination /mnt/myfiles --source-key <sourcekey> --recursive --quiet

eller på separata rader:

    --source http://myaccount.blob.core.windows.net/mycontainer
    --destination /mnt/myfiles
    --source-key<sourcekey>
    --recursive
    --quiet

AzCopy misslyckas om du dela upp parametern på två rader som visas här för den `--source-key` parameter:

    http://myaccount.blob.core.windows.net/mycontainer
    /mnt/myfiles
    --sourcekey
    <sourcekey>
    --recursive
    --quiet

### <a name="specify-a-shared-access-signature-sas"></a>Ange en signatur för delad åtkomst (SAS)

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer1/abc.txt \
    --destination https://myaccount.blob.core.windows.net/mycontainer2/abc.txt \
    --source-sas <SAS1> \
    --dest-sas <SAS2>
```

Du kan också ange en SAS URI-behållaren:

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer1/?SourceSASToken \
    --destination /mnt/myfiles \
    --recursive
```

### <a name="journal-file-folder"></a>Ändringsjournalen filmapp
Varje gång du utfärda ett kommando till AzCopy, kontrollerar den om en journalfil finns i standardmappen eller om den finns i en mapp som du har angett via det här alternativet. Om journalfilen inte finns på någon plats, AzCopy behandlar åtgärden som nya och genererar en ny journalfil.

Om journalfilen finns kontrollerar AzCopy om den kommandorad som du anger matchar kommandoraden i journal-fil. Om två kommandorader matchar, återupptar AzCopy i åtgärden slutfördes inte. Om de inte matchar uppmanar AzCopy användaren att antingen skriva över journalfil att starta en ny åtgärd eller för att avbryta den aktuella åtgärden.

Om du vill använda standardplatsen för journalfilen:

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --resume
```

Om du tar bort alternativet `--resume`, eller ange alternativet `--resume` utan mappsökvägen, enligt ovan, AzCopy skapar journal-fil på standardplatsen, vilket är `~\Microsoft\Azure\AzCopy`. Om ändringsjournalen filen redan finns, fortsätter igen baserat på journalfilen med AzCopy.

Om du vill ange en anpassad plats för journalfilen:

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key key \
    --resume "/mnt/myjournal"
```

Det här exemplet skapar journal-fil om den inte redan finns. Om den finns, fortsätter igen baserat på journalfilen med AzCopy.

Upprepa samma kommando om du vill återuppta en AzCopy-åtgärd. AzCopy i Linux sedan uppmanas bekräfta:

```azcopy
Incomplete operation with same command line detected at the journal directory "/home/myaccount/Microsoft/Azure/AzCopy", do you want to resume the operation? Choose Yes to resume, choose No to overwrite the journal to start a new operation. (Yes/No)
```

### <a name="output-verbose-logs"></a>Utförliga utdata-loggar

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --verbose
```

### <a name="specify-the-number-of-concurrent-operations-to-start"></a>Ange hur många samtidiga åtgärder för att starta
Alternativet `--parallel-level` anger hur många samtidiga kopieringsåtgärder. Som standard startar AzCopy ett visst antal samtidiga åtgärder för att öka dataflödet för överföring. Antalet samtidiga åtgärder är lika med åtta gånger antalet processorer som du har. Om du kör AzCopy i ett nätverk med låg bandbredd, kan du ange ett lägre värde--parallellt på servernivå för att undvika misslyckades på grund av en resurs.

>[!TIP]
>Om du vill visa den fullständiga listan med AzCopy parametrar, Kolla in 'azcopy--Hjälp-menyn.

## <a name="installation-steps-for-azcopy-71-and-earlier-versions"></a>Installationssteg för AzCopy 7.1 och tidigare versioner

AzCopy i Linux (v7.1 och tidigare) kräver .NET Core-framework. Installationsinstruktioner finns på den [.NET Core-installation](https://www.microsoft.com/net/core#linuxubuntu) sidan.

Till exempel börja med att installera .NET Core på Ubuntu 16,10. Installationsguide för senaste besök [.NET Core på Linux](https://www.microsoft.com/net/core#linuxubuntu) installationssidan.


```bash
sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/dotnet-release/ yakkety main" > /etc/apt/sources.list.d/dotnetdev.list' 
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 417A0893
sudo apt-get update
sudo apt-get install dotnet-sdk-2.0.0
```

När du har installerat .NET Core, hämta och installera AzCopy.

```bash
wget -O azcopy.tar.gz https://aka.ms/downloadazcopyprlinux
tar -xf azcopy.tar.gz
sudo ./install.sh
```

Du kan ta bort de extrahera filerna när AzCopy i Linux har installerats. Du kan också om du inte har behörighet som administratör kan du kan också köra `azcopy` med hjälp av azcopy för shell-skript i den extrahera mappen.

## <a name="known-issues-and-best-practices"></a>Kända problem och bästa praxis
### <a name="error-installing-azcopy"></a>Fel vid installation av AzCopy
Om du stöter på problem med AzCopy-installationen kan du försöka kör AzCopy med bash-skript i den extraherade `azcopy` mapp.

```bash
cd azcopy
./azcopy
```

### <a name="limit-concurrent-writes-while-copying-data"></a>Gräns för samtidiga skrivningar vid kopiering av data
När du kopierar blobar eller filer med AzCopy, Kom ihåg att ett annat program kan ändra data när du kopierar den. Kontrollera om det är möjligt att du kopierar data inte ändras under kopieringen. Till exempel när du kopierar en virtuell Hårddisk som är associerade med virtuella Azure-datorer, se till att inga andra program för närvarande skrivning till den virtuella Hårddisken. Det är ett bra sätt att göra detta genom leasing resursen som ska kopieras. Alternativt kan du först skapa en ögonblicksbild av den virtuella Hårddisken och sedan kopiera ögonblicksbilden.

Om du inte hindra andra program från att skriva till BLOB-objekt eller filer när de kopieras sedan Kom ihåg att när jobbet har slutförts, de kopierade resurserna kan inte längre ha fullständig paritet med resurserna som källa.

### <a name="running-multiple-azcopy-processes"></a>Flera AzCopy processer som körs
Du kan köra flera AzCopy processer på en enda klient att ange att du använder olika journalmappar. Med en enda journalmapp för flera AzCopy-processer stöds inte.

1 processen:
```azcopy
azcopy \
    --source /mnt/myfiles1 \
    --destination https://myaccount.blob.core.windows.net/mycontainer/myfiles1 \
    --dest-key <key> \
    --resume "/mnt/myazcopyjournal1"
```

2 processen:
```azcopy
azcopy \
    --source /mnt/myfiles2 \
    --destination https://myaccount.blob.core.windows.net/mycontainer/myfiles2 \
    --dest-key <key> \
    --resume "/mnt/myazcopyjournal2"
```

## <a name="next-steps"></a>Nästa steg
Mer information om Azure Storage och AzCopy finns i följande resurser:

### <a name="azure-storage-documentation"></a>Dokumentation om Azure Storage:
* [Introduktion till Azure Storage](../storage-introduction.md)
* [Skapa ett lagringskonto](../storage-create-storage-account.md)
* [Hantera blobar med Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs)
* [Använda Azure CLI 2.0 med Azure Storage](../storage-azure-cli.md)
* [Använda Blob storage från C++](../blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [Använda Blob Storage från Java](../blobs/storage-java-how-to-use-blob-storage.md)
* [Använda Blob Storage från Node.js](../blobs/storage-nodejs-how-to-use-blob-storage.md)
* [Använda Blob Storage från Python](../blobs/storage-python-how-to-use-blob-storage.md)

### <a name="azure-storage-blog-posts"></a>Azure Storage-blogginlägg:
* [Vi presenterar AzCopy i Linux-förhandsvisningen](https://azure.microsoft.com/en-in/blog/announcing-azcopy-on-linux-preview/)
* [Introduktion till Azure Storage Data Movement Library förhandsversion](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/)
* [AzCopy: Introduktion till synkron kopia och anpassade innehållstyp](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/01/13/azcopy-introducing-synchronous-copy-and-customized-content-type.aspx)
* [AzCopy: Meddelande om allmän tillgänglighet av AzCopy 3.0 plus förhandsversionen av AzCopy 4.0 med stöd för tabell och fil](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/10/29/azcopy-announcing-general-availability-of-azcopy-3-0-plus-preview-release-of-azcopy-4-0-with-table-and-file-support.aspx)
* [AzCopy: Optimerats för storskaliga kopia scenarier](http://go.microsoft.com/fwlink/?LinkId=507682)
* [AzCopy: Stöd för läsåtkomst till geografiskt redundant lagring](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/04/07/azcopy-support-for-read-access-geo-redundant-account.aspx)
* [AzCopy: Överföra data med omstartsläge och SAS-token](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/09/07/azcopy-transfer-data-with-re-startable-mode-and-sas-token.aspx)
* [AzCopy: Med hjälp av flera konto kopiering av Blob](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/04/01/azcopy-using-cross-account-copy-blob.aspx)
* [AzCopy: Ladda upp/ned filer för Azure-Blobar](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/12/03/azcopy-uploading-downloading-files-for-windows-azure-blobs.aspx)
