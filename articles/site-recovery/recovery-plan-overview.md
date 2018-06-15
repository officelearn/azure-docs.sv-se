---
title: Med hjälp av återställningsplaner i Azure Site Recovery | Microsoft Docs
description: Läs mer om i Azure Site Recovery-återställningsplaner.
services: site-recovery
documentationcenter: ''
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.date: 03/21/2018
ms.author: raynew
ms.openlocfilehash: 871c9e8404438f966cab2fc5ab782e254295569e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
ms.locfileid: "30181985"
---
# <a name="about-recovery-plans"></a>Om återställningsplaner

Den här artikeln beskriver återställningsplaner i [Azure Site Recovery](site-recovery-overview.md).

En återställningsplan samlar datorer i grupper för återställning. Du kan anpassa en plan genom att lägga till ordning, instruktioner och uppgifter till den. När en plan har definierats kan köra du en växling vid fel på den.





## <a name="why-use-a-recovery-plan"></a>Varför ska jag använda en återställningsplan?

En återställningsplan hjälper dig att definiera en systematiskt återställningsprocessen genom att skapa små oberoende enheter som du kan växla över. En enhet representerar vanligen en app i din miljö. En återställningsplan definierar hur datorerna ska växlas över och ordning som de startar efter växling vid fel. Använd återställningsplaner för att:

* Modellera en app runt dess beroenden.
* Automatisera återställningsuppgifter för att minska Återställningstidsmål.
- Kontrollera att du är förberedd för migrering eller katastrofåterställning genom att säkerställa att dina appar är en del av en återställningsplan.
* Kör testa redundans på återställningsplaner katastrofåterställning eller migrering fungerar som förväntat.


## <a name="model-apps"></a>Modellen appar

Du kan planera och skapa en grupp för dataåterställning för att avbilda app-specifik egenskaper. T.ex, nu ska vi titta ett typiskt trelagers-program med en SQLServer backend mellanprogram och en webbserver-klientdel. Normalt anpassa återställningsplanen så att datorer i varje nivå start i rätt ordning efter växling vid fel.

    - SQL-backend ska starta först mellanprogram nästa och slutligen web-klientdel.
    - Den här ordningen för start säkerställer att appen fungerar när den sista datorn startar.
    - Den här ordningen innebär att när mellanprogram startar och försöker ansluta till SQL Server-nivå, SQL Server-nivån körs redan. 
    - Den här ordningen hjälper också att kontrollera att frontend-servern startar senaste, så att slutanvändare inte ansluta till app-URL innan alla komponenter som är igång och körs och appen är redo att acceptera begäranden.

För att skapa den här ordningen, lägga till grupper i gruppen återställning och lägga till datorer i grupperna. 
    - Om ordning anges används ordningsföljd. Åtgärder som körs parallellt där det behövs för att förbättra programåterställning Återställningstidsmål.
    - Datorer i en grupp inte över parallellt.
    - Datorerna i olika grupper växlas över i Gruppordning, så att datorer i Grupp2 starta sina redundans endast när alla datorer i grupp 1 har redundansväxlats och igång.

    ![Exempel återställningsplan](./media/recovery-plan-overview/rp.png)

Med den här anpassningen för är det här vad som händer när du kör en växling vid fel på återställningsplanen: 

1. En avstängning försöker stänga av lokala datorer. Undantaget är om du kör ett redundanstest, då den primära platsen fortsätter att köras. 
2. Avstängningen utlöser en parallell redundans för alla datorer i återställningsplanen.
3. Redundans förbereder virtuella diskar med hjälp av replikerade data.
4. Start-grupper körs i ordning och starta datorer i varje grupp. Först körs grupp 1 sedan Grupp2 och slutligen grupp 3. Om det finns mer än en dator i en grupp, starta alla datorer parallellt.


## <a name="automate-tasks"></a>Automatisera uppgifter

Det kan vara komplicerat att återställa stora program. Manuella steg gör processen felbenägna och den person som kör en redundansväxling kan inte vara medveten om alla app krångligheter. Du kan använda en återställningsplan för att införa ordning och automatisera åtgärder som behövs på varje steg med hjälp av Azure Automation-runbooks för redundans till Azure eller skript. Du kan infoga pausar för manuella åtgärder i återställningsplaner för aktiviteter som kan automatiseras. Det finns ett par olika typer av aktiviteter som du kan konfigurera:

* **Uppgifter på virtuella Azure-datorn efter redundans**: när du växlar över till Azure, vanligtvis måste du utföra åtgärder så att du kan ansluta till den virtuella datorn efter redundans. Exempel: 
    * Skapa en offentlig IP-adress på Azure VM.
    * Tilldela en nätverkssäkerhetsgrupp till nätverkskortet på den virtuella Azure-datorn.
    * Lägg till en belastningsutjämnare i en tillgänglighetsuppsättning.
* **Uppgifter i VM efter redundans**: dessa uppgifter vanligtvis konfigurera appen körs på datorn så att den fortsätter att fungera i den nya miljön. Exempel:
    * Ändra anslutningssträngen för databasen på den virtuella datorn.
    * Ändra webbserverns konfiguration eller regler.


## <a name="test-failover"></a>Testa redundans

Du kan använda en återställningsplan som utlöser ett redundanstest. Använd följande metodtips:

- Slutför alltid testa redundans på en app, innan du kör en fullständig växling vid fel. Redundanstestning hjälpa dig att kontrollera om appen kommer på återställningsplatsen.
- Om du har missat något kan utlösa en ren in och kör sedan om testningen. 
- Köra ett redundanstest flera gånger, tills du är säker på att appen återställer smidigt.
- Eftersom varje app är unikt, måste du skapa återställningsplaner som är anpassade för varje program och köra ett redundanstest på varje.
- Appar och deras beroenden ändras ofta. Säkerställ är aktuella genom att köra ett redundanstest för varje app varje kvartal.

    ![Skärmbild av ett exempel testa återställningsplan i Site Recovery](./media/recovery-plan-overview/rptest.png)

## <a name="watch-the-video"></a>Titta på videon

Titta på ett enkelt exempel video som visar en-Klicka redundans för en två-lagers WordPress-appen.
    
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]



## <a name="next-steps"></a>Nästa steg

- [Skapa](site-recovery-create-recovery-plans.md) en återställningsplan.
* Lär dig mer om [köra redundansväxlingar](site-recovery-failover.md).  
