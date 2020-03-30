---
title: Distribuera om virtuella Windows-datorer i Azure | Microsoft-dokument
description: Så här distribuerar du om virtuella Windows-datorer i Azure för att minska rdp-anslutningsproblem.
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
ms.openlocfilehash: 36af0eeb43fb209ed65f950576f2dc9e97ec3633
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "71058632"
---
# <a name="redeploy-windows-virtual-machine-to-new-azure-node"></a>Distribuera om en virtuell Windows-dator till en ny Azure-nod
Om du har haft problem med att felsöka rdp-anslutning eller programåtkomst till Windows-baserad virtuell virtuell virtuell dator (VM) kan det hjälpa att distribuera om den virtuella datorn. När du distribuerar om en virtuell dator stänger Azure av den virtuella datorn, flyttar den virtuella datorn till en ny nod i Azure-infrastrukturen och slår sedan på den igen och behåller alla dina konfigurationsalternativ och tillhörande resurser. Den här artikeln visar hur du distribuerar om en virtuell dator med Azure PowerShell eller Azure-portalen.

> [!NOTE]
> När du har distribuerat om en virtuell dator går den tillfälliga disken förlorad och dynamiska IP-adresser som är associerade med virtuellt nätverksgränssnitt uppdateras. 


## <a name="using-azure-powershell"></a>Använda Azure PowerShell
Kontrollera att du har den senaste Azure PowerShell 1.x installerad på din dator. Mer information finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/overview).

I följande exempel distribueras `myVM` den virtuella `myResourceGroup`datorn som namnges i resursgruppen:

```powershell
Set-AzVM -Redeploy -ResourceGroupName "myResourceGroup" -Name "myVM"
```

[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>Nästa steg
Om du har problem med att ansluta till den virtuella datorn kan du hitta specifik hjälp med [att felsöka RDP-anslutningar](troubleshoot-rdp-connection.md) eller [detaljerade felsökningssteg för fjärrskrivbordstjänst](detailed-troubleshoot-rdp.md). Om du inte kan komma åt ett program som körs på den virtuella datorn kan du även läsa [problem med programfelsökning](../windows/troubleshoot-app-connection.md).

