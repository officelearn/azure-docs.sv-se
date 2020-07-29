---
title: PowerShell – Lägg till en Marketplace-avbildning i ett labb i Azure DevTest Labs
description: Det här PowerShell-skriptet lägger till en Marketplace-avbildning i ett labb i Azure DevTest Labs.
services: lab-services
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/22/2020
ms.author: spelluru
ms.openlocfilehash: 1c221f86d721a14f82f75d1cd9c81e37bba32a60
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87282627"
---
# <a name="use-powershell-to-add-a-marketplace-image-to-a-lab-in-azure-devtest-labs"></a>Använd PowerShell för att lägga till en Marketplace-avbildning i ett labb i Azure DevTest Labs

Det här exemplet på PowerShell-skript lägger till en Marketplace-avbildning i ett labb i Azure DevTest Labs. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Krav
* **Ett labb**. Skriptet kräver att du har ett befintligt labb. 

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/devtest-lab/add-marketplace-images-to-lab/add-marketplace-images-to-lab.ps1 "Add marketplace images to a lab")]

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon: 

| Kommando | Obs! |
|---|---|
| Find-AzResource | Söker efter resurser baserat på angivna parametrar. |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Hämtar resurser. |
| [Set-AzResource](/powershell/module/az.resources/set-azresource) | Ändrar en resurs. |
| [New-AzResource](/powershell/module/az.resources/new-azresource) | skapa en resurs |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](/powershell/).

Ytterligare Azure Lab Services PowerShell-skript exempel finns i [Azure Lab Services PowerShell-exempel](../samples-powershell.md).
