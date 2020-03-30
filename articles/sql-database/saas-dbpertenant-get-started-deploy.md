---
title: SaaS-självstudie för databas per klient
description: Distribuera och utforska Wingtip Tickets SaaS multitenant-program som demonstrerar mönstret för databas per klient och andra SaaS-mönster med hjälp av Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: sstein
ms.date: 01/25/2019
ms.openlocfilehash: 31f712f80ee2492e4bbaec99bd093d46f9d04e6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73824000"
---
# <a name="deploy-and-explore-a-multitenant-saas-app-that-uses-the-database-per-tenant-pattern-with-sql-database"></a>Distribuera och utforska en SaaS-app med flera innehavare som använder mönstret databas per klient med SQL Database

I den här självstudien distribuerar och utforskar du Wingtip Tickets SaaS-databas-per-klient-programmet (Wingtip). Appen använder ett databas-per-klient-mönster för att lagra data från flera klienter. Appen är utformad för att visa upp funktioner i Azure SQL Database som förenklar hur du aktiverar SaaS-scenarier.

Fem minuter efter att du har **valt Distribuera till Azure**har du ett SaaS-program med flera trogna. Appen innehåller en SQL-databas som körs i molnet. Appen distribueras med tre exempelklienter, var och en med sin egen databas. Alla databaser distribueras till en elastisk SQL-pool. Appen distribueras till din Azure-prenumeration. Du har full tillgång till utforska och arbeta med de enskilda komponenterna i appen. Programmet C# källkod och skript hantering finns i [WingtipTicketsSAaS-DbPerTenant GitHub repo][github-wingtip-dpt].

I den här guiden lär du dig:

> [!div class="checklist"]
> - Så här distribuerar du Wingtip SaaS-programmet.
> - Var du kan hämta programmets källkod och hanteringsskript.
> - Om de servrar, pooler och databaser som utgör appen.
> - Så här mappas klienter till sina data med *katalogen*.
> - Så här etablerar du en ny klient.
> - Så här övervakar du klientaktivitet i appen.

En [serie relaterade tutorials](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials) erbjuder sig att utforska olika SaaS design och mönster förvaltning. Självstudierna bygger utöver den första distributionen. När du använder självstudierna kan du undersöka de angivna skripten för att se hur de olika SaaS-mönstren implementeras. Skripten visar hur funktioner i SQL Database förenklar utvecklingen av SaaS-program.

## <a name="prerequisites"></a>Krav

För att slutföra den här självstudien, kontrollera att Azure PowerShell är installerat. Mer information finns i [Kom igång med Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="deploy-the-wingtip-tickets-saas-application"></a>Distribuera Wingtip Biljetter SaaS ansökan

### <a name="plan-the-names"></a>Planera namnen

I stegen i det här avsnittet anger du ett användarvärde som används för att se till att resursnamnen är globalt unika. Du anger också ett namn för resursgruppen som innehåller alla resurser som skapats av en distribution av appen. För en fiktiv person som heter Ann Finley, föreslår vi:

- **Användare**: *af1* består av Ann Finleys initialer plus en siffra. Om du distribuerar appen en andra gång använder du ett annat värde. Ett exempel är af2.
- **Resursgrupp**: *wingtip-dpt-af1* anger att detta är appen databas per klient. Lägg till användarnamnet af1 för att korrelera resursgruppsnamnet med namnen på de resurser som det innehåller.

Välj dina namn nu och skriv ner dem.

### <a name="steps"></a>Steg

1. Om du vill öppna distributionsmallen För Wingtip Tickets SaaS-databas per klient i Azure-portalen väljer du **Distribuera till Azure**.

   <a href="https://aka.ms/deploywingtipdpt" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

1. Ange värden i mallen för de parametrar som krävs.

    > [!IMPORTANT]
    > Vissa autentiserings- och serverbrandvägger är avsiktligt osäkrade för demonstrationsändamål. Vi rekommenderar att du skapar en ny resursgrupp. Använd inte befintliga resursgrupper, servrar eller pooler. Använd inte det här programmet, skripten eller några distribuerade resurser för produktion. Ta bort den här resursgruppen när du är klar med programmet för att stoppa relaterad fakturering.

    - **Resursgrupp**: Välj **Skapa ny**och ange det unika namn som du valde tidigare för resursgruppen.
    - **Plats**: Välj en plats i listrutan.
    - **Användare**: Använd det användarnamnsvärde som du valde tidigare.

1. Distribuera programmet.

    a. Välj det här om du vill godkänna villkoren.

    b. Välj **Köp**.

1. Om du vill övervaka distributionsstatus väljer du **Meddelanden** (klockikonen till höger om sökrutan). Det tar cirka fem minuter att distribuera Wingtip Tickets SaaS-appen.

   ![Distributionen lyckades](media/saas-dbpertenant-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-wingtip-tickets-management-scripts"></a>Ladda ner och låsa upp Wingtip Tickets management skript

Medan programmet distribueras hämtar du källkoden och hanteringsskripten.

> [!IMPORTANT]
> Körbart innehåll (skript och DLL-filer) kan blockeras av Windows när ZIP-filer hämtas från en extern källa och extraheras. Följ stegen för att avblockera ZIP-filen innan du extraherar skripten. Avblockering säkerställer att skripten tillåts köras.

1. Bläddra till [WingtipTicketsSSaaS-DbPerTenant GitHub repo][github-wingtip-dpt].
1. Välj **Klona eller ladda ned**.
1. Välj **Hämta ZIP**och spara sedan filen.
1. Högerklicka på filen **WingtipTicketsSSaaS-DbPerTenant-master.zip** och välj sedan **Egenskaper**.
1. Välj Ta bort **blockeringen** > **Använd**på fliken **Allmänt** .
1. Välj **OK**och extrahera filerna

Skript finns i ... \\Mappen WingtipTicketsSSaaS-DbPerTenant-master\\Learning Modules.

## <a name="update-the-user-configuration-file-for-this-deployment"></a>Uppdatera användarkonfigurationsfilen för den här distributionen

Innan du kör skript uppdaterar du resursgruppen och användarvärdena i filen Användarkonfiguration. Ange dessa variabler till de värden som du använde under distributionen.

1. I PowerShell ISE, öppna ... \\Utbildningsmoduler\\**UserConfig.psm1**
1. Uppdatera **ResourceGroupName** och **Namn** med specifika värden för distributionen (endast på raderna 10 och 11).
1. Spara ändringarna.

Dessa värden refereras i nästan alla skript.

## <a name="run-the-application"></a>Köra appen

Appen visar upp platser som är värd för evenemang. Lokaltyper inkluderar konsertsalar, jazzklubbar och idrottsklubbar. I Wingtip Biljetter, platser är registrerade som hyresgäster. Att vara hyresgäst ger en plats ett enkelt sätt att lista evenemang och att sälja biljetter till sina kunder. Varje plats får en personlig webbplats för att lista sina evenemang och sälja biljetter.

Internt i appen får varje klient en SQL-databas distribuerad till en ELASTISK SQL-pool.

En central **händelsehubbsida** innehåller en lista med länkar till klienterna i distributionen.

1. Använd URL:en för att öppna händelsehubben i webbläsaren: http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net. Ersätt &lt;&gt; användare med distributionens användarvärde.

    ![Hub för evenemang](media/saas-dbpertenant-get-started-deploy/events-hub.png)

2. Välj **Fabrikam Jazz Club** i evenemangshubben.

    ![Händelser](./media/saas-dbpertenant-get-started-deploy/fabrikam.png)

### <a name="azure-traffic-manager"></a>Azure Traffic Manager

Wingtip-programmet använder [*Azure Traffic Manager*](../traffic-manager/traffic-manager-overview.md) för att styra distributionen av inkommande begäranden. Url:en för att komma åt händelsesidan för en viss klient använder följande format:

- http://events.wingtip-dpt.&lt;användare&gt;.trafficmanager.net/fabrikamjazzclub

    Delarna i föregående format förklaras i följande tabell.

    | URL-del        | Beskrivning       |
    | :-------------- | :---------------- |
    | http://events.wingtip-dpt | Händelsedelarna i Wingtip-appen.<br /><br /> *-DPT* skiljer *databas-per-hyresgäst* genomförandet av Wingtip Biljetter från andra implementeringar. Exempel är implementeringarna *för en app* per klient (*-sa*) eller *multitenant database* (*-mt).* |
    | . * &lt;användare&gt;* | *af1* i exemplet. |
    | .trafficmanager.net/ | Traffic Manager, bas-URL. |
    | fabrikamjazzclub | Identifierar klienten som heter Fabrikam Jazz Club. |
    | &nbsp; | &nbsp; |

- Klientnamnet tolkas från URL:en av händelseappen.
- Klientnamnet används för att skapa en nyckel.
- Nyckeln används för att komma åt katalogen för att hämta platsen för klientens databas.
  - Katalogen implementeras med hjälp av *shard map management*.
- Händelsehubben använder utökade metadata i katalogen för att konstruera url:er för händelseförteckningssidan för varje klient.

I en produktionsmiljö skapar du vanligtvis en CNAME DNS-post för att [*peka ett företags internetdomän*](../traffic-manager/traffic-manager-point-internet-domain.md) till Traffic Manager DNS-namnet.

> [!NOTE]
> Det kanske inte är omedelbart uppenbart vad användningen av trafikhanteraren är i den här guiden. Målet med denna serie av tutorials är att visa upp mönster som kan hantera omfattningen av en komplex produktionsmiljö. I så fall skulle du till exempel ha flera webbappar distribuerade över hela världen, samlokalring med databaser och du skulle behöva trafikhanterare för att dirigera mellan dessa instanser.
En annan uppsättning självstudier som illustrerar användningen av trafikhanteraren är dock [geo-återställa](saas-dbpertenant-dr-geo-restore.md) och [geo-replikering](saas-dbpertenant-dr-geo-replication.md) tutorials. I dessa självstudier används trafikhanteraren för att växla över till en återställningsinstans av SaaS-appen i händelse av ett regionalt avbrott.

## <a name="start-generating-load-on-the-tenant-databases"></a>Börja generera belastningar på klient-databaserna

Nu när appen är distribuerad, låt oss sätta det i arbete.

*Demo-LoadGenerator PowerShell-skriptet* startar en arbetsbelastning som körs mot alla klientdatabaser. Den verkliga belastningen på många SaaS-appar är sporadisk och oförutsägbar. För att simulera den här typen av belastning producerar generatorn en belastning med randomiserade toppar eller aktivitetsskurar på varje klient. Bursts inträffar med slumpmässiga intervall. Det tar flera minuter för belastningsmönstret att växa fram. Låt generatorn gå i minst tre eller fyra minuter innan du övervakar belastningen.

1. I PowerShell ISE, öppna ... \\Utbildningsmoduler\\\\*Demo-LoadGenerator.ps1-skript.*
2. Tryck på F5 för att köra skriptet och starta laddningsgeneratorn. Lämna standardparametervärdena för tillfället.
3. Logga in på ditt Azure-konto och välj den prenumeration du vill använda om det behövs.

Inläsningsgeneratorskriptet startar ett bakgrundsjobb för varje databas i katalogen och stannar sedan. Om du kör inladdningsgeneratorskriptet igen stoppas alla bakgrundsjobb som körs innan det startar nya.

### <a name="monitor-the-background-jobs"></a>Övervaka bakgrundsjobben

Om du vill styra och övervaka bakgrundsjobben använder du följande cmdlets:

- `Get-Job`
- `Receive-Job`
- `Stop-Job`

### <a name="demo-loadgeneratorps1-actions"></a>Åtgärder för demo-loadgenerator.ps1

*Demo-LoadGenerator.ps1* härmar en aktiv arbetsbelastning för kundtransaktioner. I följande steg beskrivs sekvensen av åtgärder som *Demo-LoadGenerator.ps1* initierar:

1. *Demo-LoadGenerator.ps1* startar *LoadGenerator.ps1* i förgrunden.

    - Båda PS1-filerna lagras under mapparna\\Learning\\Modules Utilities .

2. *LoadGenerator.ps1* loopar genom alla klientdatabaser i katalogen.

3. *LoadGenerator.ps1* startar ett PowerShell-jobb i bakgrunden för varje klientdatabas:

    - Som standard körs bakgrundsjobben i 120 minuter.
    - Varje jobb orsakar en CPU-baserad belastning på en klientdatabas genom att köra *sp_CpuLoadGenerator*. Belastningens intensitet och varaktighet varierar beroende på `$DemoScenario`.
    - *sp_CpuLoadGenerator* loopar runt ett SQL SELECT-uttryck som orsakar en hög CPU-belastning. Tidsintervallet mellan frågorna i SELECT varierar beroende på parametervärden för att skapa en kontrollerbar CPU-belastning. Belastningsnivåer och intervall randomiseras för att simulera mer realistiska belastningar.
    - Den här SQL-filen lagras under *WingtipTenantDB\\dbo\\StoredProcedures\\*.

4. Om `$OneTime = $false`startar lastgeneratorn bakgrundsjobben och fortsätter sedan att köras. Var 10:e sekund övervakar den för alla nya klienter som har etablerats. Om du `$OneTime = $true`anger startar LoadGenerator bakgrundsjobben och slutar sedan köras i förgrunden. För den här `$OneTime = $false`självstudien, lämna .

   Använd Ctrl-C eller Stop Operation Ctrl-Break om du vill stoppa eller starta om laddningsgeneratorn.

   Om du låter lastgeneratorn köras i förgrunden använder du en annan PowerShell ISE-instans för att köra andra PowerShell-skript.

&nbsp;

Innan du fortsätter med nästa avsnitt lämnar du lastgeneratorn igång i jobbanskadetillståndet.

## <a name="provision-a-new-tenant"></a>Etablera en ny klient

Den första distributionen skapar tre exempelklienter. Nu kan du skapa en annan klient för att se effekten på det distribuerade programmet. I Wingtip-appen förklaras arbetsflödet för att etablera nya klienter i [självstudiekursen Etablera och katalogen](saas-dbpertenant-provision-and-catalog.md). I den här fasen skapar du en ny klient, vilket tar mindre än en minut.

1. Öppna en ny PowerShell ISE.
2. Öppna... \\Utbildningsmoduler\Etablering och\\*katalog demo-provisionAndCatalog.ps1*.
3. Om du vill köra skriptet trycker du på F5. Lämna standardvärdena för tillfället.

   > [!NOTE]
   > Många Wingtip SaaS-skript använder *$PSScriptRoot* för att bläddra i mappar för att anropa funktioner i andra skript. Den här variabeln utvärderas endast när det fullständiga skriptet körs genom att trycka på F5.Om du markerar och kör en markering med F8 kan det leda till fel. Om du vill köra skripten trycker du på F5.

Den nya klientdatabasen är:

- Skapad i en ELASTISK SQL-pool.
- Initierats.
- Registrerad i katalogen.

När *etableringen* har slutförts visas händelseplatsen för den nya klienten i webbläsaren.

![Ny klient](./media/saas-dbpertenant-get-started-deploy/red-maple-racing.png)

Uppdatera händelsehubben så att den nya klienten visas i listan.

## <a name="explore-the-servers-pools-and-tenant-databases"></a>Utforska servrar, pooler, och klientdatabaser

Nu när du har börjat köra en belastning mot insamling av klienter, låt oss titta på några av de resurser som har distribuerats.

1. I [Azure-portalen](https://portal.azure.com)bläddrar du till din lista över SQL-servrar. Öppna sedan **&lt;katalog-dpt-USER-servern.&gt; **
    - Katalogservern innehåller två databaser, **klientkatalog** och **basetenantdb** (en malldatabas som kopieras för att skapa nya klienter).

   ![Databaser](./media/saas-dbpertenant-get-started-deploy/databases.png)

2. Gå tillbaka till listan över SQL-servrar.

3. Öppna **klienten1-dpt-&gt; USER-servern&lt;** som innehåller klientdatabaserna.

4. Se följande punkter:

    - Varje klientdatabas är en **Elastisk standarddatabas** i en standardpool på 50 eDTU.
    - Red Maple Racing-databasen är klientdatabasen som du har etablerat tidigare.

   ![Server med databaser](./media/saas-dbpertenant-get-started-deploy/server.png)

## <a name="monitor-the-pool"></a>Övervaka poolen

När *LoadGenerator.ps1* har körts i flera minuter bör det finnas tillräckligt med data för att börja titta på vissa övervakningsfunktioner. Dessa funktioner är inbyggda i pooler och databaser.

Bläddra till **serverns&lt;klienter1-dpt-användare&gt;** och välj **Pool1** för att visa resursutnyttjande för poolen. I följande diagram körde lastgeneratorn i en timme.

   ![Övervaka pool](./media/saas-dbpertenant-get-started-deploy/monitor-pool.png)

- Det första diagrammet, med namnet **Resursutnyttjande,** visar pool eDTU-användning.
- Det andra diagrammet visar eDTU-användningen av de fem mest aktiva databaserna i poolen.

De två diagrammen visar att elastiska pooler och SQL Database är väl lämpade för oförutsägbara SaaS-programarbetsbelastningar. Diagrammen visar att fyra databaser är varje spricker till så mycket som 40 eDTU: er, och ändå alla databaser är bekvämt stöds av en 50-eDTU pool. 50-eDTU-poolen kan stödja ännu tyngre arbetsbelastningar. Om databaserna etableras som enskilda databaser måste var och en vara en S2 (50 DTU) för att stödja bursts. Kostnaden för fyra enda S2 databaser är nästan tre gånger priset på poolen. I verkliga situationer kör SQL Database-kunder upp till 500 databaser i 200 eDTU-pooler. Mer information finns i [självstudiekursen För prestandaövervakning](saas-dbpertenant-performance-monitoring.md).

## <a name="additional-resources"></a>Ytterligare resurser

- Mer information finns i ytterligare [självstudier som bygger på Wingtip Tickets SaaS-programmet för databas per klient](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials).
- Mer information om elastiska pooler finns i [Vad är en elastisk Azure SQL-pool?](sql-database-elastic-pool.md).
- Mer information om elastiska jobb finns i [Hantera utskalade molndatabaser](elastic-jobs-overview.md).
- Mer information om SaaS-program med flera emande finns i [Designmönster för SaaS-program med flera egenskaper](saas-tenancy-app-design-patterns.md).

## <a name="next-steps"></a>Nästa steg

I den här guiden har du lärt dig:

> [!div class="checklist"]
> - Så här distribuerar du Wingtip Tickets SaaS-programmet.
> - Om de servrar, pooler och databaser som utgör appen.
> - Så här mappas klienter till sina data med *katalogen*.
> - Så här etablerar du nya klienter.
> - Så här visar du poolanvändning för att övervaka klientaktivitet.
> - Så här tar du bort exempelresurser för att stoppa relaterad fakturering.

Försök sedan [med självstudiekursen Etablera och katalogen](saas-dbpertenant-provision-and-catalog.md).

<!-- Link references. -->

[github-wingtip-dpt]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant
