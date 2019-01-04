---
title: Med hjälp av återställningsplaner för haveriberedskap med Azure Site Recovery | Microsoft Docs
description: Lär dig mer om hur du använder återställningsplaner för haveriberedskap med Azure Site Recovery-tjänsten.
author: rayne-wiselman
manager: carmonm
services: site-recovery
ms.service: site-recovery
ms.topic: article
ms.date: 12/27/2018
ms.author: raynew
ms.openlocfilehash: c7d66c389958aa3b5274a3d81f27f416308acdee
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/02/2019
ms.locfileid: "53975669"
---
# <a name="about-recovery-plans"></a>Om återställningsplaner

Den här artikeln beskriver återställningsplaner i [Azure Site Recovery](site-recovery-overview.md).

En återställningsplan samlar datorer i grupper för återställning. Du kan anpassa en plan genom att lägga till order, instruktioner och uppgifter till den. När en plan har definierats kan köra du en redundans på den.



## <a name="why-use-a-recovery-plan"></a>Varför ska jag använda en återställningsplan?

En återställningsplan hjälper dig att definiera en systematisk återställningsprocessen genom att skapa små oberoende enheter som du kan redundansväxla. En enhet motsvarar vanligtvis en app i din miljö. En återställningsplan definierar hur datorer växlas över och ordning som de startar efter en redundansväxling. Använd återställningsplaner för att:

* Modellera en app runt dess beroenden.
* Automatisera aktiviteter för återställning för att minska RTO.
- Kontrollera att du är förberedd för migrering och haveriberedskap genom att säkerställa att dina appar är en del av en återställningsplan.
* Kör redundanstestet på återställningsplaner, haveriberedskap och migrering fungerar som förväntat.


## <a name="model-apps"></a>Modellappar

Du kan planera och skapa en grupp för dataåterställning för att samla in app-specifika egenskaper. Till exempel anta att du har ett typiskt trelagers-program med en SQLServer-serverdel, mellanprogram och en webbservergrupp. Normalt anpassa återställningsplanen så att datorerna i varje nivå börjar i rätt ordning efter en redundansväxling.

    - SQL-serverdelen ska starta först mellanprogrammet nästa och slutligen webbservergrupp.
    - Den här startordningen säkerställer att appen fungerar när den sista datorn startar.
    - Den här ordningen säkerställer att när mellanprogrammet startar och försöker ansluta till SQL Server-nivå, SQL Server-nivå körs redan. 
    - Den här ordningen kan också se till att front-end-server börjar senaste, så att slutanvändare inte ansluta till den app-URL innan alla komponenter som är igång och körs och appen är redo att acceptera begäranden.

Om du vill skapa den här ordningen, lägga till grupper i gruppen återställning och lägga till datorer i grupper. 
    - Om ordning anges, används ordningsföljd. Åtgärder köras parallellt vid behov för att förbättra programåterställning RTO.
    - Datorer i en enda grupp växlas över parallellt.
    - Datorer i olika grupper växlas över i Gruppordning, så att Grupp2 datorer starta sina redundans endast när alla datorer i grupp 1 har redundansväxlats och igång.

    ![Exempel återställningsplan](./media/recovery-plan-overview/rp.png)

Med den här anpassningen på plats är här vad som händer när du kör en redundansväxling i återställningsplanen: 

1. Ett avstängning steg försöker stänga av lokala datorer. Undantaget är om du kör ett redundanstest, den primära platsen i så fall fortsätter att köras. 
2. Avstängningen utlöser en parallell redundans för alla datorer i återställningsplanen.
3. Redundansen förbereder virtuella diskar med hjälp av replikerade data.
4. Start-grupper körs i ordningen och starta datorer i varje grupp. Först körs grupp 1 sedan Grupp2 och slutligen grupp 3. Om det finns fler än en dator i någon grupp, startar alla datorer parallellt.


## <a name="automate-tasks"></a>Automatisera uppgifter

Återställa stora program kan vara en komplicerad uppgift. Manuella steg gör processen felbenägna och den person som kör redundans kanske inte är medveten om alla app krångla. Du kan använda en återställningsplan för att införa ordning och automatisera de åtgärder som behövs i varje steg med hjälp av Azure Automation-runbooks för redundans till Azure eller skript. Du kan infoga pauser för manuella åtgärder i återställningsplaner för uppgifter som inte kan automatiseras. Det finns ett par olika typer av uppgifter som du kan konfigurera:

* **Uppgifter för virtuella Azure-datorn efter redundans**: När du växlar över till Azure, vanligtvis måste du utföra åtgärder så att du kan ansluta till den virtuella datorn efter redundans. Exempel: 
    * Skapa en offentlig IP-adress för virtuella Azure-datorn.
    * Tilldela en nätverkssäkerhetsgrupp till nätverkskortet på den virtuella Azure-datorn.
    * Lägg till en belastningsutjämnare i en tillgänglighetsuppsättning.
* **Aktiviteter i virtuell dator efter redundans**: Dessa uppgifter konfigurera vanligtvis när appen körs på datorn, så att de fortsätter att fungera korrekt i den nya miljön. Exempel:
    * Ändra anslutningssträngen för databasen på datorn.
    * Ändra webbserverns konfiguration eller regler.


## <a name="test-failover"></a>Redundanstest

Du kan använda en återställningsplan för att utlösa ett redundanstest. Använd följande metodtips:

- Alltid göra ett redundanstest på en app innan du kör en fullständig växling vid fel. Redundanstestning hjälper dig att kontrollera om appen kommer på återställningsplatsen.
- Om du hittar du har missat något kan utlösa en ren in och kör sedan redundanstestningen. 
- Köra ett redundanstest flera gånger, tills du är säker på att appen återställer smidigt.
- Eftersom varje app som är unikt, måste du skapa återställningsplaner som är anpassade för varje program och kör ett redundanstest på var och en.
- Appar och deras beroenden ändras ofta. Säkerställ är aktuella genom att köra ett redundanstest för varje app varje kvartal.

    ![Skärmbild av exempel testa återställningsplan i Site Recovery](./media/recovery-plan-overview/rptest.png)

## <a name="watch-the-video"></a>Titta på videon

Videon ett enkelt exempel som visar en på klick-redundans för en tvålagers-WordPress-app.
    
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]



## <a name="next-steps"></a>Nästa steg

- [Skapa](site-recovery-create-recovery-plans.md) en återställningsplan.
* Lär dig mer om [köra redundansväxlingar](site-recovery-failover.md).  
