---
title: Aktivera Azure Monitor for VMs (för hands version) för utvärdering | Microsoft Docs
description: Lär dig hur du utvärderar Azure Monitor for VMs på en virtuell Azure-dator eller på en virtuell dators skalnings uppsättning.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 11/14/2019
ms.openlocfilehash: 3ab3164db7702566e62b2eedf2a9a03e9f7fb55c
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2019
ms.locfileid: "74109159"
---
# <a name="enable-azure-monitor-for-vms-preview-for-evaluation"></a>Aktivera Azure Monitor for VMs (för hands version) för utvärdering

Du kan utvärdera Azure Monitor for VMs (för hands version) på ett litet antal virtuella datorer i Azure (VM) eller på en enskild virtuell dator eller skalnings uppsättning för virtuella datorer. Det enklaste och mest direkta sättet att aktivera övervakning är från Azure Portal. Målet är att övervaka dina virtuella datorer och identifiera eventuella prestanda-och tillgänglighets problem. 

Innan du börjar [granskar du kraven och kontrollerar](vminsights-enable-overview.md) att din prenumeration och dina resurser uppfyller kraven.  

## <a name="enable-monitoring-for-a-single-azure-vm"></a>Aktivera övervakning för en enskild virtuell Azure-dator
Så här aktiverar du övervakning av den virtuella Azure-datorn:

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Välj **virtuella datorer**.

1. Välj en virtuell dator i listan.

1. På sidan virtuell dator i den **övervakning** väljer **Insights (förhandsversion)** .

1. På den **Insights (förhandsversion)** väljer **Prova nu**.

    ![Aktivera Azure Monitor för virtuella datorer för en virtuell dator](./media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png)

1. På den **Azure Monitor insikter Onboarding** om du har en befintlig Log Analytics-arbetsyta i samma prenumeration, markerar du den i den nedrullningsbara listan.  

    I listan förväljs standard arbets ytan och den plats där den virtuella datorn distribueras i prenumerationen. 

    >[!NOTE]
    >Om du vill skapa en ny Log Analytics-arbetsyta för att lagra övervaknings data från den virtuella datorn, se [skapa en Log Analytics arbets yta](../../azure-monitor/learn/quick-create-workspace.md). Din Log Analytics-arbetsyta måste tillhöra en av de [regioner som stöds](vminsights-enable-overview.md#log-analytics).

När du har aktiverat övervakning kan du behöva vänta cirka 10 minuter innan du kan visa hälso måtten för den virtuella datorn.

![Aktivera Azure Monitor för virtuella datorer övervakning distributionsbearbetning](./media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

## <a name="enable-monitoring-for-a-single-virtual-machine-scale-set"></a>Aktivera övervakning för en enskild virtuell dators skalnings uppsättning

Så här aktiverar du övervakning av din skalnings uppsättning för virtuella Azure-datorer:

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Välj **Virtual Machine Scale Sets**.

3. Välj en skalnings uppsättning för virtuell dator i listan.

4. På sidan skalnings uppsättning för virtuell dator i avsnittet **övervakning** väljer du **insikter (för hands version)** .

5. På sidan **insikter (för hands version)** , om du vill använda en befintlig Log Analytics arbets yta, väljer du den i list rutan.

    I listan förväljs standard arbets ytan och platsen som den virtuella datorn distribueras till i prenumerationen. 

    ![Aktivera Azure Monitor for VMs för en skalnings uppsättning för virtuell dator](./media/vminsights-enable-single-vm/enable-vminsights-vmss-portal.png)

    >[!NOTE]
    >Om du vill skapa en ny Log Analytics-arbetsyta för att lagra övervaknings data från den virtuella datorns skal uppsättning, se [skapa en Log Analytics arbets yta](../learn/quick-create-workspace.md). Din Log Analytics-arbetsyta måste tillhöra en av de [regioner som stöds](vminsights-enable-overview.md#log-analytics).

När du har aktiverat övervakning kan du behöva vänta cirka 10 minuter innan du kan visa övervaknings data för skalnings uppsättningen.

>[!NOTE]
>Om du använder en manuell uppgraderings modell för din skalnings uppsättning uppgraderar du instanserna för att slutföra installationen. Du kan starta uppgraderingar från sidan **instanser** i avsnittet **Inställningar** .

![Aktivera Azure Monitor för virtuella datorer övervakning distributionsbearbetning](./media/vminsights-enable-single-vm/onboard-vminsights-vmss-portal-status-01.png)

Nu när du har aktiverat övervakning av den virtuella datorn eller skalnings uppsättningen för virtuella datorer är övervaknings informationen tillgänglig för analys i Azure Monitor for VMs. 

## <a name="next-steps"></a>Nästa steg

* Om du vill visa identifierade program beroenden, se [använd Azure Monitor for VMS karta](vminsights-maps.md). 
* Information om hur du identifierar Flask halsar, övergripande användning och den virtuella datorns prestanda finns i [Visa prestanda för virtuella Azure-datorer](vminsights-performance.md).