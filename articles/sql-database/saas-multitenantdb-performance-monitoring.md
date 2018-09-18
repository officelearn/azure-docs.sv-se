---
title: Övervaka prestanda för en shardade Azure SQL-databas för flera klienter i en SaaS-app för flera klienter | Microsoft Docs
description: Övervaka och hantera prestanda för shardade flera innehavare Azure SQL-databas i en SaaS-app för flera innehavare
keywords: sql database tutorial
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: sstein
ms.openlocfilehash: d04fd4202a0544a8549f1729dbba9a829482c2f0
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2018
ms.locfileid: "45730689"
---
# <a name="monitor-and-manage-performance-of-sharded-multi-tenant-azure-sql-database-in-a-multi-tenant-saas-app"></a>Övervaka och hantera prestanda för shardade flera innehavare Azure SQL-databas i en SaaS-app för flera innehavare

I den här självstudien beskrivs flera prestandarelaterade hanteringsscenarier som används i SaaS-program. Använder en belastningsgenerator för att simulera aktivitet på shardade databaser för flera innehavare, är inbyggd övervakning och aviseringsfunktionerna i SQL-databas visas.

Databas för flera klienter i Wingtip biljetter SaaS-appen använder en modell för shardade data för flera innehavare, där distribueras lokal (klient) data genom att klient-ID på potentiellt flera databaser. Precis som för flera SaaS-program så är de förväntade belastningsmönstren för klienterna oberäkneliga och sporadiska. Biljettförsäljningar kan med andra ord ske när som helst. Om du vill dra nytta av den här typiska databasanvändningsmönstret, kan databaser skalas upp och ned för att optimera kostnaderna för en lösning. Med den här typen av mönster är det viktigt att övervaka databasresursanvändning för att säkerställa att belastningar belastningarna balanseras över potentiellt flera databaser. Du måste också se till att enskilda databaser har tillräckliga resurser och inte träffar sina [DTU](sql-database-service-tiers.md#what-are-database-transaction-units-dtus) gränser. Den här guiden går igenom sätt att övervaka och hantera databaser och hur du vidta åtgärder som svar på belastningsvariationer.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]

> * Simulera användning på en fragmenterade (sharded) databas för flera innehavare genom att köra en angiven belastningsgenerator
> * Övervaka databasen eftersom den svarar på belastningsökningar
> * Skala upp databasen som svar på den ökade databasbelastningen
> * Etablera en klient till en enda klient-databas

Följande krav måste uppfyllas för att kunna köra den här självstudiekursen:

* Databas för flera klienter i Wingtip biljetter SaaS-appen har distribuerats. Om du vill distribuera i mindre än fem minuter [distribuera och utforska databas för flera klienter i Wingtip biljetter SaaS-program](saas-multitenantdb-get-started-deploy.md)
* Azure PowerShell ska ha installerats. Mer information finns i [Komma igång med Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-saas-performance-management-patterns"></a>Introduktion till SaaS-prestandahanteringsmönster

Hantering av databasprestanda innebär kompilering och analys av prestandadata för att sedan reagera på den genom att justera parametrarna för att bibehålla en acceptabel svarstid för programmet. 

### <a name="performance-management-strategies"></a>Strategier för prestandahantering

* Om du vill undvika att behöva manuellt övervaka prestanda, är det bäst att **ställa in aviseringar som utlöser när databaser som avviker från normala intervall**.
* Svara på kortsiktiga variationer i beräkningsstorleken för en databas i **DTU-nivå skalas upp eller ned**. Om den här variationerna inträffar på regelbunden eller förutsägbar basis **skala databasen kan schemaläggas att ske automatiskt**. Du kan exempelvis skala ned när du vet att din arbetsbelastning är lätt, på nätter eller helger till exempel.
* Svara till mer långsiktiga variationer eller förändringar innehavare, **enskilda klienter kan flyttas till andra databasen**.
* Svara på kortsiktiga ökningar *enskilda* klient belastning, **enskilda klienter kan tas bort från en databas och tilldelas en enskild beräkningsstorleken**. När belastningen minskar, kan klienten sedan returneras till databas för flera innehavare. När det här är känt i förväg, kan klienter flyttas förebyggande syfte att säkerställa att databasen alltid har de resurser den behöver och för att undvika påverkan på andra klienter i databas för flera innehavare. Om det här kravet är förutsägbart, till exempel en plats där ett populärt evenemang skapar en rusning efter biljetter, kan det här hanteringsbeteendet integreras i programmet.

[Azure-portalen](https://portal.azure.com) tillhandahåller inbyggd övervakning och avisering för de flesta resurser. För SQL-databas är övervakning och avisering tillgängligt för databaser. Den här inbyggda övervakningen och aviseringen är resursspecifik, så det är praktiskt för små antal resurser, men inte är praktiskt när du arbetar med många resurser.

För scenarion med stora volymer, där du arbetar med många resurser, [Log Analytics](https://azure.microsoft.com/services/log-analytics/) kan användas. Det här är en separat Azure-tjänst som tillhandahåller analys för utgivna diagnostikloggar och telemetri som samlats in i en log analytics-arbetsyta. Log Analytics kan samla in telemetri från flera olika tjänster och användas för att fråga och ställa in aviseringar.

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Hämta källkoden för databas för flera klienter i Wingtip biljetter SaaS-program och skript

Databas för flera klienter i Wingtip biljetter SaaS-skript och programmets källkod finns tillgängliga i den [WingtipTicketsSaaS MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) GitHub-lagringsplatsen. Kolla in den [allmänna riktlinjer](saas-tenancy-wingtip-app-guidance-tips.md) steg att ladda ned och avblockera Wingtip biljetter SaaS-skript.

## <a name="provision-additional-tenants"></a>Etablera ytterligare klienter

För en god förståelse för hur prestandaövervakning och hantering fungerar i stor skala måste den här självstudien du har flera klienter i en fragmenterade (sharded) databas för flera innehavare.

Om du redan har etablerat en batch med klienter i en tidigare självstudiekurs, gå vidare till den [simulera användning på alla klientdatabaser](#simulate-usage-on-all-tenant-databases) avsnittet.

1. I den **PowerShell ISE**öppnar... \\Inlärningsmoduler\\prestandaövervakning och hantering\\*Demo-PerformanceMonitoringAndManagement.ps1*. Ha det här skriptet öppet medan du kör scenarierna i den här guiden.
1. Ställ in **$DemoScenario** = **1**, _etablera en batch med klienter_
1. Tryck **F5** för att köra skriptet.

Skriptet distribuerar 17 klienter till databas för flera klienter i ett par minuter. 

Den *New-TenantBatch* skriptet skapar nya klienter med unika klient nycklarna i den fragmenterade (sharded) databasen för flera innehavare och initieras de med klienttyp och platstyp. Det här är likadant som hur appen etablerar en ny klient. 

## <a name="simulate-usage-on-all-tenant-databases"></a>Simulera användning på alla klientdatabaser

Den *Demo-PerformanceMonitoringAndManagement.ps1* skript har angetts som simulerar en arbetsbelastning som körs mot databas för flera innehavare. Belastningen skapas med någon av de tillgängliga load-scenarierna:

| Demo | Scenario |
|:--|:--|
| 2 | Generera av normal intensitet (cirka 30 DTU) |
| 3 | Generera belastning med längre toppar per klient|
| 4 | Generera belastningar med högre DTU-toppar per klient (cirka 70 DTU)|
| 5 | Generera en hög intensitet (cirka 90 DTU) på en enda klient plus en normal intensitet belastningen på alla klienter |

Belastningsgeneratorn tillämpar en *syntetisk* enbart-CPU-belastning på varje klientdatabas. Generatorn startar ett jobb för varje klientdatabas som anropar en lagrad procedur med jämna mellanrum, vilket genererar belastningen. Den belastningsnivåer (i dtu: er), varaktighet och intervaller varierar över alla databaser, vilket simulerar oförutsägbar klientaktivitet.

1. I den **PowerShell ISE**öppnar... \\Inlärningsmoduler\\prestandaövervakning och hantering\\*Demo-PerformanceMonitoringAndManagement.ps1*. Ha det här skriptet öppet medan du kör scenarierna i den här guiden.
1. Ange **$DemoScenario** = **2**, _generera av normal intensitet_
1. Tryck på **F5** att tillämpa en belastning på alla klienter.

Databas för flera klienter i Wingtip biljetter SaaS är en SaaS-app och verkliga belastningen på en SaaS-app är vanligtvis sporadisk och oförutsägbar. För att simulera det så skapar belastningsgeneratorn en slumpmässig belastning som distribueras över alla klienterna. Flera minuter behövs för mönster för belastningsutjämning till dyker upp, så kör belastningsgeneratorn för 3 – 5 minuter innan du försöker övervaka belastningen i följande avsnitt.

> [!IMPORTANT]
> Belastningsgeneratorn körs som en serie jobb i ett nytt PowerShell-fönster. Om du stänger sessionen, stannar belastningsgeneratorn. Belastningsgeneratorn finns kvar i en *jobbet anropar* tillstånd där det genererar belastningen på alla nya klienter som har etablerats efter generatorn har startats. Använd *Ctrl-C* att stoppa anropar nya jobb och avsluta skriptet. Belastningsgeneratorn fortsätter att köras, men endast på befintliga klienter.

## <a name="monitor-resource-usage-using-the-azure-portal"></a>Övervaka Resursanvändning med Azure-portalen

För att övervaka Resursanvändning som resulterar från den tillämpade belastningen, öppnar du portalen till databas för flera innehavare **tenants1**, som innehåller innehavarna som:

1. Öppna den [Azure-portalen](https://portal.azure.com) och bläddra till servern *tenants1-mt -&lt;användaren&gt;*.
1. Rulla nedåt och leta upp databaser och klickar på **tenants1**. Den här fragmenterade (sharded) databas för flera innehavare innehåller alla klienter som skapats än så länge.

![databas-diagram](./media/saas-multitenantdb-performance-monitoring/multitenantdb.png)

Notera den **DTU** diagram.

## <a name="set-performance-alerts-on-the-database"></a>Ställ in prestandaaviseringar för databasen

Ställa in en avisering på databasen som utlöses vid \>75% utnyttjande enligt följande:

1. Öppna den *tenants1* databasen (på den *tenants1-mt -&lt;användaren&gt;*  server) i den [Azure-portalen](https://portal.azure.com).
1. Klicka på **aviseringsregler** och därefter på **+ lägg till avisering**:

   ![lägg till avisering](media/saas-multitenantdb-performance-monitoring/add-alert.png)

1. Ange ett namn, exempelvis **hög DTU**,
1. Ställ in följande värden:
   * **Mått = DTU-procent**
   * **Villkor = större än**
   * **Tröskelvärdet = 75**.
   * **Period = de senaste 30 minuterna**
1. Lägg till en e-postadress till den *administratören email(s)* och klickar på **OK**.

   ![ange varning](media/saas-multitenantdb-performance-monitoring/set-alert.png)

## <a name="scale-up-a-busy-database"></a>Skala upp en upptagen databas

Om belastningsnivån ökar på en databas till den punkt som maximerar databasen och når 100% DTU-användning, sedan påverkas databasens prestanda, potentiellt saktas frågesvarstiderna.

**Kortsiktigt**, Överväg att skala upp databasen för att tillhandahålla ytterligare resurser eller ta bort klienter från databas för flera innehavare (flytta dem utanför databas för flera innehavare till en fristående databas).

**Lång sikt**, Överväg att optimera frågorna eller indexanvändningen för att förbättra databasprestanda. Beroende på programmets känslighet för prestandaproblem utfärdar det praxis att skala en databas upp innan den når 100% DTU-användningen. Använd en avisering för att varna dig i förväg.

Du kan simulera en upptagen databas genom att öka belastningen som generatorn skapar. Gör innehavarna som ska tillhandahålla oftare och för längre, ökar belastningen på databas för flera klienter utan att ändra kraven för enskilda klienter. Skala upp databasen görs enkelt i portalen eller PowerShell. Den här övningen använder portalen.

1. Ange *$DemoScenario* = **3**, _generera belastning med längre och mer frekventa toppar per databas_ att öka intensiteten av den sammanställda belastningen på den databas utan att ändra toppbelastningen som krävs av varje klient.
1. Tryck på **F5** för att tillämpa en belastning på alla dina klientdatabaser.
1. Gå till den **tenants1** databas i Azure-portalen.

Övervaka den öka databasen DTU-användningen i det övre diagrammet. Det tar några minuter innan den nya högre belastningen sätter in, men du bör se databasen börjar når maximal användning snabbt och som belastningen steadies till det nya mönstret, overloads snabbt databasen.

1. Om du vill skala upp databasen, klickar du på **prisnivå (skalnings-dtu: er)** på inställningsbladet.
1. Justera den **DTU** att ställa in **100**. 
1. Klicka på **tillämpa** att skicka förfrågan om att skala databasen.

Gå tillbaka till **tenants1** > **översikt** att visa Övervakningsdiagrammen. Övervaka effekten av att tillhandahålla databasen med mer resurser (men med några klienter och en slumpmässig belastning inte är det alltid lätt att se definitivt tills du kör under en viss tid). När du tittar på diagrammen, Tänk Tänk på att 100% representerar det övre diagrammet nu 100 dtu: er i det lägre diagrammet 100% fortfarande är 50 dtu: er.

Databaserna är online och fullt tillgängliga under hela processen. Programkod ska alltid skriva att återförsöka brutna anslutningar, och då kommer att återansluta till databasen.

## <a name="provision-a-new-tenant-in-its-own-database"></a>Etablera en ny klient i en egen databas 

Shardade modell för flera klienter kan du välja om du vill etablera en ny klient i en databas för flera innehavare tillsammans med andra klienter eller att etablera klient i en databas med sin egen. Genom att etablera en klient i en egen databas, dra nytta av isolering i separata databasen, så att du kan hantera prestanda för den klienten oberoende av andra, återställa klienten oberoende av andra, osv. Du kanske exempelvis vill placera kostnadsfri utvärderingsversion eller vanliga kunder i en databas för flera innehavare och premium-kunder i enskilda databaser.  Om isolerade databaser som enda klient skapas, kan de fortfarande hanteras gemensamt i en elastisk pool för att optimera resurskostnader.

Hoppa över följande steg om du redan har etablerat en ny klient i en egen databas.

1. I den **PowerShell ISE**öppnar... \\Inlärningsmoduler\\ProvisionTenants\\*Demo-ProvisionTenants.ps1*. 
1. Ändra **$TenantName = ”Salix Salsa”** och **$VenueType = ”kontroll”**
1. Ange **$Scenario** = **2**, _etablera en klient i en ny databas för enstaka klientorganisationer_
1. Tryck **F5** för att köra skriptet.

Skriptet etablerar den här klienten i en separat databas, registrera databasen och klienten i katalogen och öppnar du klientens händelser i webbläsaren. Uppdatera Evenemangshubben sidan och du ser ”Salix Salsa” har lagts till som en plats.

## <a name="manage-performance-of-a-single-database"></a>Hantera prestanda för en enkel databas

Om en enda klient i en databas för flera klienter får en varaktigt hög belastning, kan det brukar dominera databasresurserna och påverka andra klienter i samma databas. Om aktiviteten är sannolikt kommer att fortsätta under en viss tid, kan klienten flyttas tillfälligt från databasen och i en egen databas för enstaka klientorganisationer. Detta gör att klienten har extra resurser den behöver och fullständigt isolerar den från andra klienter.

Den här övningen simulerar effekten av Salix Salsa upplever en hög belastning när biljetter för ett populärt evenemang.

1. Öppna den... \\ *Demo-PerformanceMonitoringAndManagement.ps1* skript.
1. Ange **$DemoScenario = 5**, _Generera en normal belastning plus en hög belastning på en enskild klient (cirka 90 DTU)._
1. Ange **$SingleTenantName = Salix Salsa**
1. Kör skriptet med **F5**.

Gå till portalen och gå till **salixsalsa** > **översikt** att visa Övervakningsdiagrammen. 

## <a name="other-performance-management-patterns"></a>Övriga prestandahanteringsmönster

**Klient självbetjäning skalning**

Eftersom skalning är en aktivitet enkelt kan anropas via hanterings-API: et, du enkelt bygga in möjligheten att skala klientdatabaser i ditt klientriktade program och erbjuda de som en funktion i SaaS-tjänsten. Exempelvis kan du låta klienter självhantera upp- och nedskalning, kanske kopplat direkt till sin fakturering!

**Skala upp eller ned en databas på ett schema för att matcha användningsmönster**

Där sammanställd klientanvändning följer förutsägbara mönster, kan du använda Azure Automation för att skala upp eller ned en databas på ett schema. Till exempel Nedskalning en databas efter 18.00 och upp igen innan 06.00 på veckodagar som när du vet det finns en minskning av resurskrav.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Simulera användning på en fragmenterade (sharded) databas för flera innehavare genom att köra en angiven belastningsgenerator
> * Övervaka databasen eftersom den svarar på belastningsökningar
> * Skala upp databasen som svar på den ökade databasbelastningen
> * Etablera en klient till en enda klient-databas

## <a name="additional-resources"></a>Ytterligare resurser

<!--* [Additional tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application deployment](saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
* [Azure Automation](../automation/automation-intro.md)