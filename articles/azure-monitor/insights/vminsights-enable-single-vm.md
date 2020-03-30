---
title: Aktivera Azure Monitor för virtuella datorer i Azure-portalen
description: Lär dig hur du utvärderar Azure Monitor för virtuella datorer på en enda virtuell Azure-dator eller på en skalningsuppsättning för virtuella datorer.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 45bc8f16a547d4a95820f9dcd02132844b3be83c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480716"
---
# <a name="enable-azure-monitor-for-vms-in-the-azure-portal"></a>Aktivera Azure Monitor för virtuella datorer i Azure-portalen

I den här artikeln beskrivs hur du aktiverar Azure Monitor för virtuella datorer på ett litet antal virtuella Azure-datorer med Hjälp av Azure-portalen. Ditt mål är att övervaka dina virtuella datorer och upptäcka eventuella prestanda- eller tillgänglighetsproblem. 

Innan du börjar bör du granska [förutsättningarna](vminsights-enable-overview.md) och se till att prenumerationen och resurserna uppfyller kraven.  

## <a name="enable-monitoring-for-a-single-azure-vm"></a>Aktivera övervakning för en enda Virtuell Azure-dator
Så här aktiverar du övervakning av din virtuella Azure-dator:

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Välj **virtuella datorer**.

1. Välj en virtuell dator i listan.

1. På sidan Virtuell dator i avsnittet **Övervakning** väljer du **Insikter** och **aktiverar**sedan .

    ![Aktivera Azure Monitor för virtuella datorer för en virtuell dator](media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png)

1. Om du har en befintlig Log Analytics-arbetsyta i samma prenumeration på sidan **Azure Monitor Insights Onboarding** väljer du den i listrutan.  

    Listan förväljs till standardarbetsyta och plats där den virtuella datorn distribueras i prenumerationen. 

    >[!NOTE]
    >Information om hur du skapar en ny Log Analytics-arbetsyta för att lagra övervakningsdata från den virtuella datorn finns i [Skapa en Log Analytics-arbetsyta](../../azure-monitor/learn/quick-create-workspace.md). Log Analytics-arbetsytan måste tillhöra en av de [regioner som stöds.](vminsights-enable-overview.md#log-analytics)

6. Du får statusmeddelanden när konfigurationen utförs.

    ![Aktivera Azure Monitor för virtuella datorer som övervakar distributionsbearbetning](media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

## <a name="enable-monitoring-for-a-single-virtual-machine-scale-set"></a>Aktivera övervakning för en enda skalningsuppsättning för virtuella datorer

Så här aktiverar du övervakning av din Azure-skalningsuppsättning för virtuella datorer:

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Välj **skaluppsättningar för virtuella datorer**.

3. Välj en skaluppsättning för den virtuella datorn i listan.

4. På sidan för skalningsuppsättning för virtuell dator väljer du **Insikter** i avsnittet **Övervakning** och **aktiverar**sedan .

5. Om du vill använda en befintlig Log Analytics-arbetsyta på sidan **Insikter** väljer du den i listrutan.

    Listan förväljs till standardarbetsytan och platsen som den virtuella datorn distribueras till i prenumerationen. 

    ![Aktivera Azure Monitor för virtuella datorer för en skalningsuppsättning för virtuella datorer](media/vminsights-enable-single-vm/enable-vminsights-vmss-portal.png)

    >[!NOTE]
    >Information om hur du skapar en ny Log Analytics-arbetsyta för att lagra övervakningsdata från skaluppsättningen för den virtuella datorn finns i [Skapa en Log Analytics-arbetsyta](../learn/quick-create-workspace.md). Log Analytics-arbetsytan måste tillhöra en av de [regioner som stöds.](vminsights-enable-overview.md#log-analytics)

6. Du får statusmeddelanden när konfigurationen utförs.

    >[!NOTE]
    >Om du använder en manuell uppgraderingsmodell för skalningsuppsättningen uppgraderar du instanserna för att slutföra installationen. Du kan starta uppgraderingarna från sidan **Instanser** i avsnittet **Inställningar.**
    
    ![Aktivera Azure Monitor för virtuella datorer som övervakar distributionsbearbetning](media/vminsights-enable-single-vm/onboard-vminsights-vmss-portal-status.png)

Nu när du har aktiverat övervakning för din virtuella dator eller virtuell dator skalningsuppsättning, är övervakningsinformationen tillgänglig för analys i Azure Monitor för virtuella datorer. 

## <a name="next-steps"></a>Nästa steg

* Information om hur du visar identifierade programberoenden finns i [Använda Azure Monitor för virtuella datorer Map](vminsights-maps.md). 
* Information om hur du identifierar flaskhalsar, övergripande användning och den virtuella datorns prestanda finns i [Visa Azure VM-prestanda](vminsights-performance.md).
