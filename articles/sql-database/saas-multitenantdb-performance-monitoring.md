---
title: Övervaka prestanda för en delat flera innehavare Azure SQL-databas i en app för flera innehavare SaaS | Microsoft Docs
description: Övervaka och hantera prestanda för delat flera innehavare Azure SQL-databas i en SaaS-app för flera innehavare
keywords: sql database tutorial
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: 75431715b5948525e92c99b778842d26a684da82
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2018
ms.locfileid: "36753461"
---
# <a name="monitor-and-manage-performance-of-sharded-multi-tenant-azure-sql-database-in-a-multi-tenant-saas-app"></a>Övervaka och hantera prestanda för delat flera innehavare Azure SQL-databas i en SaaS-app för flera innehavare

I den här självstudiekursen beskrivs flera KPI hanteringsscenarier som används i SaaS-program. Använder en load-generatorn för att simulera aktiviteten över delat databaser för flera innehavare, är inbyggd övervakning och avisering funktioner i SQL-databas visas.

Appen Wingtip biljetter SaaS flera innehavare databasen använder ett delat flera innehavare datamodellen, där distribueras plats (klient) data genom att klient-ID på eventuellt flera databaser. Precis som för flera SaaS-program så är de förväntade belastningsmönstren för klienterna oberäkneliga och sporadiska. Biljettförsäljningar kan med andra ord ske när som helst. Om du vill dra nytta av den här databasen användningsmönstret kan databaser skalas upp och ned för att optimera kostnaden för en lösning. Med den här typen av mönstret är det viktigt att övervaka Resursanvändning för databasen för att säkerställa att belastningar rimligen balanserad över potentiellt flera databaser. Du måste också se till att enskilda databaser har tillräckliga resurser och inte träffa sina [DTU](sql-database-service-tiers.md#what-are-database-transaction-units-dtus) gränser. Den här självstudiekursen utforskar sätt att övervaka och hantera databaser och hur du vidta åtgärder som svar på variationer i arbetsbelastning.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]

> * Simulera användning av en databas som delat flera innehavare genom att köra ett angivet belastningen generator
> * Övervaka databasen som den svarar på ökade belastningen
> * Skala upp databasen som svar på databasbelastningen ökar
> * Etablera en klient till en enskild klient-databas

Följande krav måste uppfyllas för att kunna köra den här självstudiekursen:

* Wingtip biljetter SaaS flera innehavare databasen appen har distribuerats. För att distribuera på mindre än fem minuter finns [distribuera och utforska databasprogram Wingtip biljetter SaaS flera innehavare](saas-multitenantdb-get-started-deploy.md)
* Azure PowerShell ska ha installerats. Mer information finns i [Komma igång med Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-saas-performance-management-patterns"></a>Introduktion till SaaS prestanda management mönster

Hantering av databasprestanda innebär kompilering och analys av prestandadata för att sedan reagera på den genom att justera parametrarna för att bibehålla en acceptabel svarstid för programmet. 

### <a name="performance-management-strategies"></a>Strategier för prestandahantering

* Om du vill undvika att övervaka prestanda manuellt är det mest effektiva till **ställa in aviseringar som utlöses när databaser avvika utanför normal adressintervall**.
* Att svara på kortsiktiga variationer i prestandanivåerna för en databas i **DTU nivå kan skalas upp eller ned**. Om den här variationerna inträffar på grundval regelbundna eller förutsägbar **skalning databasen kan schemaläggas ska ske automatiskt**. Du kan exempelvis skala ned när du vet att din arbetsbelastning är lätt, på nätter eller helger till exempel.
* Svara till långsiktig variationer eller ändringar i innehavarna **enskilda klienter kan flyttas till andra databasen**.
* Att svara på kort sikt ökningar i *enskilda* klient belastningen **enskilda klienter kan tas bort från en databas och tilldelats en enskilda prestandanivå**. När belastningen minskar, kan klienten sedan returneras till databasen för flera innehavare. När det är känt i förväg, kan klienter flyttas pre-emptively att databasen har alltid resurser som behövs och för att undvika påverkan på andra klienter i databasen för flera innehavare. Om det här kravet är förutsägbart, till exempel en plats där ett populärt evenemang skapar en rusning efter biljetter, kan det här hanteringsbeteendet integreras i programmet.

[Azure-portalen](https://portal.azure.com) tillhandahåller inbyggd övervakning och avisering för de flesta resurser. För SQL-databas är övervakning och avisering tillgängligt för databaser. Den här inbyggda övervakning och avisering är resurs-specifika, så det är praktiskt att använda för litet antal resurser, men är inte praktiskt när du arbetar med många resurser.

Omfattande scenarier där du arbetar med många resurser, [logganalys](https://azure.microsoft.com/services/log-analytics/) kan användas. Detta är en separat Azure-tjänst som ger analytics över skickade diagnostikloggar och telemetri som samlats in i en log analytics-arbetsyta. Logganalys kan samla in telemetri från många tjänster och användas för att fråga efter och Ställ in aviseringar.

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Hämta Wingtip biljetter SaaS flera innehavare databasen programmets källkod och skript

Wingtip biljetter SaaS flera innehavare databasen skript och programmets källkod är tillgängliga i den [WingtipTicketsSaaS MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) GitHub-lagringsplatsen. Kolla in den [allmänna riktlinjer](saas-tenancy-wingtip-app-guidance-tips.md) steg för att ladda ned och avblockera Wingtip biljetter SaaS-skript.

## <a name="provision-additional-tenants"></a>Etablera ytterligare klienter

För en god förståelse av hur hantering och övervakning av programprestanda fungerar i stor skala måste den här självstudiekursen du har flera klienter i ett delat databas för flera innehavare.

Om du har redan etablerats en grupp med klienter i en tidigare kursen, går du till den [simulera användning för alla databaser som klient](#simulate-usage-on-all-tenant-databases) avsnitt.

1. I den **PowerShell ISE**öppnar... \\Learning moduler\\prestandaövervakning och hantering av\\*Demo-PerformanceMonitoringAndManagement.ps1*. Ha det här skriptet öppet medan du kör scenarierna i den här guiden.
1. Ställ in **$DemoScenario** = **1**, _etablera en batch med klienter_
1. Tryck **F5** för att köra skriptet.

Skriptet distribuerar 17 innehavare i databasen med flera innehavare i ett par minuter. 

Den *ny TenantBatch* skriptet skapar nya klienter med unika innehavaren nycklar i delat flera innehavare databasen och initierar dem med typen klient namn och plats. Detta är förenligt med hur appen etablerar en ny klient. 

## <a name="simulate-usage-on-all-tenant-databases"></a>Simulera användning på alla klientdatabaser

Den *Demo-PerformanceMonitoringAndManagement.ps1* skript har angetts som simulerar en arbetsbelastning som körs mot databasen för flera innehavare. Belastningen skapas med någon av de tillgängliga belastning scenarierna:

| Demo | Scenario |
|:--|:--|
| 2 | Generera normal styrka belastning (ca 30 DTU) |
| 3 | Generera belastningen med längre belastning per klient|
| 4 | Generera belastningen med högre belastning för DTU per klient (ungefärlig 70 DTU)|
| 5 | Generera en hög intensitet (ca 90 DTU) för en enskild klient plus en normal styrka belastning på alla andra klienter |

Belastningsgeneratorn tillämpar en *syntetisk* enbart-CPU-belastning på varje klientdatabas. Generatorn startar ett jobb för varje klientdatabas som anropar en lagrad procedur med jämna mellanrum, vilket genererar belastningen. Belastningsnivåer (i dtu: er), varaktighet och intervall varieras över alla databaser som simulerar oförutsägbart klient aktivitet.

1. I den **PowerShell ISE**öppnar... \\Learning moduler\\prestandaövervakning och hantering av\\*Demo-PerformanceMonitoringAndManagement.ps1*. Ha det här skriptet öppet medan du kör scenarierna i den här guiden.
1. Ange **$DemoScenario** = **2**, _generera normal styrka belastning_
1. Tryck på **F5** att tillämpa en belastning på alla klienter.

Wingtip biljetter SaaS flera innehavare databasen är en SaaS-app och verkliga belastningen på en SaaS-app är vanligtvis sporadiska och oförutsägbart. För att simulera det så skapar belastningsgeneratorn en slumpmässig belastning som distribueras över alla klienterna. Flera minuter krävs för belastningen mönstret ut, så kör belastningen generatorn för 3-5 minuter innan du försöker att övervaka belastningen i följande avsnitt.

> [!IMPORTANT]
> Läs in generator körs som en serie jobb i ett nytt PowerShell-fönster. Om du stänger sessionen avbryts generatorn belastningen. Läs in generator finns kvar i en *jobbet anropar* tillstånd där den genererar belastningen på alla nya klienter som tillhandahålls när generatorn har startats. Använd *Ctrl-C* att stoppa anropar nya jobb och avslutas skriptet. Läs in generator fortsätter att köras, men endast på befintliga klienter.

## <a name="monitor-resource-usage-using-the-azure-portal"></a>Övervaka Resursanvändning i Azure Portal

Öppna portalen för flera innehavare i databasen för att övervaka Resursanvändning som resultat av belastningen tillämpas, **tenants1**, som innehåller klienterna:

1. Öppna den [Azure-portalen](https://portal.azure.com) och bläddra till servern *tenants1-huvudmålservern -&lt;användare&gt;*.
1. Bläddra nedåt och hitta databaser och klickar på **tenants1**. Delat flera innehavare databasen innehåller alla klienter som skapat hittills.

![diagram för databasen](./media/saas-multitenantdb-performance-monitoring/multitenantdb.png)

Se den **DTU** diagram.

## <a name="set-performance-alerts-on-the-database"></a>Ange prestandavarningar i databasen

Ange en varning på databasen som utlösare på \>75% användning på följande sätt:

1. Öppna den *tenants1* databasen (på den *tenants1-huvudmålservern -&lt;användare&gt;*  server) i den [Azure-portalen](https://portal.azure.com).
1. Klicka på **aviseringsregler** och därefter på **+ lägg till avisering**:

   ![lägg till avisering](media/saas-multitenantdb-performance-monitoring/add-alert.png)

1. Ange ett namn, exempelvis **hög DTU**,
1. Ställ in följande värden:
   * **Måttet = DTU-procent**
   * **Villkor = större än**
   * **Tröskelvärdet = 75**.
   * **Period = under de senaste 30 minuterna**
1. Lägg till en e-postadress till den *ytterligare administratören email(s)* och klicka på **OK**.

   ![ange varning](media/saas-multitenantdb-performance-monitoring/set-alert.png)

## <a name="scale-up-a-busy-database"></a>Skala upp en upptagen databas

Om nivån belastningen ökar på en databas till den punkt som maximerar databasen och når 100% DTU-användningen, sedan påverkas databasens prestanda, potentiellt saktas svarstider för frågan.

**Kort sikt**, Överväg att skala upp databasen för att tillhandahålla ytterligare resurser eller ta bort klienter från databasen med flera innehavare (flyttas från databasen för flera innehavare till en fristående databas).

**Längre sikt**, optimera frågor eller index-syntax för att förbättra prestanda för databasen. Beroende på programmets känslighet för prestanda utfärdar sin bästa praxis för att skala en databas innan den når 100% DTU-användningen. Använd en avisering för att varna dig i förväg.

Du kan simulera en upptagen databas genom att öka belastningen produceras av generator. Orsakar innehavarna som ska burst oftare och för längre, ökar belastningen på flera innehavare databas utan att ändra kraven för enskilda klienter. Skala upp databasen görs enkelt i portalen eller från PowerShell. Den här övningen använder portalen.

1. Ange *$DemoScenario* = **3**, _generera belastningen med längre och oftare belastning per databas_ att öka intensiteten sammanställd belastningen på den databas utan att ändra den belastning som krävs för varje klient.
1. Tryck på **F5** för att tillämpa en belastning på alla dina klientdatabaser.
1. Gå till den **tenants1** databas i Azure-portalen.

Övervaka de ökade databasen DTU-användningen på övre diagrammet. Det tar några minuter för den nya högre belastning kan förbättra, men du se snabbt databasen börjar nått maximal användning och som belastningen steadies till det nya mönstret, overloads snabbt databasen.

1. Om du vill skala upp i databasen, klickar du på **prisnivå (skala dtu: er)** i inställningsbladet.
1. Justera det **DTU** till **100**. 
1. Klicka på **tillämpa** att skicka begäran om att skala databasen.

Gå tillbaka till **tenants1** > **översikt** att visa övervakning diagram. Övervaka effekten av att tillhandahålla databasen med mer resurser (även om några klienter med en slumpmässig belastning inte är det alltid lätt att se säkerhet förrän du kör under en viss tid). När du tittar på rent diagram i åtanke att 100% representerar övre diagrammet nu 100 dtu: er på lägre diagrammet 100% fortfarande är 50 dtu: er.

Databaserna är online och fullt tillgängliga under hela processen. Programkod bör alltid skrivas till försök avbrutna anslutningar och så att återansluta till databasen.

## <a name="provision-a-new-tenant-in-its-own-database"></a>Etablera en ny klient i en egen databas 

Delat modell för flera klienter kan du välja om du vill etablera en ny klient i en databas för flera innehavare tillsammans med andra klienter eller att etablera klienten i en databas med sin egen. Genom att etablera en klient i en egen databas fördelar från isolering som är inbyggd i separata-databasen så att du kan hantera prestanda för den klienten oberoende av andra, återställa den klientorganisationen oberoende av andra, osv. Du kan till exempel välja att placera kostnadsfria eller vanliga kunder i en databas med flera innehavare och premium kunder i enskilda databaser.  Om isolerade stöd för en innehavare databaser skapas, kan de fortfarande hanteras gemensamt i en elastisk pool för att optimera resurskostnader.

Hoppa över följande steg om du redan har etablerats en ny klient i en egen databas.

1. I den **PowerShell ISE**öppnar... \\Learning moduler\\ProvisionTenants\\*Demo-ProvisionTenants.ps1*. 
1. Ändra **$TenantName = ”Salix Salsa”** och **$VenueType = ”webbsidor”**
1. Ange **$Scenario** = **2**, _etablera en klient i en ny databas för en klient_
1. Tryck **F5** för att köra skriptet.

Skriptet etablera den här klienten i en separat databas, registrera databasen och klienten med katalogen och öppnar du klientens händelser i webbläsaren. Uppdatera sidan händelser hubb och du ser ”Salix Salsa” har lagts till som en plats.

## <a name="manage-performance-of-a-single-database"></a>Hantera prestanda för en enskild databas

Om en enskild klient i en databas med flera innehavare får en varaktigt hög belastning, kan den ofta dominerar databasresurser och påverka andra klienter i samma databas. Om aktiviteten är sannolikt att fortsätta under en viss tid, kan klienten flyttas tillfälligt från databasen och i sin egen enskild klient-databas. Detta gör att klienten har extra resurser den behöver och fullständigt isoleras från andra klienter.

Den här övningen simulerar effekten av Salix Salsa upplever en hög belastning när biljetter går till försäljning för en populär händelse.

1. Öppna den... \\ *Demo-PerformanceMonitoringAndManagement.ps1* skript.
1. Ange **$DemoScenario = 5**, _Generera en normal belastning plus en hög belastning på en enskild klient (cirka 90 DTU)._
1. Ange **$SingleTenantName = Salix Salsa**
1. Kör skriptet med **F5**.

Gå till portalen och gå till **salixsalsa** > **översikt** att visa övervakning diagram. 

## <a name="other-performance-management-patterns"></a>Andra mönster för hantering av prestanda

**Klient självbetjäning skalning**

Eftersom skalning är en aktivitet enkelt kan anropas via management API, du enkelt skapa möjlighet att skala klient databaser i klient-riktade programmet och erbjuda som en funktion i SaaS-tjänsten. Exempelvis kan du låta klienter självhantera upp- och nedskalning, kanske kopplat direkt till sin fakturering!

**Skala upp och ner en-databas enligt ett schema så att den matchar användningsmönster**

Om sammanställd klientanvändning följer förutsägbar användningsmönster, kan du använda Azure Automation för att skala upp eller ned en-databas enligt ett schema. Exempelvis skala en databas efter 18: 00 och in igen innan 06: 00 på vardagar när du vet det finns en minskning av resursbehov.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Simulera användning av en databas som delat flera innehavare genom att köra ett angivet belastningen generator
> * Övervaka databasen som den svarar på ökade belastningen
> * Skala upp databasen som svar på databasbelastningen ökar
> * Etablera en klient till en enskild klient-databas

## <a name="additional-resources"></a>Ytterligare resurser

<!--* [Additional tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application deployment](saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
* [Azure Automation](../automation/automation-intro.md)