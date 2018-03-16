---
title: "Självstudier för flera innehavare SaaS - Azure SQL Database | Microsoft Docs"
description: "Distribuera och utforska ett fristående enskild klient SaaS-program, som använder Azure SQL Database."
keywords: sql database tutorial
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 11/30/2017
ms.author: genemi
ms.openlocfilehash: e30f096a9f02214839550c2dc143ab665e1cd85c
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="deploy-and-explore-a-standalone-single-tenant-application-that-uses-azure-sql-database"></a>Distribuera och utforska en enskild klient fristående program som använder Azure SQL Database

I den här självstudiekursen, distribuera och utforska Wingtip biljetter SaaS exempelprogrammet som utvecklats med fristående program eller app per klient, mönstret.  Programmet är avsedd att demonstrerar funktionerna i Azure SQL Database som gör det lättare att aktivera flera innehavare SaaS scenarier.

Fristående program eller mönster app per klient distribuerar du en instans av programmet för varje klient.  Varje program har konfigurerats för en viss klient och distribueras i en separat Azure resursgrupp. Flera instanser av programmet etableras för att ge en lösning för flera innehavare. Det här mönstret passar bäst till ett mindre antal, klienter där klientisolering är högsta prioritet. Azure har partnerprogram som tillåter att resurser som ska distribueras till en klient prenumeration och hanteras av en tjänsteleverantör å klientens vägnar. 

I den här självstudiekursen distribuerar du tre fristående program för tre klienter i din Azure-prenumeration.  Du har fullständig åtkomst till att utforska och arbeta med enskilda programkomponenter.

Programmet källa kod och hantering av skript är tillgängliga i den [WingtipTicketsSaaS StandaloneApp](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp) GitHub-lagringsplatsen.


I den här guiden lär du dig:

> [!div class="checklist"]
> * Så här distribuerar du Wingtip biljetter SaaS fristående program.
> * Var du kan hämta programmets källkod och av hanteringsskript.
> * Om servrar och databaser som ingår i appen.

Ytterligare självstudier kommer att släppas. De kan du utforska en mängd hanteringsscenarier baserat på det här programmet.   

## <a name="deploy-the-wingtip-tickets-saas-standalone-application"></a>Distribuera Wingtip biljetter SaaS fristående program

Distribuera appen för de tre angivna klienterna:

1. Klicka på varje blå **till Azure** för att öppna distributionsmallen i den [Azure-portalen](https://portal.azure.com). Varje mall kräver två parametervärden. ett namn för en ny resursgrupp och ett användarnamn som särskiljer den här distributionen från andra distributioner av appen. Nästa steg innehåller information för att ange dessa värden.<br><br>
    <a href="http://aka.ms/deploywingtipsa-contoso" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a> &nbsp; **Contoso samklang Hall**
<br><br>
    <a href="http://aka.ms/deploywingtipsa-dogwood" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a> &nbsp; **Skogskornell Dojo**
<br><br>
    <a href="http://aka.ms/deploywingtipsa-fabrikam" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a> &nbsp; **Fabrikam Jazz Club**

2. Ange parametervärden som krävs för varje distribution.

    > [!IMPORTANT]
    > Vissa brandväggar för autentisering och servern är avsiktligt oskyddat i exempelsyfte. **Skapa en ny resursgrupp** för varje distribution.  Använd inte en befintlig resursgrupp. Använd inte det här programmet eller alla resurser som den skapar för produktion. Ta bort alla resursgrupper när du är klar med program att stoppa relaterade fakturering.

    Det är bäst att använda bara gemena bokstäver, siffror och bindestreck i din resursnamn.
    * För **resursgruppen**, Välj Skapa nytt och ange sedan ett gemener namn för resursgruppen. **Wingtip-sa -\<venueName\>-\<användare\>**  är det rekommenderade mönstret.  För \<venueName\>, ersätta plats namnet utan blanksteg. För \<användaren\>, Ersätt värdet för användaren nedan.  Med det här mönstret resursgruppnamn kanske *wingtip-sa-contosoconcerthall-af1*, *wingtip-sa-dogwooddojo-af1*, *wingtip-sa-fabrikamjazzclub-af1*.
    * Välj en **plats** från den nedrullningsbara listan.

    * För **användare** -rekommenderar vi ett kort användaren värde, till exempel din initialer plus en siffra: till exempel *af1*.


3. **Distribuera programmet**.

    * Klicka om du accepterar villkoren.
    * Klicka på **Köp**.

4. Övervaka status för alla tre distributioner genom att klicka på **meddelanden** (klockikonen till höger om sökrutan). Distribuera appar tar cirka fem minuter.


## <a name="run-the-applications"></a>Köra programmen

Appen demonstreras handelsplatser som värd för händelser.  Handelsplatser är innehavare av programmet. Varje plats hämtar en anpassad webbplats till en lista över händelserna och sälja biljetter. Platsen typer är konsertsalar, jazz föreningar och sport föreningar. I det här exemplet anger vilken typ av plats i bakgrunden foto som visas på webbplatsen för den platsen.   Varje plats har en separat program-instans med sin egen fristående SQL-databasen i fristående app-modellen.

1. Öppna sidan händelser för var och en av de tre innehavarna i separata flikar i webbläsaren:

    - http://events.contosoconcerthall.&lt;user&gt;.trafficmanager.net
    - http://events.dogwooddojo.&lt;user&gt;.trafficmanager.net
    - http://events.fabrikamjazzclub.&lt;user&gt;.trafficmanager.net

    (I varje URL ersätter &lt;användaren&gt; med din distribution användaren värde.)

   ![Händelser](./media/saas-standaloneapp-get-started-deploy/fabrikam.png)

Att kontrollera distributionen av inkommande begäranden, appen använder [ *Azure Traffic Manager*](../traffic-manager/traffic-manager-overview.md). Varje klient-specifik app-instansen innehåller klientnamnet på som en del av namnet på en domän i URL-Adressen. Alla de URL: er omfattar ditt specifika **användaren** värde. URL: er gör du följande format:
- http://events.&lt;venuename&gt;.&lt;user&gt;.trafficmanager.net

Varje klient databasen **plats** ingår i appinställningarna för den motsvarande distribuerade appen.

I en produktionsmiljö, vanligtvis skapar du en CNAME DNS-posten [ *peka företagets Internetdomän* ](../traffic-manager/traffic-manager-point-internet-domain.md) till trafikhanterarprofilen-URL.


## <a name="explore-the-servers-and-tenant-databases"></a>Utforska servrar och databaser för innehavare

Nu ska vi titta på några av de resurser som har distribuerats:

1. I den [Azure-portalen](http://portal.azure.com), bläddra i listan över resursgrupper.
2. Du bör se resursgrupper tre klient.
3. Öppna den **wingtip-sa-fabrikam -&lt;användare&gt;**  resursgruppen som innehåller resurser för Fabrikam Jazz en distribution.  Den **fabrikamjazzclub -&lt;användare&gt;**  server innehåller den **fabrikamjazzclub** databas.

Varje klient-databasen är 50 DTU *fristående* databas.

## <a name="additional-resources"></a>Ytterligare resurser

<!--
* Additional [tutorials that build on the Wingtip SaaS application](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)
* To learn about elastic pools, see [*What is an Azure SQL elastic pool*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool)
* To learn about elastic jobs, see [*Managing scaled-out cloud databases*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-jobs-overview)
-->

- Mer information om flera innehavare SaaS-program, se [designmönster för SaaS-program med flera innehavare](saas-tenancy-app-design-patterns.md).

 
## <a name="delete-resource-groups-to-stop-billing"></a>Ta bort resursgrupper för att stoppa fakturering ##

När du är klar använder exemplet, ta bort alla resursgrupper som du skapade för att stoppa den associerade faktureringen.

## <a name="next-steps"></a>Nästa steg

I den här guiden har du lärt dig:

> [!div class="checklist"]
> * Så här distribuerar du Wingtip biljetter SaaS fristående program.
> * Om servrar och databaser som ingår i appen.
> * Ta bort resurser för att stoppa relaterade fakturering.

Försök med den [etablera och katalogen](saas-standaloneapp-provision-and-catalog.md) självstudiekursen där du vill utforska användningen av en katalog med klienter som gör att en mängd mellan klient scenarier, till exempel schemat hantering och klient analytics.
 

