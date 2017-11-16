---
title: "Självstudier för flera innehavare SaaS - Azure SQL Database | Microsoft Docs"
description: "Distribuera och utforska ett fristående enskild klient SaaS-program, som använder Azure SQL Database."
keywords: sql database tutorial
services: sql-database
documentationcenter: 
author: stevestein
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: sstein
ms.openlocfilehash: 2ca290dfcb23215ffd727500e76076ae8b14fa6f
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/16/2017
---
# <a name="deploy-and-explore-a-standalone-single-tenant-application-that-uses-azure-sql-database"></a>Distribuera och utforska en enskild klient fristående program som använder Azure SQL Database

I den här självstudiekursen, distribuera och utforska Wingtip biljetter SaaS fristående program. Programmet är utformad för att presentera Azure SQL Database-funktioner som förenklar aktiverar SaaS-scenarier.

Fristående program mönstret distribuerar en Azure-resursgrupp som innehåller en enda klient-program och en enskild klient-databas för varje klient.  Flera instanser av programmet kan etableras för att ge en lösning för flera innehavare.

Medan i den här självstudiekursen kommer du distribuerar resursgrupper för flera klienter i din Azure-prenumeration, kan det här mönstret resursgrupper som ska distribueras till en klients Azure-prenumeration.  Azure har partnerprogram som gör att dessa resursgrupper som ska hanteras av en tjänsteleverantör för klientens räkning som administratör i klientens prenumerationen.

Det finns tre distribuera till Azure, på knappar som distribuerar en annan instans av programmet som är anpassade för en viss klient i avsnittet distribution. När du trycker på varje knapp med motsvarande programmet distribueras helt fem minuter senare.  Apparna som distribueras i din Azure-prenumeration.  Du har fullständig åtkomst till att utforska och arbeta med enskilda programkomponenter.

Programmet källa kod och hantering av skript är tillgängliga i den [WingtipTicketsSaaS StandaloneApp](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp) GitHub-lagringsplatsen.


I den här guiden lär du dig:

> [!div class="checklist"]

> * Så här distribuerar du Wingtip biljetter SaaS fristående program
> * Var du kan hämta programmets källkod och av hanteringsskript
> * Om servrar och databaser som ingår i appen

Ytterligare självstudier kommer att släppas i vederbörlig som gör att du kan utforska en mängd hanteringsscenarier baserat på det här programmet.   

## <a name="deploy-the-wingtip-tickets-saas-standalone-application"></a>Distribuera Wingtip biljetter SaaS fristående program

Distribuera appen för de tre angivna klienterna:

1. Klicka på var **till Azure** knappen för att öppna mallen för distribution i Azure-portalen. Varje mall kräver två parametervärden. ett namn för en ny resursgrupp och ett användarnamn som särskiljer den här distributionen från andra distributioner av appen. Nästa steg innehåller information för att ange dessa värden.<br><br>
    <a href="http://aka.ms/deploywingtipsa-contoso" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a>     &nbsp;&nbsp;**Contoso samklang Hall**
<br><br>
    <a href="http://aka.ms/deploywingtipsa-dogwood" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a>&nbsp; &nbsp; **Skogskornell Dojo**
<br><br>
    <a href="http://aka.ms/deploywingtipsa-fabrikam" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a>    &nbsp;&nbsp;**Fabrikam Jazz en**

2. Ange parametervärden som krävs för varje distribution.

    > [!IMPORTANT]
    > En del autentisering och brandväggar för servern har avsiktligt lämnats oskyddade i demonstrationssyfte. **Skapa en ny resursgrupp** för varje distribution.  Använd inte en befintlig resursgrupp. Använd inte det här programmet eller alla resurser som den skapar för produktion. Ta bort alla resursgrupper när du är klar med program att stoppa relaterade fakturering.

    Det är bäst att använda bara gemena bokstäver, siffror och bindestreck i din resursnamn.
    * För **resursgruppen** – Välj Skapa nytt och ange sedan ett namn för resursgruppen (skiftlägeskänsligt).
        * Vi rekommenderar att alla bokstäver i din resursgruppens namn är gemener.
        * Vi rekommenderar att du lägger till ett streck följt av dina initialer, följt av en siffra: till exempel _wingtip-sa-af1_.
        * Välj en plats från den nedrullningsbara listan.

    * För **användare** -rekommenderar vi att du väljer ett kort värde som användaren, till exempel din initialer plus en siffra: till exempel _af1_.


1. **Distribuera programmet**.

    * Klicka om du accepterar villkoren.
    * Klicka på **Köp**.

1. Övervaka Distributionsstatus för alla tre distributioner genom att klicka på **meddelanden** (klockikonen höger om sökrutan). Distribuera appen tar cirka fem minuter.


## <a name="run-the-application"></a>Köra programmet

Appen innehåller platser, till exempel konsertsalar, jazz föreningar och sport föreningar som värd för händelser. Handelsplatser registrera som kunder (eller klienter) Wingtip biljetter för ett enkelt sätt att visa en lista över händelser och sälja biljetter. Varje plats hämtar en anpassad webbplats för att hantera och visa deras händelser och sälja biljetter oberoende och isoleras från andra klienter. Under försättsbladen hämtar varje klient en separat programinstansen och fristående SQL-databasen.

1. Öppna sidan händelser för var och en av de tre innehavarna i separata flikar i webbläsaren:

    http://events.contosoconcerthall. &lt;Användaren&gt;. trafficmanager.net <br>
    http://events.dogwooddojo. &lt;Användaren&gt;. trafficmanager.net<br>
    http://events.fabrikamjazzclub. &lt;Användaren&gt;. trafficmanager.net

    (Ersätt &lt;användaren&gt; med din distribution användaren värde).

   ![Händelser](./media/saas-standaloneapp-get-started-deploy/fabrikam.png)


Att kontrollera distributionen av inkommande begäranden, appen använder [ *Azure Traffic Manager*](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview). Varje klient-specifik app innehåller klientnamnet på som en del av namnet på en domän i URL-Adressen. Alla de URL: er omfattar ditt specifika *användare* värde och följande format: http://events.&lt; venuename&gt;.&lt; ANVÄNDAREN&gt;. trafficmanager.net. Varje klient databasplatsen ingår i appinställningarna för den motsvarande distribuerade appen.

I en produktionsmiljö skulle du vanligtvis skapa en CNAME DNS-posten [ *peka företagets Internetdomän* ](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-point-internet-domain) till trafikhanterarprofilen-URL.


## <a name="explore-the-servers-and-tenant-databases"></a>Utforska servrar och databaser för innehavare

Nu ska vi titta på några av de resurser som har distribuerats:

1. I den [Azure-portalen](http://portal.azure.com), bläddra i listan över resursgrupper.  Du bör se den **wingtip-sa-katalog -&lt;användare&gt;**  resursgruppen som **katalog-sa -&lt;användare&gt;**  server distribueras med **tenantcatalog** databas. Du bör också se resursgrupper tre klient.

1. Öppna den **wingtip-sa-fabrikam -&lt;användare&gt;**  resursgruppen som innehåller resurser för Fabrikam Jazz en distribution.  Den **fabrikamjazzclub -&lt;användare&gt;**  server innehåller den **fabrikamjazzclub** databas.


Varje klient-databasen är 50 DTU _fristående_ databas.

## <a name="next-steps"></a>Nästa steg

I den här guiden har du lärt dig:

> [!div class="checklist"]

> * Så här distribuerar du Wingtip biljetter SaaS fristående program
> * Om servrar och databaser som ingår i appen
> * Hur du tar bort exempelresurser för att stoppa relaterad fakturering


## <a name="additional-resources"></a>Ytterligare resurser

<!--* Additional [tutorials that build on the Wingtip SaaS application](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)
* To learn about elastic pools, see [*What is an Azure SQL elastic pool*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool)
* To learn about elastic jobs, see [*Managing scaled-out cloud databases*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-jobs-overview) -->
* Läs mer om SaaS-program med flera klienter i [*Designmönster för SaaS-program med flera klienter*](saas-tenancy-app-design-patterns.md)
