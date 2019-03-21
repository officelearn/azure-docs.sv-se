---
title: Enda klient SaaS-självstudie – Azure SQL Database | Microsoft Docs
description: Distribuera och utforska ett fristående enda klient SaaS-program, som använder Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: sstein
manager: craigg
ms.date: 11/07/2018
ms.openlocfilehash: 4dbf53df4d3f34e80757f9575981b4b053587d97
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58012662"
---
# <a name="deploy-and-explore-a-standalone-single-tenant-application-that-uses-azure-sql-database"></a>Distribuera och utforska en enda klient fristående program som använder Azure SQL Database

I den här självstudien, distribuera och utforska Wingtip biljetter SaaS-exempelprogram som utvecklats med fristående program eller app-per-klient, mönstret.  Programmet har utformats för att demonstrera funktionerna i Azure SQL Database som gör det lättare att aktivera flera innehavare SaaS-scenarier.

Fristående program eller mönster för app-per-klient distribuerar du en programinstans för varje klient.  Varje program är konfigurerad för en specifik klient och distribueras i en separat Azure-resursgrupp. Flera instanser av programmet etableras för att tillhandahålla en lösning för flera innehavare. Det här mönstret är bäst för lägre värden med klienter där klientisolering är högsta prioritet. Azure har partnerprogram som gör att resurser som ska distribueras till en klient-prenumeration och hanteras av en tjänsteleverantör för klientens räkning. 

I den här självstudien distribuerar du tre fristående program för tre klienter i din Azure-prenumeration.  Du har fullständig åtkomst att utforska och arbeta med de olika programkomponenterna.

Programmet källa kod- och skript är tillgängliga i den [WingtipTicketsSaaS StandaloneApp](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp) GitHub-lagringsplatsen. Programmet har skapats med hjälp av Visual Studio 2015 och det gick inte att öppna och kompilera i Visual Studio 2017 utan uppdatering.


I den här guiden lär du dig:

> [!div class="checklist"]
> * Så här distribuerar Wingtip biljetter SaaS fristående program.
> * Var du kan hämta programmets källkod och skript.
> * Om servrar och databaser som ingår i appen.

Ytterligare självstudier kommer att släppas. De kan du utforska olika scenarier för hantering av baserat på det här mönstret för programmet.   

## <a name="deploy-the-wingtip-tickets-saas-standalone-application"></a>Distribuera Wingtip biljetter SaaS fristående program

Distribuera appen för de tre angivna innehavarna:

1. Klicka på varje blå **distribuera till Azure** knappen för att öppna distributionsmallen i den [Azure-portalen](https://portal.azure.com). Varje mall kräver två parametervärden; ett namn för en ny resursgrupp och ett användarnamn som särskiljer den här distributionen från andra distributioner av appen. Nästa steg innehåller information för att ange dessa värden.<br><br>
    <a href="https://aka.ms/deploywingtipsa-contoso" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a> &nbsp; **Contosos Konserthall**
<br><br>
    <a href="https://aka.ms/deploywingtipsa-dogwood" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a> &nbsp; **Skogskornell Dojo**
<br><br>
    <a href="https://aka.ms/deploywingtipsa-fabrikam" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a> &nbsp; **Fabrikam Jazz Club**

2. Ange nödvändiga parametervärden för varje distribution.

    > [!IMPORTANT]
    > Vissa brandväggar för autentisering och servern har avsiktligt lämnats oskyddade i demonstrationssyfte. **Skapa en ny resursgrupp** för varje programdistribution.  Använd inte en befintlig resursgrupp. Använd inte det här programmet eller resurser som det skapar för produktion. Ta bort alla resursgrupper när du är klar med program för att stoppa relaterad fakturering.

    Det är bäst att använda endast gemena bokstäver, siffror och bindestreck i ditt resursnamn.
    * För **resursgrupp**, Välj Skapa ny och ange sedan ett gemener namn för resursgruppen. **Wingtip-sa -\<venueName\>-\<användaren\>**  är det rekommendera mönstret.  För \<venueName\>, ersätter du jurisdiktionsort namnet utan blanksteg. För \<användaren\>, ersätta värdet för användaren nedan.  Med det här mönstret kanske resursgruppnamn *wingtip-sa-contosoconcerthall-af1*, *wingtip-sa-dogwooddojo-af1*, *wingtip-sa-fabrikamjazzclub-af1*.
    * Välj en **plats** från den nedrullningsbara listan.

    * För **användaren** -vi rekommenderar ett värde som kort användaren, till exempel din initialer plus en siffra: till exempel *af1*.


3. **Distribuera programmet**.

    * Klicka för att godkänna de allmänna villkoren.
    * Klicka på **Köp**.

4. Övervaka status för alla tre distributioner genom att klicka på **meddelanden** (klockikonen till höger om sökrutan). Det tar cirka fem minuter att distribuera appar.


## <a name="run-the-applications"></a>Köra programmen

Appen visar platser som är värdar för händelser.  Platser är innehavare av programmet. Varje plats får en anpassad webbplats kan sina evenemang och sälja biljetter. Platstyperna är konsertlokaler, jazzklubbar och sport föreningar. I det här exemplet anger vilken typ av plats i bakgrunden foto som visas på webbplatsen för den platsen.   I fristående app-modellen har varje plats en separat program-instans med sin egen fristående SQL-databas.

1. Öppna sidan händelser för var och en av tre klienter i separata flikar i webbläsaren:

   - http://events.contosoconcerthall.&lt;user&gt;.trafficmanager.net
   - http://events.dogwooddojo.&lt;user&gt;.trafficmanager.net
   - http://events.fabrikamjazzclub.&lt;user&gt;.trafficmanager.net

     (I varje URL ersätter &lt;användaren&gt; med värdet för användarnamn för din distribution.)

   ![Händelser](./media/saas-standaloneapp-get-started-deploy/fabrikam.png)

Att styra distributionen av inkommande begäranden, appen använder [ *Azure Traffic Manager*](../traffic-manager/traffic-manager-overview.md). Varje klientspecifik appinstansen innehåller klientnamnet på som en del av domännamnet i URL: en. Alla klient URL: er inkluderar ditt specifika **användaren** värde. URL: er följer följande format:
- http://events.&lt; venuename&gt;.&lt; användaren&gt;. trafficmanager.net

Varje klientdatabas **plats** ingår i appinställningarna för den motsvarande distribuerade appen.

I en produktionsmiljö, vanligtvis skapar du en CNAME DNS-post till [ *peka företagets Internetdomän* ](../traffic-manager/traffic-manager-point-internet-domain.md) till Webbadressen för traffic manager-profil.


## <a name="explore-the-servers-and-tenant-databases"></a>Utforska servrar och klientdatabaser

Nu ska vi titta på några av de resurser som har distribuerats:

1. I den [Azure-portalen](https://portal.azure.com), bläddra i listan över resursgrupper.
2. Du bör se tre klient resursgrupper.
3. Öppna den **wingtip-sa-fabrikam -&lt;användaren&gt;**  resursgruppen som innehåller resurser för Fabrikam Jazzklubb-distribution.  Den **fabrikamjazzclub -&lt;användaren&gt;**  server innehåller den **fabrikamjazzclub** databas.

Varje klientdatabas är en 50 DTU *fristående* databas.

## <a name="additional-resources"></a>Ytterligare resurser

<!--
* Additional [tutorials that build on the Wingtip SaaS application](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* To learn about elastic pools, see [*What is an Azure SQL elastic pool*](sql-database-elastic-pool.md)
* To learn about elastic jobs, see [*Managing scaled-out cloud databases*](sql-database-elastic-jobs-overview.md)
-->

- Mer information om delade SaaS-program, se [designmönster för SaaS-program för flera innehavare](saas-tenancy-app-design-patterns.md).

 
## <a name="delete-resource-groups-to-stop-billing"></a>Ta bort resursgrupper för att stoppa debiteringen ##

Ta bort alla resursgrupper som du skapade för att stoppa den associerade faktureringen när du är klar med hjälp av exemplet.

## <a name="next-steps"></a>Nästa steg

I den här guiden har du lärt dig:

> [!div class="checklist"]
> * Så här distribuerar Wingtip biljetter SaaS fristående program.
> * Om servrar och databaser som ingår i appen.
> * Hur du tar bort exempelresurser för att stoppa relaterad fakturering.

Nu ska du prova den [etablera och katalogisera](saas-standaloneapp-provision-and-catalog.md) kursen som du vill utforska användningen av en katalog med klienter som gör att flera olika scenarier för flera klienter, till exempel schemat hanterings och klienttrafik analytics.
 

