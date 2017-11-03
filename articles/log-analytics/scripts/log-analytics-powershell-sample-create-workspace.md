---
title: Azure PowerShell-skript Sample - skapa en logganalys-arbetsytan | Microsoft Docs
description: Azure PowerShell-skript Sample - skapa en logganalys-arbetsytan till
services: log-analytics
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: tysonn
tags: 
ms.assetid: 
ms.service: log-analytics
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2017
ms.author: magoedte
ms.openlocfilehash: 30d036ae56acc3a798d2776f292243f65cbea43d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-log-analytics-workspace-with-powershell"></a>Skapa en logganalys-arbetsyta med PowerShell

Det här skriptet hjälper dig att komma igång snabbt med en Azure logganalys-arbetsyta krävs om du vill börja samla in, analysera och vidtar åtgärder på data.  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/log-analytics/log-analytics-create-new-resource/log-analytics-create-new-resource.ps1 "Create new Log Analytics workspace")]

## <a name="script-explanation"></a>Skriptet förklaring

Det här skriptet använder följande kommandon för att skapa en ny logganalys-arbetsyta i din prenumeration. Varje kommando i tabellen länkar till kommandot viss dokumentation.

| Kommando | Anteckningar |
|---|---|
| [Get-AzureRmOperationalInsightsWorkspace](/powershell/module/azurerm.operationalinsights/get-azurermoperationalinsightsworkspace) | Hämtar information om en befintlig arbetsyta. |
| [Ny AzureRmOperationalInsightsWorkspace](/powershell/module/azurerm.operationalinsights/new-azurermoperationalinsightsworkspace) | Skapar en arbetsyta i den angivna resursgrupp och plats. |


## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell-modulen finns [Azure PowerShell dokumentationen](/powershell/azure/overview).

