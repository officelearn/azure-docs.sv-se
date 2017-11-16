---
title: "Distribuera ett delat flera innehavare databasen SaaS-appar som använder Azure SQL Database | Microsoft Docs"
description: "Distribuera och utforska den delat Wingtip biljetter SaaS flera innehavare databasprogram, som visar SaaS mönster med hjälp av Azure SQL Database."
keywords: sql database tutorial
services: sql-database
documentationcenter: 
author: stevestein
manager: craigg
editor: billgib;MightyPen
ms.service: sql-database
ms.custom: scale out apps
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: sstein
ms.openlocfilehash: bc96221abf62677b53df43daa44a925ac5792043
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2017
---
# <a name="deploy-and-explore-a-sharded-multi-tenant-application-that-uses-azure-sql-database"></a>Distribuera och utforska ett delat flera innehavare program som använder Azure SQL Database

I den här självstudiekursen, distribuera och utforska ett SaaS flera innehavare databasen exempelprogram som heter Wingtip biljetter. Appen Wingtip är utformat för att presentera Azure SQL Database-funktioner som förenklar implementeringen av SaaS-scenarier.

Den här implementeringen av Wingtips använder ett delat flera innehavare databasen mönster. Den horisontell partitionering är klient-ID. Klientdata distribueras till databasen enligt värden för klient-identifierare. Oavsett hur många klienter som en viss databas innehåller är alla databaser flera innehavare i den mening att tabellscheman inkluderar ett klient-ID. 

Det här mönstret för databasen kan du lagra en eller flera innehavare i varje Fragmentera eller databasen. Du kan optimera för lägst kostnad genom att konfigurera varje databas som delas av flera klienter. Eller du kan optimera för isolering genom att konfigurera varje databas som lagrar bara en klient. Önskat optimering kan göras separat för varje specifik klient. Ditt val kan göras när klienten först lagras eller du ändrar dig senare. Programmet fungerar bra alls.

#### <a name="app-deploys-quickly"></a>Appen distribuerar snabbt

Distribution av avsnittet som följer ger den **till Azure** knappen. Om knappen är nedtryckt distribueras helt appen Wingtip fem minuter senare. Wingtip appen körs i Azure-molnet och använder Azure SQL Database. Wingtip distribueras till din Azure-prenumeration. Du har fullständig åtkomst till fungerar med enskilda programkomponenter.

Programmet har distribuerats med data för tre exempel klienter. Innehavarna lagras tillsammans i en databas för flera innehavare.

Vem som helst kan ladda ned källkoden C# och PowerShell för Wingtip biljetter från [vår Github-lagringsplats][link-github-wingtip-multitenantdb-55g].

#### <a name="learn-in-this-tutorial"></a>Lär dig i den här självstudiekursen

> [!div class="checklist"]

> - Så här distribuerar du Wingtip SaaS-program.
> - Var du kan hämta programmets källkod och av hanteringsskript.
> - Om servrar och databaser som ingår i appen.
> - Hur klienter mappas till sina data med den *katalogen*.
> - Hur du etablerar en ny klient.
> - Så här övervakar du klient aktivitet i appen.

En serie relaterade kurser är tillgängliga som bygger på den här första distributionen. Självstudierna utforska en intervallet SaaS design- och mönster. När du går igenom självstudierna, uppmanas du att stega igenom de angivna skript att se hur de olika SaaS-mönster implementeras.

## <a name="prerequisites"></a>Krav

Följande krav måste uppfyllas för att kunna köra den här självstudiekursen:

- Den senaste Azure PowerShell har installerats. Mer information finns i [komma igång med Azure PowerShell][link-azure-get-started-powershell-41q].

## <a name="deploy-the-wingtip-tickets-app"></a>Distribuera Wingtip biljetter app

1. Klicka på följande **till Azure** knappen.
    - Azure-portalen öppnas med mallen för distribution av Wingtip biljetter SaaS. Mallen kräver två parametervärden: ett namn för en ny resursgrupp och en 'user'-värde som särskiljer den här distributionen från andra distributioner av appen. Nästa steg innehåller information för att ange dessa värden.
        - Glöm inte att känna till de exakta värden som du använder, eftersom du behöver dem senare för en konfigurationsfil.

    [![Knapp för att distribuera till Azure.][image-deploy-to-azure-blue-48d]][link-aka-ms-deploywtp-mtapp-52k]

2. Ange parametervärden som krävs för distributionen.

    > [!IMPORTANT]
    > Vissa autentiserings- och brandväggsinställningar server är inte avsiktligt säkra att underlätta demonstrationen. Välj **skapa en ny resursgrupp**, och Använd inte befintliga resursgrupper, servrar eller pooler. Använd inte det här programmet eller alla resurser som den skapar för produktion. Ta bort den här resursgruppen när du är klar med att programmet för att stoppa relaterad fakturering.

    Det är bäst att använda bara gemena bokstäver, siffror och bindestreck i din resursnamn.

    - För **resursgruppen** – Välj **Skapa nytt**, och ange sedan en **namn** för resursgruppen (skiftlägeskänsligt).
        - Vi rekommenderar att alla bokstäver i din resursgruppens namn är gemener.
        - Vi rekommenderar att du lägger till ett streck följt av dina initialer, följt av en siffra: till exempel *wingtip af1*.
        - Välj en **plats** från den nedrullningsbara listan.
    - För **användare** -rekommenderar vi att du väljer en kort **användare** värde, till exempel din initialer plus en siffra: till exempel *af1*.

3. **Distribuera programmet**.

    - Klicka om du accepterar villkoren.
    - Klicka på **Köp**.

4. Övervaka Distributionsstatus genom att klicka på **meddelanden**, vilket är klockikonen till höger om sökrutan. Distribuera appen Wingtip tar cirka fem minuter.

   ![distributionen lyckades](media/saas-multitenantdb-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-management-scripts"></a>Hämta och avblockera management-skript

Hämta program källa kod och hantering av skript vid distribution av programmet.

> [!IMPORTANT]
> Körbara innehållet (skript, DLL-filer) kan blockeras av Windows när zip-filer laddas ned från en extern källa och extraheras. När skript från en zip-fil, Använd följande steg för att avblockera .zip-filen innan du extraherar. Genom att avblockera .zip-filen Kontrollera du skript tillåts köra.

1. Bläddra till [github-repo-WingtipTicketsSaaS MultiTenantDb](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb).
2. Klicka på **kloning eller hämta**.
3. Klicka på **hämta ZIP** och spara filen.
4. Högerklicka på den **WingtipTicketsSaaS-MultiTenantDb-master.zip** fil och markera **egenskaper**.
5. På den **allmänna** väljer **avblockera**, och klicka på **tillämpa**.
6. Klicka på **OK**.
7. Extrahera filerna.

Skript finns i den *... \\WingtipTicketsSaaS MultiTenantDb master\\Learning moduler\\*  mapp.

## <a name="update-the-configuration-file-for-this-deployment"></a>Uppdatera konfigurationsfilen för den här distributionen

Innan du kör alla skript, ange den *resursgruppen* och *användare* värdena i **UserConfig.psm1**. Ange variablerna till samma värden som du anger under distributionen.

1. Öppna... \\inlärningsmoduler\\*UserConfig.psm1* i *PowerShell ISE*
2. Uppdatera *ResourceGroupName* och *namn* med specifika värden för din distribution (på rader 10 och 11 endast).
3. Spara ändringarna.

De värden som anges i den här filen som används av alla skript, så det är viktigt att de stämmer. Om du distribuerar appen, se till att du väljer en annan grupp och användare resursvärde. Uppdatera UserConfig med de nya värdena.

## <a name="run-the-application"></a>Köra programmet

Appen visar platser, som konsertlokaler, jazzklubbar, sportklubbar som organiserar evenemang. Handelsplatser registrera som kunder (eller klienter) i Wingtip-plattformen för ett enkelt sätt att visa en lista över händelser och sälja biljetter. Varje plats får en personaliserad webbapp för att hantera sina evenemang och sälja biljetter, oberoende och isolerat från andra klienter. Varje klient data lagras under försättsbladen, som standard i en databas med delat flera innehavare.

Central **händelser hubb** innehåller en lista med länkar till innehavare i din distribution.

1. Öppna den *händelser hubb* i webbläsaren:
    - http://events.Wingtip. &lt;Användare&gt;. trafficmanager.net &nbsp; *(Ersätt med din distribution användaren värdet).*

    ![evenemangshubben](media/saas-multitenantdb-get-started-deploy/events-hub.png)

2. Klicka på **Fabrikam Jazzklubb** i *evenemangshubben*.

   ![Händelser](./media/saas-multitenantdb-get-started-deploy/fabrikam.png)

Att kontrollera distributionen av inkommande begäranden, appen använder [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md). Sidorna händelser som är specifika för klienten, inkludera innehavarens namn i URL: en. URL: er också innehålla värdet för din specifika användare och följande format:

- http://events.Wingtip. &lt;Användare&gt;.trafficmanager.net/*fabrikamjazzclub*
 
Händelser appen Parsar klientnamn från URL: en och hash-kodar den för att skapa en nyckel för att få åtkomst till en katalog med hjälp av [Fragmentera kartan management](sql-database-elastic-scale-shard-map-management.md). Katalogen mappar nyckeln till klientens databasplatsen. Den **händelser hubb** visar en lista över alla klienter som är registrerade i katalogen. Den **händelser hubb** använder utökade metadata i katalogen för att hämta namnet på den klient som är associerade med varje avbildning för att konstruera URL: er.

I en produktionsmiljö skulle du vanligtvis skapa en CNAME DNS-posten [peka företagets Internetdomän](../traffic-manager/traffic-manager-point-internet-domain.md) till trafikhanterarprofilen.

## <a name="start-generating-load-on-the-tenant-databases"></a>Börja generera belastningar på klient-databaserna

Nu när appen har distribuerats, vi få det att fungera! Den *Demo-LoadGenerator* PowerShell-skript startar en arbetsbelastning som körs för varje klient. Verkliga belastningen på många SaaS-appar är vanligtvis sporadiska och oförutsägbart. Om du vill simulera den här typen av belastningen producerar generatorn en belastning fördelad över alla klienter. Belastningen innehåller slumpmässig belastning på varje klient som uppstår vid slumpmässiga intervall. Det tar flera minuter för belastningen mönstret ut, så det är bäst att låta generator kör minst tre eller fyra minuter innan övervakning belastningen.

1. I den *PowerShell ISE*öppnar den... \\Learning moduler\\verktyg\\*Demo-LoadGenerator.ps1* skript.
2. Tryck **F5** för att köra skriptet och starta belastningsgeneratorn (Lämna standardvärdet för parametern för tillfället).

> [!IMPORTANT]
> Den *Demo-LoadGenerator.ps1* skriptet öppnas en annan PowerShell-session där belastningen generator körs. Läs in generator körs i den här sessionen som en aktivitet i förgrunden som anropar bakgrundsjobb för generering av belastningen, ett för varje klient.

När aktiviteten förgrunden har startat finns kvar i ett jobb startar tillstånd. Aktiviteten startar ytterligare bakgrundsjobb för alla nya klienter som tillhandahålls senare.

Stänga PowerShell-sessionen avbryts alla jobb.

Du kanske vill starta om belastningen generator sessionen om du vill använda olika parametervärden. I så fall, stänger du PowerShell generation sessionen och kör sedan den *Demo-LoadGenerator.ps1*.

## <a name="provision-a-new-tenant-into-the-sharded-database"></a>Etablera en ny klient till delat databasen

Den första distributionen innehåller tre exempel hyresgäster i den *Tenants1* databas. Nu ska vi skapa en annan klient om du vill se hur detta påverkar det distribuerade programmet. I det här steget kan skapa du snabbt en ny klient.

1. Öppna... \\Modules\Provision och katalogen\\*Demo-ProvisionTenants.ps1* i den *PowerShell ISE*.
2. Tryck på **F5** att köra skriptet (lämna standardvärdena för tillfället).

   > [!NOTE]
   > Många Wingtip biljetter SaaS-skript använder *$PSScriptRoot* Tillåt navigering av mappar, eller anropa andra skript eller importera moduler. Den här variabeln utvärderas bara när skriptet körs hela genom att trycka på **F5**.  Markera och kör en markering (**F8**) kan leda till fel, trycker du på **F5** när du kör skript.

Den nya innehavaren Röd lönn tävling har lagts till i den *Tenants1* databasen och registrerats i katalogen. Den nya innehavaren har biljett sälja *händelser* plats visas i webbläsaren:

![Ny klient](./media/saas-multitenantdb-get-started-deploy/red-maple-racing.png)

Uppdatera den *händelser hubb* och den nya innehavaren visas i listan.

## <a name="provision-a-new-tenant-in-its-own-database"></a>Etablera en ny klient i en egen databas

Delat modell för flera klienter kan du välja om för att etablera en ny klient i en databas som innehåller andra klienter eller att etablera klienten i en databas med sin egen. En klient i sin egen fördelar data isoleras från andra klienter-data från. Isolering kan du hantera prestanda för den klienten oberoende av andra klienter. Det är också enklare att återställa data till en tidigare tidpunkt för isolerade klient. Du kan välja att placera kostnadsfria eller vanliga kunder i en databas med flera innehavare och premium kunder i enskilda databaser. Om du skapar många databaser att var och en innehåller endast en klient kan hantera du dem alla tillsammans i en elastisk pool för att optimera resurskostnader.  

Nu etablera vi en annan klient nu i en egen databas.

1. I... \\Learning moduler\\etablera och katalogen\*Demo-ProvisionTenants.ps1* ändra *$TenantName* till **Salix Salsa**, *$VenueType*  till **webbsidor** och *$Scenario* till **2**.

2. Tryck på **F5** att köra skriptet igen.
    - Den här tryck på F5 etablerar den nya innehavaren i en separat databas. Databasen och klienten har registrerats i katalogen. Sedan öppnas i webbläsaren sidan händelser för innehavaren.

   ![Salix Salsa händelser sida](./media/saas-multitenantdb-get-started-deploy/salix-salsa.png)

   - Bläddra till längst ned på sidan. I popup-meddelandet finns det databasnamn som klientdata lagras.

3. Uppdatera hubben händelser och två nya klienter nu visas i listan.



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

Om belastningen generator har körts i flera minuter, finns tillräckligt med telemetri ska börja söka på några av databasen övervakningsfunktionerna som är inbyggda i portalen.

1. Bläddra till den **tenants1 huvudmålservern&lt;användare&gt;**  server och klicka på **tenants1** att visa resursanvändningen för databasen med fyra hyresgäster i den. Varje klient regleras sporadiska belastat från belastningen generator:

   ![övervaka tenants1](./media/saas-multitenantdb-get-started-deploy/monitor-tenants1.png)

   DTU-användning diagram förtydligar snyggt hur en databas med flera innehavare kan stödja en oförutsägbart arbetsbelastningen mellan många klienter. I det här fallet är belastningen generator tillämpades en sporadiska belastningen på ungefär 30 dtu: er till varje klient. Den här inläsningen är lika med 60% användning av en 50 DTU-databas. Toppar som överskrider 60% är resultatet av belastning som tillämpas på fler än en klient på samma gång.

2. Bläddra till den **tenants1 huvudmålservern&lt;användare&gt;**  server och klicka på den **salixsalsa** databasen för att visa resursanvändningen för den här databasen som innehåller endast en klient.

   ![salixsalsa databas](./media/saas-multitenantdb-get-started-deploy/monitor-salix.png)

Läs in generator tillämpar en liknande belastning till varje klient, oavsett vilken databas varje klient är i. Med bara en klient i den **salixsalsa** databasen, kan du se att databasen stöder en mycket högre belastning än databasen med flera innehavare. 

> [!NOTE]
> Belastningar som skapas av belastningen generator är endast för jämförelseändamål.  Resurserna för flera innehavare och stöd för en innehavare databaser och hur många klienter som du kan vara värd i en databas med flera innehavare, beror på de faktiska arbetsbelastning mönster i ditt program.


## <a name="next-steps"></a>Nästa steg

I den här guiden har du lärt dig:

> [!div class="checklist"]

> - Så här distribuerar du databasprogram Wingtip biljetter SaaS flera innehavare
> - Om servrar och databaser som ingår i appen
> - Klienter mappas till sina data med *katalogen*
> - Hur du etablerar nya klienter i en databas för flera innehavare och databasen för en klient.
> - Hur du ser poolanvändning för att övervaka klientaktivitet
> - Hur du tar bort exempelresurser för att stoppa relaterad fakturering

Prova den [etablera och katalogen kursen](sql-database-saas-tutorial-provision-and-catalog.md).



## <a name="additional-resources"></a>Ytterligare resurser

- Läs mer om SaaS-program med flera klienter i [*Designmönster för SaaS-program med flera klienter*](https://docs.microsoft.com/azure/sql-database/sql-database-design-patterns-multi-tenancy-saas-applications)








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

[image-deploy-to-azure-blue-48d]: media/saas-multitenantdb-get-started-deploy/deploy.png

