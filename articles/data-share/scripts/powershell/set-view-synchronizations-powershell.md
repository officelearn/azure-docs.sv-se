---
title: 'PowerShell-skript: Ange och visa azure datadelningsychronization-inställningar| Microsoft-dokument'
description: Det här PowerShell-skriptet anger och hämtar inställningar för resurssynkronisering.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: b8d01a383e816bfc73413d21e9cc8d51d326ab14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "70307186"
---
# <a name="use-powershell-to-monitor-the-usage-of-a-sent-data-share"></a>Använda PowerShell för att övervaka användningen av en skickad dataresurs

Det här PowerShell-skriptet anger och hämtar inställningar för resurssynkronisering.

## <a name="sample-script"></a>Exempelskript


```powershell
# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"
$settingName = "<Synchronization setting name>"
$recurrenceInterval = "<Synchronization recurrence interval>"
$synchronizationTime = "<Synchronization time>"

# Create a new synchronization setting
New-AzDataShareSynchronizationSetting -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareName $dataShareName -Name $settingName  -RecurrenceInterval $recurrenceInterval -SynchronizationTime $synchronizationTime

#List share synchronization settings
Get-AzDataShareSynchronizationSetting -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName  -ShareName $dataShareName 

#Get a specific share synchronization setting
Get-AzDataShareSynchronizationSetting -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName  -ShareName $dataShareName -Name $settingName  
```

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon: 

| Kommando | Anteckningar |
|---|---|
| [Ny-AzDataShareSynchronizationSetting](/powershell/module/az.datashare/new-azdatasharesynchronizationsetting?view=azps-2.6.0) | Skapa en resurssynkronisering. |
| [Hämta-AzDataShareSynchronizationSetting](/powershell/module/az.datashare/get-azdatasharesynchronizationsetting?view=azps-2.6.0) | Hämtar synkroniseringsinställningar för en resurssynkronisering. |
|||

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](https://docs.microsoft.com/powershell/).

Ytterligare Azure Data Share PowerShell-skriptexempel finns i [Azure Data Share PowerShell-exemplen](../../samples-powershell.md).
