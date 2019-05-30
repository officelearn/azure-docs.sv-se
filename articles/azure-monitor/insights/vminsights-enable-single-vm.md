---
title: Aktivera Azure Monitor för virtuella datorer (förhandsversion) för utvärdering | Microsoft Docs
description: Den här artikeln beskrivs hur du aktiverar Azure Monitor för virtuella datorer för en enskild Azure-dator eller en VM-skalningsuppsättning i utvärderingssyfte.
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
ms.openlocfilehash: 673f153b551e4b0c89a564c96d6bd9819ca26f5d
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2019
ms.locfileid: "65524092"
---
# <a name="enable-azure-monitor-for-vms-preview-for-evaluation"></a>Aktivera Azure Monitor för virtuella datorer (förhandsversion) för utvärdering

Om du vill utvärdera Azure Monitor för virtuella datorer (förhandsversion) på ett litet antal virtuella Azure-datorer eller en enskild virtuell dator eller virtual machine scale Sets, är det enklaste och snabbaste sättet att aktivera övervakning i Azure Portal. I slutet av den här processen du kommer har börjat övervaka dem och lär dig om de har problem prestanda eller tillgänglighet. 

Innan komma igång, bör du granska den [krav](vminsights-enable-overview.md) och kontrollera din prenumeration och resurser som uppfyller kraven.  

## <a name="enable-monitoring-for-a-single-azure-vm"></a>Aktivera övervakning för en virtuell Azure-dator
Om du vill aktivera övervakning av Azure-VM i Azure-portalen, gör du följande:

1. Logga in på [Azure Portal](https://portal.azure.com).

1. Välj **virtuella datorer**.

1. Välj en virtuell dator i listan.

1. På sidan virtuell dator i den **övervakning** väljer **Insights (förhandsversion)** .

1. På den **Insights (förhandsversion)** väljer **Prova nu**.

    ![Aktivera Azure Monitor för virtuella datorer för en virtuell dator](./media/vminsights-enable-single-vm/enable-vminsights-vm-portal-01.png)

1. På den **Azure Monitor insikter Onboarding** om du har en befintlig Log Analytics-arbetsyta i samma prenumeration, markerar du den i den nedrullningsbara listan.  

    Listan förväljer standardarbetsytan och plats som den virtuella datorn har distribuerats till i prenumerationen. 

    >[!NOTE]
    >Om du vill skapa en ny Log Analytics-arbetsyta för att lagra övervakningsdata från den virtuella datorn följer du anvisningarna i [skapa en Log Analytics-arbetsyta](../../azure-monitor/learn/quick-create-workspace.md) i någon av regionerna som stöds visas [här](vminsights-enable-overview.md#log-analytics).

När du har aktiverat övervakning, kan det ta ungefär 10 minuter innan du kan visa hälsomått för den virtuella datorn.

![Aktivera Azure Monitor för virtuella datorer övervakning distributionsbearbetning](./media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

## <a name="enable-monitoring-for-a-single-virtual-machine-scale-set"></a>Aktivera övervakning för en enda VM-skalningsuppsättning

Om du vill aktivera övervakning av din Azure VM-skalningsuppsättning i Azure-portalen, gör du följande:

1. Logga in på [Azure Portal](https://portal.azure.com).

2. Välj **Virtual Machine Scale Sets**.

3. I listan, Välj en virtual machine scale Sets.

4. På den virtuella datorn scale Sets-sidan i den **övervakning** väljer **Insights (förhandsversion)** .

5. På den **Insights (förhandsversion)** om du har en befintlig Log Analytics-arbetsyta som du vill använda, markerar du den i den nedrullningsbara listan.

    Listan förväljer standardarbetsytan och plats som den virtuella datorn har distribuerats till i prenumerationen. 

    ![Aktivera Azure Monitor för virtuella datorer för en VM-skalningsuppsättning](./media/vminsights-enable-single-vm/enable-vminsights-vmss-portal-01.png)

    >[!NOTE]
    >Om du vill skapa en ny Log Analytics-arbetsyta för att lagra övervakningsdata från den virtuella datorn följer du anvisningarna i [skapa en Log Analytics-arbetsyta](../learn/quick-create-workspace.md) i någon av regionerna som stöds visas [här](vminsights-enable-overview.md#log-analytics).

När du har aktiverat övervakning, kan det ta ungefär 10 minuter innan du kan visa övervakningsdata för skalningsuppsättningen.

>[!NOTE]
>Om du använder en manuell uppgradering modell för din skalningsuppsättning måste du uppgradera instanser för att slutföra installationen.  Detta kan göras från sidan instanser under de **inställningar** avsnittet.

![Aktivera Azure Monitor för virtuella datorer övervakning distributionsbearbetning](./media/vminsights-enable-single-vm/onboard-vminsights-vmss-portal-status-01.png)

## <a name="next-steps"></a>Nästa steg

Nu när övervakning har aktiverats för din virtuella datorn eller VM-skalningsuppsättning, är den här informationen tillgänglig för analys med Azure Monitor för virtuella datorer. Läs hur du använder funktionen hälsotillstånd i [visa Azure Monitor för virtuella datorer Health](vminsights-health.md). Identifierade programberoenden finns [visa Azure Monitor för virtuella datorer kartan](vminsights-maps.md). För att identifiera flaskhalsar och totala användningen med din prestanda för virtuella datorer, se [visa Azure VM prestanda](vminsights-performance.md), eller om du vill visa identifierade programberoenden, se [visa Azure Monitor för virtuella datorer kartan](vminsights-maps.md).