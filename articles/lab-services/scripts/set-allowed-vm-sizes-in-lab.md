---
title: 'PowerShell-skript: Ange tillåtna VM-storlekar i Azure Lab Services | Microsoft-dokument'
description: Den här artikeln innehåller ett exempel på PowerShell-skript som anger tillåtna storlekar för virtuella datorer (VM) i Azure Lab Services.
services: lab-services
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: a1b0e9a4aed475f04ec8dcffa9bc95b7c7c713e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76760477"
---
# <a name="use-powershell-to-set-allowed-vm-sizes-in-azure-lab-services"></a>Använda PowerShell för att ange tillåtna VM-storlekar i Azure Lab Services

Det här exemplet powershell-skriptuppsättningar tillåts virtuella datorstorlekar (VM) i Azure Lab Services.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Krav
* **Ett labb.** Skriptet kräver att du har ett befintligt labb. 

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/devtest-lab/set-allowed-vm-sizes-in-lab/set-allowed-vm-sizes-in-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon: 

| Kommando | Anteckningar |
|---|---|
| Sök-AzResource | Söker efter resurser baserat på angivna parametrar. |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Hämtar resurser. |
| [Set-AzResource](/powershell/module/az.resources/set-azresource) | Ändrar en resurs. |
| [Ny-AzResource](/powershell/module/az.resources/new-azresource) | skapa en resurs |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](https://docs.microsoft.com/powershell/).

Ytterligare Azure Lab Services PowerShell-skriptexempel finns i [Azure Lab Services PowerShell-exempel](../samples-powershell.md).
