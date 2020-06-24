---
title: 'PowerShell-skript: ange tillåtna VM-storlekar i Azure Lab Services | Microsoft Docs'
description: I den här artikeln finns ett exempel på ett PowerShell-skript som anger tillåtna storlekar för virtuella datorer i Azure Lab Services.
services: lab-services
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/24/2020
ms.author: spelluru
ms.openlocfilehash: 5b3dbee7d0ac928c4f18f25348e714aba9c1cd13
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/17/2020
ms.locfileid: "84898061"
---
# <a name="use-powershell-to-set-allowed-vm-sizes-in-azure-lab-services"></a>Använd PowerShell för att ställa in tillåtna VM-storlekar i Azure Lab Services

Det här exemplet på PowerShell-skript anger tillåtna virtuella dator storlekar (VM) i Azure Lab Services.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Krav
* **Ett labb**. Skriptet kräver att du har ett befintligt labb. 

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/devtest-lab/set-allowed-vm-sizes-in-lab/set-allowed-vm-sizes-in-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon: 

| Kommando | Kommentarer |
|---|---|
| Find-AzResource | Söker efter resurser baserat på angivna parametrar. |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Hämtar resurser. |
| [Set-AzResource](/powershell/module/az.resources/set-azresource) | Ändrar en resurs. |
| [New-AzResource](/powershell/module/az.resources/new-azresource) | skapa en resurs |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](https://docs.microsoft.com/powershell/).

Ytterligare Azure Lab Services PowerShell-skript exempel finns i [Azure Lab Services PowerShell-exempel](../samples-powershell.md).
