---
title: Migrera lokala data till Azure Storage med AzCopy | Microsoft Docs
description: Använd AzCopy när du vill migrera data eller kopiera data till och från blob-, tabell- och filinnehåll. Migrera enkelt data från din lokala lagring till Azure Storage.
services: storage
author: roygara
ms.service: storage
ms.devlang: azcopy
ms.topic: tutorial
ms.date: 12/14/2017
ms.author: rogarana
ms.component: common
ms.openlocfilehash: bb3df82e66024b49e399ab2f7795a27932f7559e
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46953981"
---
#  <a name="migrate-on-premises-data-to-cloud-storage-by-using-azcopy"></a>Migrera lokala data till molnlagring med AzCopy

AzCopy är ett kommandoradsverktyg med vilket du kan kopiera data till eller från Azure Blob Storage, Azure Files och Azure Table Storage med hjälp av enkla kommandon. Kommandona är utformade för att ge optimala prestanda. Du kan kopiera data mellan ett filsystem och ett lagringskonto, eller mellan lagringskonton.  

Du kan hämta två versioner av AzCopy:

* [AzCopy på Linux](storage-use-azcopy-linux.md) har byggts med .NET Core Framework. Den är inriktad på Linux-plattformar och erbjuder kommandoradsalternativ av POSIX-typ. 
* [AzCopy på Windows](storage-use-azcopy.md) har byggts med .NET Framework. Den tillhandahåller kommandoradsalternativ i Windows-format. 
 
I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa ett lagringskonto. 
> * Överför alla dina data med AzCopy.
> * Ändra data i testsyfte.
> * Skapa en schemalagd uppgift eller ett Cron-jobb som identifierar nya filer att överföra.

## <a name="prerequisites"></a>Nödvändiga komponenter

Slutför den här självstudien genom att ladda ned den senaste versionen av AzCopy på [Linux](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux#download-and-install-azcopy) eller [Windows](http://aka.ms/downloadazcopy). 

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

>[!NOTE]
>Om du vill kunna ladda ned blobar från en sekundär region till din lokala lagring och vice versa, så ange **Read-Access Geo Redundant Storage** för **Replikering**. Om du väljer det här alternativet skapas ett konto för [geo-redundant lagring](https://docs.microsoft.com/azure/storage/common/storage-redundancy#geo-redundant-storage). 
>
>

## <a name="create-a-container"></a>Skapa en container

Blobar laddas alltid upp till en container. Du kan använda containrar för att ordna blobgrupper på samma sätt som du ordnar filer i mappar på datorn. 

Skapa en container genom att följa de här stegen:

1. Välj knappen **Lagringskonton** på huvudsidan och markera det lagringskonto som du har skapat.
2. Välj **Blobar** under **Tjänster**, och välj sedan **Container**. 

   ![Skapa en container](media/storage-azcopy-migrate-on-premises-data/CreateContainer.png)
 
Containernamn måste börja med en bokstav eller siffra. De får bara innehålla bokstäver, siffror och bindestreck (-). Mer information om namngivning av blobar och containrar finns i [Namngivning och referens av containrar, blobar och metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

## <a name="upload-all-files-in-a-folder-to-blob-storage"></a>Överför alla filer i en mapp till Blob Storage

Du kan överföra alla filer i en mapp till Blob Storage i [Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy#upload-blobs-to-blob-storage) eller [Linux](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux#blob-download) med AzCopy. Överför alla blobar i en mapp genom att ange följande AzCopy-kommando:

# <a name="linuxtablinux"></a>[Linux](#tab/linux)
    azcopy \
        --source /mnt/myfolder \
        --destination https://myaccount.blob.core.windows.net/mycontainer \
        --dest-key <key> \
        --recursive

# <a name="windowstabwindows"></a>[Windows](#tab/windows)
    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:<key> /S
---

Ersätt `<key>` och `key` med din kontonyckel. I Azure Portal kan du hämta din kontonyckel genom att välja **Åtkomstnycklar** under **Inställningar** i ditt lagringskonto. Välj en nyckel och klistra in den i AzCopy-kommandot. Om den angivna målcontainern inte finns, så skapar AzCopy den och överför filen till den. Uppdatera sökvägen till datakatalogen och ersätt **mittkonto** i mål-URL:en med lagringskontots namn.

Om du vill överföra den angivna katalogens innehåll till Blob Storage rekursivt, så ange alternativet `--recursive` (Linux) eller `/S` (Windows). När du kör AzCopy med något av följande alternativ, så överförs även alla undermappar och filer.

## <a name="upload-modified-files-to-blob-storage"></a>Modifierade filer har överförts till Blob Storage
Du kan använda AzCopy för att [överföra filer](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy#other-azcopy-features) utifrån deras tid för senaste ändring. Om du vill testa detta, så ändra eller skapa nya filer i källkatalogen i testsyfte. Om du bara vill överföra uppdaterade eller nya filer, så lägg till parametern `--exclude-older` (Linux) eller `/XO` (Windows) i AzCopy-kommandot.

Om du bara vill kopiera källresurser som inte finns i målet, så ange båda parametrarna `--exclude-older` och `--exclude-newer` (Linux) eller `/XO` och `/XN` (Windows) i AzCopy-kommandot. AzCopy överför bara uppdaterade data utifrån deras tidsstämplar.
 
# <a name="linuxtablinux"></a>[Linux](#tab/linux)
    azcopy \
    --source /mnt/myfolder \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --recursive \
    --exclude-older

# <a name="windowstabwindows"></a>[Windows](#tab/windows)
    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:<key> /S /XO
---

## <a name="create-a-scheduled-task-or-cron-job"></a>Skapa en schemalagd uppgift eller ett Cron-jobb 
Du kan skapa en schemalagd uppgift eller ett Cron-jobb som kör ett AzCopy-kommandoskript. Skriptet identifierar och överför nya lokala data till molnlagringen enligt ett specifikt tidsintervall. 

Kopiera AzCopy-kommandot till en textredigerare. Uppdatera AzCopy-kommandots parametervärden till korrekta värden. Spara filen som `script.sh` (Linux) eller `script.bat` (Windows) för AzCopy. 

# <a name="linuxtablinux"></a>[Linux](#tab/linux)
    azcopy --source /mnt/myfiles --destination https://myaccount.blob.core.windows.net/mycontainer --dest-key <key> --recursive --exclude-older --exclude-newer --verbose >> Path/to/logfolder/`date +\%Y\%m\%d\%H\%M\%S`-cron.log

# <a name="windowstabwindows"></a>[Windows](#tab/windows)
    cd C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy
    AzCopy /Source: C:\myfolder  /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:<key> /V /XO /XN >C:\Path\to\logfolder\azcopy%date:~-4,4%%date:~-7,2%%date:~-10,2%%time:~-11,2%%time:~-8,2%%time:~-5,2%.log
---

AzCopy körs med det utförliga alternativet `--verbose` (Linux) eller `/V` (Windows). Utdata omdirigeras till en loggfil. 

I den här självstudiekursen används [Schtasks](https://msdn.microsoft.com/library/windows/desktop/bb736357(v=vs.85).aspx) för att skapa en schemalagd uppgift i Windows. Kommandot [Crontab](http://crontab.org/) används för att skapa ett Cron-jobb i Linux. 
 Med **Schtasks** kan en administratör skapa, ta bort, fråga, ändra, köra och avsluta schemalagda uppgifter på en lokal eller fjärransluten dator. Med **Cron** kan Linux- och Unix-användare köra kommandon eller skript angivet datum och angiven med hjälp av [Cron-uttryck](https://en.wikipedia.org/wiki/Cron#CRON_expression).


# <a name="linuxtablinux"></a>[Linux](#tab/linux)
Om du vill skapa ett Cron-jobb på Linux anger du följande kommando på en terminal: 

```bash
crontab -e 
*/5 * * * * sh /path/to/script.sh 
```

Om du anger Cron-uttrycket `*/5 * * * * ` i kommandot indikerar detta att kommandoskriptet `script.sh` ska köras var femte minut. Du kan schemalägga skriptet så att det körs vid en viss tidpunkt varje dag, varje månad eller varje år. Mer information om hur du anger datum och tid för jobbkörning finns [Cron-uttryck](https://en.wikipedia.org/wiki/Cron#CRON_expression). 

# <a name="windowstabwindows"></a>[Windows](#tab/windows)
Skapa en schemalagd uppgift i Windows genom att ange följande kommando i kommandotolken eller PowerShell:

```cmd 
schtasks /CREATE /SC minute /MO 5 /TN "AzCopy Script" /TR C:\Users\username\Documents\script.bat
```

Kommandot använder:
- Parametern `/SC` för att ange ett minutschema.
- Parametern `/MO` för att ange ett femminutersintervall.
- Parametern `/TN` för att ange uppgiftens namn.
- Parametern `/TR` för att ange sökvägen till filen `script.bat`. 

Mer information om hur du skapar en schemalagd uppgift i Windows finns i [Schtasks](https://technet.microsoft.com/library/cc772785(v=ws.10).aspx#BKMK_minutes).

---
 
Du kan verifiera att den schemalagda uppgiften/Cron-jobbet körs korrekt genom att skapa nya filer i din `myfolder`-katalog. Vänta fem minuter och bekräfta att de nya filerna har överförts till ditt lagringskonto. Gå till loggkatalogen och visa den schemalagda uppgiftens eller Cron-jobbets utdataloggar. 

Mer information om hur du flyttar lokala data till Azure Storage och vice versa finns i [Flytta data till och från Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-moving-data?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).  

## <a name="next-steps"></a>Nästa steg
Mer information om Azure Storage och AzCopy finns i följande resurser:

* [Introduktion till Azure Storage](../storage-introduction.md)
* [Överföra data med AzCopy i Windows](storage-use-azcopy.md) 
* [Överföra data med AzCopy i Linux](storage-use-azcopy-linux.md) 
* [Skapa ett lagringskonto](../storage-create-storage-account.md)
* [Hantera blobar med Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs)  






 
 

