---
title: 'Självstudier: Migrera lokala data till Azure Storage med AzCopy | Microsoft Docs'
description: I den här självstudien använder du AzCopy för att migrera data eller kopiera data till och från blob-, tabell- och filinnehåll. Migrera enkelt data från din lokala lagring till Azure Storage.
services: storage
author: normesta
ms.service: storage
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: normesta
ms.reviewer: seguler
ms.subservice: common
ms.openlocfilehash: 64d79abd1e142a231c08e02e7d62e8bfbab7b90e
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244727"
---
#  <a name="tutorial-migrate-on-premises-data-to-cloud-storage-by-using-azcopy"></a>Självstudier: Migrera lokala data till molnlagring med AzCopy

AzCopy är ett kommandoradsverktyg med vilket du kan kopiera data till eller från Azure Blob Storage, Azure Files och Azure Table Storage med hjälp av enkla kommandon. Kommandona är utformade för att ge optimala prestanda. Med AzCopy kan du antingen kopiera data mellan ett filsystem och ett lagringskonto eller mellan lagringskonton. AzCopy kan användas för att kopiera data från lokala data till ett lagringskonto.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa ett lagringskonto. 
> * Överför alla dina data med AzCopy.
> * Ändra data i testsyfte.
> * Skapa en schemalagd uppgift eller ett Cron-jobb som identifierar nya filer att överföra.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Ladda ned den senaste versionen av AzCopy för att slutföra den här självstudien. Se [Kom igång med AzCopy](storage-use-azcopy-v10.md).

Om du kör Windows behöver du [Schtasks](https://msdn.microsoft.com/library/windows/desktop/bb736357(v=vs.85).aspx) eftersom den här självstudien använder det för att schemalägga en uppgift. Linux-användare använder i stället crontab-kommandot.

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

## <a name="create-a-container"></a>Skapa en container

Det första steget är att skapa en container, eftersom blobar alltid måste laddas upp till en container. Containrar används som en metod för att organisera grupper av blobar på samma sätt som du gör med filer i mappar på datorn.

Skapa en container genom att följa de här stegen:

1. Välj knappen **Lagringskonton** på huvudsidan och markera det lagringskonto som du har skapat.
2. Välj **Blobar** under **Tjänster**, och välj sedan **Container**.

   ![Skapa en container](media/storage-azcopy-migrate-on-premises-data/CreateContainer.png)
 
Containernamn måste börja med en bokstav eller siffra. De får bara innehålla bokstäver, siffror och bindestreck (-). Mer information om namngivning av blobar och containrar finns i [Namngivning och referens av containrar, blobar och metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

## <a name="download-azcopy"></a>Ladda ned AzCopy

Ladda ned den körbara filen med AzCopy V10.

- [Windows](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Linux](https://aka.ms/downloadazcopy-v10-linux) (tar)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

Placera filen AzCopy var som helst på datorn. Lägga till filens plats path-systemvariabeln så att du kan referera till den här körbara filen från en mapp på datorn.

## <a name="authenticate-with-azure-ad"></a>Autentisera med Azure AD

Först, tilldela den [Storage Blob Data-deltagare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor) rollen till din identitet. Se [bevilja åtkomst till Azure blob och kö data med RBAC i Azure-portalen](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal).

Öppna en kommandotolk, Skriv följande kommando och trycker på RETUR-tangenten.

```azcopy
azcopy login
```

Det här kommandot returnerar en Autentiseringskod och Webbadressen till en webbplats. Öppna webbplatsen, ange koden och välj sedan den **nästa** knappen.

![Skapa en container](media/storage-use-azcopy-v10/azcopy-login.png)

Ett fönster för inloggning visas. Logga in på ditt Azure-konto med hjälp av dina Azure-autentiseringsuppgifter i det aktuella fönstret. När du har loggat in, kan du stänga webbläsaren och börja använda AzCopy.

## <a name="upload-contents-of-a-folder-to-blob-storage"></a>Ladda upp innehåll i en mapp till Blob Storage

Du kan överföra alla filer i en mapp till Blob Storage i [Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy#upload-blobs-to-blob-storage) eller [Linux](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux#blob-download) med AzCopy. Överför alla blobar i en mapp genom att ange följande AzCopy-kommando:

```AzCopy
azcopy copy "<local-folder-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive=true
```

* Ersätt den `<local-folder-path>` med sökvägen till en mapp som innehåller filer (till exempel: `C:\myFolder` eller `/mnt/myFolder`).

* Ersätt platshållaren `<storage-account-name>` med namnet på ditt lagringskonto.

* Ersätt den `<container-name>` platshållare med namnet på behållaren som du skapade.

Om du vill överföra innehållet i den angivna katalogen till Blob storage rekursivt, ange den `--recursive` alternativet. När du kör AzCopy med det här alternativet, laddas alla undermappar och filer samt.

## <a name="upload-modified-files-to-blob-storage"></a>Modifierade filer har överförts till Blob Storage

Du kan använda AzCopy för att ladda upp filer baserat på deras tid för senaste ändring. 

Om du vill testa detta, så ändra eller skapa nya filer i källkatalogen i testsyfte. Använd sedan AzCopy `sync` kommando.

```AzCopy
azcopy sync "<local-folder-path>" "https://<storage-account-name>.blob.core.windows.net/<container-name>" --recursive=true
```

* Ersätt den `<local-folder-path>` med sökvägen till en mapp som innehåller filer (till exempel: `C:\myFolder` eller `/mnt/myFolder`.

* Ersätt platshållaren `<storage-account-name>` med namnet på ditt lagringskonto.

* Ersätt den `<container-name>` platshållare med namnet på behållaren som du skapade.

Mer information om den `sync` kommandot, se [synkronisera filer](storage-use-azcopy-blobs.md#synchronize-files).

## <a name="create-a-scheduled-task"></a>Skapa en schemalagd uppgift

Du kan skapa en schemalagd uppgift eller ett Cron-jobb som kör ett AzCopy-kommandoskript. Skriptet identifierar och överför nya lokala data till molnlagringen enligt ett specifikt tidsintervall.

Kopiera AzCopy-kommandot till en textredigerare. Uppdatera AzCopy-kommandots parametervärden till korrekta värden. Spara filen som `script.sh` (Linux) eller `script.bat` (Windows) för AzCopy. 

Dessa exempel förutsätter att mappen har namnet `myFolder`, namnet på ditt lagringskonto är `mystorageaccount` och behållarens namn är `mycontainer`.

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

    azcopy sync "/mnt/myfiles" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive=true

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

    azcopy sync "C:\myFolder" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive=true

---

I den här självstudiekursen används [Schtasks](https://msdn.microsoft.com/library/windows/desktop/bb736357(v=vs.85).aspx) för att skapa en schemalagd uppgift i Windows. Kommandot [Crontab](http://crontab.org/) används för att skapa ett Cron-jobb i Linux.

 Med **Schtasks** kan en administratör skapa, ta bort, fråga, ändra, köra och avsluta schemalagda uppgifter på en lokal eller fjärransluten dator. Med **Cron** kan Linux- och Unix-användare köra kommandon eller skript angivet datum och angiven med hjälp av [Cron-uttryck](https://en.wikipedia.org/wiki/Cron#CRON_expression).

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

Om du vill skapa ett Cron-jobb på Linux anger du följande kommando på en terminal:

```bash
crontab -e
*/5 * * * * sh /path/to/script.sh
```

Om du anger Cron-uttrycket `*/5 * * * *` i kommandot indikerar detta att kommandoskriptet `script.sh` ska köras var femte minut. Du kan schemalägga skriptet så att det körs vid en viss tidpunkt varje dag, varje månad eller varje år. Mer information om hur du anger datum och tid för jobbkörning finns [Cron-uttryck](https://en.wikipedia.org/wiki/Cron#CRON_expression).

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

Skapa en schemalagd uppgift i Windows genom att ange följande kommando i kommandotolken eller PowerShell:

Det här exemplet förutsätter att skriptet finns i datorns rotenhet datorn, men ditt skript kan finnas var som helst som du vill.

```cmd
schtasks /CREATE /SC minute /MO 5 /TN "AzCopy Script" /TR C:\script.bat
```

Kommandot använder:
- Parametern `/SC` för att ange ett minutschema.
- Parametern `/MO` för att ange ett femminutersintervall.
- Parametern `/TN` för att ange uppgiftens namn.
- Parametern `/TR` för att ange sökvägen till filen `script.bat`.

Mer information om hur du skapar en schemalagd uppgift i Windows finns i [Schtasks](https://technet.microsoft.com/library/cc772785(v=ws.10).aspx#BKMK_minutes).

---

Du kan verifiera att den schemalagda uppgiften/Cron-jobbet körs korrekt genom att skapa nya filer i din `myFolder`-katalog. Vänta fem minuter och bekräfta att de nya filerna har överförts till ditt lagringskonto. Gå till loggkatalogen och visa den schemalagda uppgiftens eller Cron-jobbets utdataloggar.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du flyttar lokala data till Azure Storage och vice versa finns i följande länk:

* [Flytta data till och från Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-moving-data?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).  

Mer information om AzCopy finns i någon av följande artiklar:

* [Kom igång med AzCopy](storage-use-azcopy-v10.md)

* [Överföra data med AzCopy och blob storage](storage-use-azcopy-blobs.md)

* [Överföra data med AzCopy och fillagring](storage-use-azcopy-files.md)

* [Överföra data med AzCopy och Amazon S3 buckets](storage-use-azcopy-s3.md)
 
* [Konfigurera, optimera och felsöka AzCopy](storage-use-azcopy-configure.md)
