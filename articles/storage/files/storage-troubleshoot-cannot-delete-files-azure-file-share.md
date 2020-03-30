---
title: Azure-filresurs – det gick inte att ta bort filer från en Azure-filresurs
description: Identifiera och felsöka felet att ta bort filer från Azure File Share.
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
manager: dcscontentpm
ms.date: 10/25/2019
ms.service: storage
ms.subservice: common
services: storage
tags: ''
ms.openlocfilehash: d3a3763a8964810626bcdc47da230a9ee406f1f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74196485"
---
# <a name="azure-file-share--failed-to-delete-files-from-azure-file-share"></a>Azure-filresurs – det gick inte att ta bort filer från en Azure-filresurs

Det gick inte att ta bort filer från Azure File Share kan ha flera symptom:

**Symptom 1:**

Det gick inte att ta bort en fil i azure-filresursen på grund av ett av de två problemen nedan:

* Filen som är markerad för borttagning
* Den angivna resursen kan användas av en SMB-klient

**Symptom 2:**

Det finns inte tillräckligt med kvot för att bearbeta det här kommandot

## <a name="cause"></a>Orsak

Fel 1816 inträffar när du når den övre gränsen för samtidiga öppna referenser som tillåts för en fil på datorn där filresursen monteras. Mer information finns i [checklistan för Azure Storage-prestanda och skalbarhet](https://docs.microsoft.com/azure/storage/blobs/storage-performance-checklist).

## <a name="resolution"></a>Lösning

Minska antalet samtidiga öppna handtag genom att stänga vissa handtag.

## <a name="prerequisite"></a>Krav

### <a name="install-the-latest-azure-powershell-module"></a>Installera den senaste Azure PowerShell-modulen

* [Installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps)

### <a name="connect-to-azure"></a>Anslut till Azure:

```
# Connect-AzAccount
```

### <a name="select-the-subscription-of-the-target-storage-account"></a>Välj prenumeration på mållagringskontot:

```
# Select-AzSubscription -subscriptionid "SubscriptionID"
```

### <a name="create-context-for-the-target-storage-account"></a>Skapa kontext för mållagringskontot:

```
$Context = New-AzStorageContext -StorageAccountName "StorageAccountName" -StorageAccountKey "StorageAccessKey"
```

### <a name="get-the-current-open-handles-of-the-file-share"></a>Hämta de aktuella öppna handtagen för filresursen:

```
# Get-AzStorageFileHandle -Context $Context -ShareName "FileShareName" -Recursive
```

## <a name="example-result"></a>Exempel på resultat:

|HandleId (Handtag)|Sökväg|ClientIp|ClientPort (klientport)|OpenTime (OpenTime)|LastReconnectTime|Fileid|ParentId (svenska)|Sessionid|
|---|---|---|---|---|---|---|---|---|
|259101229083|---|10.222.10.123|62758|2019-10-05|12:16:50Z|0|0|9507758546259807489|
|259101229131|---|10.222.10.123|62758|2019-10-05|12:36:20Z|0|0|9507758546259807489|
|259101229137|---|10.222.10.123|62758|2019-10-05|12:36:53Z|0|0|9507758546259807489|
|259101229136|Ny mapp/test.zip|10.222.10.123|62758|2019-10-05|12:36:29Z|13835132822072852480|9223446803645464576|9507758546259807489|
|259101229135|test.zip (påtr)-blixtar|37.222.22.143|62758|2019-10-05|12:36:24Z|11529250230440558592|0|9507758546259807489|

### <a name="close-an-open-handle"></a>Stäng ett öppet handtag:

Om du vill stänga ett öppet handtag använder du följande kommando:

```
# Close-AzStorageFileHandle -Context $Context -ShareName "FileShareName" -Path 'New folder/test.zip' -CloseAll
```

## <a name="next-steps"></a>Nästa steg

* [Felsöka Azure-filer i Windows](storage-troubleshoot-windows-file-connection-problems.md)
* [Felsöka Azure-filer i Linux](storage-troubleshoot-linux-file-connection-problems.md)
* [Felsöka Azure File Sync](storage-sync-files-troubleshoot.md)