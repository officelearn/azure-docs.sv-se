---
title: 'PowerShell-skript: Lista inbjudningar till Azure Data Share som skickas till en konsument | Microsoft-dokument'
description: Det här PowerShell-skriptet accepterar inbjudningar från en befintlig dataresurs.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 157b844afd9d59b6c03c7ae8585bc2182370d3a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "70307157"
---
# <a name="use-powershell-to-get-a-data-share-invitation"></a>Använda PowerShell för att hämta en inbjudan till datadelning

Det här PowerShell-skriptet får inbjudningar skickade till en konsument.

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
| [Få-AzDataShareInvitation](/powershell/module/az.datashare/get-azdatashareinvitation?view=azps-2.6.0) | Hämta och lista skickade inbjudningar till datadelning. |
|||

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](https://docs.microsoft.com/powershell/).

Ytterligare Azure Data Share PowerShell-skriptexempel finns i [Azure Data Share PowerShell-exemplen](../../samples-powershell.md).
