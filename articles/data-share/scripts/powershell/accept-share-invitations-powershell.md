---
title: 'PowerShell-skript: Acceptera inbjudan från en Azure-Dataresurs | Microsoft Docs'
description: Det här PowerShell-skriptet accepterar inbjudningar från en befintlig data resurs.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 9ef35e0865a7c5bbfb4accc058a8d63bb6f1e80b
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2019
ms.locfileid: "70243079"
---
# <a name="use-powershell-to-accept-a-data-share-invitation"></a>Använd PowerShell för att acceptera en inbjudan till en data resurs

Det här PowerShell-skriptet accepterar inbjudningar som skickas till en konsument.

## <a name="sample-script"></a>Exempelskript
```powershell
#List invitations sent to a consumer
Get-AzDataShareInvitation

# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"
$invitationId = "<Invitation id>"

#Accept a specific invitation by creating a share subscription
New-AzDataShareSubscription -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -Name $dataShareName -InvitationId $invitationId

```

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon: 

| Kommando | Anteckningar |
|---|---|
| [Get-AzDataShareInvitation](/powershell/module/az.resources/get-azdatashareinvitation) | Hämta och visa en lista över inbjudningar till skickade data resurser. |
| [New-AzDataShareSubscription](/powershell/module/az.resources/get-azdatashareinvitation) | Skapa en data resurs prenumeration. |
|||

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](https://docs.microsoft.com/powershell/).

Ytterligare exempel för Azure Data Share PowerShell-skript finns i [PowerShell-exemplen för Azure Data Share](../../samples-powershell.md).

