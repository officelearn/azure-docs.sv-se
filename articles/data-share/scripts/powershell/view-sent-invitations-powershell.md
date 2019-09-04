---
title: 'PowerShell-skript: Visa en lista över Azure Data Share-inbjudningar som skickas till en konsument | Microsoft Docs'
description: Det här PowerShell-skriptet accepterar inbjudningar från en befintlig data resurs.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 1304b478bd07ed61293b668badee56338a9cab5a
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2019
ms.locfileid: "70242910"
---
# <a name="use-powershell-to-get-a-data-share-invitation"></a>Använd PowerShell för att hämta en inbjudan till data resursen

Det här PowerShell-skriptet hämtar inbjudningar som skickas till en konsument.

## <a name="sample-script"></a>Exempelskript
```powershell
# Set variables with your own values
$invitationId = "<invitationId>"
$location = "<location>"

#List invitations sent to a consumer
Get-AzDataShareInvitation

#Get a specific invitation sent to a consumer
Get-AzDataShareInvitation -location -invitationId 

```

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon: 

| Kommando | Anteckningar |
|---|---|
| [Get-AzDataShareInvitation](/powershell/module/az.resources/get-azdatashareinvitation) | Hämta och visa en lista över inbjudningar till skickade data resurser. |
|||

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](https://docs.microsoft.com/powershell/).

Ytterligare exempel för Azure Data Share PowerShell-skript finns i [PowerShell-exemplen för Azure Data Share](../../samples-powershell.md).
