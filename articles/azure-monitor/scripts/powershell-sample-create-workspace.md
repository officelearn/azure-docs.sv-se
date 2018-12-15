---
title: Azure PowerShell-skriptexempel – Skapa en Log Analytics-arbetsyta | Microsoft Docs
description: Azure PowerShell-skriptexempel – Skapa en Log Analytics-arbetsyta för att
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: log-analytics
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2017
ms.author: magoedte
ms.openlocfilehash: 5ad04c52da4709a7097ff7915d7af7404d6725eb
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53110444"
---
# <a name="create-a-log-analytics-workspace-with-powershell"></a>Skapa en Log Analytics-arbetsyta med PowerShell

Med det här skriptet kommer du igång snabbt med en Azure Log Analytics-arbetsyta, vilket är nödvändigt om du vill börja samla in, analysera och vidta åtgärder på data.  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/log-analytics/log-analytics-create-new-resource/log-analytics-create-new-resource.ps1 "Create new Log Analytics workspace")]

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon för att skapa en ny Log Analytics-arbetsyta i din prenumeration. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [Get-AzureRmOperationalInsightsWorkspace](/powershell/module/azurerm.operationalinsights/get-azurermoperationalinsightsworkspace) | Hämtar information om en befintlig arbetsyta. |
| [New-AzureRmOperationalInsightsWorkspace](/powershell/module/azurerm.operationalinsights/new-azurermoperationalinsightsworkspace) | Skapar en arbetsyta i den angivna resursgruppen och platsen. |


## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](/powershell/azure/overview).

