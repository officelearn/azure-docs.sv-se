---
title: "Distribuera ett delat flera innehavare databasen SaaS-appar som använder Azure SQL Database | Microsoft Docs"
description: "Distribuera och utforska den delat Wingtip biljetter SaaS flera innehavare databasprogram, som visar SaaS mönster med hjälp av Azure SQL Database."
keywords: sql database tutorial
services: sql-database
author: MightyPen
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.workload: data-management
ms.topic: article
ms.date: 12/18/2017
ms.author: genemi
ms.openlocfilehash: 82983c8926bdf3c7256c529448e5b4f3b2c2381c
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="deploy-and-explore-a-sharded-multi-tenant-application-that-uses-azure-sql-database"></a>Distribuera och utforska ett delat flera innehavare program som använder Azure SQL Database

I den här självstudiekursen, distribuera och utforska ett flera innehavare SaaS exempelprogram som heter Wingtip biljetter. Appen Wingtip biljetter är utformat för att presentera Azure SQL Database-funktioner som förenklar implementeringen av SaaS-scenarier.

Den här implementeringen av Wingtip biljetter appen använder ett delat flera innehavare databasen mönster. Den horisontell partitionering är klient-ID. Klientdata distribueras till en viss databas enligt värden för klient-identifierare. 

Det här mönstret för databasen kan du lagra en eller flera innehavare i varje Fragmentera eller databasen. Du kan optimera för lägst kostnad genom att konfigurera varje databas som delas av flera klienter. Eller du kan optimera för isolering genom att konfigurera varje databas som lagrar bara en klient. Önskat optimering kan göras separat för varje specifik klient. Ditt val kan göras när klienten först lagras eller du ändrar dig senare. Programmet fungerar bra alls.

#### <a name="app-deploys-quickly"></a>Appen distribuerar snabbt

Appen körs i Azure-molnet och använder Azure SQL Database. Distribution av avsnittet som följer ger blå **till Azure** knappen. Om knappen är nedtryckt distribueras helt appen till din Azure-prenumeration inom fem minuter. Du har fullständig åtkomst till fungerar med enskilda programkomponenter.

Programmet har distribuerats med data för tre exempel klienter. Innehavarna lagras tillsammans i en databas för flera innehavare.

Vem som helst kan ladda ned källkoden C# och PowerShell för Wingtip biljetter från [dess GitHub-lagringsplatsen][link-github-wingtip-multitenantdb-55g].

#### <a name="learn-in-this-tutorial"></a>Lär dig i den här självstudiekursen

> [!div class="checklist"]
> - Så här distribuerar du Wingtip biljetter SaaS-program.
> - Var du kan hämta programmets källkod och av hanteringsskript.
> - Om servrar och databaser som ingår i appen.
> - Hur klienter mappas till sina data med den *katalogen*.
> - Hur du etablerar en ny klient.
> - Så här övervakar du klient aktivitet i appen.

En serie relaterade kurser är tillgängliga som bygger på den här första distributionen. Självstudierna utforska en intervallet SaaS design- och mönster. När du går igenom självstudierna, uppmanas du att stega igenom de angivna skript att se hur de olika SaaS-mönster implementeras.

## <a name="prerequisites"></a>Förutsättningar

Följande krav måste uppfyllas för att kunna köra den här självstudiekursen:

- Den senaste Azure PowerShell har installerats. Mer information finns i [komma igång med Azure PowerShell][link-azure-get-started-powershell-41q].

## <a name="deploy-the-wingtip-tickets-app"></a>Distribuera Wingtip biljetter app

#### <a name="plan-the-names"></a>Planera namn

I stegen i det här avsnittet ger du en *användare* värde som används för att säkerställa globalt unikt namn och ett namn för den *resursgruppen* som innehåller alla resurser som skapats av en distribution i appen. För en person med namnet *Ann Finley*, föreslår vi:
- *Användare:* **af1***(hennes initialer plus en siffra. Använd ett annat värde (t.ex. af2) om du distribuerar appen till en andra gång.)*
- *Resursgrupp:* **wingtip-dpt-af1** *(wingtip dpt innebär att det är appen databas per klient. Lägga till användaren namnet af1 korrelerar resursgruppens namn med namnen på de resurser som den innehåller.)*

Välj namn på din nu och Skriv ned dem. 

#### <a name="steps"></a>Steg

1. Klicka på följande blå **till Azure** knappen.
    - Azure-portalen öppnas med mallen för distribution av Wingtip biljetter SaaS.

    [![Knapp för att distribuera till Azure.][image-deploy-to-azure-blue-48d]][link-aka-ms-deploywtp-mtapp-52k]

1. Ange parametervärdena som krävs för distributionen.

    > [!IMPORTANT]
    > För den här demonstrationen Använd inte några befintliga resursgrupper, servrar eller pooler. Välj i stället **skapa en ny resursgrupp**. Ta bort den här resursgruppen när du är klar med att programmet för att stoppa relaterad fakturering.
    > Använd inte det här programmet eller alla resurser som den skapar för produktion. Vissa aspekter av autentiserings- och brandväggsinställningar server är avsiktligt osäker i appen för att underlätta demonstrationen.

    - För **resursgruppen** – Välj **Skapa nytt**, och ange sedan en **namn** för resursgruppen (skiftlägeskänsligt).
        - Välj en **plats** från den nedrullningsbara listan.
    - För **användare** -rekommenderar vi att du väljer en kort **användaren** värde.

1. **Distribuera programmet**.

    - Klicka om du accepterar villkoren.
    - Klicka på **Köp**.

1. Övervaka Distributionsstatus genom att klicka på **meddelanden**, vilket är klockikonen till höger om sökrutan. Distribuera appen Wingtip tar cirka fem minuter.

   ![distributionen lyckades](media/saas-multitenantdb-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-management-scripts"></a>Hämta och avblockera management-skript

Hämta program källa kod och hantering av skript vid distribution av programmet.

> [!NOTE]
> Körbara innehållet (skript, DLL-filer) kan blockeras av Windows när zip-filer laddas ned från en extern källa och extraheras. När skript från en zip-fil, Använd följande steg för att avblockera .zip-filen innan du extraherar. Genom att avblockera .zip-filen, kontrollera skript tillåts köra.

1. Bläddra till [WingtipTicketsSaaS MultiTenantDb GitHub-repo-](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb).
2. Klicka på **kloning eller hämta**.
3. Klicka på **hämta ZIP** och spara filen.
4. Högerklicka på den **WingtipTicketsSaaS-MultiTenantDb-master.zip** fil och markera **egenskaper**.
5. På den **allmänna** väljer **avblockera**, och klicka på **tillämpa**.
6. Klicka på **OK**.
7. Extrahera filerna.

Skript finns i den *... \\WingtipTicketsSaaS MultiTenantDb master\\Learning moduler\\*  mapp.

## <a name="update-the-configuration-file-for-this-deployment"></a>Uppdatera konfigurationsfilen för den här distributionen

Innan du kör alla skript, ange den *resursgruppen* och *användare* värdena i **UserConfig.psm1**. Ange variablerna till samma värden som du anger under distributionen.

1. Öppna... \\Learning moduler\\*UserConfig.psm1* i den *PowerShell ISE*.
2. Uppdatera *ResourceGroupName* och *namn* med specifika värden för din distribution (på rader 10 och 11 endast).
3. Spara ändringarna.

De värden som anges i den här filen som används av alla skript, så det är viktigt att de stämmer. Om du distribuerar appen, måste du välja olika värden för användar- och resursgruppen. Uppdatera UserConfig.psm1 filen igen med de nya värdena.

## <a name="run-the-application"></a>Köra programmet

Klienterna finns i appen Wingtip handelsplatser. En plats kan vara t.ex eller en sport en annan plats som är värd för händelser. Handelsplatser registrera i Wingtip som kunder och en klient-identifierare genereras för varje plats. Varje plats visar dess kommande händelser i företaget, så att offentligt kan köpa biljetter till händelser.

Varje plats hämtar en anpassad webbprogram till deras händelser och sälja biljetter. Varje webbprogram är oberoende och isoleras från andra klienter. Internt i Azure SQL Database, var data för varje klient lagras i ett delat flera innehavare-databasen, som standard. Alla data som är märkta med klient-ID.

Central **händelser hubb** webbsidan innehåller en lista med länkar till innehavare i en viss distribution. Använd följande steg för att uppleva den **händelser hubb** webbsidan och en enskild webbapp:

1. Öppna den **händelser hubb** i webbläsaren:
    - http://events.Wingtip-MT.&lt;användare&gt;. trafficmanager.net &nbsp; *(Ersätt &lt;användaren&gt; med din distribution användaren värde.)*

    ![evenemangshubben](media/saas-multitenantdb-get-started-deploy/events-hub.png)

2. Klicka på **Fabrikam Jazzklubb** i **evenemangshubben**.

   ![Händelser](./media/saas-multitenantdb-get-started-deploy/fabrikam.png)

#### <a name="azure-traffic-manager"></a>Azure Traffic Manager

För att styra distributionen av inkommande begäranden Wingtip appen använder [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md). Sidan händelser för varje klient innehåller innehavarens namn i Webbadressen. Varje URL innehåller också värdet för din specifika användare. Varje URL följs formatet visas med hjälp av följande steg:

- http://events.wingtip-mt.&lt;user&gt;.trafficmanager.net/*fabrikamjazzclub*

1. Appen händelser Parsar klientnamn från URL: en. Innehavarens namn är *fabrikamjazzclub* i föregående exempel-URL.
2. Appen sedan hashar innehavarens namn för att skapa en nyckel för att komma åt en katalog med hjälp av [Fragmentera kartan management](sql-database-elastic-scale-shard-map-management.md).
3. Appen hittar nyckeln i katalogen och erhåller motsvarande platsen för klientens databas.
4. Appen använder platsinformation för att hitta och tillgång till en databas som innehåller alla data för klienten.

#### <a name="events-hub"></a>Händelser Hub

1. Den **händelser hubb** visar en lista över alla klienter som är registrerade i katalogen och deras handelsplatser.
2. Den **händelser hubb** använder utökade metadata i katalogen för att hämta namnet på den klient som är associerade med varje avbildning för att konstruera URL: er.

I en produktionsmiljö kan du vanligtvis skapa en CNAME DNS-posten [peka företagets Internetdomän](../traffic-manager/traffic-manager-point-internet-domain.md) till trafikhanterarprofilen.

## <a name="start-generating-load-on-the-tenant-databases"></a>Börja generera belastningar på klient-databaserna

Nu när appen har distribuerats, vi få det att fungera! Den *Demo-LoadGenerator* PowerShell-skript startar en arbetsbelastning som körs för varje klient. Verkliga belastningen på många SaaS-appar är vanligtvis sporadiska och oförutsägbart. Om du vill simulera den här typen av belastningen producerar generatorn en belastning fördelad över alla klienter. Belastningen innehåller slumpmässig belastning på varje klient som uppstår vid slumpmässiga intervall. Det tar flera minuter för belastningen mönstret ut, så det är bäst att låta generator kör minst tre eller fyra minuter innan övervakning belastningen.

1. I den *PowerShell ISE*öppnar den... \\Learning moduler\\verktyg\\*Demo-LoadGenerator.ps1* skript.
2. Tryck **F5** för att köra skriptet och starta belastningsgeneratorn (Lämna standardvärdet för parametern för tillfället).

Den *Demo-LoadGenerator.ps1* skriptet öppnas en annan PowerShell-session där belastningen generator körs. Läs in generator körs i den här sessionen som en aktivitet i förgrunden som anropar bakgrundsjobb för generering av belastningen, ett för varje klient.

När aktiviteten förgrunden har startat finns kvar i ett jobb startar tillstånd. Aktiviteten startar ytterligare bakgrundsjobb för alla nya klienter som tillhandahålls senare.

Stänga PowerShell-sessionen avbryts alla jobb.

Du kanske vill starta om belastningen generator sessionen om du vill använda olika parametervärden. I så fall, stänger du PowerShell generation sessionen och kör sedan den *Demo-LoadGenerator.ps1*.

## <a name="provision-a-new-tenant-into-the-sharded-database"></a>Etablera en ny klient till delat databasen

Den första distributionen innehåller tre exempel hyresgäster i den *Tenants1* databas. Nu ska vi skapa en annan klient och notera att dess effekter på det distribuerade programmet. Tryck på en nyckel för att skapa en ny klient i det här steget:

1. Öppna... \\Learning moduler\\etablera och katalogen\\*Demo-ProvisionTenants.ps1* i den *PowerShell ISE*.
2. Tryck på **F5** (inte **F8**) att köra skriptet (lämna standardvärdena för tillfället).

   > [!NOTE]
   > Du måste köra PowerShell-skript genom att trycka på den **F5** nyckel inte genom att trycka på **F8** att köra valda delar av skriptet. Problem med **F8** är att den *$PSScriptRoot* variabeln utvärderas inte. Den här variabeln krävs för många skript att navigera i mappar, eller att anropa andra skript eller importera moduler.

Den nya innehavaren Röd lönn tävling har lagts till i den *Tenants1* databasen och registrerats i katalogen. Den nya innehavaren har biljett sälja **händelser** plats visas i webbläsaren:

![Ny klient](./media/saas-multitenantdb-get-started-deploy/red-maple-racing.png)

Uppdatera den **händelser hubb**, och den nya innehavaren visas i listan.

## <a name="provision-a-new-tenant-in-its-own-database"></a>Etablera en ny klient i en egen databas

Delat modell för flera klienter kan du välja om du vill etablera en ny klient i en databas som innehåller andra klienter eller i en databas med sin egen. En klient separat i en egen databas har följande fördelar:
- Prestanda för klientens databas kan hanteras utan att behöva angripa med behoven hos andra klienter.
- Om det behövs kan databasen återställas till en tidigare tidpunkt, eftersom inga andra hyresgäster kan påverkas.

Du kan välja att placera kostnadsfria kunder eller ekonomi kunder i flera databaser. Du kan placera varje premium-klientorganisation i sin egen dedicerad databas. Om du skapar många databaser som innehåller endast en klient kan hantera du dem alla tillsammans i en elastisk pool för att optimera resurskostnader.

Nu ska vi att etablera en annan klient nu i en egen databas:

1. I... \\Learning moduler\\etablera och katalogen\\*Demo-ProvisionTenants.ps1*, ändra *$TenantName* till **Salix Salsa**, *$VenueType* till **webbsidor** och *$Scenario* till **2**.

2. Tryck på **F5** att köra skriptet igen.
    - Detta **F5** tryck etablerar den nya innehavaren i en separat databas. Databasen och klienten har registrerats i katalogen. Sedan öppnas i webbläsaren sidan händelser för innehavaren.

   ![Salix Salsa händelser sida](./media/saas-multitenantdb-get-started-deploy/salix-salsa.png)

   - Bläddra till längst ned på sidan. I popup-meddelandet finns det databasnamn som klientdata lagras.

3. Uppdatera den **händelser hubb** och två nya klienter visas i listan.

## <a name="explore-the-servers-and-tenant-databases"></a>Utforska servrar och databaser för innehavare

Nu igenom vi några av de resurser som har distribuerats:

1. I den [Azure-portalen](http://portal.azure.com), bläddra i listan över resursgrupper. Öppna den resursgrupp som du skapade när du distribuerade programmet.

   ![Resursgrupp](./media/saas-multitenantdb-get-started-deploy/resource-group.png)

2. Klicka på **katalog huvudmålservern&lt;användare&gt;**  server. Katalogservern innehåller två databaserna med namnet *tenantcatalog* och *basetenantdb*. Den *basetenantdb* databasen är en tom mall-databas. Det kopieras om du vill skapa en ny klient databas om för många klienter eller en klient.

   ![katalogserver](./media/saas-multitenantdb-get-started-deploy/catalog-server.png)

3. Gå tillbaka till resursgruppen och välj den *tenants1 huvudmålservern* server som innehåller klient-databaser.
    - Tenants1-databasen är en databas för flera innehavare där de ursprungliga tre innehavarna plus första klienten som du lagt till, lagras. Den är konfigurerad som en 50 Standard DTU-databas.
    - Den **salixsalsa** databasen innehåller Salix Salsa webbsidor plats som den enda klienten. Den är konfigurerad som en Standard edition-databas med 50 dtu: er som standard.

   ![server för klienter](./media/saas-multitenantdb-get-started-deploy/tenants-server.png)


## <a name="monitor-the-performance-of-the-database"></a>Övervaka prestanda för databasen

Om belastningen generator har körts i flera minuter, finns tillräckligt med telemetri titta på databasen övervakningsfunktionerna i Azure-portalen.

1. Bläddra till den **tenants1 huvudmålservern&lt;användare&gt;**  server och klicka på **tenants1** att visa resursanvändningen för databasen med fyra hyresgäster i den. Varje klient regleras sporadiska belastat från belastningen generator:

   ![övervaka tenants1](./media/saas-multitenantdb-get-started-deploy/monitor-tenants1.png)

   DTU-användning diagram förtydligar snyggt hur en databas med flera innehavare kan stödja en oförutsägbart arbetsbelastningen mellan många klienter. I det här fallet är belastningen generator tillämpades en sporadiska belastningen på ungefär 30 dtu: er till varje klient. Den här inläsningen är lika med 60% användning av en 50 DTU-databas. Toppar som överskrider 60% är resultatet av belastning som tillämpas på fler än en klient på samma gång.

2. Bläddra till den **tenants1 huvudmålservern&lt;användare&gt;**  server och klicka på den **salixsalsa** databas. Du kan se resursanvändningen på den här databasen som innehåller endast en klient.

   ![salixsalsa databas](./media/saas-multitenantdb-get-started-deploy/monitor-salix.png)

Läs in generator tillämpar en liknande belastning till varje klient, oavsett vilken databas varje klient är i. Med bara en klient i den **salixsalsa** databasen, kan du se att databasen stöder en mycket högre belastning än databasen med flera innehavare. 

#### <a name="resource-allocations-vary-by-workload"></a>Resursallokeringar varierar beroende på arbetsbelastning

Ibland en databas med flera innehavare kräver mer resurser för goda prestanda än en enskild klient-databas, men inte alltid. Den optimala allokeringen av resurser beror på viss arbetsbelastning egenskaper för klienterna i systemet.

Arbetsbelastningar som genererats av belastningen generator skriptet är endast för illustration.

## <a name="additional-resources"></a>Ytterligare resurser

- Mer information om flera innehavare SaaS-program, se [designmönster för SaaS-program med flera innehavare](saas-tenancy-app-design-patterns.md).

- Om du vill veta mer om elastiska pooler, se:
    - [Hjälper dig att hantera och skala flera Azure SQL-databaser för elastiska pooler](sql-database-elastic-pool.md)
    - [Skala ut med Azure SQL Database](sql-database-elastic-scale-introduction.md)

## <a name="next-steps"></a>Nästa steg

I den här guiden har du lärt dig:

> [!div class="checklist"]
> - Så här distribuerar du databasprogram Wingtip biljetter SaaS flera innehavare.
> - Om servrar och databaser som ingår i appen.
> - Klienter som är mappade till sina data med den *katalogen*.
> - Hur du etablerar nya klienter i en databas för flera innehavare och databasen för en klient.
> - Så här visar du poolen nätverksanvändning för att övervaka aktivitet på klienten.
> - Ta bort resurser för att stoppa relaterade fakturering.

Prova den [etablera och katalogen kursen](sql-database-saas-tutorial-provision-and-catalog.md).


<!--  Link references.

A [series of related tutorials] is available that build upon this initial deployment.
[link-wtp-overivew-jumpto-saas-tutorials-97j]: saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials

-->

[link-aka-ms-deploywtp-mtapp-52k]: http://aka.ms/deploywtp-mtapp


[link-azure-get-started-powershell-41q]: https://docs.microsoft.com/powershell/azure/get-started-azureps

[link-github-wingtip-multitenantdb-55g]: https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB/



<!--  Image references.

[image-deploy-to-azure-blue-48d]: http://aka.ms/deploywtp-mtapp "Button for Deploy to Azure."
-->

[image-deploy-to-azure-blue-48d]: media/saas-multitenantdb-get-started-deploy/deploy.png "Knappen för att distribuera till Azure."

