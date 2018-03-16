---
title: "Övervaka prestanda för många Azure SQL-databaser i en app för flera innehavare SaaS | Microsoft Docs"
description: "Övervaka och hantera prestanda för Azure SQL-databaser och pooler i en SaaS-app för flera innehavare"
keywords: sql database tutorial
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 10/31/2017
ms.author: sstein
ms.openlocfilehash: 352b0d48ab223a236b96c633526f792b6f8a703d
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="monitor-and-manage-performance-of-azure-sql-databases-and-pools-in-a-multi-tenant-saas-app"></a>Övervaka och hantera prestanda för Azure SQL-databaser och pooler i en SaaS-app för flera innehavare

I den här självstudiekursen beskrivs flera KPI hanteringsscenarier som används i SaaS-program. Simulera aktiviteten över alla klient-databaser med hjälp av en load-generatorn är inbyggd övervakning och avisering funktioner i SQL-databasen och elastiska pooler visas.

Appen Wingtip biljetter SaaS databas Per klient använder en enskild klient datamodellen, där varje plats (klient) har sina egna databasen. Precis som för flera SaaS-program så är de förväntade belastningsmönstren för klienterna oberäkneliga och sporadiska. Biljettförsäljningar kan med andra ord ske när som helst. För att dra nytta av det här typiska databasanvändningsmönstret, distribueras klientdatabaserna i elastiska databaspooler. Elastiska pooler optimerar kostnaderna för en lösning genom att dela resurser över flera databaser. Med den här typen av mönster är det viktigt att övervaka användningen av databas- och poolresurser för att försäkra att belastningarna balanseras över poolerna. Du behöver också se till att enskilda databaser har tillräckliga resurser och att pooler inte träffar sina [eDTU](sql-database-what-is-a-dtu.md)-gränser. Den här guiden går igenom sätt att övervaka och hantera databaser och pooler och hur man vidtar åtgärder i respons på belastningsvariationer.

I den här guiden får du lära dig hur man:

> [!div class="checklist"]

> * Simulerar användning på klientdatabaserna genom att köra en angiven belastningsgenerator
> * Övervakar klientdatabaserna när de svarar på belastningsökningar
> * Skalar upp den elastiska poolen i svar på den ökade databasbelastningen
> * Etablerar en andra elastisk pool för att belastningsutjämna databasaktiviteten


Följande krav måste uppfyllas för att kunna köra den här självstudiekursen:

* Wingtip biljetter SaaS databas Per klient appen har distribuerats. För att distribuera på mindre än fem minuter finns [distribuera och utforska programmet Wingtip biljetter SaaS databas Per klient](saas-dbpertenant-get-started-deploy.md)
* Azure PowerShell ska ha installerats. Mer information finns i [Komma igång med Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-saas-performance-management-patterns"></a>Introduktion till SaaS prestanda management mönster

Hantering av databasprestanda innebär kompilering och analys av prestandadata för att sedan reagera på den genom att justera parametrarna för att bibehålla en acceptabel svarstid för programmet. När du är värd för flera klientorganisationer, är elastiska databaspooler ett kostnadseffektivt sätt att tillhandahålla och hantera resurser för en grupp databaser med oförutsägbara arbetsbelastningar. Med vissa arbetsbelastningmönster, kan så få som två S3-databaser dra nytta av att hanteras i en pool.

![Diagram över](./media/saas-dbpertenant-performance-monitoring/app-diagram.png)

Pooler och databaserna i poolen, bör övervakas för att säkerställa att de förblir inom acceptabel prestanda. Finjustera konfigurationen för lagringspooler för att uppfylla behoven för alla databaser som säkerställer att pool-edtu: er är lämpliga för den övergripande arbetsbelastningen sammanställd arbetsbelastningen. Justera per databas min och max eDTU-värden till lämpliga värden för dina specifika programkrav.

### <a name="performance-management-strategies"></a>Strategier för prestandahantering

* Om du vill undvika att övervaka prestanda manuellt är det mest effektiva till **ställa in aviseringar som utlöses när databaser eller pooler avvika utanför normal adressintervall**.
* För att svara på kortsiktiga variationer i sammansatt prestandanivå för en pool, kan **poolens eDTU-nivå skalas upp eller ned**. Om de här variationerna inträffar på regelbunden eller förutsägbar basis, **kan skalning av poolen schemaläggas att ske automatiskt**. Du kan exempelvis skala ned när du vet att din arbetsbelastning är lätt, på nätter eller helger till exempel.
* För att svara på mer långsiktiga variationer eller förändringar av antalet databaser, **kan enskilda databaser flyttas till andra pooler**.
* Att svara på kort sikt ökningar i *enskilda* databasbelastningen **enskilda databaser kan tas bort från en pool och tilldelats en enskilda prestandanivå**. När belastningen minskar, kan databasen returneras till poolen. När det är känt i förväg, kan databaser flyttas pre-emptively att säkerställa att databasen alltid har resurser som behövs och undvika påverkan på andra databaser i poolen. Om det här kravet är förutsägbart, till exempel en plats där ett populärt evenemang skapar en rusning efter biljetter, kan det här hanteringsbeteendet integreras i programmet.

[Azure-portalen](https://portal.azure.com) tillhandahåller inbyggd övervakning och avisering för de flesta resurser. För SQL Database, finns övervakning och avisering tillgängligt för databaser och pooler. Den här inbyggda övervakning och avisering är resurs-specifika, så det är praktiskt att använda för litet antal resurser, men är inte är användbart när du arbetar med många resurser.

Omfattande scenarier där du arbetar med många resurser, [logganalys (OMS)](saas-dbpertenant-log-analytics.md) kan användas. Detta är en separat Azure-tjänst som ger analytics över skickade diagnostikloggar och telemetri som samlats in i en log analytics-arbetsyta. Logganalys kan samla in telemetri från många tjänster och användas för att fråga efter och Ställ in aviseringar.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Hämta programskript Wingtip biljetter SaaS databas Per klient

Wingtip biljetter SaaS flera innehavare databasen skript och programmets källkod är tillgängliga i den [WingtipTicketsSaaS DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub-lagringsplatsen. Kolla in den [allmänna riktlinjer](saas-tenancy-wingtip-app-guidance-tips.md) steg för att ladda ned och avblockera Wingtip biljetter SaaS-skript.

## <a name="provision-additional-tenants"></a>Etablera ytterligare klienter

Pooler kan vara kostnadseffektiva med bara två S3 databaser, men ju fler databaser som finns i poolen, desto mer kostnadseffektiv blir genomsnittseffekten. För att få en bra förståelse för hur prestandaövervakning och hantering fungerar i stor skala, kräver den här kursen du har distribuerat minst 20 databaser.

Om du redan har etablerats en grupp med klienter i en tidigare kursen går du till den [simulera användning för alla databaser som klient](#simulate-usage-on-all-tenant-databases) avsnitt.

1. I den **PowerShell ISE**öppnar... \\Learning moduler\\prestandaövervakning och hantering av\\*Demo-PerformanceMonitoringAndManagement.ps1*. Ha det här skriptet öppet medan du kör scenarierna i den här guiden.
1. Ställ in **$DemoScenario** = **1**, **etablera en batch med klienter**
1. Tryck **F5** för att köra skriptet.

Skriptet distribuerar 17 klienter på mindre än fem minuter.

Den *ny TenantBatch* skript som använder en kapslad eller länkade uppsättning [Resource Manager](../azure-resource-manager/index.md) mallar som skapar en grupp med klienter, vilket som standard kopieras databasen **basetenantdb** på katalogserver att skapa den nya innehavaren databaser registrerar sedan dessa i katalogen och slutligen initierar dem med typen klient namn och plats. Detta är förenligt med hur appen etablerar en ny klient. Ändringar som görs till *basetenantdb* tillämpas på alla nya klienter som etablerats därefter. Finns det [schemat Management kursen](saas-tenancy-schema-management.md) att se hur du ändrar schemat *befintliga* klient databaser (inklusive den *basetenantdb* databas).

## <a name="simulate-usage-on-all-tenant-databases"></a>Simulera användning på alla klientdatabaser

Den *Demo-PerformanceMonitoringAndManagement.ps1* skript har angetts som simulerar en arbetsbelastning som körs mot alla klient-databaser. Belastningen skapas med någon av de tillgängliga belastning scenarierna:

| Demo | Scenario |
|:--|:--|
| 2 | Generera belastning av normal intensitet (ca 40 DTU) |
| 3 | Generera belastning med längre och mer frekventa toppar per databas|
| 4 | Generera belastningar med högre DTU-toppar per databas (cirka 80 DTU)|
| 5 | Generera en normal belastning plus en hög belastning på en enskild klient (cirka 95 DTU)|
| 6 | Generera obalanserad belastning över flera pooler|

Belastningsgeneratorn tillämpar en *syntetisk* enbart-CPU-belastning på varje klientdatabas. Generatorn startar ett jobb för varje klientdatabas som anropar en lagrad procedur med jämna mellanrum, vilket genererar belastningen. Belastningsnivåer (i eDTU:er), varaktighet och intervaller varierar över alla databaser, vilket simulerar oförutsägbar klientaktivitet.

1. I den **PowerShell ISE**öppnar... \\Learning moduler\\prestandaövervakning och hantering av\\*Demo-PerformanceMonitoringAndManagement.ps1*. Ha det här skriptet öppet medan du kör scenarierna i den här guiden.
1. Ange **$DemoScenario** = **2**, *generera normal styrka belastningen*.
1. Tryck på **F5** för att tillämpa en belastning på alla dina klientdatabaser.

Wingtip biljetter SaaS databas Per klient är en SaaS-app och verkliga belastningen på en SaaS-app är vanligtvis sporadiska och oförutsägbart. För att simulera det så skapar belastningsgeneratorn en slumpmässig belastning som distribueras över alla klienterna. Flera minuter krävs för belastningen mönstret ut, så kör belastningen generatorn för 3-5 minuter innan du försöker att övervaka belastningen i följande avsnitt.

> [!IMPORTANT]
> Belastningsgeneratorn körs som en serie jobb i din lokala PowerShell-session. Behåll fliken *Demo-PerformanceMonitoringAndManagement.ps1* öppen! Om du stänger fliken eller pausar din dator, stannar belastningsgeneratorn. Läs in generator finns kvar i en *jobbet anropar* tillstånd där den genererar belastningen på alla nya klienter som tillhandahålls när generatorn har startats. Använd *Ctrl-C* att stoppa anropar nya jobb och avslutas skriptet. Läs in generator fortsätter att köras, men endast på befintliga klienter.

## <a name="monitor-resource-usage-using-the-azure-portal"></a>Övervaka Resursanvändning i Azure Portal

Öppna portal till poolen innehåller klient-databaser för att övervaka Resursanvändning som resultat av belastningen tillämpas:

1. Öppna den [Azure-portalen](https://portal.azure.com) och bläddra till den *tenants1-dpt -&lt;användare&gt; * server.
1. Bläddra ned och hitta elastiska pooler och klicka på **Pool1**. Den här poolen innehåller alla klientdatabaser som skapats än så länge.

Se den **elastisk pool övervakning** och **elastisk databas övervakning** diagram.

Poolens resursutnyttjandet är sammanställd databasen användning för alla databaser i poolen. Databasen diagrammet visar de fem senaste databaserna:

![diagram för databasen](./media/saas-dbpertenant-performance-monitoring/pool1.png)

Eftersom det finns ytterligare databaser i poolen utöver fem, visar aktivitet som inte återspeglas i översta fem databaser diagrammet pool-användning. Mer information klickar du på **databasen resursutnyttjande**:

![databasen resursutnyttjande](./media/saas-dbpertenant-performance-monitoring/database-utilization.png)


## <a name="set-performance-alerts-on-the-pool"></a>Ställ in prestandaaviseringar för poolen

Ange en avisering i den pool som utlösare på \>75% användning på följande sätt:

1. Öppna *Pool1* (på den *tenants1-dpt -\<användare\> * server) i den [Azure-portalen](https://portal.azure.com).
1. Klicka på **aviseringsregler** och därefter på **+ lägg till avisering**:

   ![lägg till avisering](media/saas-dbpertenant-performance-monitoring/add-alert.png)

1. Ange ett namn, exempelvis **hög DTU**,
1. Ställ in följande värden:
   * **Mått = eDTU procent**
   * **Villkor = större än**
   * **Tröskelvärde = 75**
   * **Period = under de senaste 30 minuterna**
1. Lägg till en e-postadress till den *ytterligare administratören email(s)* och klicka på **OK**.

   ![ange varning](media/saas-dbpertenant-performance-monitoring/alert-rule.png)


## <a name="scale-up-a-busy-pool"></a>Skala upp en upptagen pool

Om den sammanställda belastningsnivån ökar på en pool till den punkt där poolen maxas ut och den når 100% eDTU-användning så påverkas individuell databasprestanda, vilket kan sakta ner frågesvarstiderna för alla databaser i poolen.

**Kortsiktig**, Överväg att skala upp pool för att tillhandahålla ytterligare resurser eller ta bort databaser från poolen (flytta dem till andra pooler eller från poolen till en fristående tjänstnivå).

**Längre sikt**, optimera frågor eller index-syntax för att förbättra prestanda för databasen. Beroende på programmets känslighet för prestandaproblem, är det praxis att skala upp en pool innan den når 100% eDTU-användning. Använd en avisering för att varna dig i förväg.

Du kan simulera en upptagen pool genom att öka belastningen som generatorn skapar. Orsakar databaser till burst oftare och för längre, öka sammanställd belastningen på poolen utan att ändra kraven för enskilda databaser. Det är enkelt att skala upp poolen i portalen eller från PowerShell. Den här övningen använder portalen.

1. Ställ in *$DemoScenario* = **3**, _generera belastning med längre och mer frekventa toppar per databas_ för att öka intensiteten av den sammanställda belastningen på poolen utan att ändra toppbelastningen som krävs av varje databas.
1. Tryck på **F5** för att tillämpa en belastning på alla dina klientdatabaser.

1. Gå till **Pool1** i Azure-portalen.

Övervaka ökad poolen eDTU-användning på övre diagrammet. Det tar några minuter för den nya högre belastning kan förbättra, men du se snabbt poolen börjar nått maximal användning och som belastningen steadies till det nya mönstret, overloads snabbt poolen.

1. Om du vill skala upp poolen klickar du på **konfigurera pool** överst i den **Pool1** sidan.
1. Justera det **Pool-eDTU** till **100**. Ändringar av pool-eDTU:er, ändrar inte inställningarna per databas (som fortfarande är 50 eDTU max per databas). Du kan se per databas-inställningar på höger sida av den **konfigurera pool** sidan.
1. Klicka på **spara** att skicka begäran om att skala poolen.

Gå tillbaka till **Pool1** > **översikt** att visa övervakning diagram. Övervaka effekten av att tillhandahålla poolen med mer resurser (men med några databaser och en slumpmässig belastning inte är det alltid lätt att se säkerhet förrän du kör under en viss tid). När du tittar på diagrammen, tänk på att 100% i det övre diagrammet nu representerar 100 eDTU:er, medan 100% i det lägre diagrammet fortfarande är 50 eDTU:er eftersom max per databas fortfarande är 50 eDTU:er.

Databaserna är online och fullt tillgängliga under hela processen. Under det sista ögonblicket när varje databas är redo att aktiveras med de nya pool-eDTU:erna, bryts alla aktiva anslutningar. Du ska alltid skriva programkod för att återförsöka brutna anslutningar, vilken då kommer att återansluta till databasen i den uppskalade poolen.

## <a name="load-balance-between-pools"></a>Belastningsutjämna mellan pooler

Som ett alternativ till att skala upp poolen, kan du skapa en andra pool och flytta databaserna till den för att balansera belastningen mellan de två poolerna. Det gör du genom att skapa den nya poolen på samma server som den första.

1. I den [Azure-portalen](https://portal.azure.com)öppnar den **tenants1-dpt -&lt;användare&gt; ** server.
1. Klicka på **+ ny pool** att skapa en pool på den aktuella servern.
1. På den **elastisk databaspool** mallen:

    1. Ange **namn** till *Pool2*.
    1. Lämna prisnivån som **standardpool**.
    1. Klicka på **konfigurera poolen**,
    1. Ange **Pool-eDTU** till *50 eDTU*.
    1. Klicka på **lägga till databaser** att se en lista över databaser på den server som kan läggas till *Pool2*.
    1. Välj alla 10 databaser att flytta dem till den nya poolen och klickar sedan på **Välj**. Om du har kört belastningen generator, vet tjänsten redan att profilen prestanda kräver en pool som är större än 50 eDTU standardstorleken och rekommenderar att du börjar med en 100 eDTU-inställning.

    ![rekommendation](media/saas-dbpertenant-performance-monitoring/configure-pool.png)

    1. Låt standardvärdet på 50 edtu: er för den här självstudien och klicka på **Välj** igen.
    1. Välj **OK** att skapa den nya poolen och flytta valda databaserna till den.

Skapa poolen och Flytta databaserna tar några minuter. När databaser flyttas de fortfarande är online och tillgängliga fullständigt tills sista, då alla öppna anslutningar stängs. Så länge som du har några logik ansluter klienter sedan till databasen i den nya poolen.

Bläddra till **Pool2** (på den *tenants1-dpt -\<användare\> * server) att öppna poolen och övervaka dess prestanda. Om du inte ser det, vänta tills etableringen av den nya poolen för att slutföra.

Du ser nu att resursanvändningen på *Pool1* har tagits bort och att *Pool2* läses nu på samma sätt.

## <a name="manage-performance-of-a-single-database"></a>Hantera prestanda för en enskild databas

Om en enskild databas i poolen har problem med en varaktigt hög belastning, kan det beroende poolkonfigurationen dominera resurserna i poolen och påverka andra databaser. Om aktiviteten är sannolikt att fortsätta under en viss tid, flyttas databasen tillfälligt från poolen. Detta gör att databasen ska ha de extra resurser som den behöver och isoleras från andra databaser.

Den här övningen simulerar effekten av att Contosos konserthall upplever en hög belastning när biljetter börjar säljas för ett populärt evenemang.

1. I den **PowerShell ISE**öppnar den... \\ *Demo-PerformanceMonitoringAndManagement.ps1* skript.
1. Ställ in **$DemoScenario = 5, generera en normal belastning plus en hög belastning för en enskild klient (cirka 95 DTU).**
1. Ställ in **$SingleTenantDatabaseName = contosoconcerthall**
1. Kör skriptet med **F5**.


1. I den [Azure-portalen](https://portal.azure.com), bläddra till listan över databaser på den *tenants1-dpt -\<användare\> * server. 
1. Klicka på den **contosoconcerthall** databas.
1. Klicka på poolen som **contosoconcerthall** finns i. Hitta poolen i den **elastisk databaspool** avsnitt.

1. Kontrollera den **elastisk pool övervakning** diagram och leta efter ökad pool-eDTU-användning. Efter någon minut sätter den högre belastningen in och du borde snabbt se att poolen når 100% användning.
2. Kontrollera den **elastisk databas övervakning** visas, vilket visar de mest populära databaserna under den senaste timmen. Den *contosoconcerthall* databasen snart ska visas som en av de fem senaste databaserna.
3. **Klicka på övervakning elastisk databas** **diagram** och öppnar den **databasen resursutnyttjande** sida där du kan övervaka någon av databaserna. På så sätt kan du isolera visa för den *contosoconcerthall* databas.
4. Från listan över databaser, klickar du på **contosoconcerthall**.
5. Klicka på **prisnivån (skala dtu: er)** att öppna den **konfigurera prestanda** sida där du kan ange en fristående prestandanivå för databasen.
6. Klicka på **Standard**-fliken för att öppna skalningsalternativen på standardnivån.
7. Dra den **DTU skjutreglaget** till höger för att välja **100** dtu: er. Obs detta motsvarar tjänstmålet, **S3**.
8. Klicka på **tillämpa** flytta databasen från poolen och ge den en *Standard S3* databas.
9. När skalning är klar kan du övervaka effekten på contosoconcerthall databasen och Pool1 på de elastiska poolen och databas-blad.

När hög belastning på databasen för contosoconcerthall subsides returnera du den för att minska kostnaden för dess. Om det är oklart om som sker du kan ange en avisering på databasen som utlöses när dess DTU-användningen sjunker under den per databasen max på poolen. Övrning 5 beskriver hur du flyttar en databas till en pool.

## <a name="other-performance-management-patterns"></a>Andra mönster för hantering av prestanda

**Pre-emptive skalning** i övning ovan där utforskade du hur du skalar en isolerad databas du vet vilken databas som ska sökas. Om hantering av Contoso samklang Hall hade Wingtips för nära förestående biljett försäljning, ha databasen flyttats från poolen pre-emptively. Annars hade det sannolikt behövts en avisering på poolen eller databasen för att upptäcka vad som hände. Du vill inte få reda på sånt här genom att andra klienter i poolen klagar på försämrad prestanda. Och om klienten kan förutse hur länge de behöver ytterligare resurser, kan du ställa in en Azure Automation-runbook för att flytta ut databasen från poolen och sedan tillbaka igen enligt ett definierat schema.

**Klientskalning som självservice** eftersom skalning är en aktivitet som enkelt kan anropas via hanterings-API:t, kan du enkelt bygga in möjligheten att skala klientdatabaser i ditt klientriktade program och erbjuda de som en funktion av din SaaS-tjänst. Exempelvis kan du låta klienter självhantera upp- och nedskalning, kanske kopplat direkt till sin fakturering!

**Skala upp och ner poolen enligt ett schema så att den matchar användningsmönster**

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

* Ytterligare [självstudier som bygger på programdistribution Wingtip biljetter SaaS databas Per klient](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Elastiska SQL-pooler](sql-database-elastic-pool.md)
* [Azure Automation](../automation/automation-intro.md)
* [Log Analytics](saas-dbpertenant-log-analytics.md) - Guide för att konfigurera och använda Log Analytics
