---
title: 'Saas-app: Övervaka prestanda för många Azure SQL-databaser | Microsoft Docs'
description: Övervaka och hantera prestanda för Azure SQL-databaser och pooler i en SaaS-app för flera innehavare
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: df54f9dd4047fffb578a1a95a2edc47cba711ba1
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2019
ms.locfileid: "57433526"
---
# <a name="monitor-and-manage-performance-of-azure-sql-databases-and-pools-in-a-multi-tenant-saas-app"></a>Övervaka och hantera prestanda för Azure SQL-databaser och pooler i en SaaS-app för flera innehavare

I den här självstudien beskrivs flera prestandarelaterade hanteringsscenarier som används i SaaS-program. Använder en belastningsgenerator för att simulera aktivitet för alla klientdatabaser, är inbyggda övervaknings- och aviseringsfunktionerna i SQL Database och elastiska pooler visas.

Wingtip biljetter SaaS databas Per klient appen använder en enda klient datamodell, där varje lokal (klient) har sin egen databas. Precis som för flera SaaS-program så är de förväntade belastningsmönstren för klienterna oberäkneliga och sporadiska. Biljettförsäljningar kan med andra ord ske när som helst. Om du vill dra nytta av den här typiska databasanvändningsmönstret, distribueras klientdatabaserna i elastiska pooler. Elastiska pooler optimerar kostnaderna för en lösning genom att dela resurser över flera databaser. Med den här typen av mönster är det viktigt att övervaka användningen av databas- och poolresurser för att försäkra att belastningarna balanseras över poolerna. Du behöver också se till att enskilda databaser har tillräckliga resurser och att pooler inte träffar sina [eDTU](sql-database-purchase-models.md#dtu-based-purchasing-model)-gränser. Den här guiden går igenom sätt att övervaka och hantera databaser och pooler och hur man vidtar åtgärder i respons på belastningsvariationer.

I den här guiden får du lära dig hur man:

> [!div class="checklist"]

> * Simulerar användning på klientdatabaserna genom att köra en angiven belastningsgenerator
> * Övervakar klientdatabaserna när de svarar på belastningsökningar
> * Skalar upp den elastiska poolen i svar på den ökade databasbelastningen
> * Etablerar en andra elastisk pool för att belastningsutjämna databasaktiviteten


Följande krav måste uppfyllas för att kunna köra den här självstudiekursen:

* Wingtip biljetter SaaS databas Per klient-appen har distribuerats. Om du vill distribuera i mindre än fem minuter [distribuera och utforska Wingtip biljetter SaaS databas Per klient-programmet](saas-dbpertenant-get-started-deploy.md)
* Azure PowerShell ska ha installerats. Mer information finns i [Komma igång med Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-saas-performance-management-patterns"></a>Introduktion till SaaS-prestandahanteringsmönster

Hantering av databasprestanda innebär kompilering och analys av prestandadata för att sedan reagera på den genom att justera parametrarna för att bibehålla en acceptabel svarstid för programmet. När du har flera klienter, är elastiska pooler ett kostnadseffektivt sätt att tillhandahålla och hantera resurser för en grupp med databaser med oförutsägbara arbetsbelastningar. Med vissa arbetsbelastningmönster, kan så få som två S3-databaser dra nytta av att hanteras i en pool.

![Diagram för program](./media/saas-dbpertenant-performance-monitoring/app-diagram.png)

Pooler och databaser i pooler, bör övervakas för att säkerställa att de håller sig inom acceptabla prestandaintervall. Justera poolkonfigurationen för att uppfylla behoven hos den sammanställda arbetsbelastningen för alla databaser, att se till att pool-edtu: er är lämpliga för den övergripande arbetsbelastningen. Justera per databas min och max eDTU-värden till lämpliga värden för dina specifika programkrav.

### <a name="performance-management-strategies"></a>Strategier för prestandahantering

* Om du vill undvika att behöva manuellt övervaka prestanda, är det bäst att **ställa in aviseringar som utlöser när databaser eller pooler avviker från normala intervall**.
* Svara på kortsiktiga variationer i sammansatt beräkningsstorleken för en pool i **poolens eDTU-nivå kan skalas upp eller ned**. Om de här variationerna inträffar på regelbunden eller förutsägbar basis, **kan skalning av poolen schemaläggas att ske automatiskt**. Du kan exempelvis skala ned när du vet att din arbetsbelastning är lätt, på nätter eller helger till exempel.
* För att svara på mer långsiktiga variationer eller förändringar av antalet databaser, **kan enskilda databaser flyttas till andra pooler**.
* Svara på kortsiktiga ökningar *enskilda* databasbelastningen **enskilda databaser kan tas bort från en pool och tilldelas en enskild beräkningsstorleken**. När belastningen minskar, kan databasen returneras till poolen. När det här är känt i förväg, kan databaser flyttas förebyggande syfte att säkerställa att databasen alltid har de resurser den behöver och för att undvika påverkan på andra databaser i poolen. Om det här kravet är förutsägbart, till exempel en plats där ett populärt evenemang skapar en rusning efter biljetter, kan det här hanteringsbeteendet integreras i programmet.

[Azure-portalen](https://portal.azure.com) tillhandahåller inbyggd övervakning och avisering för de flesta resurser. För SQL Database, finns övervakning och avisering tillgängligt för databaser och pooler. Den här inbyggda övervakningen och aviseringen är resursspecifik, så det är praktiskt för små antal resurser, men är inte mindre praktisk när du arbetar med många resurser.

För scenarion med stora volymer, där du arbetar med många resurser, [Azure Monitor loggar](saas-dbpertenant-log-analytics.md) kan användas. Det här är en separat Azure-tjänst som tillhandahåller analys för utgivna diagnostikloggar och telemetri som samlats in i Log Analytics-arbetsytan. Azure Monitor-loggar kan samla in telemetri från flera olika tjänster och användas för att fråga och ställa in aviseringar.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Hämta programskript Wingtip biljetter SaaS databas Per klient

Databas för flera klienter i Wingtip biljetter SaaS-skript och programmets källkod finns tillgängliga i den [WingtipTicketsSaaS DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub-lagringsplatsen. Kolla in den [allmänna riktlinjer](saas-tenancy-wingtip-app-guidance-tips.md) steg att ladda ned och avblockera Wingtip biljetter SaaS-skript.

## <a name="provision-additional-tenants"></a>Etablera ytterligare klienter

Pooler kan vara kostnadseffektiva med bara två S3 databaser, men ju fler databaser som finns i poolen, desto mer kostnadseffektiv blir genomsnittseffekten. För att få en bra förståelse för hur prestandaövervakning och hantering fungerar i stor skala, kräver den här kursen du har distribuerat minst 20 databaser.

Om du redan har etablerat en batch med klienter i en tidigare självstudiekurs gå vidare till den [simulera användning på alla klientdatabaser](#simulate-usage-on-all-tenant-databases) avsnittet.

1. I den **PowerShell ISE**öppnar... \\Inlärningsmoduler\\prestandaövervakning och hantering\\*Demo-PerformanceMonitoringAndManagement.ps1*. Ha det här skriptet öppet medan du kör scenarierna i den här guiden.
1. Ställ in **$DemoScenario** = **1**, **etablera en batch med klienter**
1. Tryck **F5** för att köra skriptet.

Skriptet distribuerar 17 klienter på mindre än fem minuter.

Den *New-TenantBatch* skriptet använder en kapslad eller länkad uppsättning [Resource Manager](../azure-resource-manager/index.yml) mallar som skapar en batch med klienter, vilket som standard kopieras databasen **basetenantdb**på katalogservern för att skapa den nya innehavaren databaser, och registrerar sedan dessa i katalogen och slutligen initieras de med klienttyp och platstyp. Det här är likadant som hur appen etablerar en ny klient. Ändringar som görs till *basetenantdb* tillämpas på alla nya klienter som etableras därefter. Se den [schemahanteringsguiden](saas-tenancy-schema-management.md) att se hur du gör schemaändringar för *befintliga* klientdatabaser (inklusive den *basetenantdb* databas).

## <a name="simulate-usage-on-all-tenant-databases"></a>Simulera användning på alla klientdatabaser

Den *Demo-PerformanceMonitoringAndManagement.ps1* skript har angetts som simulerar en arbetsbelastning som körs mot alla klientdatabaser. Belastningen skapas med någon av de tillgängliga load-scenarierna:

| Demo | Scenario |
|:--|:--|
| 2 | Generera normal intensitet (cirka 40 DTU) |
| 3 | Generera belastning med längre och mer frekventa toppar per databas|
| 4 | Generera belastningar med högre DTU-toppar per databas (cirka 80 DTU)|
| 5 | Generera en normal belastning plus en hög belastning på en enskild klient (cirka 95 DTU)|
| 6 | Generera obalanserad belastning över flera pooler|

Belastningsgeneratorn tillämpar en *syntetisk* enbart-CPU-belastning på varje klientdatabas. Generatorn startar ett jobb för varje klientdatabas som anropar en lagrad procedur med jämna mellanrum, vilket genererar belastningen. Belastningsnivåer (i eDTU:er), varaktighet och intervaller varierar över alla databaser, vilket simulerar oförutsägbar klientaktivitet.

1. I den **PowerShell ISE**öppnar... \\Inlärningsmoduler\\prestandaövervakning och hantering\\*Demo-PerformanceMonitoringAndManagement.ps1*. Ha det här skriptet öppet medan du kör scenarierna i den här guiden.
1. Ange **$DemoScenario** = **2**, *generera av normal intensitet*.
1. Tryck på **F5** för att tillämpa en belastning på alla dina klientdatabaser.

Wingtip biljetter SaaS databas Per klient är en SaaS-app och verkliga belastningen på en SaaS-app är vanligtvis sporadisk och oförutsägbar. För att simulera det så skapar belastningsgeneratorn en slumpmässig belastning som distribueras över alla klienterna. Flera minuter behövs för mönster för belastningsutjämning till dyker upp, så kör belastningsgeneratorn för 3 – 5 minuter innan du försöker övervaka belastningen i följande avsnitt.

> [!IMPORTANT]
> Belastningsgeneratorn körs som en serie jobb i din lokala PowerShell-session. Behåll fliken *Demo-PerformanceMonitoringAndManagement.ps1* öppen! Om du stänger fliken eller pausar din dator, stannar belastningsgeneratorn. Belastningsgeneratorn finns kvar i en *jobbet anropar* tillstånd där det genererar belastningen på alla nya klienter som har etablerats efter generatorn har startats. Använd *Ctrl-C* att stoppa anropar nya jobb och avsluta skriptet. Belastningsgeneratorn fortsätter att köras, men endast på befintliga klienter.

## <a name="monitor-resource-usage-using-the-azure-portal"></a>Övervaka Resursanvändning med Azure-portalen

För att övervaka Resursanvändning som resulterar från den tillämpade belastningen, öppnar du portalen till poolen som innehåller klientdatabaserna:

1. Öppna den [Azure-portalen](https://portal.azure.com) och bläddra till den *tenants1-dpt -&lt;användaren&gt;*  server.
1. Bläddra ned och hitta elastiska pooler och klicka på **Pool1**. Den här poolen innehåller alla klientdatabaser som skapats än så länge.

Notera den **övervakning av elastiska pooler** och **elastisk databasövervakning** diagram.

Poolens resursanvändningen är sammanställda databasanvändningen för alla databaser i poolen. Databas-diagrammet visar de fem hetaste databaserna:

![databas-diagram](./media/saas-dbpertenant-performance-monitoring/pool1.png)

Eftersom det finns ytterligare databaser i poolen utöver de översta fem, visar poolanvändningen aktivitet som inte visas i den översta fem databaser. Mer information klickar du på **Databasresursanvändning**:

![resursutnyttjande för databas](./media/saas-dbpertenant-performance-monitoring/database-utilization.png)


## <a name="set-performance-alerts-on-the-pool"></a>Ställ in prestandaaviseringar för poolen

Ställa in en avisering på poolen som utlöses vid \>75% utnyttjande enligt följande:

1. Öppna *Pool1* (på den *tenants1-dpt -\<användaren\>*  server) i den [Azure-portalen](https://portal.azure.com).
1. Klicka på **aviseringsregler** och därefter på **+ lägg till avisering**:

   ![lägg till avisering](media/saas-dbpertenant-performance-monitoring/add-alert.png)

1. Ange ett namn, exempelvis **hög DTU**,
1. Ställ in följande värden:
   * **Mått = eDTU procent**
   * **Villkor = större än**
   * **Tröskelvärdet = 75**
   * **Period = de senaste 30 minuterna**
1. Lägg till en e-postadress till den *administratören email(s)* och klickar på **OK**.

   ![ange varning](media/saas-dbpertenant-performance-monitoring/alert-rule.png)


## <a name="scale-up-a-busy-pool"></a>Skala upp en upptagen pool

Om den sammanställda belastningsnivån ökar på en pool till den punkt där poolen maxas ut och den når 100% eDTU-användning så påverkas individuell databasprestanda, vilket kan sakta ner frågesvarstiderna för alla databaser i poolen.

**Kortsiktig**, Överväg att skala upp poolen för att tillhandahålla ytterligare resurser eller ta bort databaser från poolen (flytta dem till andra pooler eller från poolen till en fristående tjänstnivå).

**Lång sikt**, Överväg att optimera frågorna eller indexanvändningen för att förbättra databasprestanda. Beroende på programmets känslighet för prestandaproblem, är det praxis att skala upp en pool innan den når 100% eDTU-användning. Använd en avisering för att varna dig i förväg.

Du kan simulera en upptagen pool genom att öka belastningen som generatorn skapar. Gör databasen att toppa oftare och för längre, ökar den sammanställda belastningen på poolen utan att ändra kraven för individuella databaser. Det är enkelt att skala upp poolen i portalen eller från PowerShell. Den här övningen använder portalen.

1. Ställ in *$DemoScenario* = **3**, _generera belastning med längre och mer frekventa toppar per databas_ för att öka intensiteten av den sammanställda belastningen på poolen utan att ändra toppbelastningen som krävs av varje databas.
1. Tryck på **F5** för att tillämpa en belastning på alla dina klientdatabaser.

1. Gå till **Pool1** i Azure-portalen.

Övervaka ökad pool-eDTU-användning i det övre diagrammet. Det tar några minuter innan den nya högre belastningen sätter in, men du borde snabbt se poolen börjar når maximal användning och som belastningen steadies till det nya mönstret, overloads snabbt poolen.

1. Om du vill skala upp poolen, klickar du på **konfigurera pool** överst i den **Pool1** sidan.
1. Justera den **Pool-eDTU** att ställa in **100**. Ändringar av pool-eDTU:er, ändrar inte inställningarna per databas (som fortfarande är 50 eDTU max per databas). Du kan se inställningarna per databas på höger sida av den **konfigurera pool** sidan.
1. Klicka på **spara** att skicka förfrågan om att skala poolen.

Gå tillbaka till **Pool1** > **översikt** att visa Övervakningsdiagrammen. Övervaka effekten av att ge poolen fler resurser (även om med få databaser och en slumpmässig belastning inte är det alltid lätt att se definitivt tills du kör under en viss tid). När du tittar på diagrammen, tänk på att 100% i det övre diagrammet nu representerar 100 eDTU:er, medan 100% i det lägre diagrammet fortfarande är 50 eDTU:er eftersom max per databas fortfarande är 50 eDTU:er.

Databaserna är online och fullt tillgängliga under hela processen. Under det sista ögonblicket när varje databas är redo att aktiveras med de nya pool-eDTU:erna, bryts alla aktiva anslutningar. Du ska alltid skriva programkod för att återförsöka brutna anslutningar, vilken då kommer att återansluta till databasen i den uppskalade poolen.

## <a name="load-balance-between-pools"></a>Belastningsutjämna mellan pooler

Som ett alternativ till att skala upp poolen, kan du skapa en andra pool och flytta databaserna till den för att balansera belastningen mellan de två poolerna. Det gör du genom att skapa den nya poolen på samma server som den första.

1. I den [Azure-portalen](https://portal.azure.com)öppnar den **tenants1-dpt -&lt;användaren&gt;**  server.
1. Klicka på **+ ny pool** att skapa en pool på den aktuella servern.
1. På den **elastisk pool** mall:

    1. Ange **namn** till *Pool2*.
    1. Lämna prisnivån som **standardpool**.
    1. Klicka på **konfigurera poolen**,
    1. Ange **Pool-eDTU** till *50 eDTU*.
    1. Klicka på **lägga till databaser** att se en lista över databaser på den server som kan läggas till *Pool2*.
    1. Välj 10 databaser att flytta dem till den nya poolen och klickar sedan på **Välj**. Om du har kört belastningsgeneratorn, vet tjänsten redan att profilen prestanda kräver en pool som är större än 50 eDTU standardstorleken och rekommenderar att du börjar med en 100 eDTU-inställningen.

    ![rekommendation](media/saas-dbpertenant-performance-monitoring/configure-pool.png)

    1. Låt standardvärdet på 50 edtu: er i den här självstudien och klicka på **Välj** igen.
    1. Välj **OK** att skapa den nya poolen och flytta de valda databaserna till den.

Skapa poolen och Flytta databaserna tar några minuter. När databaser flyttas de fortfarande är online och fullt tillgängliga tills sista, då alla öppna anslutningar bryts. Så länge som du har några logik för omprövning klienter sedan att ansluta till databasen i den nya poolen.

Bläddra till **Pool2** (på den *tenants1-dpt -\<användaren\>*  server) att öppna poolen och övervaka dess prestanda. Om du inte ser det, vänta tills etableringen av den nya poolen för att slutföra.

Du nu se att resursanvändningen på *Pool1* har sänkts och att *Pool2* läses nu på samma sätt.

## <a name="manage-performance-of-an-individual-database"></a>Hantera prestanda för en enskild databas

Om en enskild databas i en pool får en varaktigt hög belastning, beroende på poolkonfiguration kan det brukar dominera resurserna i poolen och påverka andra databaser. Om aktiviteten är sannolikt kommer att fortsätta under en viss tid, kan databasen tillfälligt flyttas från poolen. På så sätt kan databasen extra resurser den behöver och isolerar den från andra databaser.

Den här övningen simulerar effekten av att Contosos konserthall upplever en hög belastning när biljetter börjar säljas för ett populärt evenemang.

1. I den **PowerShell ISE**öppnar den... \\ *Demo-PerformanceMonitoringAndManagement.ps1* skript.
1. Ange **$DemoScenario = 5, generera en normal belastning plus en hög belastning på en enskild klient (cirka 95 DTU).**
1. Ställ in **$SingleTenantDatabaseName = contosoconcerthall**
1. Kör skriptet med **F5**.


1. I den [Azure-portalen](https://portal.azure.com), bläddra i listan över databaser på den *tenants1-dpt -\<användaren\>*  server. 
1. Klicka på den **contosoconcerthall** databas.
1. Klicka på poolen som **contosoconcerthall** finns i. Leta upp poolen i den **elastisk pool** avsnittet.

1. Granska den **övervakning av elastiska pooler** diagram och leta efter ökad pool-eDTU-användning. Efter någon minut sätter den högre belastningen in och du borde snabbt se att poolen når 100% användning.
2. Granska den **elastisk databasövervakning** visa, som visar de hetaste databaserna under den senaste timmen. Den *contosoconcerthall* databasen borde snart visas som en av de fem hetaste databaserna.
3. **Klicka på elastisk databasövervakning** **diagram** och öppnar den **Databasresursanvändning** sidan där du kan övervaka alla databaser. På så sätt kan du isolera skärmen för den *contosoconcerthall* databas.
4. I listan över databaser, klickar du på **contosoconcerthall**.
5. Klicka på **prisnivå (skalnings-dtu: er)** att öppna den **konfigurera prestanda** sidan där du kan ange en fristående beräkningsstorleken för databasen.
6. Klicka på **Standard**-fliken för att öppna skalningsalternativen på standardnivån.
7. Dra den **DTU-skjutreglaget** till höger för att välja **100** dtu: er. Obs detta motsvarar tjänstmålet, **S3**.
8. Klicka på **tillämpa** att flytta ut databasen från poolen och göra den en *Standard S3* databas.
9. När skalning är klar kan du övervaka effekten på contosoconcerthall-databasen och Pool1 på de elastiska poolen och databasbladen.

När hög belastning på contosoconcerthall-databasen har avtagit, bör du snabbt återställa den till poolen för att minska kostnaderna. Om det är oklart när som sker du kan ange en avisering på databasen som utlöses när DTU-användningen sjunker under per databas max för poolen. Övrning 5 beskriver hur du flyttar en databas till en pool.

## <a name="other-performance-management-patterns"></a>Övriga prestandahanteringsmönster

**Förebyggande skalning** i övning ovan där du utforskade hur man skalar en isolerad databas så Visste du vilken databas du söker efter. Om hanteringen för Contosos Konserthall hade informerat Wingtips av den kommande biljettförsäljningen, kan databasen ha flyttats från poolen förebyggande syfte. Annars hade det sannolikt behövts en avisering på poolen eller databasen för att upptäcka vad som hände. Du vill inte få reda på sånt här genom att andra klienter i poolen klagar på försämrad prestanda. Och om klienten kan förutse hur länge de behöver ytterligare resurser, kan du ställa in en Azure Automation-runbook för att flytta ut databasen från poolen och sedan tillbaka igen enligt ett definierat schema.

**Klientskalning som självservice** eftersom skalning är en aktivitet som enkelt kan anropas via hanterings-API:t, kan du enkelt bygga in möjligheten att skala klientdatabaser i ditt klientriktade program och erbjuda de som en funktion av din SaaS-tjänst. Exempelvis kan du låta klienter självhantera upp- och nedskalning, kanske kopplat direkt till sin fakturering!

**Skala en pool upp och ned enligt ett schema för att matcha användningsmönster**

I de fall där sammanställd klientanvändning följer förutsägbara mönster, kan du använda Azure Automation för att skala upp en pool efter ett schema. Skala exempelvis ned en pool efter 18.00 och upp igen innan 06.00 på veckodagar, när du vet att kraven på resursanvändning går ned.



## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Simulerar användning på klientdatabaserna genom att köra en angiven belastningsgenerator
> * Övervakar klientdatabaserna när de svarar på belastningsökningar
> * Skalar upp den elastiska poolen i svar på den ökade databasbelastningen
> * Etablerar en andra elastisk pool för att belastningsutjämna databasaktiviteten

[Guiden återställ en enskild klient](saas-dbpertenant-restore-single-tenant.md)


## <a name="additional-resources"></a>Ytterligare resurser

* Ytterligare [självstudier som bygger på programdistributionen Wingtip biljetter SaaS databas Per klient](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Elastiska SQL-pooler](sql-database-elastic-pool.md)
* [Azure Automation](../automation/automation-intro.md)
* [Azure Monitor-loggar](saas-dbpertenant-log-analytics.md) - guide konfigurera och använda Azure Monitor-loggar självstudien
