---
title: Migrera lokala data till Azure Storage med AzCopy | Microsoft Docs
description: "Använd AzCopy för att migrera data eller kopiera data till och från blob-, tabell- och innehåll. Enkelt migrera data från din lokala lagring till Azure-lagring."
services: storage
author: ruthogunnnaike
manager: jeconnoc
ms.service: storage
ms.tgt_pltfrm: na
ms.devlang: azcopy
ms.topic: tutorial
ms.date: 12/14/2017
ms.author: v-ruogun
ms.openlocfilehash: f2c0b80248ef706394b3f84df4c3da26fb79026a
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/02/2018
---
#  <a name="migrate-on-premises-data-to-cloud-storage-with-azcopy"></a>Migrera lokala data till lagringsutrymmet i molnet med AzCopy

AzCopy är ett kommandoradsverktyg som utformats för att kopiera data till och från Microsoft Azure Blob-, fil- och Table storage med hjälp av enkla kommandon som utformats för optimala prestanda. Du kan kopiera data mellan ett filsystem och ett lagringskonto eller mellan lagringskonton.  

Det finns två versioner av AzCopy som du kan ladda ned:

* [AzCopy på Linux](storage-use-azcopy.md) har byggts med .NET Core Framework, som riktar sig till Linux-plattformar som erbjuder POSIX format kommandoradsalternativ. 
* [AzCopy på Windows](../storage-use-azcopy.md) har skapats med .NET Framework och erbjuder kommandoradsalternativ för Windows-formatet. 
 
I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * skapar ett lagringskonto 
> * Använda AzCopy för att överföra alla data
> * Ändra data för testning
> * Skapa ett schemalagt jobb för aktivitet eller cron för att identifiera nya filer som ska överföras

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här kursen behöver du: 

* Hämta den senaste versionen av AzCopy på [Linux](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux#download-and-install-azcopy) eller [Windows](http://aka.ms/downloadazcopy). 

## <a name="log-in-to-the-azure-portalhttpsportalazurecom"></a>Logga in på [Azure-portalen](https://portal.azure.com/).

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

>[!NOTE]
>Om du vill kunna ladda ned blobbar från en sekundär region i din lokala lagring och vice versa, ange **replikering** till **Läs-åtkomst-geo-redundant lagring**. Om du väljer det här alternativet skapas en [geo-redundant lagring](https://docs.microsoft.com/azure/storage/common/storage-redundancy#geo-redundant-storage) konto. 
>
>

## <a name="create-a-container"></a>Skapa en behållare

Blobar laddas alltid upp till en behållare. Behållare kan du ordna grupper med blobbar som du ordna dina filer på datorn i mappar. 

Följ dessa steg om du vill skapa en behållare:

1. Välj den **lagringskonton** från huvudsidan och välj lagringskontot som du skapade.
2. Välj **Blobbar** under **Services**och välj **behållare**. 

![Skapa en behållare](media/storage-azcopy-migrate-on-premises-data/CreateContainer.png)
 
Behållarnamnet måste börja med en bokstav eller en siffra och får bara innehålla bokstäver, siffror och bindestreck (-). Mer information om namngivning av blobar och behållare finns i [Namngivning och referens av behållare, blobbar och metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

## <a name="upload-all-files-in-a-folder-to-blob-storage"></a>Ladda upp alla filer i en mapp till Blob storage

Du kan använda AzCopy för att överföra alla filer i en mapp till Blob storage på [Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy#upload-blobs-to-blob-storage) eller [Linux](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux#blob-download). Ange följande kommando för AzCopy för att överföra alla blobbar i en mapp:

# <a name="linuxtablinux"></a>[Linux](#tab/linux)
    azcopy \
        --source /mnt/myfolder \
        --destination https://myaccount.blob.core.windows.net/mycontainer \
        --dest-key <key> \
        --recursive

# <a name="windowstabwindows"></a>[Windows](#tab/windows)
    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey: key /S
---

Ersätt  **\<nyckeln\>**  med din kontonyckel. I Azure-portalen kan du hämta din kontonyckel genom att välja **åtkomstnycklar** under inställningar i ditt lagringskonto. Välj en nyckel och klistra in den i AzCopy-kommandot. Om den angivna Målbehållaren inte finns, skapar den AzCopy och överför filen till den. Uppdatera sökvägen till datakatalogen och Ersätt **MITTKONTO** i mål-URL för namnet på ditt lagringskonto.

Ange den `--recursive` och `/S` alternativ, i Linux och Windows, om du vill överföra innehållet i den angivna katalogen Blob storage rekursivt. När du kör AzCopy med något av följande alternativ, överförs alla undermappar och filer samt.

## <a name="upload-modified-files-to-blob-storage"></a>Ladda upp ändrade filer till Blob storage
Du kan använda AzCopy för att [ladda upp filer](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy#other-azcopy-features) baserat på deras tid för senaste ändring. Om du vill testa detta, ändra eller skapa nya filer i källkatalogen för att testa syfte. Överför bara uppdaterade eller nya filer genom att lägga till `--exclude-older`eller `/XO` parameter för AzCopy Linux och Windows kommandot respektive.

Om du bara vill kopiera käll-resurser som inte finns i målet ange både `--exclude-older` och `--exclude-newer` eller `/XO` och `/XN` parametrar i AzCopy Linux och Windows kommandot respektive. AzCopy Överför bara uppdaterade data baserat på deras tidsstämpel.
 
# <a name="linuxtablinux"></a>[Linux](#tab/linux)
    azcopy \
    --source /mnt/myfolder \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --recursive \
    --exclude-older

# <a name="windowstabwindows"></a>[Windows](#tab/windows)
    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey: key /S /XO
---

## <a name="create-a-scheduled-task-or-cron-job"></a>Skapa en schemalagd aktivitet eller cron jobb 
Du kan skapa en schemalagd aktivitet/cron jobb som körs ett skript för AzCopy-kommandot som identifierar och överför den nya lokala data till lagringsutrymmet i molnet på ett visst tidsintervall. 

Kopiera AzCopy-kommandot till en textredigerare. Uppdatera parametervärdena för AzCopy-kommandot till lämpliga värden. Spara filen som `script.sh` eller `script.bat` för AzCopy för Linux och Windows respektive. 

# <a name="linuxtablinux"></a>[Linux](#tab/linux)
    azcopy --source /mnt/myfiles --destination https://myaccount.blob.core.windows.net/mycontainer --dest-key <key> --recursive --exclude-older --exclude-newer --verbose >> Path/to/logfolder/`date +\%Y\%m\%d\%H\%M\%S`-cron.log

# <a name="windowstabwindows"></a>[Windows](#tab/windows)
    cd C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy
    AzCopy /Source: C:\myfolder  /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey: key /V /XO /XN >C:\Path\to\logfolder\azcopy%date:~-4,4%%date:~-7,2%%date:~-10,2%%time:~-11,2%%time:~-8,2%%time:~-5,2%.log
---

AzCopy körs med utförlig `--verbose` (Linux) och `/V` (Windows) alternativet och utdata omdirigeras till en loggfil. 

I den här självstudiekursen [Schtasks](https://msdn.microsoft.com/library/windows/desktop/bb736357(v=vs.85).aspx) används för att skapa en schemalagd aktivitet på Windows, och [Crontab](http://crontab.org/) kommandot används för att skapa ett cron-jobb på Linux. 
 **Schtasks** kan en administratör för att skapa, ta bort, fråga, ändra, köra och avsluta schemalagda aktiviteter på en lokal eller fjärransluten dator. **Cron** Linux och Unix användare kan köra kommandon eller skript vid angivet datum och tid med [cron-uttryck](https://en.wikipedia.org/wiki/Cron#CRON_expression)


# <a name="linuxtablinux"></a>[Linux](#tab/linux)
**Så här skapar du en cron på Linux**, anger du följande kommando på en terminal. 

```bash
crontab -e 
*/5 * * * * sh /path/to/script.sh 
```

Ange cron-uttryck `*/5 * * * * ` i kommandot shell-skript som anger `script.sh` ska köras var femte minut. Skriptet kan schemaläggas att köras vid en viss tid varje dag, månad eller år. Se [cron uttryck](https://en.wikipedia.org/wiki/Cron#CRON_expression) vill veta mer om hur du anger datum och tid för jobbkörningen. 

# <a name="windowstabwindows"></a>[Windows](#tab/windows)
**Skapa en schemalagd aktivitet på Windows**, anger du följande kommando i Kommandotolken eller PowerShell.

```cmd 
schtasks /CREATE /SC minute /MO 5 /TN "AzCopy Script" /TR C:\Users\username\Documents\script.bat
```

Kommandot använder den `/SC` parametern för att ange en minut schema och `/MO` parametern för att ange ett intervall på fem minuter. Den `/TN` parametern används för att ange aktivitetsnamnet på och `/TR` parametern för att ange sökvägen till `script.bat` filen. Besök [schtasks](https://technet.microsoft.com/library/cc772785(v=ws.10).aspx#BKMK_minutes) mer information om hur du skapar en schemalagd aktivitet i Windows.

---
 
Validera den schemalagda aktiviteten/cron jobbet körs korrekt, skapa nya filer i katalogen `myfolder`. Vänta på fem minuter för att bekräfta de nya filerna har överförts till ditt lagringskonto. Gå till log-katalogen för att visa utdata-loggarna för schemalagd uppgift/cron-jobbet. 

Besök [flytta på lokala data](https://docs.microsoft.com/azure/storage/common/storage-moving-data?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) Lär dig mer om olika sätt att flytta lokala data till Azure-lagring och vice versa.  

## <a name="next-steps"></a>Nästa steg
Mer information om Azure Storage och AzCopy finns i följande resurser:

### <a name="azure-storage-documentation"></a>Azure Storage-dokumentation:
* [Introduktion till Azure Storage](../storage-introduction.md)
* [Överföra data med AzCopy i Windows](storage-use-azcopy.md) 
* [Överföra data med AzCopy på Linux](storage-use-azcopy-linux.md) 
* [Skapa ett lagringskonto](../storage-create-storage-account.md)
* [Hantera blobbar med Lagringsutforskaren](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs)  






 
 

