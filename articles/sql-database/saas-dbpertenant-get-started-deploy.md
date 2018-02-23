---
title: "Databasen per klient SaaS tutorial – Azure SQL Database | Microsoft Docs"
description: "Distribuera och utforska Wingtip biljetter SaaS flera innehavare-program som visar databas per klient och andra SaaS-mönster med Azure SQL Database."
keywords: sql database tutorial
services: sql-database
documentationcenter: 
author: MightyPen
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: genemi
ms.openlocfilehash: cbe8a04abbf2dada7cc43e57e823c3a41bf83fe7
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/22/2018
---
# <a name="deploy-and-explore-a-multi-tenant-saas-application-that-uses-the-database-per-tenant-pattern-with-azure-sql-database"></a>Distribuera och utforska ett SaaS-program med flera innehavare som använder databasen per klient mönster med Azure SQL Database

I kursen får du distribuera och utforska Wingtip biljetter SaaS *databas per klient* program (Wingtip). Appen använder en databas per klient mönster för att lagra data för flera innehavare. Appen är utformat för att presentera Azure SQL Database-funktioner som förenklar aktiverar SaaS-scenarier.

Fem minuter efter klickar du på knappen blå märkta **till Azure**, du har ett SaaS-program med flera innehavare. Appen innehåller en Azure SQL-databas som körs i Microsoft Azure-molnet. Appen har distribuerats med tre exempel klienter, var och en med sin egen databas. Alla databaser som har distribuerats till en SQL *elastisk pool*. Appen har distribuerats till din Azure-prenumeration. Du har fullständig åtkomst till att utforska och arbeta med enskilda komponenter i appen. Programmets källkod för C# och management-skript som är tillgängliga i den [WingtipTicketsSaaS DbPerTenant GitHub-repo][github-wingtip-dpt].

I den här guiden lär du dig:

> [!div class="checklist"]
> - Så här distribuerar du Wingtip SaaS-program.
> - Var du kan hämta programmets källkod och av hanteringsskript.
> - Om servrar, pooler och databaser som ingår i appen.
> - Hur klienter mappas till sina data med den *katalogen*.
> - Hur du etablerar en ny klient.
> - Så här övervakar du klient aktivitet i appen.

En [antal relaterade självstudier](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials) erbjuder att utforska olika SaaS design- och mönster. Självstudierna skapa utöver inledande distributionen.
Du kan se hur de olika SaaS-mönster implementeras genom att undersöka de tillhandahållna skript när du använder självstudierna.
Skript visar hur funktioner i SQL Database förenkla utvecklingen av SaaS-program.

## <a name="prerequisites"></a>Förutsättningar

Följande krav måste uppfyllas för att kunna köra den här självstudiekursen:

* Azure PowerShell ska ha installerats. Mer information finns i [komma igång med Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="deploy-the-wingtip-tickets-saas-application"></a>Distribuera Wingtip biljetter SaaS-program

#### <a name="plan-the-names"></a>Planera namn

I stegen i det här avsnittet ger du en *användare* värde som används för att säkerställa globalt unikt namn och ett namn för den *resursgruppen* som innehåller alla resurser som skapats av en distribution i appen. För en person med namnet *Ann Finley*, föreslår vi:
- *Användare:* **af1***(hennes initialer plus en siffra. Använd ett annat värde (t.ex. af2) om du distribuerar appen till en andra gång.)*
- *Resursgrupp:* **wingtip-dpt-af1** *(wingtip dpt innebär att det är appen databas per klient. Lägga till användaren namnet af1 korrelerar resursgruppens namn med namnen på de resurser som den innehåller.)*

Välj namn på din nu och Skriv ned dem. 

#### <a name="steps"></a>Steg

1. Öppna Wingtip biljetter SaaS databas per klient Distributionsmall i Azure-portalen genom att klicka på blå **till Azure** knappen.

   <a href="https://aka.ms/deploywingtipdpt" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

1. Ange värden för obligatoriska parametrar i mallen:

    > [!IMPORTANT]
    > En del autentisering och brandväggar för servern har avsiktligt lämnats oskyddade i demonstrationssyfte. Vi rekommenderar att du *skapa en ny resursgrupp*. Använd inte befintliga resursgrupper, servrar eller pooler. Använd inte det här programmet, skript och eventuella distribuerade resurser för produktion. Ta bort den här resursgruppen när du är klar med att programmet för att stoppa relaterad fakturering.

    - **Resursgruppen** – Välj **Skapa nytt** och ange den unika **namn** du valde tidigare för resursgruppen. 
    - **Plats** – Välj en **plats** från den nedrullningsbara listan.
    - **Användaren** -använder värdet för det användarnamn du valde tidigare.

1. Distribuera programmet.

    - Klicka om du accepterar villkoren.
    - Klicka på **Köp**.

1. Övervaka Distributionsstatus genom att klicka på **meddelanden**, vilket är klockikonen höger om sökrutan. Distribuera appen Wingtip biljetter SaaS tar cirka fem minuter.

   ![distributionen lyckades](media/saas-dbpertenant-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-wingtip-tickets-management-scripts"></a>Hämta och avblockera hanteringsskript Wingtip biljetter

Ladda ned källan kod och hantering av skript vid distribution av programmet.

> [!IMPORTANT]
> Körbara innehållet (skript, DLL-filer) kan blockeras av Windows när ZIP-filer laddas ned från en extern källa och extraheras. När skript från en zip-fil, Använd följande steg för att avblockera .zip-filen innan du extraherar. Den avblockera garanterar skript tillåts köra.

1. Bläddra till den [WingtipTicketsSaaS DbPerTenant GitHub-repo][github-wingtip-dpt].
2. Klicka på **kloning eller hämta**.
3. Klicka på **hämta ZIP**, och sedan spara filen.
4. Högerklicka på den **WingtipTicketsSaaS-DbPerTenant-master.zip** filen och välj sedan **egenskaper**.
5. På den **allmänna** väljer **avblockera**, och klicka sedan på **tillämpa**.
6. Klicka på **OK**.
7. Extrahera filerna.

Skript finns i den *... \\WingtipTicketsSaaS DbPerTenant master\\Learning moduler* mapp.

## <a name="update-the-user-configuration-file-for-this-deployment"></a>Uppdatera konfigurationsfilen för användaren för den här distributionen

Innan du kör alla skript måste du uppdatera den *resursgruppen* och *användare* värdena i den *Användarkonfiguration* filen. Ange de värden som du använde under distributionen av dessa variabler.

1. I den **PowerShell ISE**öppnar... \\Learning moduler\\**UserConfig.psm1** 
2. Uppdatera **ResourceGroupName** och **namn** med specifika värden för din distribution (på rader 10 och 11 endast).
3. Spara ändringarna!

Dessa värden refereras i nästan alla skript.

## <a name="run-the-application"></a>Köra programmet

Appen demonstreras handelsplatser som värd för händelser. Platsen typer är konsertsalar, jazz föreningar och sport föreningar. I Wingtip biljetter registrerad handelsplatser som klienter. En klient har en plats ett enkelt sätt att visa händelser och sälja biljetter till sina kunder. Varje plats hämtar en anpassad webbplats till sina händelser och sälja biljetter.

I appen hämtar internt varje klient en SQL-databas som har distribuerats till en elastisk SQL-pool.

Central **händelser hubb** sidan innehåller en lista med länkar till innehavare i distributionen.

1. Öppna den *händelser hubb* i webbläsaren: http://events.wingtip-dpt.&lt;användare&gt;. trafficmanager.net (ersätta &lt;användaren&gt; med din distribution användaren värde):

    ![evenemangshubben](media/saas-dbpertenant-get-started-deploy/events-hub.png)

2. Klicka på **Fabrikam Jazzklubb** i *evenemangshubben*.

    ![Händelser](./media/saas-dbpertenant-get-started-deploy/fabrikam.png)

#### <a name="azure-traffic-manager"></a>Azure Traffic Manager

Wingtip programmet använder [ *Azure Traffic Manager* ](../traffic-manager/traffic-manager-overview.md) att styra distributionen av inkommande begäranden. URL till sidan händelser för en viss klient används följande format:

- http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net/fabrikamjazzclub

Delar av det föregående formatet beskrivs i följande tabell.

| URL: en del        | Beskrivning       |
| :-------------- | :---------------- |
| http://events.wingtip-dpt | De händelser som delarna av Wingtip appen.<br /><br /> *-dpt* särskiljer den *databas per klient* implementeringen av Wingtip biljetter från andra implementeringar. Till exempel den *fristående* app per klient (*-sa*), eller *flera innehavare databasen* (*- huvudmålservern*) implementeringar. |
| .*&lt;user&gt;* | *af1* i vårt exempel. |
| .trafficmanager.net/ | Azure Traffic Manager bas-URL. |
| fabrikamjazzclub | Identifierar klient med namnet *Fabrikam Jazz en*. |
| &nbsp; | &nbsp; |

1. Innehavarens namn analyseras av appen händelser från URL: en.
2. Innehavarens namn används för att skapa en nyckel.
3. Nyckeln används för att få åtkomst till katalogen för att hämta platsen för klientens databas.
    - Katalogen implementeras med hjälp av *Fragmentera kartan management*.
4. Den *händelser hubb* använder utökade metadata i katalogen för att konstruera händelser sidan URL-listan för varje klient.

I en produktionsmiljö, vanligtvis skapar du en CNAME DNS-posten [ *peka företagets Internetdomän* ](../traffic-manager/traffic-manager-point-internet-domain.md) traffic manager DNS-namn.

## <a name="start-generating-load-on-the-tenant-databases"></a>Börja generera belastningar på klient-databaserna

Nu när appen har distribuerats, vi få det att fungera!
Den *Demo-LoadGenerator* PowerShell-skript startar en arbetsbelastning som körs mot alla klient-databaser.
Verkliga belastningen på många SaaS-appar är sporadiska och oförutsägbart.
Om du vill simulera den här typen av belastningen producerar generatorn en belastning med slumpmässig toppar eller belastning av aktivitet på varje klient.
Belastning som uppstår vid slumpmässiga intervall.
Det tar flera minuter innan load mönstret ut. Det är därför bäst att låta generator kör minst tre eller fyra minuter innan övervakning belastningen.

1. I den *PowerShell ISE*öppnar den... \\Learning moduler\\verktyg\\*Demo-LoadGenerator.ps1* skript.
1. Tryck på **F5** att köra skriptet och starta generatorn belastningen. (Låt standardvärdet parametervärden för tillfället.)
1. Du uppmanas att logga in på ditt Azure-konto och, om det behövs för att välja prenumerationen du vill använda.

Läs in generator skriptet startar bakgrunden för varje databas i katalogen och sedan stoppar.  Om du kör skriptet belastningen generator stoppas först alla bakgrundsjobb som körs innan du startar nya.

#### <a name="monitor-the-background-jobs"></a>Övervaka bakgrundsjobb

Om du vill kontrollera och övervaka bakgrundsjobb, använder du följande cmdlets:

- `Get-Job`
- `Receive-Job`
- `Stop-Job`

#### <a name="demo-loadgeneratorps1-actions"></a>Demo-LoadGenerator.ps1 åtgärder

*Demo-LoadGenerator.ps1* efterliknar en aktiva arbetsbelastningen för kund-transaktioner. Följande steg beskriver de åtgärder som *Demo-LoadGenerator.ps1* initierar:

1. *Demo-LoadGenerator.ps1* startar *LoadGenerator.ps1* i förgrunden.
    - Båda dessa .ps1 filer lagras under mapparna *Learning moduler\\verktyg\\*.

1. *LoadGenerator.ps1* slingor via alla klient-databaser i katalogen.

1. *LoadGenerator.ps1* startar PowerShell bakgrunden för varje klient-databas: 
    - Som standard körs bakgrundsjobb i 120 minuter.
    - Varje jobb gör en CPU-baserade belastning på databasen för en klient genom att köra *sp_CpuLoadGenerator*.  Intensiteten och varaktighet för belastningen varierar beroende på `$DemoScenario`. 
    - *sp_CpuLoadGenerator* slingor runt en SQL SELECT-uttryck som orsakar hög processorbelastning. Tidsintervallet mellan problem för Välj varierar beroende på parametervärden för att skapa en kan kontrolleras CPU-belastning. Belastningsnivåer och intervall väljs slumpvis för att simulera mer realistisk belastning.
    - Den här SQL-filen lagras under *WingtipTenantDB\\dbo\\StoredProcedures\\*.

1. Om `$OneTime = $false`, load generator startar bakgrundsjobb och sedan fortsätter och övervaka var 10: e sekund för alla nya klienter som tillhandahålls. Om du ställer in `$OneTime = $true`, LoadGenerator startar bakgrundsjobb och sedan stoppa körs i förgrunden. Den här självstudiekursen lämna `$OneTime = $false`.

  Du kan använda Ctrl-C eller stoppa åtgärden Ctrl-Break om du vill stoppa eller starta om belastningen generator. 

  Om du lämnar belastningen generatorn körs i förgrunden, kan du använda en annan PowerShell ISE-instans för att köra andra PowerShell-skript.

&nbsp;

Innan du fortsätter till nästa avsnitt, lämna belastningen generatorn körs i läget jobbet startar.

## <a name="provision-a-new-tenant"></a>Etablera en ny klient

Den första distributionen skapas tre exempel innehavare. Nu kan du skapa en annan klient om du vill visa effekten på det distribuerade programmet. I appen Wingtip arbetsflödet för att etablera nya klienter förklaras i den [etablera och katalogen kursen](saas-dbpertenant-provision-and-catalog.md). I det här steget skapar du en ny klient som tar mindre än en minut.

1. Öppna ett nytt *PowerShell ISE*.
1. Öppna... \\Modules\Provision och katalogen\\*Demo-ProvisionAndCatalog.ps1* .
2. Tryck **F5** för att köra skriptet. (Lämna standardvärdena för tillfället.)

   > [!NOTE]
   > Många SaaS Wingtip skript använder *$PSScriptRoot* att navigera mappar för att anropa funktioner i andra skript. Den här variabeln utvärderas bara när fullständig skriptet körs genom att trycka på **F5**.  Markera och kör en markering med **F8** kan leda till fel. Så kör skript genom att trycka på **F5**.

Den nya innehavare databasen är:

- Skapa i en elastisk SQL-pool.
- Initiera.
- Registrerats i katalogen.

Efter lyckad etableringen av *händelser* platsen för den nya innehavaren visas i webbläsaren:

![Ny klient](./media/saas-dbpertenant-get-started-deploy/red-maple-racing.png)

Uppdatera den *händelser hubb* så att den nya innehavaren som visas i listan.

## <a name="explore-the-servers-pools-and-tenant-databases"></a>Utforska servrar, pooler, och klientdatabaser

Nu när du har startat körs en belastning för samlingen av innehavare kan vi titta på några av de resurser som har distribuerats:

1. I den [Azure-portalen](http://portal.azure.com), bläddra i listan över SQL-servrar och sedan öppna den **katalog-dpt -&lt;användare&gt;**  server.
    - Katalogservern innehåller två databaserna, **tenantcatalog** och **basetenantdb** (en mall för databas som kopieras om du vill skapa nya klienter).

   ![databaser](./media/saas-dbpertenant-get-started-deploy/databases.png)

2. Gå tillbaka till listan med SQL-servrar.

3. Öppna den **tenants1-dpt -&lt;användare&gt;**  server som innehåller klient-databaser.

4. Se följande:
    - Varje klient-databasen är en *elastisk Standard* databas i en standard 50 eDTU-pool.
    - Den *Röd lönn tävling* databas, vilket är klient-databasen som du tidigare har etablerats.

   ![server](./media/saas-dbpertenant-get-started-deploy/server.png)

## <a name="monitor-the-pool"></a>Övervaka poolen

Efter *LoadGenerator.ps1* körs under flera minuter tillräckligt med data ska vara tillgängliga för att starta tittar på vissa övervakningsfunktionerna. Dessa funktioner är inbyggda i pooler och databaser.

Bläddra till servern **tenants1-dpt -&lt;användare&gt;**, och klicka på **Pool1** att visa resursanvändningen för poolen. I följande diagram kördes belastningen generator under en timme.

   ![övervaka poolen](./media/saas-dbpertenant-get-started-deploy/monitor-pool.png)

- Det första diagrammet märkta **resursutnyttjande**, visar pool-eDTU-användning.
- Det andra diagrammet visar eDTU-användning av fem mest aktiva databaser i poolen.

De två diagrammen visar att elastiska pooler och SQL-databas är utmärkt för arbetsbelastningar för oväntade SaaS-program.
Diagrammen visar att 4 databaser är varje burst-överföring till så mycket som 40 edtu: er och ännu alla databaser bekvämt stöds av en 50 eDTU-pool. 50 eDTU-pool stöder även tyngre arbetsbelastningar.
Om de har etablerats som fristående databaser varje databas måste vara en S2 (50 DTU) för att stödja belastning.
Kostnaden för 4 fristående S2 databaser är nästan 3 gånger priset för poolen.
SQL-databas kunder kör upp till 500 databaser i 200 eDTU-pooler i verkliga situationer.
Mer information finns i [prestandaövervakningsguiden](saas-dbpertenant-performance-monitoring.md).

## <a name="additional-resources"></a>Ytterligare resurser

- Ytterligare [självstudier som bygger på Wingtip biljetter SaaS databasen per klient program](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
- Läs mer om elastiska pooler, i [*vad är en Azure SQL elastisk pool*](sql-database-elastic-pool.md)
- Läs om elastiska jobb i [*hantera utskalade molndatabaser*](sql-database-elastic-jobs-overview.md)
- Läs mer om SaaS-program med flera klienter i [*Designmönster för SaaS-program med flera klienter*](saas-tenancy-app-design-patterns.md)


## <a name="next-steps"></a>Nästa steg

I den här guiden har du lärt dig:

> [!div class="checklist"]
> - Så här distribuerar du Wingtip biljetter SaaS-program
> - Om de servrar, pooler och databaser som ingår i appen
> - Klienter mappas till sina data med *katalogen*
> - Hur du etablerar nya klienter
> - Hur du ser poolanvändning för att övervaka klientaktivitet
> - Hur du tar bort exempelresurser för att stoppa relaterad fakturering

Försök med den [etablera och katalogen kursen](saas-dbpertenant-provision-and-catalog.md).



<!-- Link references. -->

[github-wingtip-dpt]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant 

