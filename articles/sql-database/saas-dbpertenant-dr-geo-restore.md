---
title: 'SaaS-appar: Geoundundanta säkerhetskopior för haveriberedskap'
description: Lär dig att använda geosanta säkerhetskopior i Azure SQL Database för att återställa en SaaS-app med flera innehavare i händelse av ett avbrott
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: AyoOlubeko
ms.author: craigg
ms.reviewer: sstein
ms.date: 01/14/2019
ms.openlocfilehash: 270fc157fa14efa19ed30d35b614fb769804b72e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73826461"
---
# <a name="use-geo-restore-to-recover-a-multitenant-saas-application-from-database-backups"></a>Använd geoåterställning för att återställa ett SaaS-program med flera innehavare från säkerhetskopiering av databaser

Den här självstudien utforskar ett fullständigt katastrofåterställningsscenario för ett SaaS-program med flera innehavare som implementerats med databasen per klientmodell. Du använder [geo-återställning](sql-database-recovery-using-backups.md) för att återställa katalogen och klientdatabaser från automatiskt underhållna geo-redundanta säkerhetskopior till en alternativ återställningsregion. När avbrottet har lösts använder du [geo-replikering](sql-database-geo-replication-overview.md) för att skicka tillbaka ändrade databaser till den ursprungliga regionen.

![Geo-återställande-arkitektur](media/saas-dbpertenant-dr-geo-restore/geo-restore-architecture.png)

Geoåterställning är den billigaste katastrofåterställningslösningen för Azure SQL Database. Återställning från geo-redundanta säkerhetskopior kan dock resultera i dataförlust på upp till en timme. Det kan ta lång tid, beroende på storleken på varje databas. 

> [!NOTE]
> Återställ program med lägsta möjliga RPO och RTO med hjälp av geo-replikering i stället för geo-återställning.

Den här självstudien utforskar både återställnings- och repatrieringsarbetsflöden. Lär dig att:
> [!div class="checklist"]
> 
> * Synkronisera databas- och elastisk poolkonfigurationsinformation i klientkatalogen.
> * Konfigurera en spegelbildsmiljö i en återställningsregion som innehåller program, servrar och pooler.   
> * Återställ katalog- och klientdatabaser med hjälp av geo-återställning.
> * Använd geo-replikering för att skicka tillbaka klientkatalogen och ändrade klientdatabaser när avbrottet har lösts.
> * Uppdatera katalogen när varje databas återställs (eller skickas tillbaka) för att spåra den aktuella platsen för den aktiva kopian av varje klients databas.
> * Kontrollera att program- och klientdatabasen alltid finns i samma Azure-region för att minska svarstiden. 
 

Innan du startar den här självstudien ska du fylla i följande förutsättningar:
* Distribuera Wingtip Tickets SaaS-databasen per klientapp. Information om hur du distribuerar på mindre än fem minuter finns i [Distribuera och utforska Wingtip Tickets SaaS-databasen per klientprogram](saas-dbpertenant-get-started-deploy.md). 
* Installera Azure PowerShell. Mer information finns i [Komma igång med Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-geo-restore-recovery-pattern"></a>Introduktion till återställningsmönstret för geoåterställning

Haveriberedskap (DR) är en viktig faktor för många program, oavsett om det är av efterlevnadsskäl eller affärskontinuitet. Om det finns ett långvarigt avbrott i tjänsten kan en väl förberedd DR-plan minimera avbrott i verksamheten. En DR-plan baserad på geoåterställning måste uppnå flera mål:
 * Reservera all nödvändig kapacitet i den valda återställningsregionen så snabbt som möjligt för att säkerställa att den är tillgänglig för att återställa klientdatabaser.
 * Upprätta en spegelbildsåterställningsmiljö som återspeglar den ursprungliga poolen och databaskonfigurationen. 
 * Tillåt inbokning av återställningsprocessen i mitten av flygningen om den ursprungliga regionen är online igen.
 * Aktivera klientetablering snabbt så att den nya klientens introduktion kan startas om så snart som möjligt.
 * Optimeras för att återställa klienter i prioritetsordning.
 * Optimeras för att få hyresgäster online så snart som möjligt genom att göra steg parallellt där det är praktiskt möjligt.
 * Var motståndskraftig mot fel, omstartbar och idempotent.
 * Skicka tillbaka databaser till den ursprungliga regionen med minimal påverkan på klienter när avbrottet har åtgärdats.  

> [!NOTE]
> Programmet återställs till den parade regionen i den region där programmet distribueras. Mer information finns i [Azure-parade regioner](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).   

Den här självstudien använder funktioner i Azure SQL Database och Azure-plattformen för att hantera dessa utmaningar:

* [Azure Resource Manager-mallar](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template)för att reservera all nödvändig kapacitet så snabbt som möjligt. Azure Resource Manager-mallar används för att etablera en spegelavbildning av de ursprungliga servrarna och elastiska pooler i återställningsregionen. En separat server och pool skapas också för etablering av nya klienter.
* [Elastiskt databasklientbibliotek](sql-database-elastic-database-client-library.md) (EDCL) för att skapa och underhålla en klientdatabaskatalog. Den utökade katalogen innehåller regelbundet uppdaterad pool- och databaskonfigurationsinformation.
* [Shard management recovery features](sql-database-elastic-database-recovery-manager.md) of the EDCL, to maintain database locationtries in the catalog during recovery and repatriation.  
* [Geo-återställning](sql-database-disaster-recovery.md), för att återställa katalogen och klientdatabaser från automatiskt underhållna geo-redundanta säkerhetskopior. 
* [Asynkrona återställningsåtgärder](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations), som skickas i klientprioritetsordning, köas för varje pool av systemet och bearbetas i batchar så att poolen inte överbelastas. Dessa åtgärder kan avbrytas före eller under körningen om det behövs.   
* [Geo-replikering](sql-database-geo-replication-overview.md), för att skicka tillbaka databaser till den ursprungliga regionen efter avbrottet. Det finns ingen dataförlust och minimal påverkan på klienten när du använder geo-replikering.
* [SQL Server DNS-alias](dns-alias-overview.md), så att katalogsynkroniseringsprocessen kan ansluta till den aktiva katalogen oavsett var den finns.  

## <a name="get-the-disaster-recovery-scripts"></a>Hämta skript för haveriberedskap

DR-skripten som används i den här självstudien är tillgängliga i [Wingtip Tickets SaaS-databasen per GitHub-databas för klienten](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant). Kolla in den [allmänna vägledningen](saas-tenancy-wingtip-app-guidance-tips.md) för steg för att ladda ner och låsa upp Wingtip Tickets management skript.

> [!IMPORTANT]
> Liksom alla Wingtip Biljetter förvaltning skript, DR skript är prov kvalitet och får inte användas i produktionen.

## <a name="review-the-healthy-state-of-the-application"></a>Granska programmets felfria tillstånd
Innan du startar återställningsprocessen bör du granska programmets normala felfria tillstånd.

1. Öppna händelsehubben för Wingtip-biljetterhttp://events.wingtip-dpt.&lti&gt;webbläsaren ( ;user &lt;.trafficmanager.net, ersätt användaren&gt; med distributionens användarvärde).
    
   Bläddra längst ned på sidan och lägg märke till katalogserverns namn och plats i sidfoten. Platsen är den region där du distribuerade appen.    

   > [!TIP]
   > Håll musen över platsen för att förstora skärmen.

   ![Felfritt tillstånd för händelser i den ursprungliga regionen](media/saas-dbpertenant-dr-geo-restore/events-hub-original-region.png)

2. Välj Contoso Concert Hall-klienten och öppna dess evenemangssida.

   Lägg märke till klientens servernamn i sidfoten. Platsen är densamma som katalogserverns plats.

   ![Contoso Konserthus ursprungliga regionen](media/saas-dbpertenant-dr-geo-restore/contoso-original-location.png) 

3. Granska och öppna resursgruppen där du distribuerade appen i [Azure-portalen.](https://portal.azure.com)

   Lägg märke till de resurser och den region där apptjänstkomponenterna och SQL Database-servrarna distribueras.

## <a name="sync-the-tenant-configuration-into-the-catalog"></a>Synkronisera klientkonfigurationen i katalogen

I den här uppgiften startar du en process för att synkronisera konfigurationen av servrar, elastiska pooler och databaser i klientkatalogen. Den här informationen används senare för att konfigurera en spegelbildsmiljö i återställningsregionen.

> [!IMPORTANT]
> För enkelhetens skull implementeras synkroniseringsprocessen och andra tidskrävande återställnings- och repatrieringsprocesser i dessa exempel som lokala PowerShell-jobb eller sessioner som körs under klientanvändarinloggningen. Autentiseringstoken som utfärdas när du loggar in upphör att gälla efter flera timmar och jobben misslyckas sedan. I ett produktionsscenario bör tidskrävande processer implementeras som tillförlitliga Azure-tjänster av något slag och köras under ett huvudnamn för tjänsten. Se [Använda Azure PowerShell för att skapa ett tjänsthuvudnamn med ett certifikat](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal). 

1. Öppna filen ...\Learning Modules\UserConfig.psm1 i PowerShell ISE. Ersätt `<resourcegroup>` `<user>` och på raderna 10 och 11 med det värde som användes när du distribuerade appen. Spara filen.

2. Öppna skriptet ...\Learning-moduler\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 i PowerShell ISE.

    I den här självstudien kör du alla scenarier i det här PowerShell-skriptet, så håll den här filen öppen.

3. Ställ in följande:

    $DemoScenario = 1: Starta ett bakgrundsjobb som synkroniserar klientserver- och poolkonfigurationsinformation i katalogen.

4. Om du vill köra synkroniseringsskriptet väljer du F5. 

    Den här informationen används senare för att säkerställa att återställning skapar en spegelbild av servrar, pooler och databaser i återställningsregionen.  

    ![Synkroniseringsprocess](media/saas-dbpertenant-dr-geo-restore/sync-process.png)

Lämna PowerShell-fönstret som körs i bakgrunden och fortsätt med resten av den här självstudien.

> [!NOTE]
> Synkroniseringsprocessen ansluter till katalogen via ett DNS-alias. Aliaset ändras under återställning och repatriering för att peka på den aktiva katalogen. Synkroniseringsprocessen håller katalogen uppdaterad med alla ändringar av databas- eller poolkonfiguration som gjorts i återställningsregionen. Under repatriering tillämpas dessa ändringar på motsvarande resurser i den ursprungliga regionen.

## <a name="geo-restore-recovery-process-overview"></a>Översikt över återställningsprocess för geoåterställning

Återställningsprocessen för geoåterställning distribuerar programmet och återställer databaser från säkerhetskopior till återställningsregionen.

Återställningsprocessen gör följande:

1. Inaktiverar slutpunkten för Azure Traffic Manager för webbappen i den ursprungliga regionen. Om du inaktiverar slutpunkten hindras användare från att ansluta till appen i ett ogiltigt tillstånd om den ursprungliga regionen skulle anslutas under återställningen.

2. Etablerar en återställningskatalogserver i återställningsregionen, geo-återställer katalogdatabasen och uppdaterar activecatalog-aliaset så att det pekar på den återställda katalogservern. Genom att ändra katalogaliaset synkroniseras katalogsynkningen alltid till den aktiva katalogen.

3. Markerar alla befintliga klienter i återställningskatalogen som offline för att förhindra åtkomst till klientdatabaser innan de återställs.

4. Etablerar en instans av appen i återställningsregionen och konfigurerar den för att använda den återställda katalogen i den regionen. För att hålla svarstiden till ett minimum är exempelappen utformad för att alltid ansluta till en klientdatabas i samma region.

5. Etablerar en server och elastisk pool där nya klienter etableras. Genom att skapa dessa resurser säkerställer du att etablering av nya klienter inte stör återställningen av befintliga klienter.

6. Uppdaterar det nya klientaliaset för att peka på servern för nya klientdatabaser i återställningsregionen. Om du ändrar det här aliaset säkerställs att databaser för alla nya klienter etableras i återställningsregionen.
        
7. Bestämmelser servrar och elastiska pooler i återställningsregionen för att återställa klientdatabaser. Dessa servrar och pooler är en spegelbild av konfigurationen i den ursprungliga regionen. Etableringspooler på framsidan reserverar den kapacitet som behövs för att återställa alla databaser.

    Ett avbrott i en region kan sätta ett betydande tryck på de resurser som är tillgängliga i den parade regionen. Om du förlitar dig på geoåterställning för DR rekommenderas du att reservera resurser snabbt. Överväg geo-replikering om det är viktigt att ett program återställs i en viss region. 

8. Aktiverar Slutpunkten för Traffic Manager för webbappen i återställningsregionen. Om du aktiverar den här slutpunkten kan programmet etablera nya klienter. I det här skedet är befintliga klienter fortfarande offline.

9. Skickar batchar med begäranden om att återställa databaser i prioritetsordning. 

    * Batchar är ordnade så att databaser återställs parallellt i alla pooler.  

    * Återställningsbegäranden skickas asynkront så att de skickas in snabbt och köas för körning i varje pool.

    * Eftersom återställningsbegäranden bearbetas parallellt mellan alla pooler är det bättre att distribuera viktiga klienter över många pooler. 

10. Övervakar SQL Database-tjänsten för att avgöra när databaser återställs. När en klientdatabas har återställts markeras den online i katalogen och en rowversionsumma för klientdatabasen registreras. 

    * Klientdatabaser kan nås av programmet så snart de är markerade online i katalogen.

    * En summa rowversion-värden i klientdatabasen lagras i katalogen. Den här summan fungerar som ett fingeravtryck som gör det möjligt för repatrieringsprocessen att avgöra om databasen har uppdaterats i återställningsregionen.       

## <a name="run-the-recovery-script"></a>Kör återställningsskriptet

> [!IMPORTANT]
> Den här självstudien återställer databaser från geo-redundanta säkerhetskopior. Även om dessa säkerhetskopior vanligtvis är tillgängliga inom 10 minuter kan det ta upp till en timme. Skriptet pausas tills de är tillgängliga.

Föreställ dig att det finns ett avbrott i den region där programmet distribueras och kör återställningsskriptet:

1. I powershell ISE anger du följande värde i skriptet ...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1:

    $DemoScenario = 2: Återställ appen till en återställningsregion genom att återställa från geouppsagda säkerhetskopior.

2. Om du vill köra skriptet väljer du F5.  

    * Skriptet öppnas i ett nytt PowerShell-fönster och startar sedan en uppsättning PowerShell-jobb som körs parallellt. Dessa jobb återställer servrar, pooler och databaser till återställningsregionen.

    * Återställningsregionen är den parade region som är associerad med Azure-regionen där du distribuerade programmet. Mer information finns i [Azure-parade regioner](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

3. Övervaka status för återställningsprocessen i PowerShell-fönstret.

    ![Återställningsprocess](media/saas-dbpertenant-dr-geo-restore/dr-in-progress.png)

> [!NOTE]
> Om du vill utforska koden för återställningsjobben läser du PowerShell-skripten i mappen ...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\RecoveryJobs.

## <a name="review-the-application-state-during-recovery"></a>Granska programtillståndet under återställningen
Medan programslutpunkten är inaktiverad i Traffic Manager är programmet inte tillgängligt. Katalogen återställs och alla klienter markeras offline. Programslutpunkten i återställningsregionen aktiveras sedan och programmet är online igen. Även om programmet är tillgängligt visas klienter offline i händelsehubben tills deras databaser återställs. Det är viktigt att utforma ditt program för att hantera offline klientdatabaser.

* När katalogdatabasen har återställts men innan klienterna är online igen uppdaterar du händelsehubben Wingtip Tickets i webbläsaren.

  * I sidfoten märker du att katalogservernamnet nu har ett -recovery-suffix och finns i återställningsregionen.

  * Observera att klienter som ännu inte har återställts är markerade som offline och inte kan väljas.   
 
    ![Återställningsprocess](media/saas-dbpertenant-dr-geo-restore/events-hub-tenants-offline-in-recovery-region.png)    

  * Om du öppnar en klients händelsesida direkt medan klienten är offline visas en offlineavisering för klienten. Om Contoso Concert Hall till exempel är http://events.wingtip-dpt.&ltoffline&gt;försöker du öppna ;användare .trafficmanager.net/contosoconcerthall.

    ![Återställningsprocess](media/saas-dbpertenant-dr-geo-restore/dr-in-progress-offline-contosoconcerthall.png)

## <a name="provision-a-new-tenant-in-the-recovery-region"></a>Etablera en ny klient i återställningsregionen
Redan innan klientdatabaser återställs kan du etablera nya klienter i återställningsregionen. Nya klientdatabaser som etablerats i återställningsregionen skickas tillbaka med de återställda databaserna senare.   

1. I powershell ISE anger du följande egenskap i skriptet ...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1:

    $DemoScenario = 3: Etablera en ny klient i återställningsregionen.

2. Om du vill köra skriptet väljer du F5.

3. Evenemangssidan för Hawthorn Hall öppnas i webbläsaren när etableringen är klar. 

    Observera att Hawthorn Hall-databasen finns i återställningsregionen.

    ![Hawthorn Hall etableras i återhämtningsregionen](media/saas-dbpertenant-dr-geo-restore/hawthorn-hall-provisioned-in-recovery-region.png)

4. I webbläsaren uppdaterar du hubbsidan för Wingtip Tickets-evenemang för att se Hawthorn Hall inkluderad. 

    Om du etablerat Hawthorn Hall utan att vänta på de andra hyresgästerna att återställa, kan andra hyresgäster fortfarande vara offline.

## <a name="review-the-recovered-state-of-the-application"></a>Granska programmets återställda tillstånd

När återställningsprocessen är klar är programmet och alla klienter fullt fungerande i återställningsregionen. 

1. När skärmen i PowerShell-konsolfönstret visar att alla klienter har återställts uppdaterar du händelsehubben. 

    Hyresgästerna visas alla på nätet, inklusive den nya hyresgästen, Hawthorn Hall.

    ![Återställda och nya klienter i händelsehubben](media/saas-dbpertenant-dr-geo-restore/events-hub-with-hawthorn-hall.png)

2. Klicka på Contoso Konserthus och öppna dess evenemangssida. 

    I sidfoten observerar du att databasen finns på återställningsservern som finns i återställningsregionen.

    ![Contoso i återställningsregionen](media/saas-dbpertenant-dr-geo-restore/contoso-recovery-location.png)

3. Öppna listan över resursgrupper i [Azure-portalen.](https://portal.azure.com)  

    Lägg märke till den resursgrupp som du har distribuerat, plus återställningsresursgruppen, med -recovery-suffixet. Resursgruppen för återställning innehåller alla resurser som skapats under återställningsprocessen, plus nya resurser som skapats under avbrottet. 

4. Öppna resursgruppen för återställning och lägg märke till följande objekt:

   * Återställningsversionerna av katalogen och klienterna1 servrar, med -recovery-suffixet. Den återställda katalogen och klientdatabaserna på dessa servrar har alla de namn som används i den ursprungliga regionen.

   * Den&lt;tenants2-dpt-&gt;användare -recovery SQL-server. Den här servern används för att etablera nya klienter under avbrottet.

   * Apptjänsten som heter&lt;händelse-wingtip-dpt- recoveryregion-användare&gt;-&lt;&gt;, vilket är återställningsinstansen för händelseappen.

     ![Contoso-resurser i återställningsregionen](media/saas-dbpertenant-dr-geo-restore/resources-in-recovery-region.png) 
    
5. Öppna sql-servern för&lt;klienten2-dpt- användare&gt;-återställning. Observera att den innehåller databasen hawthornhall och den elastiska poolen Pool1. Hawthornhall-databasen är konfigurerad som en elastisk databas i den elastiska poolen Pool1.

## <a name="change-the-tenant-data"></a>Ändra klientdata 
I den här uppgiften uppdaterar du en av de återställda klientdatabaserna. Repatrieringsprocessen kopierar återställda databaser som har ändrats till den ursprungliga regionen. 

1. I din webbläsare hittar du evenemangslistan för Contoso Concert Hall, bläddrar igenom händelserna och märker den sista händelsen, Allvarligt Strauss.

2. I powershell ISE anger du följande värde i skriptet ...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1:

    $DemoScenario = 4: Ta bort en händelse från en klient i återställningsregionen.

3. Om du vill köra skriptet väljer du F5.

4. Uppdatera Contoso Concert Hallhttp://events.wingtip-dpt.&lthändelser&gt;sida ( , användare .trafficmanager.net/contosoconcerthall), och märker att händelsen Allvarligt Strauss saknas.

Vid denna punkt i självstudien har du återställt programmet, som nu körs i återställningsregionen. Du har etablerat en ny klient i återställningsregionen och ändrat data för en av de återställda klienterna.  

> [!NOTE]
> Andra självstudier i exemplet är inte utformade för att köras med appen i återställningstillståndet. Om du vill utforska andra tutorials, se till att repatriera programmet först.

## <a name="repatriation-process-overview"></a>Översikt över repatrieringsprocessen

Repatrieringsprocessen återställer programmet och dess databaser till den ursprungliga regionen efter att ett avbrott har lösts.

![Restaurering av geoåterställning](media/saas-dbpertenant-dr-geo-restore/geo-restore-repatriation.png) 

Processen:

1. Stoppar all pågående återställningsaktivitet och avbryter alla utestående eller under flygning databasåterställningsbegäranden.

2. Reactivates i den ursprungliga regionen klientdatabaser som inte har ändrats sedan avbrottet. Dessa databaser inkluderar de som inte har återställts ännu och de som återställts men inte ändrats efteråt. De återaktiverade databaserna är exakt som senast nås av sina klienter.

3. Etablerar en spegelbild av den nya klientens server och elastiska pool i den ursprungliga regionen. När den här åtgärden är klar uppdateras det nya klientaliaset för att peka på den här servern. Om du uppdaterar aliaset uppstår ny klientanknlänning i den ursprungliga regionen i stället för återställningsregionen.

3. Använder georeplikering för att flytta katalogen till den ursprungliga regionen från återställningsregionen.

4. Uppdaterar poolkonfigurationen i den ursprungliga regionen så att den överensstämmer med ändringar som har gjorts i återställningsregionen under avbrottet.

5. Skapar de servrar och pooler som krävs för att vara värd för alla nya databaser som skapats under avbrottet.

6. Använder geo-replikering för att repatriera återställda klientdatabaser som har uppdaterats efter återställning och alla nya klientdatabaser som etablerats under avbrottet. 

7. Rensar resurser som skapats i återställningsregionen under återställningsprocessen.

För att begränsa antalet klientdatabaser som måste skickas tillbaka görs steg 1 till 3 omgående.  

Steg 4 görs bara om katalogen i återställningsregionen har ändrats under avbrottet. Katalogen uppdateras om nya klienter skapas eller om någon databas- eller poolkonfiguration ändras i återställningsregionen.

Det är viktigt att steg 7 orsakar minimala störningar för klienter och inga data går förlorade. För att uppnå detta mål använder processen geo-replikering.

Innan varje databas geo-replikeras tas motsvarande databas i det ursprungliga området bort. Databasen i återställningsregionen geo-replikeras sedan, vilket skapar en sekundär replik i den ursprungliga regionen. När replikeringen är klar markeras klienten offline i katalogen, som bryter alla anslutningar till databasen i återställningsregionen. Databasen misslyckades sedan över, vilket gör att eventuella väntande transaktioner bearbetas på den sekundära så att inga data går förlorade. 

Vid redundans är databasrollerna omvända. Den sekundära i den ursprungliga regionen blir den primära läs-skriv-databasen och databasen i återställningsregionen blir en skrivskyddad sekundär. Klientposten i katalogen uppdateras för att referera till databasen i den ursprungliga regionen och klienten markeras online. Nu är återsändandet av databasen klar. 

Program bör skrivas med återförsökslogik för att säkerställa att de återansluter automatiskt när anslutningar bryts. När de använder katalogen för att förmedla återanslutningen ansluter de till den repatrierade databasen i den ursprungliga regionen. Även om den korta frånkopplingen ofta inte märks kan du välja att skicka tillbaka databaser utanför kontorstid.

När en databas har skickats tillbaka kan den sekundära databasen i återställningsregionen tas bort. Databasen i den ursprungliga regionen förlitar sig sedan igen på geo-återställning för DR-skydd.

I steg 8 tas resurser i återställningsregionen, inklusive återställningsservrar och pooler, bort.

## <a name="run-the-repatriation-script"></a>Kör repatrieringsskriptet
Låt oss föreställa oss att avbrottet är löst och köra repatrieringsskriptet.

Om du har följt handledningen, skriptet omedelbart reactivates Fabrikam Jazz Club och Dogwood Dojo i den ursprungliga regionen eftersom de är oförändrade. Det repatrierar sedan den nya hyresgästen, Hawthorn Hall, och Contoso Concert Hall eftersom det har ändrats. Manuset repatrierar också katalogen, som uppdaterades när Hawthorn Hall etablerades.
  
1. I PowerShell ISE, i skriptet ...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1, kontrollerar du att katalogsynkroniseringsprocessen fortfarande körs i powershell-instansen. Om det behövs startar du om den genom att ställa in:

    $DemoScenario = 1: Börja synkronisera information om klientserver, pool och databaskonfiguration i katalogen.

    Om du vill köra skriptet väljer du F5.

2.  Sedan för att starta repatrieringsprocessen, ställ in:

    $DemoScenario = 5: Skicka tillbaka appen till den ursprungliga regionen.

    Om du vill köra återställningsskriptet i ett nytt PowerShell-fönster väljer du F5. Repatriering tar flera minuter och kan övervakas i PowerShell-fönstret.

3. När skriptet körs uppdaterar du sidanhttp://events.wingtip-dpt.&lthändelserhubb ( ;användare&gt;.trafficmanager.net).

    Observera att alla klienter är online och tillgängliga under hela den här processen.

4. Välj Fabrikam Jazz Club för att öppna den. Om du inte har ändrat klienten lägger du märke till från sidfoten att servern redan har återställts till den ursprungliga servern.

5. Öppna eller uppdatera evenemangssidan contosokonserthus. Meddelande från sidfoten att databasen till en början fortfarande finns på återställningsservern. 

6. Uppdatera evenemangssidan för Contoso Concert Hall när repatrieringsprocessen är klar och observera att databasen nu finns i den ursprungliga regionen.

7. Uppdatera evenemangshubben igen och öppna Hawthorn Hall. Observera att dess databas också finns i den ursprungliga regionen. 

## <a name="clean-up-recovery-region-resources-after-repatriation"></a>Rensa upp återhämtning region resurser efter repatriering
När repatriering har slutförts är det säkert att ta bort resurserna i återställningsregionen. 

> [!IMPORTANT]
> Ta bort dessa resurser omgående för att stoppa all fakturering för dem.

Återställningsprocessen skapar alla återställningsresurser i en återställningsresursgrupp. Rensningsprocessen tar bort den här resursgruppen och tar bort alla referenser till resurserna från katalogen. 

1. I PowerShell ISE, i skriptet ...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1, ange:
    
    $DemoScenario = 6: Ta bort föråldrade resurser från återställningsregionen.

2. Om du vill köra skriptet väljer du F5.

När du har rensat skripten är programmet tillbaka där det började. Nu kan du köra skriptet igen eller prova andra självstudier.

## <a name="designing-the-application-to-ensure-that-the-app-and-the-database-are-co-located"></a>Utforma programmet för att säkerställa att appen och databasen är samlokala 
Programmet är utformat för att alltid ansluta från en instans i samma region som klientens databas. Den här designen minskar svarstiden mellan programmet och databasen. Den här optimeringen förutsätter att interaktionen mellan appar till databasen är pratsamare än interaktionen mellan användare och appar.  

Klientdatabaser kan spridas över återställning och ursprungliga regioner under en tid under repatriering. För varje databas slår appen upp det område där databasen finns genom att göra en DNS-sökning på klientserverns namn. I SQL Database är servernamnet ett alias. Det aliaserade servernamnet innehåller regionnamnet. Om programmet inte finns i samma region som databasen omdirigeras det till instansen i samma region som databasservern. Omdirigering till instansen i samma region som databasen minimerar svarstiden mellan appen och databasen.  

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:
> [!div class="checklist"]
> 
> * Använd klientkatalogen för att innehålla regelbundet uppdaterad konfigurationsinformation, vilket gör att en spegelbildsåterställningsmiljö kan skapas i en annan region.
> * Återställa Azure SQL-databaser till återställningsregionen med hjälp av geo-återställning.
> * Uppdatera klientkatalogen så att den återspeglar återställda klientdatabasplatser. 
> * Använd ett DNS-alias för att aktivera ett program för att ansluta till klientkatalogen hela utan omkonfigurering.
> * Använd geo-replikering för att skicka tillbaka återställda databaser till sin ursprungliga region efter att ett avbrott har lösts.

Prova [haveriberedskapen för ett SaaS-program med flera egenskaper med hjälp av självstudiekurs för georeplikation](saas-dbpertenant-dr-geo-replication.md) för att lära dig hur du använder georeplikering för att dramatiskt minska den tid som krävs för att återställa ett storskaligt multitenantprogram.

## <a name="additional-resources"></a>Ytterligare resurser

[Ytterligare självstudier som bygger på Wingtip SaaS-programmet](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
