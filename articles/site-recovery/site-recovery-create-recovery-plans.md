---
title: "Skapa och anpassa en återställningsplan för redundans och återställning i Azure Site Recovery | Microsoft Docs"
description: "Lär dig hur du skapar och anpassar i Azure Site Recovery-återställningsplaner. Den här artikeln beskriver hur du växla över och återställa virtuella datorer och fysiska servrar."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 72408c62-fcb6-4ee2-8ff5-cab1218773f2
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 01/26/2018
ms.author: raynew
ms.openlocfilehash: 6ad82a8a2f8e16ab794ba90c109904bd45cb6b89
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="create-a-recovery-plan-by-using-site-recovery"></a>Skapa en återställningsplan med Site Recovery

Den här artikeln beskriver hur du skapar och anpassar en återställningsplan i [Azure Site Recovery](site-recovery-overview.md).

Skapa en återställningsplan för att göra följande:

* Definiera grupper av datorer som redundansväxlar tillsammans och starta sedan tillsammans.
* Modellen beroenden mellan datorer genom att gruppera dem i en grupp för dataåterställning plan. Innehåller till exempel alla virtuella datorer för programmet i samma grupp för återställning-plan för att växla över och få fram ett visst program.
* Kör en redundansväxling. Du kan köra en test, planerad eller oplanerad växling vid fel på en återställningsplan.

## <a name="why-use-a-recovery-plan"></a>Varför ska jag använda en återställningsplan

En återställningsplan kan hjälpa dig att förbereda en systematiskt återställningsprocessen genom att skapa små oberoende enheter som du kan hantera. Enheterna som representerar vanligtvis ett program i din miljö. Med hjälp av en återställningsplan kan du definiera ordningen som de virtuella datorerna ska starta. Du kan också använda en återställningsplan för att automatisera vanliga uppgifter under återställningen.

> [!TIP]
> Ett sätt att kontrollera att du har förberett för molnet migrering eller disaster recovery är att säkerställa att var och en av dina program är en del av en återställningsplan. Kontrollera också att varje återställningsplan testas för återställning till Azure. Med den här beredskap du säkert sätt migrera eller växlar över ditt fullständiga datacenter till Azure.
 
En återställningsplan har tre viktiga värderingsförslag:

* Modellen är ett program för att avbilda beroenden.
* Automatisera aktiviteter för de flesta återställning för att minska Återställningstidsmål.
* Testa redundans för en katastrofåterställning.

### <a name="model-an-application-to-capture-dependencies"></a>Modellen är ett program för att avbilda beroenden

En återställningsplan är en grupp med virtuella datorer som vanligtvis utgör ett program och försummat över tillsammans. Med hjälp av återställningsplan konstruktioner, kan du utöka en grupp för dataåterställning plan för att avbilda dina programspecifika egenskaper. 

I den här artikeln använder vi ett typiskt trelagers-program som kan ha en SQL tillbaka slutet mellanprogram och en frontwebb. Du kan anpassa återställningsplanen för att säkerställa att de virtuella datorerna startar i rätt ordning efter en redundansväxling. SQL serverdelen bör starta först, mellanprogram ska starta nästa och frontwebben ska starta senaste. Den här ordningen säkerställer att programmet fungerar genom att den tid som den sista virtuella datorn startar. 

Till exempel när mellanprogram startar försöker den ansluta till SQL-nivån. Återställningsplanen ser till att SQL-nivån körs redan. Med en front-end-server start senast hjälper också till att kontrollera att slutanvändare inte ansluta till programmets URL innan alla komponenter som är igång och körs och programmet är redo att acceptera begäranden. Anpassa återställningsplanen så att lägga till grupper för att skapa dessa beroenden, och välj en virtuell dator. Ändra den virtuella datorns grupp om du vill flytta en virtuell dator mellan grupper.

![Skärmbild av ett exempel återställningsplan i Site Recovery](./media/site-recovery-create-recovery-plans/rp.png)

När du har slutfört anpassningen kan du visualisera hur återställningen. Här är ordningen på stegen som utförs under växling vid fel för en återställningsplan:

1. En avstängning försöker stänga av den virtuella maskiner lokalt. (Förutom i ett redundanstest under vilken den primära platsen måste fortsätta att köras.)
2. Avstängning försöket utlöser redundans för alla virtuella datorer i återställningsplanen parallellt. Failover-steget förbereder virtuella diskar med hjälp av replikerade data.
3. Start-grupper körs i sin ordning och starta virtuella datorer i varje grupp. Först grupp 1 utförs, Grupp2 kör och slutligen grupp 3 körs. Om det finns fler än en virtuell dator i valfri grupp (till exempel en belastningsutjämnad frontwebb), starta alla virtuella datorer parallellt.

> [!TIP]
> Sekvensering mellan grupper säkerställer att beroenden mellan olika programnivåerna gäller. Parallellitet förbättrar där det är lämpligt att använda, Återställningstidsmålet för återställning-program.

   > [!NOTE]
   > Datorer som ingår i en grupp inte över parallellt. Datorer som ingår i olika grupper växlar över i den ordning grupperna. Datorer i Grupp2 starta sina redundans endast när alla datorer i grupp 1 har redundansväxlats och igång.

### <a name="automate-most-recovery-tasks-to-reduce-rto"></a>Automatisera aktiviteter för de flesta återställning för att minska RTO

Det kan vara komplicerat att återställa stora program. Med många manuella steg för att komma ihåg i gånger kaotisk är svår och gör processen felbenägna. Dessutom kan det vara någon som är medveten om program-krångligheter som utlöser verkligen redundansen. 

Du kan använda en återställningsplan för att automatisera nödvändiga åtgärder som du måste vidta vid varje steg. Du kan konfigurera nödvändiga åtgärder med hjälp av Azure Automation-runbooks. Med runbooks, kan du automatisera vanliga åtgärder för återställning, som uppgifterna i följande exempel. Du kan infoga manuella åtgärder i din återställningsplaner för aktiviteter som kan automatiseras.

* **Uppgifter på virtuella Azure-datorn postredundans**: dessa uppgifter krävs normalt för att kunna ansluta till den virtuella datorn. Exempel:
    * Skapa en offentlig IP-adress på virtuella datorns postredundans.
    * Tilldela en nätverkssäkerhetsgrupp till nätverkskortet på den virtuella datorn kunde inte över.
    * Lägg till en belastningsutjämnare i en tillgänglighetsuppsättning.
* **Uppgifter i virtuella datorns postredundans**: Konfigurera uppgifterna om programmet så att den fortsätter att fungera i den nya miljön. Exempel:
    * Ändra anslutningssträngen för databasen i den virtuella datorn.
    * Ändra webbserverns konfiguration eller regler.

> [!TIP]
> Uppnå redundans med ett klick och optimera RTO genom att skapa en fullständig återställningsplan som automatiserar aktiviteter efter återställning med hjälp av Automation-runbooks.

### <a name="test-failover-to-be-ready-for-a-disaster"></a>Testa redundans för en katastrofåterställning

Du kan använda en återställningsplan för att utlösa en växling vid fel eller ett redundanstest. Slutför alltid testa redundans på programmet innan du utför en växling vid fel. Testa hjälp för växling vid fel du kontrollera om programmet kommer på återställningsplatsen. Om du har missat något i din konfiguration, kan du enkelt aktivera rensning och gör om redundanstestningen. Gör du testar redundansen flera gånger tills du är säker på att programmet återställer smidigt.

![Skärmbild av ett exempel testa återställningsplan i Site Recovery](./media/site-recovery-create-recovery-plans/rptest.png)

> [!TIP]
> Eftersom varje program är unikt, måste du skapa återställningsplaner som är anpassade för varje program. 
>
> Även i dagens dynamiska, datacenter fokuserar miljö ändra program och deras beroenden ofta. För att säkerställa att återställningsplanen är aktuell, testa redundans för dina program varje kvartal.

## <a name="create-a-recovery-plan"></a>Skapa en återställningsplan

1. Välj **Återställningsplaner** > **skapa återställningsplan**.
   Ange ett namn för återställningsplanen, och en källa och mål. Källplatsen måste ha virtuella datorer som är aktiverade för redundans och återställning. Välj en källa och mål baserat på de virtuella datorerna som du vill ska ingå i återställningsplanen. 

   |Scenario                   |Källa               |Mål           |
   |---------------------------|---------------------|-----------------|
   |Azure till Azure             |Azure-region         |Azure-region     |
   |VMware till Azure            |Konfigurationsservern |Azure            |
   |Virtual Machine Manager (VMM) till Azure               |Visningsnamn för VMM    |Azure            |
   |Hyper-V-plats till Azure      |Hyper-V-platsnamn    |Azure            |
   |Fysiska datorer till Azure |Konfigurationsservern |Azure            |
   |VMM till VMM                 |Eget namn för VMM    |Visningsnamn för VMM|

   > [!NOTE]
   > En återställningsplan kan innehålla virtuella datorer som har samma käll- och. VMware och System Center Virtual Machine Manager (VMM) virtuella datorer får inte vara i samma återställningsplan. Men du kan lägga till VMware-datorer och fysiska datorer i samma återställningsplan. Källan för båda datorerna är i det här fallet en konfigurationsservern.

2. Under **Välj virtuella datorer**, Välj virtuella datorer (eller replikeringsgrupp) som du vill lägga till i standardgruppen (grupp 1) i återställningsplanen. Du kan bara välja virtuella datorer som skyddades på källan (som markerats i återställningsplanen) och som skyddas på målet (som markerats i återställningsplanen).

## <a name="customize-and-extend-recovery-plans"></a>Anpassa och utöka återställningsplaner

Om du vill anpassa och utöka återställningsplaner, går du till Site Recovery plan resurs återställningsfönstret. Välj den **anpassa** fliken. Du kan anpassa och utöka återställningsplaner med hjälp av följande alternativ:

- **Lägga till nya grupper**: du kan lägga till upp till sju ytterligare återställningspunkter planeringsgrupper i standardgruppen. Sedan du kan lägga till flera datorer eller replikgrupper grupperna recovery plan. Grupper är numrerade i den ordning i vilken du lägger till dem. Du kan inkludera en virtuell dator eller en replikeringsgrupp i endast en grupp för dataåterställning plan.
- **Lägg till en manuell åtgärd**: du kan lägga till manuella åtgärder som körs före eller efter en återställning plan grupp. När återställningsplanen körs, stoppas på den plats där du har infogat åtgärden manuellt. En dialogruta där uppmanas du att ange att den manuella åtgärden har slutförts.
- **Lägga till ett skript**: du kan lägga till skript som körs före eller efter en återställning plan grupp. När du lägger till ett skript lägger den till en ny uppsättning åtgärder för gruppen. Till exempel en uppsättning före steg för grupp 1 skapas med namnet *grupp 1: innan steg*. Alla före stegen finns i den här uppsättningen. Du kan lägga till ett skript på den primära platsen endast om du har en VMM-server distribueras. Mer information finns i [lägga till en VMM-skript till en återställningsplan](site-recovery-how-to-add-vmmscript.md).
- **Lägg till Azure-runbooks**: du kan utöka återställningsplaner med hjälp av Azure-runbooks. Du kan till exempel använda en runbook att automatisera uppgifter eller skapa enda steg återställning. Mer information finns i [lägga till Azure Automation-runbook till i återställningsplaner](site-recovery-runbook-automation.md).


## <a name="add-a-script-runbook-or-manual-action-to-a-plan"></a>Lägga till ett skript, runbook eller manuell åtgärd i en plan

När du har lagt till virtuella datorer eller replikeringsgrupper en standardgrupp recovery plan, du kan lägga till ett skript eller en manuell åtgärd gruppen återställning plan.

1. Öppna återställningsplanen.
2. I den **steg** väljer du ett objekt. Markera **skriptet** eller **manuell åtgärd**.
3. Ange om du vill lägga till det skript eller åtgärder före eller efter det valda objektet. För att flytta positionen för skriptet uppåt eller nedåt, Välj den **Flytta upp** eller **Flytta ned** knappar.
4. Om du lägger till ett skript i VMM, väljer **redundans till VMM-skript**. I **skriptsökvägen**, ange den relativa sökvägen till resursen. Till exempel **\RPScripts\RPScript.PS1**.
5. Om du lägger till en Automation-runbook ange Automation-konto där runbook finns. Välj Azure runbook-skriptet.
6. Gör en redundansväxling i återställningsplanen för att säkerställa att skriptet fungerar som förväntat.

Skript eller runbook alternativ finns bara i följande scenarier och under en växling vid fel eller återställning efter fel. En manuell åtgärd är tillgänglig både för redundans och återställning efter fel.


|Scenario               |Redundans |Återställning efter fel |
|-----------------------|---------|---------|
|Azure till Azure         |Runbook |Runbook  |
|VMware till Azure        |Runbook |Ej tillämpligt       | 
|VMM till Azure           |Runbook |Skript   |
|Hyper-V-plats till Azure  |Runbook |Ej tillämpligt       |
|VMM till VMM             |Skript   |Skript   |


## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [köra redundansväxlingar](site-recovery-failover.md).  
* Återställningsplan åtgärden finns i den här videon:
    
    > [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]
