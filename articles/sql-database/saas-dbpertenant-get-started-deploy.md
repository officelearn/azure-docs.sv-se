---
title: Databasen per klient SaaS tutorial – Azure SQL Database | Microsoft Docs
description: Distribuera och utforska den multitenant Wingtip biljetter SaaS-program som visar databasen per klient mönstret och andra SaaS-mönster med hjälp av Azure SQL Database.
keywords: sql database tutorial
services: sql-database
author: MightyPen
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 04/01/2018
ms.author: genemi
ms.openlocfilehash: 84e6f2303bcfd6a2c91f29e9e9afdc2b84f990ca
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="deploy-and-explore-a-multitenant-saas-app-that-uses-the-database-per-tenant-pattern-with-sql-database"></a>Distribuera och utforska en multitenant SaaS-appar som använder databasen per klient-mönstret med SQL-databas

I den här självstudiekursen, distribuera och utforska Wingtip biljetter SaaS databas per klient programmet (Wingtip). Appen använder ett mönster för databasen per klient för att lagra data för flera innehavare. Appen är utformat för att presentera Azure SQL Database-funktioner som förenklar så här aktiverar du SaaS-scenarier.

Fem minuter när du har valt **till Azure**, du har en multitenant SaaS-program. Appen innehåller en SQL-databas som körs i molnet. Appen har distribuerats med tre exempel klienter, var och en med sin egen databas. Alla databaser har distribuerats till en elastisk SQL-pool. Appen har distribuerats till din Azure-prenumeration. Du har fullständig åtkomst till att utforska och arbeta med enskilda komponenter i appen. Programmets källkod för C# och av hanteringsskript är tillgängliga i den [WingtipTicketsSaaS DbPerTenant GitHub-repo][github-wingtip-dpt].

I den här guiden lär du dig:

> [!div class="checklist"]
> - Så här distribuerar du Wingtip SaaS-program.
> - Var du kan hämta programmet källan koden och hantering av skript.
> - Om servrar, pooler och databaser som ingår i appen.
> - Hur klienter mappas till sina data med den *katalogen*.
> - Hur du etablerar en ny klient.
> - Så här övervakar du klient aktivitet i appen.

En [antal relaterade självstudier](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials) erbjuder att utforska olika SaaS design- och mönster. Självstudierna skapa utöver inledande distributionen. När du använder självstudierna kan undersöka du angivna skript om du vill se hur de olika SaaS-mönster implementeras. Skript visar hur funktioner i SQL Database förenkla utvecklingen av SaaS-program.

## <a name="prerequisites"></a>Förutsättningar

Kontrollera att Azure PowerShell är installerat för att slutföra den här självstudiekursen. Mer information finns i [Kom igång med Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="deploy-the-wingtip-tickets-saas-application"></a>Distribuera Wingtip biljetter SaaS-program

#### <a name="plan-the-names"></a>Planera namn

Stegen i det här avsnittet anger du ett värde som användaren som används för att se till att resursnamn är globalt unika. Du kan också ange ett namn för resursgruppen som innehåller de resurser som skapats av en distribution av appen. Vi rekommenderar följande för en fiktiv person som heter Ann Finley:

- **Användaren**: *af1* består av Ann Finley initialer plus en siffra. Om du distribuerar appen till en andra gång, kan du använda ett annat värde. Ett exempel är af2.
- **Resursgruppen**: *wingtip-dpt-af1* innebär att det är appen databas per klient. Lägg till användaren namnet af1 för att korrelera resursgruppens namn med namnen på de resurser som den innehåller.

Välj namn på din nu och Skriv ned dem. 

#### <a name="steps"></a>Steg

1. För att öppna databasen per klient-Distributionsmall Wingtip biljetter SaaS i Azure portal, Välj **till Azure**.

   <a href="https://aka.ms/deploywingtipdpt" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

2. Ange värden i mallen för de obligatoriska parametrarna.

    > [!IMPORTANT]
    > Vissa brandväggar för autentisering och servern är avsiktligt oskyddat i exempelsyfte. Vi rekommenderar att du skapar en ny resursgrupp. Använd inte befintliga resursgrupper, servrar eller pooler. Använd inte det här programmet, skript och eventuella distribuerade resurser för produktion. Ta bort den här resursgruppen när du är klar med att programmet slutar relaterade fakturering.

    - **Resursgruppen**: Välj **Skapa nytt**, och ange det unika namnet som du valde tidigare för resursgruppen. 
    - **Plats**: Välj en plats från den nedrullningsbara listan.
    - **Användaren**: använda värdet för det användarnamn du valde tidigare.

3. Distribuera programmet.

    a. Välj om du accepterar villkoren.

    b. Välj **inköp**.

4. Om du vill övervaka Distributionsstatus, Välj **meddelanden** (klockikonen till höger om sökrutan). Distribuera appen Wingtip biljetter SaaS tar cirka fem minuter.

   ![Distribueringen lyckades](media/saas-dbpertenant-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-wingtip-tickets-management-scripts"></a>Hämta och avblockera hanteringsskript Wingtip biljetter

När programmet distribueras, hämta koden och hantering av skript för källa.

> [!IMPORTANT]
> Körbara innehållet (skript och DLL-filer) kan blockeras av Windows när ZIP-filer laddas ned från en extern källa och extraheras. Följ stegen för att låsa upp ZIP-filen innan du extraherar skripten. Avblockera säkerställer skript tillåts köra.

1. Bläddra till den [WingtipTicketsSaaS DbPerTenant GitHub-repo][github-wingtip-dpt].
2. Välj **kloning eller hämta**.
3. Välj **hämta ZIP**, och sedan spara filen.
4. Högerklicka på den **WingtipTicketsSaaS-DbPerTenant-master.zip** filen och välj sedan **egenskaper**.
5. På den **allmänna** väljer **avblockera** > **tillämpa**.
6. Välj **OK**, och extrahera filerna

Skript finns i den... \\WingtipTicketsSaaS DbPerTenant master\\Learning moduler mapp.

## <a name="update-the-user-configuration-file-for-this-deployment"></a>Uppdatera konfigurationsfilen för användaren för den här distributionen

Uppdatera resurs grupp- och värden i konfigurationsfilen för användaren innan du kör alla skript. Ange de värden som du använde under distributionen av dessa variabler.

1. I PowerShell ISE öppna... \\Learning moduler\\**UserConfig.psm1** 
2. Uppdatera **ResourceGroupName** och **namn** med specifika värden för din distribution (på rader 10 och 11 endast).
3. Spara ändringarna.

Dessa värden refereras i nästan alla skript.

## <a name="run-the-application"></a>Köra programmet

Appen demonstreras handelsplatser som värd för händelser. Platsen typer är konsertsalar, jazz föreningar och sport föreningar. I Wingtip biljetter registrerad handelsplatser som klienter. En klient har en plats ett enkelt sätt att visa händelser och sälja biljetter till sina kunder. Varje plats hämtar en anpassad webbplats till sina händelser och sälja biljetter.

I appen hämtar internt varje klient en SQL-databas som har distribuerats till en elastisk SQL-pool.

Central **händelser hubb** sidan innehåller en lista med länkar till innehavare i distributionen.

1. Använda Webbadressen för att öppna hubben händelser i webbläsaren: http://events.wingtip-dpt.&lt; användaren&gt;. trafficmanager.net. Ersätt &lt;användaren&gt; med din distribution användaren värde.

    ![Händelser Hub](media/saas-dbpertenant-get-started-deploy/events-hub.png)

2. Välj **Fabrikam Jazz en** i hubben händelser.

    ![Händelser](./media/saas-dbpertenant-get-started-deploy/fabrikam.png)

#### <a name="azure-traffic-manager"></a>Azure Traffic Manager

Wingtip programmet använder [ *Azure Traffic Manager* ](../traffic-manager/traffic-manager-overview.md) att styra distributionen av inkommande begäranden. URL till sidan händelser för en viss klient används följande format:

- http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net/fabrikamjazzclub

    Delar av det föregående formatet beskrivs i följande tabell.

    | URL: en del        | Beskrivning       |
    | :-------------- | :---------------- |
    | http://events.wingtip-dpt | De händelser som delarna av Wingtip appen.<br /><br /> *-dpt* särskiljer den *databas per klient* implementeringen av Wingtip biljetter från andra implementeringar. Exempel är den *fristående* app per klient (*-sa*) eller *multitenant databasen* (*- huvudmålservern*) implementeringar. |
    | .*&lt;user&gt;* | *af1* i exemplet. |
    | .trafficmanager.net/ | Traffic Manager bas-URL. |
    | fabrikamjazzclub | Identifierar klient med namnet Fabrikam Jazz en. |
    | &nbsp; | &nbsp; |

* Innehavarens namn analyseras av appen händelser från URL: en.
* Innehavarens namn används för att skapa en nyckel.
* Nyckeln används för att få åtkomst till katalogen för att hämta platsen för klientens databas.
    - Katalogen implementeras med hjälp av *Fragmentera kartan management*.
* Händelser hubben använder utökade metadata i katalogen för att konstruera lista för händelser webbadresser för varje klient.

I en produktionsmiljö, vanligtvis skapar du en CNAME DNS-posten [ *peka företagets Internetdomän* ](../traffic-manager/traffic-manager-point-internet-domain.md) som Traffic Manager DNS-namnet.

## <a name="start-generating-load-on-the-tenant-databases"></a>Börja generera belastningar på klient-databaserna

Nu när appen har distribuerats, vi få det att fungera.

Den *Demo-LoadGenerator* PowerShell-skript startar en arbetsbelastning som körs mot alla klient-databaser. Verkliga belastningen på många SaaS-appar är sporadiska och oförutsägbart. Om du vill simulera den här typen av belastningen producerar generatorn en belastning med slumpmässig toppar eller belastning av aktivitet på varje klient. Belastning som uppstår vid slumpmässiga intervall. Det tar flera minuter innan load mönstret ut. Låt generator kör minst tre eller fyra minuter innan du övervaka belastningen.

1. I PowerShell ISE öppnar den... \\Learning moduler\\verktyg\\*Demo-LoadGenerator.ps1* skript.
2. Tryck på F5 för att köra skriptet och starta belastningen generator. Låt standardvärdet parametervärden för tillfället.
3. Logga in på ditt Azure-konto och välj den prenumeration som du vill använda, om det behövs.

Läs in generator skriptet startar bakgrunden för varje databas i katalogen och sedan stoppar. Om du kör skriptet belastningen generator stoppar alla bakgrundsjobb som körs innan den startar nya.

#### <a name="monitor-the-background-jobs"></a>Övervaka bakgrundsjobb

Om du vill kontrollera och övervaka bakgrundsjobb använder du följande cmdlets:

- `Get-Job`
- `Receive-Job`
- `Stop-Job`

#### <a name="demo-loadgeneratorps1-actions"></a>Demo-LoadGenerator.ps1 åtgärder

*Demo-LoadGenerator.ps1* efterliknar en aktiva arbetsbelastningen för kund-transaktioner. Följande steg beskriver de åtgärder som *Demo-LoadGenerator.ps1* initierar:

1. *Demo-LoadGenerator.ps1* startar *LoadGenerator.ps1* i förgrunden.

    - Båda .ps1 filer under mappar Learning moduler\\verktyg\\.

2. *LoadGenerator.ps1* slingor via alla klient-databaser i katalogen.

3. *LoadGenerator.ps1* startar PowerShell bakgrunden för varje klient-databas:

    - Som standard körs bakgrundsjobb i 120 minuter.
    - Varje jobb gör en CPU-baserade belastning på databasen för en klient genom att köra *sp_CpuLoadGenerator*. Intensiteten och varaktighet för belastningen varierar beroende på `$DemoScenario`. 
    - *sp_CpuLoadGenerator* slingor runt en SQL SELECT-uttryck som orsakar hög processorbelastning. Tidsintervallet mellan problem för Välj varierar beroende på parametervärden för att skapa en kan kontrolleras CPU-belastning. Belastningsnivåer och intervall väljs slumpvis för att simulera mer realistisk belastning.
    - Den här SQL-filen lagras under *WingtipTenantDB\\dbo\\StoredProcedures\\*.

4. Om `$OneTime = $false`, load generator startar bakgrundsjobb och fortsätter sedan. Var 10: e sekund som övervakas för alla nya klienter som tillhandahålls. Om du ställer in `$OneTime = $true`, LoadGenerator startar bakgrundsjobb och sedan slutar att köras i förgrunden. Den här självstudiekursen lämna `$OneTime = $false`.

  Du kan använda Ctrl-C eller stoppa åtgärden Ctrl-Break om du vill stoppa eller starta om belastningen generator. 

  Om du lämnar belastningen generatorn körs i förgrunden, kan du använda en annan PowerShell ISE-instans för att köra andra PowerShell-skript.

&nbsp;

Lämna belastningen generatorn körs i läget jobbet anropar innan du fortsätter med nästa avsnitt.

## <a name="provision-a-new-tenant"></a>Etablera en ny klient

Den första distributionen skapas tre exempel innehavare. Nu kan du skapa en annan klient om du vill visa effekten på det distribuerade programmet. I appen Wingtip arbetsflödet för att etablera nya klienter förklaras i den [etablera och katalogen kursen](saas-dbpertenant-provision-and-catalog.md). I det här steget skapar du en ny klient som tar mindre än en minut.

1. Öppna en ny PowerShell ISE.
2. Öppna... \\Modules\Provision och katalogen\\*Demo-ProvisionAndCatalog.ps1*.
3. Tryck på F5 för att köra skriptet. Lämna standardvärdena för tillfället.

   > [!NOTE]
   > Många SaaS Wingtip skript använder *$PSScriptRoot* att bläddra i mapparna för att anropa funktioner i andra skript. Den här variabeln utvärderas bara när fullständig skriptet körs genom att trycka på F5. Markera och kör en markering med F8 kan leda till fel. Tryck på F5 för att köra skripten.

Den nya innehavare databasen är:

- Skapa i en elastisk SQL-pool.
- Initiera.
- Registrerats i katalogen.

Efter lyckad etableringen av *händelser* platsen för den nya innehavaren visas i webbläsaren.

![Ny klient](./media/saas-dbpertenant-get-started-deploy/red-maple-racing.png)

Uppdatera hubben händelser så att den nya innehavaren som visas i listan.

## <a name="explore-the-servers-pools-and-tenant-databases"></a>Utforska servrar, pooler, och klientdatabaser

Nu när du har startat körs en belastning för samlingen av innehavare kan vi titta på några av de resurser som har distribuerats.

1. I den [Azure-portalen](http://portal.azure.com), bläddra i listan över SQL-servrar. Öppna den **katalog-dpt -&lt;användare&gt;**  server.
    - Katalogservern innehåller två databaserna, **tenantcatalog** och **basetenantdb** (en mall för databas som kopieras om du vill skapa nya klienter).

   ![Databaser](./media/saas-dbpertenant-get-started-deploy/databases.png)

2. Gå tillbaka till listan med SQL-servrar.

3. Öppna den **tenants1-dpt -&lt;användare&gt;**  server som innehåller klient-databaser.

4. Se följande:

    - Varje klient-databasen är en **elastisk Standard** databas i en standard 50 eDTU-pool.
    - Röd lönn tävling-databasen är klient-databasen som du tidigare har etablerats.

   ![Server med databaser](./media/saas-dbpertenant-get-started-deploy/server.png)

## <a name="monitor-the-pool"></a>Övervaka poolen

Efter *LoadGenerator.ps1* körs under flera minuter tillräckligt med data ska vara tillgängliga för att starta tittar på vissa övervakningsfunktionerna. Dessa funktioner är inbyggda i pooler och databaser.

Bläddra till servern **tenants1-dpt -&lt;användare&gt;**, och välj **Pool1** att visa resursanvändningen för poolen. I följande diagram kördes belastningen generator under en timme.

   ![Övervaka poolen](./media/saas-dbpertenant-get-started-deploy/monitor-pool.png)

- Det första diagrammet märkta **resursutnyttjande**, visar pool-eDTU-användning.
- Det andra diagrammet visar eDTU-användning av fem mest aktiva databaser i poolen.

De två diagrammen visar att elastiska pooler och SQL-databas är utmärkt för arbetsbelastningar för oväntade SaaS-program. Diagrammen visar att fyra databaser är varje burst-överföring till så mycket som 40 edtu: er och ännu alla databaser bekvämt stöds av en 50 eDTU-pool. 50 eDTU-pool stöder även tyngre arbetsbelastningar. Om databaserna har etablerats som fristående databaser, var och en måste vara en S2 (50 DTU) för att stödja belastning. Kostnaden för fyra fristående S2 databaser är nästan tre gånger priset för poolen. SQL-databas kunder kör upp till 500 databaser i 200 eDTU-pooler i verkliga situationer. Mer information finns i [prestanda övervakning kursen](saas-dbpertenant-performance-monitoring.md).

## <a name="additional-resources"></a>Ytterligare resurser

- Mer information finns i ytterligare [självstudier som bygger på det databas per klient Wingtip biljetter SaaS-programmet](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials).
- Läs om elastiska pooler i [vad är en Azure SQL-elastisk pool?](sql-database-elastic-pool.md).
- Läs om elastiska jobb i [hantera utskalat moln databaser](sql-database-elastic-jobs-overview.md).
- Mer information om flera SaaS-program, se [designmönster för SaaS-program som multitenant](saas-tenancy-app-design-patterns.md).


## <a name="next-steps"></a>Nästa steg

I den här guiden har du lärt dig:

> [!div class="checklist"]
> - Så här distribuerar du Wingtip biljetter SaaS-program.
> - Om servrar, pooler och databaser som ingår i appen.
> - Hur klienter mappas till sina data med den *katalogen*.
> - Hur du etablerar nya klienter.
> - Så här visar du poolen nätverksanvändning för att övervaka aktivitet på klienten.
> - Ta bort resurser för att stoppa relaterade fakturering.

Försök med den [etablera och katalogen kursen](saas-dbpertenant-provision-and-catalog.md).



<!-- Link references. -->

[github-wingtip-dpt]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant 

