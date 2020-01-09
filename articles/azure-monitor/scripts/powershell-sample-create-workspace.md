---
title: Azure PowerShell-skriptexempel – Skapa en Log Analytics-arbetsyta | Microsoft Docs
description: Azure PowerShell-skriptexempel – Skapa en Log Analytics-arbetsyta för att
ms.service: azure-monitor
ms.subservice: logs
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 09/07/2017
ms.openlocfilehash: 242ba4d8487e25ae0d6c5fd7dc1525942deb2a64
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75363106"
---
# <a name="create-a-log-analytics-workspace-with-powershell"></a>Skapa en Log Analytics-arbetsyta med PowerShell

Med det här skriptet kommer du igång snabbt med en Azure Log Analytics-arbetsyta, vilket är nödvändigt om du vill börja samla in, analysera och vidta åtgärder på data.  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/log-analytics/log-analytics-create-new-resource/log-analytics-create-new-resource.ps1 "Create new Log Analytics workspace")]

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon för att skapa en ny Log Analytics-arbetsyta i din prenumeration. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [Get-AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/get-azoperationalinsightsworkspace) | Hämtar information om en befintlig arbetsyta. |
| [New-AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/new-azoperationalinsightsworkspace) | Skapar en arbetsyta i den angivna resursgruppen och platsen. |


## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](/powershell/azure/overview).

