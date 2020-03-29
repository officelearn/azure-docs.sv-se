---
title: Skapa/anpassa återställningsplaner i Azure Site Recovery
description: Lär dig hur du skapar och anpassar återställningsplaner för haveriberedskap med hjälp av Azure Site Recovery-tjänsten.
ms.topic: how-to
ms.date: 01/23/2020
ms.openlocfilehash: 6540317324a9f0d9bccc046ecf95824d4128bd09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76705844"
---
# <a name="create-and-customize-recovery-plans"></a>Skapa och anpassa återställningsplaner

I den här artikeln beskrivs hur du skapar och anpassar en återställningsplan för redundans i [Azure Site Recovery](site-recovery-overview.md). Innan du börjar [kan du läsa mer](recovery-plan-overview.md) om återställningsplaner.

## <a name="create-a-recovery-plan"></a>Skapa en återställningsplan

1. I valvet för återställningstjänster väljer du **Återställningsplaner (Site Recovery)** > **+Återställningsplan**.
2. Ange ett namn för planen i **Skapa återställningsplan.**
3. Välj en källa och ett mål baserat på datorerna i planen och välj **Resurshanteraren** för distributionsmodellen. Källplatsen måste ha datorer som är aktiverade för redundans och återställning. 

    **Redundans** | **Källkod** | **Mål** 
   --- | --- | ---
   Azure till Azure | Välj Azure-region | Välj Azure-region
   VMware till Azure | Välj konfigurationsserver | Välj Azure
   Fysiska datorer till Azure | Välj konfigurationsserver | Välj Azure   
   Hyper-V till Azure | Välj hyper-V-webbplatsnamn | Välj Azure
   Hyper-V (hanteras av VMM) till Azure  | Välj VMM-servern | Välj Azure
  
    Observera följande:
    -  Du kan bara använda en återställningsplan för redundans från källplatsen till Azure. Du kan inte använda en återställningsplan för återställning efter fel från Azure.
    - Källplatsen måste ha datorer som är aktiverade för redundans och återställning. 
    - En återställningsplan kan innehålla datorer med samma källa och mål. 
    - Du kan inkludera virtuella datorer med VMware och virtuella virtuella datorer med hyper-v som hanteras av VMM i samma plan.
    - Virtuella datorer och fysiska servrar kan finnas i samma plan.

4. I **Välj objekt virtuella datorer**väljer du de datorer (eller replikeringsgruppen) som du vill lägga till i planen. Klicka sedan på **OK**.
    - Maskiner läggs till standardgrupp (grupp 1) i planen. Efter redundans startar alla datorer i den här gruppen samtidigt.
    - Du kan bara välja datorer på de käll- och målplatser som du har angett. 
5. Klicka på **OK** för att skapa planen.

## <a name="add-a-group-to-a-plan"></a>Lägga till en grupp i en plan

Du skapar ytterligare grupper och lägger till datorer i olika grupper så att du kan ange olika beteenden grupp för grupp. Du kan till exempel ange när datorer i en grupp ska starta efter redundans eller ange anpassade åtgärder per grupp.

1. Högerklicka på planen > **anpassa**i **återställningsplaner.** Som standard finns alla datorer som du har lagt till i den som standardgrupp 1 när du har skapat en plan.
2. Klicka på **+Grupp**. Som standard numreras en ny grupp i den ordning den läggs till. Du kan ha upp till sju grupper.
3. Välj den dator som du vill flytta till den nya gruppen, klicka på **Ändra grupp**och välj sedan den nya gruppen. Du kan också högerklicka på gruppnamnet > **skyddat objekt**och lägga till datorer i gruppen. En dator eller replikeringsgrupp kan bara tillhöra en grupp i en återställningsplan.


## <a name="add-a-script-or-manual-action"></a>Lägga till ett skript eller en manuell åtgärd

Du kan anpassa en återställningsplan genom att lägga till ett skript eller en manuell åtgärd. Tänk på följande:

- Om du replikerar till Azure kan du integrera Azure automation runbooks i din återställningsplan. [Läs mer](site-recovery-runbook-automation.md).
- Om du replikerar virtuella hyper-V-datorer som hanteras av System Center VMM kan du skapa ett skript på den lokala VMM-servern och inkludera det i återställningsplanen.
- När du lägger till ett skript läggs en ny uppsättning åtgärder till för gruppen. En uppsättning försteg för grupp 1 skapas till exempel med namnet *Grupp 1: försteg*. Alla försteg visas i den här uppsättningen. Du kan bara lägga till ett skript på den primära platsen om du har distribuerat en VMM-server.
- Om du lägger till en manuell åtgärd, när återställningsplanen körs, stoppas den vid den punkt där du infogade den manuella åtgärden. I en dialogruta uppmanas du att ange att den manuella åtgärden har slutförts.
- Om du vill skapa ett skript på VMM-servern följer du instruktionerna i den [här artikeln](hyper-v-vmm-recovery-script.md).
- Skript kan användas under redundans till den sekundära platsen och under återställningen från den sekundära platsen till den primära platsen. Supporten beror på replikeringsscenariot:
    
    **Scenario** | **Redundans** | **Failback**
    --- | --- | --- 
    Azure till Azure  | Runbook | Runbook
    VMware till Azure | Runbook | Ej tillämpligt 
    Hyper-V med VMM till Azure | Runbook | Skript
    Hyper-V-plats till Azure | Runbook | Ej tillämpligt
    VMM till sekundär VMM | Skript | Skript

1. I återställningsplanen klickar du på det steg som åtgärden ska läggas till i och anger när åtgärden ska utföras:
    1. Om du vill att åtgärden ska utföras innan datorerna i gruppen startas efter redundans väljer du **Lägg till föråtgärd**.
    1. Om du vill att åtgärden ska utföras efter att datorerna i gruppen har börjat efter redundans väljer du **Lägg till inläggsåtgärd**. Om du vill flytta åtgärdens position väljer du knapparna **Flytta upp** eller **Flytta ned.**
2. I **åtgärden Infoga**väljer du **Skript** eller Manuell **åtgärd**.
3. Om du vill lägga till en manuell åtgärd gör du följande:
    1. Skriv in ett namn för åtgärden och skriv in åtgärdsinstruktioner. Den person som kör redundans kommer att se dessa instruktioner.
    1. Ange om du vill lägga till den manuella åtgärden för alla typer av redundans (Test, Redundans, Planerad redundans (om det är relevant)). Klicka sedan på **OK**.
4. Om du vill lägga till ett skript gör du följande:
    1. Om du lägger till ett VMM-skript väljer du **Redundans till VMM-skript**och i **Skriptsökväg** skriver du den relativa sökvägen till resursen. Om resursen till exempel \\ \<finns på VMMServerName>\MSSCVMMLibrary\RPScripts anger du sökvägen: \RPScripts\RPScript.PS1.
    1. Om du lägger till en Azure automation-körningsbok anger du **Azure Automation-kontot** där runbooken finns och väljer lämpligt **Azure Runbook Script**.
5. Kör en testväxling av återställningsplanen för att säkerställa att skriptet fungerar som förväntat.

## <a name="watch-a-video"></a>Se ett videoklipp

Titta på en video som visar hur du skapar en återställningsplan.


> [!VIDEO https://www.youtube.com/embed/1KUVdtvGqw8]

## <a name="next-steps"></a>Nästa steg

Läs mer om [att köra redundans .](site-recovery-failover.md)  

    
