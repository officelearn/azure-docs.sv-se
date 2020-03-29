---
title: PowerShell - Skapa anpassad avbildning från VHD-fil i Azure Lab Services
description: Det här PowerShell-skriptet skapar en anpassad avbildning från en VHD-fil i Azure Lab Services.
services: lab-services
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 38383462a665ced1ccb6c6a2f062fab0492eee9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169989"
---
# <a name="use-powershell-to-create-a-custom-image-from-a-vhd-file-in-azure-lab-services"></a>Använda PowerShell för att skapa en anpassad avbildning från en VHD-fil i Azure Lab Services

Det här exemplet med PowerShell-skript skapar en anpassad avbildning från en VHD-fil i Azure Lab Services

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Krav
* **Ett labb.** Skriptet kräver att du har ett befintligt labb. 

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/devtest-lab/create-custom-image-from-vhd/create-custom-image-from-vhd.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon: 

| Kommando | Anteckningar |
|---|---|
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Hämtar resurser. |
| [Get-AzStorageAccountKey](/powershell/module/az.storage/get-azstorageaccountkey) | Hämtar åtkomstnycklarna för Azure Storage-kontot. |
| [Ny-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) | Lägger till en Azure-distribution i en resursgrupp. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](https://docs.microsoft.com/powershell/).

Ytterligare Azure Lab Services PowerShell-skriptexempel finns i [Azure Lab Services PowerShell-exempel](../samples-powershell.md).
