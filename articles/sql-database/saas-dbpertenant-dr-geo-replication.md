---
title: Haveriberedskap för SaaS-appar som använder Azure SQL Database Geo-replikering | Microsoft Docs
description: Lär dig hur du använder Azure SQL Database-georepliker för att återställa en SaaS-app för flera klienter vid ett eventuellt strömavbrott
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
ms.date: 04/09/2018
ms.openlocfilehash: f24c76fb6b7ca24573a97aa122659fe5ca019550
ms.sourcegitcommit: 715813af8cde40407bd3332dd922a918de46a91a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47056343"
---
# <a name="disaster-recovery-for-a-multi-tenant-saas-application-using-database-geo-replication"></a>Haveriberedskap för en SaaS-program med hjälp av database geo-replikering

I den här självstudien får utforska du fullständig haveriberedskap för ett SaaS-program för flera innehavare som implementeras med hjälp av modellen databas-per-klient. För att skydda appen från ett avbrott kan du använda [ _georeplikering_ ](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview) att skapa repliker för catalog och klient-databaser i en annan återställningsregion. Om ett avbrott uppstår kan växlar du snabbt över till de här replikeringarna att återuppta normal verksamheten. Vid redundans blir databaser i den ursprungliga regionen sekundära repliker för databaserna i återställningsregionen. När dessa repliker är online igen fånga de automatiskt upp till tillståndet för databaserna i återställningsregionen. När driftstörningarna har åtgärdats, växla tillbaka till databaserna i produktionsregionen.

Den här guiden utforskar arbetsflöden för både redundans och återställning efter fel. Du lär dig följande:
> [!div classs="checklist"]

>* Synkronisera databaser och elastiska konfigurationsinformation för lagringspool i klient-katalogen
>* Konfigurera en Återställningsmiljö i en annan region, som består av program, servrar och pooler
>* Använd _geo-replikering_ att replikera katalogen och klient-databaser till återställningsregionen
>* Växla över programmet och katalogen och klient-databaser till återställningsregionen 
>* Senare, växlar över programmet, katalog- och klienttrafik databaserna tillbaka till den ursprungliga regionen när driftstörningarna har åtgärdats
>* Uppdatera katalogen eftersom varje klientdatabas växlas över till att spåra den primära platsen för varje klientdatabas
>* Se till att program och primära klientdatabasen alltid samplaceras i samma Azure-region som minskar svarstiderna  
 

Kontrollera att följande förutsättningar har slutförts innan du påbörjar den här självstudiekursen:
* Wingtip biljetter SaaS-databas per klient appen har distribuerats. Om du vill distribuera i mindre än fem minuter [distribuera och utforska Wingtip biljetter SaaS-databas per användare](saas-dbpertenant-get-started-deploy.md)  
* Azure PowerShell ska ha installerats. Mer information finns i [Komma igång med Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-the-geo-replication-recovery-pattern"></a>Introduktion till geo-replikering recovery mönstret

![Recovery-arkitekturen](media/saas-dbpertenant-dr-geo-replication/recovery-architecture.png)
 
Haveriberedskap (DR) är viktigt för många program, vare sig det gäller efterlevnadsskäl och verksamhetskontinuitet. Det bör finnas en långvarig tjänstavbrott kan en väl förberedd DR-plan minimera verksamhetsavbrott. Med geo-replikering ger lägsta RPO och RTO genom att underhålla databasrepliker i en återställningsregion som kan flyttas över till på kort Observera.

En DR-planen baserat på geo-replikering består av tre separata delar:
* Konfigurera-skapande och underhåll av Återställningsmiljö
* Recovery - redundans för appen och databaser till Återställningsmiljö om ett eventuellt strömavbrott
* Hemtransport - redundans för appen och databaserna tillbaka till den ursprungliga regionen när programmet har åtgärdats 

Alla delar måste planeras noggrant, särskilt om du kör i skala. Övergripande måste planen utföra flera mål:

* Konfiguration
    * Upprätta och upprätthålla en spegelbild miljö i återställningsregionen. Skapa elastiska pooler och replikering av alla enskilda databaser i den här Återställningsmiljö reserverar kapacitet i återställningsregionen. Underhåll av den här miljön innehåller replikera nya klientdatabaserna när de har etablerats.  
* Återställning
    * Om en skalas ned Återställningsmiljö används för att minimera dagliga kostnader, måste pooler och enskilda databaser skalas upp för att hämta fullständig driftskapacitet i återställningsregionen
    * Aktivera etableringen i återställningsregionen så snart som möjligt av nya klienter  
    * Optimeras för att återställa klienter i prioritetsordning
    * Optimeras för att hämta klienter online så snabbt som möjligt genom att göra steg parallellt där praktiska
    * Vara motståndskraftiga mot fel, kan startas om, och idempotenta
    * Går att avbryta processen i mitten rör sig om den ursprungliga regionen kommer tillbaka online.
* Hemtransport 
    * Växla över databaser från regionen som recovery till repliker i den ursprungliga regionen med minimal påverkan på klienter: inga data går förlorade och minimiperiod offline per klient.   

I de här självstudierna behandlas svårigheter med hjälp av funktionerna i Azure SQL Database och Azure-plattformen:

* [Azure Resource Manager-mallar](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template), för att reservera alla nödvändiga kapacitet så snabbt som möjligt. Azure Resource Manager-mallar används för att etablera en spegelbild av produktionsservrar och elastiska pooler i återställningsregionen.
* [GEO-replikering](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview), för att skapa asynkront replikerade sekundärservrar för alla databaser som skrivskyddad. Vid driftsstopp växlar du över till repliker i återställningsregionen.  När driftstörningarna har åtgärdats, växla tillbaka till databaserna i den ursprungliga regionen utan att förlora data.
* [Asynkron](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations) failover-åtgärder som skickas i klient-prioritetsordning, minimera redundanstiden för många databaser.
* [Shard för recovery hanteringsfunktioner](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-database-recovery-manager), ändra databasposter i katalogen under återställning och hemtransport. De här funktionerna kan appen att ansluta till klientdatabaser, oavsett plats utan att konfigurera om appen.
* [SQL server-DNS-alias](https://docs.microsoft.com/azure/sql-database/dns-alias-overview), för att aktivera sömlös etablering av nya klienter oavsett vilken region appen körs i. DNS-alias används också för att tillåta catalog synkroniseringen att ansluta till den aktiva katalogen oavsett var.

## <a name="get-the-disaster-recovery-scripts"></a>Hämta disaster recovery skript 

> [!IMPORTANT]
> Precis som alla Wingtip biljetter management skripten DR-skript är exempel kvalitet och som inte ska användas i produktion. 

Återställningen skripten som används i den här självstudien och Wingtip programmets källkod är tillgängliga i den [Wingtip biljetter SaaS-databas per klient GitHub-lagringsplatsen](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/). Kolla in den [allmänna riktlinjer](saas-tenancy-wingtip-app-guidance-tips.md) steg att ladda ned och avblockera management Wingtip biljettskripten.

## <a name="tutorial-overview"></a>Självstudier – översikt
I den här självstudien får använda du först geo-replikering för att skapa repliker av Wingtip biljetter programmet och dess databaser i en annan region. Sedan kan växla du över till den här regionen för att simulera återställning från ett avbrott. När du är färdig är programmet fungerar i återställningsregionen.

Senare, i ett separat hemtransport steg växlar över katalog- och klienttrafik databaserna i återställningsregionen till den ursprungliga regionen. Håll dig tillgängliga i hela hemtransport program och databaser. När du är klar är programmet helt funktionella i den ursprungliga regionen.

> [!Note]
> Programmet återställs till den _parad region_ för den region där programmet har distribuerats. Mer information finns i [parade Azure-regioner](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

## <a name="review-the-healthy-state-of-the-application"></a>Granska felfri statusen för programmet

Granska det normala felfritt tillståndet innan du startar återställningsprocessen.
1. Öppna i webbläsaren, Evenemangshubben Wingtip biljetter (http://events.wingtip-dpt.&lt; användare&gt;. trafficmanager.net - Ersätt &lt;användaren&gt; med värdet för användarnamn för din distribution).
    * Bläddra längst ned på sidan och Lägg märke till katalogen servernamnet och en plats i sidfoten. Platsen är den region där du har distribuerat appen.
    *Tips: Pekar på platsen för att visa.*
    ![Händelser hub felfritt tillstånd i ursprungliga region](media/saas-dbpertenant-dr-geo-replication/events-hub-original-region.png)

2. Klicka på Contosos Konserthall-klient och öppna appens sida för händelsen.
    * Observera klient servernamnet i sidfoten. Platsen ska vara samma som den katalogservern plats.

3. I den [Azure-portalen](https://portal.azure.com), öppna resursgruppen där appen har distribuerats
    * Observera den region där servrarna distribueras. 

## <a name="sync-tenant-configuration-into-catalog"></a>Synkronisera klientkonfiguration till katalog

I den här uppgiften ska starta du en process som synkroniserar konfigurationen av servrar, elastiska pooler och databaser till klient-katalogen. Processen ser den här informationen aktuell i katalogen.  Processen fungerar med den aktiva katalogen, vare sig den ursprungliga regionen eller i återställningsregionen. Konfigurationsinformationen används som en del av återställningsprocessen för att se till att Återställningsmiljön är konsekvent med den ursprungliga miljön och sedan senare under hemtransport att se till att den ursprungliga regionen blir konsekvent igen med ändringar som görs i den Återställningsmiljö. Katalogen används också för att hålla reda på återställningstillstånd av klienternas resurser

> [!IMPORTANT]
> För enkelhetens skull implementeras synkroniseringen och andra tidskrävande återställning och hemtransport i de här självstudierna som lokala Powershell-jobb eller sessioner som körs under din klient användarinloggning. Autentiseringstoken som utfärdas när du har loggat in upphör att gälla efter flera timmar och sedan jobb som misslyckas. I ett produktionsscenario för bör tidskrävande processer implementeras som tillförlitlig Azure-tjänster av något slag, som körs under ett huvudnamn för tjänsten. Se [med Azure PowerShell för att skapa ett huvudnamn för tjänsten med ett certifikat](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal).

1. I den _PowerShell ISE_, öppna filen ...\Learning Modules\UserConfig.psm1. Ersätt `<resourcegroup>` och `<user>` på raderna 10 och 11 med det värde som används när du distribuerade appen.  Spara filen!

2. I den *PowerShell ISE*genom att öppna ...\Learning Modules\Business affärskontinuitet och Haveriberedskap Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 skript och ange:
    * **$DemoScenario = 1**startar ett bakgrundsjobb som synkroniserar klientservern och programpoolen konfigurationsinformation i katalogen

3. Tryck på **F5** att köra skriptet synkronisering. Då öppnas en ny PowerShell-session för att synkronisera konfigurationen av klienternas resurser.
![Synkroniseringen](media/saas-dbpertenant-dr-geo-replication/sync-process.png)

Lämna fönstret PowerShell som körs i bakgrunden och fortsätter med resten av kursen. 

> [!Note]
> Synkroniseringen ansluter till katalog via ett DNS-alias. Det här aliaset har ändrats under återställning och hemtransport så att den pekar till den aktiva katalogen. Synkroniseringen håller katalogen uppdaterad med några databas eller pool konfigurationsändringar som gjorts i återställningsregionen.  Under hemtransport tillämpas ändringarna på motsvarande resurser i den ursprungliga regionen.

## <a name="create-secondary-database-replicas-in-the-recovery-region"></a>Skapa sekundära databasrepliker i återställningsregionen

I den här uppgiften ska starta du en process som distribuerar en duplicerad app-instansen och replikerar katalogen och alla klientdatabaser till en återställningsregion.

> [!Note]
> Den här självstudien lägger till skydd för geo-replikering till exempelprogrammet Wingtip biljetter. Varje klient skulle etableras med en geo-replikerad databas redan från början i ett produktionsscenario med för ett program som använder geo-replikering. Se [utforma högtillgängliga tjänster med Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-designing-cloud-solutions-for-disaster-recovery#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime)

1. I den *PowerShell ISE*genom att öppna ...\Learning Modules\Business affärskontinuitet och Haveriberedskap Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 skript och ange följande värden:
    * **$DemoScenario = 2**, skapa spegelbild Återställningsmiljö och replikera katalog- och klienttrafik databaser

2. Tryck **F5** för att köra skriptet. Då öppnas en ny PowerShell-session för att skapa replikerna.
![Synkroniseringen](media/saas-dbpertenant-dr-geo-replication/replication-process.png)  

## <a name="review-the-normal-application-state"></a>Granska det normala programtillståndet
Nu körs normalt i regionen ursprungliga programmet och nu är skyddat av geo-replikering.  Skrivskyddade sekundära repliker, finns i återställningsregionen för alla databaser. 
1. I Azure-portalen, titta på dina resursgrupper och Observera att gruppen har skapats med - recovery-suffix i återställningsregionen. 

1. Utforska resurser i resursgruppen för återställning.  

1. Klicka på Contosos Konserthall databasen på den _tenants1-dpt -&lt;användaren&gt;-recovery_ server.  Klicka på Geo-replikering till vänster. 

    ![Contoso samklang geo-replikeringslänken](media/saas-dbpertenant-dr-geo-replication/contoso-geo-replication.png) 

Observera länken geo-replikering mellan primärt i regionen ursprungliga och sekundärt i återställningsregionen i Azure-regioner-kartan.  

## <a name="fail-over-the-application-into-the-recovery-region"></a>Växla över program i återställningsregionen

### <a name="geo-replication-recovery-process-overview"></a>GEO-replikering översikt över återställningsprocessen

Återställningsskriptet utför följande uppgifter:

1. Inaktiverar Traffic Manager-slutpunkt för webbappen i regionen ursprungliga. Inaktivera slutpunkten förhindrar användare från att ansluta till appen i ett ogiltigt tillstånd bör den ursprungliga regionen är online under återställningen.

1. Använder en påtvingad redundans av katalogdatabasen i återställningsregionen så att de blir den primära databasen och uppdaterar den _activecatalog_ alias så att den pekar till katalogen återställningsservern.

1. Uppdateringar i _newtenant_ alias så att den pekar till klientservern i återställningsregionen. Ändra det här aliaset säkerställer att databaserna för alla nya klienter som har etablerats i återställningsregionen. 

1. Markerar alla befintliga klienter i katalogen recovery som offline om du vill förhindra åtkomst till klientdatabaser innan de har redundansväxlats.

1. Uppdaterar konfigurationen av alla elastiska pooler och replikerade enskilda databaser i återställningsregionen för spegling av deras konfiguration i den ursprungliga regionen. (Den här uppgiften krävs endast om pooler eller replikerade databaser i recovery environment skalas under normal drift för att minska kostnaderna).

1. Aktiverar Traffic Manager-slutpunkten för webbappen i återställningsregionen. Om du aktiverar den här slutpunkten kan programmet för att etablera nya klienter. I det här skedet är befintliga klienter fortfarande offline.

1. Skickar batchar av begäranden för att tvinga växlar över databaser i prioritetsordning.
    * Batchar ordnas så att databaserna har redundansväxlats parallellt över alla pooler.
    * Redundans begäranden skickas med asynkrona åtgärder så att de skickas snabbt och flera begäranden kan bearbetas samtidigt.

   > [!Note]
   > I ett scenario för avbrott är primära databaser i den ursprungliga regionen offline.  Force redundansväxlar sekundära bryter anslutningen till primärt utan att tillämpa alla återstående köade transaktioner. I ett DR-test scenario som den här självstudien om det inte finns någon aktivitet vid tidpunkten för redundans kan det finnas vissa data går förlorade. Senare under hemtransport används när du växlar över databaser i återställningsregionen tillbaka till den ursprungliga regionen normala redundans för att se till att det finns inga data går förlorade.

1. Övervakar SQL-databastjänst för att avgöra när databaser har redundansväxlats. När en klientdatabas har redundansväxlats uppdaterar katalogen för att registrera recovery staten klientdatabasen och markera klienten som online.
    * Klientdatabaser kan nås av programmet när de är märkta online i katalogen.
    * En summa av rowversion värden i klientdatabasen lagras i katalogen. Det här värdet fungerar som ett fingeravtryck där hemtransport processen för att avgöra om databasen har uppdaterats i återställningsregionen.

### <a name="run-the-script-to-fail-over-to-the-recovery-region"></a>Kör skript för att växla över till återställningsregionen

Nu imagine det uppstår ett avbrott i regionen där programmet distribueras och kör skriptet för återställning:

1. I den *PowerShell ISE*genom att öppna ...\Learning Modules\Business affärskontinuitet och Haveriberedskap Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 skript och ange följande värden:
    * **$DemoScenario = 3**, Återställ appen till en återställningsregion av växling till repliker

2. Tryck **F5** för att köra skriptet.  
    * Skriptet öppnas i ett nytt PowerShell-fönster och startar sedan en serie PowerShell-jobb som körs parallellt. Dessa jobb växlar över klientdatabaser till återställningsregionen.
    * Återställningsregionen är den _parad region_ som är associerade med Azure-regionen där du har distribuerat programmet. Mer information finns i [parade Azure-regioner](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

3. Övervaka status för återställningen i PowerShell-fönstret.
    ![Redundans](media/saas-dbpertenant-dr-geo-replication/failover-process.png)

> [!Note]
> Granska PowerShell-skript i mappen ...\Learning Modules\Business affärskontinuitet och Haveriberedskap Recovery\DR-FailoverToReplica\RecoveryJobs för att utforska koden för återställningsjobben.

### <a name="review-the-application-state-during-recovery"></a>Granska programmets tillstånd under återställningen
Programslutpunkten är inaktiverad i Traffic Manager, är programmet inte tillgänglig. När katalogen har redundansväxlats till återställningsregionen och alla klienter markeras offline, kan programmet tas online igen. Även om programmet är tillgängligt, visas varje klient som offline evenemangshubben förrän databasen har redundansväxlats. Det är viktigt att utforma ditt program hantera offline klientdatabaser.

1. Omgående när katalogdatabasen har återställts, uppdatera Evenemangshubben Wingtip biljetter i webbläsaren.
    * I sidfoten, Lägg märke till att namnet på katalogen har nu en _-recovery_ suffix och finns i återställningsregionen.
    * Observera att klienter som ännu inte återställs kan markeras som offline och inte är valbara.  

    > [!Note]
    > Med bara några få databaser att återställa, kan du inte att kunna uppdatera webbläsaren innan återställningen har slutförts, så att du inte kan se innehavarna när de är offline. 
 
    ![Evenemangshubben offline](media/saas-dbpertenant-dr-geo-replication/events-hub-offlinemode.png) 

    * Om du öppnar sidan för evenemang i en offline-klient direkt, visas ett meddelande om klient offline. Till exempel om Contosos Konserthall är offline försöker öppna http://events.wingtip-dpt.&lt; användare&gt;.trafficmanager.net/contosoconcerthall ![Contoso Offline sida](media/saas-dbpertenant-dr-geo-replication/dr-in-progress-offline-contosoconcerthall.png) 

### <a name="provision-a-new-tenant-in-the-recovery-region"></a>Etablera en ny klient i återställningsregionen
Du kan etablera nya klienter i återställningsregionen även innan alla befintliga klientdatabaser har redundansväxlats.  

1. I den *PowerShell ISE*genom att öppna ...\Learning Modules\Business affärskontinuitet och Haveriberedskap Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 skript och ange egenskapen följande:
    * **$DemoScenario = 4**, etablera en ny klient i återställningsregionen

2. Tryck på **F5** att köra skriptet och etablera den nya innehavaren. 

3. Sidan Hawthorn Hall händelser öppnas i webbläsaren när den är klar. Observera att Hawthorn Hall databasen etableras i återställningsregionen från sidfoten.
    ![Sidan för hawthorn Hall evenemang](media/saas-dbpertenant-dr-geo-replication/hawthornhallevents.png) 

4. Uppdatera Evenemangshubben för Wingtip biljetter sidan om du vill se Hawthorn Hall ingår i webbläsaren. 
    * Om du har etablerat Hawthorn Hall utan att vänta tills de andra klienterna att återställa andra klienter kan fortfarande att vara offline.


## <a name="review-the-recovered-state-of-the-application"></a>Granska återställda statusen för programmet

När återställningen är klar, programmet och alla klienter är helt funktionella i återställningsregionen. 

1. När visningen i PowerShell-konsolfönster visar alla klienter har återställts måste du uppdatera Evenemangshubben.  Klienterna kommer alla visas online, inklusive den nya innehavaren Hawthorn Hall.

    ![återställda och nya klienter i evenemangshubben](media/saas-dbpertenant-dr-geo-replication/events-hub-with-hawthorn-hall.png)

2. I den [Azure-portalen](https://portal.azure.com), öppna listan över resursgrupper.  
    * Lägg märke till den resursgrupp som du har distribuerat, plus resursgrupp recovery med den _-recovery_ suffix.  Recovery resursgruppen innehåller alla resurser som skapades under återställningen plus nya resurser som skapas under avbrottet.  

3. Öppna resursgruppen återställning och Lägg märke till följande objekt:
    * Recovery versioner av katalogen och tenants1-servrar, med _-recovery_ suffix.  De återställda katalog- och klienttrafik databaserna på dessa servrar som alla har de namn som används i den ursprungliga regionen.

    * Den _tenants2-dpt -&lt;användaren&gt;-recovery_ SQLServer.  Den här servern används för att etablera nya klienter under avbrottet.
    *   App Service med namnet _händelser-wingtip-dpt -&lt;recoveryregion&gt;-&lt;användare & gt_; vilket är evenemangsappen recovery-instansen. 

    ![Azure recovery-resurser ](media/saas-dbpertenant-dr-geo-replication/resources-in-recovery-region.png)    
    
4. Öppna den _tenants2-dpt -&lt;användaren&gt;-recovery_ SQLServer.  Observera att den innehåller databasen _hawthornhall_ och den elastiska poolen _Pool1_.  Den _hawthornhall_ databasen är konfigurerad som en elastisk databas i _Pool1_ elastisk pool.

5. Gå tillbaka till resursgruppen och klickar på Contosos Konserthall databasen på den _tenants1-dpt -&lt;användaren&gt;-recovery_ server. Klicka på Geo-replikering till vänster.
    
    ![Contoso databasen efter en redundansväxling](media/saas-dbpertenant-dr-geo-replication/contoso-geo-replication-after-failover.png)

## <a name="change-tenant-data"></a>Ändring av klientdata 
I den här uppgiften ska uppdatera du en av klientdatabaser. 

1. Hitta händelselistan för Contosos Konserthall i webbläsaren och Observera senaste händelsenamn.
2. I den *PowerShell ISE*, ange följande värde i ...\Learning Modules\Business affärskontinuitet och Haveriberedskap Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 skript:
    * **$DemoScenario = 5** ta bort en händelse från en klient i återställningsregionen
3. Tryck på **F5** att köra skriptet
4. Uppdatera sidan Contosos Konserthall händelser (http://events.wingtip-dpt.&lt; användare&gt;.trafficmanager.net/contosoconcerthall - ersätta &lt;användaren&gt; med värdet för användarnamn för din distribution) och Lägg märke till att den senaste händelsen har tagits bort.

## <a name="repatriate-the-application-to-its-original-production-region"></a>Hem programmet till dess ursprungliga produktionsregion

Den här uppgiften repatriates programmet till dess ursprungliga region. I ett scenario med verkliga skulle du initiera hemtransport när driftstörningarna har åtgärdats.

### <a name="repatriation-process-overview"></a>Översikt över hemtransport

![Hemtransport arkitektur](media/saas-dbpertenant-dr-geo-replication/repatriation-architecture.png)

Hemtransport processen:
1. Avbryter alla begäranden för återställning av utestående eller pågående databas.
2. Uppdateringar i _newtenant_ alias så att den pekar till klienternas server i regionen ursprung. Ändra det här aliaset säkerställer att databaserna för alla nya klienter som nu kommer att tillhandahållas i regionen ursprung.
3. Lägger till alla ändrade klientdata till den ursprungliga regionen
4. Växlar över klientdatabaser i prioritetsordning.

Redundansväxling flyttar effektivt databasen till den ursprungliga regionen. När databasen växlar måste alla öppna anslutningar tas bort och databasen är inte tillgänglig för några sekunder. Program ska skrivas med logik för omprövning av att se till att de ansluter igen.  Även om den här korta disconnect ofta inte märkt du hem databaser utanför arbetstid. 


### <a name="run-the-repatriation-script"></a>Kör skriptet hemtransport
Nu ska vi imagine avbrottet åtgärdats och kör skriptet hemtransport.

1. I den *PowerShell ISE*i den ...\Learning Modules\Business affärskontinuitet och Haveriberedskap Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 skript.

2. Kontrollera att katalogen synkroniseringsprocessen körs fortfarande i en PowerShell-instans.  Om det behövs kan du starta om den genom att ange:
    * **$DemoScenario = 1**, start synkronisera klient-server, pooler och konfigurationsinformation för databasen i katalogen
    * Tryck **F5** för att köra skriptet.

3.  Om du vill börja hemtransport, ange:
    * **$DemoScenario = 6**, hem appen till dess ursprungliga region
    * Tryck på **F5** att köra skriptet för återställning i ett nytt PowerShell-fönster.  Hemtransport tar några minuter och kan övervakas i PowerShell-fönstret.
    ![Hemtransport process](media/saas-dbpertenant-dr-geo-replication/repatriation-process.png)

4. När skriptet körs, uppdatera sidan Evenemangshubben (http://events.wingtip-dpt.&lt; användare&gt;. trafficmanager.net)
    * Observera att alla klienter är online och tillgängliga under hela processen.

5. När hemtransport är klar kan du uppdatera evenemangshubben och öppna sidan händelser för Hawthorn Hall. Observera att den här databasen har tagits återföras till den ursprungliga regionen.
    ![Evenemangshubben återföras](media/saas-dbpertenant-dr-geo-replication/events-hub-repatriated.png)


## <a name="designing-the-application-to-ensure-app-and-database-are-colocated"></a>Designa programmet så att app- och är samordnade 
Programmet har utformats så att den alltid ansluter från en instans i samma region som klientdatabasen. Den här designen minskar svarstider mellan programmet och databasen. Denna optimering antar att interaktionen app att databasen är chattier än användaren att appen interaktion.  

Klientdatabaser kan spridas för återställning och ursprungliga regioner under en viss tid under hemtransport. För varje databas letar appen du upp den region där databasen finns genom att göra en DNS-sökning på namnet på klienten. I SQL-databas är namnet på ett alias. Namnet på ett alias innehåller namnet på region. Om programmet inte finns i samma region som databasen, omdirigerar den till instansen i samma region som databasserver.  Omdirigera till instans i samma region som databasen minimerar fördröjning mellan appen och databasen. 

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:
> [!div classs="checklist"]

>* Synkronisera databaser och elastiska konfigurationsinformation för lagringspool i klient-katalogen
>* Konfigurera en Återställningsmiljö i en annan region, som består av program, servrar och pooler
>* Använd _geo-replikering_ att replikera katalogen och klient-databaser till återställningsregionen
>* Växla över programmet och katalogen och klient-databaser till återställningsregionen 
>* Återställa programmet, katalogen och klient-databaser till den ursprungliga regionen när driftstörningarna har åtgärdats

Du kan läsa mer om de tekniker som Azure SQL-databas tillhandahåller för att aktivera verksamhetskontinuitet i den [översikt över affärskontinuitet](sql-database-business-continuity.md) dokumentation.

## <a name="additional-resources"></a>Ytterligare resurser

* [Ytterligare självstudier som bygger på Wingtip SaaS-program](https://docs.microsoft.com/azure/sql-database/sql-database-wtp-overview#sql-database-wingtip-saas-tutorials)
