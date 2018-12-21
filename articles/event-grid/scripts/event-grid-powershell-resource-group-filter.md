---
title: Azure PowerShell-skriptexempel – Prenumerera på resursgrupp och filtrera efter resurs | Microsoft Docs
description: Azure PowerShell-skriptexempel – Prenumerera på resursgrupp och filtrera efter resurs
services: event-grid
documentationcenter: na
author: tfitzmac
ms.service: event-grid
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/10/2018
ms.author: tomfitz
ms.openlocfilehash: 21719ab4e6b999f262ff53adf31d855d6e1833b4
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53271410"
---
# <a name="subscribe-to-events-for-a-resource-group-and-filter-for-a-resource-with-powershell"></a>Prenumerera på händelser för en resursgrupp och filtrera efter en resurs med PowerShell

Det här skriptet skapar en Event Grid-prenumeration på händelser för en resursgrupp. Det använder ett filter för att endast hämta händelser för en specifik resurs i resursgruppen.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Förhandsversionen av exempelskriptet kräver Event Grid-modulen. Installera genom att köra `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

## <a name="sample-script---stable"></a>Exempelskript – stabilt

[!code-powershell[main](../../../powershell_scripts/event-grid/filter-events/filter-events.ps1 "Filter events")]

## <a name="sample-script---preview-module"></a>Exempelskript – modul för förhandsversion

[!code-powershell[main](../../../powershell_scripts/event-grid/filter-events-preview/filter-events-preview.ps1 "Filter events")]


## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon för att skapa händelseprenumerationen. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [New-AzureRmEventGridSubscription](https://docs.microsoft.com/powershell/module/azurerm.eventgrid/new-azurermeventgridsubscription) | Skapa en Event Grid-prenumeration. |

## <a name="next-steps"></a>Nästa steg

* En introduktion till hanterade program finns i [översikten över Azure Managed Application](../overview.md).
* Mer information om PowerShell finns i [Azure PowerShell-dokumentationen](https://docs.microsoft.com/powershell/azure/get-started-azureps).
