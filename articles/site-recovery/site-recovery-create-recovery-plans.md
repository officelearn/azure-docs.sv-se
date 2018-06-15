---
title: Skapa och anpassa återställningsplaner för redundans och återställning i Azure Site Recovery | Microsoft Docs
description: Lär dig hur du skapar och anpassar i Azure Site Recovery-återställningsplaner. Den här artikeln beskriver hur du växla över och återställa virtuella datorer och fysiska servrar.
services: site-recovery
documentationcenter: ''
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 03/21/2018
ms.author: raynew
ms.openlocfilehash: e02672ea76eada2d660b20f91c4417019d4efc97
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
ms.locfileid: "30189842"
---
# <a name="create-and-customize-recovery-plans"></a>Skapa och anpassa återställningsplaner

Den här artikeln beskriver hur du skapar och anpassar en återställningsplan i [Azure Site Recovery](site-recovery-overview.md). Innan du börjar [mer](recovery-plan-overview.md) om återställningsplaner.

## <a name="create-a-recovery-plan"></a>Skapa en återställningsplan

1. Välj i Recovery Services-valvet **Återställningsplaner (Site Recovery)** > **+ Återställningsplanen**.
2. I **skapa återställningsplan**, ange ett namn för schemat.
3. Välj en källa och mål baserat på datorerna i planen och **Resource Manager** för distributionsmodell. Källplatsen måste ha datorer som har aktiverats för redundans och återställning. 

   **Redundans** | **Source** | **mål** 
   --- | --- | ---
   Azure till Azure | Azure-region |Azure-region
   VMware till Azure | Konfigurationsservern | Azure
   Fysiska datorer till Azure | Konfigurationsservern | Azure   
   Hyper-V som hanteras av VMM till Azure  | Visningsnamn för VMM | Azure
   Hyper-V utan VMM till Azure | Hyper-V-platsnamn | Azure
   VMM till VMM |Eget namn för VMM | Visningsnamn för VMM 

   > [!NOTE]
   > En återställningsplan kan innehålla datorer med samma källan och målet. VMware och Hyper-V-datorer som hanteras av VMM kan inte vara i samma plan. Virtuella VMware-datorer och fysiska servrar kan vara i samma plan och där källan är en konfigurationsserver.

2. I **Välj objekt virtuella datorer**, Välj datorer (eller replikeringsgrupp) som du vill lägga till i planen. Klicka sedan på **OK**.
    - Datorer har lagts till standardgruppen (grupp 1) i planen. Efter växling vid fel starta alla datorer i den här gruppen på samma gång.
    - Du kan bara välja datorer är i käll- och målplatserna som du angav. 
1. Klicka på **OK** att skapa planen.

## <a name="add-a-group-to-a-plan"></a>Lägga till en grupp till en plan

Du skapar fler grupper och lägga till datorer i olika grupper så att du kan ange olika beteenden för en grupp av grupper. Du kan till exempel ange när datorer i en grupp ska starta efter växling vid fel, eller ange anpassade åtgärder per grupp.

1. I **Återställningsplaner**, högerklickar på planen > **anpassa**. Finns som standard när du har skapat en plan för alla datorer som du lagt till i grupp 1.
2. Klicka på **+ grupp**. Som standard numreras en ny grupp i den ordning som den har lagts till. Du kan ha upp till sju grupper.
3. Välj den dator som du vill flytta till den nya gruppen, klicka på **ändra gruppen**, och välj sedan den nya gruppen. Du kan också högerklicka gruppnamnet > **skyddat objekt**, och lägga till datorer i gruppen. En dator eller replikering grupp kan bara tillhöra en grupp i en återställningsplan.


## <a name="add-a-script-or-manual-action"></a>Lägg till ett skript eller en manuell åtgärd

Du kan anpassa en återställningsplan genom att lägga till ett skript eller en manuell åtgärd. Tänk på följande:

- Om du replikerar till Azure kan du integrera Azure automation-runbooks i din återställningsplan. [Läs mer](site-recovery-runbook-automation.md).
- Om du replikerar virtuella Hyper-V-datorer hanteras av System Center VMM kan du skapa ett skript på den lokala VMM-servern och inkludera den i återställningsplanen.
- När du lägger till ett skript lägger den till en ny uppsättning åtgärder för gruppen. Till exempel en uppsättning före steg för grupp 1 skapas med namnet *grupp 1: innan steg*. Alla före stegen finns i den här uppsättningen. Du kan lägga till ett skript på den primära platsen endast om du har en VMM-server distribueras.
- Om du lägger till en manuell åtgärd när återställningsplanen körs stoppar den på den plats där du har infogat åtgärden manuellt. En dialogruta där uppmanas du att ange att den manuella åtgärden har slutförts.
- Om du vill skapa ett skript på VMM-servern, följ instruktionerna i [i den här artikeln](hyper-v-vmm-recovery-script.md).
- Skript kan tillämpas under växling vid fel på den sekundära platsen, och under återställning från den sekundära platsen på den primära servern. Stöd för beror på ditt scenario replikering:
    
    **Scenario** | **Redundans** | **Återställning efter fel**
    --- | --- | --- 
    Azure till Azure  | Runbook | Runbook
    VMware till Azure | Runbook | Ej tillämpligt 
    Hyper-V med VMM till Azure | Runbook | Skript
    Hyper-V-plats till Azure | Runbook | Ej tillämpligt
    VMM till sekundär VMM | Skript | Skript

1. I återställningsplanen, klicka på steg som åtgärden ska läggas till och ange när åtgärden ska ske: en. Om du vill att åtgärden ska utföras innan datorerna i gruppen startas efter en redundansväxling, Välj **lägga till före åtgärden**.
    b. Om du vill att åtgärden ska inträffa efter datorer i gruppen början efter växling vid fel väljer **Lägg till post åtgärd**. För att flytta positionen för åtgärden, Välj den **Flytta upp** eller **Flytta ned** knappar.
2. I **Infoga instruktionen**väljer **skriptet** eller **manuell åtgärd**.
3. Gör följande om du vill lägga till en manuell åtgärd ”en. Skriv ett namn för åtgärden och typ i åtgärden instruktioner. Den person som kör växling vid fel visas dessa instruktioner.
    b. Ange om du vill lägga till åtgärden manuellt för alla typer av växling vid fel (testa redundans, planerad växling vid fel (om relevant)). Klicka sedan på **OK**.
4. Om du vill lägga till ett skript, gör du följande: en. Om du vill lägga till ett skript i VMM, väljer **redundans till VMM-skript**, och i i **skriptsökvägen** anger den relativa sökvägen till resursen. Till exempel om resursen finns på \\ <VMMServerName>\MSSCVMMLibrary\RPScripts, anger du sökvägen: \RPScripts\RPScript.PS1.
    b. Om du vill lägga till en Azure automation kör adressbok, ange den **Azure Automation-kontot** där runbook finns och välj lämpliga **Azure Runbook-skriptet**.
5. Köra ett redundanstest i återställningsplanen så att skriptet fungerar som förväntat.

## <a name="watch-a-video"></a>Se en video

Se en video som visar hur du skapar en återställningsplan.


> [!VIDEO https://www.youtube.com/embed/1KUVdtvGqw8]

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [köra redundansväxlingar](site-recovery-failover.md).  

    
