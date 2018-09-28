---
title: Distribuera om Windows-datorer i Azure | Microsoft Docs
description: Så här att distribuera om Windows-datorer i Azure för att minimera RDP-anslutningsproblem.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: genlin
manager: jeconnoc
tags: azure-resource-manager,top-support-issue
ms.assetid: 0ee456ee-4595-4a14-8916-72c9110fc8bd
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: ae5fd0d1a16d67c0649412edce6a130150f3cc6a
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2018
ms.locfileid: "47414813"
---
# <a name="redeploy-windows-virtual-machine-to-new-azure-node"></a>Distribuera om Windows virtuell dator till nya Azure-nod
Om du har med svårigheter kan felsökning av RDP (Remote Desktop)-anslutning eller program åtkomst till Windows-baserade Azure-dator (VM), distribuerar om den virtuella datorn hjälpa. När du distribuerar om en virtuell dator, flyttas den virtuella datorn till en ny nod i Azure-infrastrukturen och sedan aktiverar den igen, behålla alla konfigurationsalternativ och associerade resurser. Den här artikeln visar hur du distribuera om en virtuell dator med Azure PowerShell eller Azure-portalen.

> [!NOTE]
> När du distribuerar om en virtuell dator, den temporära disken går förlorad och den dynamiska IP-adresser som är associerade med virtuella nätverksgränssnittet har uppdaterats. 


## <a name="using-azure-powershell"></a>Använda Azure PowerShell
Kontrollera att du har den senaste Azure PowerShell 1.x installerat på datorn. Mer information finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/overview).

I följande exempel distribuerar den virtuella datorn med namnet `myVM` i resursgruppen med namnet `myResourceGroup`:

```powershell
Set-AzureRmVM -Redeploy -ResourceGroupName "myResourceGroup" -Name "myVM"
```


[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>Nästa steg
Om du har problem med att ansluta till den virtuella datorn kan du hittar detaljerad hjälp på [felsöka RDP-anslutningar](troubleshoot-rdp-connection.md) eller [detaljerad RDP felsökningssteg](detailed-troubleshoot-rdp.md). Om du inte kommer åt ett program som körs på den virtuella datorn, du kan också läsa [felsökning av problem med programmet](../windows/troubleshoot-app-connection.md).

