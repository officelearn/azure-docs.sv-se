---
title: Distribuera virtuella Windows-datorer i Azure | Microsoft Docs
description: Så här distribuerar du om virtuella Windows-datorer i Azure för att undvika problem med RDP-anslutningen.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: genlin
manager: dcscontentpm
tags: azure-resource-manager,top-support-issue
ms.assetid: 0ee456ee-4595-4a14-8916-72c9110fc8bd
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: afbea39a080e1dd768a14d6e0eacda1bad23c5a4
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87074426"
---
# <a name="redeploy-windows-virtual-machine-to-new-azure-node"></a>Distribuera om en virtuell Windows-dator till en ny Azure-nod
Om du har problem med att felsöka fjärr skrivbords anslutning (RDP) eller program åtkomst till Windows-baserad virtuell Azure-dator (VM) kan det hjälpa att distribuera om den virtuella datorn. När du distribuerar om en virtuell dator stänger Azure av den virtuella datorn, flyttar den virtuella datorn till en ny nod i Azure-infrastrukturen och slår sedan på den igen och behåller alla konfigurations alternativ och tillhör ande resurser. Den här artikeln visar hur du distribuerar om en virtuell dator med hjälp av Azure PowerShell eller Azure Portal.

> [!NOTE]
> När du har distribuerat om en virtuell dator, går den temporära disken förlorad och dynamiska IP-adresser som är kopplade till det virtuella nätverks gränssnittet uppdateras. 


## <a name="using-azure-powershell"></a>Använda Azure PowerShell
Kontrol lera att du har det senaste Azure PowerShell 1. x installerat på datorn. Mer information finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/).

I följande exempel distribueras den virtuella datorn `myVM` i resurs gruppen med namnet `myResourceGroup` :

```powershell
Set-AzVM -Redeploy -ResourceGroupName "myResourceGroup" -Name "myVM"
```

[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>Nästa steg
Om du har problem med att ansluta till din virtuella dator kan du hitta information om hur du [felsöker RDP-anslutningar](troubleshoot-rdp-connection.md) eller [detaljerade RDP-felsöknings steg](detailed-troubleshoot-rdp.md). Om du inte kan komma åt ett program som körs på den virtuella datorn kan du också läsa [fel söknings problem i programmet](./troubleshoot-app-connection.md).
