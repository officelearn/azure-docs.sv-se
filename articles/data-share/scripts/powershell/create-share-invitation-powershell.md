---
title: 'PowerShell-skript: Skapa en Azure Data Share-inbjudan | Microsoft Docs'
description: Det här PowerShell-skriptet skickar en inbjudan till data resursen.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: b1150d3a60e4ea7c22de5cfe7f9ea4a584c315d9
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2019
ms.locfileid: "70242975"
---
# <a name="use-powershell-to-monitor-the-usage-of-a-sent-data-share"></a>Använd PowerShell för att övervaka användningen av en data resurs som skickats

Det här PowerShell-skriptet skapar en inbjudan till data resursen.

## <a name="sample-script"></a>Exempelskript


```powershell
# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"
$targetEmail = "<Target email>"

# Send a data share invitation
New-AzDataShareInvitation -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareName $dataShareName -Name $dataShareName -TargetEmail $targetEmail

```


## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon: 

| Kommando | Anteckningar |
|---|---|
| [New-AzDataShareInvitation](/powershell/module/az.resources/get-azdatasharesynchronizationdetails) | Skapa en inbjudan till data delning. |
|||

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](https://docs.microsoft.com/powershell/).

Ytterligare exempel för Azure Data Share PowerShell-skript finns i [PowerShell-exemplen för Azure Data Share](../../samples-powershell.md).
