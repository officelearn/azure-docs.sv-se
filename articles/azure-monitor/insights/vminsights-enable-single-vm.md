---
title: Aktivera Azure Monitor for VMs i Azure Portal
description: Lär dig hur du utvärderar Azure Monitor for VMs på en virtuell Azure-dator eller på en virtuell dators skalnings uppsättning.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/25/2020
ms.openlocfilehash: 4cdb9390b3146df74f2cbe8eba7b170a5d11fb2c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85507066"
---
# <a name="enable-azure-monitor-for-single-vm-or-vmss-in-the-azure-portal"></a>Aktivera Azure Monitor för en enskild virtuell dator eller VMSS i Azure Portal
I den här artikeln beskrivs hur du aktiverar Azure Monitor for VMs för en enskild virtuell dator eller skalnings uppsättning för virtuella datorer med hjälp av Azure Portal. Den här proceduren kan användas för följande:

- Virtuell Azure-dator
- Skalnings uppsättning för virtuella Azure-datorer
- Azure Arc-dator

Innan du börjar [granskar du kraven och kontrollerar](vminsights-enable-overview.md) att din prenumeration och dina resurser uppfyller kraven.  

## <a name="enable-azure-monitor-for-vms"></a>Aktivera Azure Monitor for VMs

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Välj **virtuella datorer**, **skalnings uppsättningar för virtuella**datorer eller **datorer – Azure-båge**.

1. Välj en resurs i listan.

1. I avsnittet **övervakning** på menyn väljer du **insikter** och **aktiverar**sedan. I följande exempel visas en virtuell Azure-dator, men menyn liknar Azure VMSS eller Azure Arc.

    ![Aktivera Azure Monitor for VMs för en virtuell dator](media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png)

1. Om den virtuella datorn inte redan är ansluten till en Log Analytics arbets yta, uppmanas du att välja en. Om du inte redan har [skapat en arbets yta](../../azure-monitor/learn/quick-create-workspace.md)kan du välja ett standardvärde för den plats där den virtuella datorn eller VMSS distribueras i prenumerationen. Den här arbets ytan kommer att skapas och konfigureras om den inte redan finns.

2. Du får status meddelanden när konfigurationen utförs.

    >[!NOTE]
    >Om du använder en manuell uppgraderings modell för din skalnings uppsättning uppgraderar du instanserna för att slutföra installationen. Du kan starta uppgraderingar från sidan **instanser** i avsnittet **Inställningar** .

    ![Aktivera Azure Monitor for VMs övervakning av distributions bearbetning](media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)



## <a name="next-steps"></a>Nästa steg

* Om du vill visa identifierade program beroenden, se [använd Azure Monitor for VMS karta](vminsights-maps.md). 
* Information om hur du identifierar Flask halsar, övergripande användning och den virtuella datorns prestanda finns i [Visa prestanda för virtuella Azure-datorer](vminsights-performance.md).
