---
title: Etablera nya klienter i en multitenant appar som använder Azure SQL Database | Microsoft Docs
description: Lär dig hur du etablera och katalogen nya klienter i en Azure SQL Database-multitenant SaaS-app
keywords: sql database tutorial
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: 4ddb870d0513d6834aacf0964c240260f18df0fd
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="learn-how-to-provision-new-tenants-and-register-them-in-the-catalog"></a>Lär dig att etablera nya klienter och registrerar dem i katalogen

I kursen får lära du att etablera och katalogen SaaS-mönster. Du lär dig också hur de implementeras i databasen per klient Wingtip biljetter SaaS-programmet. Skapa och initiera den nya innehavaren databaser och registrera dem i programkatalogen klient. Katalogen är en databas som underhåller mappningen mellan många hyresgäster i SaaS-program och data. Katalogen spelar en viktig roll i att dirigera programmet och av hanteringsbegäranden till rätt databas.

I den här guiden får du lära dig hur man:

> [!div class="checklist"]

> * Etablera en enda ny klient.
> * Etablera en batch med ytterligare klienter.


Följande krav måste uppfyllas för att kunna köra den här självstudiekursen:

* Wingtip biljetter SaaS databas per klient appen har distribuerats. Om du vill distribuera den på mindre än fem minuter, se [distribuera och utforska Wingtip biljetter SaaS databas per klient programmet](saas-dbpertenant-get-started-deploy.md).
* Azure PowerShell ska ha installerats. Mer information finns i [Kom igång med Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-saas-catalog-pattern"></a>Introduktion till mönster för SaaS-katalog

I ett databasen har säkerhetskopierats multitenant SaaS-program är det viktigt att veta var informationen för varje klient lagras. I mönster för SaaS-katalog är en katalog databas används för att lagra mappningen mellan varje klient och databasen där deras data lagras. Det här mönstret gäller när klientdata distribueras över flera databaser.

Varje klient identifieras med en nyckel i katalogen, som är mappad till platsen för databasen. I appen Wingtip biljetter format nyckeln från en hash av klientens namn. Det här schemat tillåter appen att skapa nyckeln från innehavarens namn som ingår i programmets URL. Andra viktiga klient-system kan användas.  

Katalogen kan namn eller plats på databasen som ska ändras med minimal påverkan på programmet. I en multitenant databasmodell den här funktionen kan även hantera flytta en klient mellan databaser. Katalogen kan också användas för att ange om en klient eller databasen är offline för underhåll eller andra åtgärder. Den här funktionen utforskade i den [Återställ enstaka klient kursen](saas-dbpertenant-restore-single-tenant.md).

Katalogen kan även lagra ytterligare klient eller databasens metadata som schemaversionen, service-plan eller serviceavtal erbjudas till klienterna. Katalogen kan lagra annan information som gör det möjligt för programhantering, kundsupport eller DevOps. 

Katalogen kan aktivera Databasverktyg utöver SaaS-program. I Wingtip biljetter SaaS databas per klient provet, katalogen används för att aktivera mellan klient-fråga som utforskade i den [Ad hoc-rapportering kursen](saas-tenancy-cross-tenant-reporting.md). Hantering av flera databaser utskriftsjobb utforskade i den [schemat management](saas-tenancy-schema-management.md) och [klient analytics](saas-tenancy-tenant-analytics.md) självstudier. 

I exemplen Wingtip biljetter SaaS katalogen implementeras med hjälp av Fragmentera hanteringsfunktionerna i den [klientbibliotek för elastisk databas (EDCL)](sql-database-elastic-database-client-library.md). EDCL finns i Java och .NET Framework. EDCL kan ett program för att skapa, hantera och använda en databasen har säkerhetskopierats Fragmentera karta. 

En Fragmentera-mappning innehåller en lista över delar (databaser) och mappningen mellan nycklar (klienter) och delar. EDCL funktioner används under etablering för att skapa poster i kartan Fragmentera klient. De används vid körning av program som ansluter till rätt databas. EDCL cachelagrar anslutningsinformationen för att minimera trafik till katalogdatabasen och snabba upp programmet. 

> [!IMPORTANT]
> Mappning datan är tillgänglig i katalogdatabasen men *inte redigera den*. Redigera mappningsdata med hjälp av elastiska databasen klienten biblioteket API: er endast. Direkt manipulering mappade data risker skadas katalogen och stöds inte.


## <a name="introduction-to-the-saas-provisioning-pattern"></a>Introduktion till mönstret för SaaS-etablering

När du lägger till en ny klient i ett SaaS-program som använder en enskild klient databasmodell måste du etablera en ny klient-databas. Databasen måste skapas på rätt plats och tjänstnivån. Den måste också initieras med lämpligt schema och referensdata. Och det måste vara registrerad i katalogen under lämpliga klientnyckeln. 

Du kan använda olika metoder för databasetablering av. Du kan köra SQL-skript, distribuera en bacpac eller kopiera en mall för databas. 

Databasetablering av måste vara en del av strategin för schemat. Du måste se till att nya databaser är etablerad med det senaste schemat. Det här kravet utforskade i den [schemat management kursen](saas-tenancy-schema-management.md). 

Appen Wingtip biljetter databas per klient etablerar nya klienter genom att kopiera en mall för databas med namnet _basetenantdb_, som distribueras på katalogservern. Etablering kan integreras i programmet som en del av en registrering. Den kan även användas offline med hjälp av skript. Den här självstudiekursen utforskar etablering med hjälp av PowerShell. 

Etablering skript kopiera den _basetenantdb_ databas för att skapa en ny klient-databas i en elastisk pool. Skripten sedan initiera databasen med klient-specifik information och registrera det i katalogen Fragmentera kartan. Klient-databaser är angivet namn baserat på innehavarens namn. Den här namngivningsschemat är en viktig del av mönstret. Katalogen mappar klientnyckeln till namnet på databasen, så en namngivningskonvention kan användas. 


## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Hämta programskript Wingtip biljetter SaaS databas per klient

Wingtip biljetter SaaS-skript och programmets källkod är tillgängliga i den [WingtipTicketsSaaS DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub-lagringsplatsen. Kolla in den [allmänna riktlinjer](saas-tenancy-wingtip-app-guidance-tips.md) steg för att ladda ned och avblockera Wingtip biljetter SaaS-skript.


## <a name="provision-and-catalog-detailed-walkthrough"></a>Etablera och katalogisera detaljerad genomgång

Att förstå hur programmet Wingtip biljetter implementerar ny klient etablering, lägga till en brytpunkt och följa arbetsflödet medan du etablera en klient.

1. I PowerShell ISE öppna... \\Learning moduler\\ProvisionAndCatalog\\_Demo-ProvisionAndCatalog.ps1_ och ange följande parametrar:

   * **$TenantName** = namnet på den nya platsen (till exempel *Bushwillow Blues*).
   * **$VenueType** = en av de fördefinierade plats typerna: _blått, classicalmusic, webbsidor, jazz, judo, motor tävling, multipurpose, opera, rockmusic, fotboll_.
   * **$DemoScenario** = **1**, *etablera en enskild klient*.

2. Om du vill lägga till en brytpunkt placera markören var som helst på raden *ny klient '*. Tryck på F9.

   ![Brytpunkt](media/saas-dbpertenant-provision-and-catalog/breakpoint.png)

3. Tryck på F5 för att köra skriptet.

4. När körningen av skriptet slutar vid brytpunkten, trycker du på F11 gå in i koden.

   ![Felsökning](media/saas-dbpertenant-provision-and-catalog/debug.png)



Spåra körning av skript med hjälp av den **felsöka** menyalternativen. Tryck på F10 och F11 steg via eller som kallas funktioner. Mer information om felsökning av PowerShell-skript finns [Tips om att arbeta med och felsöka PowerShell-skript](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise).


Du behöver inte uttryckligen följa det här arbetsflödet. Den förklarar hur felsöka skript.

* **Importera modulen CatalogAndDatabaseManagement.psm1.** Den ger en katalog och abstraction klient-nivå över den [Fragmentera Management](sql-database-elastic-scale-shard-map-management.md) funktioner. Den här modulen kapslar mycket av mönstret katalog och är värt att utforska.
* **Importera modulen SubscriptionManagement.psm1.** Den innehåller funktioner för att logga in på Azure och välja den Azure-prenumeration du vill arbeta med.
* **Hämta konfigurationsinformation.** Gå till Get-konfigurationen med hjälp av F11 och se hur programmets konfiguration har angetts. Resursnamn och andra app-specifik värden anges här. Inte ändra dessa värden tills du är bekant med skript.
* **Hämta katalogobjektet.** Gå till Get-katalog composes och returnerar ett objekt i katalogen som används i skriptet på högre nivå. Den här funktionen används Fragmentera hanteringsfunktioner som importeras från **AzureShardManagement.psm1**. Katalogobjektet består av följande element:

   * $catalogServerFullyQualifiedName har skapats med hjälp av standard stam plus ditt användarnamn: _katalog -\<användare\>. database.windows .net_.
   * $catalogDatabaseName hämtas från konfigurationen: *tenantcatalog*.
   * $shardMapManager-objektet initieras från katalogdatabasen.
   * $shardMap-objektet initieras från fragmentkartan _tenantcatalog_ i katalogdatabasen. Ett objekt i katalogen består och returneras. Den används i skriptet på högre nivå.
* **Beräkna den nya klientnyckeln.** En hash-funktion används för att skapa klientnyckeln från klientnamnet.
* **Kontrollera om det redan finns klientnyckeln.** Katalogen kontrolleras för att kontrollera att nyckeln är tillgänglig.
* **Klientdatabasen etableras med New-TenantDatabase.** Använd F11 och gå till hur databasen har etablerats med hjälp av en [Azure Resource Manager-mall](../azure-resource-manager/resource-manager-template-walkthrough.md).

    Databasnamnet skapas från klientnamnet för att klargöra vilket fragment som tillhör vilken klient. Du kan också använda andra namngivningskonventioner för databasen. En mall för hanteraren för filserverresurser skapar en klient-databas genom att kopiera en mall för databas (_baseTenantDB_) på katalogservern. Alternativt kan du skapa en databas och initiera genom att importera en bacpac. Eller så kan du köra ett Initieringsskript för från en känd plats.

    Resource Manager-mallen finns i mappen ...\Learning Modules\Common\: *tenantdatabasecopytemplate.json*

* **Ytterligare har klient-databasen initierats.** Namnet på platsen (klient) och vilken plats läggs till. Du kan också göra andra initieringen här.

* **Klient-databas har registrerats i katalogen.** Den är registrerad på *Lägg till TenantDatabaseToCatalog* med hjälp av klientnyckeln. Använd F11 för att gå till detaljer:

    * Katalogdatabasen läggs till i fragmentkartan (listan över kända databaser).
    * Mappningen som länkar nyckelvärdet till fragmentet skapas.
    * Ytterligare metadata om klienten (namn på den platsen) har lagts till i tabellen hyresgäster i katalogen. Tabellen klienter är inte en del av hantering av Fragmentera schemat och det är inte installerat på EDCL. I följande tabell visas hur katalogdatabasen kan utökas för att stödja ytterligare programspecifika data.


När etableringen är klar körning returnerar till ursprungligt *Demo-ProvisionAndCatalog* skript. Den **händelser** öppnas för den nya innehavaren i webbläsaren.

   ![Sidan händelser](media/saas-dbpertenant-provision-and-catalog/new-tenant.png)


## <a name="provision-a-batch-of-tenants"></a>Etablera en grupp med klienter

Den här övningen etablerar en batch med 17 innehavare. Vi rekommenderar att du etablera den här gruppen med klienter innan du startar andra kurser i Wingtip biljetter SaaS databas per klient. Det finns fler än några databaser du arbetar med.

1. I PowerShell ISE öppna... \\Learning moduler\\ProvisionAndCatalog\\*Demo-ProvisionAndCatalog.ps1*. Ändra den *$DemoScenario* parameter till 3:

   * **$DemoScenario** = **3**, *etablera en grupp med klienter*.
2. Tryck på F5 för att köra skriptet.

Skriptet etablerar en batch med ytterligare klienter. Den använder en [Azure Resource Manager-mall](../azure-resource-manager/resource-manager-template-walkthrough.md) som styr batchen och delegerar etablering av varje databas för en länkad mall. Om du använder mallar på det här sättet, kan Azure Resource Manager mäkla etableringsprocessen för ditt skript. Mallarna etablera databaser i parallellt och referensen omförsök, om det behövs. Skriptet är idempotent, så om den misslyckas eller avbryts av någon anledning, kör det igen.

### <a name="verify-the-batch-of-tenants-that-successfully-deployed"></a>Kontrollera i gruppen med klienter som har distribuerats

* I den [Azure-portalen](https://portal.azure.com)genom att bläddra i listan över servrar och öppna den *tenants1* server. Välj **SQL-databaser**, och kontrollera att gruppen 17 nya databaser finns i listan.

   ![Databaslistan](media/saas-dbpertenant-provision-and-catalog/database-list.png)



## <a name="other-provisioning-patterns"></a>Andra etableringsmönster

Andra etablering mönster som inte ingår i den här kursen:

**Före etablering databaser**: före etablering mönstret utnyttjar det faktum att databaser i en elastisk pool Lägg inte till extra kostnad. Faktureringen är för den elastiska poolen inte databaser. Inaktiv databaser förbrukar några resurser. Du kan minska tiden för att lägga till klienter före etablering databaser i poolen och tilldela dem när de behövs. Antalet databaser som etableras i förväg kan justeras efter behov för att hålla en buffert lämpar sig för den förväntade etablering hastigheten.

**Automatisk etablering**: I mönstret Automatisk etablering en allokering tjänst etablerar servrar, pooler och databaserna automatiskt efter behov. Om du vill kan inkludera du före etablering databaser i elastiska pooler. Om databaser inaktiveras och tas bort, kan du fylla luckor i elastiska pooler av tjänsten etablering. Dessa tjänster kan vara enkla eller avancerade, till exempel hantering etablering över flera geografiska områden och ställa in geo-replikering för katastrofåterställning. 

Skickar en allokering begäran till en kö som ska bearbetas av tjänsten etablering med Automatisk etablering-mönster ett client-program eller skript. Den genomsöker sedan tjänsten för att fastställa slutförande. Om före etablering används hanteras begäranden snabbt. Tjänsten etablerar en ersättning databas i bakgrunden.


## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]

> * Etablera en enda ny klient.
> * Etablera en batch med ytterligare klienter.
> * Gå till information om etablering klienter och registrera dem till katalogen.

Försök i [prestanda övervakning kursen](saas-dbpertenant-performance-monitoring.md).

## <a name="additional-resources"></a>Ytterligare resurser

* Ytterligare [självstudier som bygger på det databas per klient Wingtip biljetter SaaS-programmet](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Klientbibliotek för elastiska databaser](sql-database-elastic-database-client-library.md)
* [Felsöka skript i Windows PowerShell ISE](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)
