---
title: Skriptexempel för Azure PowerShell – Prenumerera på en Azure-prenumeration| Microsoft Docs
description: Skriptexempel för Azure PowerShell – Prenumerera på en Azure-prenumeration
services: event-grid
documentationcenter: na
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/10/2018
ms.author: tomfitz
ms.openlocfilehash: 3d3d7a864bf6941dfb0bf7496b291639e7e5ea6d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58180559"
---
# <a name="subscribe-to-events-for-an-azure-subscription-with-powershell"></a>Prenumerera på händelser för en Azure-prenumeration med PowerShell

Det här skriptet skapar en Event Grid-prenumeration på händelser för en Azure-prenumeration.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Förhandsversionen av exempelskriptet kräver Event Grid-modulen. Installera genom att köra `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

## <a name="sample-script---stable"></a>Exempelskript – stabilt

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/event-grid/subscribe-to-azure-subscription/subscribe-to-azure-subscription.ps1 "Subscribe to Azure subscription")]

## <a name="sample-script---preview-module"></a>Exempelskript – modul för förhandsversion

[!INCLUDE [requires-azurerm](../../../includes/requires-azurerm.md)]

[!code-powershell[main](../../../powershell_scripts/event-grid/subscribe-to-azure-subscription-preview/subscribe-to-azure-subscription-preview.ps1 "Subscribe to Azure subscription")]

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon för att skapa händelseprenumerationen. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [New-AzEventGridSubscription](https://docs.microsoft.com/powershell/module/az.eventgrid/new-azeventgridsubscription) | Skapa en Event Grid-prenumeration. |

## <a name="next-steps"></a>Nästa steg

* En introduktion till hanterade program finns i [översikten över Azure Managed Application](../overview.md).
* Mer information om PowerShell finns i [Azure PowerShell-dokumentationen](https://docs.microsoft.com/powershell/azure/get-started-azureps).
