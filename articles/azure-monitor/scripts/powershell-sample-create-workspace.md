---
title: Skapa logganalysarbetsyta - Azure PowerShell
description: Azure PowerShell-skriptexempel – Skapa en Log Analytics-arbetsyta för att
ms.subservice: logs
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 09/07/2017
ms.openlocfilehash: 62b02de5d1c08f6047052d71e3be420cceb1c5c0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "80054636"
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
| [Hämta-AzOperationalInsightsArbetsområde](/powershell/module/az.operationalinsights/get-azoperationalinsightsworkspace) | Hämtar information om en befintlig arbetsyta. |
| [Nya-AzOperationalInsightsArbetsområde](/powershell/module/az.operationalinsights/new-azoperationalinsightsworkspace) | Skapar en arbetsyta i den angivna resursgruppen och platsen. |


## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](/powershell/azure/overview).

