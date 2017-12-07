---
title: "Azure snabbstart – Skapa ett lagringskonto med hjälp av PowerShell | Microsoft Docs"
description: "Lär dig snabbt att skapa ett nytt lagringskonto med hjälp av PowerShell"
services: storage
documentationcenter: 
author: tamram
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
ms.author: tamram
ms.openlocfilehash: b4b917adfb3644cca71b6696df005fbf9e295240
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/29/2017
---
# <a name="create-a-storage-account-using-powershell"></a>Skapa ett lagringskonto med PowerShell

Azure PowerShell-modulen används för att skapa och hantera Azure-resurser från PowerShell-kommandoraden eller i skript. I den här guiden beskrivs det hur du använder PowerShell för att skapa ett Azure Storage-konto. 

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

Den här snabbstarten kräver Azure PowerShell-modul version 3.6 eller senare. Kör `Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul).

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på Azure-prenumerationen med kommandot `Login-AzureRmAccount` och följ anvisningarna på skärmen.

```powershell
Login-AzureRmAccount
```

Om du inte vet vilken plats du vill använda kan du visa en lista med tillgängliga platser. Hitta den du vill använda i listan som visas. I det här exemplet används **eastus**. Lagra det i en variabel och använd variabeln så att du kan ändra den på en enda plats.

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

Det finns flera typer av lagringskonton, beroende på hur det ska användas och för vilken tjänst (Blob Service, File Service, Table Service eller Queue Service). Den här tabellen visar vilka möjligheter som finns.

|**Typ av lagringskonto**|**Allmän Standard**|**Allmän Premium**|**Blob Storage, frekvent och lågfrekvent åtkomstnivå**|
|-----|-----|-----|-----|
|**Tjänster som stöds**| Blob Service, File Service, Table Service, Queue Service | Blob Service | Blob Service|
|**Typer av blobbar som stöds**|Blockblobar, sidblobar och tilläggsblobar | Sidblobbar | Blockblobbar och tilläggsblobbar|

Använd [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount) att skapa ett allmänt standardlagringskonto som du kan använda för alla fyra tjänster. Ge lagringskontot namnet *contosomvcstandard* och konfigurera det så att lokalt redundant lagring och blobkryptering är aktiverat.

```powershell
New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name "contosomvcstandard" `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind Storage `
  -EnableEncryptionService Blob
```

## <a name="clean-up-resources"></a>Rensa resurser

När resursgruppen inte längre behövs kan du använda kommandot [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) för att ta bort resursgruppen och alla relaterade resurser. I det här fallet tar du då bort det lagringskonto som du skapade.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du skapat ett allmänt standardlagringskonto. Om du vill lära dig hur laddar upp och ned blobar på lagringskontot kan du fortsätta med snabbstarten om Blob Storage.
> [!div class="nextstepaction"]
> [Överföra objekt till och från Azure Blob Storage med hjälp av PowerShell](../blobs/storage-quickstart-blobs-powershell.md)