---
title: 'PowerShell-skript: Lista befintliga resurser i Azure Data Share | Microsoft-dokument'
description: Det här PowerShell-skriptet listar och visar information om resurser.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 6314bd348c22c901001b88eda6875181a2f69df4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "70307130"
---
# <a name="use-powershell-to-view-the-details-of-a-sent-data-share"></a>Använda PowerShell för att visa information om en skickad dataresurs

Det här PowerShell-skriptet listar dataresurser från ett befintligt konto och hämtar information om en viss resurs.


## <a name="sample-script"></a>Exempelskript

```powershell

# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"

#Lists all data shares within an account
Get-AzDataShare -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName

#Gets details of a specific data share
Get-AzDataShare -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -Name $dataShareName

```


## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon: 

| Kommando | Anteckningar |
|---|---|
| [Hämta-AzDataShare](/powershell/module/az.datashare/get-azdatashare?view=azps-2.6.0) | Hämtar och listar aktier i ett konto. |
|||

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](https://docs.microsoft.com/powershell/).

Ytterligare Azure Data Share PowerShell-skriptexempel finns i [Azure Data Share PowerShell-exemplen](../../samples-powershell.md).
