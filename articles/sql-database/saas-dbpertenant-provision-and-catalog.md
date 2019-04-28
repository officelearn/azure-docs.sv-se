---
title: Etablera nya klienter i en multitenant-app som använder Azure SQL Database | Microsoft Docs
description: Lär dig hur du etablera och katalogisera nya klienter i en Azure SQL Database med flera klienter SaaS-app
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
ms.date: 09/24/2018
ms.openlocfilehash: 803d05e1aaf4d9c26a6132bde30f101ce3905924
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61388367"
---
# <a name="learn-how-to-provision-new-tenants-and-register-them-in-the-catalog"></a>Lär dig hur du etablerar nya klienter och registrera dem i katalogen

I den här självstudien får du lära dig hur du etablera och katalogisera SaaS-mönstren. Du lär dig också hur de är implementerade i databas-per-klient-programmet Wingtip biljetter SaaS. Skapa och initiera nya klientdatabaserna och registrerar dem i programmets klientkatalog. Katalogen är en databas som upprätthåller mappningen mellan SaaS-programmets många klienter och deras data. Katalogen spelar en viktig roll i att dirigera program och av hanteringsbegäranden till rätt databas.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> 
> * Etablera en enskild ny klient.
> * Etablera en batch med ytterligare klienter.


Följande krav måste uppfyllas för att kunna köra den här självstudiekursen:

* Wingtip biljetter SaaS databas-per-klient appen har distribuerats. Om du vill distribuera den i mindre än fem minuter, [distribuera och utforska Wingtip biljetter SaaS databas-per-klient programmet](saas-dbpertenant-get-started-deploy.md).
* Azure PowerShell ska ha installerats. Mer information finns i [Kom igång med Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-saas-catalog-pattern"></a>Introduktion till SaaS-katalogmönstret

I en databasstödd multitenant SaaS-program är det viktigt att veta var informationen för varje klient lagras. I SaaS-katalogmönstret används en katalogdatabas att hålla reda på mappningen mellan varje klient och databasen där deras data lagras. Det här mönstret används när klientdata fördelas över flera databaser.

Varje klient har identifierats av en nyckel i katalogen, som mappas till platsen för sin databas. I appen Wingtip biljetter Skapad är nyckeln från en hash av klientens namn. Det här schemat tillåter appen att skapa nyckeln från klientnamnet ingår i programmets URL. Andra viktiga klient-scheman kan användas.  

Katalogen kan namn eller plats på databasen som ska ändras med minimal inverkan på programmet. I en multitenant databasmodell har den här funktionen också flytta en klient mellan databaser. Katalogen kan också användas för att indikera om en klient eller databasen är offline för underhåll och andra åtgärder. Den här funktionen är utforskat i den [återställning enskild klient självstudien](saas-dbpertenant-restore-single-tenant.md).

Katalogen också lagra ytterligare klient eller databasens metadata som schemaversion, service-plan och serviceavtal erbjuds till klienter. Katalogen kan lagra annan information som gör det möjligt för programhantering, kundsupport och DevOps. 

Katalogen kan aktivera Databasverktyg bortom SaaS-program. I exemplet Wingtip biljetter SaaS databas-per-klient i katalogen används för att aktivera fråga flera klienter som är utforskat i den [Ad hoc-rapportering självstudien](saas-tenancy-cross-tenant-reporting.md). Jobbhantering databasöverskridande utforskat i den [schemahantering](saas-tenancy-schema-management.md) och [Klientanalys](saas-tenancy-tenant-analytics.md) självstudier. 

I Wingtip biljetter SaaS-exempel katalogen implementeras med hjälp av Fragmenthanterings funktionerna i den [Elastic Database-klientbibliotek (EDCL)](sql-database-elastic-database-client-library.md). EDCL finns i Java och .NET Framework. EDCL kan ett program för att skapa, hantera och använda en databasstödd fragmentkartan. 

En fragmentkartan innehåller en lista över fragment (databaser) och mappningen mellan nycklar (klienter) och shards. EDCL funktioner används under klientorganisationens Etableringsläge för att skapa poster i fragmentkartan. De används vid körning av program för att ansluta till rätt databas. EDCL cachelagrar anslutningsinformationen för att minimera trafiken till katalogdatabasen och snabba upp programmet. 

> [!IMPORTANT]
> Mappningsdata är tillgängliga i katalogdatabasen, men *inte redigera den*. Redigera mappningsdata genom att endast använda Elastic Database klient biblioteket API: er. Direkt manipulering av mappningsdatan riskerar att skada katalogen och stöds inte.


## <a name="introduction-to-the-saas-provisioning-pattern"></a>Introduktion till mönster för SaaS-etablering

När du lägger till en ny klient i ett SaaS-program som använder en enda klient databasmodell måste du etablera en ny klientdatabas. Databasen måste skapas i rätt plats och tjänstnivå. Det måste också initieras med lämpligt schema och referensdata. Och det måste vara registrerat i katalogen under lämplig klientnyckeln. 

Du kan använda olika metoder för databasetablering. Du kan köra SQL-skript, distribuera en bacpac eller kopiera en mall-databas. 

Databasetableringen måste vara en del av din strategi för hantering av schemat. Du måste se till att nya databaser är etablerad med det senaste schemat. Det här kravet är utforskat i den [schemat självstudiekurs om enhetshantering](saas-tenancy-schema-management.md). 

Wingtip biljetter databas-per-klient-appen etablerar nya klienter genom att kopiera en mall-databas med namnet _basetenantdb_, som har distribuerats på katalogservern. Etablering kan integreras i programmet som en del av en inloggning. Den kan också användas offline med hjälp av skript. Den här guiden utforskar etablering med hjälp av PowerShell. 

Etablering skript kopiera den _basetenantdb_ databasen för att skapa en ny klientdatabas i en elastisk pool. Klientdatabasen har skapats i klient-server som mappats till den _newtenant_ DNS-alias. Det här aliaset underhåller en referens till den server som används för att etablera nya klienter och har uppdaterats för att peka på en klient återställningsservern i disaster recovery självstudier ([DR med georestore](saas-dbpertenant-dr-geo-restore.md), [DR med georeplikering](saas-dbpertenant-dr-geo-replication.md)). Skripten sedan initiera databasen med klientspecifik information och registrera den i katalogen fragmentkartan. Klient-databaserna finns angivna namn baserat på innehavarens namn. Den här namngivningsschemat är inte en viktig del av mönstret. Katalogen mappar klientnyckeln till namnet på databasen, så alla namngivningskonvention kan användas. 


## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Hämta Wingtip biljetter SaaS databas-per-klient programskript

Wingtip biljetter SaaS-skript och programmets källkod finns tillgängliga i den [WingtipTicketsSaaS DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub-lagringsplatsen. Kolla in den [allmänna riktlinjer](saas-tenancy-wingtip-app-guidance-tips.md) steg att ladda ned och avblockera Wingtip biljetter SaaS-skript.


## <a name="provision-and-catalog-detailed-walkthrough"></a>Etablera och katalogisera detaljerad genomgång

För att förstå hur Wingtip biljetter programmet implementerar etableringen av nya klienter, Lägg till en brytpunkt och följ arbetsflödet medan du etablera en klient.

1. I PowerShell ISE öppnar du... \\Inlärningsmoduler\\ProvisionAndCatalog\\_Demo-ProvisionAndCatalog.ps1_ och ange följande parametrar:

   * **$TenantName** = namnet på den nya platsen (till exempel *Bushwillow Blues*).
   * **$VenueType** = en av de fördefinierade platstyperna: _blues, classicalmusic, dance, jazz, judo, motor tävling, multipurpose, opera, rockmusic, soccer_.
   * **$DemoScenario** = **1**, *etablera en enda klient*.

2. Om du vill lägga till en brytpunkt, placera markören var som helst på raden *New-Tenant ”*. Tryck på F9.

   ![Brytpunkt](media/saas-dbpertenant-provision-and-catalog/breakpoint.png)

3. Tryck på F5 för att köra skriptet.

4. När körningen av skriptet stannar vid brytpunkten, trycker du F11 gå igenom koden.

   ![Felsökning](media/saas-dbpertenant-provision-and-catalog/debug.png)



Spåra de skriptkörningen med hjälp av den **felsöka** menyalternativ. Tryck på F10 och F11 för att gå över eller in i de anropade funktionerna. Mer information om hur du felsöker PowerShell-skript finns i [Tips om att arbeta med och felsöka PowerShell-skript](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise).


Du behöver inte uttryckligen följa det här arbetsflödet. Den förklarar hur du felsöka skript.

* **Importera modulen CatalogAndDatabaseManagement.psm1.** Den innehåller en katalog och abstraktion på klientnivå över den [Fragmenthanterings](sql-database-elastic-scale-shard-map-management.md) funktioner. Den här modulen innehåller en stor del av katalogmönstret och är värd att utforska.
* **Importera modulen SubscriptionManagement.psm1.** Den innehåller funktioner för att logga in på Azure och välja den Azure-prenumeration du vill arbeta med.
* **Hämta information om konfigurationen.** Stega in Get-konfiguration med hjälp av F11 och se hur appkonfigurationen specificeras. Resursnamn och andra appspecifika värden definieras här. Inte ändra dessa värden innan du bekantat dig med skript.
* **Hämta katalogobjektet.** Stega in Get-katalogen, som composes och returnerar ett katalogobjekt som används i skriptet på högre nivå. Den här funktionen används fragment hanteringsfunktioner som importeras från **AzureShardManagement.psm1**. Katalogobjektet består av följande element:

   * $catalogServerFullyQualifiedName konstrueras med standardstammen plus ditt användarnamn: _catalog -\<användaren\>. database.windows .net_.
   * $catalogDatabaseName hämtas från konfigurationen: *tenantcatalog*.
   * $shardMapManager-objektet initieras från katalogdatabasen.
   * $shardMap-objektet initieras från fragmentkartan _tenantcatalog_ i katalogdatabasen. Ett katalogobjekt består och returneras. Den används i skriptet på högre nivå.
* **Beräkna den nya klientnyckeln.** En hash-funktion används för att skapa klientnyckeln från klientnamnet.
* **Kontrollera om klientnyckeln redan finns.** Katalogen kontrolleras för att kontrollera att nyckeln är tillgänglig.
* **Klientdatabasen etableras med New-TenantDatabase.** Använda F11 gå igenom hur databasen etableras med hjälp av en [Azure Resource Manager-mall](../azure-resource-manager/resource-manager-template-walkthrough.md).

    Databasnamnet skapas från klientnamnet för att klargöra vilket fragment som tillhör vilken klient. Du kan också använda andra namngivningskonventioner för databasen. En Resource Manager-mallen skapar en klientdatabas genom att kopiera en mall för databas (_baseTenantDB_) på katalogservern. Alternativt kan du skapa en databas och initiera den genom att importera en bacpac. Eller du kan köra ett Initieringsskript för från en känd plats.

    Resource Manager-mallen finns i mappen ...\Learning Modules\Common\: *tenantdatabasecopytemplate.json*

* **Ytterligare har klientdatabasen initierats.** Lokal (klient)-namnet och platstypen har lagts till. Du kan också göra övrig initialisering här.

* **Klientdatabasen registreras i katalogen.** Den är registrerad med *Add-TenantDatabaseToCatalog* med hjälp av klientnyckeln. Använd F11 gå igenom informationen:

    * Katalogdatabasen läggs till i fragmentkartan (listan över kända databaser).
    * Mappningen som länkar nyckelvärdet till fragmentet skapas.
    * Ytterligare metadata om klienten (platsnamnet) läggs till i tabellen klienter i katalogen. Tabellen klienter är inte en del av Fragmenthanterings schemat och det är inte installerat på EDCL. Den här tabellen visar hur katalogdatabasen kan utökas för att stödja ytterligare programspecifik data.


När etableringen har slutförts, återgår körningen till ursprungligt *Demo-ProvisionAndCatalog* skript. Den **händelser** öppnas för den nya innehavaren i webbläsaren.

   ![Sidan för evenemang](media/saas-dbpertenant-provision-and-catalog/new-tenant.png)


## <a name="provision-a-batch-of-tenants"></a>Etablera en batch med klienter

Den här övningen etablerar en batch med 17 klienter. Vi rekommenderar att du etablerar den här batchen med klienter innan du startar andra självstudier för databas-per-klient Wingtip biljetter SaaS. Det finns mer än bara några få databaser du arbetar med.

1. I PowerShell ISE öppnar du... \\Inlärningsmoduler\\ProvisionAndCatalog\\*Demo-ProvisionAndCatalog.ps1*. Ändra den *$DemoScenario* parameter 3:

   * **$DemoScenario** = **3**, *etablera en batch med klienter*.
2. Tryck på F5 för att köra skriptet.

Skriptet etablerar en batch med ytterligare klienter. Den använder en [Azure Resource Manager-mall](../azure-resource-manager/resource-manager-template-walkthrough.md) som styr batchen och delegerar etableringen av varje databas till en länkad mall. Om du använder mallar på det här sättet, kan Azure Resource Manager mäkla etableringsprocessen för ditt skript. Mallar etablerar databaser i parallella och hantera återförsök, om det behövs. Skriptet är idempotenta, så om det misslyckas eller stoppas av någon anledning, kör den igen.

### <a name="verify-the-batch-of-tenants-that-successfully-deployed"></a>Verifiera att batchen med klienter som har distribuerats

* I den [Azure-portalen](https://portal.azure.com)bläddrar du till listan över servrar och öppna den *tenants1* server. Välj **SQL-databaser**, och verifiera att batchen 17 nya databaser finns i listan.

   ![Lista över databaser](media/saas-dbpertenant-provision-and-catalog/database-list.png)



## <a name="other-provisioning-patterns"></a>Andra etableringsmönster

Andra etableringsmönster som inte ingår i den här självstudien:

**Företablering av databaser**: Företablering mönstret utnyttjar faktumet att databaser i en elastisk pool inte lägga till extra kostnader. Faktureringen sker för den elastiska poolen, inte databaserna. Inaktiva databaser inte konsumerar några resurser. Du kan minska tiden för att lägga till klientorganisationer genom företablering av databaser i poolen och allokera dem efter behov. Antalet företablerade databaser kan justeras efter behov för att ha en buffert som är lämpliga för den förväntade etableringstakten.

**Automatisk etablering**: I mönstret för automatisk etablering etablerar en etableringstjänst servrar, pooler och databaser automatiskt efter behov. Om du vill kan inkludera du företablering av databaser i elastiska pooler. Om databaser inaktiveras och tas bort, kan luckorna i elastiska pooler fyllas av etableringstjänsten. En sådan tjänst kan vara enkla eller komplexa, till exempel hantera etablering över flera geografier och ställa in geo-replikering för haveriberedskap. 

Med mönstret för automatisk etablering skickar ett program eller skript du en etableringsbegäran till en kö för att behandlas av etableringstjänsten. Den frågar sedan tjänsten för att avgöra när det slutförts. Om företablering används, hanteras begäranden snabbt. Tjänsten etablerar en ersättningsdatabas i bakgrunden.


## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> 
> * Etablera en enskild ny klient.
> * Etablera en batch med ytterligare klienter.
> * Stega in etablerar klienter och registrerar dem i katalogen.

Prova den [guide för prestandaövervakning](saas-dbpertenant-performance-monitoring.md).

## <a name="additional-resources"></a>Ytterligare resurser

* Ytterligare [självstudier som bygger på databas-per-klient-programmet Wingtip biljetter SaaS](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Klientbibliotek för elastiska databaser](sql-database-elastic-database-client-library.md)
* [Felsök skript i Windows PowerShell ISE](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)
