---
title: Haveriberedskap för SaaS-appar som använder Azure SQL Database Geo-replikering | Microsoft Docs
description: Lär dig hur du använder Azure SQL Database-georepliker för att återställa en app för flera innehavare SaaS vid ett avbrott
keywords: sql database tutorial
services: sql-database
author: AyoOlubeko
manager: craigg
ms.service: sql-database
ms.custom: saas apps
ms.topic: article
ms.date: 04/09/2018
ms.author: ayolubek
ms.openlocfilehash: c6f3da52643caa9aa1172db5b884c5336c409715
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="disaster-recovery-for-a-multi-tenant-saas-application-using-database-geo-replication"></a>Haveriberedskap för ett SaaS-program för flera innehavare som använder databasen geo-replikering

I kursen får utforska du en fullständig katastrofåterställning för ett SaaS-program för flera innehavare som implementeras med hjälp av databasen per klient-modellen. Om du vill skydda appen från ett avbrott måste du använda [ _georeplikering_ ](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-geo-replication-overview) att skapa repliker för katalogen och klient-databaser i en annan återställning region. Om ett avbrott inträffar kan växlar du snabbt över till de här replikeringarna att återuppta normal verksamheten. Vid redundans blir databaser i den ursprungliga regionen sekundära repliker för databaserna i området för återställning. När dessa repliker är tillbaka online fånga de automatiskt upp till tillståndet för databaserna i området för återställning. När driftstörningarna har åtgärdats, växlar tillbaka till databaserna i produktionsområdet för.

Den här självstudiekursen utforskar både redundans och återställning av arbetsflöden. Du lär dig hur du:
> [!div classs="checklist"]

>* Synkronisera databasen och elastisk pool konfigurationsinformation till katalogen för klient
>* Konfigurera en Återställningsmiljö i en annan region, som består av program, servrar och pooler
>* Använd _georeplikering_ att replikera katalogen och klient databaserna till regionen som recovery
>* Växla över program och katalog och klient-databaser till regionen som recovery 
>* Senare kan växla över programmet, katalogen och klient databaserna tillbaka till den ursprungliga regionen när driftstörningarna har åtgärdats
>* Uppdatera katalogen som varje klient-databasen har redundansväxlats att spåra den primära platsen för varje klient databasen
>* Se till att ett program och primära klient-databasen är alltid samordnade i samma Azure-region att minska svarstiden  
 

Kontrollera att följande förutsättningar har slutförts innan du påbörjar den här kursen:
* Wingtip biljetter SaaS-databasen per klient appen har distribuerats. För att distribuera på mindre än fem minuter finns [distribuera och utforska Wingtip biljetter SaaS-databasen per klient program](saas-dbpertenant-get-started-deploy.md)  
* Azure PowerShell ska ha installerats. Mer information finns i [Komma igång med Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-the-geo-replication-recovery-pattern"></a>Introduktion till recovery mönstret geo-replikering

![Arkitektur för återställning](media/saas-dbpertenant-dr-geo-replication/recovery-architecture.png)
 
Katastrofåterställning (DR) är viktigt för många program för kompatibilitetsskäl eller kontinuitet för företag. Det bör finnas en långvariga avbrott, kan en väl förberedd DR-plan minimera avbrott i verksamheten. Med geo-replikering innehåller lägsta Återställningspunktmål och Återställningstidsmål genom att upprätthålla databasrepliker i en region som recovery som kan flyttas över till på kort meddelande.

En DR-plan utifrån geo-replikering består av tre separata delar:
* Ställa in-skapande och underhåll av Återställningsmiljö
* Återställning - redundans för appen och databaser till Återställningsmiljö om ett avbrott inträffar
* Hemtransport - redundans för appen och databaserna tillbaka till den ursprungliga regionen när programmet har lösts 

Alla delar måste beaktas noggrant, särskilt om du kör i större skala. Generellt sett måste planen utföra flera mål:

* Konfiguration
    * Fastställa och upprätthålla en spegling miljö i området för återställning. Skapa elastiska pooler och replikering av alla fristående databaser i den här Återställningsmiljö reserverar kapacitet i området för återställning. Underhåll av den här miljön innehåller replikerar den nya innehavaren databaser som de har etablerats.  
* Återställning
    * Om en skala ned Återställningsmiljö används för att minimera dagliga kostnader, måste pooler och fristående databaser skalas upp och hämta fullständig driftskapacitet i området för återställning
    * Aktivera nya innehavaren etablering i området för återställning så snart som möjligt  
    * Optimeras för att återställa hyresgäster i prioritetsordning
    * Optimeras för att hämta klienter online så snabbt som möjligt genom att göra steg parallellt där praktiska
    * Bli motståndskraftiga mot fel, kan startas om, och idempotent
    * Kunna avbryta processen halva rör sig om den ursprungliga regionen kommer tillbaka online.
* Hemtransport 
    * Växla över databaser från regionen som recovery till repliker i den ursprungliga regionen med minimal påverkan på klienter: ingen dataförlust och minst offline per klient.   

I den här kursen behandlas dessa problem med funktionerna i Azure SQL Database och Azure-plattformen:

* [Azure Resource Manager-mallar](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template)att reservera alla nödvändiga kapacitet så snabbt som möjligt. Azure Resource Manager-mallar används för att etablera en speglad bild av produktionsservrar och elastiska pooler i området för återställning.
* [GEO-replikering](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview), för att skapa asynkront replikerade sekundärservrar för alla databaser som skrivskyddad. Vid ett avbrott växlar du över till repliker i området för återställning.  När driftstörningarna har åtgärdats, växlar tillbaka till databaserna i den ursprungliga regionen med inga data går förlorade.
* [Asynkron](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations) redundansåtgärder skickas i klient-prioritetsordning, för att minimera failover-tid för stort antal databaser.
* [Fragmentera hanteringsfunktioner recovery](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-database-recovery-manager), för att ändra databasposter i katalogen under återställning och hemtransport. Dessa funktioner kan programmet för att ansluta till klient-databaser oavsett plats utan att konfigurera om appen.
* [SQL server-DNS-alias](https://docs.microsoft.com/azure/sql-database/dns-alias-overview), för att aktivera sömlös etablering av nya klienter oavsett vilken region appen körs i. DNS-alias används också att synkroniseringen katalog att ansluta till den aktiva katalogen oavsett dess plats.

## <a name="get-the-disaster-recovery-scripts"></a>Hämta disaster recovery-skript 

> [!IMPORTANT]
> Precis som alla Wingtip biljetter management skripten DR-skript är exempel kvalitet och ska inte användas i produktionen. 

Återställningen skript som används i den här kursen och Wingtip programmets källkod är tillgängliga i den [Wingtip biljetter SaaS databas per klient GitHub-lagringsplatsen](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/). Kolla in den [allmänna riktlinjer](saas-tenancy-wingtip-app-guidance-tips.md) steg för att ladda ned och avblockera hanteringsskript Wingtip biljetter.

## <a name="tutorial-overview"></a>Självstudier – översikt
I kursen får använda du först geo-replikering för att skapa repliker Wingtip biljetter och databaserna i en annan region. Sedan kan växla du över till den här regionen att simulera återställning från ett avbrott. När du är färdig fungerar programmet fullt ut i området för återställning.

Senare i ett separat hemtransport steg växlar över katalog och klient-databaser i området för återställning till den ursprungliga regionen. Program och databaser förblir tillgängliga i hela hemtransport. När du är färdig fungerar programmet fullt ut i den ursprungliga regionen.

> [!Note]
> Programmet återställs till den _parad region_ för den region där programmet har distribuerats. Mer information finns i [Azure länkas regioner](https://docs.microsoft.com/en-us/azure/best-practices-availability-paired-regions).

## <a name="review-the-healthy-state-of-the-application"></a>Granska felfri statusen för programmet

Granska den normala hälsotillstånd för programmet innan du startar återställningsprocessen.
1. Öppna i webbläsaren, Wingtip biljetter händelser navet (http://events.wingtip-dpt.&lt; användaren&gt;. trafficmanager.net - Ersätt &lt;användaren&gt; med din distribution användaren värde).
    * Bläddra till längst ned på sidan och Lägg märke till katalogen servernamn och en plats i sidfoten. Platsen är den region där du har distribuerat appen.
    *Tips: Markören över plats för att visa. * 
     ![Händelser hubb felfritt tillstånd i ursprungliga region](media/saas-dbpertenant-dr-geo-replication/events-hub-original-region.png)

2. Klicka på Contoso samklang Hall innehavaren och öppna appens sida för händelsen.
    * Observera server klientnamn i sidfoten. Platsen ska vara samma som den katalogservern plats.

3. I den [Azure-portalen](https://portal.azure.com), öppna resursgruppen där appen har distribuerats
    * Lägg märke till den region där servrarna som har distribuerats. 

## <a name="sync-tenant-configuration-into-catalog"></a>Synkronisera klientkonfiguration i katalogen

I den här uppgiften kan du starta en process som synkroniserar konfigurationen av servrar, elastiska pooler och databaser till klient-katalogen. Processen för att hålla informationen aktuell i katalogen.  Processen fungerar med den aktiva katalogen om den ursprungliga regionen eller i området för återställning. Konfigurationsinformationen används som en del av återställningsprocessen så Återställningsmiljön i är konsekvent med den ursprungliga miljön, och sedan senare under hemtransport så den ursprungliga regionen görs konsekvent med ändringar som görs i den Återställningsmiljö. Katalogen används också för att hålla reda på återställningstillstånd av klienternas resurser

> [!IMPORTANT]
> För enkelhetens skull implementeras synkroniseringen och andra långvariga återställning och hemtransport i dessa självstudiekurser som lokala Powershell jobb eller sessioner som körs under din klient användarinloggning. Autentiseringstoken som utfärdas när du loggar in upphör att gälla efter flera timmar och sedan jobben misslyckas. I ett produktionsscenario med bör tidskrävande processer implementeras som tillförlitliga Azure-tjänster av något slag, körs under ett huvudnamn för tjänsten. Se [Använd Azure PowerShell för att skapa ett huvudnamn för tjänsten med ett certifikat](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authenticate-service-principal).

1. I den _PowerShell ISE_, öppna filen ...\Learning Modules\UserConfig.psm1. Ersätt `<resourcegroup>` och `<user>` på rader 10 och 11 med det värde som används när du har distribuerat appen.  Spara filen!

2. I den *PowerShell ISE*, öppna ...\Learning Modules\Business affärskontinuitet och Haveriberedskap Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 skript och ange:
    * **$DemoScenario = 1**starta bakgrunden som synkroniseras klient-server och poolen konfigurationsinformation till katalogen

3. Tryck på **F5** att köra skriptet synkronisering. En ny PowerShell-session öppnas för att synkronisera konfigurationen av klienternas resurser.
![Synkroniseringen](media/saas-dbpertenant-dr-geo-replication/sync-process.png)

Lämna fönstret PowerShell körs i bakgrunden och fortsätt med resten av kursen. 

> [!Note]
> Synkroniseringen ansluter till programkatalogen via ett DNS-alias. Det här aliaset har ändrats under återställning och hemtransport att peka till den aktiva katalogen. Synkroniseringen håller katalogen aktuella databasen eller adresspool configuration ändringar som gjorts i området för återställning.  Under hemtransport tillämpas ändringarna på motsvarande resurser i den ursprungliga regionen.

## <a name="create-secondary-database-replicas-in-the-recovery-region"></a>Skapa sekundära databasrepliker i området för återställning

I den här uppgiften kan du starta en process som distribuerar en duplicerad app-instansen och replikerar katalogen och alla klient-databaser till en region för återställning.

> [!Note]
> Den här kursen ger det skydd på geo-replikering till exempelprogrammet Wingtip biljetter. I en form av produktionsscenario för ett program som använder geo-replikering, skulle varje klient etableras med en georeplikerad databas redan från början. Se [skapar tjänster med hög tillgänglighet med hjälp av Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-designing-cloud-solutions-for-disaster-recovery#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime)

1. I den *PowerShell ISE*, öppna ...\Learning Modules\Business affärskontinuitet och Haveriberedskap Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 skript och ange följande värden:
    * **$DemoScenario = 2**, Skapa spegling Återställningsmiljö och replikera katalogen och klient-databaser

2. Tryck **F5** för att köra skriptet. En ny PowerShell-session öppnas för att skapa replikerna.
![Synkroniseringen](media/saas-dbpertenant-dr-geo-replication/replication-process.png)  

## <a name="review-the-normal-application-state"></a>Granska normal programtillståndet
Nu programmet körs normalt i den ursprungliga regionen och nu är skyddade med geo-replikering.  Skrivskyddad sekundära repliker, finns i recovery region för alla databaser. 
1. I Azure-portalen, titta på dina resursgrupper och Observera att en resursgrupp har skapats med - återställning suffix i området för återställning. 

1. Utforska resurserna i resursgruppen för återställning.  

1. Klicka på Contoso samklang Hall databasen på den _tenants1-dpt -&lt;användare&gt;-återställning_ server.  Klicka på Geo-replikering på vänster sida. 

    ![Länk för Contoso samklang geo-replikering](media/saas-dbpertenant-dr-geo-replication/contoso-geo-replication.png) 

Observera länken geo-replikering mellan den primära servern i den ursprungliga regionen och sekundärt i området för återställning i Azure-regioner-mappningen.  

## <a name="fail-over-the-application-into-the-recovery-region"></a>Växla över programmet till regionen för återställning

### <a name="geo-replication-recovery-process-overview"></a>GEO-replikering processöversikt för återställning

Skriptet recovery utför följande uppgifter:

1. Inaktiverar Traffic Manager-slutpunkten för webbappen i den ursprungliga regionen. Inaktivera slutpunkten hindrar användare från att ansluta till appen i ett ogiltigt tillstånd bör den ursprungliga regionen anslutas under återställningen.

1. Använder en påtvingad redundans av katalogdatabasen i området för återställning så att den primära databasen och uppdaterar den _activecatalog_ alias så att den pekar till katalogen återställningsservern.

1. Uppdateringar av _newtenant_ alias att peka till klient-server i området för återställning. Ändra det här aliaset säkerställer att databaserna för alla nya klienter har etablerats i området för återställning. 

1. Markerar alla befintliga klienter i katalogen återställning som offline för att förhindra åtkomst till klient databaser innan de har redundansväxlats.

1. Uppdaterar konfigurationen av alla elastiska pooler och replikerade fristående databaser i recovery region för spegling av konfigurationen i det ursprungliga regionen. (Den här uppgiften krävs endast om pooler eller replikerade databaser i recovery environment skalas under normal drift för att minska kostnaderna).

1. Aktiverar Traffic Manager-slutpunkten för webbappen i området för återställning. Om du aktiverar den här slutpunkten kan programmet för att etablera nya klienter. Befintliga klienter är fortfarande offline i det här skedet.

1. Skickar batchar av begäranden att tvinga växla över databaser i prioritetsordning.
    * Batchar ordnas så att databaser har redundansväxlats parallellt över alla pooler.
    * Failover-begäranden skickas med hjälp av asynkrona åtgärder så att de skickas snabbt och flera begäranden kan bearbetas samtidigt.

   > [!Note]
   > I ett avbrott scenario är primära databaser i den ursprungliga regionen offline.  Tvingad växlar över på sekundär radbrytningar anslutningen till primärt utan att tillämpa alla återstående köade transaktioner. I ökad katastrofåterställning som den här självstudiekursen, om det inte finns någon aktivitet vid tidpunkten för växling vid fel kan det finnas vissa data går förlorade. Senare under hemtransport används när du redundansväxlar databaser i recovery region tillbaka till den ursprungliga regionen en normal växling vid fel för att se till att det finns inga data går förlorade.

1. Övervakar tjänsten SQL-databasen för att avgöra när databaser har har redundansväxlats. När en klient-databasen har redundansväxlats uppdaterar katalogen för att registrera återställningstillstånd för klient-databasen och markera klienten som online.
    * Klient-databaser kan nås av programmet så snart de har markerats online i katalogen.
    * Summan av rowversion värden i klient-databasen lagras i katalogen. Det här värdet fungerar som ett fingeravtryck som tillåter hemtransport processen för att avgöra om databasen har uppdaterats i området för återställning.

### <a name="run-the-script-to-fail-over-to-the-recovery-region"></a>Kör skript för att växla över till regionen som recovery

Nu anta att det finns ett avbrott i den region där programmet distribueras och kör skriptet återställning:

1. I den *PowerShell ISE*, öppna ...\Learning Modules\Business affärskontinuitet och Haveriberedskap Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 skript och ange följande värden:
    * **$DemoScenario = 3**, återställa appen till en region för återställning av misslyckande replikerna

2. Tryck **F5** för att köra skriptet.  
    * Skriptet öppnas i ett nytt PowerShell-fönster och startar sedan en serie med PowerShell-jobb som körs parallellt. Dessa jobb växlar över klient databaser till regionen som recovery.
    * Återställning regionen är de _parad region_ som är associerade med Azure-regionen där du distribuerade programmet. Mer information finns i [Azure länkas regioner](https://docs.microsoft.com/en-us/azure/best-practices-availability-paired-regions). 

3. Övervaka status för återställningsprocessen i PowerShell-fönstret.
    ![Failover-processen](media/saas-dbpertenant-dr-geo-replication/failover-process.png)

> [!Note]
> Granska PowerShell-skript i mappen ...\Learning Modules\Business affärskontinuitet och Haveriberedskap Recovery\DR-FailoverToReplica\RecoveryJobs om du vill utforska koden för återställningsjobben.

### <a name="review-the-application-state-during-recovery"></a>Granska programmets tillstånd under återställningen
Programmet är inte tillgänglig medan programmet slutpunkten är inaktiverad i Traffic Manager. När katalogen växlas över till regionen som recovery och alla klienter markeras offline, kan programmet tas online igen. Även om programmet är tillgängligt, visas varje klient offline i hubben händelser förrän databasen har redundansväxlats. Det är viktigt att utforma ditt program för att hantera offline klient databaser.

1. Så fort som möjligt när katalogdatabasen har återställts, uppdatera Wingtip biljetter händelser hubben i webbläsaren.
    * I sidfoten, Lägg märke till att namnet på katalogen har nu en _-återställning_ suffix och finns i området för återställning.
    * Observera att klienter som ännu inte återställs kan markeras som offline och inte är valbara.  

    > [!Note]
    > Med bara några databaser att återställa kan du inte att kunna uppdatera webbläsaren innan återställningen har slutförts, så att du inte kan se innehavarna när de är offline. 
 
    ![Händelser hubb offline](media/saas-dbpertenant-dr-geo-replication/events-hub-offlinemode.png) 

    * Om du öppnar en offline-klient händelser sida direkt, visas ett meddelande om klient offline. Till exempel om Contoso samklang Hall är offline försöker öppna http://events.wingtip-dpt.&lt; användaren&gt;.trafficmanager.net/contosoconcerthall ![Contoso Offline sida](media/saas-dbpertenant-dr-geo-replication/dr-in-progress-offline-contosoconcerthall.png) 

### <a name="provision-a-new-tenant-in-the-recovery-region"></a>Etablera en ny klient i området för återställning
Du kan etablera nya klienter i recovery region innan alla befintliga klient databaser har redundansväxlats.  

1. I den *PowerShell ISE*, öppna ...\Learning Modules\Business affärskontinuitet och Haveriberedskap Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 skript och ange följande egenskap:
    * **$DemoScenario = 4**, etablera en ny klient i området för återställning

2. Tryck på **F5** att köra skriptet och etablera den nya innehavaren. 

3. Sidan Hawthorn Hall händelser öppnas i webbläsaren när den har slutförts. Observera att databasen Hawthorn Hall etableras i området för återställning från sidfoten.
    ![Hawthorn Hall händelser sida](media/saas-dbpertenant-dr-geo-replication/hawthornhallevents.png) 

4. Uppdatera Wingtip biljetter händelser hubb sidan om du vill se Hawthorn Hall ingår i webbläsaren. 
    * Om du har etablerat Hawthorn Hall utan att vänta på andra hyresgäster att återställa det andra klienter kan fortfarande vara offline.


## <a name="review-the-recovered-state-of-the-application"></a>Granska återställda statusen för programmet

När återställningen är klar är programmet och alla klienter helt funktionella i området för återställning. 

1. När visas i PowerShell-konsolfönster visar alla klienter har återställts, uppdatera händelser hubben.  Klienterna kommer alla visas online, inklusive den nya innehavaren Hawthorn Hall.

    ![återställda och nya klienter i hubben händelser](media/saas-dbpertenant-dr-geo-replication/events-hub-with-hawthorn-hall.png)

2. I den [Azure-portalen](https://portal.azure.com), öppna listan över resursgrupper.  
    * Lägg märke till den resursgrupp som du distribuerat, plus resursgruppen återställning med den _-återställning_ suffix.  Resursgruppen återställning innehåller alla resurser som skapades under återställningen plus nya resurser som skapades under avbrottet.  

3. Öppna resursgruppen återställning och Observera följande:
    * Återställning versioner av katalogen och tenants1 servrar, med _-återställning_ suffix.  Återställda katalog och klient databaserna på dessa servrar som alla har namn som används i den ursprungliga regionen.

    * Den _tenants2-dpt -&lt;användare&gt;-återställning_ SQLServer.  Den här servern används för att etablera nya klienter under avbrottet.
    *   App Service med namnet _händelser-wingtip-dpt -&lt;recoveryregion&gt;-&lt;användaren & gt_; vilket är återställning instans av appen händelser. 

    ![Azure recovery resurser ](media/saas-dbpertenant-dr-geo-replication/resources-in-recovery-region.png)    
    
4. Öppna den _tenants2-dpt -&lt;användare&gt;-återställning_ SQLServer.  Meddelande som den innehåller databasen _hawthornhall_ och den elastiska poolen _Pool1_.  Den _hawthornhall_ databasen är konfigurerad som en elastisk databas i _Pool1_ elastisk pool.

5. Gå tillbaka till resursgruppen och klicka på Contoso samklang Hall databasen på den _tenants1-dpt -&lt;användare&gt;-återställning_ server. Klicka på Geo-replikering på vänster sida.
    
    ![Contoso-databasen efter växling vid fel](media/saas-dbpertenant-dr-geo-replication/contoso-geo-replication-after-failover.png)

## <a name="change-tenant-data"></a>Ändra klientdata 
Du uppdaterar en av databaserna klient i den här uppgiften. 

1. Hitta händelselistan för Contoso samklang Hall och notera efternamn händelse i webbläsaren.
2. I den *PowerShell ISE*, anger följande värde i ...\Learning Modules\Business affärskontinuitet och Haveriberedskap Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 skript:
    * **$DemoScenario = 5** ta bort en händelse från en klient i området för återställning
3. Tryck på **F5** att köra skriptet
4. Uppdatera sidan Contoso samklang Hall händelser (http://events.wingtip-dpt.&lt; användaren&gt;.trafficmanager.net/contosoconcerthall - ersätta &lt;användaren&gt; med din distribution användaren värde) och Lägg märke till att den sista händelsen har tagits bort.

## <a name="repatriate-the-application-to-its-original-production-region"></a>Hem programmet till dess ursprungliga produktionsregion

Den här uppgiften repatriates programmet till dess ursprungliga region. I ett verkligt scenario kan du initiera hemtransport när driftstörningarna har åtgärdats.

### <a name="repatriation-process-overview"></a>Översikt över processen för hemtransport

![Hemtransport arkitektur](media/saas-dbpertenant-dr-geo-replication/repatriation-architecture.png)

Hemtransport processen:
1. Avbryter alla begäranden om återställning utestående eller relä databas.
2. Uppdateringar av _newtenant_ alias att peka till klienternas server i ursprung region. Ändra det här aliaset säkerställer att databaserna för alla nya klienter nu etableras i området ursprung.
3. Initierar ändrade klientdata till den ursprungliga regionen
4. Växlar klient databaser i prioritetsordning.

Failover flyttas effektivt databasen till den ursprungliga regionen. När databasen växlar måste alla öppna anslutningar tas bort och databasen är inte tillgänglig för några sekunder. Program ska skrivas med logik så de ansluta igen.  Även om den här korta frånkoppling är ofta inte tänkt på att du hem databaser utanför arbetstid. 


### <a name="run-the-repatriation-script"></a>Kör skriptet hemtransport
Nu ska vi anta avbrottet är löst och kör skriptet hemtransport.

1. I den *PowerShell ISE*i den ...\Learning Modules\Business affärskontinuitet och Haveriberedskap Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 skript.

2. Kontrollera att katalogen synkroniseringen körs fortfarande i dess PowerShell-instansen.  Om det behövs kan du starta om den genom att ange:
    * **$DemoScenario = 1**, Starta synkronisering klient-server, pool och konfigurationsinformation för databasen till katalogen
    * Tryck **F5** för att köra skriptet.

3.  Om du vill starta processen hemtransport, ange:
    * **$DemoScenario = 6**, hem appen till dess ursprungliga region
    * Tryck på **F5** att köra skriptet återställning i ett nytt PowerShell-fönster.  Hemtransport tar flera minuter och kan övervakas i PowerShell-fönstret.
    ![Hemtransport process](media/saas-dbpertenant-dr-geo-replication/repatriation-process.png)

4. När skriptet körs, uppdatera sidan händelser hubb (http://events.wingtip-dpt.&lt; användaren&gt;. trafficmanager.net)
    * Observera att alla klienter är online och tillgängliga hela processen.

5. När hemtransport är klar, uppdatera hubben händelser och öppna sidan händelser för Hawthorn Hall. Observera att den här databasen har tagits återföras till den ursprungliga regionen.
    ![Händelser hubb återföras](media/saas-dbpertenant-dr-geo-replication/events-hub-repatriated.png)


## <a name="designing-the-application-to-ensure-app-and-database-are-colocated"></a>Utforma program så att appen och databasen är samordnade 
Programmet är utformat så att den alltid ansluter från en instans i samma region som klient-databas. Den här designen minskar Nätverksfördröjningen mellan programmet och databasen. Denna optimering förutsätter interaktionen app att databasen är chattier än användaren att appen interaktion.  

Klient-databaser kan spridas över återställning och ursprungliga regioner för någon gång under hemtransport. För varje databas letar appen upp den region där databasen finns genom att göra en DNS-sökning på klient-servernamn. Servernamnet är ett alias i SQL-databas. Servernamnet alias innehåller regionsnamnet. Om programmet inte finns i samma region som databasen, dirigerar den till instansen i samma region som databasserver.  Omdirigera till instans i samma region som databasen minimerar fördröjning mellan appen och databasen. 

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:
> [!div classs="checklist"]

>* Synkronisera databasen och elastisk pool konfigurationsinformation till katalogen för klient
>* Konfigurera en Återställningsmiljö i en annan region, som består av program, servrar och pooler
>* Använd _georeplikering_ att replikera katalogen och klient databaserna till regionen som recovery
>* Växla över program och katalog och klient-databaser till regionen som recovery 
>* Utför återställning efter fel programmet, katalogen och klient databaserna till den ursprungliga regionen när driftstörningarna har åtgärdats

Mer information om tekniker som Azure SQL database tillhandahåller för att aktivera kontinuitet för företag i den [översikt över verksamhetskontinuitet](sql-database-business-continuity.md) dokumentation.

## <a name="additional-resources"></a>Ytterligare resurser

* [Ytterligare självstudier som bygger på Wingtip SaaS-program](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-wtp-overview#sql-database-wingtip-saas-tutorials)
