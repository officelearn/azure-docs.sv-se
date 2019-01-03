---
title: Skapa och anpassa återställningsplaner för haveriberedskap med hjälp av Azure Site Recovery | Microsoft Docs
description: Lär dig mer om att skapa och anpassa återställningsplaner för haveriberedskap med hjälp av Azure Site Recovery-tjänsten.
author: rayne-wiselman
manager: carmonm
services: site-recovery
ms.service: site-recovery
ms.topic: article
ms.date: 12/27/2018
ms.author: raynew
ms.openlocfilehash: d52aa3b39a17c42c0f0e0cb669c69d336b41ba48
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/02/2019
ms.locfileid: "53973221"
---
# <a name="create-and-customize-recovery-plans"></a>Skapa och anpassa återställningsplaner

Den här artikeln beskriver hur du skapar och anpassar en återställningsplan i [Azure Site Recovery](site-recovery-overview.md). Innan du börjar [mer](recovery-plan-overview.md) om återställningsplaner.

## <a name="create-a-recovery-plan"></a>Skapa en återställningsplan

1. I Recovery Services-valv väljer **Återställningsplaner (Site Recovery)** > **+ återställningsplan**.
2. I **skapa återställningsplan**, ange ett namn för schemat.
3. Välj en källa och mål baserat på datorerna i planen och **Resource Manager** för distributionsmodellen. Källplatsen måste ha datorer som har aktiverats för redundans och återställning. 

   **Redundans** | **Källa** | **Mål** 
   --- | --- | ---
   Azure till Azure | Azure-region |Azure-region
   VMware till Azure | Konfigurationsserver | Azure
   Fysiska datorer till Azure | Konfigurationsserver | Azure   
   Hyper-V som hanteras av VMM till Azure  | Visningsnamn för VMM | Azure
   Hyper-V utan VMM till Azure | Hyper-V-platsnamn | Azure
   VMM till VMM |Eget namn för VMM | Visningsnamn för VMM 

   > [!NOTE]
   > En återställningsplan kan innehålla datorer med samma källa och mål. VMware och Hyper-V-datorer som hanteras av VMM kan inte vara i samma plan. Virtuella VMware-datorer och fysiska servrar kan vara i samma plan och där källan är en konfigurationsserver.

2. I **Välj objekt virtuella datorer**, Välj datorer (eller replikeringsgrupp) som du vill lägga till i planen. Klicka sedan på **OK**.
    - Datorer har lagts till standardgruppen (grupp 1) i planen. Efter en redundansväxling starta alla datorer i den här gruppen på samma gång.
    - Du kan bara välja datorer är i käll- och målplatserna som du har angett. 
1. Klicka på **OK** för att skapa planen.

## <a name="add-a-group-to-a-plan"></a>Lägga till en grupp till en plan

Du skapar fler grupper och lägga till datorer i olika grupper så att du kan ange olika sätt beroende på basis av grupp för grupp. Du kan till exempel ange när datorer i en grupp ska starta efter redundans eller ange anpassade åtgärder per grupp.

1. I **Återställningsplaner**, högerklickar på planen > **anpassa**. Som standard när du har skapat en plan placerade alla datorer som du lade till är i grupp 1.
2. Klicka på **+ grupp**. Som standard är en ny grupp numrerade i den ordning som den har lagts till. Du kan ha upp till sju grupper.
3. Välj den dator som du vill flytta till den nya gruppen, klicka på **ändra grupp**, och välj sedan den nya gruppen. Du kan också högerklicka på namnet på > **skyddat objekt**, och lägga till datorer i gruppen. En dator eller replikering grupp kan bara höra till en grupp i en återställningsplan.


## <a name="add-a-script-or-manual-action"></a>Lägg till ett skript eller en manuell åtgärd

Du kan anpassa en återställningsplan genom att lägga till ett skript eller en manuell åtgärd. Tänk på följande:

- Om du replikerar till Azure kan du integrera Azure automation-runbooks i din plan. [Läs mer](site-recovery-runbook-automation.md).
- Om du replikerar virtuella Hyper-V-datorer som hanteras av System Center VMM kan du skapa ett skript på den lokala VMM-servern och inkludera den i återställningsplanen.
- När du lägger till ett skript, läggs en ny uppsättning åtgärder för gruppen. Till exempel en uppsättning steg före för grupp 1 skapas med namnet *grupp 1: steg före*. Alla steg före visas i den här uppsättningen. Du kan lägga till ett skript på den primära platsen bara om du har en VMM-server distribueras.
- Om du lägger till en manuell åtgärd när en återställningsplan körs, stoppas vid tidpunkten då du infogat manuell åtgärd. En dialogruta där uppmanas du att ange att den manuella åtgärden utfördes.
- Om du vill skapa ett skript på VMM-servern, följer du anvisningarna i [i den här artikeln](hyper-v-vmm-recovery-script.md).
- Skript kan användas under redundansväxling till den sekundära platsen och under återställning efter fel från den sekundära platsen till primärt. Support är beroende av replikeringsscenariot:
    
    **Scenario** | **Redundans** | **Återställning efter fel**
    --- | --- | --- 
    Azure till Azure  | Runbook | Runbook
    VMware till Azure | Runbook | Ej tillämpligt 
    Hyper-V med VMM till Azure | Runbook | Skript
    Hyper-V-plats till Azure | Runbook | Ej tillämpligt
    VMM till sekundär VMM | Skript | Skript

1. I återställningsplanen, klickar du på de steg som åtgärden som ska läggas till och ange när åtgärden bör ske: en. Om du vill att åtgärden ska utföras innan datorer i gruppen startas efter en redundansväxling, Välj **Lägg till åtgärd**.
    b. Om du vill att åtgärden ska inträffa när datorer i gruppen början efter en redundansväxling, Välj **Lägg till efteråtgärd**. Om du vill flytta positionen för åtgärden, Välj den **Flytta upp** eller **Flytta ned** knappar.
2. I **Infoga åtgärd**väljer **skriptet** eller **manuell åtgärd**.
3. Gör följande om du vill lägga till en manuell åtgärd ”en. Skriv ett namn för åtgärden och anger åtgärdsanvisningar. Den person som kör redundansväxlingen visas dessa instruktioner.
    b. Ange om du vill lägga till den manuella åtgärden för alla typer av redundans (Test, växling vid fel, planerad redundans (om det behövs)). Klicka sedan på **OK**.
4. Om du vill lägga till ett skript, gör du följande: en. Om du vill lägga till ett VMM-skript, väljer **redundans till VMM skript**, och i **skriptets sökväg** skriver du den relativa sökvägen till resursen. Exempel: om resursen finns på \\ <VMMServerName>\MSSCVMMLibrary\RPScripts, anger du sökvägen: \RPScripts\RPScript.PS1.
    b. Om du vill lägga till ett Azure automation köra bok, ange den **Azure Automation-konto** där runbook finns och välja lämplig **Azure-Runbookskript**.
5. Köra ett redundanstest av återställningsplan för att se till att skriptet fungerar som förväntat.

## <a name="watch-a-video"></a>Titta på en video

Titta på en video som visar hur du skapar en återställningsplan.


> [!VIDEO https://www.youtube.com/embed/1KUVdtvGqw8]

## <a name="next-steps"></a>Nästa steg

Läs mer om [köra redundansväxlingar](site-recovery-failover.md).  

    
