---
title: 'Tutorial: Migrate on-premises data to Azure Storage with AzCopy| Microsoft Docs'
description: I den här självstudien använder du AzCopy för att migrera data eller kopiera data till och från blob-, tabell- och filinnehåll. Migrera enkelt data från din lokala lagring till Azure Storage.
author: normesta
ms.service: storage
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: normesta
ms.reviewer: seguler
ms.subservice: common
ms.openlocfilehash: f7155053072b3533503765dc6f4fbf185d21f0d4
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327522"
---
#  <a name="tutorial-migrate-on-premises-data-to-cloud-storage-with-azcopy"></a>Tutorial: Migrate on-premises data to cloud storage with AzCopy

AzCopy är ett kommandoradsverktyg med vilket du kan kopiera data till eller från Azure Blob Storage, Azure Files och Azure Table Storage med hjälp av enkla kommandon. Kommandona är utformade för att ge optimala prestanda. Med AzCopy kan du antingen kopiera data mellan ett filsystem och ett lagringskonto eller mellan lagringskonton. AzCopy kan användas för att kopiera data från lokala data till ett lagringskonto.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa ett lagringskonto. 
> * Överför alla dina data med AzCopy.
> * Ändra data i testsyfte.
> * Skapa en schemalagd uppgift eller ett Cron-jobb som identifierar nya filer att överföra.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Krav

To complete this tutorial, download the latest version of AzCopy. See [Get started with AzCopy](storage-use-azcopy-v10.md).

Om du kör Windows behöver du [Schtasks](https://msdn.microsoft.com/library/windows/desktop/bb736357(v=vs.85).aspx) eftersom den här självstudien använder det för att schemalägga en uppgift. Linux-användare använder i stället crontab-kommandot.

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

## <a name="create-a-container"></a>Skapa en container

Det första steget är att skapa en container, eftersom blobar alltid måste laddas upp till en container. Containrar används som en metod för att organisera grupper av blobar på samma sätt som du gör med filer i mappar på datorn.

Skapa en container genom att följa de här stegen:

1. Välj knappen **Lagringskonton** på huvudsidan och markera det lagringskonto som du har skapat.
2. Välj **Blobar** under **Tjänster**, och välj sedan **Container**.

   ![Skapa en container](media/storage-azcopy-migrate-on-premises-data/CreateContainer.png)
 
Containernamn måste börja med en bokstav eller siffra. De får bara innehålla bokstäver, siffror och bindestreck (-). Mer information om namngivning av blobar och containrar finns i [Namngivning och referens av containrar, blobar och metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

## <a name="download-azcopy"></a>Download AzCopy

Download the AzCopy V10 executable file.

- [Windows](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Linux](https://aka.ms/downloadazcopy-v10-linux) (tar)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

Place the AzCopy file anywhere on your computer. Add the location of the file to your system path variable so that you can refer to this executable file from any folder on your computer.

## <a name="authenticate-with-azure-ad"></a>Autentisera med Azure AD

First, assign the [Storage Blob Data Contributor](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor) role to your identity. See [Grant access to Azure blob and queue data with RBAC in the Azure portal](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal).

Then, open a command prompt, type the following command, and press the ENTER key.

```azcopy
azcopy login
```

This command returns an authentication code and the URL of a website. Open the website, provide the code, and then choose the **Next** button.

![Skapa en container](media/storage-use-azcopy-v10/azcopy-login.png)

A sign-in window will appear. In that window, sign into your Azure account by using your Azure account credentials. After you've successfully signed in, you can close the browser window and begin using AzCopy.

## <a name="upload-contents-of-a-folder-to-blob-storage"></a>Ladda upp innehåll i en mapp till Blob Storage

Du kan överföra alla filer i en mapp till Blob Storage i [Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) eller [Linux](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux) med AzCopy. Överför alla blobar i en mapp genom att ange följande AzCopy-kommando:

```AzCopy
azcopy copy "<local-folder-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive=true
```

* Replace the `<local-folder-path>` placeholder with the path to a folder that contains files (For example: `C:\myFolder` or `/mnt/myFolder`).

* Ersätt platshållaren `<storage-account-name>` med namnet på ditt lagringskonto.

* Replace the `<container-name>` placeholder with the name of the container that you created.

To upload the contents of the specified directory to Blob storage recursively, specify the `--recursive` option. When you run AzCopy with this option, all subfolders and their files are uploaded as well.

## <a name="upload-modified-files-to-blob-storage"></a>Modifierade filer har överförts till Blob Storage

You can use AzCopy to upload files based on their last-modified time. 

Om du vill testa detta, så ändra eller skapa nya filer i källkatalogen i testsyfte. Then, use the AzCopy `sync` command.

```AzCopy
azcopy sync "<local-folder-path>" "https://<storage-account-name>.blob.core.windows.net/<container-name>" --recursive=true
```

* Replace the `<local-folder-path>` placeholder with the path to a folder that contains files (For example: `C:\myFolder` or `/mnt/myFolder`.

* Ersätt platshållaren `<storage-account-name>` med namnet på ditt lagringskonto.

* Replace the `<container-name>` placeholder with the name of the container that you created.

To learn more about the `sync` command, see [Synchronize files](storage-use-azcopy-blobs.md#synchronize-files).

## <a name="create-a-scheduled-task"></a>Skapa en schemalagd uppgift

Du kan skapa en schemalagd uppgift eller ett Cron-jobb som kör ett AzCopy-kommandoskript. Skriptet identifierar och överför nya lokala data till molnlagringen enligt ett specifikt tidsintervall.

Kopiera AzCopy-kommandot till en textredigerare. Uppdatera AzCopy-kommandots parametervärden till korrekta värden. Spara filen som `script.sh` (Linux) eller `script.bat` (Windows) för AzCopy. 

These examples assume that your folder is named `myFolder`, your storage account name is `mystorageaccount` and your container name is `mycontainer`.

> [!NOTE]
> The Linux example appends a SAS token. You'll need to provide one in your command. The current version of AzCopy V10 doesn't support Azure AD authorization in cron jobs.

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

    azcopy sync "/mnt/myfiles" "https://mystorageaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-05-30T06:57:40Z&st=2019-05-29T22:57:40Z&spr=https&sig=BXHippZxxx54hQn%2F4tBY%2BE2JHGCTRv52445rtoyqgFBUo%3D" --recursive=true

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

This example assumes that your script is located in the root drive of your computer, but your script can be anywhere that you want.

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

For more information about AzCopy, see any of these articles:

* [Get started with AzCopy](storage-use-azcopy-v10.md)

* [Transfer data with AzCopy and blob storage](storage-use-azcopy-blobs.md)

* [Transfer data with AzCopy and file storage](storage-use-azcopy-files.md)

* [Transfer data with AzCopy and Amazon S3 buckets](storage-use-azcopy-s3.md)
 
* [Configure, optimize, and troubleshoot AzCopy](storage-use-azcopy-configure.md)
