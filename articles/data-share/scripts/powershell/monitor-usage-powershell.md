---
title: 'PowerShell-skript: Övervaka användningen av en Azure-Dataresurs | Microsoft Docs'
description: Det här PowerShell-skriptet hämtar användnings statistik för en data resurs som skickats.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 9cc7cbe8a1f523a50c91125802804e564b48a164
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2019
ms.locfileid: "70242962"
---
# <a name="use-powershell-to-monitor-the-usage-of-a-sent-data-share"></a>Använd PowerShell för att övervaka användningen av en data resurs som skickats

Det här PowerShell-skriptet övervakar data användningen genom att ange synkroniseringarna för en data resurs som skickats och hämta information om en speciell synkronisering.

## <a name="sample-script"></a>Exempelskript


```powershell
# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"
$synchronizationId = "<Azure synchronization id>"

# List synchronizations on a share
Get-AzDataShareSynchronization -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareName $dataShareName

#Get details of a specific synchronization
Get-AzDataShareSynchronizationDetails -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareName $dataShareName -SynchronizationId $synchronizationId
```


## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon: 

| Kommando | Anteckningar |
|---|---|
| [Get-AzDataShareSynchronization](/powershell/module/az.resources/get-azdatasharesynchronizationdetails) | Lista synkroniseringar på en resurs. |
| [Get-AzDataShareSynchronizationDetails](/powershell/module/az.resources/get-azdatasharesynchronizationdetails) | Hämtar synkroniseringsinformation för en resurs-synkronisering. |
|||

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](https://docs.microsoft.com/powershell/).

Ytterligare exempel för Azure Data Share PowerShell-skript finns i [PowerShell-exemplen för Azure Data Share](../../samples-powershell.md).
