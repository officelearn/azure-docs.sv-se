---
title: 'PowerShell-skript: Lägg till en marketplace-avbildning till ett labb i Azure DevTest Labs | Microsoft Docs'
description: Det här PowerShell-skriptet lägger till en marketplace-avbildning till ett labb i Azure DevTest Labs.
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
ms.openlocfilehash: a3a007bf19a28e6f361837856f83a191a761ef9b
ms.sourcegitcommit: 2b2129fa6413230cf35ac18ff386d40d1e8d0677
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2018
ms.locfileid: "43247422"
---
# <a name="use-powershell-to-add-a-marketplace-image-to-a-lab-in-azure-devtest-labs"></a>Använd PowerShell för att lägga till en marketplace-avbildning till ett labb i Azure DevTest Labs

Det här PowerShell-exempelskript lägger till en marketplace-avbildning till ett labb i Azure DevTest Labs. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Förutsättningar
* **Ett labb**. Skriptet kräver att du har en befintlig labb. 

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/devtest-lab/add-marketplace-images-to-lab/add-marketplace-images-to-lab.ps1 "Add marketplace images to a lab")]

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
