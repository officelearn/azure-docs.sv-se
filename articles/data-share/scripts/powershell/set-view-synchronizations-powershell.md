---
title: 'PowerShell-skript: Ange och Visa synkroniseringsaktivitet-inställningar för Azure Data resurs | Microsoft Docs'
description: Det här PowerShell-skriptet anger och hämtar inställningarna för synkronisering av resurser.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 14f1925649a665b0e63ac37ef9ec7e3469042e3e
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2019
ms.locfileid: "70242949"
---
# <a name="use-powershell-to-monitor-the-usage-of-a-sent-data-share"></a>Använd PowerShell för att övervaka användningen av en data resurs som skickats

Det här PowerShell-skriptet anger och hämtar inställningarna för synkronisering av resurser.

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
| [New-AzDataShareSynchronizationSetting](/powershell/module/az.resources/new-azdatasharesynchronizationsettings) | Skapa en resurs-synkronisering. |
| [Get-AzDataShareSynchronizationSetting](/powershell/module/az.resources/get-azdatasharesynchronizationsetting) | Hämtar synkroniseringsinställningar för en resurs-synkronisering. |
|||

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](https://docs.microsoft.com/powershell/).

Ytterligare exempel för Azure Data Share PowerShell-skript finns i [PowerShell-exemplen för Azure Data Share](../../samples-powershell.md).
