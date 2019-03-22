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
ms.openlocfilehash: 4dc98fc3912f9d9c659ecfcaa3df253d30bfcc78
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2019
ms.locfileid: "57306977"
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

