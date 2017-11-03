---
title: "Azure PowerShell script exempel – ha en hanterad resursgrupp och ändra storlek på virtuella datorer | Microsoft Docs"
description: "Azure PowerShell skriptexempel - ha en hanterad resursgrupp och ändra storlek på virtuella datorer"
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: poweshell
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/27/2017
ms.author: tomfitz
ms.openlocfilehash: f549f26cb3f9fdb2d805d2efb2c0e1706abe3edb
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2017
---
# <a name="get-resources-in-a-managed-resource-group-and-resize-vms-with-powershell"></a>Hämta resurser i en hanterad resursgrupp och ändra storlek på virtuella datorer med PowerShell

Detta skript hämtar resurser från en hanterad resursgruppen och ändrar storlek på virtuella datorer i resursgruppen.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/managed-applications/get-application/get-application.ps1 "Get application")]


## <a name="script-explanation"></a>Skriptet förklaring

Det här skriptet använder följande kommandon för att distribuera det hanterade programmet. Varje kommando i tabellen länkar till kommando-specifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [Get-AzureRmManagedApplication](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermmanagedapplication) | Lista över hanterade program. Ange resursgruppens namn att fokusera resultaten. |
| [Get-AzureRmResource](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermresource) | Lista över resurser. Ange en resursgrupp och en resurstyp om du vill begränsa resultatet. |
| [Uppdatera AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvm) | Uppdatera storleken på en virtuell dator. |


## <a name="next-steps"></a>Nästa steg

* En introduktion till hanterade program, se [översikt över Azure Managed Application](../overview.md).
* Mer information om PowerShell finns [Azure PowerShell dokumentationen](https://docs.microsoft.com/powershell/azure/get-started-azureps).
