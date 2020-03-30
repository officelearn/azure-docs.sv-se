---
title: Haveriberedskap för SaaS-appar med Geo Replication
description: Lär dig hur du använder Georepliker i Azure SQL Database för att återställa en SaaS-app med flera innehavare i händelse av ett avbrott
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: AyoOlubeko
ms.author: craigg
ms.reviewer: sstein
ms.date: 01/25/2019
ms.openlocfilehash: 0668ccf5ceb972dd120e4e3f37be6d879a12d0a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73811708"
---
# <a name="disaster-recovery-for-a-multi-tenant-saas-application-using-database-geo-replication"></a>Haveriberedskap för ett SaaS-program med flera innehavare med hjälp av databasgeo-replikering

I den här självstudien utforskar du ett fullständigt scenario för haveriberedskap för ett SaaS-program med flera innehavare som implementerats med hjälp av modellen databas per klient. För att skydda appen från ett avbrott använder du [_geo-replikering_](sql-database-geo-replication-overview.md) för att skapa repliker för katalog- och klientdatabaserna i en alternativ återställningsregion. Om ett avbrott inträffar växlar du snabbt över till dessa repliker för att återuppta normala affärsåtgärder. Vid redundans blir databaserna i den ursprungliga regionen sekundära repliker av databaserna i återställningsregionen. När dessa repliker kommer tillbaka online de automatiskt komma ikapp tillståndet för databaserna i återställningsregionen. När avbrottet har lösts kan du inte gå tillbaka till databaserna i den ursprungliga produktionsregionen.

Den här självstudien utforskar både redundans- och redundansarbetsflödena. Du lär dig följande:
> [!div class="checklist"]
> 
> * Synkronisera information om databas- och elastisk poolkonfiguration i klientkatalogen
> * Konfigurera en återställningsmiljö i en alternativ region, som består av program, servrar och pooler
> * Använda _georeplikering_ för att replikera katalog- och klientdatabaserna till återställningsregionen
> * Växla över program- och katalog- och klientdatabaserna till återställningsregionen 
> * Senare växlas programmet, katalogen och klientdatabaserna tillbaka till den ursprungliga regionen efter att avbrottet har lösts
> * Uppdatera katalogen när varje klientdatabas inte kan spåra den primära platsen för varje klientdatabas
> * Se till att programmet och den primära klientdatabasen alltid samlokaliseras i samma Azure-region för att minska svarstiden  
 

Innan du startar den här självstudien kontrollerar du att följande förutsättningar är slutförda:
* Wingtip Tickets SaaS-databasen per klientapp distribueras. Information om hur du distribuerar på mindre än fem minuter finns i [Distribuera och utforska Wingtip Tickets SaaS-databasen per klientprogram](saas-dbpertenant-get-started-deploy.md)  
* Azure PowerShell ska ha installerats. Mer information finns i [Kom igång med Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-the-geo-replication-recovery-pattern"></a>Introduktion till återställningsmönstret för georeplikering

![Återställningsarkitektur](media/saas-dbpertenant-dr-geo-replication/recovery-architecture.png)
 
Haveriberedskap (DR) är en viktig faktor för många program, oavsett om det är av efterlevnadsskäl eller affärskontinuitet. Om det skulle bli ett långvarigt avbrott i tjänsten kan en väl förberedd DR-plan minimera avbrott i verksamheten. Med hjälp av geo-replikering ger den lägsta RPO och RTO genom att underhålla databasrepliker i en återställningsregion som kan överföras till med kort varsel.

En DR-plan baserad på georeplikering består av tre olika delar:
* Konfiguration - skapande och underhåll av återställningsmiljön
* Återställning - redundans för appen och databaser till återställningsmiljön om ett avbrott inträffar,
* Repatriering - redundans för appen och databaser tillbaka till den ursprungliga regionen när programmet har lösts 

Alla delar måste övervägas noggrant, särskilt om de arbetar i stor skala. Totalt sett måste planen uppnå flera mål:

* Installation
    * Upprätta och underhålla en spegelbildsmiljö i återställningsregionen. När du skapar elastiska pooler och replikerar alla databaser i den här återställningsmiljön reserveras kapacitet i återställningsregionen. Att underhålla den här miljön omfattar att replikera nya klientdatabaser när de etableras.  
* Återställning
    * Om en nedskalad återställningsmiljö används för att minimera de dagliga kostnaderna, måste pooler och databaser skalas upp för att få full operativ kapacitet i återställningsregionen
    * Aktivera ny klientetablering i återställningsregionen så snart som möjligt  
    * Optimeras för att återställa klienter i prioritetsordning
    * Optimeras för att få hyresgäster online så fort som möjligt genom att göra steg parallellt där praktiska
    * Var motståndskraftig mot fel, omstartbar och idempotent
    * Det är möjligt att avbryta processen i mitten av flygningen om den ursprungliga regionen kommer tillbaka online.
* Repatriering 
    * Redundans över databaser från återställningsregionen till repliker i den ursprungliga regionen med minimal påverkan på klienter: ingen dataförlust och minsta period offline per klient.   

I den här självstudien åtgärdas dessa utmaningar med hjälp av funktioner i Azure SQL Database och Azure-plattformen:

* [Azure Resource Manager-mallar](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template)för att reservera all nödvändig kapacitet så snabbt som möjligt. Azure Resource Manager-mallar används för att etablera en spegelavbildning av produktionsservrarna och elastiska pooler i återställningsregionen.
* [Geo-replikering](sql-database-geo-replication-overview.md), för att skapa asynkront replikerade skrivskyddade sekundärer för alla databaser. Under ett avbrott växlar du över till replikerna i återställningsregionen.  När avbrottet har lösts kan du återgå till databaserna i den ursprungliga regionen utan dataförlust.
* [Asynkrona](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations) redundansåtgärder som skickas i klientprioritetsordning, för att minimera redundanstiden för ett stort antal databaser.
* [Shard management recovery features](sql-database-elastic-database-recovery-manager.md), to change database entries in the catalog during recovery and repatriation. Med de här funktionerna kan appen ansluta till klientdatabaser oavsett plats utan att konfigurera om appen.
* [SQL server DNS-alias](dns-alias-overview.md), för att möjliggöra sömlös etablering av nya klienter oavsett vilken region appen är verksam i. DNS-alias används också för att katalogsynkroniseringsprocessen ska kunna ansluta till den aktiva katalogen oavsett var den finns.

## <a name="get-the-disaster-recovery-scripts"></a>Hämta skript för haveriberedskap 

> [!IMPORTANT]
> Liksom alla Wingtip Biljetter förvaltning skript, DR skript är prov kvalitet och får inte användas i produktionen. 

Återställningsskripten som används i den här självstudien och Wingtip-programmets källkod är tillgängliga i [Wingtip Tickets SaaS-databasen per GitHub-databas för klienten](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/). Kolla in den [allmänna vägledningen](saas-tenancy-wingtip-app-guidance-tips.md) för steg för att ladda ner och låsa upp Wingtip Tickets management skript.

## <a name="tutorial-overview"></a>Självstudier – översikt
I den här självstudien använder du först georeplikering för att skapa repliker av Wingtip-biljetter-programmet och dess databaser i en annan region. Sedan kan du växla över till den här regionen för att simulera återställning från ett avbrott. När det är klart är programmet fullt fungerande i återställningsregionen.

Senare, i ett separat repatrieringssteg, växlar du över katalogen och klientdatabaserna i återställningsregionen till den ursprungliga regionen. Ansökan och databaser förblir tillgängliga under hela hemtransport. När det är klart är programmet fullt fungerande i den ursprungliga regionen.

> [!Note]
> Programmet återställs till den _parade regionen_ i den region där programmet distribueras. Mer information finns i [Azure-parade regioner](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

## <a name="review-the-healthy-state-of-the-application"></a>Granska programmets felfria tillstånd

Innan du startar återställningsprocessen bör du granska programmets normala felfria tillstånd.
1. Öppnahttp://events.wingtip-dpt.&ltHub för Händelser i webbläsaren ( ,användare&gt;.trafficmanager.net – ersätta &lt;&gt; användaren med distributionens användarvärde).
    * Bläddra längst ned på sidan och lägg märke till katalogserverns namn och plats i sidfoten. Platsen är den region där du distribuerade appen.
    *TIPS: Håll musen över platsen för att förstora skärmen.* 
    Felfritt tillstånd för händelser i den ursprungliga ![regionen](media/saas-dbpertenant-dr-geo-replication/events-hub-original-region.png)

2. Klicka på Contoso Concert Hall hyresgäst och öppna sin händelse sida.
    * Lägg märke till klientserverns namn i sidfoten. Platsen kommer att vara samma som katalogserverns plats.

3. Öppna resursgruppen där appen distribueras i [Azure-portalen](https://portal.azure.com)
    * Lägg märke till den region där servrarna distribueras. 

## <a name="sync-tenant-configuration-into-catalog"></a>Synkronisera klientkonfigurationen i katalogen

I den här uppgiften startar du en process som synkroniserar konfigurationen av servrar, elastiska pooler och databaser i klientkatalogen. Processen håller den här informationen uppdaterad i katalogen.  Processen fungerar med den aktiva katalogen, oavsett om den är i den ursprungliga regionen eller i återställningsregionen. Konfigurationsinformationen används som en del av återställningsprocessen för att säkerställa att återställningsmiljön är förenlig med den ursprungliga miljön, och senare under repatriering för att säkerställa att den ursprungliga regionen görs i överensstämmelse med eventuella ändringar som görs i återhämtningsmiljö. Katalogen används också för att hålla reda på återställningstillståndet för klientresurser

> [!IMPORTANT]
> För enkelhetens skull implementeras synkroniseringsprocessen och andra tidskrävande återställnings- och repatrieringsprocesser i dessa självstudier som lokala PowerShell-jobb eller sessioner som körs under klientanvändarinloggningen. Autentiseringstoken som utfärdas när du loggar in upphör att gälla efter flera timmar och jobben misslyckas sedan. I ett produktionsscenario bör tidskrävande processer implementeras som tillförlitliga Azure-tjänster av något slag och köras under ett huvudnamn för tjänsten. Se [Använda Azure PowerShell för att skapa ett tjänsthuvudnamn med ett certifikat](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal).

1. Öppna filen ...\Learning Modules\UserConfig.psm1 i _PowerShell ISE._ Ersätt `<resourcegroup>` `<user>` och på raderna 10 och 11 med det värde som användes när du distribuerade appen.  Spara filen!

2. Öppna *PowerShell ISE*skriptet ...\Learning-moduler\Business Continuity and Disaster Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 och ange:
    * **$DemoScenario = 1**, Starta ett bakgrundsjobb som synkroniserar klientserver och poolkonfigurationsinformation i katalogen

3. Tryck på **F5** för att köra synkroniseringsskriptet. En ny PowerShell-session öppnas för att synkronisera konfigurationen av klientresurser.
![Synkroniseringsprocess](media/saas-dbpertenant-dr-geo-replication/sync-process.png)

Lämna PowerShell-fönstret som körs i bakgrunden och fortsätt med resten av självstudien. 

> [!Note]
> Synkroniseringsprocessen ansluter till katalogen via ett DNS-alias. Det här aliaset ändras under återställning och repatriering för att peka på den aktiva katalogen. Synkroniseringsprocessen håller katalogen uppdaterad med alla ändringar av databas- eller poolkonfiguration som görs i återställningsregionen.  Under repatriering tillämpas dessa ändringar på motsvarande resurser i den ursprungliga regionen.

## <a name="create-secondary-database-replicas-in-the-recovery-region"></a>Skapa sekundära databasrepliker i återställningsregionen

I den här uppgiften startar du en process som distribuerar en dubblettappinstans och replikerar katalogen och alla klientdatabaser till en återställningsregion.

> [!Note]
> Den här självstudien lägger till geo-replikeringsskydd i exempelprogrammet Wingtip Tickets. I ett produktionsscenario för ett program som använder geo-replikering, skulle varje klient etableras med en geo-replikerad databas från början. Se [Utforma tjänster med högtillgänge med Azure SQL Database](sql-database-designing-cloud-solutions-for-disaster-recovery.md#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime)

1. Öppna *skriptet*...\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 och ange följande värden:
    * **$DemoScenario = 2**, Skapa spegelavbildningsåterställningsmiljö och replikera katalog- och klientdatabaser

2. Tryck **F5** för att köra skriptet. En ny PowerShell-session öppnas för att skapa replikerna.
![Synkroniseringsprocess](media/saas-dbpertenant-dr-geo-replication/replication-process.png)  

## <a name="review-the-normal-application-state"></a>Granska det normala programtillståndet

Nu körs programmet normalt i den ursprungliga regionen och skyddas nu av geo-replikering.  Skrivskyddade sekundära repliker, finns i återställningsregionen för alla databaser. 

1. I Azure-portalen tittar du på dina resursgrupper och noterar att en resursgrupp har skapats med -recovery-suffix i återställningsregionen. 

2. Utforska resurserna i resursgruppen för återställning.  

3. Klicka på Contoso Concert Hall-databasen på _tenants1-dpt-&lt;&gt;user -recovery_ server.  Klicka på Geo-Replication till vänster. 

    ![Georeplikeringslänk för Contoso-konsert](media/saas-dbpertenant-dr-geo-replication/contoso-geo-replication.png) 

I azure-områdeskartan noterar du geo-replikeringslänken mellan den primära i den ursprungliga regionen och den sekundära i återställningsregionen.  

## <a name="fail-over-the-application-into-the-recovery-region"></a>Växla över programmet i återställningsregionen

### <a name="geo-replication-recovery-process-overview"></a>Översikt över återställningsprocess för georeplikering

Återställningsskriptet utför följande uppgifter:

1. Inaktiverar Slutpunkten för Traffic Manager för webbappen i den ursprungliga regionen. Om du inaktiverar slutpunkten hindras användare från att ansluta till appen i ett ogiltigt tillstånd om den ursprungliga regionen skulle anslutas under återställningen.

1. Använder en kraftväxling av katalogdatabasen i återställningsregionen för att göra den till den primära databasen och uppdaterar _activecatalog-aliaset_ för att peka på återställningskatalogservern.

1. Uppdaterar det _nya aliaset_ för att peka på klientservern i återställningsregionen. Om du ändrar det här aliaset säkerställs att databaserna för alla nya klienter etableras i återställningsregionen. 

1. Markerar alla befintliga klienter i återställningskatalogen som offline för att förhindra åtkomst till klientdatabaser innan de misslyckades.

1. Uppdaterar konfigurationen av alla elastiska pooler och replikerade enskilda databaser i återställningsregionen för att spegla deras konfiguration i den ursprungliga regionen. (Den här uppgiften behövs bara om pooler eller replikerade databaser i återställningsmiljön skalas ned under normala åtgärder för att minska kostnaderna).

1. Aktiverar Slutpunkten för Traffic Manager för webbappen i återställningsregionen. Om du aktiverar den här slutpunkten kan programmet etablera nya klienter. I det här skedet är befintliga klienter fortfarande offline.

1. Skickar batchar med begäranden om att tvinga över misslyckade databaser i prioritetsordning.
    * Batchar är ordnade så att databaser kan komma över parallellt i alla pooler.
    * Redundansbegäranden skickas med asynkrona åtgärder så att de skickas snabbt och flera begäranden kan bearbetas samtidigt.

   > [!Note]
   > I ett avbrottsscenario är de primära databaserna i den ursprungliga regionen offline.  Tvinga överväxling på den sekundära bryter anslutningen till den primära utan att försöka tillämpa kvarvarande kötransaktioner. I en DR-borr scenario som den här självstudien, om det finns någon uppdatering aktivitet vid tidpunkten för redundans kan det finnas vissa dataförlust. Senare, under repatriering, när du växlar över databaser i återställningsregionen tillbaka till den ursprungliga regionen, används en normal redundans för att säkerställa att det inte finns någon dataförlust.

1. Övervakar SQL-databastjänsten för att avgöra när databaser har misslyckats över. När en klientdatabas har misslyckats över uppdateras katalogen för att registrera återställningstillståndet för klientdatabasen och markera klienten som online.
    * Klientdatabaser kan nås av programmet så snart de är markerade online i katalogen.
    * En summa rowversion-värden i klientdatabasen lagras i katalogen. Det här värdet fungerar som ett fingeravtryck som gör att repatrieringsprocessen kan avgöra om databasen har uppdaterats i återställningsregionen.

### <a name="run-the-script-to-fail-over-to-the-recovery-region"></a>Kör skriptet för att växla över till återställningsregionen

Föreställ dig nu att det finns ett avbrott i den region där programmet distribueras och kör återställningsskriptet:

1. Öppna *skriptet*...\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 och ange följande värden:
    * **$DemoScenario = 3**, Återställ appen till en återställningsregion genom att gå över till repliker

2. Tryck **F5** för att köra skriptet.  
    * Skriptet öppnas i ett nytt PowerShell-fönster och startar sedan en serie PowerShell-jobb som körs parallellt. Dessa jobb växlar över klientdatabaser till återställningsregionen.
    * Återställningsregionen är den parade region som är _associerad_ med Azure-regionen där du distribuerade programmet. Mer information finns i [Azure-parade regioner](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

3. Övervaka status för återställningsprocessen i PowerShell-fönstret.
    ![redundansprocess](media/saas-dbpertenant-dr-geo-replication/failover-process.png)

> [!Note]
> Om du vill utforska koden för återställningsjobben läser du PowerShell-skripten i mappen ...\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\RecoveryJobs.

### <a name="review-the-application-state-during-recovery"></a>Granska programtillståndet under återställningen

Medan programslutpunkten är inaktiverad i Traffic Manager är programmet inte tillgängligt. När katalogen har misslyckats över till återställningsregionen och alla klienter markerade offline, är programmet online igen. Även om programmet är tillgängligt visas varje klient offline i händelsehubben tills databasen har misslyckats över. Det är viktigt att utforma ditt program för att hantera offline klientdatabaser.

1. Uppdatera eventhubben för Wingtip Tickets Events Hub i webbläsaren direkt efter att katalogdatabasen har återställts.
   * I sidfoten märker du att katalogservernamnet nu har ett _-recovery-suffix_ och finns i återställningsregionen.
   * Observera att klienter som ännu inte har återställts, markeras som offline och inte kan väljas.  

     > [!Note]
     > Med bara ett fåtal databaser att återställa kanske du inte kan uppdatera webbläsaren innan återställningen har slutförts, så du kanske inte ser klienterna när de är offline. 
 
     ![Händelsehubben offline](media/saas-dbpertenant-dr-geo-replication/events-hub-offlinemode.png) 

   * Om du öppnar en offlineklients sida direkt visas ett "klient offline"-meddelande. Om Contoso Concert Hall till exempel är http://events.wingtip-dpt.&ltoffline&gt;försöker du ![öppna sidan ;användare .trafficmanager.net/contosoconcerthall Contoso Offline](media/saas-dbpertenant-dr-geo-replication/dr-in-progress-offline-contosoconcerthall.png) 

### <a name="provision-a-new-tenant-in-the-recovery-region"></a>Etablera en ny klient i återställningsregionen
Redan innan alla befintliga klientdatabaser har misslyckats över kan du etablera nya klienter i återställningsregionen.  

1. Öppna *skriptet*...\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 och ange följande egenskap:
    * **$DemoScenario = 4**, Etablera en ny klient i återställningsregionen

2. Tryck på **F5** för att köra skriptet och etablera den nya klienten. 

3. Evenemangssidan för Hawthorn Hall öppnas i webbläsaren när den är klar. Observera från sidfoten att Hawthorn Hall-databasen är etablerad i återställningsregionen.
    ![Hawthorn Hall Evenemang Sida](media/saas-dbpertenant-dr-geo-replication/hawthornhallevents.png) 

4. I webbläsaren uppdaterar du sidan Wingtip Tickets Events Hub för att se Hawthorn Hall inkluderad. 
    * Om du etablerat Hawthorn Hall utan att vänta på de andra hyresgästerna att återställa, kan andra hyresgäster fortfarande vara offline.


## <a name="review-the-recovered-state-of-the-application"></a>Granska programmets återställda tillstånd

När återställningsprocessen är klar är programmet och alla klienter fullt fungerande i återställningsregionen. 

1. När skärmen i PowerShell-konsolfönstret visar att alla klienter har återställts uppdaterar du händelsehubben.  Hyresgästerna kommer alla att visas på nätet, inklusive den nya hyresgästen, Hawthorn Hall.

    ![återvunna och nya klienter i händelsehubben](media/saas-dbpertenant-dr-geo-replication/events-hub-with-hawthorn-hall.png)

2. Öppna listan över resursgrupper i [Azure-portalen.](https://portal.azure.com)  
    * Lägg märke till den resursgrupp som du har distribuerat, plus återställningsresursgruppen, med _-recovery-suffixet._  Resursgruppen för återställning innehåller alla resurser som skapats under återställningsprocessen, plus nya resurser som skapats under avbrottet.  

3. Öppna resursgruppen för återställning och lägg märke till följande objekt:
   * Återställningsversionerna av katalogen och klienterna1 servrar, med _-recovery_ suffix.  Den återställda katalogen och klientdatabaserna på dessa servrar har alla de namn som används i den ursprungliga regionen.

   * _Den&lt;tenants2-dpt-&gt;användare -recovery_ SQL-server.  Den här servern används för att etablera nya klienter under avbrottet.
   * Apptjänsten heter, _&lt;events-wingtip-dpt-&gt;-&lt;recoveryregion user&gt_;, som är återställningsinstansen för appen Händelser. 

     ![Azure-återställningsresurser](media/saas-dbpertenant-dr-geo-replication/resources-in-recovery-region.png) 
    
4. Öppna _sql-servern för&lt;klienten2-dpt- användare&gt;-återställning._  Lägg märke till att den innehåller databasen _hawthornhall_ och den elastiska poolen, _Pool1_.  _Hawthornhall-databasen_ är konfigurerad som en elastisk databas i _Pool1_ elastisk pool.

5. Navigera tillbaka till resursgruppen och klicka på Contoso Concert _Hall-databasen&lt;&gt;på klienterna1-dpt- user -recovery_ server. Klicka på Geo-Replication till vänster.
    
    ![Contoso-databas efter redundans](media/saas-dbpertenant-dr-geo-replication/contoso-geo-replication-after-failover.png)

## <a name="change-tenant-data"></a>Ändra klientdata 
I den här uppgiften uppdaterar du en av klientdatabaserna. 

1. I din webbläsare hittar du händelselistan för Contoso Concert Hall och noterar det efterbrickt händelsenamnet.
2. I *PowerShell ISE*anger du följande värde i skriptet ...\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1:
    * **$DemoScenario = 5** Ta bort en händelse från en klient i återställningsregionen
3. Tryck på **F5** för att köra skriptet
4. Uppdatera händelsesidan för Contosohttp://events.wingtip-dpt.&ltConcert&gt;Hall ( ;user &lt;&gt; .trafficmanager.net/contosoconcerthall - ersätt användaren med distributionens användarvärde) och observera att den senaste händelsen har tagits bort.

## <a name="repatriate-the-application-to-its-original-production-region"></a>Skicka tillbaka ansökan till den ursprungliga produktionsregionen

Den här uppgiften skickar tillbaka programmet till den ursprungliga regionen. I ett verkligt scenario skulle du initiera repatriering när avbrottet är löst.

### <a name="repatriation-process-overview"></a>Översikt över repatrieringsprocessen

![Repatriering Arkitektur](media/saas-dbpertenant-dr-geo-replication/repatriation-architecture.png)

Repatrieringsprocessen:
1. Avbryter alla utestående eller under flygning databasåterställningsbegäranden.
2. Uppdaterar det _nya aliaset_ för att peka på klientservern i ursprungsregionen. Om du ändrar det här aliaset säkerställs att databaserna för alla nya klienter nu etableras i ursprungsregionen.
3. Frön alla ändrade klientdata till den ursprungliga regionen
4. Misslyckas över klientdatabaser i prioritetsordning.

Redundans flyttar databasen effektivt till den ursprungliga regionen. När databasen växlar över tas alla öppna anslutningar bort och databasen är inte tillgänglig i några sekunder. Program bör skrivas med återförsök logik för att säkerställa att de ansluter igen.  Även om denna korta frånkoppling ofta inte märks, kan du välja att repatriera databaser utanför kontorstid. 


### <a name="run-the-repatriation-script"></a>Kör repatrieringsskriptet
Nu ska vi föreställa oss att avbrottet är löst och köra repatrieringsskriptet.

1. I *PowerShell ISE*visas skriptet ...\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1.

2. Kontrollera att katalogsynkroniseringsprocessen fortfarande körs i powershell-instansen.  Om det behövs startar du om den genom att ställa in:
    * **$DemoScenario = 1**, Börja synkronisera information om klientserver, pool och databaskonfiguration i katalogen
    * Tryck **F5** för att köra skriptet.

3.  Sedan för att starta repatrieringsprocessen, ställ in:
    * **$DemoScenario = 6**, Repatriera appen till den ursprungliga regionen
    * Tryck på **F5** för att köra återställningsskriptet i ett nytt PowerShell-fönster.  Repatriering tar flera minuter och kan övervakas i PowerShell-fönstret.
    ![Repatrieringsprocessen](media/saas-dbpertenant-dr-geo-replication/repatriation-process.png)

4. Medan skriptet körs uppdaterar du sidanhttp://events.wingtip-dpt.&ltHändelserhub (;användare&gt;.trafficmanager.net)
    * Observera att alla klienter är online och tillgängliga under hela den här processen.

5. När hemtransporten är klar uppdaterar du hubben Händelser och öppnar evenemangssidan för Hawthorn Hall. Observera att databasen har skickats tillbaka till den ursprungliga regionen.
    ![Händelsenavet har skickats tillbaka](media/saas-dbpertenant-dr-geo-replication/events-hub-repatriated.png)


## <a name="designing-the-application-to-ensure-app-and-database-are-colocated"></a>Utforma programmet för att säkerställa att appen och databasen är samlokaliseras 
Programmet är utformat så att det alltid ansluter från en instans i samma region som klientdatabasen. Den här designen minskar svarstiden mellan programmet och databasen. Den här optimeringen förutsätter att interaktionen mellan appar till databasen är pratsamare än interaktionen mellan användare och appar.  

Klientdatabaser kan spridas över återställning och ursprungliga regioner under en tid under repatriering. För varje databas slår appen upp det område där databasen finns genom att göra en DNS-sökning på klientserverns namn. I SQL Database är servernamnet ett alias. Det aliaserade servernamnet innehåller regionnamnet. Om programmet inte finns i samma region som databasen omdirigeras det till instansen i samma region som databasservern.  Omdirigering till instans i samma region som databasen minimerar svarstiden mellan app och databas. 

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:
> [!div class="checklist"]
> 
> * Synkronisera information om databas- och elastisk poolkonfiguration i klientkatalogen
> * Konfigurera en återställningsmiljö i en alternativ region, som består av program, servrar och pooler
> * Använda _georeplikering_ för att replikera katalog- och klientdatabaserna till återställningsregionen
> * Växla över program- och katalog- och klientdatabaserna till återställningsregionen 
> * Red inte tillbaka program-, katalog- och klientdatabaserna till den ursprungliga regionen efter att avbrottet har lösts

Du kan läsa mer om de tekniker som Azure SQL-databasen tillhandahåller för att aktivera affärskontinuitet i dokumentationen för översikt [över affärskontinuitet.](sql-database-business-continuity.md)

## <a name="additional-resources"></a>Ytterligare resurser

* [Ytterligare självstudier som bygger på Wingtip SaaS-programmet](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
