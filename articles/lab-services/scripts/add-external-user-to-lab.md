---
title: 'PowerShell-skript: lägga till en extern användare i ett labb i Azure DevTest Labs | Microsoft Docs'
description: Detta PowerShell-skript lägger till en extern användare i ett labb i Azure DevTest Labs.
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
ms.openlocfilehash: bd86e44c21ca3c0b9c061f9a5c24bd19c4b207f9
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34636451"
---
# <a name="use-powershell-to-add-an-external-user-to-a-lab-in-azure-devtest-labs"></a>Använd PowerShell för att lägga till en extern användare i ett labb i Azure DevTest Labs

Det här PowerShell-exempelskript lägger till en extern användare i ett labb i Azure DevTest Labs. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Förutsättningar
* **Ett labb**. Skriptet kräver att du har en befintlig labbet. 

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/devtest-lab/add-external-user-to-lab/add-external-user-to-custom-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon: 

| Kommando | Anteckningar |
|---|---|
| [Get-AzureRmADUser](/powershell/module/azurerm.resources/get-azurermaduser) | Antal försök användarobjektet från Azure active directory. |
| [New-AzureRmRoleAssignment](/module/azurerm.resources/new-azurermroleassignment) | Tilldelar den angivna rollen till den angivna säkerhetsobjekt i det specificerade omfånget. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](https://docs.microsoft.com/powershell/).

Ytterligare Azure Lab Services PowerShell skriptexempel finns i den [Azure Lab Services PowerShell-exempel](../samples-powershell.md).