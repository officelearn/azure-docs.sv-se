---
title: 'PowerShell-skript: skapa en ny Azure-Dataresurs | Microsoft Docs'
description: Det här PowerShell-skriptet skapar en ny data resurs i ett befintligt data resurs konto.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: c76dfea83d0f736dbd48239cc3be3496a88ec8a2
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2019
ms.locfileid: "70243001"
---
# <a name="use-powershell-to-create-a-data-share-in-azure"></a>Använd PowerShell för att skapa en data resurs i Azure

Det här PowerShell-skriptet skapar en ny data resurs i ett befintligt data resurs konto.

## <a name="sample-script"></a>Exempelskript

```powershell

# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"

# Create a new Azure Data Share
New-AzDataShare -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -Name $dataShareName

```


## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon: 

| Kommando | Anteckningar |
|---|---|
| [New-AzDataShare](/powershell/module/az.resources/new-azdatashare) | Skapar en data resurs. |
|||

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](https://docs.microsoft.com/powershell/).

Ytterligare exempel för Azure Data Share PowerShell-skript finns i [PowerShell-exemplen för Azure Data Share](../../samples-powershell.md).
