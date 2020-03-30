---
title: Om återställningsplaner i Azure Site Recovery
description: Läs mer om återställningsplaner i Azure Site Recovery.
ms.topic: conceptual
ms.date: 01/23/2020
ms.openlocfilehash: beb92bd62d011ef8aaf304dbb769e7694e6d7e60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257776"
---
# <a name="about-recovery-plans"></a>Om återställningsplaner

Den här artikeln innehåller en översikt över återställningsplaner i [Azure Site Recovery](site-recovery-overview.md).

En återställningsplan samlar in datorer i återställningsgrupper i redundans. En återställningsplan hjälper dig att definiera en systematisk återställningsprocess genom att skapa små oberoende enheter som du kan växla över. En enhet representerar vanligtvis en app i din miljö.

- En återställningsplan definierar hur datorer växlar över och i vilken ordning de börjar efter redundans.
- Återställningsplaner används för redundans till Azure, men kan inte användas för återställning efter fel från Azure.
- Upp till 100 skyddade instanser kan läggas till i en återställningsplan.
- Du kan anpassa en plan genom att lägga till ordning, instruktioner och uppgifter i den.
- När en plan har definierats kan du köra en redundansväxling på den.
- Datorer kan refereras i flera återställningsplaner, där efterföljande planer hoppar över distributionen/starten av en dator om den tidigare har distribuerats med en annan återställningsplan.



### <a name="why-use-a-recovery-plan"></a>Varför använda en återställningsplan?

Använd återställningsplaner för att:

* Modellera en app runt dess beroenden.
* Automatisera återställningsuppgifter för att minska återställningstidens mål (RTO).
* Kontrollera att du är förberedd för migrering eller haveriberedskap genom att se till att dina appar ingår i en återställningsplan.
* Kör testundans om återställningsplaner för att säkerställa att haveriberedskap eller migrering fungerar som förväntat.


## <a name="model-apps"></a>Modellappar 
Du kan planera och skapa en återställningsgrupp för att fånga appspecifika egenskaper. Låt oss till exempel överväga ett typiskt program med tre nivåer med en SQL-server backend, middleware och en webb frontend. Vanligtvis anpassar du återställningsplanen så att datorer på varje nivå startar i rätt ordning efter redundans.

- SQL-serverdelen ska börja först, mellanprogram nästa, och slutligen webb frontend.
- Den här startordningen säkerställer att appen fungerar när den senaste datorn startar.
- Den här ordern säkerställer att när mellanprogrammet startar och försöker ansluta till SQL Server-nivån körs SQL Server-nivån redan. 
- Den här ordern säkerställer också att frontend-servern startar sist, så att slutanvändarna inte ansluter till appens URL innan alla komponenter är igång och appen är redo att acceptera begäranden.

Om du vill skapa den här ordningen lägger du till grupper i återställningsgruppen och lägger till datorer i grupperna.
- Där ordning anges används sekvensering. Åtgärder körs parallellt när det är lämpligt, för att förbättra programåterställning RTO.
- Maskiner i en enda grupp växlar över parallellt.
- Datorer i olika grupper växlar över i gruppordning, så att grupp 2-datorer startar sin redundans först efter att alla datorer i grupp 1 har misslyckats över och startat.

    ![Exempel på återställningsplan](./media/recovery-plan-overview/rp.png)

Med den här anpassningen på plats händer det här när du kör en redundansväxling på återställningsplanen: 

1. Ett avstängningssteg försöker stänga av lokala datorer. Undantaget är om du kör en test redundans, i vilket fall den primära platsen fortsätter att köras. 
2. Avstängningen utlöser en parallell redundans för alla datorer i återställningsplanen.
3. Redundans förbereder virtuella datordiskar med hjälp av replikerade data.
4. Startgrupperna körs i ordning och startar datorerna i varje grupp. Först löper grupp 1, sedan grupp 2, och slutligen grupp 3. Om det finns mer än en maskin i någon grupp, då alla maskiner startar parallellt.


## <a name="automate-tasks-in-recovery-plans"></a>Automatisera uppgifter i återställningsplaner

Återställa stora program kan vara en komplex uppgift. Manuella steg gör processen benägen att fel, och den person som kör redundans kanske inte är medveten om alla app krångligheter. Du kan använda en återställningsplan för att införa ordning och automatisera de åtgärder som behövs i varje steg med hjälp av Azure Automation-runbooks för redundans till Azure eller skript. För uppgifter som inte kan automatiseras kan du infoga pauser för manuella åtgärder i återställningsplaner. Det finns ett par typer av uppgifter som du kan konfigurera:

* **Uppgifter på den virtuella Azure-datorn efter redundans:** När du växlar över till Azure måste du vanligtvis utföra åtgärder så att du kan ansluta till den virtuella datorn efter redundans. Ett exempel: 
    * Skapa en offentlig IP-adress på den virtuella Azure-datorn.
    * Tilldela en nätverkssäkerhetsgrupp till nätverkskortet för Den virtuella Azure-datorn.
    * Lägg till en belastningsutjämnare i en tillgänglighetsuppsättning.
* **Aktiviteter i vm efter redundans:** Dessa uppgifter konfigurerar vanligtvis om appen som körs på datorn, så att den fortsätter att fungera korrekt i den nya miljön. Ett exempel:
    * Ändra databasanslutningssträngen inuti datorn.
    * Ändra webbserverns konfiguration eller regler.


### <a name="run-a-test-failover-on-recovery-plans"></a>Kör en testväxling på återställningsplaner

Du kan använda en återställningsplan för att utlösa en test redundans. Använd följande metodtips:

- Slutför alltid en testväxling på en app innan du kör en fullständig redundans. Testa redundans hjälper dig att kontrollera om appen kommer upp på återställningsplatsen.
- Om du upptäcker att du har missat något utlöser du en rensning och kör sedan om testundanundanställningen. 
- Kör en testväxling flera gånger, tills du är säker på att appen återställs smidigt.
- Eftersom varje app är unik måste du skapa återställningsplaner som är anpassade för varje program och köra en testväxling på varje.
- Appar och deras beroenden ändras ofta. Om du vill vara säkra på att återställningsplanerna är uppdaterade kör du en testundanställning för varje app varje kvartal.

    ![Skärmbild av en teståterställningsplan för exempel i Återställning av webbplats](./media/recovery-plan-overview/rptest.png)

## <a name="watch-a-recovery-plan-video"></a>Titta på en video för återställningsplan

Titta på ett snabbt exempel video som visar en på-klick redundans för en återställningsplan för en två-tier WordPress app.
    
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]



## <a name="next-steps"></a>Nästa steg

- [Skapa](site-recovery-create-recovery-plans.md) en återställningsplan.
- [Kör](site-recovery-failover.md) redundans. 
