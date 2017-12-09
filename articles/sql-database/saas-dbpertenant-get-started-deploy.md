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
ms.openlocfilehash: 5342b5290fab9826a2b38cd7ada63a6736c77601
ms.sourcegitcommit: 094061b19b0a707eace42ae47f39d7a666364d58
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2017
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

## <a name="prerequisites"></a>Krav

Följande krav måste uppfyllas för att kunna köra den här självstudiekursen:

* Azure PowerShell ska ha installerats. Mer information finns i [komma igång med Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="deploy-the-wingtip-tickets-saas-application"></a>Distribuera Wingtip biljetter SaaS-program

Distribuera appen:

1. Välj och Kom ihåg värden som du behöver för följande parametrar:

    - **Användaren**: Välj ett kort värde, till exempel din initialer följt av en siffra. Till exempel *af1*. Denna parameter kan innehålla endast bokstäver, siffror och bindestreck (inga blanksteg). Det första och sista tecknet måste vara en bokstav eller en siffra. Vi rekommenderar att alla bokstäver vara gemener.
    - **Resursgruppen**: varje gång du distribuerar Wingtip programmet, du måste välja ett annat unikt namn för den nya resursgruppen. Vi rekommenderar att du lägger till användarnamnet basnamn för resursgruppen. Ett exempel resursgruppens namn kan vara *wingtip af1*. Igen, rekommenderar vi att alla bokstäver vara gemener.

2. Öppna Wingtip biljetter SaaS databas per klient Distributionsmall i Azure-portalen genom att klicka på blå **till Azure** knappen.

   <a href="https://aka.ms/deploywingtipdpt" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

3. Ange värden för obligatoriska parametrar i mallen:

    > [!IMPORTANT]
    > En del autentisering och brandväggar för servern har avsiktligt lämnats oskyddade i demonstrationssyfte. Vi rekommenderar att du *skapa en ny resursgrupp*. Använd inte befintliga resursgrupper, servrar eller pooler. Använd inte det här programmet, skript och eventuella distribuerade resurser för produktion. Ta bort den här resursgruppen när du är klar med att programmet för att stoppa relaterad fakturering.

    - **Resursgruppen** – Välj **Skapa nytt** och ange den unika **namn** du valde tidigare för resursgruppen. 
    - **Plats** – Välj en **plats** från den nedrullningsbara listan.
    - **Användaren** -använder värdet för det användarnamn du valde tidigare.

4. Distribuera programmet.

    - Klicka om du accepterar villkoren.
    - Klicka på **Köp**.

5. Övervaka Distributionsstatus genom att klicka på **meddelanden**, vilket är klockikonen höger om sökrutan. Distribuera appen Wingtip biljetter SaaS tar cirka fem minuter.

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

Innan du kör alla skript måste du uppdatera den *resursgruppen* och *användare* värdena i **UserConfig.psm1**. Ange de värden som du använde under distributionen av dessa variabler.

1. I den *PowerShell ISE*öppnar... \\Learning moduler\\*UserConfig.psm1* 
2. Uppdatera *ResourceGroupName* och *namn* med specifika värden för din distribution (på rader 10 och 11 endast).
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

Wingtip programmet använder [ *Azure Traffic Manager* ](../traffic-manager/traffic-manager-overview.md) att styra distributionen av inkommande begäranden. URL till hubben händelser för en klient måste följa följande format:

- http://events.Wingtip-DPT.&lt;användare&gt;.trafficmanager.net/fabrikamjazzclub

Delar av det föregående formatet beskrivs i följande tabell.

| URL: en del | Beskrivning |
| :------- | :---------- |
| http://events.Wingtip-DPT | De händelser som delarna av Wingtip appen.<br /><br />den ***-dpt*** del särskiljer den *databas Per klient* implementering av Wingtip från andra lite olika implementeringar. Andra dokumentationsartiklarna ger till exempel Wingtip för *Standalong* (*-sa*), eller för *flera innehavare DB*. |
| .  *&lt;ANVÄNDARE&gt;* | *af1* i vårt exempel. |
| .trafficmanager.NET/ | Azure Traffic Manager bas-URL. |
| fabrikamjazzclub | För klienten med namnet *Fabrikam Jazz en*. |
| &nbsp; | &nbsp; |

1. Innehavarens namn analyseras av appen händelser från URL-Adressen.
2. Innehavarens namn används för att skapa en nyckel.
3. Nyckeln används för att få åtkomst till katalogen för att hämta platsen för klientens databas.
    - Katalogen implementeras med hjälp av *Fragmentera kartan management*.
4. Den *händelser hubb* använder utökade metadata i katalogen för att erhålla en lista över URL: er för händelsen.

I en produktionsmiljö, vanligtvis skapar du en CNAME DNS-posten [ *peka företagets Internetdomän* ](../traffic-manager/traffic-manager-point-internet-domain.md) till trafikhanterarprofilen.

## <a name="start-generating-load-on-the-tenant-databases"></a>Börja generera belastningar på klient-databaserna

Nu när appen har distribuerats, vi få det att fungera!
Den *Demo-LoadGenerator* PowerShell-skript startar en arbetsbelastning som körs mot alla klient-databaser.
Verkliga belastningen på många SaaS-appar är sporadiska och oförutsägbart.
Om du vill simulera den här typen av belastningen producerar generatorn en belastning med slumpmässig toppar eller belastning av aktivitet på varje klient.
Belastning som uppstår vid slumpmässiga intervall.
Det tar flera minuter innan load mönstret ut. Det är därför bäst att låta generator kör minst tre eller fyra minuter innan övervakning belastningen.

1. I den *PowerShell ISE*öppnar den... \\Learning moduler\\verktyg\\*Demo-LoadGenerator.ps1* skript.
2. Tryck på **F5** att köra skriptet och starta generatorn belastningen. (Låt standardvärdet parametervärden för tillfället.)

Återanvänd inte samma PowerShell ISE-instans för, kanske en kör av *Demo-LoadGenerator.ps1*. Om du behöver köra andra PowerShell-skript, startar du en separat PowerShell ISE.

#### <a name="rerun-with-different-parameters"></a>Kör igen med andra parametrar

Följ dessa steg om du vill köra testet arbetsbelastning med olika parametrar:

1. Stoppa *LoadGenerator.ps1*.
    - Antingen genom att använda **Ctrl + C**, eller klicka på den **stoppa** knappen.
    - Den här avbrott Stoppa inte och påverkar alla ofullständiga bakgrundsjobb körs fortfarande.

2. Kör *Demo-LoadGenerator.ps1*.
    - Den här kör först stoppar alla bakgrundsjobb som kan fortfarande köra *sp_CpuLoadGenerator*.

Eller du kan säga upp PowerShell ISE-instansen, som stoppas alla bakgrundsjobb som. Starta en ny instans av PowerShell ISE sedan och kör *Demo-LoadGenerator.ps1*.

#### <a name="monitor-the-background-jobs"></a>Övervaka bakgrundsjobb

Om du vill kontrollera och övervaka bakgrundsjobb, använder du följande cmdlets:

- Get-Job
- Ta emot jobb
- Stoppa jobb

#### <a name="demo-loadgeneratorps1-actions"></a>Demo-LoadGenerator.ps1 åtgärder

*Demo-LoadGenerator.ps1* efterliknar en aktiva arbetsbelastningen för kund-transaktioner. Följande steg beskriver de åtgärder som *Demo-LoadGenerator.ps1* initierar:

1. *Demo-LoadGenerator.ps1* startar *LoadGenerator.ps1* i förgrunden.
    - Båda dessa .ps1 filer lagras under mapparna *Learning moduler\\verktyg\\*.

2. *LoadGenerator.ps1* slingor via alla klient-databaser som har registrerats i katalogen.

3. För varje innehavare i databasen *LoadGenerator.ps1* startar en körning av Transact-SQL lagrad procedur med namnet *sp_CpuLoadGenerator*.
    - Körningar startas i bakgrunden, genom att anropa den *Invoke-SqlAzureWithRetry* cmdlet.
    - *sp_CpuLoadGenerator* slingor runt en SQL SELECT-instruktion för en standardvaraktigheten 60 sekunder. Tidsintervallet mellan problem för Välj varierar beroende på parametervärden.
    - Den här SQL-filen lagras under *WingtipTenantDB\\dbo\\StoredProcedures\\*.

4. För varje innehavare i databasen *LoadGenerator.ps1* startas även den *Start-Job* cmdlet.
    - *Start-Job* efterliknar en arbetsbelastning biljett försäljning.

5. *LoadGenerator.ps1* fortsätter och övervakning för alla nya klienter som tillhandahålls.

&nbsp;

Innan du fortsätter till nästa avsnitt, lämna belastningen generatorn körs i läget jobbet startar.

## <a name="provision-a-new-tenant"></a>Etablera en ny klient

Den första distributionen skapas tre exempel innehavare. Nu kan du skapa en annan klient om du vill visa effekten på det distribuerade programmet. I appen Wingtip arbetsflödet för att etablera nya klienter förklaras i den [etablera och katalogen kursen](saas-dbpertenant-provision-and-catalog.md). I det här steget skapar du en ny klient som tar mindre än en minut.

1. I den *PowerShell ISE*öppnar... \\Modules\Provision och katalogen\\*Demo-ProvisionAndCatalog.ps1* .
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
    - Katalogservern innehåller två databaserna, den **tenantcatalog** och **basetenantdb** (en mall för databas som kopieras om du vill skapa nya klienter).

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
- Det andra diagrammet visar eDTU-användning av de översta fem databaserna i poolen.

De två diagrammen visar att elastiska pooler och SQL-databas är utmärkt för arbetsbelastningar för SaaS-program.
Diagrammen visar att 4 databaser är varje burst-överföring till så mycket som 40 edtu: er och ännu alla databaser bekvämt stöds av en 50 eDTU-pool. 50 eDTU-pool stöder även tyngre arbetsbelastningar.
Om de har etablerats som fristående databaser, de skulle varje måste vara en S2 (50 DTU) för att stödja belastning.
Kostnaden för 4 fristående S2 databaser är nästan 3 gånger priset för poolen.
I verkliga situationer använder SQL Database kunder för närvarande upp till 500 databaser i 200 eDTU pooler.
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

