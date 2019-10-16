---
title: Skriptexempel för Azure PowerShell – Hämta en hanterad resursgrupp och ändra storleken på virtuella datorer | Microsoft Docs
description: Innehåller Azure PowerShell exempel skript som hämtar en hanterad resurs grupp för ett Azure-hanterat program. Skriptet ändrar storlek på virtuella datorer.
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/27/2017
ms.author: tomfitz
ms.openlocfilehash: f36ed42e0b7f8e4d1e5b092d2a14fd0cd8178ee0
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2019
ms.locfileid: "72330125"
---
# <a name="get-resources-in-a-managed-resource-group-and-resize-vms-with-powershell"></a>Hämta resurser från en hanterad resursgrupp och ändra storleken på virtuella datorer med PowerShell

Detta skript hämtar resurser från en hanterad resursgrupp och ändrar storlek på de virtuella datorerna i resursgruppen.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/managed-applications/get-application/get-application.ps1 "Get application")]


## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon för att distribuera det hanterade programmet. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [Get-AzManagedApplication](https://docs.microsoft.com/powershell/module/az.resources/get-azmanagedapplication) | Visar en lista med hanterade program. Ange resursgruppens namn för att förfina resultatet. |
| [Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource) | Visar resurserna. Ange en resursgrupp och en resurstyp för att förfina resultatet. |
| [Update-AzVM](https://docs.microsoft.com/powershell/module/az.compute/update-azvm) | Uppdatera storleken på en virtuell dator. |


## <a name="next-steps"></a>Nästa steg

* En introduktion till hanterade program finns i [översikten över Azure Managed Application](../overview.md).
* Mer information om PowerShell finns i [Azure PowerShell-dokumentationen](https://docs.microsoft.com/powershell/azure/get-started-azureps).
