---
title: Distribuera en fragmenterad SaaS-databasapp för flera innehavare
description: Distribuera och utforska det fragmenterade Wingtip Tickets SaaS-databasprogrammet för flera innehavare som demonstrerar SaaS-mönster med hjälp av Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: billgib, stein
ms.date: 10/16/2018
ms.openlocfilehash: 3277318e01362df8fc21ff7ca769aaeb8006abc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73827992"
---
# <a name="deploy-and-explore-a-sharded-multi-tenant-application"></a>Distribuera och utforska ett fragmenterat program för flera innehavare

I den här självstudien distribuerar och utforskar du ett exempel på SaaS-ett-exempel som heter Wingtip Tickets. Wingtip-biljetter-appen är utformad för att visa upp funktioner i Azure SQL Database som förenklar implementeringen av SaaS-scenarier.

Den här implementeringen av wingtip-biljetter-appen använder ett fragmenterat databasmönster med flera innehavare. Shardingen är vid tenant-identifierare. Klientdata distribueras till en viss databas enligt klientidentifieringsvärdena. 

Med det här databasmönstret kan du lagra en eller flera klienter i varje shard eller databas. Du kan optimera för lägsta kostnad genom att låta varje databas delas av flera klienter. Du kan också optimera för isolering genom att låta varje databas lagra endast en klient. Ditt optimeringsval kan göras oberoende av varandra för varje specifik klient. Ditt val kan göras när hyresgästen först lagras, eller så kan du ändra dig senare. Programmet är utformat för att fungera bra åt båda hållen.

## <a name="app-deploys-quickly"></a>App distribuerar snabbt

Appen körs i Azure-molnet och använder Azure SQL Database. Distributionsavsnittet som följer ger den blå **knappen Distribuera till Azure.** När knappen trycks in distribueras appen helt till din Azure-prenumeration inom fem minuter. Du har full tillgång till arbete med de enskilda programkomponenterna.

Programmet distribueras med data för tre exempelklienter. Klienterna lagras tillsammans i en databas med flera innehavare.

Vem som helst kan ladda ner C# och PowerShell källkod för Wingtip Biljetter från [sin GitHub-lagringsplats][link-github-wingtip-multitenantdb-55g].

## <a name="learn-in-this-tutorial"></a>Lär dig i den här självstudien

> [!div class="checklist"]
> - Så här distribuerar du Wingtip Tickets SaaS-programmet.
> - Var du kan hämta programmets källkod och hanteringsskript.
> - Om de servrar och databaser som utgör appen.
> - Så här mappas klienter till sina data med *katalogen*.
> - Så här etablerar du en ny klient.
> - Så här övervakar du klientaktivitet i appen.

En serie relaterade självstudier är tillgängliga som bygger på den första distributionen. Självstudierna utforskar en rad SaaS design- och hanteringsmönster. När du arbetar igenom självstudierna uppmanas du att gå igenom de medföljande skripten för att se hur de olika SaaS-mönstren implementeras.

## <a name="prerequisites"></a>Krav

Följande krav måste uppfyllas för att kunna köra den här självstudiekursen:

- Den senaste Azure PowerShell är installerad. Mer information finns i [Komma igång med Azure PowerShell][link-azure-get-started-powershell-41q].

## <a name="deploy-the-wingtip-tickets-app"></a>Distribuera appen Wingtip-biljetter

### <a name="plan-the-names"></a>Planera namnen

I stegen i det här avsnittet anger du ett *användarvärde* som används för att säkerställa att resursnamn är globalt unika och ett namn för *resursgruppen* som innehåller alla resurser som skapas av en distribution av appen. För en person som heter *Ann Finley*föreslår vi:
- *Användare:* **af1**  *(Deras initialer, plus en siffra. Använd ett annat värde (t.ex. af2) om du distribuerar appen en andra gång.)*
- *Resursgrupp:* **wingtip-mt-af1** *(wingtip-mt anger att detta är den fragmenterade appen för flera innehavare. Lägga till användarnamnet af1 korrelerar resursgruppnamnet med namnen på de resurser som den innehåller.)*

Välj dina namn nu och skriv ner dem. 

### <a name="steps"></a>Steg

1. Klicka på följande blå **distribution till Azure-knapp.**
   - Den öppnar Azure-portalen med Wingtip Tickets SaaS-distributionsmallen.

     [![Knapp för distribuera till Azure.][image-deploy-to-azure-blue-48d]][link-aka-ms-deploywtp-mtapp-52k]

1. Ange de parametervärden som krävs för distributionen.

    > [!IMPORTANT]
    > Använd inte befintliga resursgrupper, servrar eller pooler för den här demonstrationen. Välj i stället **Skapa en ny resursgrupp**. Ta bort den här resursgruppen när du är klar med att programmet för att stoppa relaterad fakturering.
    > Använd inte det här programmet, eller några resurser som det skapar, för produktion. Vissa aspekter av autentisering och serverbrandväggsinställningarna är avsiktligt osäkra i appen för att underlätta demonstrationen.

    - För **resursgrupp** - Välj **Skapa ny**och ange sedan ett **namn** för resursgruppen (skiftlägeskänslig).
        - Välj en **plats** i listrutan.
    - För **användare** - Vi rekommenderar att du väljer ett kort **användarvärde.**

1. **Distribuera programmet**.

    - Klicka för att godkänna villkoren.
    - Klicka på **Köp**.

1. Övervaka distributionsstatus genom att klicka på **Meddelanden**, som är klockikonen till höger om sökrutan. Det tar cirka fem minuter att distribuera Wingtip-appen.

   ![distributionen lyckades](media/saas-multitenantdb-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-management-scripts"></a>Hämta och avblockera hanteringsskripten

Medan programmet distribueras hämtar du programmets källkod och hanteringsskript.

> [!NOTE]
> Körbart innehåll (skript, DLL:er) kan blockeras av Windows när zip-filer hämtas från en extern källa och extraheras. När du extraherar skripten från en zip-fil använder du följande steg för att låsa upp ZIP-filen innan du extraherar. Genom att avblockera ZIP-filen kontrollerar du att skripten tillåts köras.

1. Bläddra till [WingtipTicketsSSaaS-MultiTenantDb GitHub repo](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb).
2. Klicka på **Klona eller hämta**.
3. Klicka på **Hämta ZIP** och spara filen.
4. Högerklicka på filen **WingtipTicketsSSaaS-MultiTenantDb-master.zip** och välj **Egenskaper**.
5. Välj Ta bort **blockering**på fliken **Allmänt** och klicka på **Använd**.
6. Klicka på **OK**.
7. Extrahera filerna.

Skripten finns i *.. Mappen WingtipTicketsSSaaS-MultiTenantDb-master\\Learning Modules.\\ \\*

## <a name="update-the-configuration-file-for-this-deployment"></a>Uppdatera konfigurationsfilen för den här distributionen

Innan du kör några skript anger du *resursgruppen* och *användarvärdena* i **UserConfig.psm1**. Ange dessa variabler till samma värden som du anger under distributionen.

1. Öppna... \\Utbildningsmoduler\\*UserConfig.psm1* i *PowerShell ISE*.
2. Uppdatera *ResourceGroupName* och *Namn* med specifika värden för distributionen (endast på raderna 10 och 11).
3. Spara ändringarna.

De värden som anges i den här filen används av alla skript, så det är viktigt att de är korrekta. Om du distribuerar om appen måste du välja olika värden för användar- och resursgrupp. Uppdatera sedan filen UserConfig.psm1 igen med de nya värdena.

## <a name="run-the-application"></a>Köra appen

I Wingtip-appen är hyresgästerna mötesplatser. En lokal kan vara konsertsal, en idrottsklubb eller någon annan plats som är värd för evenemang. Platserna registrerar sig i Wingtip som kunder, och en klientidentifierare genereras för varje plats. Varje plats listar sina kommande evenemang i Wingtip, så att allmänheten kan köpa biljetter till evenemangen.

Varje plats får en personlig webbapp för att lista sina evenemang och sälja biljetter. Varje webbapp är oberoende och isolerad från andra klienter. Internt i Azure SQL Database lagras var och en av data för varje klient i en fragmenterad databas med flera innehavare, som standard. Alla data taggas med klientidentifieraren.

En central **events hub-webbsida** innehåller en lista med länkar till klienterna i just distributionen. Gör så här för att uppleva webbsidan **Händelserhub** och en enskild webbapp:

1. Öppna **händelsehubben** i webbläsaren:
   - http://events.wingtip-mt.&lt;användare&gt;.trafficmanager.net &nbsp; *(Ersätt &lt;användare&gt; med distributionens användarvärde.)*

     ![evenemangshubben](media/saas-multitenantdb-get-started-deploy/events-hub.png)

2. Klicka på **Fabrikam Jazzklubb** i **evenemangshubben**.

   ![Händelser](./media/saas-multitenantdb-get-started-deploy/fabrikam.png)

### <a name="azure-traffic-manager"></a>Azure Traffic Manager

För att styra distributionen av inkommande begäranden använder Wingtip-appen [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md). Händelsesidan för varje klient innehåller klientnamnet i webbadressen. Varje webbadress innehåller också ditt specifika användarvärde. Varje webbadress följer det visade formatet med hjälp av följande steg:

- http://events.wingtip-mt.&lt;användare&gt;.trafficmanager.net/*fabrikamjazzclub*

1. Händelseappen tolkar klientnamnet från webbadressen. Klientnamnet är *fabrikamjazzclub* i föregående exempel-URL.
2. Appen hashar sedan klientnamnet för att skapa en nyckel för att komma åt en katalog med hjälp av [fragmentkarthantering](sql-database-elastic-scale-shard-map-management.md).
3. Appen hittar nyckeln i katalogen och hämtar motsvarande plats för klientens databas.
4. Appen använder platsinformationen för att hitta och komma åt en databas som innehåller alla data för klienten.

### <a name="events-hub"></a>Hub för evenemang

1. **Händelsehubben** listar alla klienter som är registrerade i katalogen och deras platser.
2. **Händelsehubben** använder utökade metadata i katalogen för att hämta klientens namn som är associerat med varje mappning för att konstruera url:erna.

I en produktionsmiljö skapar du vanligtvis en CNAME DNS-post för att [peka ett företags internetdomän](../traffic-manager/traffic-manager-point-internet-domain.md) till traffic manager-profilen.

## <a name="start-generating-load-on-the-tenant-databases"></a>Börja generera belastningar på klient-databaserna

Nu när appen är distribuerad, låt oss sätta det i arbete! *Demo-LoadGenerator PowerShell-skriptet* startar en arbetsbelastning som körs för varje klient. Den verkliga belastningen på många SaaS-appar är vanligtvis sporadisk och oförutsägbar. För att simulera den här typen av belastning producerar generatorn en belastning fördelad på alla klienter. Inläsningen innehåller randomiserade skurar på varje klient som inträffar med slumpmässiga intervall. Det tar flera minuter för belastningsmönstret att växa fram, så det är bäst att låta generatorn köra i minst tre eller fyra minuter innan belastningen övervakas.

1. I *PowerShell ISE*, öppna ... \\Utbildningsmoduler\\\\*Demo-LoadGenerator.ps1-skript.*
2. Tryck **F5** för att köra skriptet och starta belastningsgeneratorn (Lämna standardvärdet för parametern för tillfället).

*Skriptet Demo-LoadGenerator.ps1* öppnar en annan PowerShell-session där laddningsgeneratorn körs. Belastningsgeneratorn körs i den här sessionen som en förgrundsaktivitet som anropar jobb för bakgrundsinläsning, ett för varje klient.

När förgrundsaktiviteten har startats förblir den i ett jobbansnat tillstånd. Aktiviteten startar ytterligare bakgrundsjobb för alla nya klienter som sedan etableras.

Om du stänger PowerShell-sessionen stoppas alla jobb.

Du kanske vill starta om laddningsgeneratorsessionen för att använda olika parametervärden. Stäng i så fall powershell-genereringssessionen och kör sedan *demo-loadgenerator.ps1*igen .

## <a name="provision-a-new-tenant-into-the-sharded-database"></a>Etablera en ny klient i den fragmenterade databasen

Den första distributionen innehåller tre exempelklienter i *databasen Tenants1.* Nu ska vi skapa en annan klient och observera dess effekter på det distribuerade programmet. I det här steget trycker du på en tangent för att skapa en ny klient:

1. Öppna... \\Utbildningsmoduler\\Etablering\\och katalog*Demo-ProvisionTenants.ps1* i *PowerShell ISE*.
2. Tryck på **F5** (inte **F8)** för att köra skriptet (lämna standardvärdena för tillfället).

   > [!NOTE]
   > Du måste bara köra PowerShell-skripten genom att trycka på **F5-tangenten,** inte genom att trycka på **F8** för att köra en markerad del av skriptet. Problemet med **F8** är att *$PSScriptRoot* variabeln inte utvärderas. Den här variabeln behövs av många skript för att navigera i mappar, anropa andra skript eller importera moduler.

Den nya Red Maple Racing-klienten läggs till i *Databasen Tenants1* och registreras i katalogen. Den nya hyresgästens biljettförsäljning **Evenemang** webbplats öppnas i din webbläsare:

![Ny klient](./media/saas-multitenantdb-get-started-deploy/red-maple-racing.png)

Uppdatera **händelsehubben**och den nya klienten visas nu i listan.

## <a name="provision-a-new-tenant-in-its-own-database"></a>Etablera en ny klient i sin egen databas

Med den fragmenterade modellen med flera innehavare kan du välja om du vill etablera en ny klient i en databas som innehåller andra klienter eller till en egen databas. En klient som är isolerad i sin egen databas har följande fördelar:

- Klientens databas kan hanteras utan att andra klienter behöver kompromissa med behoven.
- Om det behövs kan databasen återställas till en tidigare tidpunkt, eftersom inga andra klienter skulle påverkas.

Du kan välja att placera kostnadsfria provkunder, eller ekonomikunder, i databaser med flera innehavare. Du kan placera varje premiumklient i sin egen dedikerade databas. Om du skapar många databaser som bara innehåller en klient kan du hantera dem alla tillsammans i en elastisk pool för att optimera resurskostnaderna.

Därefter etablerar vi en annan klient, den här gången i sin egen databas:

1. I... \\\\Learning Modules Provision\\och Katalog*Demo-ProvisionTenants.ps1*, ändra *$TenantName* **salix Salsa**, *$VenueType* att **dansa** och *$Scenario* till **2**.

2. Tryck på **F5** för att köra skriptet igen.
    - **F5-pressen** avser den nya klienten i en separat databas. Databasen och klienten är registrerade i katalogen. Sedan öppnas webbläsaren till klientens händelsesida.

   ![Salix Salsa evenemang sida](./media/saas-multitenantdb-get-started-deploy/salix-salsa.png)

   - Bläddra längst ned på sidan. Där i banderollen ser du databasnamnet där klientdata lagras.

3. Uppdatera **händelsehubben** och de två nya klienterna visas nu i listan.

## <a name="explore-the-servers-and-tenant-databases"></a>Utforska servrar och klientdatabaser

Nu tittar vi på några av de resurser som har distribuerats:

1. I [Azure-portalen](https://portal.azure.com)bläddrar du till listan över resursgrupper. Öppna resursgruppen som du skapade när du distribuerade programmet.

   ![Resursgrupp](./media/saas-multitenantdb-get-started-deploy/resource-group.png)

2. Klicka på **&lt;användarserver&gt; för katalog mt.** Katalogservern innehåller två databaser med namnet *klientkatalog* och *basetenantdb*. *Basetenantdb-databasen* är en tom malldatabas. Den kopieras för att skapa en ny klientdatabas, oavsett om den används för många klienter eller bara en klient.

   ![katalogserver](./media/saas-multitenantdb-get-started-deploy/catalog-server.png)

3. Gå tillbaka till resursgruppen och välj den *klienten1-mt-server* som innehåller klientdatabaserna.
    - Klient1-databasen är en databas med flera innehavare där de ursprungliga tre klienterna, plus den första klienten du har lagt till, lagras. Den är konfigurerad som en 50 DTU Standard-databas.
    - **Salixsalsa-databasen** har Salix Salsa danslokal som enda hyresgäst. Den är konfigurerad som en Standard Edition-databas med 50 DSU:er som standard.

   ![klientserver](./media/saas-multitenantdb-get-started-deploy/tenants-server.png)

## <a name="monitor-the-performance-of-the-database"></a>Övervaka databasens prestanda

Om laddningsgeneratorn har körts i flera minuter finns tillräckligt med telemetri tillgängligt för att titta på databasövervakningsfunktionerna som är inbyggda i Azure-portalen.

1. Bläddra till **användarservern&gt; &lt;tenants1-mt** och klicka på **tenants1** för att visa resursutnyttjande för databasen som har fyra klienter i den. Varje hyresgäst är föremål för en sporadisk tung belastning från lastgeneratorn:

   ![övervaka klienter1](./media/saas-multitenantdb-get-started-deploy/monitor-tenants1.png)

   DTU-användningsdiagrammet illustrerar på ett snyggt sätt hur en databas med flera innehavare kan stödja en oförutsägbar arbetsbelastning för många klienter. I det här fallet tillämpar lastgeneratorn en sporadisk belastning på ungefär 30 DTUs till varje klient. Den här belastningen motsvarar 60 % utnyttjande av en 50 DTU-databas. Toppar som överstiger 60% är resultatet av att belastningen tillämpas på mer än en klient samtidigt.

2. Bläddra till **användarservern&gt; &lt;för klienter1 mt** och klicka på **salixsalsa-databasen.** Du kan se resursanvändningen i den här databasen som bara innehåller en klient.

   ![salixsalsa databas](./media/saas-multitenantdb-get-started-deploy/monitor-salix.png)

Lastgeneratorn tillämpar en liknande belastning för varje klient, oavsett vilken databas varje klient är i. Med bara en klient i **salixsalsa-databasen** kan du se att databasen kan upprätthålla en mycket högre belastning än databasen med flera klienter. 

### <a name="resource-allocations-vary-by-workload"></a>Resursallokeringar varierar beroende på arbetsbelastning

Ibland kräver en databas med flera innehavare mer resurser för bra prestanda än en databas med en enda klientorganisation, men inte alltid. Den optimala fördelningen av resurser beror på de särskilda arbetsbelastningsegenskaperna för klienterna i systemet.

Arbetsbelastningarna som genereras av belastningsgeneratorskriptet är endast avsedda som illustration.

## <a name="additional-resources"></a>Ytterligare resurser

- Mer information om SaaS-program med flera innehavare finns i [Designmönster för SaaS-program med flera innehavare](saas-tenancy-app-design-patterns.md).

- Mer information om elastiska pooler finns i:

  - [Elastiska pooler hjälper dig att hantera och skala flera Azure SQL-databaser](sql-database-elastic-pool.md)
  - [Skala ut med Azure SQL Database](sql-database-elastic-scale-introduction.md)

## <a name="next-steps"></a>Nästa steg

I den här guiden har du lärt dig:

> [!div class="checklist"]
> - Så här distribuerar du Wingtip Tickets SaaS Multi-tenant Database-programmet.
> - Om servrar och databaser som utgör appen.
> - Klienter mappas till sina data med *katalogen*.
> - Så här etablerar du nya klienter i en databas med flera innehavare och en klientdatabas.
> - Så här visar du poolanvändning för att övervaka klientaktivitet.
> - Så här tar du bort exempelresurser för att stoppa relaterad fakturering.

Försök nu [med självstudiekursen Etablera och katalogen](sql-database-saas-tutorial-provision-and-catalog.md).


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

[image-deploy-to-azure-blue-48d]: media/saas-multitenantdb-get-started-deploy/deploy.png "Knapp för distribution till Azure."

