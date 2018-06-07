---
title: 'PowerShell-skript: ange tillåtna storlekar på VM i Azure Lab Services | Microsoft Docs'
description: Detta PowerShell-skript anger tillåtna storlekar på VM i Azure labb-tjänster.
services: lab-services
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/11/2018
ms.author: spelluru
ms.openlocfilehash: 159f175e7bb27b2d89001e1eba737c67adb89e50
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34638151"
---
# <a name="use-powershell-to-set-allowed-vm-sizes-in-azure-lab-services"></a>Använd PowerShell för att ange tillåtna storlekar på VM i Azure Lab Services

Det här exempelskriptet PowerShell anger storlekar för tillåtna virtuella datorer (VM) i Azure Lab Services.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Förutsättningar
* **Ett labb**. Skriptet kräver att du har en befintlig labbet. 

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/devtest-lab/set-allowed-vm-sizes-in-lab/set-allowed-vm-sizes-in-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon: 

| Kommando | Anteckningar |
|---|---|
| [Find-AzureRmResource](/module/azurerm.resources/find-azurermresource) | Söker efter resurser baserat på angivna parametrar. |
| [Get-AzureRmResource](/powershell/module/azurerm.resources/get-azurermresource) | Hämtar resurser. |
| [Set-AzureRmResource](/powershell/module/azurerm.resources/set-azurermresource) | Ändrar en resurs. |
| [New-AzureRmResource](/powershell/module/azurerm.resources/new-azurermresource) | Skapa en resurs. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](https://docs.microsoft.com/powershell/).

Ytterligare Azure Lab Services PowerShell skriptexempel finns i den [Azure Lab Services PowerShell-exempel](../samples-powershell.md).