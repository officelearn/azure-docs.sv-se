---
title: 'PowerShell-skript: Skapa nytt Azure Data Share-konto | Microsoft Docs'
description: Det här PowerShell-skriptet skapar ett nytt data resurs konto.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: cc4e157856a05290da82aca686e2268a5ed2b3ce
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2019
ms.locfileid: "70243027"
---
# <a name="use-powershell-to-create-a-data-share-account-in-azure"></a>Använd PowerShell för att skapa ett data resurs konto i Azure

Det här PowerShell-skriptet skapar ett nytt data resurs konto. 

## <a name="sample-script"></a>Exempelskript

```powershell
# Set variables with your own values
$resourceGroupName = "<Resource group name"
$location = "<Location>"
$dataShareAccountName = "<Data share account name>"

# Create a new Azure Data Share account
New-AzDataShareAccount -ResourceGroupName $resourceGroupName -Name $dataShareAccountName -Location $location
```

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon: 

| Kommando | Anteckningar |
|---|---|
| [New-AzDataShareAccount](/powershell/module/az.resources/new-azdatashareaccount) | Skapar ett data resurs konto. |
|||

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](https://docs.microsoft.com/powershell/).

Ytterligare exempel för Azure Data Share PowerShell-skript finns i [PowerShell-exemplen för Azure Data Share](../../samples-powershell.md).
