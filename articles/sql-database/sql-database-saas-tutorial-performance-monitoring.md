---
title: "Övervaka prestanda hos en SQL Database SaaS-app  | Microsoft Docs"
description: "Övervaka och hantera prestanda för Azure SQL Database-exempelappen Wingtip Tickets (WTP)"
keywords: sql database tutorial
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: tutorial
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 05/10/2017
ms.author: billgib; sstein
ms.translationtype: Human Translation
ms.sourcegitcommit: fc4172b27b93a49c613eb915252895e845b96892
ms.openlocfilehash: af9511978718af10c97bee6af3a2835c9d2c1ff4
ms.contentlocale: sv-se
ms.lasthandoff: 05/12/2017


---
# <a name="monitor-performance-of-the-wtp-sample-saas-application"></a>Övervaka prestandan för WTP SaaS-exempelprogrammet

I den här guiden demonstrerar vi de inbyggda övervaknings- och aviseringsfunktionerna i SQL Database och elastiska pooler och vi tar upp flera nyckelscenarier för prestandahantering som används i SaaS-program.

Wingtip biljettappen använder sig av en datamodell med en klient, där varje lokal (klient) har sin egen databas. Precis som för flera SaaS-program så är de förväntade belastningsmönstren för klienterna oberäkneliga och sporadiska. Biljettförsäljningar kan med andra ord ske när som helst. För att dra nytta av det här typiska databasanvändningsmönstret, distribueras klientdatabaserna i elastiska databaspooler. Elastiska pooler optimerar kostnaderna för en lösning genom att dela resurser över flera databaser. Med den här typen av mönster är det viktigt att övervaka användningen av databas- och poolresurser för att försäkra att belastningarna balanseras över poolerna. Du behöver också se till att enskilda databaser har tillräckliga resurser och att pooler inte träffar sina [eDTU](sql-database-what-is-a-dtu.md)-gränser. Den här guiden går igenom sätt att övervaka och hantera databaser och pooler och hur man vidtar åtgärder i respons på belastningsvariationer.

I den här guiden får du lära dig hur man:

> [!div class="checklist"]

> * Simulerar användning på klientdatabaserna genom att köra en angiven belastningsgenerator
> * Övervakar klientdatabaserna när de svarar på belastningsökningar
> * Skalar upp den elastiska poolen i svar på den ökade databasbelastningen
> * Etablerar en andra elastisk pool för att belastningsutjämna databasaktiviteten


Se till att följande förhandskrav är slutförda för att kunna slutföra den här guiden:

* WTP-appen ska ha distribuerats. Du kan distribuera appen på mindre än fem minuter genom att följa anvisningarna i [Distribuera och utforska WTP SaaS-appen](sql-database-saas-tutorial.md)
* Azure PowerShell ska ha installerats. Mer information finns i [Kom igång med Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-saas-performance-management-patterns"></a>Introduktion till SaaS-prestandahanteringsmönster

Hantering av databasprestanda innebär kompilering och analys av prestandadata för att sedan reagera på den genom att justera parametrarna för att bibehålla en acceptabel svarstid för programmet. När du är värd för flera klientorganisationer, är elastiska databaspooler ett kostnadseffektivt sätt att tillhandahålla och hantera resurser för en grupp databaser med oförutsägbara arbetsbelastningar. Med vissa arbetsbelastningmönster, kan så få som två S3-databaser dra nytta av att hanteras i en pool. En pool gör inte bara att du delar kostnaden för resurserna, du kan också eliminera behovet att ständigt övervaka och spåra individuella databaser.

![media](./media/sql-database-saas-tutorial-performance-monitoring/app-diagram.png)

Pooler och databaser i pooler måste övervakas för att säkerställa att de håller sig inom acceptabla prestandaintervall. Justera poolkonfigurationen för att uppfylla behoven för den sammanställda arbetsbelastningen och se till att pool-eDTU:er är lämpliga för den övergripande arbetsbelastningen. Justera per databas min och max eDTU-värden till lämpliga värden för dina specifika programkrav.

### <a name="performance-management-strategies"></a>Strategier för prestandahantering

* Om du vill undvika att behöva manuellt övervaka prestanda, är det bäst att **ställa in aviseringar som utlöses om databaser eller pooler avviker från normala intervall**.
* För att svara på kortsiktiga variationer i sammansatt prestandanivå för en pool, kan **poolens eDTU-nivå skalas upp eller ned**. Om de här variationerna inträffar på regelbunden eller förutsägbar basis, **kan skalning av poolen schemaläggas att ske automatiskt**. Du kan exempelvis skala ned när du vet att din arbetsbelastning är lätt, på nätter eller helger till exempel.
* För att svara på mer långsiktiga variationer eller förändringar av antalet databaser, **kan enskilda databaser flyttas till andra pooler**.
* För att svara på kortsiktiga ökningar i *enskild* databasbelastning, **kan enskilda databaser tas bort från en pool och tilldelas en individuell prestandanivå** under en period. När belastningen minskar, kan databasen returneras till poolen. När det här är känt i förväg, kan databaser flyttas i förebyggande syfte för att tillse att databasen alltid har de resurser som den behöver och för att undvika påverkan på andra databaser i poolen. Om det här kravet är förutsägbart, till exempel en plats där ett populärt evenemang skapar en rusning efter biljetter, kan det här hanteringsbeteendet integreras i programmet.

[Azure-portalen](https://portal.azure.com) tillhandahåller inbyggd övervakning och avisering för de flesta resurser. För SQL Database, finns övervakning och avisering tillgängligt för databaser och pooler. Den här inbyggda övervakningen och aviseringen är resursspecifik så den är praktisk att använda sig av för ett litet antal resurser, men mindre praktisk när du arbetar med många resurser.

För scenarion med stora volymer, kan Log Analytics (även kallat OMS) användas. Det här är en separat Azure-tjänst som tillhandahåller analys för utgivna diagnostikloggar och telemetri som samlats in i en arbetsyta för logganalys. Den samlar telemetri från flera olika tjänster som sedan kan användas för att fråga och ställa in aviseringar.

## <a name="get-the-wingtip-application-scripts"></a>Hämta Wingtip-programskripten

Wingtip biljettskripten och programmets källkod finns tillgängliga från github-repon [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS). Skriptfilerna finns i [mappen inlärningsmoduler](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules). Ladda ner mappen **inlärningsmoduler** till din lokala dator med sin mappstruktur intakt.

## <a name="provision-additional-tenants"></a>Etablera ytterligare klienter

Pooler kan vara kostnadseffektiva med bara två S3 databaser, men ju fler databaser som finns i poolen, desto mer kostnadseffektiv blir genomsnittseffekten. För att få en bra förståelse för hur prestandaövervakning och hantering fungerar i stor skala, kräver den här kursen du har distribuerat minst 20 databaser.

Om du redan har etablerat en batch med klienter i en tidigare guide, kan du hoppa till avsnittet [simulera användning på alla klientdatabaser](#simulate-usage-on-all-tenant-databases).

1. Öppna... \\inlärningsmoduler\\etablera och katalogisera\\*Demo-PerformanceMonitoringAndManagement.ps1* i **PowerShell ISE**. Ha det här skriptet öppet medan du kör scenarierna i den här guiden.
1. Ställ in **$DemoScenario** = **1**, **etablera en batch med klienter**
1. Tryck **F5** för att köra skriptet.

Skriptet distribuerar 17 klienter på mindre än fem minuter.

Skriptet *New-TenantBatch* använder sig av en kapslad eller länkad uppsättning med [Resource Manager](../azure-resource-manager/index.md)-mallar som skapar en batch med klienter. Som standard så kopierar det databasen **baseTenantDb** på katalogservern för att skapa de nya klientdatabaserna och registrerar sedan dessa i katalogen. Slutligen initieras de med klientnamn och platstyp. Det här är likadant som hur WTP-appen etablerar en ny klient. Ändringar som görs i *baseTenantDB* tillämpas på alla nya klienter som etableras därefter. Se [schemahanteringsguiden](sql-database-saas-tutorial-schema-management.md) för att se hur du gör schemaändringar för *befintliga* klientdatabaser (inklusive den *gyllene* databasen).

## <a name="simulate-different-usage-patterns-by-generating-different-load-types"></a>Simulera olika användningsmönster genom att generera olika belastningstyper

Skriptet*Demo-PerformanceMonitoringAndManagement.ps1* startar belastningsgeneratorn med någon av de tillgängliga *belastningstyperna*:

| Demo | Scenario |
|:--|:--|
| 2 | Generera belastning av normal intensitet (ca 40 DTU) |
| 3 | Generera belastning med längre och mer frekventa toppar per databas|
| 4 | Generera belastningar med högre DTU-toppar per databas (cirka 80 DTU)|
| 5 | Generera en normal belastning plus en hög belastning på en enskild klient (cirka 95 DTU)|
| 6 | Generera obalanserad belastning över flera pooler|

## <a name="simulate-usage-on-all-tenant-databases"></a>Simulera användning på alla klientdatabaser

Belastningsgeneratorn tillämpar en *syntetisk* enbart-CPU-belastning på varje klientdatabas. Generatorn startar ett jobb för varje klientdatabas som anropar en lagrad procedur med jämna mellanrum, vilket genererar belastningen. Belastningsnivåer (i eDTU:er), varaktighet och intervaller varierar över alla databaser, vilket simulerar oförutsägbar klientaktivitet.

1. Ställ in **$DemoScenario** = **2**, *Generera en belastning av normal intensitet*.
1. Tryck på **F5** för att tillämpa en belastning på alla dina klientdatabaser.

På grund av den sporadiska naturen hos belastningen, är det bästa att låta generatorn köra i 10-20 minuter så att aktiviteten kan uppnå ett stabilt tillstånd och uppnå ett bra mönster.

> [!IMPORTANT]
> Belastningsgeneratorn körs som en serie jobb i din lokala PowerShell-session. Behåll fliken *Demo-PerformanceMonitoringAndManagement.ps1* öppen! Om du stänger fliken eller pausar din dator, stannar belastningsgeneratorn.

## <a name="monitor-resource-usage-using-the-portal"></a>Övervaka resursanvändning med portalen

För att övervaka resursanvändning som resulterar av den tillämpade belastningen, öppnar du portalen till poolen som innehåller klientdatabaserna.

1. Öppna [Azure-portalen](https://portal.azure.com) och bläddra till servern tenants1-&lt;användare&gt;.
1. Du borde se listan över klientdatabaser, inklusive den nya batchen med databaser.
1. Bläddra ned och hitta elastiska pooler och klicka på **Pool1**. Den här poolen innehåller alla klientdatabaser som skapats än så länge.
1. Expandera pool-bladet som öppnas och notera diagrammen över poolanvändning och högsta databasanvändning.

Poolanvändningen är i princip sammanställningen av databasanvändningen för alla databaser i poolen. Diagrammet över databasanvändning visar de 5 hetaste databaserna:

![](./media/sql-database-saas-tutorial-performance-monitoring/pool1.png)

Eftersom det finns andra databaser i poolen utöver de högsta 5, visar poolanvändningen aktivitet som inte visas i diagrammet över de fem viktigaste databaserna. För att se ytterligare information, klickar du på **databasresursanvändning**:

![](./media/sql-database-saas-tutorial-performance-monitoring/database-utilization.png)


## <a name="set-performance-alerts-on-the-pool"></a>Ställ in prestandaaviseringar för poolen

Gör på följande sätt för att ställa in en avisering för poolen som utlöses vid \>75% utnyttjande som varar i 5 minuter:

1. Öppna *Pool1* (på servern *tenants1-\<användare\>*) i [Azure-portalen](https://portal.azure.com).
1. Klicka på **aviseringsregler** och därefter på **+ lägg till avisering**:

   ![lägg till avisering](media/sql-database-saas-tutorial-performance-monitoring/add-alert.png)

1. Ange ett namn, exempelvis **hög DTU**, 
1. Ställ in följande värden:
   * **Mått = eDTU procent**
   * **Villkor = större än**.
   * **Tröskelvärdet = 75**.
   * **Period = de senaste 30 minuterna**.

   ![ange varning](media/sql-database-saas-tutorial-performance-monitoring/alert-rule.png)

Du kan ha meddelanden som skickas till e-postadressen för ditt Azure-konto och alternativt andra e-postadresser också (det rekommenderas att du inte ställer in det här, om du inte äger prenumerationen som används).

> [!NOTE]
> Eftersom aviseringen bara utlöses om tröskelvärdet överstigs under de senaste 30 minuterna, måste belastningsgeneratorn köras i över 30 minuter för att testa aviseringen.


## <a name="scale-up-a-busy-pool"></a>Skala upp en upptagen pool

Om den sammanställda belastningsnivån ökar på en pool till den punkt där poolen maxas ut och den når 100% eDTU-användning så påverkas individuell databasprestanda, vilket kan sakta ner frågesvarstiderna för alla databaser i poolen.

Kortsiktigt så bör du överväga att skala upp poolen för att lägga till resurser, eller ta bort databaser från poolen (flytta dem till andra pooler, eller bort från poolen till en fristående tjänstnivå).

Långsiktigt bör du överväga att optimera frågorna eller indexanvändningen för att förbättra databasprestandan. Beroende på programmets känslighet för prestandaproblem, är det praxis att skala upp en pool innan den når 100% eDTU-användning. Använd en avisering för att varna dig i förväg.

Du kan simulera en upptagen pool genom att öka belastningen som generatorn skapar. Om du får databasen att toppa oftare och längre, ökar du den sammanställda belastningen för poolen utan att ändra kraven för individuella databaser. Det är enkelt att skala upp poolen i portalen eller från PowerShell. Den här övningen använder portalen.

1. Ställ in *$DemoScenario* = **3**, _generera belastning med längre och mer frekventa toppar per databas_ för att öka intensiteten av den sammanställda belastningen på poolen utan att ändra toppbelastningen som krävs av varje databas.
1. Tryck på **F5** för att tillämpa en belastning på alla dina klientdatabaser.


1. **Öppna pool-bladet** **för tenants1/Pool1**.


1. Övervaka den ökade DTU-användningen för poolen i det övre diagrammet. Det tar några minuter för den nya, högre belastningen att sätta igång, men du borde snabbt se poolen börja komma upp i 100% användning och allteftersom belastningen stabiliseras i det nya mönstret så överbelastar det snabbt poolen.


1. Klicka på **konfigurera poolen** för att skala upp poolen
1. Justera **Pool-eDTU**-skjutreglaget till 100 (det rekommenderas att du inte går högre för att begränsa kostnaderna). Observera hur det sammanställda lagringsutrymmet för alla databaser i poolen, vilket indikeras av **Pool GB**, är länkat till eDTU-inställningen och ökar. Ändringar av pool-eDTU:er, ändrar inte inställningarna per databas (som fortfarande är 50 eDTU max per databas). Du kan se inställningarna per databas på höger sida av bladet **konfigurera pool**.
1. Klicka på **spara** för att skicka begäran. Ändringen tar vanligtvis 3-5 minuter för en standardpool.

Gå tillbaka till den **Pool1** > **översikt** för att visa övervakningsdiagrammen. Övervaka effekten av att ge poolen fler resurser (med få databaser och en slumpmässig belastning är det dock inte alltid lätt att se definitivt). När du tittar på diagrammen, tänk på att 100% i det övre diagrammet nu representerar 100 eDTU:er, medan 100% i det lägre diagrammet fortfarande är 50 eDTU:er eftersom max per databas fortfarande är 50 eDTU:er.

Databaserna är online och fullt tillgängliga under hela processen. Under det sista ögonblicket när varje databas är redo att aktiveras med de nya pool-eDTU:erna, bryts alla aktiva anslutningar. Du ska alltid skriva programkod för att återförsöka brutna anslutningar, vilken då kommer att återansluta till databasen i den uppskalade poolen.

## <a name="create-a-second-pool-and-load-balance-databases-to-handle-increased-aggregate-load"></a>Skapa en andra pool och belastningsutjämna databaserna för att hantera den ökade sammanställda belastningen

Som ett alternativ till att skala upp poolen, kan du skapa en andra pool och flytta databaserna till den för att balansera belastningen mellan de två poolerna. Det gör du genom att skapa den nya poolen på samma server som den första.

1. Öppna **serverbladet för customers1-&lt;användare&gt;-servern**. Om du är på en databas eller ett poolblad, kan du öppna kontrollen essentials och välja servernamnet som en genväg.
1. Klicka på **+ ny pool** för att skapa en pool på den aktuella servern
1. I mallen ny elastisk databaspool:

    1. ställ in **Name = Pool2**.
    1. Lämna prisnivån som **standardpool**.
    1. Klicka på **konfigurera poolen**,
    1. På bladet konfigurera pool som öppnas, ställer du in **Pool-eDTU = 50 DTU**.
    1. Klicka på kommandot **lägg till databaser** för att se en lista över databaser på den här servern som inte ingår i den aktuella poolen.
    1. I listan **markerar** du hälften av databaserna (10 av 20) för att flytta dem till den nya poolen och klickar sedan på **Välj**.
    1. Klicka på **Välj** igen för att godkänna konfigurationsändringarna. Observera kostnadsuppskattningen för en månads användning med de valda alternativen.
    1. Välj **OK** för att skapa den nya poolen med den nya konfigurationen och flytta databaserna.

Det tar några minuter att skapa poolen och flytta databaserna till den. Varje databas som flyttas är online och fullt tillgängliga tills sista ögonblicket, när alla öppna anslutningar bryts. När en klient försöker ansluta igen, ansluter den till databasen i den nya poolen.

När poolen har skapats, visas den i serverbladet customers1. Klicka på poolnamnet för att öppna poolbladet och övervaka dess prestanda.

Du borde se att resursanvändningen för pool1 har minskat och att pool2 har en liknande belastning.

## <a name="manage-an-increased-load-on-a-single-database"></a>Hantera en ökad belastning på en enda databas

Om en enskild databas i poolen har problem med en varaktigt hög belastning, kan det beroende poolkonfigurationen dominera resurserna i poolen och påverka andra databaser. Om aktiviteten sannolikt kommer att fortsätta, kan databasen tillfälligt flyttas från poolen. Det gör både att databasen kan ges fler resurser än de andra i poolen och isolerar den från de andra databaserna. Den här övningen simulerar effekten av att Contosos konserthall upplever en hög belastning när biljetter börjar säljas för ett populärt evenemang.

1. I skriptet ...\\**Demo-PerformanceManagementAndMonitoring**.ps1
1. Ställ in **$DemoScenario = 5, generera en normal belastning plus en hög belastning för en enskild klient (cirka 95 DTU).**
1. Ställ in **$SingleTenantDatabaseName = contosoconcerthall**
1. Kör skriptet med **F5**.
1. **Öppna poolbladet** **för Customers1/Pool1**.
1. Titta på skärmen **elastisk poolövervakning** som visas överst på bladet och leta efter den ökade DTU-användningen för poolen. Efter någon minut sätter den högre belastningen in och du borde snabbt se att poolen når 100% användning.
1. Kolla även skärmen **elastisk databasövervakning** som visar de hetaste databaserna den senaste timmen. Contosoconcerthall-databasen borde snart visas som en av de 5 hetaste databaserna.
1. **Klicka på **diagrammet** elastisk databasövervakning** så öppnas ett blad med **resursanvändning för databaser** där du selektivt kan övervaka alla databaser. På så sätt kan du isolera skärmen för contosoconcerthall-databasen.
1. I listan över databaser, **klickar du på contosoconcerthall** så öppnas dess databasblad.
1. Klicka på **prisnivå (skalnings-DTU:er)** i snabbmenyn för att öppna bladet **konfigurera prestanda** där du kan ställa in en isolerad prestandanivå för databasen.
1. Klicka på **Standard**-fliken för att öppna skalningsalternativen på standardnivån.
1. Skjut **DTU-skjutreglaget** till höger för att välja 100 DTU:er. Observera att det här motsvarar tjänstmålet, **S3,** som visas inom hakparenteser mellan mätarna DTU och lagringsstorlek.
1. Klicka på **Tillämpa** för att flytta databasen från poolen och göra den till en standard S3-databas.
1. När distributionen är klar, kan du övervaka effekten på contosoconcerthall-databasen och poolen den togs bort från i den elastiska poolen och databasbladen.

När den högre än normala belastningen på contosoconcerthall-databasen har avtagit, bör du snabbt återställa den till poolen för att minska kostnaden. Om det är oklart när det kommer att ske, kan du ställa in en avisering på databasen som utlöses när DTU-användningen sjunker under max per databas gränsen för poolen. Övrning 5 beskriver hur du flyttar en databas till en pool.

## <a name="other-performance-management-patterns"></a>Övriga prestandahanteringsmönster

**Förebyggande skalning** i övning 6 där du utforskade hur man skalar en isolerad databas så visste du vilken databas du var ute efter. Om hanteringen för Contosos konserthall hade informerat WTP om den kommande biljettförsäljningen, kunde databasen ha flyttats ut från poolen i förebyggande syfte. Annars hade det sannolikt behövts en avisering på poolen eller databasen för att upptäcka vad som hände. Du vill inte få reda på sånt här genom att andra klienter i poolen klagar på försämrad prestanda. Och om klienten kan förutse hur länge de behöver ytterligare resurser, kan du ställa in en Azure Automation-runbook för att flytta ut databasen från poolen och sedan tillbaka igen enligt ett definierat schema.

**Klientskalning som självservice** eftersom skalning är en aktivitet som enkelt kan anropas via hanterings-API:t, kan du enkelt bygga in möjligheten att skala klientdatabaser i ditt klientriktade program och erbjuda de som en funktion av din SaaS-tjänst. Exempelvis kan du låta klienter självhantera upp- och nedskalning, kanske kopplat direkt till sin fakturering!

### <a name="scaling-a-pool-up-and-down-on-a-schedule-to-match-usage-patterns"></a>Skala en pool upp och ned enligt ett schema för att matcha användningsmönster

I de fall där sammanställd klientanvändning följer förutsägbara mönster, kan du använda Azure Automation för att skala upp en pool efter ett schema. Skala exempelvis ned en pool efter 18.00 och upp igen innan 06.00 på veckodagar, när du vet att kraven på resursanvändning går ned.



## <a name="next-steps"></a>Nästa steg

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Simulerar användning på klientdatabaserna genom att köra en angiven belastningsgenerator
> * Övervakar klientdatabaserna när de svarar på belastningsökningar
> * Skalar upp den elastiska poolen i svar på den ökade databasbelastningen
> * Etablerar en andra elastisk pool för att belastningsutjämna databasaktiviteten

[Guiden återställ en enskild klient](sql-database-saas-tutorial-restore-single-tenant.md)


## <a name="additional-resources"></a>Ytterligare resurser

* [Ytterligare guider som bygger på den initiala programdistributionen av Wingtip biljettplattformen (WTP)](sql-database-wtp-overview.md#sql-database-wtp-saas-tutorials)
* [Elastiska SQL-pooler](sql-database-elastic-pool.md)
* [Azure Automation](../automation/automation-intro.md)
* [Log Analytics](sql-database-saas-tutorial-log-analytics.md) - Guide för att konfigurera och använda Log Analytics
