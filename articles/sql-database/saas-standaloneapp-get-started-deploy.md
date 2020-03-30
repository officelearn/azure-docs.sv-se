---
title: SaaS-självstudie för en klient
description: Distribuera och utforska ett fristående SaaS-program med en klientorganisation som använder Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: sstein
ms.date: 11/07/2018
ms.openlocfilehash: e3afc8aa58551b995070ffaca978c8e7c8454da3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73822124"
---
# <a name="deploy-and-explore-a-standalone-single-tenant-application-that-uses-azure-sql-database"></a>Distribuera och utforska ett fristående program med en innehavare som använder Azure SQL Database

I den här självstudien distribuerar och utforskar du SaaS-exempelprogrammet Wingtip Tickets som utvecklats med hjälp av det fristående programmet, eller app-per-klient- mönstret.  Programmet är utformat för att visa upp funktioner i Azure SQL Database som förenklar aktiveringen av SaaS-scenarier med flera innehavare.

Det fristående programmet eller mönstret app per klient distribuerar en programinstans för varje klient.  Varje program är konfigurerat för en specifik klient och distribueras i en separat Azure-resursgrupp. Flera instanser av programmet har etablerats för att tillhandahålla en lösning med flera innehavare. Det här mönstret passar bäst för mindre antal, klienter där klientisolering har högsta prioritet. Azure har partnerprogram som gör att resurser kan distribueras till en klients prenumeration och hanteras av en tjänsteleverantör för klientens räkning. 

I den här självstudien distribuerar du tre fristående program för tre klienter till din Azure-prenumeration.  Du har full tillgång till utforska och arbeta med de enskilda applikationskomponenterna.

Programmets källkod och hanteringsskript finns i [WingtipTicketsSSaaS-StandaloneApp](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp) GitHub repo. Programmet skapades med Visual Studio 2015 och öppnas och kompileras inte i Visual Studio 2019 utan att uppdateras.


I den här guiden lär du dig:

> [!div class="checklist"]
> * Hur man distribuerar Wingtip Biljetter SaaS fristående program.
> * Var du kan hämta programmets källkod och hanteringsskript.
> * Om de servrar och databaser som utgör appen.

Ytterligare självstudier kommer att släppas. De gör att du kan utforska en rad hanteringsscenarier baserat på det här programmönstret.   

## <a name="deploy-the-wingtip-tickets-saas-standalone-application"></a>Distribuera Wingtip Biljetter SaaS fristående program

Distribuera appen för de tre som tillhandahålls klienter:

1. Klicka på varje blå **distribution till Azure-knapp** för att öppna distributionsmallen i [Azure-portalen](https://portal.azure.com). Varje mall kräver två parametervärden. ett namn på en ny resursgrupp och ett användarnamn som skiljer den här distributionen från andra distributioner av appen. Nästa steg innehåller information om hur du anger dessa värden.<br><br>
    <a href="https://aka.ms/deploywingtipsa-contoso" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a>&nbsp; **Contoso konserthus**
<br><br>
    <a href="https://aka.ms/deploywingtipsa-dogwood" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a>&nbsp; **Mer från Dogwood Dojo**
<br><br>
    <a href="https://aka.ms/deploywingtipsa-fabrikam" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a>&nbsp; **Fabrikam jazzklubb**

2. Ange obligatoriska parametervärden för varje distribution.

    > [!IMPORTANT]
    > Vissa autentiserings- och serverbrandvägger är avsiktligt osäkrade för demonstrationsändamål. **Skapa en ny resursgrupp** för varje programdistribution.  Använd inte en befintlig resursgrupp. Använd inte det här programmet, eller några resurser som det skapar, för produktion. Ta bort alla resursgrupper när du är klar med programmen för att stoppa relaterad fakturering.

    Det är bäst att bara använda gemener, siffror och bindestreck i resursnamnen.
    * För **resursgrupp**väljer du Skapa ny och ger sedan ett gemener Namn för resursgruppen. **wingtip-sa-\<venueName-användare\>-\<\> ** är det rekommenderade mönstret.  För \<venueName\>ersätter du platsnamnet utan mellanslag. För \<\>användare ersätter du användarvärdet underifrån.  Med detta mönster, resursgrupp namn kan vara *wingtip-sa-contosoconcerthall-af1*, *wingtip-sa-dogwooddojo-af1*, *wingtip-sa-fabrikamjazzclub-af1*.
    * Välj en **plats** i listrutan.

    * För **användare** - Vi rekommenderar ett kort användarvärde, till exempel dina initialer plus en siffra: till exempel *af1*.


3. **Distribuera programmet**.

    * Klicka för att godkänna villkoren.
    * Klicka på **Köp**.

4. Övervaka status för alla tre distributionerna genom att klicka på **Meddelanden** (klockikonen till höger om sökrutan). Det tar cirka fem minuter att distribuera apparna.


## <a name="run-the-applications"></a>Köra programmen

Appen visar upp platser som är värd för evenemang.  Platserna är hyresgäster i ansökan. Varje plats får en personlig webbplats för att lista sina evenemang och sälja biljetter. Lokaltyper inkluderar konsertsalar, jazzklubbar och idrottsklubbar. I urvalet bestämmer typen av plats bakgrundsbilden som visas på lokalens webbplats.   I den fristående appmodellen har varje plats en separat programinstans med sin egen fristående SQL-databas.

1. Öppna händelsesidan för var och en av de tre klienterna på separata webbläsarflikar:

   - http://events.contosoconcerthall.&lt;användare&gt;.trafficmanager.net
   - http://events.dogwooddojo.&lt;användare&gt;.trafficmanager.net
   - http://events.fabrikamjazzclub.&lt;användare&gt;.trafficmanager.net

     (I varje URL &lt;&gt; ersätter du användaren med distributionens användarvärde.)

   ![Händelser](./media/saas-standaloneapp-get-started-deploy/fabrikam.png)

För att styra distributionen av inkommande begäranden använder appen [*Azure Traffic Manager*](../traffic-manager/traffic-manager-overview.md). Varje klientspecifik appinstans innehåller klientnamnet som en del av domännamnet i URL:en. Alla klientadresser innehåller ditt specifika **användarvärde.** Webbadresserna följer följande format:
- http://events.&lt;venuename&gt;. &lt;användare&gt;.trafficmanager.net

Varje klients **databasplats** ingår i appinställningarna för motsvarande distribuerade app.

I en produktionsmiljö skapar du vanligtvis en CNAME DNS-post för att [*peka ett företags internetdomän*](../traffic-manager/traffic-manager-point-internet-domain.md) till url:en till traffic manager-profilen.


## <a name="explore-the-servers-and-tenant-databases"></a>Utforska servrar och klientdatabaser

Låt oss titta på några av de resurser som har distribuerats:

1. I [Azure-portalen](https://portal.azure.com)bläddrar du till listan över resursgrupper.
2. Du bör se de tre klientresursgrupperna.
3. Öppna resursgruppen **wingtip-sa-fabrikam- user,&lt;&gt; ** som innehåller resurserna för fabrikam Jazz Club-distributionen.  Den **&lt;fabrikamjazzclub-användarservern&gt; ** innehåller **fabrikamjazzclub-databasen.**

Varje klientdatabas är en fristående 50 *DTU-databas.*

## <a name="additional-resources"></a>Ytterligare resurser

<!--
* Additional [tutorials that build on the Wingtip SaaS application](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* To learn about elastic pools, see [*What is an Azure SQL elastic pool*](sql-database-elastic-pool.md)
* To learn about elastic jobs, see [*Managing scaled-out cloud databases*](elastic-jobs-overview.md)
-->

- Mer information om SaaS-program med flera innehavare finns i [Designmönster för SaaS-program med flera innehavare](saas-tenancy-app-design-patterns.md).

 
## <a name="delete-resource-groups-to-stop-billing"></a>Ta bort resursgrupper för att stoppa fakturering ##

När du är klar med exemplet tar du bort alla resursgrupper som du har skapat för att stoppa den associerade faktureringen.

## <a name="next-steps"></a>Nästa steg

I den här guiden har du lärt dig:

> [!div class="checklist"]
> * Hur man distribuerar Wingtip Biljetter SaaS fristående program.
> * Om de servrar och databaser som utgör appen.
> * Så här tar du bort exempelresurser för att stoppa relaterad fakturering.

Försök sedan [med](saas-standaloneapp-provision-and-catalog.md) självstudien Etablering och katalog där du ska utforska användningen av en katalog med klienter som möjliggör en rad scenarier mellan innehavare, till exempel schemahantering och klientanalys.
 

