---
title: 'Saas-appen: Övervaka prestanda för många databaser'
description: Övervaka och hantera prestanda för Azure SQL-databaser och -pooler i en SaaS-app med flera innehavare
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: 34c50795567615637e31446ad3dc51a5e1b355f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79214456"
---
# <a name="monitor-and-manage-performance-of-azure-sql-databases-and-pools-in-a-multi-tenant-saas-app"></a>Övervaka och hantera prestanda för Azure SQL-databaser och -pooler i en SaaS-app med flera innehavare

I den här självstudien utforskas flera viktiga prestandahanteringsscenarier som används i SaaS-program. Med hjälp av en belastningsgenerator för att simulera aktivitet i alla klientdatabaser demonstreras de inbyggda övervaknings- och varningsfunktionerna i SQL Database och elastiska pooler.

Wingtip Tickets SaaS Database Per Tenant-appen använder en datamodell för en klientorganisation, där varje plats (klient) har en egen databas. Precis som för flera SaaS-program så är de förväntade belastningsmönstren för klienterna oberäkneliga och sporadiska. Biljettförsäljningar kan med andra ord ske när som helst. För att dra nytta av det här typiska databasanvändningsmönstret distribueras klientdatabaser i elastiska pooler. Elastiska pooler optimerar kostnaderna för en lösning genom att dela resurser över flera databaser. Med den här typen av mönster är det viktigt att övervaka användningen av databas- och poolresurser för att försäkra att belastningarna balanseras över poolerna. Du behöver också se till att enskilda databaser har tillräckliga resurser och att pooler inte träffar sina [eDTU](sql-database-purchase-models.md#dtu-based-purchasing-model)-gränser. Den här guiden går igenom sätt att övervaka och hantera databaser och pooler och hur man vidtar åtgärder i respons på belastningsvariationer.

I den här guiden får du lära du dig hur man:

> [!div class="checklist"]
> 
> * Simulerar användning på klientdatabaserna genom att köra en angiven belastningsgenerator
> * Övervakar klientdatabaserna när de svarar på belastningsökningar
> * Skalar upp den elastiska poolen i svar på den ökade databasbelastningen
> * Etablerar en andra elastisk pool för att belastningsutjämna databasaktiviteten


Följande krav måste uppfyllas för att kunna köra den här självstudiekursen:

* Den Wingtip Biljetter SaaS Databas per klient app distribueras. Information om hur du distribuerar på mindre än fem minuter finns i [Distribuera och utforska Programmet Wingtip Tickets SaaS Database Per Tenant](saas-dbpertenant-get-started-deploy.md)
* Azure PowerShell ska ha installerats. Mer information finns i [Kom igång med Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-saas-performance-management-patterns"></a>Introduktion till SaaS prestandahanteringsmönster

Hantering av databasprestanda innebär kompilering och analys av prestandadata för att sedan reagera på den genom att justera parametrarna för att bibehålla en acceptabel svarstid för programmet. När du är värd för flera klienter är elastiska pooler ett kostnadseffektivt sätt att tillhandahålla och hantera resurser för en grupp databaser med oförutsägbara arbetsbelastningar. Med vissa arbetsbelastningmönster, kan så få som två S3-databaser dra nytta av att hanteras i en pool.

![programdiagram](./media/saas-dbpertenant-performance-monitoring/app-diagram.png)

Pooler och databaser i pooler bör övervakas för att säkerställa att de håller sig inom godtagbara prestandaintervall. Justera poolkonfigurationen så att den uppfyller behoven hos den sammanlagda arbetsbelastningen för alla databaser, så att pool-eDT:erna är lämpliga för den totala arbetsbelastningen. Justera per databas min och max eDTU-värden till lämpliga värden för dina specifika programkrav.

### <a name="performance-management-strategies"></a>Strategier för prestandahantering

* För att undvika att behöva övervaka prestanda manuellt är det mest effektivt att **ställa in aviseringar som utlöses när databaser eller pooler avviker från normala intervall**.
* Om du vill svara på kortsiktiga fluktuationer i den sammanlagda beräkningsstorleken för en pool **kan pool-eDTU-nivån skalas upp eller ned**. Om de här variationerna inträffar på regelbunden eller förutsägbar basis, **kan skalning av poolen schemaläggas att ske automatiskt**. Du kan exempelvis skala ned när du vet att din arbetsbelastning är lätt, på nätter eller helger till exempel.
* För att svara på mer långsiktiga variationer eller förändringar av antalet databaser, **kan enskilda databaser flyttas till andra pooler**.
* För att svara på kortsiktiga ökningar i *enskilda* databasinläsning **kan enskilda databaser tas ut ur en pool och tilldelas en enskild beräkningsstorlek**. När belastningen minskar, kan databasen returneras till poolen. När detta är känt i förväg kan databaser flyttas förebyggande för att säkerställa att databasen alltid har de resurser den behöver och för att undvika påverkan på andra databaser i poolen. Om det här kravet är förutsägbart, till exempel en plats där ett populärt evenemang skapar en rusning efter biljetter, kan det här hanteringsbeteendet integreras i programmet.

[Azure-portalen](https://portal.azure.com) tillhandahåller inbyggd övervakning och avisering för de flesta resurser. För SQL Database, finns övervakning och avisering tillgängligt för databaser och pooler. Den här inbyggda övervakningen och aviseringen är resursspecifik, så det är praktiskt att använda för ett litet antal resurser, men är inte särskilt bekvämt när du arbetar med många resurser.

För scenarier med stora volymer, där du arbetar med många resurser, kan [Azure Monitor-loggar](saas-dbpertenant-log-analytics.md) användas. Det här är en separat Azure-tjänst som tillhandahåller analyser över utgivna loggar som samlats in i en Log Analytics-arbetsyta. Azure Monitor-loggar kan samla in telemetri från många tjänster och användas för att fråga och ställa in aviseringar.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Hämta Wingtip Tickets SaaS Database Per Tenant-programskript

Wingtip-biljetterna SaaS Multi-tenant Database skript och program källkod finns i [WingtipTicketsSSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub repo. Kolla in den [allmänna vägledningen](saas-tenancy-wingtip-app-guidance-tips.md) för steg för att ladda ner och låsa upp Wingtip Tickets SaaS skript.

## <a name="provision-additional-tenants"></a>Etablera ytterligare klienter

Pooler kan vara kostnadseffektiva med bara två S3 databaser, men ju fler databaser som finns i poolen, desto mer kostnadseffektiv blir genomsnittseffekten. För att få en bra förståelse för hur prestandaövervakning och hantering fungerar i stor skala, kräver den här kursen du har distribuerat minst 20 databaser.

Om du redan har etablerat en batch med klienter i en föregående självstudiekurs går du till avsnittet [Simulera användning på alla klientdatabaser.](#simulate-usage-on-all-tenant-databases)

1. I **PowerShell ISE**, öppna ... \\Lärande Moduler\\Prestanda övervakning\\och förvaltning*Demo-PerformanceMonitoringAndManagement.ps1*. Ha det här skriptet öppet medan du kör scenarierna i den här guiden.
1. Ange **$DemoScenario** = **1**, **Etablera en batch med klienter**
1. Tryck **F5** för att köra skriptet.

Skriptet distribuerar 17 klienter på mindre än fem minuter.

Skriptet *Ny-TenantBatch* använder en kapslad eller länkad uppsättning [Resource Manager-mallar](../azure-resource-manager/index.yml) som skapar en batch med klienter, som som standard kopierar **databasbastenantdb** på katalogservern för att skapa de nya klientdatabaserna, sedan registrerar dessa i katalogen och slutligen initierar dem med klientnamn och platstyp. Detta är förenligt med hur appen etablerar en ny klient. Alla ändringar som görs *i basetenantdb* tillämpas på alla nya klienter som etablerats därefter. Se [självstudien schemahantering](saas-tenancy-schema-management.md) för att se hur du gör schemaändringar i *befintliga* klientdatabaser (inklusive *basetenantdb-databasen).*

## <a name="simulate-usage-on-all-tenant-databases"></a>Simulera användning på alla klientdatabaser

*Skriptet Demo-PerformanceMonitoringAndManagement.ps1* tillhandahålls som simulerar en arbetsbelastning som körs mot alla klientdatabaser. Belastningen genereras med hjälp av ett av de tillgängliga belastningsscenarierna:

| Demo | Scenario |
|:--|:--|
| 2 | Generera normal intensitetsbelastning (ca 40 DTU) |
| 3 | Generera belastning med längre och mer frekventa toppar per databas|
| 4 | Generera belastning med högre DTU-skurar per databas (cirka 80 DTU)|
| 5 | Generera en normal belastning plus en hög belastning på en enda klient (cirka 95 DTU)|
| 6 | Generera obalanserad belastning över flera pooler|

Belastningsgeneratorn tillämpar en *syntetisk* enbart-CPU-belastning på varje klientdatabas. Generatorn startar ett jobb för varje klientdatabas som anropar en lagrad procedur med jämna mellanrum, vilket genererar belastningen. Belastningsnivåer (i eDTU:er), varaktighet och intervaller varierar över alla databaser, vilket simulerar oförutsägbar klientaktivitet.

1. I **PowerShell ISE**, öppna ... \\Lärande Moduler\\Prestanda övervakning\\och förvaltning*Demo-PerformanceMonitoringAndManagement.ps1*. Ha det här skriptet öppet medan du kör scenarierna i den här guiden.
1. Ställ in **$DemoScenario** = **2**, *Generera normal intensitet belastning*.
1. Tryck på **F5** för att tillämpa en belastning på alla dina klientdatabaser.

Wingtip Tickets SaaS Database Per Tenant är en SaaS-app, och den verkliga belastningen på en SaaS-app är vanligtvis sporadisk och oförutsägbar. För att simulera det så skapar belastningsgeneratorn en slumpmässig belastning som distribueras över alla klienterna. Flera minuter behövs för att belastningsmönstret ska dyka upp, så kör lastgeneratorn i 3-5 minuter innan du försöker övervaka belastningen i följande avsnitt.

> [!IMPORTANT]
> Belastningsgeneratorn körs som en serie jobb i din lokala PowerShell-session. Behåll fliken *Demo-PerformanceMonitoringAndManagement.ps1* öppen! Om du stänger fliken eller pausar din dator, stannar belastningsgeneratorn. Lastgeneratorn förblir i ett *jobbansnat* tillstånd där den genererar belastning på alla nya klienter som etableras när generatorn har startats. Använd *Ctrl-C* för att sluta anropa nya jobb och avsluta skriptet. Lastgeneratorn fortsätter att köras, men bara på befintliga klienter.

## <a name="monitor-resource-usage-using-the-azure-portal"></a>Övervaka resursanvändning med Hjälp av Azure-portalen

Om du vill övervaka resursanvändningen som uppstår vid inläsningen öppnar du portalen till poolen som innehåller klientdatabaserna:

1. Öppna [Azure-portalen](https://portal.azure.com) och bläddra till *&lt;klienterna1-dpt-USER-servern.&gt; *
1. Bläddra ned och hitta elastiska pooler och klicka på **Pool1**. Den här poolen innehåller alla klientdatabaser som skapats än så länge.

Observera **övervakningsdiagram** för elastisk poolövervakning **och elastisk databas.**

Poolens resursutnyttjande är den sammanlagda databasanvändningen för alla databaser i poolen. Databasdiagrammet visar de fem hetaste databaserna:

![databasdiagram](./media/saas-dbpertenant-performance-monitoring/pool1.png)

Eftersom det finns ytterligare databaser i poolen utanför de fem översta, visar poolutnyttjandet aktivitet som inte återspeglas i topp fem databasdiagrammet. Om du vill veta mer klickar du på **Databasresursutnyttjande:**

![användning av databasresurser](./media/saas-dbpertenant-performance-monitoring/database-utilization.png)


## <a name="set-performance-alerts-on-the-pool"></a>Ställ in prestandaaviseringar för poolen

Ställ in en avisering på \>poolen som utlöser 75 % användning enligt följande:

1. Öppna *Pool1* (på *klienten1-dpt-användarservern)\<\> i Azure-portalen* . [Azure portal](https://portal.azure.com)
1. Klicka på **aviseringsregler** och därefter på **+ lägg till avisering**:

   ![lägg till avisering](media/saas-dbpertenant-performance-monitoring/add-alert.png)

1. Ange ett namn, exempelvis **hög DTU**,
1. Ställ in följande värden:
   * **Mått = eDTU procent**
   * **Villkor = större än**
   * **Tröskel = 75**
   * **Period = Under de senaste 30 minuterna**
1. Lägg till en e-postadress i rutan *Ytterligare administratörs-e-post* och klicka på **OK**.

   ![ange varning](media/saas-dbpertenant-performance-monitoring/alert-rule.png)


## <a name="scale-up-a-busy-pool"></a>Skala upp en upptagen pool

Om den sammanställda belastningsnivån ökar på en pool till den punkt där poolen maxas ut och den når 100% eDTU-användning så påverkas individuell databasprestanda, vilket kan sakta ner frågesvarstiderna för alla databaser i poolen.

**Kortsiktigt**, överväg att skala upp poolen för att tillhandahålla ytterligare resurser eller ta bort databaser från poolen (flytta dem till andra pooler eller ut ur poolen till en fristående tjänstnivå).

**Längre sikt**bör du överväga att optimera frågor eller indexanvändning för att förbättra databasens prestanda. Beroende på programmets känslighet för prestandaproblem, är det praxis att skala upp en pool innan den når 100% eDTU-användning. Använd en avisering för att varna dig i förväg.

Du kan simulera en upptagen pool genom att öka belastningen som generatorn skapar. Vilket gör att databaserna spricker oftare och längre ökar den sammanlagda belastningen på poolen utan att ändra kraven för de enskilda databaserna. Det är enkelt att skala upp poolen i portalen eller från PowerShell. Den här övningen använder portalen.

1. Ange *$DemoScenario* = **3**, _Generera belastning med längre och mer frekventa skurar per databas_ för att öka intensiteten för den sammanlagda belastningen på poolen utan att ändra den toppbelastning som krävs av varje databas.
1. Tryck på **F5** för att tillämpa en belastning på alla dina klientdatabaser.

1. Gå till **Pool1** i Azure-portalen.

Övervaka den ökade pool-eDTU-användningen i det övre diagrammet. Det tar några minuter för den nya högre belastningen att sparka in, men du bör snabbt se poolen börjar slå max utnyttjande, och som belastningen stadigt i det nya mönstret, överbelastar den snabbt poolen.

1. Om du vill skala upp poolen klickar du på **Konfigurera pool** högst upp på **sidan Pool1.**
1. Justera **inställningen för pool eDTU** till **100**. Ändringar av pool-eDTU:er, ändrar inte inställningarna per databas (som fortfarande är 50 eDTU max per databas). Du kan se inställningarna per databas till höger på **poolsidan Konfigurera.**
1. Klicka på **Spara** om du vill skicka begäran om att skala poolen.

Gå tillbaka till **Pool1** > **Översikt** för att visa övervakningsdiagrammen. Övervaka effekten av att ge poolen mer resurser (även om det med få databaser och en randomiserad belastning inte alltid är lätt att se slutgiltigt tills du kör under en tid). När du tittar på diagrammen, tänk på att 100% i det övre diagrammet nu representerar 100 eDTU:er, medan 100% i det lägre diagrammet fortfarande är 50 eDTU:er eftersom max per databas fortfarande är 50 eDTU:er.

Databaserna är online och fullt tillgängliga under hela processen. Under det sista ögonblicket när varje databas är redo att aktiveras med de nya pool-eDTU:erna, bryts alla aktiva anslutningar. Du ska alltid skriva programkod för att återförsöka brutna anslutningar, vilken då kommer att återansluta till databasen i den uppskalade poolen.

## <a name="load-balance-between-pools"></a>Belastningsbalans mellan pooler

Som ett alternativ till att skala upp poolen, kan du skapa en andra pool och flytta databaserna till den för att balansera belastningen mellan de två poolerna. Det gör du genom att skapa den nya poolen på samma server som den första.

1. Öppna **servern tenants1-dpt- USER&lt;&gt; i Azure-portalen.** [Azure portal](https://portal.azure.com)
1. Klicka på **+ Ny pool** om du vill skapa en pool på den aktuella servern.
1. På mallen **Elastisk pool:**

   1. Ange **namn** till *Pool2*.
   1. Lämna prisnivån som **standardpool**.
   1. Klicka på **Konfigurera pool**,
   1. Ställ in **pool eDTU** till *50 eDTU*.
   1. Klicka på **Lägg till databaser** om du vill visa en lista över databaser på servern som kan läggas till i *Pool2*.
   1. Markera de tio databaserna om du vill flytta dessa till den nya poolen och klicka sedan på **Välj**. Om du har kört lastgeneratorn vet tjänsten redan att din prestandaprofil kräver en större pool än standardstorleken på 50 eDTU och rekommenderar att du börjar med en 100 eDTU-inställning.

      ![Rekommendation](media/saas-dbpertenant-performance-monitoring/configure-pool.png)

   1. För den här självstudien lämnar du standardvärdet på 50 eDTU:er och klickar på **Välj** igen.
   1. Välj **OK** om du vill skapa den nya poolen och flytta de markerade databaserna till den.

Det tar några minuter att skapa poolen och flytta databaserna. När databaser flyttas förblir de online och fullt tillgängliga fram till sista stund, då alla öppna anslutningar stängs. Så länge du har några återförsök logik, klienter kommer sedan att ansluta till databasen i den nya poolen.

Bläddra till **Pool2** (på *klienterna1-dpt-användarserver)\<\> * för att öppna poolen och övervaka dess prestanda. Om du inte ser den väntar du på att etableringen av den nya poolen ska slutföras.

Du ser nu att resursanvändningen på *Pool1* har minskat och att *Pool2* nu har lästs in på samma sätt.

## <a name="manage-performance-of-an-individual-database"></a>Hantera prestanda för en enskild databas

Om en enskild databas i en pool har en ihållande hög belastning, beroende på poolkonfigurationen, kan den tenderar att dominera resurserna i poolen och påverka andra databaser. Om aktiviteten sannolikt kommer att fortsätta under en tid kan databasen tillfälligt flyttas från poolen. Detta gör att databasen kan ha de extra resurser den behöver och isolerar den från de andra databaserna.

Den här övningen simulerar effekten av att Contosos konserthall upplever en hög belastning när biljetter börjar säljas för ett populärt evenemang.

1. I **PowerShell ISE**, öppna ... \\ *Demo-PerformanceMonitoringAndManagement.ps1* script.
1. Ställ in **$DemoScenario = 5, Generera en normal belastning plus en hög belastning på en enda klient (ca 95 DTU).**
1. Ställ in **$SingleTenantDatabaseName = contosoconcerthall**
1. Kör skriptet med **F5**.


1. I [Azure-portalen](https://portal.azure.com)bläddrar du till listan över databaser på *\<klienterna1-dpt-användarservern.\> * 
1. Klicka på **contosoconcerthall-databasen.**
1. Klicka på poolen som **contosoconcerthall** är i. Leta upp poolen i avsnittet **Elastisk pool.**

1. Kontrollera **övervakningsdiagrammet för elastisk pool** och leta efter den ökade eDTU-användningen för poolen. Efter någon minut sätter den högre belastningen in och du borde snabbt se att poolen når 100% användning.
2. Kontrollera den **elastiska databasövervakningsvisningen,** som visar de hetaste databaserna under den senaste timmen. *Contosoconcerthall-databasen* bör snart visas som en av de fem hetaste databaserna.
3. **Klicka på övervakningsdiagrammet för elastisk databas** **chart** och den öppnar sidan **Databasresursanvändning** där du kan övervaka någon av databaserna. På så sätt kan du isolera visningen för *contosoconcerthall-databasen.*
4. Klicka på **contosoconcerthall**i listan över databaser .
5. Klicka på **Prisnivå (skala DU:er)** för att öppna sidan **Konfigurera prestanda** där du kan ange en fristående beräkningsstorlek för databasen.
6. Klicka på **Standard**-fliken för att öppna skalningsalternativen på standardnivån.
7. Skjut **skjutreglaget DTU** till höger för att välja **100** DTU:er. Observera att detta motsvarar servicemålet, **S3**.
8. Klicka på **Använd** om du vill flytta databasen från poolen och göra den till en *Standard S3-databas.*
9. När skalningen är klar övervakar du effekten på contosoconcerthall-databasen och Pool1 på den elastiska poolen och databasbladen.

När den höga belastningen på contosoconcerthall-databasen avtar bör du omedelbart returnera den till poolen för att minska dess kostnad. Om det är oklart när det kommer att hända kan du ställa in en avisering på databasen som utlöser när dess DTU-användning sjunker under max per databas på poolen. Övrning 5 beskriver hur du flyttar en databas till en pool.

## <a name="other-performance-management-patterns"></a>Andra mönster för prestandahantering

**Förebyggande skalning** I övningen ovan där du utforskade hur du skalar en isolerad databas visste du vilken databas du skulle leta efter. Om ledningen av Contoso Concert Hall hade informerat Wingtips om den förestående biljettförsäljningen, kunde databasen ha flyttats ut ur poolen förebyggande. Annars hade det sannolikt behövts en avisering på poolen eller databasen för att upptäcka vad som hände. Du vill inte få reda på sånt här genom att andra klienter i poolen klagar på försämrad prestanda. Och om klienten kan förutse hur länge de behöver ytterligare resurser, kan du ställa in en Azure Automation-runbook för att flytta ut databasen från poolen och sedan tillbaka igen enligt ett definierat schema.

**Klientskalning som självservice** eftersom skalning är en aktivitet som enkelt kan anropas via hanterings-API:t, kan du enkelt bygga in möjligheten att skala klientdatabaser i ditt klientriktade program och erbjuda de som en funktion av din SaaS-tjänst. Exempelvis kan du låta klienter självhantera upp- och nedskalning, kanske kopplat direkt till sin fakturering!

**Skala en pool upp och ned enligt ett schema för att matcha användningsmönster**

I de fall där sammanställd klientanvändning följer förutsägbara mönster, kan du använda Azure Automation för att skala upp en pool efter ett schema. Skala exempelvis ned en pool efter 18.00 och upp igen innan 06.00 på veckodagar, när du vet att kraven på resursanvändning går ned.



## <a name="next-steps"></a>Nästa steg

I den här guiden får du lära du dig hur man:

> [!div class="checklist"]
> * Simulerar användning på klientdatabaserna genom att köra en angiven belastningsgenerator
> * Övervakar klientdatabaserna när de svarar på belastningsökningar
> * Skalar upp den elastiska poolen i svar på den ökade databasbelastningen
> * Etablerar en andra elastisk pool för att belastningsutjämna databasaktiviteten

[Guiden återställ en enskild klient](saas-dbpertenant-restore-single-tenant.md)


## <a name="additional-resources"></a>Ytterligare resurser

* Ytterligare [självstudier som bygger på Wingtip Tickets SaaS Database Per Tenant-programdistribution](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [ELASTISKA SQL-pooler](sql-database-elastic-pool.md)
* [Azure-automatisering](../automation/automation-intro.md)
* [Azure Monitor-loggar](saas-dbpertenant-log-analytics.md) – konfigurera och använda självstudiekurs för Azure Monitor-loggar
