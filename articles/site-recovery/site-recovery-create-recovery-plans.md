---
title: "Skapa och anpassa återställningsplaner för redundans och återställning i Azure Site Recovery | Microsoft Docs"
description: "Beskriver hur du skapar och anpassa återställningsplaner i Azure Site Recovery växla över och återställa virtuella datorer och fysiska servrar"
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
ms.openlocfilehash: 9839a989246b28c1a194b8d1f0e99c1bd80ac2e5
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2018
---
# <a name="create-recovery-plans"></a>Skapa återställningsplaner


Den här artikeln innehåller information om att skapa och anpassa återställningsplaner i [Azure Site Recovery](site-recovery-overview.md).

Skriv dina kommentarer eller frågor längst ned i den här artikeln eller i [Azure Recovery Services-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

 Skapa återställningsplaner för att göra följande:

* Definiera grupper av datorer som redundansväxlar tillsammans och sedan starta tillsammans.
* Modellen beroenden mellan datorer genom att gruppera dem tillsammans i en återställning planera grupp. Till exempel för att växla över och få fram ett visst program, gruppera du alla virtuella datorer för programmet i samma grupp för återställning-plan.
* Kör en redundansväxling. Du kan köra en test, planerad eller oplanerad växling vid fel på en återställningsplan.

## <a name="why-use-recovery-plans"></a>Varför använda återställningsplaner?

Återställningsplaner när du planerar för en systematiskt återställningsprocessen genom att skapa små oberoende enheter som du kan hantera. Dessa enheter vanligtvis representerar ett program i din miljö. Återställningsplanen inte bara kan du definiera ordningen som de virtuella datorerna startar, men även hjälper dig att automatisera vanliga uppgifter under återställningen.


**I princip ett sätt att kontrollera att du har förberett för molnmigrering eller katastrofåterställning är genom att säkerställa att alla program i din egen är en del av en återställningsplan och var och en av återställningsplaner har testats för återställning till Microsoft Azure. Med den här beredskap du säkert sätt migrera eller redundans datacentrets klar till Microsoft Azure.**
 
Här följer tre viktiga värderingsförslag av en återställningsplan:

### <a name="model-an-application-to-capture-dependencies"></a>Modellen är ett program för att avbilda beroenden

En återställningsplan är en grupp av virtuella datorer vanligtvis som består av ett program som tillsammans med växling vid fel. Med hjälp av återställningsplanen konstruktioner, kan du utöka den här gruppen för att avbilda dina programspecifika egenskaper.
 
Låt oss ta exempel på en typisk tre nivåprogram med

* en SQL-serverdelen
* en mellanprogram
* webbplats med en klientdel

Återställningsplanen kan anpassas för att säkerställa att de virtuella datorerna kommer i rätt ordning efter en växling vid fel. SQL-backend måste komma först, mellanprogram ska hämtas nästa och webb-klientdel måste komma in senaste. Den här ordningen gör vissa att programmet fungerar genom att den tid som den sista virtuella datorn kommer in. Till exempel när mellanprogram finns, görs ett försök att ansluta till SQL-skikt och återställningsplanen har sett till att SQL-nivån körs redan. Klientdelar kommande senaste också innebär att slutanvändare inte ansluter till programmets URL av misstag tills alla komponenter är igång och att programmet är redo att acceptera begäranden. Du kan anpassa återställningsplanen så att lägga till grupper för att skapa dessa beroenden. Välj en virtuell dator och ändra dess grupp flyttas mellan grupper.

![Exempelplan för återställning](./media/site-recovery-create-recovery-plans/rp.png)

När du har slutfört anpassningen kan du visualisera hur återställningen. Här är ordningen på stegen som körs under växling vid fel för en återställningsplan:

* Det finns ett avstängning steg som försöker stänga av den virtuella maskiner lokalt (förutom i redundanstest där den primära platsen måste fortsätta att köras)
* Sedan utlöser redundans för alla virtuella datorer i återställningsplanen parallellt. Failover-steget förbereder diskar för virtuella datorer från replikerade data.
* Slutligen körs Autostart-grupperna i sin ordning starta de virtuella datorerna i varje grupp - grupp 1 först, sedan Grupp2 och slutligen grupp 3. Om det finns flera virtuella datorer i valfri grupp (till exempel en belastningsutjämnad web-klientdel) startat för alla upp parallellt.

**Sekvensering mellan grupper säkerställer att beroenden mellan olika programnivåerna gäller och parallellitet i förekommande fall förbättrar Återställningstidsmålet för återställning-program.**

   > [!NOTE]
   > Datorer som ingår i en enda grupp redundansväxlas parallellt. Datorer som ingår i olika grupper kommer redundans i oder grupperna. När alla datorer i grupp 1 har redundansväxlats och startat, startar datorer i Grupp2 sina växling vid fel.

### <a name="automate-most-recovery-tasks-to-reduce-rto"></a>Automatisera aktiviteter för de flesta återställning för att minska RTO

Det kan vara komplicerat att återställa stora program. Det är också svårt att komma ihåg exakt anpassning steg efter växling vid fel eller migrering. Ibland är det inte du, men någon annan som är medveten om program-krångligheter som ska utlösa en redundansväxling. Kom ihåg att det är svårt att för många steg i gånger kaotisk och tillförlitligt. En återställningsplan ger dig ett sätt att automatisera nödvändiga åtgärder du måste vidta vid varje steg med hjälp av Microsoft Azure Automation-runbooks. Med runbooks, kan du automatisera vanliga återställningsuppgifter som de exempel som anges nedan. För de aktiviteter som kan automatiseras ge återställningsplaner också dig möjlighet att infoga manuella åtgärder.

* Uppgifter på den virtuella Azure-datorn efter redundans – dessa krävs normalt så att du kan ansluta till den virtuella datorn, till exempel:
    * Skapa en offentlig IP på virtuella datorer efter redundans
    * Tilldela en NSG till den via virtuella datorns nätverkskort
    * Lägg till en belastningsutjämnare i en tillgänglighetsuppsättning
* Uppgifter i den virtuella datorn efter redundans – dessa konfigurera programmet så att den fortsätter att fungera i den nya miljön, till exempel:
    * Ändra anslutningssträngen för databasen i den virtuella datorn
    * Ändra web server/konfigurationsregler

**Du kan uppnå redundans med ett klick och optimera Återställningstidsmålet med en fullständig återställningsplan som automatiserar post återställningsuppgifter med hjälp av automation-runbooks.**

### <a name="test-failover-to-be-ready-for-a-disaster"></a>Testa redundans för en katastrofåterställning

En återställningsplan kan användas för att utlösa både en växling vid fel eller ett redundanstest. Du bör alltid genomföra ett redundanstest för programmet innan du utför en växling vid fel. Testa redundans hjälper dig att kontrollera om programmet kommer på återställningsplatsen.  Om du har missat något, kan du enkelt aktivera rensning och gör om redundanstestningen. Gör om testningen flera gånger tills du vet med säkerhet som programmet återställer smidigt.

![Testplan för återställning](./media/site-recovery-create-recovery-plans/rptest.png)

**Varje program är olika och du behöver för att skapa återställningsplaner som är anpassade för varje. Även i den här dynamiska datacenter världen, hela program och deras beroenden tiden. Testa redundans dina program en gång i kvartalet för att kontrollera att återställningsplanen är aktuella.**

## <a name="how-to-create-a-recovery-plan"></a>Så här skapar du en återställningsplan

1. Klicka på **Återställningsplaner** > **skapa återställningsplan**.
   Ange ett namn för återställningsplanen, och en källa och mål. Källplatsen måste ha virtuella datorer som är aktiverade för redundans och återställning. Välj en källa och mål baserat på de virtuella datorerna som du vill ska ingå i återställningsplanen. 

   |Scenario                   |Källa               |Mål           |
   |---------------------------|---------------------|-----------------|
   |Azure till Azure             |Azure-region         |Azure-region     |
   |VMware till Azure            |Konfigurationsservern |Azure            |
   |VMM till Azure               |Eget namn för VMM    |Azure            |
   |Hyper-v-platsen till Azure      |Hyper-v-platsnamn    |Azure            |
   |Fysiska datorer till Azure |Konfigurationsservern |Azure            |
   |VMM till VMM                 |Eget namn för VMM    |Eget namn för VMM|

   > [!NOTE]
   > En återställningsplan kan innehålla virtuella datorer som har samma käll- och. Virtuella datorer i VMware och VMM kan inte ingå i samma återställningsplan. Virtuella VMware-datorer och fysiska datorer kan dock lägga till samma plan som källa för båda är en konfigurationsserver.

2. I **Välj virtuella datorer**, Välj virtuella datorer (eller replikeringsgrupp) som du vill lägga till i standardgruppen (grupp 1) i återställningsplanen. Endast de virtuella datorer som skyddades på källan (som markerats i återställningsplanen) och skyddas till mål (som markerats i återställningsplanen) tillåts för val.

## <a name="how-to-customize-and-extend-recovery-plans"></a>Hur du anpassar och utöka återställningsplaner

Du kan anpassa och utöka återställningsplaner genom att gå till Site Recovery recovery plan resursbladet och klicka på fliken Anpassa.

Du kan anpassa och utöka återställningsplaner:

- **Lägga till nya grupper**– lägga till ytterligare återställningspunkter planeringsgrupper (upp till sju) i standardgruppen och sedan lägga till flera datorer eller replikgrupper grupperna recovery plan. Grupper är numrerade i den ordning i vilken du lägger till dem. En virtuell dator eller en replikeringsgrupp kan bara ingå i en återställning plan gruppen.
- **Lägg till en manuell åtgärd**– du kan lägga till manuella åtgärder som körs före eller efter en återställning plan grupp. När återställningsplanen körs, stoppas på den plats där du har infogat åtgärden manuellt. En dialogruta där uppmanas du att ange att den manuella åtgärden har slutförts.
- **Lägga till ett skript**– du kan lägga till skript som körs före eller efter en återställning plan grupp. När du lägger till ett skript lägger den till en ny uppsättning åtgärder för gruppen. Till exempel en uppsättning före steg för grupp 1 kommer att skapas med namnet: grupp 1: innan steg. Alla före stegen finns i den här uppsättningen. Du kan bara lägga till ett skript på den primära platsen om du har en VMM-server distribueras. [Läs mer](site-recovery-how-to-add-vmmscript.md).
- **Lägg till Azure-runbooks**– du kan utöka återställningsplaner med Azure-runbooks. Till exempel att automatisera uppgifter eller skapa enda steg återställning. [Läs mer](site-recovery-runbook-automation.md).


## <a name="how-to-add-a-script-runbook-or-manual-action-to-a-plan"></a>Hur du lägger till ett skript, runbook eller manuell åtgärd till en plan

Du kan lägga till ett skript eller en manuell åtgärd plan standardgruppen återställning när du har lagt till virtuella datorer eller replikeringsgrupper till den och skapa planen.

1. Öppna återställningsplanen.
2. Klicka på ett objekt i den **steg** listan och klicka sedan på **skriptet** eller **manuell åtgärd**.
3. Ange om du vill lägga till det skript eller åtgärder före eller efter det valda objektet. För att flytta positionen för skriptet uppåt eller nedåt, Använd den **Flytta upp** och **Flytta ned** knappar.
4. Om du lägger till ett skript i VMM, väljer **redundans till VMM-skript**. I **skriptsökvägen**, skriver du den relativa sökvägen till resursen. I VMM exemplet nedan, anger du sökvägen: **\RPScripts\RPScript.PS1**.
5. Om du lägger till en Azure automation kör book ange Azure Automation-konto där runbook finns och välj lämplig Azure runbook-skriptet.
6. Kontrollera att skriptet gör fungerar som förväntat, du en redundansväxling i återställningsplanen.

Skript eller runbook-alternativ är endast tillgängligt i följande scenarier när du utför en växling vid fel eller återställning efter fel. En manuell åtgärd är tillgänglig för både redundans och återställning efter fel.


|Scenario               |Redundans |Återställning efter fel |
|-----------------------|---------|---------|
|Azure till Azure         |Runbooks |Runbook  |
|VMware till Azure        |Runbooks |Ej tillämpligt       | 
|VMM till Azure           |Runbooks |Skript   |
|Hyper-v-platsen till Azure  |Runbooks |Ej tillämpligt       |
|VMM till VMM             |Skript   |Skript   |


## <a name="next-steps"></a>Nästa steg

[Lär dig mer](site-recovery-failover.md) om att köra redundansväxlingar.

Det här videoklippet om du vill se återställningsplan i åtgärden.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]
