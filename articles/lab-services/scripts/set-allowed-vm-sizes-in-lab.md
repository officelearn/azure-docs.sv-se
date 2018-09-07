---
title: 'PowerShell-skript: ange tillåtna storlekar för Virtuella datorer i Azure Lab Services | Microsoft Docs'
description: Den här PowerShell.skript anger tillåtna storlekar för Virtuella datorer i Azure Lab Services.
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
ms.openlocfilehash: 559e74675a5d113584dca21979c20462c9cdf19c
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44054714"
---
# <a name="use-powershell-to-set-allowed-vm-sizes-in-azure-lab-services"></a>Använd PowerShell för att ange tillåtna storlekar för Virtuella datorer i Azure Lab Services

Det här Skriptexemplet för PowerShell anger storlekar för tillåtna virtuella datorer (VM) i Azure Lab Services.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Förutsättningar
* **Ett labb**. Skriptet kräver att du har en befintlig labb. 

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/devtest-lab/set-allowed-vm-sizes-in-lab/set-allowed-vm-sizes-in-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon: 

| Kommando | Anteckningar |
|---|---|
| [Find-AzureRmResource](/powershell/module/azurerm.resources/find-azurermresource) | Söker efter resurser baserat på de angivna parametrarna. |
| [Get-AzureRmResource](/powershell/module/azurerm.resources/get-azurermresource) | Hämtar resurser. |
| [Set-AzureRmResource](/powershell/module/azurerm.resources/set-azurermresource) | Ändrar en resurs. |
| [New-AzureRmResource](/powershell/module/azurerm.resources/new-azurermresource) | Skapa en resurs. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](https://docs.microsoft.com/powershell/).

Ytterligare Azure Lab Services PowerShell-skriptexempel finns i den [Azure Lab Services PowerShell-exempel](../samples-powershell.md).
