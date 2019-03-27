---
title: Exempel på Azure CLI-skript – Migrera blobar över lagringskonton med hjälp av AzCopy på Windows | Microsoft Docs
description: Kopierar med hjälp av AzCopy blob-innehållet från ett Azure Storage-konto till ett annat.
services: storage
documentationcenter: na
author: roygara
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/01/2018
ms.author: rogarana
ms.openlocfilehash: 88eb97a206bc75bc9635da90eb9d48818ceef0f0
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2019
ms.locfileid: "58487140"
---
# <a name="migrate-blobs-across-storage-accounts-using-azcopy-on-windows"></a>Migrera blobar över lagringskonton med hjälp av AzCopy på Windows

Det här exemplet kopierar alla blob-objekt från ett lagringskonto som användaren tillhandahåller till ett mållagringskonto som användaren tillhandahåller. 

Detta görs genom att använda `Get-AzStorageContainer`-kommandot, som visar alla containrar i ett lagringskonto. Exemplet skickar sedan AzCopy-kommandon som kopierar varje container från källans lagringskonto till målets lagringskonto. Om något fel uppstår försöker exemplet igen $retryTimes (standard är 3 och kan ändras med `-RetryTimes`-parametern). Om ett fel uppstår vid varje nytt försök kan användaren köra skriptet igen genom att ange exemplet med den container som senast kunde kopieras med hjälp av `-LastSuccessContainerName`-parametern. Exemplet fortsätter sedan kopiera containrar från den punkten.

Det här exemplet kräver Azure PowerShell Storage-modul version **0.7** eller senare. Du kan kontrollera den installerade versionen med hjälp av `Get-Module -ListAvailable Az.storage`. Om du behöver installera eller uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-Az-ps) (Installera Azure PowerShell-modul). 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Det här exemplet kräver även den senaste versionen av [AzCopy på Windows](https://aka.ms/downloadazcopy). Standardkatalogen för installation är `C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\`.

Det här exemplet använder källans lagringskontonamn och nyckel, målets lagringskontonamnet och nyckel och den fullständiga filsökvägen för AzCopy.exe (om det inte är installerat i standardkatalogen).

Följande är exempel på indata för det här exemplet:

Om AzCopy är installerat i standardkatalogen:
```powershell
srcStorageAccountName: ExampleSourceStorageAccountName
srcStorageAccountKey: ExampleSourceStorageAccountKey
DestStorageAccountName: ExampleTargetStorageAccountName
DestStorageAccountKey: ExampleTargetStorageAccountKey
```

Om AzCopy inte är installerat i standardkatalogen:

```Powershell
srcStorageAccountName: ExampleSourceStorageAccountName
srcStorageAccountKey: ExampleSourceStorageAccountKey
DestStorageAccountName: ExampleTargetStorageAccountName
DestStorageAccountKey: ExampleTargetStorageAccountKey
AzCopyPath: C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\AzCopy.exe
```

Om ett fel uppstod och exemplet måste köras igen från en viss container: 

`.\copyScript.ps1 -LastSuccessContainerName myContainerName`

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/storage/migrate-blobs-between-accounts/migrate-blobs-between-accounts.ps1 "Migrate blobs between storage accounts.")]

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon för att kopiera data från ett lagringskonto till ett annat. Varje post i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [Get-AzStorageContainer](/powershell/module/az.storage/Get-AzStorageContainer) | Returnerar de containrar som är kopplade till det här lagringskontot. |
| [New-AzStorageContext](/powershell/module/az.storage/New-AzStorageContext) | Skapar en Azure Storage-kontext. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](/powershell/azure/overview).

Ytterligare PowerShell-skriptexempel för lagring finns i [PowerShell-exempel för Azure Blob Storage](../blobs/storage-samples-blobs-powershell.md).
