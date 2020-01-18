---
title: PowerShell – skapa en anpassad roll i ett labb i Azure DevTest Labs
description: Det här PowerShell-skriptet lägger till en extern användare i ett labb i Azure DevTest Labs.
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
ms.openlocfilehash: bcad4e02105bff2dc78a4adc3ca614f997794d52
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/17/2020
ms.locfileid: "76166427"
---
# <a name="use-powershell-to-create-a-custom-role-in-a-lab-in-azure-devtest-labs"></a>Använd PowerShell för att skapa en anpassad roll i ett labb i Azure DevTest Labs

Det här exemplet på PowerShell-skriptet skapar en anpassad roll som ska användas i ett labb i Azure DevTest Labs. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Krav
* **Ett labb**. Skriptet kräver att du har ett befintligt labb. 

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/devtest-lab/create-custom-role-in-lab/create-custom-role-in-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon: 

| Kommando | Anteckningar |
|---|---|
| [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) | Hämtar åtgärder för en Azure-resurs-Provider som kan skyddas med Azure RBAC. |
| [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) | Visar en lista över alla Azure RBAC-roller som är tillgängliga för tilldelning. |
| [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) | Skapar en anpassad roll. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](https://docs.microsoft.com/powershell/).

Ytterligare Azure Lab Services PowerShell-skript exempel finns i [Azure Lab Services PowerShell-exempel](../samples-powershell.md).
