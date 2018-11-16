---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 027b370d2497822dcbd6f3958556357957f9e8f5
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/16/2018
ms.locfileid: "51817883"
---
## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på din Azure-prenumeration med kommandot `Connect-AzureRmAccount` och följ anvisningarna på skärmen.

```powershell
Connect-AzureRmAccount
```

Om du inte vet vilken plats du vill använda kan du visa en lista med tillgängliga platser. Visa listan över platser med hjälp av följande kodexempel och hitta det du vill använda. I det här exemplet används **eastus**. Lagra platsen i en variabel och använd variabeln så att du kan ändra den på en enda plats.

```powershell
Get-AzureRmLocation | select Location 
$location = "eastus"
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en Azure-resursgrupp med [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. 

```powershell
$resourceGroup = "myResourceGroup"
New-AzureRmResourceGroup -Name $resourceGroup -Location $location 
```

## <a name="create-a-storage-account"></a>skapar ett lagringskonto

Skapa ett allmänt standardlagringskonto med LRS-replikering med hjälp av [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount). Hämta sedan lagringskontokontexten som definierar det lagringskonto som du vill använda. När du arbetar med lagringskonton refererar du till kontexten i stället för att ange autentiseringsuppgifterna flera gånger. Använd följande exempel för att skapa ett lagringskonto med namnet *mystorageaccount* med lokalt redundant lagring (LRS) och blobkryptering (aktiverat som standard).

```powershell
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name "mystorageaccount" `
  -SkuName Standard_LRS `
  -Location $location `
  -Kind Storage

$ctx = $storageAccount.Context
```
