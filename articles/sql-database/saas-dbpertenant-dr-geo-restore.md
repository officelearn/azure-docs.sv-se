---
title: Haveriberedskap för SaaS-appar med hjälp av Azure SQL Database geo-redundant säkerhetskopior | Microsoft Docs
description: Lär dig hur du använder Azure SQL Database geo-redundant säkerhetskopieringar för att återställa en app för flera innehavare SaaS vid ett avbrott
keywords: sql database tutorial
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: saas apps
ms.topic: article
ms.date: 04/09/2018
ms.author: ayolubek
ms.openlocfilehash: 928ff9b2519f6e92b091d9fd6b4ea0a7bca94d2b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="recover-a-multi-tenant-saas-application-using-geo-restore-from-database-backups"></a>Återställ en flera innehavare SaaS-program som använder geo-återställning från säkerhetskopior

I kursen får utforska du en fullständig katastrofåterställning för ett SaaS-program för flera innehavare som implementeras med hjälp av databasen per klient-modellen. Du använder [ _geo-återställning_ ](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-recovery-using-backups) återställer katalog och klient databaser från automatiskt behålla geo-redundant säkerhetskopior till en annan återställning region. När avbrottet är löst kan du använda [ _georeplikering_ ](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-geo-replication-overview) till hem ändrade databaser till deras ursprungliga region.

![GEO-återställning-arkitektur](media/saas-dbpertenant-dr-geo-restore/geo-restore-architecture.png)

GEO-återställning är lägst kostnad haveriberedskapslösning för SQL-databas.  Dock återställa från geo-redundant säkerhetskopieringar kan leda till förlust av data i upp till en timme, och kan ta lång tid, beroende på storleken på varje databas. **Om du vill återställa program med lägsta möjliga Återställningspunktmål och Återställningstidsmål använder geo-replikering i stället för geo-återställning**.

Den här självstudiekursen utforskar både återställning och hemtransport arbetsflöden. Lär dig att:
> [!div class="checklist"]

>* Synkronisera databasen och elastisk pool konfigurationsinformation till katalogen för klient
>* Konfigurera en spegling miljö i en ”återställning” region som består av program, servrar och pooler    
>* Återställa katalog och klient-databaser med _geo-återställning_
>* Hem klient katalog och ändrade klient databaser med hjälp av _georeplikering_ när driftstörningarna har åtgärdats
>* Uppdatera katalogen som varje databas har återställts (eller återföras) att spåra den aktuella platsen för den aktiva kopian av databasen för varje klient
>* Se till att programmet och klient-databasen är alltid samordnad i samma Azure-region att minska svarstiden  
 

Kontrollera att följande förutsättningar har slutförts innan du påbörjar den här kursen:
* Wingtip biljetter SaaS-databasen per klient appen har distribuerats. För att distribuera på mindre än fem minuter finns [distribuera och utforska Wingtip biljetter SaaS-databasen per klient program](saas-dbpertenant-get-started-deploy.md)  
* Azure PowerShell ska ha installerats. Mer information finns i [Komma igång med Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-the-geo-restore-recovery-pattern"></a>Introduktion till recovery mönstret geo-återställning

Katastrofåterställning (DR) är viktigt för många program för kompatibilitetsskäl eller kontinuitet för företag. Det bör finnas en långvariga avbrott, kan en väl förberedd DR-plan minimera avbrott i verksamheten. En DR-plan utifrån geo-återställning måste utföra flera mål:
 * Reservera alla nödvändiga kapaciteten i den valda återställningsplatsen regionen så snabbt som möjligt för att säkerställa att den ska kunna återställa databaser för innehavare.
 * Upprätta en spegling Återställningsmiljö som motsvarar den ursprungliga konfigurationen för poolen och databas 
 * Det måste vara möjligt att avbryta återställningsprocessen i mitten rör sig om den ursprungliga regionen kommer tillbaka online.
 * Aktivera klientorganisationens Etableringsläge snabbt så att den nya innehavaren onboarding kan starta om så snart som möjligt  
 * Optimeras för att återställa hyresgäster i prioritetsordning
 * Optimeras för att hämta klienter online så snart som möjligt genom att göra steg parallellt där praktiska
 * Bli motståndskraftiga mot fel, kan startas om, och idempotent
 * Hem databaser till deras ursprungliga region med minimal påverkan på klienter när driftstörningarna har åtgärdats.  

> [!Note]
> Programmet återställs till den _parad region_ för den region där programmet har distribuerats. Mer information finns i [Azure länkas regioner](https://docs.microsoft.com/en-us/azure/best-practices-availability-paired-regions).   



I den här kursen behandlas dessa problem med funktionerna i Azure SQL Database och Azure-plattformen:

* [Azure Resource Manager-mallar](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template)att reservera alla nödvändiga kapacitet så snabbt som möjligt. Azure Resource Manager-mallar används för att etablera en speglad bild av den ursprungliga servrar och elastiska pooler i området för återställning. En separat server och poolen skapas också för att etablera nya klienter. 
* [Klientbibliotek för elastisk databas](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-elastic-database-client-library) (EDCL) för att skapa och upprätthålla en katalog för klient-databasen.  Katalogen har utökats för att inkludera regelbundet uppdatera konfigurationsinformation för poolen och databasen.
* [Fragmentera hanteringsfunktioner recovery](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-database-recovery-manager) av EDCL att underhålla plats databasposter i katalogen under återställning och hemtransport.  
* [GEO-återställning](https://docs.microsoft.com/azure/sql-database/sql-database-disaster-recovery), för att återställa databaserna katalog och klienten från automatiskt behålla geo-redundant säkerhetskopior. 
* [Asynkron återställningsåtgärderna](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations) skickas i klient-prioritetsordning, som är i kö för varje pool av systemet och behandlas i batchar så poolen inte är överbelastad. Dessa åtgärder kan avbrytas innan eller under körning om det behövs.    
* [GEO-replikering](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview)att hem databaser till den ursprungliga regionen efter avbrottet. Geo-replikering används garanterar det finns inga data går förlorade och minimal inverkan på klienten.
* [SQL server-DNS-alias](https://docs.microsoft.com/azure/sql-database/dns-alias-overview) så att synkroniseringen katalog att ansluta till den aktiva katalogen oavsett dess plats.  

## <a name="get-the-disaster-recovery--scripts"></a>Hämta disaster recovery-skript 

DR-skript som används i den här kursen är tillgängliga i den [Wingtip biljetter SaaS databas per klient GitHub-lagringsplatsen](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/tree/feature-DR-georestore). Kolla in den [allmänna riktlinjer](saas-tenancy-wingtip-app-guidance-tips.md) steg för att ladda ned och avblockera hanteringsskript Wingtip biljetter.
> [!IMPORTANT]
> Precis som alla Wingtip biljetter management skripten DR-skript är exempel kvalitet och ska inte användas i produktionen.   

## <a name="review-the-healthy-state-of-the-application"></a>Granska felfri statusen för programmet
Granska den normala hälsotillstånd för programmet innan du startar återställningsprocessen.
1. Öppna i webbläsaren, Wingtip biljetter händelser navet (http://events.wingtip-dpt.&lt; användaren&gt;. trafficmanager.net - Ersätt &lt;användaren&gt; med din distribution användaren värde).
    * Bläddra till längst ned på sidan och Lägg märke till katalogen servernamn och en plats i sidfoten.  Platsen är den region där du har distribuerat appen.    
        Tips: markören över plats för att visa.

    ![Händelser hubb felfritt tillstånd i ursprungliga region](media/saas-dbpertenant-dr-geo-restore/events-hub-original-region.png)

1. Klicka på Contoso samklang Hall innehavaren och öppna appens sida för händelsen.
    * Observera hyresgäster servernamnet i sidfoten. Platsen ska vara samma som den katalogservern plats.

    ![Contoso samklang Hall ursprungliga region](media/saas-dbpertenant-dr-geo-restore/contoso-original-location.png)    
1. I den [Azure-portalen](https://portal.azure.com), granska och öppna den resursgrupp som du har distribuerat appen.
    * Lägg märke till resurserna och den region där appen tjänstkomponenter och SQL Database-servrar distribueras.

## <a name="sync-tenant-configuration-into-catalog"></a>Synkronisera klientkonfiguration i katalogen

I den här uppgiften kan du starta en process för att synkronisera konfigurationen av servrar, elastiska pooler och databaser till klient-katalogen.  Den här informationen används senare för att konfigurera en spegling i området för återställning.

> [!IMPORTANT]
> För enkelhetens skull implementeras synkroniseringen och andra långvariga återställning och hemtransport i exemplen som lokala Powershell jobb eller sessioner som körs under din klient användarinloggning. Autentiseringstoken som utfärdas när du loggar in upphör efter flera timmar och sedan jobben misslyckas. I ett produktionsscenario med bör tidskrävande processer implementeras som tillförlitliga Azure-tjänster av något slag, körs under ett huvudnamn för tjänsten. Se [Använd Azure PowerShell för att skapa ett huvudnamn för tjänsten med ett certifikat](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authenticate-service-principal). 

1. I den _PowerShell ISE_, öppna filen ...\Learning Modules\UserConfig.psm1. Ersätt `<resourcegroup>` och `<user>` på rader 10 och 11 med det värde som används när du har distribuerat appen.  Spara filen!

2. I den *PowerShell ISE*, öppna ...\Learning Modules\Business affärskontinuitet och Haveriberedskap Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 skript.
    *  Under den här självstudiekursen kommer du att köra varje scenarier i det här PowerShell skript, så spara den här filen öppen.

3. Ange följande:
    * **$DemoScenario = 1**starta bakgrunden som synkroniseras klient-server och poolen konfigurationsinformation till katalogen

3. Tryck på **F5** att köra skriptet synkronisering. 
    *  Den här informationen används senare så att återställningen skapas en speglad bild av servrar, pooler och databaser i området för återställning.  
![Synkroniseringen](media/saas-dbpertenant-dr-geo-restore/sync-process.png)

Lämna fönstret PowerShell körs i bakgrunden och fortsätt med resten av den här kursen.

> [!Note]
> Synkroniseringen ansluter till programkatalogen via ett DNS-alias. Aliaset har ändrats under återställning och hemtransport att peka till den aktiva katalogen. Synkroniseringen håller katalogen aktuella databasen eller adresspool configuration ändringar som gjorts i området för återställning.  Under hemtransport tillämpas ändringarna på motsvarande resurser i den ursprungliga regionen.

## <a name="geo-restore-recovery-process-overview"></a>Översikt över processen för GEO-återställning återställning

Geo-återställning återställningsprocessen distribuerar programmet och återställer databaser från säkerhetskopior i området för återställning.

Återställningsprocessen gör följande:

1. Inaktiverar Traffic Manager-slutpunkten för webbappen i den ursprungliga regionen. Inaktivera slutpunkten hindrar användare från att ansluta till appen i ett ogiltigt tillstånd bör den ursprungliga regionen anslutas under återställningen.

1. Etablerar en återställning katalogserver i området recovery geo återställer katalogdatabasen och uppdaterar den _activecatalog_ alias för att peka på den återställda katalogservern.  
    * Ändra katalogen alias garanterar synkroniseringen för katalogen alltid synkroniseras med den aktiva katalogen.

1. Markerar alla befintliga klienter i katalogen återställning som offline om du vill förhindra åtkomst till klient databaser innan de återställs.

1. Etablerar en instans av appen i området för återställning och konfigurerar den för att använda den återställda katalogen i den regionen.
    * Om du vill behålla fördröjning till minst har exempelappen utformats så att den alltid ansluter till en klient-databas i samma region.

1. Etablerar en server och elastisk pool med nya klienter kommer att tillhandahållas. Skapa dessa resurser ser du till att etablera nya klienter inte stör återställning av befintliga klienter.

1. Uppdaterar aliaset ny klient så att den pekar till servern för den nya innehavaren databaser i området för återställning. Ändra det här aliaset säkerställer att databaser för alla nya klienter har etablerats i området för återställning.
        
1. Etablerar servrar och elastiska pooler i området återställning för att återställa databaser för innehavare. Dessa servrar och pooler är en speglad bild av konfigurationen i den ursprungliga regionen.  Etablering pooler som är direkta reserverar kapacitet som krävs för att återställa alla databaser.
    * Ett avbrott i en region kan placera stor belastning på de tillgängliga resurserna i parad region.  Reservera resurser snabbt rekommenderas om du förlita dig på geo-återställning för Katastrofåterställning. Överväg att använda geo-replikering om det är viktigt att ett program måste återställas i en viss region. 

1. Aktiverar Traffic Manager-slutpunkten för webbappen i området för återställning. Om du aktiverar den här slutpunkten kan programmet för att etablera nya klienter. Befintliga klienter är fortfarande offline i det här skedet.

1. Skickar batchar av förfrågningar om att återställa databaser i prioritetsordning. 
    * Batchar ordnas så att återställa databaser parallellt över alla pooler.  
    * Restore-begäranden skickas asynkront så att de har skickats snabbt och i kö för körning i varje pool.
    * Eftersom återställning begäranden bearbetas parallellt i alla pooler, är det bättre att distribuera viktiga hyresgäster i många pooler. 

1. Övervakar tjänsten SQL-databasen för att avgöra när databaser återställs. När en klient-databas har återställts, den är markerad online i katalogen och rowversion summan för klient-databas registreras. 
    * Klient-databaser kan nås av programmet så snart de har markerats online i katalogen.
    * Summan av rowversion värden i klient-databasen lagras i katalogen. Den här summan fungerar som ett fingeravtryck som tillåter hemtransport processen för att avgöra om databasen har uppdaterats i området för återställning.      

## <a name="run-the-recovery-script"></a>Kör skriptet för återställning

> [!IMPORTANT]
> Den här självstudiekursen återställer databaser från geo-redundant säkerhetskopior. Även om dessa säkerhetskopior är normalt tillgängliga inom 10 minuter kan ta det upp till en timme innan de blir tillgängliga. Skriptet för att pausa tills de är tillgängliga.  Tid för att hämta en kaffe!

Nu anta att det finns ett avbrott i den region där programmet distribueras och kör skriptet återställning:

1. I den *PowerShell ISE*, i ...\Learning Modules\Business affärskontinuitet och Haveriberedskap Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 skript anger du följande värden:
    * **$DemoScenario = 2**, återställa appen i en region som recovery genom att återställa från säkerhetskopior geo-redundant

1. Tryck **F5** för att köra skriptet.  
    * Skriptet öppnas i ett nytt PowerShell-fönster och startar sedan en uppsättning PowerShell-jobb som körs parallellt.  Dessa jobb återställa servrar, pooler och databaserna till regionen som recovery. 
    * Återställning regionen är de _parad region_ som är associerade med Azure-regionen där du distribuerade programmet. Mer information finns i [Azure länkas regioner](https://docs.microsoft.com/en-us/azure/best-practices-availability-paired-regions). 

1. Övervaka status för återställningsprocessen i PowerShell-fönstret.

    ![Återställningsprocessen](media/saas-dbpertenant-dr-geo-restore/dr-in-progress.png)

>Granska PowerShell-skript i mappen ...\Learning Modules\Business affärskontinuitet och Haveriberedskap Recovery\DR-RestoreFromBackup\RecoveryJobs om du vill utforska koden för återställningsjobben.

## <a name="review-the-application-state-during-recovery"></a>Granska programmets tillstånd under återställningen
Programmet är inte tillgänglig medan programmet slutpunkten är inaktiverad i Traffic Manager. Katalogen har återställts och alla klienter markeras offline.  Programslutpunkten i området för återställning sedan är aktiverat och programmet är online igen. Även om programmet är tillgängligt, klienter verkar vara offline i händelser hubb tills deras databaser har återställts. Det är viktigt att utforma ditt program för att hantera offline klient databaser.

1. Databasen har återställts efter katalogen men innan klienterna är tillbaka online, uppdatera Wingtip biljetter händelser hubben i webbläsaren.
    * I sidfoten, Lägg märke till att namnet på katalogen har nu en _-återställning_ suffix och finns i området för återställning.
    * Observera att klienter som inte har återställts är markerad som offline och inte är valbara.   
 
    ![Återställningsprocessen](media/saas-dbpertenant-dr-geo-restore/events-hub-tenants-offline-in-recovery-region.png)    

    * Om du öppnar en klients händelser sida direkt medan klienten är nedkopplad, visas sidan ett meddelande om klient offline. Till exempel om Contoso samklang Hall är offline försöker öppna http://events.wingtip-dpt.&lt; användaren&gt;.trafficmanager.net/contosoconcerthall ![återställningsprocessen](media/saas-dbpertenant-dr-geo-restore/dr-in-progress-offline-contosoconcerthall.png)

## <a name="provision-a-new-tenant-in-the-recovery-region"></a>Etablera en ny klient i området för återställning
Innan klienten databaser återställs, kan du etablera nya klienter i området för återställning. Den nya innehavaren databaser etableras i området för återställning kommer senare att återföras med återställda databaser.   

1. I den *PowerShell ISE*, ...\Learning Modules\Business affärskontinuitet och Haveriberedskap Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 skript egenskapen i följande:
    * **$DemoScenario = 3**, etablera en ny klient i området för återställning

1. Tryck **F5** för att köra skriptet. 

1. Sidan Hawthorn Hall händelser öppnas i webbläsaren när etableringen är slutförd. 
    * Observera att Hawthorn Hall databasen finns i området för återställning.
    ![Hawthorn Hall etableras i området för återställning](media/saas-dbpertenant-dr-geo-restore/hawthorn-hall-provisioned-in-recovery-region.png)

1. Uppdatera Wingtip biljetter händelser hubb sidan om du vill se Hawthorn Hall ingår i webbläsaren. 
    * Om du har etablerat Hawthorn Hall utan att vänta på andra hyresgäster att återställa det andra klienter kan fortfarande vara offline.

## <a name="review-the-recovered-state-of-the-application"></a>Granska återställda statusen för programmet

När återställningen är klar är programmet och alla klienter helt funktionella i området för återställning. 

1. När visas i PowerShell-konsolfönster visar alla klienter har återställts, uppdatera händelser hubben.  Klienterna kommer alla visas online, inklusive den nya innehavaren Hawthorn Hall.

    ![återställda och nya klienter i hubben händelser](media/saas-dbpertenant-dr-geo-restore/events-hub-with-hawthorn-hall.png)

1. Klicka på Contoso samklang Hall och öppna appens sida händelser.
    * Observera att databasen finns på återställningsservern finns i regionen som recovery i sidfoten.

    ![Contoso i området för återställning](media/saas-dbpertenant-dr-geo-restore/contoso-recovery-location.png)

1. I den [Azure-portalen](https://portal.azure.com), öppna listan över resursgrupper.  
    * Lägg märke till den resursgrupp som du distribuerat, plus resursgruppen återställning med den _-återställning_ suffix.  Resursgruppen återställning innehåller alla resurser som skapades under återställningen plus nya resurser som skapades under avbrottet.  

1. Öppna resursgruppen återställning och Observera följande:
    * Återställning versioner av katalogen och tenants1 servrar, med _-återställning_ suffix.  Återställda katalog och klient databaserna på dessa servrar som alla har namn som används i den ursprungliga regionen.

    * Den _tenants2-dpt -&lt;användare&gt;-återställning_ SQLServer.  Den här servern används för att etablera nya klienter under avbrottet.
    *   App Service med namnet _händelser-wingtip-dpt -&lt;recoveryregion&gt;-&lt;användaren & gt_; vilket är återställning instans av appen händelser. 

    ![Contoso i området för återställning](media/saas-dbpertenant-dr-geo-restore/resources-in-recovery-region.png)   
    
1. Öppna den _tenants2-dpt -&lt;användare&gt;-återställning_ SQLServer.  Meddelande som den innehåller databasen _hawthornhall_ och den elastiska poolen _Pool1_.  Den _hawthornhall_ databasen är konfigurerad som en elastisk databas i _Pool1_ elastisk pool.

## <a name="change-tenant-data"></a>Ändra klientdata 
Du uppdaterar en av databaserna återställda klient i den här uppgiften. Hemtransport processen kommer att kopiera återställda databaser som har ändrats till den ursprungliga regionen. 

1. Rulla igenom händelserna i webbläsaren, hitta händelselistan för Contoso samklang Hall, och Observera den sista händelsen _allvarligt Strauss_.

1. I den *PowerShell ISE*, anger följande värde i ...\Learning Modules\Business affärskontinuitet och Haveriberedskap Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 skript:
    * **$DemoScenario = 4**, ta bort en händelse från en klient i området för återställning
1. Tryck på **F5** att köra skriptet.
1. Uppdatera sidan Contoso samklang Hall händelser (http://events.wingtip-dpt.&lt; användaren&gt;.trafficmanager.net/contosoconcerthall) och Lägg märke till att händelsen allvarligt Strauss saknas.

Du har nu återställt som körs nu i området för återställning i självstudierna.  Du har etablerat en ny klient i området för återställning och ändrade data på en av de återställda innehavarna.  

> [!Note]
> Andra kurser i exemplet är inte avsedd att köras med appen i tillståndet återställning. Om du vill utforska andra självstudiekurser måste du först hem programmet.

## <a name="repatriation-process-overview"></a>Översikt över processen för hemtransport

Hemtransport processen återställs programmet och dess databaser till dess ursprungliga region efter ett avbrott har lösts.

![GEO-återställning hemtransport](media/saas-dbpertenant-dr-geo-restore/geo-restore-repatriation.png) 


Processen:

1. Stoppar alla aktiviteter som en pågående återställning och avbryter alla begäranden om återställning utestående eller relä databas.

1. Reaktiverar i ursprungliga region klient-databaser som inte har ändrats sedan avbrottet.  Detta inkluderar databaser som inte återställts än och databaser som har återställts, men har inte ändrats efteråt. Återaktiverade databaserna exakt senast via sina klienter.

1. Etablerar en speglad bild av nya klienter server och elastiska poolen i det ursprungliga regionen. När den här åtgärden är klar uppdateras det nya innehavaren aliaset för att peka på den här servern. Uppdatera alias gör ny klient onboarding ska ske i den ursprungliga regionen i stället för återställning-region.

1. Med geo-replikering, flyttar katalogen till den ursprungliga regionen från regionen som recovery.

1. Uppdaterar konfigurationen för lagringspooler i den ursprungliga regionen så att den är konsekvent med ändringar som görs i området för återställning under avbrottet.

1. Skapar nödvändiga servrar och lagringspoolerna som värd för alla nya databaser som skapades under avbrottet.

1. Med geo-replikering repatriates återställt klient databaser som har uppdaterats efter återställning och alla nya klient databaser som etablerats under avbrottet. 

1. Rensar upp resurser som skapades i området för återställning under återställningsprocessen.

Om du vill begränsa antalet databaser som klienten som måste vara återföras utfärdat steg 1-3 omedelbart.  

Steg 4 görs bara om katalogen i recovery region har ändrats under avbrottet. Katalogen uppdateras om skapas nya klienter eller om någon databas eller pool konfiguration ändras i området för återställning.

Det är viktigt att steg 7 förlorar minimala störningar klienter och inga data går förlorade. För att åstadkomma detta processen använder _georeplikering_.

Innan varje databas är georeplikerad, motsvarande databas i den ursprungliga regionen har tagits bort. Databasen i området för återställning är georeplikerad, skapa en sekundär replik i den ursprungliga regionen. När replikeringen är klar, markeras klienten offline i katalogen, vilket bryter alla anslutningar till databasen i området för återställning. Databasen sedan växlas över, vilket gör eventuella väntande transaktioner som ska bearbetas på sekundärt så att inga data går förlorade. Vid redundans återförs databasroller.  Sekundär i den ursprungliga regionen blir den primära databasen skrivskyddad och databasen i området för återställning blir en skrivskyddad sekundär. Klient-post i katalogen uppdateras för att referera till databasen i den ursprungliga regionen och innehavaren markeras online.  Hemtransport av databasen är nu klar. 

Program ska skrivas med logik så de återansluta automatiskt när anslutningar bryts.  När de ansluter med hjälp av katalogen till broker anslutningen som de ansluter till repatriated databasen i den ursprungliga regionen. Även om kort frånkoppling är ofta inte tänkt på att du hem databaser utanför arbetstid. 

När en databas är återföras kan den sekundära databasen i området för återställning tas bort. Databasen i den ursprungliga regionen beroende sedan geo-återställning för DR skyddet igen.

Resurser i regionen återställning, inklusive återställning servrar och pooler, tas bort i steg 8.

## <a name="run-the-repatriation-script"></a>Kör skriptet hemtransport
Nu ska vi anta avbrottet är löst och kör skriptet hemtransport.

Om du har följt kursen reaktiverar Fabrikam Jazz en och skogskornell Dojo i den ursprungliga regionen omedelbart i skriptet eftersom de är oförändrade. Sedan repatriates den nya innehavaren Hawthorn Hall och Contoso samklang Hall eftersom den har ändrats. Skriptet repatriates även den katalogen som har uppdaterats när Hawthorn Hall har etablerats.
  
1. I den *PowerShell ISE*i den ...\Learning Modules\Business affärskontinuitet och Haveriberedskap Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 skript.

1. Kontrollera att katalogen synkroniseringen körs fortfarande i dess PowerShell-instansen.  Om det behövs kan du starta om den genom att ange:
    * **$DemoScenario = 1**, Starta synkronisering klient-server, pool och konfigurationsinformation för databasen till katalogen
    * Tryck **F5** för att köra skriptet.
1.  Om du vill starta processen hemtransport, ange:
    * **$DemoScenario = 5**, hem appen till dess ursprungliga region
    * Tryck på **F5** att köra skriptet återställning i ett nytt PowerShell-fönster.  Hemtransport tar flera minuter och kan övervakas i PowerShell-fönstret.
1. När skriptet körs, uppdatera sidan händelser hubb (http://events.wingtip-dpt.&lt; användaren&gt;. trafficmanager.net)
    * Observera att alla klienter är online och tillgängliga hela processen.
1. Klicka på Fabrikam Jazz en så att den öppnas. Om du inte ändrar den här innehavaren, se från sidfoten att servern har redan återställts till den ursprungliga servern.
1. Öppna eller uppdatera sidan Contoso samklang Hall händelser och Observera från sidfoten att databasen är fortfarande på det _-återställning_ servern först.  
1. Uppdatera sidan Contoso samklang Hall händelser när hemtransport processen har slutförts och att märka att databasen är i din ursprungliga region.
1. Uppdatera händelser navet igen och öppna Hawthorn Hall och Lägg märke till dess databas finns också i det ursprungliga regionen. 

## <a name="clean-up-recovery-region-resources-after-repatriation"></a>Rensa recovery region resurser efter hemtransport
Det är säkert att ta bort resurser i området för återställning när hemtransport har slutförts. 

> [!IMPORTANT]
> Ta bort resurserna omgående om du vill stoppa alla fakturering för dessa.

Återställningsprocessen skapar recovery resurser i en resursgrupp för återställning. Rensningen tar bort den här resursgruppen och ta bort alla referenser till resurser från katalogen. 

1. I den *PowerShell ISE*i ...\Learning Modules\Business affärskontinuitet och Haveriberedskap Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 skript, ange:
    * **$DemoScenario = 6**, ta bort föråldrade resurser från regionen som recovery

1. Tryck **F5** för att köra skriptet.

När du rensar skripten är programmet tillbaka där den har startats.  Du kan köra skriptet igen eller prova att använda andra självstudiekurser nu.

## <a name="designing-the-application-to-ensure-app-and-database-are-colocated"></a>Utforma program så att appen och databasen är samordnade 
Programmet är utformat så att den alltid ansluter från en instans i samma region som klient-databas. Den här designen minskar Nätverksfördröjningen mellan programmet och databasen. Denna optimering förutsätter interaktionen app att databasen är chattier än användaren att appen interaktion.  

Klient-databaser kan spridas över återställning och ursprungliga regioner för någon gång under hemtransport.  För varje databas letar appen upp den region där databasen finns genom att göra en DNS-sökning på klient-servernamn. Servernamnet är ett alias i SQL-databas. Servernamnet alias innehåller regionsnamnet. Om programmet inte finns i samma region som databasen, dirigerar den till instansen i samma region som databasserver.  Omdirigera till instans i samma region som databasen minimerar fördröjning mellan appen och databasen.  

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:
> [!div class="checklist"]

>* Använda klient-katalogen för att rymma med jämna mellanrum konfigurationsinformation, vilket gör att en spegling Återställningsmiljö skapas i en annan region
>* Använda geo-återställning för att återställa Azure SQL-databaser till recovery region
>* Uppdatera klienten katalogen för att återspegla återställda klient databasplatser  
>* Använd ett DNS-alias för att aktivera ett program ansluta till klient-katalogen under hela utan omkonfiguration
>* Använd geo-replikering till hem återställda databaser till deras ursprungliga region efter ett avbrott har stängts

Försök nu den [katastrofåterställning för ett SaaS-program för flera innehavare som använder databasen geo-replikering](saas-dbpertenant-dr-geo-replication.md) att lära dig att avsevärt minska den tid som behövs för att återställa ett storskaligt program för flera innehavare med geo-replikering.

## <a name="additional-resources"></a>Ytterligare resurser

* [Ytterligare självstudier som bygger på Wingtip SaaS-program](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-wtp-overview#sql-database-wingtip-saas-tutorials)
