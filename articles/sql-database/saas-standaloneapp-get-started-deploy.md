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
ms.date: 11/30/2017
ms.author: genemi
ms.openlocfilehash: d38cd108821bce05824732bbdbdd322ae8563bde
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2017
---
# <a name="deploy-and-explore-a-standalone-single-tenant-application-that-uses-azure-sql-database"></a>Distribuera och utforska en enskild klient fristående program som använder Azure SQL Database

I den här självstudiekursen, distribuera och utforska Wingtip biljetter SaaS fristående program. Programmet är utformad för att presentera Azure SQL Database-funktioner som förenklar aktiverar SaaS-scenarier.

Fristående program mönstret distribuerar en Azure-resursgrupp som innehåller en enda klient-program och en enskild klient-databas för varje klient.  Flera instanser av programmet kan etableras för att ge en lösning för flera innehavare.

I kursen får distribuera du resursgrupper för flera klienter i din Azure-prenumeration.  Det här mönstret gör resursgrupper som ska distribueras till en klients Azure-prenumeration. Azure har partnerprogram som gör att dessa resursgrupper som ska hanteras av en tjänsteleverantör å klientens vägnar. Tjänstprovidern är administratör i klientens prenumerationen.

I avsnittet senare distribution är tre blå **till Azure** knappar. Varje knapp distribuerar en annan instans av programmet. Varje instans är anpassade för en viss klient. När varje knappen trycks distribueras helt motsvarande program inom fem minuter.  Apparna som distribueras i din Azure-prenumeration.  Du har fullständig åtkomst till att utforska och arbeta med enskilda programkomponenter.

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
    <a href="http://aka.ms/deploywingtipsa-contoso" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a>&nbsp; **Contoso samklang Hall**
<br><br>
    <a href="http://aka.ms/deploywingtipsa-dogwood" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a>&nbsp; **Skogskornell Dojo**
<br><br>
    <a href="http://aka.ms/deploywingtipsa-fabrikam" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a>&nbsp; **Fabrikam Jazz en**

2. Ange parametervärden som krävs för varje distribution.

    > [!IMPORTANT]
    > Vissa brandväggar för autentisering och servern är avsiktligt oskyddat i exempelsyfte. **Skapa en ny resursgrupp** för varje distribution.  Använd inte en befintlig resursgrupp. Använd inte det här programmet eller alla resurser som den skapar för produktion. Ta bort alla resursgrupper när du är klar med program att stoppa relaterade fakturering.

    Det är bäst att använda bara gemena bokstäver, siffror och bindestreck i din resursnamn.
    * För **resursgruppen** – Välj **Skapa nytt**, och ange sedan en gemen **namn** för resursgruppen.
        * Vi rekommenderar att du lägger till ett streck följt av dina initialer, följt av en siffra: till exempel *wingtip-sa-af1*.
        * Välj en **plats** från den nedrullningsbara listan.

    * För **användare** -rekommenderar vi att du väljer ett värde som kort användaren, till exempel din initialer plus en siffra: till exempel *af1*.


3. **Distribuera programmet**.

    * Klicka om du accepterar villkoren.
    * Klicka på **Köp**.

4. Övervaka Distributionsstatus för alla tre distributioner genom att klicka på **meddelanden** (klockikonen till höger om sökrutan). Distribuera appen tar fem minuter.


## <a name="run-the-application"></a>Köra programmet

Appen demonstreras handelsplatser som värd för händelser. Platsen typer är konsertsalar, jazz föreningar och sport föreningar. Handelsplatser är kunder i appen Wingtip biljetter. Wingtip biljetter handelsplatser är registrerad som *hyresgäster*. En klient har en plats ett enkelt sätt att visa händelser och sälja biljetter till sina kunder. Varje plats hämtar en anpassad webbplats till sina händelser och sälja biljetter. Varje klient är isolerad från andra klienter och är oberoende av dem. Under försättsbladen hämtar varje klient en separat program-instans med sin egen fristående SQL-databasen.

1. Öppna sidan händelser för var och en av de tre innehavarna i separata flikar i webbläsaren:

    - http://events.contosoconcerthall. &lt;Användaren&gt;. trafficmanager.net
    - http://events.dogwooddojo. &lt;Användaren&gt;. trafficmanager.net
    - http://events.fabrikamjazzclub. &lt;Användaren&gt;. trafficmanager.net

    (I varje URL ersätter &lt;användaren&gt; med din distribution användaren värde.)

   ![Händelser](./media/saas-standaloneapp-get-started-deploy/fabrikam.png)

Att kontrollera distributionen av inkommande begäranden, appen använder [ *Azure Traffic Manager*](../traffic-manager/traffic-manager-overview.md). Varje klient-specifik app-instansen innehåller klientnamnet på som en del av namnet på en domän i URL-Adressen. Alla de URL: er omfattar ditt specifika **användaren** värde. URL: er gör du följande format:
- http://events. &lt;venuename&gt;.&lt; ANVÄNDAREN&gt;. trafficmanager.net

Varje klient databasen **plats** ingår i appinställningarna för den motsvarande distribuerade appen.

I en produktionsmiljö, vanligtvis skapar du en CNAME DNS-posten [ *peka företagets Internetdomän* ](../traffic-manager/traffic-manager-point-internet-domain.md) till trafikhanterarprofilen-URL.


## <a name="explore-the-servers-and-tenant-databases"></a>Utforska servrar och databaser för innehavare

Nu ska vi titta på några av de resurser som har distribuerats:

1. I den [Azure-portalen](http://portal.azure.com), bläddra i listan över resursgrupper.
2. Finns det **wingtip-sa-katalog -&lt;användare&gt;**  resursgruppen.
    - I den här resursgruppen den **katalog-sa -&lt;användare&gt;**  server distribueras. Servern innehåller den **tenantcatalog** databas.
    - Du bör också se resursgrupper tre klient.
3. Öppna den **wingtip-sa-fabrikam -&lt;användare&gt;**  resursgruppen som innehåller resurser för Fabrikam Jazz en distribution.  Den **fabrikamjazzclub -&lt;användare&gt;**  server innehåller den **fabrikamjazzclub** databas.

Varje klient-databasen är 50 DTU *fristående* databas.

## <a name="additional-resources"></a>Ytterligare resurser

<!--
* Additional [tutorials that build on the Wingtip SaaS application](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)
* To learn about elastic pools, see [*What is an Azure SQL elastic pool*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool)
* To learn about elastic jobs, see [*Managing scaled-out cloud databases*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-jobs-overview)
-->

- Mer information om flera innehavare SaaS-program, se [designmönster för SaaS-program med flera innehavare](saas-tenancy-app-design-patterns.md).


## <a name="next-steps"></a>Nästa steg

I den här guiden har du lärt dig:

> [!div class="checklist"]
> * Så här distribuerar du Wingtip biljetter SaaS fristående program.
> * Om servrar och databaser som ingår i appen.
> * Ta bort resurser för att stoppa relaterade fakturering.

