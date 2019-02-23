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
ms.openlocfilehash: 22c30e1c3b19e62489a08a702d8b35fbc4862e13
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2019
ms.locfileid: "56728224"
---
# <a name="use-powershell-to-add-a-marketplace-image-to-a-lab-in-azure-devtest-labs"></a>Använd PowerShell för att lägga till en marketplace-avbildning till ett labb i Azure DevTest Labs

Det här PowerShell-exempelskript lägger till en marketplace-avbildning till ett labb i Azure DevTest Labs. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Förutsättningar
* **Ett labb**. Skriptet kräver att du har en befintlig labb. 

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/devtest-lab/add-marketplace-images-to-lab/add-marketplace-images-to-lab.ps1 "Add marketplace images to a lab")]

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon: 

| Kommando | Anteckningar |
|---|---|
| [Find-AzResource](/powershell/module/az.resources/find-azresource) | Söker efter resurser baserat på de angivna parametrarna. |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Hämtar resurser. |
| [Set-AzResource](/powershell/module/az.resources/set-azresource) | Ändrar en resurs. |
| [New-AzResource](/powershell/module/az.resources/new-azresource) | Skapa en resurs. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](https://docs.microsoft.com/powershell/).

Ytterligare Azure Lab Services PowerShell-skriptexempel finns i den [Azure Lab Services PowerShell-exempel](../samples-powershell.md).
