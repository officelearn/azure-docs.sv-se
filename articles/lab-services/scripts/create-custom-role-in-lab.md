---
title: 'PowerShell-skript: Skapa en anpassad roll i ett labb i Azure DevTest Labs | Microsoft Docs'
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
ms.openlocfilehash: 4d0c795dffb40ab7efec9005660439f9baef9f3f
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2019
ms.locfileid: "56732440"
---
# <a name="use-powershell-to-create-a-custom-role-in-a-lab-in-azure-devtest-labs"></a>Använd PowerShell för att skapa en anpassad roll i ett labb i Azure DevTest Labs

Det här Skriptexemplet för PowerShell skapar en anpassad roll som ska användas i ett labb i Azure DevTest Labs. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Förutsättningar
* **Ett labb**. Skriptet kräver att du har en befintlig labb. 

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/devtest-lab/create-custom-role-in-lab/create-custom-role-in-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon: 

| Kommando | Anteckningar |
|---|---|
| [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) | Hämtar åtgärderna för en Azure-resursprovidern som kan skyddas med Azure RBAC. |
| [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) | Visar alla Azure RBAC-roller som är tillgängliga för tilldelning. |
| [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) | Skapar en anpassad roll. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](https://docs.microsoft.com/powershell/).

Ytterligare Azure Lab Services PowerShell-skriptexempel finns i den [Azure Lab Services PowerShell-exempel](../samples-powershell.md).
