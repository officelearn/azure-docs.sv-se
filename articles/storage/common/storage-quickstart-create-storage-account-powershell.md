---
title: "Azure Quickstart - skapa ett lagringskonto med hjälp av PowerShell | Microsoft Docs"
description: "Lär dig snabbt skapa ett nytt lagringskonto med PowerShell"
services: storage
documentationcenter: 
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 06/29/2017
ms.author: robinsh
ms.openlocfilehash: c9175cce0cb93e73009fb8d751e54f631603d482
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-storage-account-using-powershell"></a>Skapa ett lagringskonto med hjälp av PowerShell

Azure PowerShell-modulen används för att skapa och hantera Azure-resurser från PowerShell-kommandoraden eller i skript. Den här guiden information som använder PowerShell för att skapa ett Azure Storage-konto. 

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

Den här snabbstarten kräver Azure PowerShell-modul version 3.6 eller senare. Kör `Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul).

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på Azure-prenumerationen med kommandot `Login-AzureRmAccount` och följ anvisningarna på skärmen.

```powershell
Login-AzureRmAccount
```

Om du inte vet vilken plats som du vill använda, kan du visa tillgängliga platser. När listan visas hitta det du vill använda. Det här exemplet används **eastus**. Lagra den i en variabel och Använd variabeln så att du kan ändra den på en plats.

```powershell
Get-AzureRmLocation | select Location 
$location = "eastus"
```

## <a name="create-resource-group"></a>Skapa resursgrupp

Skapa en Azure-resursgrupp med [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). En resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras. 

```powershell
# put resource group in a variable so you can use the same group name going forward
#   without hardcoding it repeatedly
$resourceGroup = "contoso-storage-accounts"
New-AzureRmResourceGroup -Name $resourceGroup -Location $location 
```

## <a name="create-a-general-purpose-standard-storage-account"></a>Skapa ett allmänt standardlagringskonto

Det finns flera typer av lagringskonton, beroende på hur den ska användas och vilken tjänst (blobbar, filer, tabeller eller köer). Den här tabellen visar möjligheterna.

|**Typ av lagringskonto**|**Allmän Standard**|**Allmän Premium**|**Blob Storage, frekvent och lågfrekvent åtkomstnivå**|
|-----|-----|-----|-----|
|**Tjänster som stöds**| BLOB-fil, tabell, Queue-tjänster | Blob Service | Blob Service|
|**Typer av blobbar som stöds**|Blockblobbar, sidblobbar, tilläggsblobbar | Sidblobbar | Blockblobbar och tilläggsblobbar|

Använd [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount) att skapa ett allmänt standardlagringskonto som du kan använda för alla fyra tjänster. Namnge lagringskontot *contosomvcstandard*, och konfigurera den så att den har lokalt Redundant lagring och blob Aktivera kryptering.

```powershell
New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name "contosomvcstandard" `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind Storage `
  -EnableEncryptionService Blob
```

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behövs kan du använda den [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) kommandot för att ta bort resursgruppen och alla relaterade resurser. I det här fallet den tar bort lagringskontot som du skapade.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Nästa steg

Du har skapat en generell standardlagringskonto i den här snabbstartsguide. Om du vill lära dig mer om att ladda upp och ladda ned blobbar med ditt lagringskonto, även i fortsättningen Blob storage-Snabbstart.
> [!div class="nextstepaction"]
> [Överför objekt till/från Azure Blob storage med hjälp av PowerShell](../blobs/storage-quickstart-blobs-powershell.md)