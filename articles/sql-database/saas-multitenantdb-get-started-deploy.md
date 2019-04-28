---
title: Distribuera en fragmenterade (sharded) databas för flera innehavare SaaS-app som använder Azure SQL Database | Microsoft Docs
description: Distribuera och utforska shardade Wingtip biljetter SaaS databas för flera innehavare programmet, som visar SaaS-mönstren med hjälp av Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: billgib, stein
manager: craigg
ms.date: 10/16/2018
ms.openlocfilehash: 8f19303dba7fcf2a9edb325464fdd3748220903e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61486208"
---
# <a name="deploy-and-explore-a-sharded-multi-tenant-application"></a>Distribuera och utforska ett delat program för flera innehavare

I den här självstudien, distribuera och utforska ett exempel SaaS-program som kallas Wingtip biljetter. Appen Wingtip biljetter är utformad för att demonstrera funktionerna i Azure SQL Database som förenklar implementeringen av SaaS-scenarier.

Den här implementeringen av Wingtip biljetter appen använder ett mönster för fragmenterade (sharded) databas för flera innehavare. Den horisontell partitionering är genom att klient-ID. Klientorganisationens data ska distribueras till en viss databas enligt klient-ID-värden. 

Det här mönstret för databasen kan du lagra en eller flera innehavare i varje shard eller databasen. Du kan optimera för lägsta kostnad genom att konfigurera varje databas som delas av flera klienter. Eller du kan optimera för isolering genom att konfigurera varje databas som lagrar bara en klient. Valfri optimering kan göras separat för varje specifik klient. Ditt val kan göras när klienten först lagras, eller när du ändrar dig senare. Programmet är utformade att fungera bra i båda fallen.

## <a name="app-deploys-quickly"></a>Appen distribueras snabbt

Appen körs i Azure-molnet och använder Azure SQL Database. I följande distribution avsnitt ger det blå fältet **distribuera till Azure** knappen. När knappen trycks distribueras helt appen till din Azure-prenumeration inom fem minuter. Du har fullständig åtkomst till arbete med de olika programkomponenterna.

Programmet har distribuerats med data för tre exempelklienter. Klienterna lagras tillsammans i en databas för flera innehavare.

Vem som helst kan ladda ned källkoden C# och PowerShell för Wingtip-biljetter från [dess GitHub-lagringsplatsen][link-github-wingtip-multitenantdb-55g].

## <a name="learn-in-this-tutorial"></a>Lär dig i den här självstudien

> [!div class="checklist"]
> - Hur du distribuerar Wingtip biljetter SaaS-program.
> - Var du kan hämta programmets källkod och skript.
> - Om servrar och databaser som ingår i appen.
> - Hur klienter mappas till sina data med den *catalog*.
> - Hur du etablerar en ny klient.
> - Så här att övervaka klientaktivitet i appen.

Det finns en uppsättning relaterade guider som bygger på den här inledande distributionen. Självstudierna utforska ett intervall av SaaS-design och -hanteringsmönster. När du har gått igenom självstudierna uppmanas du att gå igenom de skript som tillhandahålls för att se hur de olika SaaS-mönstren implementeras.

## <a name="prerequisites"></a>Nödvändiga komponenter

Följande krav måste uppfyllas för att kunna köra den här självstudiekursen:

- Den senaste Azure PowerShell har installerats. Mer information finns i [komma igång med Azure PowerShell][link-azure-get-started-powershell-41q].

## <a name="deploy-the-wingtip-tickets-app"></a>Distribuera Wingtip biljetter app

### <a name="plan-the-names"></a>Planera namnen

I stegen i det här avsnittet anger du en *användaren* värde som används för att säkerställa att resursnamn är globalt unikt och ett namn för den *resursgrupp* som innehåller alla resurser som skapades i en distribution appens. För en person med namnet *Ann Finley*, föreslår vi att:
- *Användare:* **af1**  *(hennes initialer plus en siffra. Använd ett annat värde (t.ex. af2) om du distribuerar appen en gång.)*
- *Resursgrupp:* **wingtip-mt-af1** *(wingtip-mt innebär att det är shardad app för flera klienter. Lägga till användaren namnet af1 korrelerar resursgruppens namn med namnen på de resurser som den innehåller.)*

Välj ditt namn nu och Skriv ned dem. 

### <a name="steps"></a>Steg

1. Klicka på följande blå **distribuera till Azure** knappen.
   - Azure-portalen öppnas med Wingtip biljetter SaaS-Distributionsmall.

     [![Knappen för att distribuera till Azure.][image-deploy-to-azure-blue-48d]][link-aka-ms-deploywtp-mtapp-52k]

1. Ange nödvändiga parametervärden för distributionen.

    > [!IMPORTANT]
    > För den här demonstrationen, Använd inte några befintliga resursgrupper, servrar eller pooler. I stället välja **skapa en ny resursgrupp**. Ta bort den här resursgruppen när du är klar med att programmet för att stoppa relaterad fakturering.
    > Använd inte det här programmet eller resurser som det skapar för produktion. Vissa aspekter av autentisering och serverbrandväggsinställningarna är inte avsiktligt säkra i appen för att underlätta demonstrationen av.

    - För **resursgrupp** – Välj **Skapa nytt**, och ange sedan en **namn** för resursgruppen (skiftlägeskänsligt).
        - Välj en **plats** från den nedrullningsbara listan.
    - För **användaren** -vi rekommenderar att du väljer en kort **användaren** värde.

1. **Distribuera programmet**.

    - Klicka för att godkänna de allmänna villkoren.
    - Klicka på **Köp**.

1. Övervaka Distributionsstatus genom att klicka på **meddelanden**, vilket är klockikonen till höger om sökrutan. Distribuera Wingtip appen tar cirka fem minuter.

   ![distributionen lyckades](media/saas-multitenantdb-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-management-scripts"></a>Ladda ned och avblockera-hanteringsskript

Ladda ned programmet källa kod- och skripten medan programmet distribueras.

> [!NOTE]
> Körbart innehåll (skript, DLL-filer) kan blockeras av Windows när zip-filer laddas ned från en extern källa och extraheras. Vid extrahering av skripten från en zip-fil, Använd följande steg för att avblockera .zip-filen innan du extrahera. Genom att avblockera .zip-filen, garanteras du skripten ska tillåtas att köras.

1. Bläddra till [WingtipTicketsSaaS MultiTenantDb GitHub-lagringsplatsen](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb).
2. Klicka på **klona eller ladda ned**.
3. Klicka på **ladda ned ZIP** och spara filen.
4. Högerklicka på den **WingtipTicketsSaaS-MultiTenantDb-master.zip** och väljer **egenskaper**.
5. På den **Allmänt** fliken **avblockera**, och klicka på **tillämpa**.
6. Klicka på **OK**.
7. Extrahera filerna.

Skript finns i den *... \\WingtipTicketsSaaS-MultiTenantDb-master\\inlärningsmoduler\\*  mapp.

## <a name="update-the-configuration-file-for-this-deployment"></a>Uppdatera konfigurationsfilen för den här distributionen

Innan du kör alla skript måste ange den *resursgrupp* och *användaren* värdena i **UserConfig.psm1**. Ange variablerna till samma värden som du angav under distributionen.

1. Öppna... \\Inlärningsmoduler\\*UserConfig.psm1* i den *PowerShell ISE*.
2. Uppdatera *ResourceGroupName* och *namn* med specifika värden för din distribution (på raderna 10 och 11 endast).
3. Spara ändringarna.

De värden som anges i den här filen som används av alla skript, så det är viktigt att de stämmer. Om du distribuerar om appen måste du välja olika värden för användar- och resursgrupp. Uppdatera filen UserConfig.psm1 sedan, igen med de nya värdena.

## <a name="run-the-application"></a>Köra programmet

Klienterna finns i appen Wingtip platser. En plats kan vara konserthall, en sport club eller någon annan plats som är värd för händelser. Platser registrera i Wingtip som kunder och ett klient-ID genereras för varje plats. Varje plats visas dess kommande händelser i Wingtip, så offentligt kan köpa biljetter på händelser.

Varje plats får en personaliserad webbapp sina evenemang och sälja biljetter. Varje webbapp är oberoende och isolerat från andra klienter. Internt i Azure SQL Database, var och en data för varje klient lagras i en fragmenterade (sharded) databas för flera innehavare som standard. Alla data taggas med klient-ID.

En central **Evenemangshubben** webbsidan innehåller en lista med länkar till klienter i din viss distribution. Använd följande steg för att uppleva de **Evenemangshubben** webbsidan och en enskild webbapp:

1. Öppna den **Evenemangshubben** i webbläsaren:
   - http://events.wingtip-mt.&lt; användare&gt;. trafficmanager.net &nbsp; *(Ersätt &lt;användaren&gt; med värdet för användarnamn för din distribution.)*

     ![evenemangshubben](media/saas-multitenantdb-get-started-deploy/events-hub.png)

2. Klicka på **Fabrikam Jazzklubb** i **evenemangshubben**.

   ![Händelser](./media/saas-multitenantdb-get-started-deploy/fabrikam.png)

### <a name="azure-traffic-manager"></a>Azure Traffic Manager

Om du vill styra distributionen av inkommande begäranden, Wingtip-appen använder [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md). Sidan händelser för varje klient innehåller klientnamnet i URL: en. Varje URL innehåller också ditt specifika användare-värde. Varje URL följs formatet visas med hjälp av följande steg:

- http://events.wingtip-mt.&lt;user&gt;.trafficmanager.net/*fabrikamjazzclub*

1. Evenemangsappen Parsar klientnamnet från URL: en. Innehavarens namn är *fabrikamjazzclub* i föregående exempel-URL.
2. Appen sedan hashar klientnamnet för att skapa en nyckel för att komma åt en katalog med hjälp av [fragmentkarthantering](sql-database-elastic-scale-shard-map-management.md).
3. Appen söker efter nyckeln i katalogen och erhåller motsvarande platsen för klientens databas.
4. Appen använder informationen som platsen för att hitta och tillgång till en databas som innehåller alla data för klientorganisationen.

### <a name="events-hub"></a>Events Hub

1. Den **Evenemangshubben** visar en lista över alla klienter som är registrerade i katalogen och deras platser.
2. Den **Evenemangshubben** använder utökade metadata i katalogen för att hämta det klientnamn som är associerade med varje avbildning att konstruera URL: er.

I en produktionsmiljö kan du vanligtvis skapa en CNAME DNS-post till [peka företagets Internetdomän](../traffic-manager/traffic-manager-point-internet-domain.md) till traffic manager-profil.

## <a name="start-generating-load-on-the-tenant-databases"></a>Börja generera belastningar på klient-databaserna

Nu när appen har distribuerats kan vi få det att fungera! Den *Demo-LoadGenerator* PowerShell-skriptet startar en arbetsbelastning som körs för varje klient. Den verkliga belastningen på många SaaS-appar är vanligtvis sporadisk och oförutsägbar. Om du vill simulera den här typen av belastningen producerar generatorn en belastning som distribueras över alla klienter. Belastningen innehåller slumpmässig belastning på varje klient som sker med slumpmässiga intervall. Det tar flera minuter för mönster för belastningsutjämning till dyker upp så att det är bäst att låta generatorn köra i minst tre eller fyra minuter innan du övervakar belastningen.

1. I den *PowerShell ISE*öppnar den... \\Inlärningsmoduler\\verktyg\\*Demo-LoadGenerator.ps1* skript.
2. Tryck **F5** för att köra skriptet och starta belastningsgeneratorn (Lämna standardvärdet för parametern för tillfället).

Den *Demo-LoadGenerator.ps1* skriptet öppnas en annan PowerShell-session där belastningsgeneratorn körs. Belastningsgeneratorn körs i den här sessionen som en aktivitet i förgrunden som anropar bakgrundsjobb för load generations, en för varje klient.

När aktiviteten förgrunden har startat den finns kvar i tillståndet anrop av jobbet. Uppgiften startar ytterligare bakgrundsjobb för alla nya klienter som etableras därefter.

Stänger PowerShell-sessionen avbryts alla jobb.

Du kanske vill starta om generator belastningssessionen om du vill använda olika parametervärden. I så fall stänger PowerShell generation session och kör sedan den *Demo-LoadGenerator.ps1*.

## <a name="provision-a-new-tenant-into-the-sharded-database"></a>Etablera en ny klient till fragmenterade (sharded) databas

Den första distributionen omfattar tre exempelklienter i den *Tenants1* databas. Nu ska vi skapa en annan klient och notera dess påverkan på det distribuerade programmet. Tryck på en tangent för att skapa en ny klient i det här steget:

1. Öppna... \\Inlärningsmoduler\\etablera och katalogisera\\*Demo-ProvisionTenants.ps1* i den *PowerShell ISE*.
2. Tryck på **F5** (inte **F8**) att köra skriptet (lämna standardvärdena för tillfället).

   > [!NOTE]
   > Du måste köra PowerShell-skript enbart genom att trycka på den **F5** nyckel inte genom att trycka på **F8** att köra valda delar av skriptet. Problem med **F8** är att den *$PSScriptRoot* variabeln utvärderas inte. Den här variabeln krävs av många skript att navigera mappar anropa andra skript eller importera moduler.

Den nya innehavaren Red Maple Racing läggs till i den *Tenants1* databasen och registrerat i katalogen. Den nya innehavaren är biljett sälja **händelser** plats öppnas i webbläsaren:

![Ny klient](./media/saas-multitenantdb-get-started-deploy/red-maple-racing.png)

Uppdatera den **Evenemangshubben**, och den nya innehavaren visas i listan.

## <a name="provision-a-new-tenant-in-its-own-database"></a>Etablera en ny klient i en egen databas

Shardade modell för flera klienter kan du välja om du vill etablera en ny klient till en databas som innehåller andra klienter eller till en databas med sin egen. En klient som är isolerade i en egen databas har följande fördelar:

- Du kan hantera klientens databasens prestanda utan att behöva äventyra med behov för andra klienter.
- Om det behövs kan databasen återställas till en tidigare tidpunkt, eftersom inga andra klienter kan påverkas.

Du kan välja att lägga till kunder för kostnadsfri utvärderingsversion eller ekonomin kunder till flera klientdatabaser. Du kan placera varje premium-klient i en egen dedikerade databas. Om du skapar flera databaser som innehåller endast en klient kan hantera du dem alla gemensamt i en elastisk pool för att optimera resurskostnader.

Nu ska etablerar vi en annan klient nu i en egen databas:

1. I... \\Inlärningsmoduler\\etablera och katalogisera\\*Demo-ProvisionTenants.ps1*, ändra *$TenantName* till **Salix Salsa**, *$VenueType* till **dance** och *$Scenario* till **2**.

2. Tryck på **F5** att köra skriptet igen.
    - Detta **F5** press etablerar ny klient i en separat databas. Databasen och klienten har registrerats i katalogen. Sedan öppnar webbläsaren sidan händelser på klienten.

   ![Sidan för Salix Salsa evenemang](./media/saas-multitenantdb-get-started-deploy/salix-salsa.png)

   - Bläddra längst ned på sidan. Du kan det se namnet på databasen där data för klientorganisationen lagras i popup-meddelandet.

3. Uppdatera den **Evenemangshubben** och två nya klienter visas i listan.

## <a name="explore-the-servers-and-tenant-databases"></a>Utforska servrar och klientdatabaser

Nu titta vi på några av de resurser som har distribuerats:

1. I den [Azure-portalen](https://portal.azure.com), bläddra i listan över resursgrupper. Öppna resursgruppen du skapade när du har distribuerat programmet.

   ![Resursgrupp](./media/saas-multitenantdb-get-started-deploy/resource-group.png)

2. Klicka på **catalog mt&lt;användaren&gt;**  server. Katalogservern innehåller två databaser med namnet *tenantcatalog* och *basetenantdb*. Den *basetenantdb* databasen är en tom mall-databas. Kopieras för att skapa en ny klientdatabas, oavsett om de används för många klienter eller bara en klient.

   ![katalogserver](./media/saas-multitenantdb-get-started-deploy/catalog-server.png)

3. Gå tillbaka till den resursgrupp och välj den *tenants1-mt* server som innehåller klientdatabaserna.
    - Tenants1-databasen är en databas för flera innehavare som där de ursprungliga tre innehavarna, plus den första klienten som du lagt till, lagras. Den är konfigurerad som en 50 DTU Standard-databas.
    - Den **salixsalsa** databas innehåller Salix Salsa sköta plats som den enda klienten. Den är konfigurerad som en Standard edition-databas med 50 dtu: er som standard.

   ![klienter server](./media/saas-multitenantdb-get-started-deploy/tenants-server.png)

## <a name="monitor-the-performance-of-the-database"></a>Övervaka prestanda för databasen

Om belastningsgeneratorn har körts i flera minuter, finns tillräckligt med telemetri att titta på databasen övervakningsfunktioner som är inbyggda i Azure-portalen.

1. Bläddra till den **tenants1-mt&lt;användaren&gt;**  server och klicka på **tenants1** att visa resursanvändningen för den databas som innehåller fyra klienter. Varje klient kan komma från belastningsgeneratorn sporadiska hård belastning:

   ![övervaka tenants1](./media/saas-multitenantdb-get-started-deploy/monitor-tenants1.png)

   DTU användning diagrammet illustrerar ett snyggt sätt hur en databas för flera innehavare kan stödja en oförutsägbar arbetsbelastning över många klienter. I det här fallet tillämpar belastningsgeneratorn en sporadiska belastning på ungefär 30 dtu: er till varje klient. Den här belastningen motsvarar 60% utnyttjande av en 50 DTU-databas. Toppar som överstiger 60% är resultatet av belastning som tillämpas på fler än en klient på samma gång.

2. Bläddra till den **tenants1-mt&lt;användaren&gt;**  server och klicka på den **salixsalsa** databas. Du kan se resursanvändningen för den här databasen som innehåller endast en klient.

   ![salixsalsa databas](./media/saas-multitenantdb-get-started-deploy/monitor-salix.png)

Belastningsgeneratorn tillämpar en liknande belastning till varje klient, oavsett vilken databas varje klient finns i. Med bara en klient i den **salixsalsa** databas, kan du se att databasen stöder en mycket högre belastning än databasen med flera innehavare. 

### <a name="resource-allocations-vary-by-workload"></a>Resursallokeringar varierar beroende på arbetsbelastning

Ibland en databas för flera klienter kräver mer resurser för bra prestanda än en enda klient-databas, men inte alltid. Optimala allokeringen av resurser beror på viss arbetsbelastningsegenskaperna för klienterna i systemet.

De arbetsbelastningar som genererats av skriptet belastningen generator är endast illustrativa.

## <a name="additional-resources"></a>Ytterligare resurser

- Mer information om delade SaaS-program, se [designmönster för SaaS-program för flera innehavare](saas-tenancy-app-design-patterns.md).

- Mer information om elastiska pooler finns i:

  - [Hjälper dig att hantera och skala flera Azure SQL-databaser för elastiska pooler](sql-database-elastic-pool.md)
  - [Skala ut med Azure SQL Database](sql-database-elastic-scale-introduction.md)

## <a name="next-steps"></a>Nästa steg

I den här guiden har du lärt dig:

> [!div class="checklist"]
> - Hur du distribuerar databas för flera klienter i Wingtip biljetter SaaS-program.
> - Om servrar och databaser som ingår i appen.
> - Klienter mappas till sina data med den *catalog*.
> - Hur du etablerar nya klienter i en databas för flera innehavare och en enda klient-databasen.
> - Så här ser poolanvändning för att övervaka klientaktivitet.
> - Hur du tar bort exempelresurser för att stoppa relaterad fakturering.

Prova den [guiden etablera och katalogisera](sql-database-saas-tutorial-provision-and-catalog.md).


<!--  Link references.

A [series of related tutorials] is available that build upon this initial deployment.
[link-wtp-overivew-jumpto-saas-tutorials-97j]: saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials

-->

[link-aka-ms-deploywtp-mtapp-52k]: https://aka.ms/deploywtp-mtapp


[link-azure-get-started-powershell-41q]: https://docs.microsoft.com/powershell/azure/get-started-azureps

[link-github-wingtip-multitenantdb-55g]: https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB/



<!--  Image references.

[image-deploy-to-azure-blue-48d]: https://aka.ms/deploywtp-mtapp "Button for Deploy to Azure."
-->

[image-deploy-to-azure-blue-48d]: media/saas-multitenantdb-get-started-deploy/deploy.png "Knappen för att distribuera till Azure."

