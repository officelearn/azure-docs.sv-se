---
title: "Så här använder du PowerShell för att hantera Azure File Storage | Microsoft Docs"
description: "Lär dig hur du använder PowerShell för att hantera Azure File Storage."
services: storage
documentationcenter: 
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/27/2017
ms.author: renash
ms.translationtype: HT
ms.sourcegitcommit: f76de4efe3d4328a37f86f986287092c808ea537
ms.openlocfilehash: 5d825c1139d49fb8d37c719d8e0ab8ba2da2ed8d
ms.contentlocale: sv-se
ms.lasthandoff: 07/10/2017

---
# <a name="how-to-use-powershell-to-manage-azure-file-storage"></a>Så här använder du PowerShell för att hantera Azure File Storage
Du kan skapa och hantera filresurser med hjälp av Azure PowerShell.

## <a name="install-the-powershell-cmdlets-for-azure-storage"></a>Installera PowerShell-cmdlets för Azure Storage
Innan du kan använda PowerShell laddar du ned och installerar Azure PowerShell-cmdlets. Information om installationsplatser och installationsanvisningar finns i [Installera och konfigurera Azure PowerShell](/powershell/azureps-cmdlets-docs).

> [!NOTE]
> Vi rekommenderar att du laddar ned och installerar eller uppgradera till den senaste Azure PowerShell-modulen.
> 
> 

Öppna ett Azure PowerShell-fönster genom att klicka på **Start** och skriva **Windows PowerShell**. Azure Powershell-modulen läses in i PowerShell-fönstret.

## <a name="create-a-context-for-your-storage-account-and-key"></a>Skapa en kontext för ditt lagringskonto och din nyckel
Skapa kontexten för lagringskontot. Kontexten innehåller lagringskontots namn och åtkomstnyckel. Anvisningar för hur du kopierar din kontonyckel från [Azure Portal](https://portal.azure.com) finns i [Visa och kopiera åtkomstnycklar för lagring](storage-create-storage-account.md#view-and-copy-storage-access-keys).

Ersätt `storage-account-name` och `storage-account-key` med lagringskontots namn och nyckel i följande exempel.

```powershell
# create a context for account and key
$ctx=New-AzureStorageContext storage-account-name storage-account-key
```

## <a name="create-a-new-file-share"></a>Skapa en ny filresurs
Skapa en ny resurs med namnet `logs`.

```powershell
# create a new share
$s = New-AzureStorageShare logs -Context $ctx
```

Nu har du en filresurs i File Storage. Härnäst ska vi lägga till en katalog och en fil.

> [!IMPORTANT]
> Namnet på filresursen får bara innehålla gemener. Mer information om hur du namnger filresurser och filer finns i [Namnge och referera till resurser, kataloger, filer och Metadata](https://msdn.microsoft.com/library/azure/dn167011.aspx).
> 
> 

## <a name="create-a-directory-in-the-file-share"></a>Skapa en katalog i filresursen
Skapa en katalog i resursen. I följande exempel heter katalogen `CustomLogs`.

```powershell
# create a directory in the share
New-AzureStorageDirectory -Share $s -Path CustomLogs
```

## <a name="upload-a-local-file-to-the-directory"></a>Ladda upp en lokal fil till katalogen
Nu ska du ladda upp en lokal fil till katalogen. I följande exempel laddar vi upp en fil från `C:\temp\Log1.txt`. Ändra sökvägen till filen så att den pekar på en giltig fil på den lokala datorn.

```powershell
# upload a local file to the new directory
Set-AzureStorageFileContent -Share $s -Source C:\temp\Log1.txt -Path CustomLogs
```

## <a name="list-the-files-in-the-directory"></a>Visa en lista med filerna i katalogen
Om du vill visa filen i katalogen kan du visa en lista med alla filer i katalogen. Det här kommandot returnerar filer och underkataloger (om det finns några) i katalogen CustomLogs.

```powershell
# list files in the new directory
Get-AzureStorageFile -Share $s -Path CustomLogs | Get-AzureStorageFile
```

Get-AzureStorageFile returnerar en lista över filer och kataloger för det katalogobjekt som du har angett. ”Get-AzureStorageFile -Share $s” returnerar en lista över filer och kataloger i rotkatalogen. Om du vill hämta en lista över filerna i en underkatalog måste du skicka underkatalogen till Get-AzureStorageFile. Detta händer: den första delen av kommandot fram till pipe returnerar en kataloginstans av underkatalogen CustomLogs. Därefter skickas detta till Get-AzureStorageFile, som returnerar filerna och katalogerna i CustomLogs.

## <a name="copy-files"></a>Kopiera filer
Från och med version 0.9.7 av Azure PowerShell kan du kopiera en fil till en annan fil, en fil till en blobb eller en blobb till en fil. Nedan ser du hur du utför dessa kopieringsåtgärder med PowerShell-cmdlets.

```powershell
# copy a file to the new directory
Start-AzureStorageFileCopy -SrcShareName srcshare -SrcFilePath srcdir/hello.txt -DestShareName destshare -DestFilePath destdir/hellocopy.txt -Context $srcCtx -DestContext $destCtx

# copy a blob to a file directory
Start-AzureStorageFileCopy -SrcContainerName srcctn -SrcBlobName hello2.txt -DestShareName hello -DestFilePath hellodir/hello2copy.txt -DestContext $ctx -Context $ctx
```
## <a name="next-steps"></a>Nästa steg
Mer information om Azure File Storage finns på följande länkar.

* [Vanliga frågor och svar](storage-files-faq.md)
* [Felsökning](storage-troubleshoot-file-connection-problems.md)
