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
ms.openlocfilehash: 7d4b7f49edca6c72681734f70dba3be8722edd91
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53269319"
---
# <a name="subscribe-to-events-for-an-azure-subscription-with-powershell"></a>Prenumerera på händelser för en Azure-prenumeration med PowerShell

Det här skriptet skapar en Event Grid-prenumeration på händelser för en Azure-prenumeration.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Förhandsversionen av exempelskriptet kräver Event Grid-modulen. Installera genom att köra `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

## <a name="sample-script---stable"></a>Exempelskript – stabilt

[!code-powershell[main](../../../powershell_scripts/event-grid/subscribe-to-azure-subscription/subscribe-to-azure-subscription.ps1 "Subscribe to Azure subscription")]

## <a name="sample-script---preview-module"></a>Exempelskript – modul för förhandsversion

[!code-powershell[main](../../../powershell_scripts/event-grid/subscribe-to-azure-subscription-preview/subscribe-to-azure-subscription-preview.ps1 "Subscribe to Azure subscription")]

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon för att skapa händelseprenumerationen. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [New-AzureRmEventGridSubscription](https://docs.microsoft.com/powershell/module/azurerm.eventgrid/new-azurermeventgridsubscription) | Skapa en Event Grid-prenumeration. |

## <a name="next-steps"></a>Nästa steg

* En introduktion till hanterade program finns i [översikten över Azure Managed Application](../overview.md).
* Mer information om PowerShell finns i [Azure PowerShell-dokumentationen](https://docs.microsoft.com/powershell/azure/get-started-azureps).
