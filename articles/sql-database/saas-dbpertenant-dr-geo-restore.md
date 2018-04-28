---
title: Använda Azure SQL Database geo-redundant säkerhetskopior för katastrofåterställning av SaaS-appar | Microsoft Docs
description: Lär dig hur du använder Azure SQL Database geo-redundant säkerhetskopieringar för att återställa en multitenant SaaS-app vid ett avbrott
keywords: sql database tutorial
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: saas apps
ms.topic: article
ms.date: 04/16/2018
ms.author: ayolubek
ms.openlocfilehash: 8fd25e13f6796b8be99ad3efd425bcde7bca3905
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="use-geo-restore-to-recover-a-multitenant-saas-application-from-database-backups"></a>Använda geo-återställning för att återställa en multitenant SaaS-program från säkerhetskopiorna av databasen

Den här självstudiekursen utforskar en fullständig katastrofåterställning för en multitenant SaaS-program som har implementerats med databasen per klient modell. Du använder [geo-återställning](https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups) återställer katalog och klient databaser från automatiskt behålla geo-redundant säkerhetskopior till en annan återställning region. När avbrottet är löst kan du använda [georeplikering](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview) till hem ändrade databaser till deras ursprungliga region.

![GEO-återställning-arkitektur](media/saas-dbpertenant-dr-geo-restore/geo-restore-architecture.png)

GEO-återställning är lägst kostnad haveriberedskapslösning för Azure SQL Database. Återställning från säkerhetskopior av geo-redundant kan dock leda till förlust av data i upp till en timme. Det kan ta lång tid, beroende på storleken på varje databas. 

> [!NOTE]
> Återställa program med lägsta möjliga Återställningspunktmål och Återställningstidsmål med geo-replikering i stället för geo-återställning.

Den här självstudiekursen utforskar både återställning och hemtransport arbetsflöden. Lär dig att:
> [!div class="checklist"]

>* Synkronisera databasen och konfigurationsinformation för elastiska poolen till klient-katalogen.
>* Skapa en miljö med speglade bilden i en region som recovery som innehåller program, servrar och pooler.   
>* Återställa katalogen och klienten med hjälp av geo-återställning.
>* Använd geo-replikering till hem klient katalog och ändrade klient databaser när driftstörningarna har åtgärdats.
>* Uppdatera katalogen som varje databas har återställts (eller återföras) att spåra den aktuella platsen för den aktiva kopian av databasen för varje klient.
>* Se till att programmet och klient-databasen är alltid samordnad i samma Azure-region att minska svarstiden. 
 

Innan du börjar den här självstudiekursen måste uppfylla följande krav:
* Distribuera Wingtip biljetter SaaS-databasen per klient app. För att distribuera på mindre än fem minuter finns [distribuera och utforska Wingtip biljetter SaaS-databasen per klient program](saas-dbpertenant-get-started-deploy.md). 
* Installera Azure PowerShell. Mer information finns i [komma igång med Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-geo-restore-recovery-pattern"></a>Introduktion till recovery mönstret geo-återställning

Katastrofåterställning (DR) är viktigt för många program för kompatibilitetsskäl eller kontinuitet för företag. Om det finns en långvariga avbrott, kan en väl förberedd DR-plan minimera avbrott i verksamheten. En DR-plan utifrån geo-återställning måste utföra flera mål:
 * Reservera alla nödvändiga kapaciteten i den valda återställningsplatsen regionen så snabbt som möjligt så att den ska kunna återställa databaser för innehavare.
 * Upprätta en spegling Återställningsmiljö som motsvarar den ursprungliga pool och databas-konfigurationen. 
 * Tillåt annullering av återställningsprocessen halva rör sig om den ursprungliga regionen är online igen.
 * Aktivera klientorganisationens Etableringsläge snabbt så att den nya innehavaren onboarding kan starta om så snart som möjligt.
 * Optimeras för att återställa hyresgäster i prioritetsordning.
 * Optimeras för att hämta innehavare online så snart som möjligt genom att göra steg parallellt där praktiska.
 * Bli motståndskraftiga mot fel, kan startas om, och idempotent.
 * Hem databaser till deras ursprungliga region med minimal påverkan på klienter när driftstörningarna har åtgärdats.  

> [!NOTE]
> Programmet återställs till parad region för den region där programmet har distribuerats. Mer information finns i [Azure länkas regioner](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).   

Den här kursen använder funktioner i Azure SQL Database och Azure-plattformen för att åtgärda dessa problem:

* [Azure Resource Manager-mallar](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template)att reservera alla nödvändiga kapacitet så snabbt som möjligt. Azure Resource Manager-mallar används för att etablera en speglad bild av den ursprungliga servrar och elastiska pooler i området för återställning. En separat server och poolen skapas också för att etablera nya klienter.
* [Klientbibliotek för elastisk databas](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-database-client-library) (EDCL) att skapa och upprätthålla en katalog för klient-databasen. Utökade katalogen innehåller med jämna mellanrum konfigurationsinformation för poolen och databasen.
* [Fragmentera hanteringsfunktioner recovery](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-database-recovery-manager) av EDCL att underhålla plats databasposter i katalogen under återställning och hemtransport.  
* [GEO-återställning](https://docs.microsoft.com/azure/sql-database/sql-database-disaster-recovery), för att återställa databaserna katalog och klienten från automatiskt behålla geo-redundant säkerhetskopior. 
* [Asynkron återställningsåtgärderna](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations), skickas i prioritetsordning för klienten, är i kö för varje pool av systemet och behandlas i batchar så poolen inte är överbelastad. Dessa åtgärder kan avbrytas innan eller under körning om det behövs.   
* [GEO-replikering](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview)att hem databaser till den ursprungliga regionen efter avbrottet. Det finns inga data går förlorade och minimal inverkan på klienten när du använder geo-replikering.
* [SQL server-DNS-alias](https://docs.microsoft.com/azure/sql-database/dns-alias-overview), så att synkroniseringen katalog att ansluta till den aktiva katalogen oavsett dess plats.  

## <a name="get-the-disaster-recovery-scripts"></a>Hämta disaster recovery-skript

DR-skript som används i den här kursen är tillgängliga i den [Wingtip biljetter SaaS databas per klient GitHub-lagringsplatsen](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant). Kolla in den [allmänna riktlinjer](saas-tenancy-wingtip-app-guidance-tips.md) steg för att ladda ned och avblockera hanteringsskript Wingtip biljetter.

> [!IMPORTANT]
> Precis som alla Wingtip biljetter management skripten DR-skript är exempel kvalitet och ska inte användas i produktionen.

## <a name="review-the-healthy-state-of-the-application"></a>Granska felfri statusen för programmet
Granska den normala hälsotillstånd för programmet innan du startar återställningsprocessen.

1. Öppna i webbläsaren, Wingtip biljetter händelser navet (http://events.wingtip-dpt.&lt; användaren&gt;. trafficmanager.net, Ersätt &lt;användaren&gt; med din distribution användaren värde).
    
   Bläddra till längst ned på sidan och Lägg märke till katalogen servernamn och en plats i sidfoten. Platsen är den region där du har distribuerat appen.    

   > [!TIP]
   > Pekar på plats för att visa.

   ![Händelser hubb felfritt tillstånd i ursprungliga region](media/saas-dbpertenant-dr-geo-restore/events-hub-original-region.png)

2. Välj Contoso samklang Hall innehavaren och öppna appens sida för händelsen.

   Observera klientens servernamnet i sidfoten. Platsen är samma som den katalogservern plats.

   ![Contoso samklang Hall ursprungliga region](media/saas-dbpertenant-dr-geo-restore/contoso-original-location.png) 

3. I den [Azure-portalen](https://portal.azure.com), granska och öppna den resursgrupp som du har distribuerat appen.

   Lägg märke till resurserna och den region där appen tjänstkomponenter och SQL Database-servrar distribueras.

## <a name="sync-the-tenant-configuration-into-the-catalog"></a>Synkronisera klientkonfiguration till katalogen

I den här uppgiften kan du starta en process för att synkronisera konfigurationen av servrar, elastiska pooler och databaser till klient-katalogen. Den här informationen används senare för att konfigurera en spegling i området för återställning.

> [!IMPORTANT]
> För enkelhetens skull implementeras synkroniseringen och andra långvariga återställning och hemtransport processer i exemplen som lokala PowerShell jobb eller sessioner som körs under din klient användarinloggning. Autentiseringstoken som utfärdas när du loggar in upphör efter flera timmar och sedan jobben misslyckas. I ett produktionsscenario med bör tidskrävande processer implementeras som tillförlitliga Azure-tjänster av något slag, körs under ett huvudnamn för tjänsten. Se [Använd Azure PowerShell för att skapa ett huvudnamn för tjänsten med ett certifikat](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal). 

1. Öppna filen ...\Learning Modules\UserConfig.psm1 i PowerShell ISE. Ersätt `<resourcegroup>` och `<user>` på rader 10 och 11 med det värde som används när du har distribuerat appen. Spara filen.

2. Öppna ...\Learning Modules\Business affärskontinuitet och Haveriberedskap Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 skriptet i PowerShell ISE.

    I kursen får du köra var och en av scenarierna i detta PowerShell-skript, så spara den här filen öppen.

3. Ange följande:

    $DemoScenario = 1: starta bakgrunden som synkroniseras klient-server och konfigurationsinformation för lagringspool till katalogen.

4. Välj F5 för att köra skriptet synkronisering. 

    Den här informationen används senare så att återställningen skapas en speglad bild av servrar, pooler och databaser i området för återställning.  

    ![Synkroniseringen](media/saas-dbpertenant-dr-geo-restore/sync-process.png)

Lämna fönstret PowerShell körs i bakgrunden och fortsätt med resten av den här kursen.

> [!NOTE]
> Synkroniseringen ansluter till programkatalogen via ett DNS-alias. Aliaset har ändrats under återställning och hemtransport att peka till den aktiva katalogen. Synkroniseringen håller katalogen aktuella databasen eller adresspool configuration ändringar som gjorts i området för återställning. Under hemtransport tillämpas ändringarna på motsvarande resurser i den ursprungliga regionen.

## <a name="geo-restore-recovery-process-overview"></a>Översikt över processen för GEO-återställning återställning

Geo-återställning återställningsprocessen distribuerar programmet och återställer databaser från säkerhetskopior i området för återställning.

Återställningsprocessen gör följande:

1. Inaktiverar Azure Traffic Manager-slutpunkten för webbappen i den ursprungliga regionen. Inaktivera slutpunkten hindrar användare från att ansluta till appen i ett ogiltigt tillstånd bör den ursprungliga regionen anslutas under återställningen.

2. Tillhandahåller en återställning katalogen servern i området recovery geo återställer katalogdatabasen och uppdaterar activecatalog alias så att den pekar till den återställda katalogservern. Ändra katalogen alias säkerställer att synkroniseringen för katalogen alltid synkroniseras till den aktiva katalogen.

3. Markerar alla befintliga klienter i katalogen återställning som offline om du vill förhindra åtkomst till klient databaser innan de återställs.

4. Etablerar en instans av appen i området för återställning och konfigurerar den för att använda den återställda katalogen i den regionen. Om du vill behålla fördröjning till minst är sample-appen avsedd att alltid ansluta till en klient-databas i samma region.

5. Etablerar en server och elastisk pool som nya klienter har etablerats. Skapa dessa resurser ser du till att etablera nya klienter inte stör återställning av befintliga klienter.

6. Uppdaterar aliaset klient så att den pekar till servern för den nya innehavaren databaser i området för återställning. Ändra det här aliaset säkerställer att databaser för alla nya klienter har etablerats i området för återställning.
        
7. Etablerar servrar och elastiska pooler i området återställning för att återställa databaser för innehavare. Dessa servrar och pooler är en speglad bild av konfigurationen i den ursprungliga regionen. Etablering pooler direkt reserverar kapacitet som krävs för att återställa alla databaser.

    Ett avbrott i en region kan placera stor belastning på de tillgängliga resurserna i parad region. Reservera resurser snabbt rekommenderas om du förlita dig på geo-återställning för Katastrofåterställning. Överväg att geo-replikering om det är viktigt att ett program återställs i en viss region. 

8. Aktiverar Traffic Manager-slutpunkten för webbappen i området för återställning. Om du aktiverar den här slutpunkten kan programmet för att etablera nya klienter. Befintliga klienter är fortfarande offline i det här skedet.

9. Skickar batchar av förfrågningar om att återställa databaser i prioritetsordning. 

    * Batchar ordnas så att återställa databaser parallellt över alla pooler.  

    * Restore-begäranden skickas asynkront så att de har skickats snabbt och i kö för körning i varje pool.

    * Eftersom återställning begäranden bearbetas parallellt i alla pooler, är det bättre att distribuera viktiga hyresgäster i många pooler. 

10. Övervakar SQL Database-tjänsten för att avgöra när databaser återställs. När en klient-databas har återställts kan den markeras online i katalogen och rowversion summan för klient-databas registreras. 

    * Klient-databaser kan nås av programmet så snart de har markerats online i katalogen.

    * Summan av rowversion värden i klient-databasen lagras i katalogen. Den här summan fungerar som ett fingeravtryck som tillåter hemtransport processen för att avgöra om databasen har uppdaterats i området för återställning.       

## <a name="run-the-recovery-script"></a>Kör skriptet för återställning

> [!IMPORTANT]
> Den här självstudiekursen återställer databaser från geo-redundant säkerhetskopior. Även om dessa säkerhetskopior är normalt tillgängliga inom 10 minuter kan ta det upp till en timme. Skriptet pausas tills de är tillgängliga.

Anta att det finns ett avbrott i den region där programmet distribueras, och kör skriptet återställning:

1. Ange följande värde i ...\Learning Modules\Business affärskontinuitet och Haveriberedskap Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 skriptet i PowerShell ISE:

    $DemoScenario = 2: återställa appen i en region som recovery genom att återställa från geo-redundant säkerhetskopior.

2. Välj F5 för att köra skriptet.  

    * Skriptet öppnas i ett nytt PowerShell-fönster och startar sedan en uppsättning PowerShell-jobb som körs parallellt. Dessa jobb återställa servrar, pooler och databaserna till regionen som recovery.

    * Återställning region är parad region som är associerade med Azure-regionen där du distribuerade programmet. Mer information finns i [Azure länkas regioner](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

3. Övervaka status för återställningsprocessen i PowerShell-fönstret.

    ![Återställningsprocessen](media/saas-dbpertenant-dr-geo-restore/dr-in-progress.png)

> [!NOTE]
> Granska PowerShell-skript i mappen ...\Learning Modules\Business affärskontinuitet och Haveriberedskap Recovery\DR-RestoreFromBackup\RecoveryJobs om du vill utforska koden för återställningsjobben.

## <a name="review-the-application-state-during-recovery"></a>Granska programmets tillstånd under återställningen
Programmet är inte tillgänglig medan programmet slutpunkten är inaktiverad i Traffic Manager. Katalogen har återställts och alla klienter markeras offline. Programslutpunkten i området för återställning sedan är aktiverad och programmet är online igen. Även om programmet är tillgängligt, klienter verkar vara offline i händelser hubb tills deras databaser har återställts. Det är viktigt att utforma ditt program för att hantera offline klient databaser.

* Databasen har återställts efter katalogen men innan klienterna är tillbaka online, uppdatera Wingtip biljetter händelser hubben i webbläsaren.

    * I sidfoten, Lägg märke till att namnet på katalogen har nu - återställning suffix och finns i området för återställning.

    * Observera att klienter som inte har återställts är markerad som offline och inte är valbara.   
 
    ![Återställningsprocessen](media/saas-dbpertenant-dr-geo-restore/events-hub-tenants-offline-in-recovery-region.png)    

    * Om du öppnar en klients händelser sida direkt medan klienten är nedkopplad, visar sidan ett offline klient-meddelande. Till exempel om Contoso samklang Hall är offline försöker öppna http://events.wingtip-dpt.&lt; användaren&gt;.trafficmanager.net/contosoconcerthall.

    ![Återställningsprocessen](media/saas-dbpertenant-dr-geo-restore/dr-in-progress-offline-contosoconcerthall.png)

## <a name="provision-a-new-tenant-in-the-recovery-region"></a>Etablera en ny klient i området för återställning
Innan klienten databaser återställs, kan du etablera nya klienter i området för återställning. Den nya innehavaren databaser etableras i området recovery återföras senare med återställda databaser.   

1. Ange följande egenskap i ...\Learning Modules\Business affärskontinuitet och Haveriberedskap Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 skriptet i PowerShell ISE:

    $DemoScenario = 3: etablera en ny klient i området för återställning.

2. Välj F5 för att köra skriptet.

3. Sidan Hawthorn Hall händelser öppnas i webbläsaren när etableringen är slutförd. 

    Observera att Hawthorn Hall databasen finns i området för återställning.

    ![Hawthorn Hall etableras i området för återställning](media/saas-dbpertenant-dr-geo-restore/hawthorn-hall-provisioned-in-recovery-region.png)

4. Uppdatera sidan Wingtip biljetter händelser hubb för att se Hawthorn Hall ingår i webbläsaren. 

    Om du har etablerat Hawthorn Hall utan att vänta på andra hyresgäster att återställa det andra klienter kan fortfarande vara offline.

## <a name="review-the-recovered-state-of-the-application"></a>Granska återställda statusen för programmet

När återställningen är klar är programmet och alla klienter helt funktionella i området för återställning. 

1. När visas i PowerShell-konsolfönster visar alla klienter har återställts, uppdatera händelser hubben. 

    Alla hyresgäster visas online, inklusive den nya innehavaren Hawthorn Hall.

    ![återställda och nya klienter i hubben händelser](media/saas-dbpertenant-dr-geo-restore/events-hub-with-hawthorn-hall.png)

2. Klicka på Contoso samklang Hall och öppna appens sida händelser. 

    Observera att databasen finns på återställningsservern finns i regionen som recovery i sidfoten.

    ![Contoso i området för återställning](media/saas-dbpertenant-dr-geo-restore/contoso-recovery-location.png)

3. I den [Azure-portalen](https://portal.azure.com), öppna listan över resursgrupper.  

    Lägg märke till den resursgrupp som du distribuerat, plus den recovery resursgrupp, med suffixet - återställning. Resursgruppen återställning innehåller alla resurser som skapades under återställningen plus nya resurser som skapades under avbrottet. 

4. Öppna resursgruppen återställning och Observera följande:

    * Återställning versioner av katalogen och tenants1 servrar, med suffixet - återställning. Återställda katalog och klient databaserna på dessa servrar som alla har namn som används i den ursprungliga regionen.

    * Tenants2-dpt -&lt;användaren&gt;-återställning SQLServer. Den här servern används för att etablera nya klienter under avbrottet.

    * App service med namnet händelser-wingtip-dpt -&lt;recoveryregion&gt;-&lt;användaren&gt;, vilket är återställning instans av appen händelser.

    ![Contoso resurser i området för återställning](media/saas-dbpertenant-dr-geo-restore/resources-in-recovery-region.png) 
    
5. Öppna tenants2-dpt -&lt;användaren&gt;-återställning SQLServer. Observera att den innehåller hawthornhall för databasen och den elastiska poolen Pool1. Hawthornhall databasen är konfigurerad som en elastisk databas i den elastiska poolen Pool1.

## <a name="change-the-tenant-data"></a>Ändra klientdata 
Du uppdaterar en av databaserna återställda klient i den här uppgiften. Hemtransport processen kopior återställt databaserna som har ändrats till den ursprungliga regionen. 

1. Hitta händelselistan för Contoso samklang Hall i webbläsaren, bläddra igenom händelser och Observera den sista händelsen allvarligt Strauss.

2. Ange följande värde i ...\Learning Modules\Business affärskontinuitet och Haveriberedskap Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 skriptet i PowerShell ISE:

    $DemoScenario = 4: ta bort en händelse från en klient i området för återställning.

3. Välj F5 för att köra skriptet.

4. Uppdatera sidan Contoso samklang Hall händelser (http://events.wingtip-dpt.&lt; användaren&gt;.trafficmanager.net/contosoconcerthall), och Lägg märke till att händelsen allvarligt Strauss saknas.

Du har nu återställt som körs nu i området för återställning i självstudierna. Du har etablerat en ny klient i området för återställning och ändrade data på en av de återställda innehavarna.  

> [!NOTE]
> Andra kurser i exemplet är inte avsedd att köras med appen i tillståndet återställning. Om du vill utforska andra självstudiekurser måste du först hem programmet.

## <a name="repatriation-process-overview"></a>Översikt över processen för hemtransport

Hemtransport processen återställs programmet och dess databaser till dess ursprungliga region efter ett avbrott har lösts.

![GEO-återställning hemtransport](media/saas-dbpertenant-dr-geo-restore/geo-restore-repatriation.png) 

Processen:

1. Stoppar alla aktiviteter som en pågående återställning och avbryter alla begäranden om återställning utestående eller relä databas.

2. Reaktiverar i ursprungliga region klient-databaser som inte har ändrats sedan avbrottet. Databaserna är de som inte återställts än och de återställs, men inte ändrats efteråt. Återaktiverade databaserna exakt senast via sina klienter.

3. Etablerar en speglad bild av den nya innehavaren server och elastisk pool i den ursprungliga regionen. När den här åtgärden är klar uppdateras aliaset klient för att peka på den här servern. Uppdatera alias gör ny klient onboarding ska ske i den ursprungliga regionen i stället för återställning-region.

3. Använder geo-replikering för att flytta katalogen till den ursprungliga regionen från området för återställning.

4. Uppdaterar konfigurationen för lagringspooler i den ursprungliga regionen så att den är konsekvent med ändringar som gjorts i området för återställning under avbrottet.

5. Skapar servrar som krävs och pooler som värd för alla nya databaser som skapades under avbrottet.

6. Använder geo-replikering till hem återställts klient databaser som har uppdaterats efter återställning och alla nya klient databaser etablerats under avbrottet. 

7. Rensar upp resurser som skapades i området för återställning under återställningsprocessen.

Om du vill begränsa antalet databaser som klienten som måste vara återföras utfärdat steg 1 till 3 omedelbart.  

Steg 4 görs bara om katalogen i recovery region har ändrats under avbrottet. Katalogen uppdateras om skapas nya klienter eller om någon databas eller pool konfiguration ändras i området för återställning.

Det är viktigt att steg 7 förlorar minimala störningar klienter och inga data går förlorade. För att åstadkomma detta använder processen geo-replikering.

Innan varje databas är georeplikerad, motsvarande databas i den ursprungliga regionen har tagits bort. Databasen i området för återställning är georeplikerad, skapa en sekundär replik i den ursprungliga regionen. När replikeringen är klar, markeras klienten offline i katalogen, vilket bryter alla anslutningar till databasen i området för återställning. Databasen sedan växlas över, orsakar väntande transaktioner bearbeta på sekundärt så att inga data går förlorade. 

Vid redundans återförs databasroller. Sekundär i den ursprungliga regionen blir den primära databasen skrivskyddad och databasen i området för återställning blir en skrivskyddad sekundär. Klient-post i katalogen uppdateras för att referera till databasen i den ursprungliga regionen och innehavaren markeras online. Hemtransport av databasen är nu klar. 

Program ska skrivas med logik så att de återupptas automatiskt när anslutningar bryts. När de använder katalogen till broker återanslutning ansluter de till repatriated databasen i den ursprungliga regionen. Även om kort frånkoppling är ofta inte tänkt på att kan du välja att hem databaser utanför arbetstid.

När en databas är återföras kan den sekundära databasen i området för återställning tas bort. Databasen i den ursprungliga regionen sedan beroende igen geo-återställning för DR-skydd.

Resurser i regionen återställning, inklusive återställning servrar och pooler, tas bort i steg 8.

## <a name="run-the-repatriation-script"></a>Kör skriptet hemtransport
Vi anta avbrottet åtgärdats och kör skriptet hemtransport.

Om du har följt kursen reaktiverar Fabrikam Jazz en och skogskornell Dojo i den ursprungliga regionen omedelbart i skriptet eftersom de är oförändrade. Sedan repatriates den nya innehavaren Hawthorn Hall och Contoso samklang Hall eftersom den har ändrats. Skriptet repatriates även den katalogen som har uppdaterats när Hawthorn Hall har etablerats.
  
1. Kontrollera att katalogen synkroniseringen körs fortfarande i dess PowerShell-instansen i PowerShell ISE i ...\Learning Modules\Business affärskontinuitet och Haveriberedskap Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 skript. Om det behövs kan du starta om den genom att ange:

    $DemoScenario = 1: starta synkroniseringen klient-server, pool och konfigurationsinformation för databasen till katalogen.

    Välj F5 för att köra skriptet.

2.  Om du vill starta processen hemtransport, ange:

    $DemoScenario = 5: Hem appen till dess ursprungliga region.

    Välj F5 för att köra skriptet för återställning i ett nytt PowerShell-fönster. Hemtransport tar flera minuter och kan övervakas i PowerShell-fönstret.

3. När skriptet körs, uppdatera sidan händelser hubb (http://events.wingtip-dpt.&lt; användaren&gt;. trafficmanager.net).

    Observera att alla klienter är online och tillgängliga hela processen.

4. Välj Fabrikam Jazz en så att den öppnas. Om du inte ändrar den här innehavaren meddelande från sidfoten att servern är redan har återställts till den ursprungliga servern.

5. Öppna eller uppdatera sidan Contoso samklang Hall händelser. Observera att, från början, databasen är fortfarande på - återställningsservern från sidfoten. 

6. Uppdatera sidan Contoso samklang Hall händelser när hemtransport processen är klar och Lägg märke till att databasen är nu i din ursprungliga region.

7. Uppdatera händelser navet igen och öppna Hawthorn Hall. Lägg märke till dess databas finns också i det ursprungliga regionen. 

## <a name="clean-up-recovery-region-resources-after-repatriation"></a>Rensa recovery region resurser efter hemtransport
Det är säkert att ta bort resurser i området för återställning när hemtransport är klar. 

> [!IMPORTANT]
> Ta bort resurserna omgående om du vill stoppa alla fakturering för dessa.

Återställningsprocessen skapar recovery resurser i en resursgrupp för återställning. Rensningen tar bort den här resursgruppen och tar bort alla referenser till resurser från katalogen. 

1. I PowerShell ISE i ...\Learning Modules\Business affärskontinuitet och Haveriberedskap Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 skriptet, ange:
    
    $DemoScenario = 6: ta bort föråldrade resurser från regionen som recovery.

2. Välj F5 för att köra skriptet.

När du rensar skripten är programmet tillbaka där den har startats. Du kan nu köra skriptet igen eller prova att använda andra självstudier.

## <a name="designing-the-application-to-ensure-that-the-app-and-the-database-are-co-located"></a>Utforma program så att appen och databasen är samordnade 
Programmet är avsedd att alltid ansluter från en instans i samma region som klientens databas. Den här designen minskar Nätverksfördröjningen mellan programmet och databasen. Denna optimering förutsätter interaktionen app att databasen är chattier än användaren att appen interaktion.  

Klient-databaser kan spridas över återställning och ursprungliga regioner för någon gång under hemtransport. För varje databas letar appen upp den region där databasen finns genom att göra en DNS-sökning på klient-servernamn. Servernamnet är ett alias i SQL-databas. Servernamnet alias innehåller regionsnamnet. Om programmet inte finns i samma region som databasen, dirigerar den till instansen i samma region som databasserver. Omdirigera till instansen i samma region som databas minimerar fördröjning mellan appen och databasen.  

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:
> [!div class="checklist"]

>* Använda klient-katalogen för att rymma med jämna mellanrum konfigurationsinformation, vilket gör att en spegling Återställningsmiljö skapas i en annan region.
>* Återställa Azure SQL-databaser till recovery region med geo-återställning.
>* Uppdatera klienten katalogen för att återspegla återställda klient databasplatser. 
>* Använd ett DNS-alias för att aktivera ett program ansluta till klient-katalogen under hela utan omkonfiguration.
>* Använd geo-replikering till hem återställda databaser till deras ursprungliga region efter ett avbrott har stängts.

Försök i [katastrofåterställning för en multitenant SaaS-program med hjälp av database geo-replikering](saas-dbpertenant-dr-geo-replication.md) kursen för att lära dig hur du använder geo-replikering för att avsevärt minska den tid som krävs för att återställa ett storskaligt flera program.

## <a name="additional-resources"></a>Ytterligare resurser

[Ytterligare självstudier som bygger på Wingtip SaaS-program](https://docs.microsoft.com/azure/sql-database/sql-database-wtp-overview#sql-database-wingtip-saas-tutorials)
