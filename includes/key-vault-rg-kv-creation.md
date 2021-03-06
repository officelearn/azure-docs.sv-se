---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: ccb69b4f0dc6090cc96849b09f7830c05aa77b73
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96027343"
---
I den här snabb starten används ett i förväg skapade Azure Key Vault. Du kan skapa ett nyckel valv genom att följa stegen i snabb starten för [Azure CLI](../articles/key-vault/general/quick-create-cli.md), [Azure PowerShell snabb start](../articles/key-vault/general/quick-create-powershell.md)eller [Azure Portal snabb start](../articles/key-vault/general/quick-create-portal.md). 

Alternativt kan du bara köra kommandona för Azure CLI eller Azure PowerShell nedan.

> [!Important]
> Varje nyckel valv måste ha ett unikt namn. Ersätt <ditt-unika-nyckel-valv> med namnet på nyckel valvet i följande exempel.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name "<your-unique-keyvault-name>" -g "myResourceGroup"
```

```azurepowershell
New-AzResourceGroup -Name myResourceGroup -Location EastUS

New-AzKeyVault -Name "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup" -Location "EastUS"
```