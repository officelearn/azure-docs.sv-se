---
title: Om återställnings planer i Azure Site Recovery
description: Lär dig mer om att använda återställnings planer för haveri beredskap med Azure Site Recovery-tjänsten.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: 640f7258821718c5d406dd581d1f9c503e0d41c2
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/12/2019
ms.locfileid: "73953881"
---
# <a name="about-recovery-plans"></a>Om återställningsplaner

I den här artikeln beskrivs återställnings planer i [Azure Site Recovery](site-recovery-overview.md).

En återställnings plan samlar in datorer i återställnings grupper. Du kan anpassa en plan genom att lägga till order, instruktioner och uppgifter till den. När en plan har definierats kan du köra en redundansväxling på den.  Datorer kan refereras till i flera återställnings planer, där efterföljande planer kommer att hoppa över distributionen/starten av datorn om den tidigare har distribuerats via en annan återställnings plan.


## <a name="why-use-a-recovery-plan"></a>Varför ska jag använda en återställnings plan?

Med en återställnings plan kan du definiera en systematisk återställnings process genom att skapa små oberoende enheter som du kan redundansväxla. En enhet representerar vanligt vis en app i din miljö. En återställnings plan definierar hur datorer växlar över och i vilken ordning de startar efter redundansväxlingen. Använd återställnings planer för att:

* Modellera en app runt dess beroenden.
* Automatisera återställnings uppgifter för att minska RTO.
* Kontrol lera att du är för beredd för migrering eller haveri beredskap genom att se till att dina appar ingår i en återställnings plan.
* Kör redundanstest på återställnings planer för att säkerställa att haveri beredskap eller migreringen fungerar som förväntat.


## <a name="model-apps"></a>Modell program

> [!NOTE]
> Upp till 100 skyddade instanser kan läggas till i en återställnings plan.

Du kan planera och skapa en återställnings grupp för att avbilda appar-/regionsspecifika egenskaper. Vi kan till exempel tänka på ett typiskt program på tre nivåer med en SQL Server-Server del, mellanprogram och en webb klient del. Normalt anpassar du återställnings planen så att datorerna på varje nivå startar i rätt ordning efter redundansväxlingen.

- SQL-Dataservern ska starta först, mellanliggande nästa och slutligen webb klient delen.
- Den här start ordningen säkerställer att appen fungerar när den senaste datorn startar.
- Den här ordningen säkerställer att när mellanprogram startar och försöker ansluta till SQL Server nivå körs redan SQL Servers nivån. 
- Den här ordningen hjälper också till att se till att frontend-servern börjar sist, så att slutanvändarna inte ansluter till appens URL innan alla komponenter är igång och appen är redo att ta emot begär Anden.

Om du vill skapa den här ordningen lägger du till grupper i återställnings gruppen och lägger till datorer i grupperna.
- Om order anges används ordningsföljd. Åtgärder körs parallellt där det är lämpligt, för att förbättra program återställnings RTO.
- Datorer i en enda grupp växlar över parallellt.
- Datorer i olika grupper växlar över i grupp ordning, så att endast datorer i grupp 2 startar sin redundans när alla datorer i grupp 1 har redundansväxlats och startat.

    ![Exempel på återställnings plan](./media/recovery-plan-overview/rp.png)

Med den här anpassningen på plats händer följande när du kör en redundansväxling i återställnings planen: 

1. Ett avstängnings steg försöker stänga av de lokala datorerna. Undantaget är om du kör ett redundanstest, i vilket fall den primära platsen fortsätter att köras. 
2. Avstängningen utlöser en parallell redundansväxling av alla datorer i återställnings planen.
3. Redundansväxlingen förbereder virtuella dator diskar med replikerade data.
4. Start grupper körs i ordning och startar datorerna i varje grupp. Först, grupp 1 körs, sedan grupp 2 och slutligen grupp 3. Om det finns fler än en dator i en grupp börjar alla datorer parallellt.


## <a name="automate-tasks"></a>Automatisera uppgifter

Det kan vara en komplicerad uppgift att återskapa stora program. Manuella steg gör processen fel och den person som kör redundansväxlingen kanske inte är medveten om all app-erna. Du kan använda en återställnings plan för att införa order och automatisera de åtgärder som krävs i varje steg, använda Azure Automation runbooks för redundans till Azure eller skript. För uppgifter som inte kan automatiseras kan du infoga pauser för manuella åtgärder i återställnings planer. Det finns några typer av uppgifter som du kan konfigurera:

* **Aktiviteter på den virtuella Azure-datorn efter redundansväxlingen**: när du växlar över till Azure måste du vanligt vis utföra åtgärder så att du kan ansluta till den virtuella datorn efter redundansväxlingen. Exempel: 
    * Skapa en offentlig IP-adress på den virtuella Azure-datorn.
    * Tilldela en nätverks säkerhets grupp till nätverkskortet på den virtuella Azure-datorn.
    * Lägg till en belastningsutjämnare i en tillgänglighets uppsättning.
* **Aktiviteter i den virtuella datorn efter redundansväxlingen**: dessa aktiviteter omkonfigurerar vanligt vis appen som körs på datorn, så att den fortsätter att fungera korrekt i den nya miljön. Exempel:
    * Ändra databas anslutnings strängen i datorn.
    * Ändra webb Server konfigurationen eller reglerna.


## <a name="test-failover"></a>Redundanstest

Du kan använda en återställnings plan för att utlösa redundanstest. Använd följande metod tips:

- Slutför alltid ett redundanstest på en app innan du kör en fullständig redundansväxling. Med redundanstest kan du kontrol lera om appen kommer upp på återställnings platsen.
- Om du tycker att du har missat något, Utlös en rensning och kör sedan redundanstest igen. 
- Kör ett redundanstest flera gånger tills du är säker på att appen återställs smidigt.
- Eftersom varje app är unik måste du skapa återställnings planer som anpassas för varje program och köra ett redundanstest på var och en.
- Appar och deras beroenden ändras ofta. För att säkerställa att återställnings planer är uppdaterade kan du köra ett redundanstest för varje app varje kvartal.

    ![Skärm bild av ett exempel på en test återställnings plan i Site Recovery](./media/recovery-plan-overview/rptest.png)

## <a name="watch-the-video"></a>Titta på videon

Titta på en snabb exempel video som visar en klickning vid fel på en WordPress-app med två nivåer.
    
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]



## <a name="next-steps"></a>Nästa steg

- [Skapa](site-recovery-create-recovery-plans.md) en återställnings plan.
- Lär dig mer om att [köra redundans](site-recovery-failover.md).  
