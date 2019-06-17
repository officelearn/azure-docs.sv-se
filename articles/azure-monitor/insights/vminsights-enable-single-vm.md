---
title: Aktivera Azure Monitor för virtuella datorer (förhandsversion) för utvärdering | Microsoft Docs
description: Lär dig att utvärdera Azure Monitor för virtuella datorer på en enda Azure-dator eller på en VM-skalningsuppsättning.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/09/2019
ms.author: magoedte
ms.openlocfilehash: ec909bcd16f923bbd7036f6a69df2bbb07e561b8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67122458"
---
# <a name="enable-azure-monitor-for-vms-preview-for-evaluation"></a>Aktivera Azure Monitor för virtuella datorer (förhandsversion) för utvärdering

Du kan utvärdera Azure Monitor för virtuella datorer (förhandsversion) på ett litet antal virtuella Azure-datorer (VM) eller på en enda virtuell dator eller virtuell dator skala. Det enklaste och snabbaste sättet att aktivera övervakning är från Azure-portalen. Målet är att övervaka dina virtuella datorer och identifiera prestanda eller tillgänglighetsproblem. 

Innan du börjar bör du granska den [krav](vminsights-enable-overview.md) och kontrollera att din prenumeration och resurser som uppfyller kraven.  

## <a name="enable-monitoring-for-a-single-azure-vm"></a>Aktivera övervakning för en virtuell Azure-dator
För att övervaka dina virtuella Azure-datorn:

1. Logga in på [Azure Portal](https://portal.azure.com).

1. Välj **virtuella datorer**.

1. Välj en virtuell dator i listan.

1. På sidan virtuell dator i den **övervakning** väljer **Insights (förhandsversion)** .

1. På den **Insights (förhandsversion)** väljer **Prova nu**.

    ![Aktivera Azure Monitor för virtuella datorer för en virtuell dator](./media/vminsights-enable-single-vm/enable-vminsights-vm-portal-01.png)

1. På den **Azure Monitor insikter Onboarding** om du har en befintlig Log Analytics-arbetsyta i samma prenumeration, markerar du den i den nedrullningsbara listan.  

    Listan förväljer standardarbetsyta och plats där den virtuella datorn har distribuerats i prenumerationen. 

    >[!NOTE]
    >Om du vill skapa en ny Log Analytics-arbetsyta för att lagra övervakningsdata från den virtuella datorn, [skapa en Log Analytics-arbetsyta](../../azure-monitor/learn/quick-create-workspace.md). Log Analytics-arbetsytan måste tillhöra en av de [regioner som stöds](vminsights-enable-overview.md#log-analytics).

När du har aktiverat övervakning, kan du behöva vänta i ungefär 10 minuter innan du kan visa hälsotillstånd mått för den virtuella datorn.

![Aktivera Azure Monitor för virtuella datorer övervakning distributionsbearbetning](./media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

## <a name="enable-monitoring-for-a-single-virtual-machine-scale-set"></a>Aktivera övervakning för en enda VM-skalningsuppsättning

Aktivera övervakning av din Azure VM-skalningsuppsättning:

1. Logga in på [Azure Portal](https://portal.azure.com).

2. Välj **Virtual Machine Scale Sets**.

3. I listan, Välj en virtual machine scale Sets.

4. På den virtuella datorn scale Sets-sidan i den **övervakning** väljer **Insights (förhandsversion)** .

5. På den **Insights (förhandsversion)** om du vill använda en befintlig Log Analytics-arbetsyta, markerar du den i den nedrullningsbara listan.

    Listan förväljer standardarbetsytan och plats som den virtuella datorn har distribuerats till i prenumerationen. 

    ![Aktivera Azure Monitor för virtuella datorer för en VM-skalningsuppsättning](./media/vminsights-enable-single-vm/enable-vminsights-vmss-portal-01.png)

    >[!NOTE]
    >Om du vill skapa en ny Log Analytics-arbetsyta för att lagra övervakningsdata från virtuella datorns skalningsuppsättning [skapa en Log Analytics-arbetsyta](../learn/quick-create-workspace.md). Log Analytics-arbetsytan måste tillhöra en av de [regioner som stöds](vminsights-enable-overview.md#log-analytics).

När du har aktiverat övervakning, kan du behöva vänta i ungefär 10 minuter innan du kan visa övervakningsdata för skalningsuppsättningen.

>[!NOTE]
>Om du använder en manuell uppgradering modell för din skalningsuppsättning, uppgradera instanser för att slutföra installationen. Du kan starta uppgraderingar från den **instanser** sidan den **inställningar** avsnittet.

![Aktivera Azure Monitor för virtuella datorer övervakning distributionsbearbetning](./media/vminsights-enable-single-vm/onboard-vminsights-vmss-portal-status-01.png)

Nu när du har aktiverat övervakning för din skalningsuppsättning för virtuell dator eller virtuell dator kan är övervakningsinformation tillgängliga för analys i Azure Monitor för virtuella datorer. 

## <a name="next-steps"></a>Nästa steg

* Läs hur du använder funktionen hälsotillstånd i [förstå hälsotillståndet för dina virtuella datorer i Azure Monitor](vminsights-health.md). 
* Identifierade programberoenden finns [Använd Azure Monitor för virtuella datorer kartan](vminsights-maps.md). 
* För att identifiera flaskhalsar, totala användningen och den Virtuella datorns prestanda, se [visa Azure VM prestanda](vminsights-performance.md).