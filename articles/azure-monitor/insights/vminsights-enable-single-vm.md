---
title: Aktivera Azure Monitor for VMs i Azure Portal
description: Lär dig hur du utvärderar Azure Monitor for VMs på en virtuell Azure-dator eller på en virtuell dators skalnings uppsättning.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 45bc8f16a547d4a95820f9dcd02132844b3be83c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "79480716"
---
# <a name="enable-azure-monitor-for-vms-in-the-azure-portal"></a>Aktivera Azure Monitor for VMs i Azure Portal

I den här artikeln beskrivs hur du aktiverar Azure Monitor for VMs på ett litet antal virtuella datorer i Azure med hjälp av Azure Portal. Målet är att övervaka dina virtuella datorer och identifiera eventuella prestanda-och tillgänglighets problem. 

Innan du börjar [granskar du kraven och kontrollerar](vminsights-enable-overview.md) att din prenumeration och dina resurser uppfyller kraven.  

## <a name="enable-monitoring-for-a-single-azure-vm"></a>Aktivera övervakning för en enskild virtuell Azure-dator
Så här aktiverar du övervakning av den virtuella Azure-datorn:

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Välj **Virtual Machines**.

1. Välj en virtuell dator i listan.

1. På sidan virtuell dator i avsnittet **övervakning** väljer du **insikter** och **aktiverar**sedan.

    ![Aktivera Azure Monitor for VMs för en virtuell dator](media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png)

1. Om du har en befintlig Log Analytics arbets yta i samma prenumeration på sidan **Azure Monitor Insights-onboarding** väljer du den i list rutan.  

    I listan förväljs standard arbets ytan och den plats där den virtuella datorn distribueras i prenumerationen. 

    >[!NOTE]
    >Om du vill skapa en ny Log Analytics-arbetsyta för att lagra övervaknings data från den virtuella datorn, se [skapa en Log Analytics arbets yta](../../azure-monitor/learn/quick-create-workspace.md). Din Log Analytics-arbetsyta måste tillhöra en av de [regioner som stöds](vminsights-enable-overview.md#log-analytics).

6. Du får status meddelanden när konfigurationen utförs.

    ![Aktivera Azure Monitor for VMs övervakning av distributions bearbetning](media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

## <a name="enable-monitoring-for-a-single-virtual-machine-scale-set"></a>Aktivera övervakning för en enskild virtuell dators skalnings uppsättning

Så här aktiverar du övervakning av din skalnings uppsättning för virtuella Azure-datorer:

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Välj **Virtual Machine Scale Sets**.

3. Välj en skalnings uppsättning för virtuell dator i listan.

4. På sidan skalnings uppsättning för virtuell dator i avsnittet **övervakning** väljer du **insikter** och **aktiverar**sedan.

5. På sidan **insikter** , om du vill använda en befintlig Log Analytics arbets yta, väljer du den i list rutan.

    I listan förväljs standard arbets ytan och platsen som den virtuella datorn distribueras till i prenumerationen. 

    ![Aktivera Azure Monitor for VMs för en skalnings uppsättning för virtuell dator](media/vminsights-enable-single-vm/enable-vminsights-vmss-portal.png)

    >[!NOTE]
    >Om du vill skapa en ny Log Analytics-arbetsyta för att lagra övervaknings data från den virtuella datorns skal uppsättning, se [skapa en Log Analytics arbets yta](../learn/quick-create-workspace.md). Din Log Analytics-arbetsyta måste tillhöra en av de [regioner som stöds](vminsights-enable-overview.md#log-analytics).

6. Du får status meddelanden när konfigurationen utförs.

    >[!NOTE]
    >Om du använder en manuell uppgraderings modell för din skalnings uppsättning uppgraderar du instanserna för att slutföra installationen. Du kan starta uppgraderingar från sidan **instanser** i avsnittet **Inställningar** .
    
    ![Aktivera Azure Monitor for VMs övervakning av distributions bearbetning](media/vminsights-enable-single-vm/onboard-vminsights-vmss-portal-status.png)

Nu när du har aktiverat övervakning av den virtuella datorn eller skalnings uppsättningen för virtuella datorer är övervaknings informationen tillgänglig för analys i Azure Monitor for VMs. 

## <a name="next-steps"></a>Nästa steg

* Om du vill visa identifierade program beroenden, se [använd Azure Monitor for VMS karta](vminsights-maps.md). 
* Information om hur du identifierar Flask halsar, övergripande användning och den virtuella datorns prestanda finns i [Visa prestanda för virtuella Azure-datorer](vminsights-performance.md).
