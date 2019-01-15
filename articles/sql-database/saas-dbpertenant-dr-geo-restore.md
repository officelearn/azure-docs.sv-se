---
title: 'SaaS-appar: Azure SQL Database geo-redundanta säkerhetskopieringar för katastrofåterställning | Microsoft Docs'
description: Lär dig hur du använder Azure SQL Database geo-redundanta säkerhetskopieringar för att återställa en multitenant SaaS-app vid ett eventuellt strömavbrott
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: AyoOlubeko
ms.author: ayolubek
ms.reviewer: sstein
manager: craigg
ms.date: 10/15/2018
ms.openlocfilehash: acc1b9e9561b9468a4638c7073a066e4cb34d911
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2019
ms.locfileid: "54264758"
---
# <a name="use-geo-restore-to-recover-a-multitenant-saas-application-from-database-backups"></a>Använda geo-återställning för att återställa en SaaS-program med flera klienter från säkerhetskopior av databasen

Den här guiden utforskar en fullständig katastrofåterställning för ett program för flera SaaS som implementeras med databasen per klient modell. Du använder [geo-återställning](https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups) återställer katalog- och klienttrafik databaser från automatiskt behålla geo-redundanta säkerhetskopieringar till en annan återställningsregion. När driftstörningarna har åtgärdats kan du använda [geo-replikering](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview) till hem ändrade databaser till deras ursprungliga region.

![GEO-återställning-arkitektur](media/saas-dbpertenant-dr-geo-restore/geo-restore-architecture.png)

GEO-återställning finns billigaste haveriberedskapslösning för Azure SQL Database. Återställa från geo-redundanta säkerhetskopieringar kan emellertid resultera i dataförlust på upp till en timme. Det kan ta ganska lång tid, beroende på storleken på varje databas. 

> [!NOTE]
> Återställa program med lägsta möjliga RPO och RTO med hjälp av geo-replikering i stället för geo-återställning.

Den här guiden utforskar både återställning och hemtransport arbetsflöden. Lär dig att:
> [!div class="checklist"]

>* Synkronisera databaser och elastiska konfigurationsinformation för lagringspool i klient-katalogen.
>* Konfigurera en spegelbild miljö i en återställningsregion som innehåller program, servrar och pooler.   
>* Återställa katalog och klient-databaser med hjälp av geo-återställning.
>* Använda geo-replikering till hem klientkatalog och ändrade klientdatabaserna när driftstörningarna har åtgärdats.
>* Uppdatera katalogen när varje databas är återställts (eller återföras) att spåra den aktuella platsen för den aktiva kopian av varje klientdatabas.
>* Se till att programmet och klientdatabas är alltid samordnad i samma Azure-region som minskar svarstiderna. 
 

Innan du börjar den här självstudien utför du följande krav:
* Distribuera Wingtip biljetter SaaS-databas per app för klienter. Om du vill distribuera i mindre än fem minuter [distribuera och utforska Wingtip biljetter SaaS-databas per klient program](saas-dbpertenant-get-started-deploy.md). 
* Installera Azure PowerShell. Mer information finns i [komma igång med Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-geo-restore-recovery-pattern"></a>Introduktion till geo-återställning recovery mönstret

Haveriberedskap (DR) är viktigt för många program, vare sig det gäller efterlevnadsskäl och verksamhetskontinuitet. Om det finns en långvarig tjänstavbrott, kan en väl förberedd DR-plan minimera verksamhetsavbrott i. En DR-planen baserat på geo-återställning måste utföra flera mål:
 * Reservera alla nödvändiga kapaciteten i den valda återställningsplatsen regionen så snabbt som möjligt att se till att den är tillgänglig för att återställa klientdatabaser.
 * Upprätta en spegelbild Återställningsmiljö som återspeglar den ursprungliga pool och konfigurationen. 
 * Tillåt annullering av återställningsprocessen i mitten rör sig om den ursprungliga regionen är online igen.
 * Aktivera klient etablering snabbt så att den nya klienten onboarding kan starta om så snart som möjligt.
 * Optimeras för att återställa klienter i prioritetsordning.
 * Optimeras för att hämta klienter online så snart som möjligt genom att göra steg parallellt där det är praktiskt.
 * Vara motståndskraftiga mot fel, kan startas om, och idempotenta.
 * Hem-databaser till deras ursprungliga region med minimal påverkan på klienterna så fort driftstörningarna har åtgärdats.  

> [!NOTE]
> Programmet återställs till den parade regionen för den region där programmet har distribuerats. Mer information finns i [parade Azure-regioner](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).   

Den här guiden använder funktioner för Azure SQL Database och Azure-plattformen för att åtgärda dessa problem:

* [Azure Resource Manager-mallar](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template), för att reservera alla nödvändiga kapacitet så snabbt som möjligt. Azure Resource Manager-mallar används för att etablera en spegelbild av de ursprungliga servrarna och elastiska pooler i återställningsregionen. En separat server och en pool skapas också för att etablera nya klienter.
* [Klientbibliotek för elastiska databaser](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-database-client-library) (EDCL) att skapa och underhålla en klientkatalog för databasen. Utökade katalogen innehåller med jämna mellanrum pool- och konfigurationsinformation.
* [Shard för recovery hanteringsfunktioner](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-database-recovery-manager) av EDCL att underhålla databasen plats poster i katalogen under återställning och hemtransport.  
* [GEO-återställning](https://docs.microsoft.com/azure/sql-database/sql-database-disaster-recovery), återställer katalog- och klienttrafik databaser från automatiskt behålla geo-redundanta säkerhetskopieringar. 
* [Asynkron återställningsåtgärder](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations), skickas i prioritetsordning för klienten, är i kö för varje pool av systemet och bearbetas i batchar så att poolen inte är överbelastad. De här åtgärderna kan avbrytas före eller under körningen om det behövs.   
* [GEO-replikering](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview)att hem databaser till den ursprungliga regionen när avbrottet. Det finns inga data går förlorade och minimal inverkan på klienten när du använder geo-replikering.
* [SQL server-DNS-alias](https://docs.microsoft.com/azure/sql-database/dns-alias-overview)för att synkroniseringen catalog att ansluta till den aktiva katalogen, oavsett dess plats.  

## <a name="get-the-disaster-recovery-scripts"></a>Hämta disaster recovery skript

DR-skripten som används i den här självstudien är tillgängliga i den [Wingtip biljetter SaaS-databas per klient GitHub-lagringsplatsen](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant). Kolla in den [allmänna riktlinjer](saas-tenancy-wingtip-app-guidance-tips.md) steg att ladda ned och avblockera management Wingtip biljettskripten.

> [!IMPORTANT]
> Precis som alla Wingtip biljetter management skripten DR-skript är exempel kvalitet och som inte ska användas i produktion.

## <a name="review-the-healthy-state-of-the-application"></a>Granska felfri statusen för programmet
Granska det normala felfritt tillståndet innan du startar återställningsprocessen.

1. Öppna i webbläsaren, evenemangshubben Wingtip biljetter (http://events.wingtip-dpt.&lt; användare&gt;. trafficmanager.net, Ersätt &lt;användaren&gt; med värdet för användarnamn för din distribution).
    
   Bläddra längst ned på sidan och Lägg märke till katalogen servernamnet och en plats i sidfoten. Platsen är den region där du har distribuerat appen.    

   > [!TIP]
   > Hovra med musen över platsen som förstora visningen.

   ![Händelser hub felfritt tillstånd i ursprungliga region](media/saas-dbpertenant-dr-geo-restore/events-hub-original-region.png)

2. Välj Contosos Konserthall-klient och öppna appens sida för händelsen.

   Observera att klientens namn i sidfoten. Platsen är samma som den katalogservern plats.

   ![Contosos Konserthall ursprungliga region](media/saas-dbpertenant-dr-geo-restore/contoso-original-location.png) 

3. I den [Azure-portalen](https://portal.azure.com)granskar och öppna den resursgrupp som du har distribuerat appen.

   Observera att resurserna och den region där app service-komponenter och SQL Database-servrar distribueras.

## <a name="sync-the-tenant-configuration-into-the-catalog"></a>Synkronisera klientkonfiguration i katalogen

I den här uppgiften ska starta du en process för att synkronisera konfigurationen av servrar, elastiska pooler och databaser till klient-katalogen. Den här informationen används senare för att konfigurera en spegelbild i återställningsregionen.

> [!IMPORTANT]
> För enkelhetens skull implementeras synkroniseringen och andra långvariga återställning och hemtransport processer i de här exemplen som lokala PowerShell-jobb eller sessioner som körs under din klient användarinloggning. Autentiseringstoken utfärdas när du loggar in upphör att gälla efter flera timmar och jobben misslyckas sedan. I ett produktionsscenario för bör tidskrävande processer implementeras som tillförlitlig Azure-tjänster av något slag, som körs under ett huvudnamn för tjänsten. Se [med Azure PowerShell för att skapa ett huvudnamn för tjänsten med ett certifikat](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal). 

1. Öppna filen ...\Learning Modules\UserConfig.psm1 i PowerShell ISE. Ersätt `<resourcegroup>` och `<user>` på raderna 10 och 11 med det värde som används när du distribuerade appen. Spara filen.

2. Öppna ...\Learning Modules\Business affärskontinuitet och Haveriberedskap Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 skript i PowerShell ISE.

    I de här självstudierna att köra var och en av scenarierna som i det här PowerShell-skript, så Håll den här filen öppen.

3. Ange följande:

    $DemoScenario = 1: Starta ett bakgrundsjobb som synkroniserar klientservern och konfigurationsinformation för lagringspool i katalogen.

4. Om du vill köra skriptet synkronisering, tryck på F5. 

    Den här informationen används senare för att säkerställa att recovery skapar en spegelbild av servrar, pooler och databaser i återställningsregionen.  

    ![Synkroniseringen](media/saas-dbpertenant-dr-geo-restore/sync-process.png)

Lämna fönstret PowerShell som körs i bakgrunden och fortsätter med resten av kursen.

> [!NOTE]
> Synkroniseringen ansluter till katalog via ett DNS-alias. Aliaset har ändrats under återställning och hemtransport så att den pekar till den aktiva katalogen. Synkroniseringen håller katalogen uppdaterad med några databas eller pool konfigurationsändringar som gjorts i återställningsregionen. Under hemtransport tillämpas ändringarna på motsvarande resurser i den ursprungliga regionen.

## <a name="geo-restore-recovery-process-overview"></a>GEO-återställning översikt över återställningsprocessen

Geo-återställning vid återställningsprocessen distribuerar programmet och återställer databaser från säkerhetskopior i återställningsregionen.

Återställningsprocessen gör följande:

1. Inaktiverar Azure Traffic Manager-slutpunkt för webbappen i regionen ursprungliga. Inaktivera slutpunkten förhindrar användare från att ansluta till appen i ett ogiltigt tillstånd bör den ursprungliga regionen är online under återställningen.

2. Etablerar en återställning catalog server i återställningsregionen, geo-återställningar katalogdatabasen och uppdaterar activecatalog aliaset så att den pekar till den återställda katalogservern. Ändra katalogen aliaset säkerställer att synkroniseringen catalog alltid synkroniseras till den aktiva katalogen.

3. Markerar alla befintliga klienter i katalogen recovery som offline om du vill förhindra åtkomst till klientdatabaser innan de återställs.

4. Etablerar en instans av appen i återställningsregionen och konfigurerar den att använda återställda katalogen i den regionen. Om du vill behålla fördröjning till minst är exempelappen avsedd att ansluter alltid till en databas för klienter i samma region.

5. Etablerar en server och elastisk pool där nya klienter har etablerats. Skapa dessa resurser säkerställer att etablerar nya klienter inte stör återställningen av befintliga klienter.

6. Uppdaterar aliaset klient så att den pekar till servern för nya klientdatabaserna i återställningsregionen. Ändra det här aliaset säkerställer att databaserna för alla nya klienter som har etablerats i återställningsregionen.
        
7. Etablerar servrar och elastiska pooler i återställningsregionen för återställning av klientdatabaser. Dessa servrar och -pooler är en spegelbild av konfigurationen i den ursprungliga regionen. Etablering pooler direkt reserverar kapacitet som krävs för att återställa alla databaser.

    Ett avbrott i en region kan placera stor belastning på resurser som är tillgängliga i den parade regionen. Reservera resurser snabbt rekommenderas om du förlitar dig på geo-återställning för Katastrofåterställning. Överväg geo-replikering om det är viktigt att ett program återställs i en viss region. 

8. Aktiverar Traffic Manager-slutpunkten för webbappen i återställningsregionen. Om du aktiverar den här slutpunkten kan programmet för att etablera nya klienter. I det här skedet är befintliga klienter fortfarande offline.

9. Skickar batchar med förfrågningar om att återställa databaser i prioritetsordning. 

    * Batchar ordnas så att databaserna återställs parallellt över alla pooler.  

    * Restore-begäranden skickas asynkront så att de har skickats snabbt och i kö för körning i varje pool.

    * Eftersom återställning begäranden bearbetas parallellt över alla pooler, är det bättre att distribuera viktiga klienter över flera pooler. 

10. Övervakar SQL Database-tjänsten för att avgöra när databaser har återställts. När en klientdatabas återställs den markeras online i katalogen och en rowversion summa för klientdatabasen registreras. 

    * Klientdatabaser kan nås av programmet när de är märkta online i katalogen.

    * En summa av rowversion värden i klientdatabasen lagras i katalogen. Den här summan fungerar som ett fingeravtryck där hemtransport processen för att avgöra om databasen har uppdaterats i återställningsregionen.       

## <a name="run-the-recovery-script"></a>Kör skriptet för återställning

> [!IMPORTANT]
> Den här självstudien återställer databaser från geo-redundanta säkerhetskopieringar. Även om dessa säkerhetskopior är vanligtvis tillgängligt inom 10 minuter, kan det ta upp till en timme. Skriptet pausas tills de är tillgängliga.

Anta att det finns ett avbrott i den region där programmet distribueras, och kör skriptet för återställning:

1. Ange följande värde i ...\Learning Modules\Business affärskontinuitet och Haveriberedskap Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 skriptet i PowerShell ISE:

    $DemoScenario = 2: Återställ appen till en återställningsregion genom att återställa från geo-redundanta säkerhetskopieringar.

2. Tryck på F5 för att köra skriptet.  

    * Skriptet öppnas i ett nytt PowerShell-fönster och startar sedan en uppsättning PowerShell-jobb som körs parallellt. Dessa jobb återställa servrar, pooler och databaser till återställningsregionen.

    * Återställningsregionen är den parade regionen som är associerade med Azure-regionen där du har distribuerat programmet. Mer information finns i [parade Azure-regioner](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

3. Övervaka status för återställningen i PowerShell-fönstret.

    ![Återställningsprocessen](media/saas-dbpertenant-dr-geo-restore/dr-in-progress.png)

> [!NOTE]
> Granska PowerShell-skript i mappen ...\Learning Modules\Business affärskontinuitet och Haveriberedskap Recovery\DR-RestoreFromBackup\RecoveryJobs för att utforska koden för återställningsjobben.

## <a name="review-the-application-state-during-recovery"></a>Granska programmets tillstånd under återställningen
Programslutpunkten är inaktiverad i Traffic Manager, är programmet inte tillgänglig. Katalogen har återställts och alla klienter markeras offline. Programslutpunkt i återställningsregionen aktiveras sedan och programmet är online igen. Även om programmet är tillgängligt, visas klienter offline i evenemangshubben förrän deras databaser som återställs. Det är viktigt att utforma ditt program hantera offline klientdatabaser.

* När katalogdatabasen har återställts, men innan innehavarna som är online igen, uppdatera evenemangshubben Wingtip biljetter i webbläsaren.

    * Observera att namnet på katalogen har nu i sidfoten - recovery suffix och finns i återställningsregionen.

    * Observera att klienter som inte har återställts markeras som offline och inte är valbara.   
 
    ![Återställningsprocessen](media/saas-dbpertenant-dr-geo-restore/events-hub-tenants-offline-in-recovery-region.png)    

    * Om du öppnar en klients händelser sidan direkt medan klienten är offline, visar sidan ett meddelande om klient offline. Till exempel om Contosos Konserthall är offline försöker öppna http://events.wingtip-dpt.&lt; användare&gt;.trafficmanager.net/contosoconcerthall.

    ![Återställningsprocessen](media/saas-dbpertenant-dr-geo-restore/dr-in-progress-offline-contosoconcerthall.png)

## <a name="provision-a-new-tenant-in-the-recovery-region"></a>Etablera en ny klient i återställningsregionen
Du kan etablera nya klienter i återställningsregionen även innan klientdatabaser återställs. Ny klientdatabaser som etablerats i återställningsregionen återföras senare med de återställda databaserna.   

1. Ange följande egenskap i ...\Learning Modules\Business affärskontinuitet och Haveriberedskap Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 skriptet i PowerShell ISE:

    $DemoScenario = 3: Etablera en ny klient i återställningsregionen.

2. Tryck på F5 för att köra skriptet.

3. Sidan Hawthorn Hall händelser öppnas i webbläsaren när etableringen har slutförts. 

    Observera att den Hawthorn-databasen finns i återställningsregionen.

    ![Hawthorn Hall som etablerats i återställningsregionen](media/saas-dbpertenant-dr-geo-restore/hawthorn-hall-provisioned-in-recovery-region.png)

4. Uppdatera sidan Wingtip biljetter händelser hub om du vill se Hawthorn Hall ingår i webbläsaren. 

    Om du har etablerat Hawthorn Hall utan att vänta tills de andra klienterna att återställa kanske andra klienter fortfarande offline.

## <a name="review-the-recovered-state-of-the-application"></a>Granska återställda statusen för programmet

När återställningen är klar programmet och alla klienter är helt funktionella i återställningsregionen. 

1. När visningen i PowerShell-konsolfönster visar alla klienter har återställts måste du uppdatera evenemangshubben. 

    Alla hyresgäster visas online, inklusive den nya innehavaren Hawthorn Hall.

    ![återställda och nya klienter i evenemangshubben](media/saas-dbpertenant-dr-geo-restore/events-hub-with-hawthorn-hall.png)

2. Klicka på Contosos Konserthall och öppna appens sida för händelser. 

    Observera att databasen finns på återställningsservern i återställningsregionen i sidfoten.

    ![Contoso i återställningsregionen](media/saas-dbpertenant-dr-geo-restore/contoso-recovery-location.png)

3. I den [Azure-portalen](https://portal.azure.com), öppna listan över resursgrupper.  

    Observera den resursgrupp som du distribuerat, plus recovery resursen gruppen med suffixet-återställning. Recovery resursgruppen innehåller alla resurser som skapades under återställningen plus nya resurser som skapas under avbrottet. 

4. Öppna resursgruppen återställning och Lägg märke till följande objekt:

    * Recovery versioner av katalogen och tenants1-servrar, med suffixet-återställning. De återställda katalog- och klienttrafik databaserna på dessa servrar som alla har de namn som används i den ursprungliga regionen.

    * Tenants2-dpt -&lt;användaren&gt;-recovery SQLServer. Den här servern används för att etablera nya klienter under avbrottet.

    * App service med namnet händelser-wingtip-dpt -&lt;recoveryregion&gt;-&lt;användaren&gt;, vilket är evenemangsappen recovery-instansen.

    ![Contoso-resurser i återställningsregionen](media/saas-dbpertenant-dr-geo-restore/resources-in-recovery-region.png) 
    
5. Öppna tenants2-dpt -&lt;användaren&gt;-recovery SQLServer. Observera att den innehåller hawthornhall för databas och elastisk pool Pool1. Hawthornhall databasen är konfigurerad som en elastisk databas i den elastiska poolen Pool1.

## <a name="change-the-tenant-data"></a>Ändra data för klientorganisationen 
I den här uppgiften ska uppdatera du en av de återställda klientdatabaserna. Hemtransport processen kopior återställt databaserna som har ändrats till den ursprungliga regionen. 

1. Hitta händelselistan för Contosos Konserthall i webbläsaren, bläddra igenom händelserna och Lägg märke till den senaste händelsen allvarligt Strauss.

2. Ange följande värde i ...\Learning Modules\Business affärskontinuitet och Haveriberedskap Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 skriptet i PowerShell ISE:

    $DemoScenario = 4: Ta bort en händelse från en klient i återställningsregionen.

3. Tryck på F5 för att köra skriptet.

4. Uppdatera sidan Contosos Konserthall händelser (http://events.wingtip-dpt.&lt; användare&gt;.trafficmanager.net/contosoconcerthall), och Lägg märke till att händelsen allvarligt Strauss saknas.

Nu har du återställt programmet, vilket körs nu i återställningsregionen i självstudien. Du har etablerat en ny klient i återställningsregionen och ändra data i en av de återställda innehavarna.  

> [!NOTE]
> Andra självstudier i det här exemplet är inte utformade att köra appen i återställningstillstånd. Om du vill utforska andra självstudier, måste du först hem programmet.

## <a name="repatriation-process-overview"></a>Översikt över hemtransport

Processen hemtransport återgår programmet och dess databaser till dess ursprungliga region efter ett avbrott har åtgärdats.

![GEO-återställning hemtransport](media/saas-dbpertenant-dr-geo-restore/geo-restore-repatriation.png) 

Processen:

1. Stoppar alla pågående återställning aktiviteter och avbryter alla begäranden för återställning av utestående eller pågående databas.

2. Reaktiverar i de ursprungliga region klientdatabaser som inte har ändrats sedan avbrottet. Dessa databaser är bland de inte ännu har återställts och de återställs, men inte ändrats efteråt. Återaktiverade databaserna exakt senast används av sina klienter.

3. Etablerar en spegelbild av den nya innehavaren server och elastisk pool i den ursprungliga regionen. När den här åtgärden är klar uppdateras det nya klient-aliaset för att peka på den här servern. Uppdaterar aliaset som gör att den nya klienten onboarding kan förekomma i den ursprungliga regionen i stället för återställningsregionen.

3. Använder geo-replikering för att flytta katalogen till den ursprungliga regionen från regionen som recovery.

4. Uppdaterar konfigurationen för lagringspooler i den ursprungliga regionen så att den är konsekvent med ändringar som gjorts i återställningsregionen under avbrottet.

5. Skapar de servrar som krävs och pooler för att vara värd för nya databaser som skapas under avbrottet.

6. Använder geo-replikering till hem återställt klienten efter återställning av databaser som har uppdaterats och alla nya klientdatabaser som tillhandahålls under avbrottet. 

7. Rensar upp resurserna som du skapade i återställningsregionen under återställningsprocessen.

För att begränsa antalet klientdatabaser som måste vara återföras, utförs steg 1 till 3 omedelbart.  

Steg 4 görs bara om katalogen i återställningsregionen har ändrats under avbrottet. Katalogen uppdateras om nya klienter skapas eller om någon databas eller pool-konfiguration har ändrats i återställningsregionen.

Det är viktigt att steg 7 orsakar störningar för klienter och inga data går förlorade. För att åstadkomma detta använder processen geo-replikering.

Innan varje databas är geo-replikerade, tas motsvarande databasen i den ursprungliga regionen bort. Databasen i återställningsregionen är sedan geo-replikerade, skapa en sekundär replik i den ursprungliga regionen. När replikeringen är klar, markeras klienten offline i katalogen, som delar alla anslutningar till databasen i återställningsregionen. Databasen sedan växlas över, vilket gör eventuella väntande transaktioner att bearbeta på sekundärt så inga data går förlorade. 

Vid redundansväxlingen återförs databasroller. Sekundär i den ursprungliga regionen blir den primära skrivskyddade-databasen och databasen i återställningsregionen blir en skrivskyddad sekundär. Posten klient i katalogen uppdateras för att referera till databasen i den ursprungliga regionen och klienten har markerats online. Hemtransport av databasen är nu klar. 

Program ska skrivas med logik för omprövning så att de automatiskt återansluta när anslutningar bryts. När de använder katalogen mäkla återanslutning kan ansluta de till repatriated databasen i den ursprungliga regionen. Även om kort frånkopplingen är ofta inte har upptäckt, kan du välja att hem databaser utanför arbetstid.

När en databas är återföras kan den sekundära databasen i återställningsregionen tas bort. Databasen i den ursprungliga regionen sedan beroende igen geo-återställning för skydd för Katastrofåterställning.

Resurser i återställningsregionen, inklusive återställning servrar och pooler, tas bort i steg 8.

## <a name="run-the-repatriation-script"></a>Kör skriptet hemtransport
Låt oss anta avbrottet åtgärdats och kör skriptet hemtransport.

Om du har följt självstudierna reaktiverar Fabrikam Jazzklubb och skogskornell Dojo i regionen ursprungliga omedelbart i skriptet eftersom de är oförändrade. Sedan repatriates den nya innehavaren Hawthorn Hall och Contosos Konserthall eftersom den har ändrats. Skriptet repatriates också den katalogen som har uppdaterats när Hawthorn Hall etablerades.
  
1. I PowerShell ISE i ...\Learning Modules\Business affärskontinuitet och Haveriberedskap Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 skript, kontrollerar du att synkroniseringen för katalogen fortfarande körs i en PowerShell-instans. Om det behövs kan du starta om den genom att ange:

    $DemoScenario = 1: Starta synkroniseringen klientservern, pooler och konfigurationsinformation för databasen i katalogen.

    Tryck på F5 för att köra skriptet.

2.  Om du vill börja hemtransport, ange:

    $DemoScenario = 5: Hem appen till dess ursprungliga region.

    Om du vill köra skriptet för återställning i ett nytt PowerShell-fönster, tryck på F5. Hemtransport tar några minuter och kan övervakas i PowerShell-fönstret.

3. När skriptet körs, uppdatera hubbsidan händelser (http://events.wingtip-dpt.&lt; användare&gt;. trafficmanager.net).

    Observera att alla klienter är online och tillgängliga under hela processen.

4. Välj Fabrikam Jazzklubb att öppna den. Om du inte ändrar den här klienten, Observera från sidfoten att servern har redan återställts till den ursprungliga servern.

5. Öppnar eller uppdaterar sidan Contosos Konserthall händelser. Observera att, till en början databasen är fortfarande på - återställningsservern från sidfoten. 

6. Uppdatera sidan för Contosos Konserthall händelser när hemtransport processen är klar och Lägg märke till att databasen är nu i din ursprungliga region.

7. Uppdatera evenemangshubben igen och öppna Hawthorn Hall. Observera att sin databas finns också i den ursprungliga regionen. 

## <a name="clean-up-recovery-region-resources-after-repatriation"></a>Rensa recovery region resurser efter hemtransport
När hemtransport är klar, är det säkert att ta bort resurser i återställningsregionen. 

> [!IMPORTANT]
> Ta bort de här resurserna direkt om du vill stoppa all fakturering för dessa.

Återställningsprocessen skapar alla resurser för återställning i en resursgrupp för återställning. Rensa igen tar du bort den här resursgruppen och tar bort alla referenser till resurser från katalogen. 

1. I PowerShell ISE i ...\Learning Modules\Business affärskontinuitet och Haveriberedskap Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 skriptet, ange:
    
    $DemoScenario = 6: Ta bort föråldrade resurser från regionen som recovery.

2. Tryck på F5 för att köra skriptet.

När du rensar skripten är programmet tillbaka där den startats. Du kan nu köra skriptet igen eller prova andra självstudier.

## <a name="designing-the-application-to-ensure-that-the-app-and-the-database-are-co-located"></a>Designa programmet så att appen och databasen är samordnade 
Programmet har utformats för att alltid ansluta från en instans i samma region som klientorganisationens databas. Den här designen minskar svarstider mellan programmet och databasen. Denna optimering antar att interaktionen app att databasen är chattier än användaren att appen interaktion.  

Klientdatabaser kan spridas till återställning och ursprungliga regioner under en viss tid under hemtransport. För varje databas letar appen du upp den region där databasen finns genom att göra en DNS-sökning på namnet på klienten. I SQL-databas är namnet på ett alias. Namnet på ett alias innehåller namnet på region. Om programmet inte finns i samma region som databasen, omdirigerar den till instansen i samma region som databasserver. Omdirigera till instans i samma region som databasen minimerar fördröjning mellan appen och databasen.  

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:
> [!div class="checklist"]

>* Använda klient-katalogen ska innehålla med jämna mellanrum konfigurationsinformation, vilket gör att en spegelbild Återställningsmiljö som ska skapas i en annan region.
>* Återställa Azure SQL-databaser i återställningsregionen genom att använda geo-återställning.
>* Uppdatera klientkatalog för att återspegla återställda klient databasplatser. 
>* Använda en DNS-alias för att aktivera ett program för att ansluta till klientkatalog i hela utan omkonfiguration.
>* Använda geo-replikering till hem återställda databaser till deras ursprungliga region efter ett avbrott har åtgärdats.

Prova den [haveriberedskap för en multitenant SaaS-program med hjälp av database geo-replikering](saas-dbpertenant-dr-geo-replication.md) självstudiekurs och lär dig hur du använder geo-replikering att avsevärt minska den tid som krävs för att återställa ett storskaligt program för flera innehavare.

## <a name="additional-resources"></a>Ytterligare resurser

[Ytterligare självstudier som bygger på Wingtip SaaS-program](https://docs.microsoft.com/azure/sql-database/sql-database-wtp-overview#sql-database-wingtip-saas-tutorials)
