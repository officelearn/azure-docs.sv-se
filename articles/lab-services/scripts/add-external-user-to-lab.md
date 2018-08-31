---
title: 'PowerShell-skript: lägga till en extern användare i ett labb i Azure DevTest Labs | Microsoft Docs'
description: Det här PowerShell-skriptet lägger till en extern användare till ett labb i Azure DevTest Labs.
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
ms.openlocfilehash: 0acff1eb4cee441187205b11a7e07cc718072cbf
ms.sourcegitcommit: 2b2129fa6413230cf35ac18ff386d40d1e8d0677
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2018
ms.locfileid: "43248197"
---
# <a name="use-powershell-to-add-an-external-user-to-a-lab-in-azure-devtest-labs"></a>Använd PowerShell för att lägga till en extern användare i ett labb i Azure DevTest Labs

Det här PowerShell-exempelskript lägger till en extern användare till ett labb i Azure DevTest Labs. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Förutsättningar
* **Ett labb**. Skriptet kräver att du har en befintlig labb. 

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/devtest-lab/add-external-user-to-lab/add-external-user-to-custom-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon: 

| Kommando | Anteckningar |
|---|---|
| [Get-AzureRmADUser](/powershell/module/azurerm.resources/get-azurermaduser) | Återförsök för user-objektet från Azure active directory. |
| [New-AzureRmRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment) | Tilldelar den angivna rollen till den angivna säkerhetsobjekt i det specificerade omfånget. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](https://docs.microsoft.com/powershell/).

Ytterligare Azure Lab Services PowerShell-skriptexempel finns i den [Azure Lab Services PowerShell-exempel](../samples-powershell.md).
