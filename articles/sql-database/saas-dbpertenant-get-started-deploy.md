---
title: Databas-per-klient SaaS-självstudie – Azure SQL Database | Microsoft Docs
description: Distribuera och utforska Wingtip biljetter SaaS med flera klienter programmet som visar mönster för databas-per-klient och andra SaaS-mönstren med hjälp av Azure SQL Database.
keywords: sql database tutorial
services: sql-database
author: MightyPen
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: genemi
ms.openlocfilehash: cc3e870d67f3c38fe4173275b6fd210d0c4ee05a
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39423573"
---
# <a name="deploy-and-explore-a-multitenant-saas-app-that-uses-the-database-per-tenant-pattern-with-sql-database"></a>Distribuera och utforska en multitenant SaaS-app som använder mönstret databas-per-klient med SQL Database

I den här självstudien får du distribuera och utforska Wingtip biljetter SaaS databas-per-klient-programmet (Wingtip). Appen använder ett mönster för databas-per-klient för att lagra data med flera klienter. Appen har utformats för att demonstrera funktionerna i Azure SQL Database som förenklar hur du aktiverar SaaS-scenarier.

Fem minuter när du har valt **distribuera till Azure**, du har en SaaS-program med flera klienter. Appen innehåller en SQL-databas som körs i molnet. Appen distribueras med tre exempelklienter, var och en med sin egen databas. Alla databaserna som har distribuerats till en SQL-elastisk pool. Appen har distribuerats till din Azure-prenumeration. Du har fullständig åtkomst att utforska och arbeta med enskilda komponenter i appen. Programmets källkod för C# och av hanteringsskript är tillgängliga i den [WingtipTicketsSaaS DbPerTenant GitHub-lagringsplatsen][github-wingtip-dpt].

I den här guiden lär du dig:

> [!div class="checklist"]
> - Hur du distribuerar Wingtip SaaS-program.
> - Var du kan hämta programkällan kod och hantering av skript.
> - Om servrar, pooler och databaser som ingår i appen.
> - Hur klienter mappas till sina data med den *catalog*.
> - Hur du etablerar en ny klient.
> - Så här att övervaka klientaktivitet i appen.

En [uppsättning relaterade guider](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials) erbjuder att utforska olika SaaS design och -hanteringsmönster. Självstudierna skapa utöver den här inledande distributionen. När du använder självstudierna kan undersöka du skripten som tillhandahålls för att se hur de olika SaaS-mönstren implementeras. Skripten visar hur funktionerna i SQL Database förenklar utvecklingen av SaaS-program.

## <a name="prerequisites"></a>Förutsättningar

Kontrollera för att slutföra den här självstudien Azure PowerShell har installerats. Mer information finns i [Kom igång med Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="deploy-the-wingtip-tickets-saas-application"></a>Distribuera Wingtip biljetter SaaS-program

#### <a name="plan-the-names"></a>Planera namnen

I stegen i det här avsnittet anger du ett värde som användaren som används för att se till att resursnamn är globalt unikt. Du kan också ange ett namn för resursgruppen som innehåller alla resurser som skapats av en distribution av appen. För en fiktiv person med namnet Ann Finley, föreslår vi:

- **Användaren**: *af1* består av Ann Finley initialer plus en siffra. Om du distribuerar appen en gång, Använd ett annat värde. Ett exempel är af2.
- **Resursgrupp**: *wingtip-dpt-af1* innebär att det är appen databas-per-klient. Lägg till användaren namnet af1 för att knyta samman resursgruppens namn med namnen på de resurser som den innehåller.

Välj ditt namn nu och Skriv ned dem. 

#### <a name="steps"></a>Steg

1. För att öppna Distributionsmall Wingtip biljetter SaaS databas-per-klient i Azure portal, Välj **distribuera till Azure**.

   <a href="https://aka.ms/deploywingtipdpt" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

1. Ange värden i mallen för de obligatoriska parametrarna.

    > [!IMPORTANT]
    > Vissa brandväggar för autentisering och servern har avsiktligt lämnats oskyddade i demonstrationssyfte. Vi rekommenderar att du skapar en ny resursgrupp. Använd inte befintliga resursgrupper, servrar eller pooler. Använd inte det här programmet, skript eller distribuerade resurser för produktion. Ta bort den här resursgruppen när du är klar med programmet för att stoppa relaterad fakturering.

    - **Resursgrupp**: Välj **Skapa nytt**, och ange det unika namn du angav tidigare för resursgruppen. 
    - **Plats**: Välj en plats från den nedrullningsbara listan.
    - **Användaren**: Använd värdet för det användarnamn du valde tidigare.

1. Distribuera programmet.

    a. Välj för att godkänna de allmänna villkoren.

    b. Välj **Köp**.

1. Om du vill övervaka Distributionsstatus, Välj **meddelanden** (klockikonen till höger om sökrutan). Distribuera Wingtip biljetter SaaS-appen tar cirka fem minuter.

   ![Distribueringen lyckades](media/saas-dbpertenant-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-wingtip-tickets-management-scripts"></a>Ladda ned och avblockera management Wingtip biljettskripten

Ladda ned källkod kod- och skript när programmet distribueras.

> [!IMPORTANT]
> Körbart innehåll (skript och DLL-filer) kan blockeras av Windows när ZIP-filer laddas ned från en extern källa och extraheras. Följ stegen för att låsa upp ZIP-filen innan du extrahera skripten. Avblockera ser till att skripten ska tillåtas att köras.

1. Bläddra till den [WingtipTicketsSaaS DbPerTenant GitHub-lagringsplatsen][github-wingtip-dpt].
1. Välj **klona eller ladda ned**.
1. Välj **ladda ned ZIP**, och spara filen.
1. Högerklicka på den **WingtipTicketsSaaS-DbPerTenant-master.zip** och väljer sedan **egenskaper**.
1. På den **Allmänt** fliken **avblockera** > **tillämpa**.
1. Välj **OK**, och extrahera filer

Skript finns i den... \\WingtipTicketsSaaS-DbPerTenant-master\\mappen inlärningsmoduler.

## <a name="update-the-user-configuration-file-for-this-deployment"></a>Uppdatera användarkonfigurationsfilen för den här distributionen

Uppdatera resurs gruppernas och användarnas värdena i konfigurationsfilen för användaren innan du kör alla skript. Ange variablerna till de värden som du använde under distributionen.

1. I PowerShell ISE öppnar du... \\Inlärningsmoduler\\**UserConfig.psm1** 
1. Uppdatera **ResourceGroupName** och **namn** med specifika värden för din distribution (på raderna 10 och 11 endast).
1. Spara ändringarna.

Dessa värden refereras i nästan alla skript.

## <a name="run-the-application"></a>Köra programmet

Appen visar platser som är värdar för händelser. Platstyperna är konsertlokaler, jazzklubbar och sport föreningar. Platser är registrerade som klienter i Wingtip biljetter. En klient har en plats enkelt kunna lista evenemang och sälja biljetter till sina kunder. Varje plats får en anpassad webbplats att sina evenemang och sälja biljetter.

I appen hämtar internt varje klient en SQL-databas som distribuerats i en elastisk SQL-pool.

En central **Evenemangshubben** sidan innehåller en lista med länkar till klienter i din distribution.

1. Använd URL: en för att öppna Evenemangshubben i webbläsaren: http://events.wingtip-dpt.&lt; användare&gt;. trafficmanager.net. Ersätt &lt;användaren&gt; med värdet för användarnamn för din distribution.

    ![Evenemangshubben](media/saas-dbpertenant-get-started-deploy/events-hub.png)

1. Välj **Fabrikam Jazzklubb** i Evenemangshubben.

    ![Händelser](./media/saas-dbpertenant-get-started-deploy/fabrikam.png)

#### <a name="azure-traffic-manager"></a>Azure Traffic Manager

Wingtip-programmet använder [ *Azure Traffic Manager* ](../traffic-manager/traffic-manager-overview.md) att styra distributionen av inkommande begäranden. URL till sidan händelser för en specifik klient använder följande format:

- http://events.wingtip-dpt.&lt; användare&gt;.trafficmanager.net/fabrikamjazzclub

    Delar av det föregående formatet beskrivs i följande tabell.

    | URL-del        | Beskrivning       |
    | :-------------- | :---------------- |
    | http://events.wingtip-dpt | Händelser som delar av Wingtip-app.<br /><br /> *-dpt* särskiljer den *databas-per-klient* implementeringen av Wingtip biljetter från andra implementeringar. Exempel är den *fristående* app-per-klient (*-sa*) eller *multitenant databasen* (*- mt*) implementeringar. |
    | .  *&lt;användare&gt;* | *af1* i det här exemplet. |
    | .trafficmanager.net/ | Traffic Manager, bas-URL. |
    | fabrikamjazzclub | Identifierar de klient med namnet Fabrikam Jazzklubb. |
    | &nbsp; | &nbsp; |

* Klientnamnet analyseras av appen händelser från URL: en.
* Innehavarens namn används för att skapa en nyckel.
* Nyckeln används för att få åtkomst till katalogen för att hämta platsen för klientens databas.
    - Katalogen implementeras med hjälp av *fragmentkarthantering*.
* Evenemangshubben använder utökade metadata i katalogen för att konstruera lista över händelser sidan-URL: er för varje klient.

I en produktionsmiljö, vanligtvis skapar du en CNAME DNS-post till [ *peka företagets Internetdomän* ](../traffic-manager/traffic-manager-point-internet-domain.md) till Traffic Manager DNS-namn.

## <a name="start-generating-load-on-the-tenant-databases"></a>Börja generera belastningar på klient-databaserna

Nu när appen har distribuerats kan vi få det att fungera.

Den *Demo-LoadGenerator* PowerShell-skriptet startar en arbetsbelastning som körs mot alla klientdatabaser. Den verkliga belastningen på många SaaS-appar är sporadisk och oförutsägbar. Om du vill simulera den här typen av belastningen producerar generatorn en belastning med slumpmässig toppar och toppar på varje klient. Topparna ske med slumpmässig mellanrum. Det tar flera minuter för mönster för belastningsutjämning till dyker upp. Att låta generatorn köra i minst tre eller fyra minuter innan du övervaka belastningen.

1. I PowerShell ISE öppnar den... \\Inlärningsmoduler\\verktyg\\*Demo-LoadGenerator.ps1* skript.
1. Tryck på F5 för att köra skriptet och starta belastningsgeneratorn. Låt standardvärdet parametervärden för tillfället.
1. Logga in på ditt Azure-konto och välj den prenumeration som du vill använda, om det behövs.

Läs in generator skriptet startar ett bakgrundsjobb för varje databas i katalogen och stoppas. Om du kör skriptet belastningen generator stoppas alla BITS-jobb som körs innan den startar nya.

#### <a name="monitor-the-background-jobs"></a>Övervaka bakgrundsjobb

Om du vill kontrollera och övervaka bakgrundsjobb använder du följande cmdlets:

- `Get-Job`
- `Receive-Job`
- `Stop-Job`

#### <a name="demo-loadgeneratorps1-actions"></a>Demo-LoadGenerator.ps1 åtgärder

*Demo-LoadGenerator.ps1* efterliknar en aktiv arbetsbelastning för kundtransaktioner. Följande steg beskriver sekvens med åtgärder som *Demo-LoadGenerator.ps1* initierar:

1. *Demo-LoadGenerator.ps1* startar *LoadGenerator.ps1* i förgrunden.

    - Båda .ps1-filer lagras under mappar inlärningsmoduler\\verktyg\\.

1. *LoadGenerator.ps1* loopar igenom alla klientdatabaser i katalogen.

1. *LoadGenerator.ps1* startar ett PowerShell-bakgrundsjobb för varje klientdatabas:

    - Som standard körs bakgrundsjobb i 120 minuter.
    - Varje jobb gör att en processorbaserad belastning på en klientdatabas genom att köra *sp_CpuLoadGenerator*. Intensiteten och varaktighet för belastningen varierar beroende på `$DemoScenario`. 
    - *sp_CpuLoadGenerator* slingor runt en SQL SELECT-instruktion som orsakar en hög CPU-belastning. Tidsintervallet mellan problem i väljer varierar beroende på parametervärden som ska skapa en kontrolleras CPU-belastning. Belastningsnivåer och intervall väljs slumpvis för att simulera mer realistisk belastning.
    - Den här SQL-filen lagras under *WingtipTenantDB\\dbo\\StoredProcedures\\*.

1. Om `$OneTime = $false`, belastningsgeneratorn startar bakgrundsjobb och sedan fortsätter att köras. Var tionde sekund som den övervakar för alla nya klienter som har etablerats. Om du ställer in `$OneTime = $true`, LoadGenerator startar bakgrundsjobb och sedan slutar att köras i förgrunden. Den här självstudien lämna `$OneTime = $false`.

  Använd Ctrl-C eller stoppa åtgärden Ctrl-Break om du vill stoppa eller starta om belastningsgeneratorn. 

  Om du lämnar belastningsgeneratorn körs i förgrunden, kan du använda en annan PowerShell ISE-instans för att köra andra PowerShell-skript.

&nbsp;

Lämna belastningsgeneratorn köra i jobbet anropar tillstånd innan du fortsätter med nästa avsnitt.

## <a name="provision-a-new-tenant"></a>Etablera en ny klient

Den första distributionen skapar tre exempelklienter. Nu kan du skapa en annan klient för att se hur det distribuerade programmet. Arbetsflöde för att etablera nya klienter i Wingtip-appen, förklaras i den [guiden etablera och katalogisera](saas-dbpertenant-provision-and-catalog.md). I det här steget skapar du en ny klient, som tar mindre än en minut.

1. Öppna en ny PowerShell ISE.
1. Öppna... \\Inlärningsmoduler\etablera och katalogisera\\*Demo-ProvisionAndCatalog.ps1*.
1. Tryck på F5 för att köra skriptet. Lämna standardvärdena för tillfället.

   > [!NOTE]
   > Många Wingtip SaaS-skript använder *$PSScriptRoot* att bläddra i mapparna för att anropa funktioner i andra skript. Den här variabeln utvärderas bara när det fullständiga skriptet har körts genom att trycka på F5. Om du markerar och kör en markering med F8 kan resultera i fel. Tryck på F5 för att köra skripten.

Nya klientdatabasen är:

- Skapa i en elastisk SQL-pool.
- Initiera.
- Registrerat i katalogen.

Efter lyckad etablering, den *händelser* platsen i den nya klienten som visas i webbläsaren.

![Ny klient](./media/saas-dbpertenant-get-started-deploy/red-maple-racing.png)

Uppdatera Evenemangshubben för att göra den nya klienten som visas i listan.

## <a name="explore-the-servers-pools-and-tenant-databases"></a>Utforska servrar, pooler, och klientdatabaser

Nu när du har börjat köra en belastning mot klientsamlingen, nu ska vi titta på några av de resurser som har distribuerats.

1. I den [Azure-portalen](http://portal.azure.com), bläddra till din lista över SQL-servrar. Öppna sedan den **catalog-dpt -&lt;användaren&gt;**  server.
    - Katalogservern innehåller två databaser **tenantcatalog** och **basetenantdb** (en mall för databas som kopieras för att skapa nya klienter).

   ![Databaser](./media/saas-dbpertenant-get-started-deploy/databases.png)

1. Gå tillbaka till din lista över SQL-servrar.

1. Öppna den **tenants1-dpt -&lt;användaren&gt;**  server som innehåller klientdatabaserna.

1. Se följande objekt:

    - Varje klientdatabas är en **elastisk Standard** databas i en 50 eDTU-Standardpool.
    - Red Maple Racing-databasen är klientdatabas som du etablerade tidigare.

   ![Server med databaser](./media/saas-dbpertenant-get-started-deploy/server.png)

## <a name="monitor-the-pool"></a>Övervaka poolen

Efter *LoadGenerator.ps1* körs i flera minuter, borde tillräckliga data finnas tillgänglig för att börja titta på vissa övervakningsfunktionerna. De här funktionerna är inbyggda i pooler och databaser.

Bläddra till servern **tenants1-dpt -&lt;användaren&gt;**, och välj **Pool1** att visa resursanvändningen för poolen. I följande diagram körde belastningsgeneratorn under en timme.

   ![Övervaka poolen](./media/saas-dbpertenant-get-started-deploy/monitor-pool.png)

- Det första diagrammet som är märkt **resursutnyttjande**, visar poolens eDTU-användningen.
- Det andra diagrammet visar eDTU-användningen av de fem mest aktiva databaserna i poolen.

De två diagrammen visar att elastiska pooler och SQL-databas är utmärkt för arbetsbelastningar med oförutsägbara SaaS-program. Diagrammen visar att fyra databaser är varje burst-överförs till så mycket som 40 edtu: er och ännu alla databaser bekvämt stöds av en 50 eDTU-pool. 50 eDTU-pool har stöd för ännu större arbetsbelastningar. Om databaserna har etablerats som fristående databaser, var och en måste vara en S2 (50 DTU) för att stödja topparna. Kostnaden för 4 fristående S2-databaser är nästan tre gånger priset för poolen. I verkliga situationer kör SQL-databaskunder upp till 500 databaser i 200 eDTU-pooler. Mer information finns i den [guide för prestandaövervakning](saas-dbpertenant-performance-monitoring.md).

## <a name="additional-resources"></a>Ytterligare resurser

- Mer information finns i ytterligare [självstudier som bygger på databas-per-klient-programmet Wingtip biljetter SaaS](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials).
- Läs om elastiska pooler i [vad är en Azure SQL-elastisk pool?](sql-database-elastic-pool.md).
- Läs om elastiska jobb i [hantera utskalade molndatabaser](sql-database-elastic-jobs-overview.md).
- Läs mer om SaaS-program för flera innehavare, i [designmönster för SaaS-program för flera innehavare](saas-tenancy-app-design-patterns.md).


## <a name="next-steps"></a>Nästa steg

I den här guiden har du lärt dig:

> [!div class="checklist"]
> - Hur du distribuerar Wingtip biljetter SaaS-program.
> - Om servrar, pooler och databaser som ingår i appen.
> - Hur klienter mappas till sina data med den *catalog*.
> - Hur du etablerar nya klienter.
> - Så här ser poolanvändning för att övervaka klientaktivitet.
> - Hur du tar bort exempelresurser för att stoppa relaterad fakturering.

Nu ska du prova den [guiden etablera och katalogisera](saas-dbpertenant-provision-and-catalog.md).



<!-- Link references. -->

[github-wingtip-dpt]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant 

