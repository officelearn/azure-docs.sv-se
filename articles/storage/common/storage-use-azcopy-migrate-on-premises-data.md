---
title: "Migrera lokala data till Azure Storage med hjälp av AzCopy | Microsoft Docs"
description: "Använd AzCopy för att migrera data eller kopiera data till och från blob-, tabell- och innehåll. Enkelt migrera data från din lokala lagring till Azure Storage."
services: storage
author: ruthogunnnaike
manager: jeconnoc
ms.service: storage
ms.tgt_pltfrm: na
ms.devlang: azcopy
ms.topic: tutorial
ms.date: 12/14/2017
ms.author: v-ruogun
ms.openlocfilehash: 3dbfb935ac0b134e127a5dccb7bc76716c688e8a
ms.sourcegitcommit: c4cc4d76932b059f8c2657081577412e8f405478
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2018
---
#  <a name="migrate-on-premises-data-to-cloud-storage-by-using-azcopy"></a>Migrera lokala data till molnet lagring med hjälp av AzCopy

AzCopy är ett kommandoradsverktyg för att kopiera data till eller från Azure Blob storage, Azure-filer och Azure Table storage med hjälp av enkla kommandon. Kommandona är utformade för optimala prestanda. Du kan kopiera data mellan ett filsystem och ett lagringskonto eller mellan lagringskonton.  

Du kan hämta två versioner av AzCopy:

* [AzCopy på Linux](storage-use-azcopy.md) har byggts med .NET Core Framework. Den riktar sig till Linux-plattformar genom att erbjuda POSIX-typ kommandoradsalternativ. 
* [AzCopy på Windows](../storage-use-azcopy.md) har byggts med .NET Framework. Den erbjuder kommandoradsalternativ för Windows-format. 
 
I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Skapa ett lagringskonto. 
> * Använda AzCopy för att överföra alla data.
> * Ändra data för testning.
> * Skapa ett schemalagt jobb för aktivitet eller cron för att identifiera nya filer som ska överföras.

## <a name="prerequisites"></a>Förutsättningar

Den här kursen kan du hämta den senaste versionen av AzCopy på [Linux](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux#download-and-install-azcopy) eller [Windows](http://aka.ms/downloadazcopy). 

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

>[!NOTE]
>Om du vill kunna ladda ned blobbar från en sekundär region i din lokala lagring och vice versa, ange **replikering** till **Läs-åtkomst-geo-redundant lagring**. Om du väljer det här alternativet skapas en [geo-redundant lagring](https://docs.microsoft.com/azure/storage/common/storage-redundancy#geo-redundant-storage) konto. 
>
>

## <a name="create-a-container"></a>Skapa en behållare

Blobar laddas alltid upp till en behållare. Du kan använda behållare för att ordna grupper med blobbar som du ordna dina filer på datorn i mappar. 

Följ dessa steg om du vill skapa en behållare:

1. Välj den **lagringskonton** från huvudsidan och välj lagringskontot som du skapade.
2. Välj **Blobbar** under **Services**, och välj sedan **behållare**. 

   ![Skapa en behållare](media/storage-azcopy-migrate-on-premises-data/CreateContainer.png)
 
Behållarnamn måste börja med en bokstav eller siffra. De kan innehålla endast bokstäver, siffror och bindestreck (-). Mer information om namngivning av blobar och behållare finns i [Namngivning och referens av behållare, blobbar och metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

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

Ersätt `<key>` och `key` med din kontonyckel. I Azure-portalen kan du hämta din kontonyckel genom att välja **åtkomstnycklar** under **inställningar** i ditt lagringskonto. Välj en nyckel och klistra in den i AzCopy-kommandot. Om den angivna Målbehållaren inte finns, skapar den AzCopy och överför filen till den. Uppdatera sökvägen till datakatalogen och Ersätt **MITTKONTO** i mål-URL med namnet på ditt lagringskonto.

Om du vill överföra innehållet i den angivna katalogen Blob storage rekursivt, ange den `--recursive` (Linux) eller `/S` (Windows) alternativet. När du kör AzCopy med något av följande alternativ, överförs alla undermappar och filer samt.

## <a name="upload-modified-files-to-blob-storage"></a>Ladda upp ändrade filer till Blob storage
Du kan använda AzCopy för att [ladda upp filer](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy#other-azcopy-features) baserat på deras tid för senaste ändring. Om du vill testa detta, ändra eller skapa nya filer i källkatalogen för testning. Överför bara uppdaterade eller nya filer genom att lägga till den `--exclude-older` (Linux) eller `/XO` (Windows) parametern AzCopy-kommandot.

Om du bara vill kopiera käll-resurser som inte finns i målet ange både `--exclude-older` och `--exclude-newer` (Linux) eller `/XO` och `/XN` (Windows)-parametrar i AzCopy-kommandot. AzCopy Överför bara uppdaterade data, baserat på dess tidsstämpel.
 
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
Du kan skapa en schemalagd aktivitet eller cron jobb som körs ett skript för AzCopy-kommandot. Skriptet identifierar och överför den nya lokala data till lagringsutrymmet i molnet på ett visst tidsintervall. 

Kopiera AzCopy-kommandot till en textredigerare. Uppdatera parametervärdena för AzCopy-kommandot till lämpliga värden. Spara filen som `script.sh` (Linux) eller `script.bat` (Windows) för AzCopy. 

# <a name="linuxtablinux"></a>[Linux](#tab/linux)
    azcopy --source /mnt/myfiles --destination https://myaccount.blob.core.windows.net/mycontainer --dest-key <key> --recursive --exclude-older --exclude-newer --verbose >> Path/to/logfolder/`date +\%Y\%m\%d\%H\%M\%S`-cron.log

# <a name="windowstabwindows"></a>[Windows](#tab/windows)
    cd C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy
    AzCopy /Source: C:\myfolder  /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey: key /V /XO /XN >C:\Path\to\logfolder\azcopy%date:~-4,4%%date:~-7,2%%date:~-10,2%%time:~-11,2%%time:~-8,2%%time:~-5,2%.log
---

AzCopy körs med den utförliga `--verbose` (Linux) eller `/V` (Windows) alternativet. Utdata omdirigeras till en loggfil. 

I den här självstudiekursen [Schtasks](https://msdn.microsoft.com/library/windows/desktop/bb736357(v=vs.85).aspx) används för att skapa en schemalagd aktivitet i Windows. Den [Crontab](http://crontab.org/) kommandot används för att skapa ett cron-jobb på Linux. 
 **Schtasks** kan en administratör för att skapa, ta bort, fråga, ändra, köra och avsluta schemalagda aktiviteter på en lokal eller fjärransluten dator. **Cron** gör det möjligt för Linux och Unix-användare att köra kommandon eller skript på ett angivet datum och tid genom att använda [cron uttryck](https://en.wikipedia.org/wiki/Cron#CRON_expression).


# <a name="linuxtablinux"></a>[Linux](#tab/linux)
Om du vill skapa ett cron-jobb på Linux, anger du följande kommando på en terminal: 

```bash
crontab -e 
*/5 * * * * sh /path/to/script.sh 
```

Ange cron-uttryck `*/5 * * * * ` i kommandot indikerar att du kommandoskript `script.sh` ska köras var femte minut. Du kan schemalägga skriptet ska köras vid en viss tid varje dag, månad eller per år. Mer information om hur du anger datum och tid för jobbkörningen finns [cron uttryck](https://en.wikipedia.org/wiki/Cron#CRON_expression). 

# <a name="windowstabwindows"></a>[Windows](#tab/windows)
Ange följande kommando i Kommandotolken eller i PowerShell för att skapa en schemalagd aktivitet i Windows:

```cmd 
schtasks /CREATE /SC minute /MO 5 /TN "AzCopy Script" /TR C:\Users\username\Documents\script.bat
```

Kommandot använder:
- Den `/SC` parametern för att ange en minut schema.
- Den `/MO` parametern för att ange ett intervall på fem minuter.
- Den `/TN` parametern för att ange namnet på aktiviteten.
- Den `/TR` parametern för att ange sökvägen till den `script.bat` filen. 

Mer information om hur du skapar en schemalagd aktivitet i Windows finns [Schtasks](https://technet.microsoft.com/library/cc772785(v=ws.10).aspx#BKMK_minutes).

---
 
Du kan kontrollera att schemalagda uppgiften/cron jobbet körs korrekt genom att skapa nya filer i din `myfolder` directory. Vänta 5 minuter för att bekräfta att de nya filerna har överförts till ditt lagringskonto. Gå till log-katalogen för att visa utdata-loggarna för schemalagd aktivitet eller cron-jobbet. 

Mer information om hur du flyttar lokala data till Azure Storage och vice versa finns [flytta data till och från Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-moving-data?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).  

## <a name="next-steps"></a>Nästa steg
Mer information om Azure Storage och AzCopy finns i följande resurser:

* [Introduktion till Azure Storage](../storage-introduction.md)
* [Överföra data med AzCopy i Windows](storage-use-azcopy.md) 
* [Överföra data med AzCopy på Linux](storage-use-azcopy-linux.md) 
* [Skapa ett lagringskonto](../storage-create-storage-account.md)
* [Hantera blobbar med Lagringsutforskaren](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs)  






 
 

