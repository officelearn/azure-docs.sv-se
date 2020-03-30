---
title: 'PowerShell-skript: Acceptera inbjudan från en Azure Data Share | Microsoft-dokument'
description: Det här PowerShell-skriptet accepterar inbjudningar från en befintlig dataresurs.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 246e3550650dfd458b4aeecda3b5b7733e49f017
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "70307339"
---
# <a name="use-powershell-to-accept-a-data-share-invitation"></a>Använda PowerShell för att acceptera en inbjudan till datadelning

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
| [Få-AzDataShareInvitation](/powershell/module/az.datashare/get-azdatashareinvitation?view=azps-2.6.0) | Hämta och lista skickade inbjudningar till datadelning. |
| [Ny-AzDataShareAbonnemang](/powershell/module/az.datashare/get-azdatasharesubscription?view=azps-2.6.0) | Skapa en prenumeration på datadelning. |
|||

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](https://docs.microsoft.com/powershell/).

Ytterligare Azure Data Share PowerShell-skriptexempel finns i [Azure Data Share PowerShell-exemplen](../../samples-powershell.md).

