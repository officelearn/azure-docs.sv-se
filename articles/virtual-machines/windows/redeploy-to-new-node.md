---
title: Distribuera Windows-datorer i Azure | Microsoft Docs
description: Hur du distribuerar Windows-datorer i Azure för att minimera RDP-anslutningsproblem.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: genlin
manager: jeconnoc
tags: azure-resource-manager,top-support-issue
ms.assetid: 0ee456ee-4595-4a14-8916-72c9110fc8bd
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: c4115e82e4d5f1ed2b952d9fb8d8d820794133b2
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="redeploy-windows-virtual-machine-to-new-azure-node"></a>Distribuera Windows-dator till den nya noden i Azure
Om du har med svårigheter kan RDP (Remote Desktop)-anslutning eller program åtkomst till Windows-baserad Azure virtuell dator (VM), omdistribuera den virtuella datorn felsökningshjälp. När du distribuerar en virtuell dator, flyttar den virtuella datorn till en ny nod i Azure-infrastrukturen och sedan aktiveras den tillbaka, behåller alla konfigurationsalternativ och associerade resurser. Den här artikeln visar hur du distribuerar en virtuell dator med hjälp av Azure PowerShell eller Azure-portalen.

> [!NOTE]
> När du distribuerar en virtuell dator tillfälliga disken går förlorad och dynamiska IP-adresser som är kopplade till virtuella nätverksgränssnittet har uppdaterats. 


## <a name="using-azure-powershell"></a>Använda Azure PowerShell
Kontrollera att du har den senaste Azure PowerShell 1.x installerat på datorn. Mer information finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/overview).

I följande exempel distribuerar den virtuella datorn med namnet `myVM` i resursgrupp med namnet `myResourceGroup`:

```powershell
Set-AzureRmVM -Redeploy -ResourceGroupName "myResourceGroup" -Name "myVM"
```


[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>Nästa steg
Om du har problem med anslutningen till den virtuella datorn kan du hittar detaljerad hjälp på [felsökning av RDP-anslutningar](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) eller [detaljerad RDP felsökningssteg](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Om du inte kommer åt ett program som körs på den virtuella datorn, du kan också läsa [felsökning av problem med programmet](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

