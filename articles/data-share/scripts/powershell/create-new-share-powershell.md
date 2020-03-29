---
title: 'PowerShell-skript: skapa en ny Azure Data Share | Microsoft-dokument'
description: Det här PowerShell-skriptet skapar en ny dataresurs i ett befintligt datadelningskonto.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 54d5dc35cf230b62cbe5f8c3cb4a1dc81eccf1e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "70307245"
---
# <a name="use-powershell-to-create-a-data-share-in-azure"></a>Använda PowerShell för att skapa en dataresurs i Azure

Det här PowerShell-skriptet skapar en ny dataresurs i ett befintligt datadelningskonto.

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
| [Nya-AzDataShare](/powershell/module/az.datashare/new-azdatashare?view=azps-2.6.0) | Skapar en dataresurs. |
|||

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](https://docs.microsoft.com/powershell/).

Ytterligare Azure Data Share PowerShell-skriptexempel finns i [Azure Data Share PowerShell-exemplen](../../samples-powershell.md).
