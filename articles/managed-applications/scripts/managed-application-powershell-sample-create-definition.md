---
title: "Azure PowerShell-skript sample - skapa en definition för hanterade | Microsoft Docs"
description: "Azure PowerShell-skript sample - skapa en definition för hanterade program"
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
ms.openlocfilehash: ee721b7c68924f5e05fbdc4bc4b919837d4cc05b
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2017
---
# <a name="create-a-managed-application-definition-with-powershell"></a>Skapa en definition för hanterade med PowerShell

Det här skriptet publicerar en definition för hanterade program till en tjänstkatalog.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/managed-applications/create-definition/create-definition.ps1 "Create definition")]


## <a name="script-explanation"></a>Skriptet förklaring

Det här skriptet använder du följande kommando för att skapa definition för hanterade program. Varje kommando i tabellen länkar till kommando-specifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [Ny AzureRmManagedApplicationDefinition](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermmanagedapplicationdefinition) | Skapa en definition för hanterade program. Ange det paket som innehåller filerna som krävs. |


## <a name="next-steps"></a>Nästa steg

* En introduktion till hanterade program, se [översikt över Azure Managed Application](../overview.md).
* Mer information om PowerShell finns [Azure PowerShell dokumentationen](https://docs.microsoft.com/powershell/azure/get-started-azureps).
