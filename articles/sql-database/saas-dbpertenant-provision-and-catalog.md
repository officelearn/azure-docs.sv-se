---
title: Etablera nya klienter i en app med flera innehavare
description: Lär dig hur du etablerar och katalogiserar nya klienter i en Azure SQL Database-multitenant SaaS-app
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/24/2018
ms.openlocfilehash: 6ec8f8835e925663fc6ac21a6eb1df09d6927109
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74132101"
---
# <a name="learn-how-to-provision-new-tenants-and-register-them-in-the-catalog"></a>Lär dig hur du etablerar nya klienter och registrerar dem i katalogen

I den här självstudien får du lära dig hur du etablerar och katalogiserar SaaS-mönster. Du lär dig också hur de implementeras i Wingtip Tickets SaaS-databas-per-klient-programmet. Du skapar och initierar nya klientdatabaser och registrerar dem i programmets klientkatalog. Katalogen är en databas som upprätthåller mappningen mellan SaaS-programmets många klienter och deras data. Katalogen spelar en viktig roll i att styra program- och hanteringsbegäranden till rätt databas.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
>
> * Etablera en enda ny klient.
> * Etablera en bunt ytterligare klienter.


Följande krav måste uppfyllas för att kunna köra den här självstudiekursen:

* Wingtip-biljetterna SaaS-appen för klient-per-klienter distribueras. Information om hur du distribuerar den på mindre än fem minuter finns i [Distribuera och utforska Programmet Wingtip Tickets SaaS database-per-tenant](saas-dbpertenant-get-started-deploy.md).
* Azure PowerShell ska ha installerats. Mer information finns i [Kom igång med Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-saas-catalog-pattern"></a>Introduktion till SaaS-katalogmönstret

I ett databasstödd SaaS-program med flera egenskaper är det viktigt att veta var information för varje klient lagras. I SaaS-katalogmönstret används en katalogdatabas för att lagra mappningen mellan varje klient och databasen där deras data lagras. Det här mönstret gäller när klientdata distribueras över flera databaser.

Varje klient identifieras av en nyckel i katalogen, som mappas till platsen för deras databas. I appen Wingtip Tickets bildas nyckeln från en hash av hyresgästens namn. Med det här schemat kan appen skapa nyckeln från klientnamnet som ingår i program-URL:en. Andra klientnyckelscheman kan användas.

Katalogen gör att namnet eller platsen för databasen kan ändras med minimal påverkan på programmet. I en databasmodell med flera innehavare kan den här funktionen även flytta en klient mellan databaser. Katalogen kan också användas för att ange om en klient eller databas är offline för underhåll eller andra åtgärder. Den här funktionen utforskas i [självstudien Återställ en enda klient](saas-dbpertenant-restore-single-tenant.md).

Katalogen kan också lagra ytterligare klient- eller databasmetadata, till exempel schemaversion, serviceplan eller SLA som erbjuds till klienter. Katalogen kan lagra annan information som möjliggör programhantering, kundsupport eller DevOps.

Utöver SaaS-programmet kan katalogen aktivera databasverktyg. I exemplet med Wingtip Tickets SaaS-databas per klient används katalogen för att aktivera fråga över klientgränser, som utforskas i [ad hoc-rapporteringshandledningen](saas-tenancy-cross-tenant-reporting.md). Jobbhantering över flera databaser utforskas i självstudierna [schemahantering](saas-tenancy-schema-management.md) [och klientanalys.](saas-tenancy-tenant-analytics.md)

I Wingtip Tickets SaaS-exemplen implementeras katalogen med hjälp av Shard Management-funktionerna i [EDCL (Elastic Database client library).](sql-database-elastic-database-client-library.md) EDCL är tillgängligt i Java och .NET Framework. EDCL gör det möjligt för ett program att skapa, hantera och använda en databasstödd fragmentkarta.

En fragmentkarta innehåller en lista över shards (databaser) och mappningen mellan nycklar (klienter) och shards. EDCL-funktioner används under klientetablering för att skapa transaktionerna i fragmentkartan. De används vid körning av program för att ansluta till rätt databas. EDCL cachelagrar anslutningsinformation för att minimera trafiken till katalogdatabasen och påskynda programmet.

> [!IMPORTANT]
> Mappningsdata är tillgängliga i katalogdatabasen, men *redigera den inte*. Redigera mappningsdata med endast API:er för elastiskt databasklientbibliotek. Direkt manipulera mappningsdata riskerar att skada katalogen och stöds inte.


## <a name="introduction-to-the-saas-provisioning-pattern"></a>Introduktion till SaaS-etableringsmönstret

När du lägger till en ny klient i ett SaaS-program som använder en databasmodell med en enda klient måste du etablera en ny klientdatabas. Databasen måste skapas på lämplig plats- och tjänstnivå. Den måste också initieras med lämpliga schema- och referensdata. Och den måste registreras i katalogen under lämplig klientnyckel.

Olika metoder för databasetablering kan användas. Du kan köra SQL-skript, distribuera en bacpac eller kopiera en malldatabas.

Databasetablering måste vara en del av din schemahanteringsstrategi. Du måste se till att nya databaser etableras med det senaste schemat. Det här kravet utforskas i [självstudiekursen schemahantering](saas-tenancy-schema-management.md).

Wingtip-biljetter-databasen per klient-app etablerar nya klienter genom att kopiera en malldatabas med namnet _basetenantdb_, som distribueras på katalogservern. Etablering kan integreras i programmet som en del av en registreringsupplevelse. Det kan också stödjas offline med hjälp av skript. Den här självstudien utforskar etablering med hjälp av PowerShell.

Etableringsskript kopierar _basetenantdb-databasen_ för att skapa en ny klientdatabas i en elastisk pool. Klientdatabasen skapas i klientservern som mappas till det _nya DNS-aliaset._ Det här aliaset behåller en referens till den server som används för att etablera nya klienter och uppdateras för att peka på en klientserver för återställning av återställning i självstudierna för haveriberedskap[(DR med georestore](saas-dbpertenant-dr-geo-restore.md), [DR med georeplication](saas-dbpertenant-dr-geo-replication.md)). Skripten initierar sedan databasen med klientspecifik information och registrerar den i katalogens fragmentkarta. Klientdatabaser får namn baserat på klientnamnet. Det här namngivningsschemat är inte en viktig del av mönstret. Katalogen mappar klientnyckeln till databasnamnet så att alla namngivningskonventioner kan användas.


## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Hämta programskripten För Wingtip Tickets SaaS-databas per klient

Wingtip Biljetter SaaS skript och ansökan källkod finns i [WingtipTicketsSAaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub repo. Kolla in den [allmänna vägledningen](saas-tenancy-wingtip-app-guidance-tips.md) för steg för att ladda ner och låsa upp Wingtip Tickets SaaS skript.


## <a name="provision-and-catalog-detailed-walkthrough"></a>Etablera och katalogisera detaljerad genomgång

Om du vill förstå hur programmet Wingtip Tickets implementerar ny klientetablering lägger du till en brytpunkt och följer arbetsflödet medan du etablerar en klient.

1. I PowerShell ISE, öppna ... \\Utbildningsmoduler\\ProvisionAndCatalog\\_Demo-ProvisionAndCatalog.ps1_ och ange följande parametrar:

   * **$TenantName** = namnet på den nya platsen (till exempel *Bushwillow Blues*).
   * **$VenueType** = en av de fördefinierade plats typer: _blues, klassiskmusik, dans, jazz, judo, motorsport, mångsidig, opera, rockmusic, fotboll_.
   * **$DemoScenario****1**, *Etablera en enda klient*. = 

2. Om du vill lägga till en brytpunkt placerar du markören var som helst på raden där det står *Ny klient .* Tryck sedan på F9.

   ![Brytpunkt](media/saas-dbpertenant-provision-and-catalog/breakpoint.png)

3. Om du vill köra skriptet trycker du på F5.

4. När skriptkörningen stoppas vid brytpunkten trycker du på F11 för att gå in i koden.

   ![Felsökning](media/saas-dbpertenant-provision-and-catalog/debug.png)



Spåra skriptets körning med hjälp av **menyalternativen Debug.** Tryck på F10 och F11 för att gå över eller in i de så kallade funktionerna. Mer information om felsökning av PowerShell-skript finns i [Tips om hur du arbetar med och felsöker PowerShell-skript](https://docs.microsoft.com/powershell/scripting/components/ise/how-to-debug-scripts-in-windows-powershell-ise).


Du behöver inte uttryckligen följa det här arbetsflödet. Det förklarar hur man felsöker skriptet.

* **Importera modulen CatalogAndDatabaseManagement.psm1.** Den innehåller en katalog och abstraktion på klientnivå över [funktionerna Shard Management.](sql-database-elastic-scale-shard-map-management.md) Denna modul sammanfattar mycket av katalogen mönster och är värt att utforska.
* **Importera modulen SubscriptionManagement.psm1.** Den innehåller funktioner för att logga in på Azure och välja den Azure-prenumeration som du vill arbeta med.
* **Hämta konfigurationsinformation.** Gå in i Get-Configuration med F11 och se hur appen config anges. Resursnamn och andra appspecifika värden definieras här. Ändra inte dessa värden förrän du är bekant med skripten.
* **Hämta katalogobjektet.** Gå in i Get-Catalog, som komponerar och returnerar ett katalogobjekt som används i skriptet på högre nivå. Den här funktionen använder Shard Management-funktioner som importeras från **AzureShardManagement.psm1**. Katalogobjektet består av följande element:

   * $catalogServerFullyQualifiedName konstrueras med hjälp av standardstammen plus ditt användarnamn: _katalog-användare\<\>.database.windows .net_.
   * $catalogDatabaseName hämtas från konfigurationen: *tenantcatalog*.
   * $shardMapManager-objektet initieras från katalogdatabasen.
   * $shardMap-objektet initieras från fragmentkartan _tenantcatalog_ i katalogdatabasen. Ett katalogobjekt består och returneras. Det används i skriptet på högre nivå.
* **Beräkna den nya klientnyckeln.** En hash-funktion används för att skapa klientnyckeln från klientnamnet.
* **Kontrollera om klientnyckeln redan finns.** Katalogen är markerad för att kontrollera att nyckeln är tillgänglig.
* **Klientdatabasen etableras med New-TenantDatabase.** Använd F11 för att gå in på hur databasen etableras med hjälp av en [Azure Resource Manager-mall](../azure-resource-manager/resource-manager-template-walkthrough.md).

    Databasnamnet skapas från klientnamnet för att klargöra vilket fragment som tillhör vilken klient. Du kan också använda andra namngivningskonventioner för databaser. En Resource Manager-mall skapar en klientdatabas genom att kopiera en malldatabas (_baseTenantDB_) på katalogservern. Som ett alternativ kan du skapa en databas och initiera den genom att importera en bacpac. Du kan också köra ett initieringsskript från en välkänd plats.

    Mallen Resurshanteraren finns i mappen ...\Learning Modules\Common\: *tenantdatabasecopytemplate.json*

* **Klientdatabasen initieras ytterligare.** Lokalens (hyresgästens) namn och platstypen läggs till. Du kan också göra andra initiering här.

* **Klientdatabasen registreras i katalogen.** Den är registrerad hos *Add-TenantDatabaseToCatalog* med hjälp av klientnyckeln. Använd F11 för att stega in i detaljerna:

    * Katalogdatabasen läggs till i fragmentkartan (listan över kända databaser).
    * Mappningen som länkar nyckelvärdet till fragmentet skapas.
    * Ytterligare metadata om klienten (platsens namn) läggs till i tabellen Klienter i katalogen. Tabellen Klienter är inte en del av schemat Shard Management och installeras inte av EDCL. Den här tabellen visar hur katalogdatabasen kan utökas för att stödja ytterligare programspecifika data.


När etableringen har slutförts återgår körningen till det ursprungliga *demoetableringochcatalog-skriptet.* Sidan **Händelser** öppnas för den nya klienten i webbläsaren.

   ![Sidan Händelser](media/saas-dbpertenant-provision-and-catalog/new-tenant.png)


## <a name="provision-a-batch-of-tenants"></a>Etablera en batch med klienter

Denna övning avsättningar ett parti med 17 hyresgäster. Vi rekommenderar att du etablerar den här batchen med klienter innan du startar andra Wingtip Tickets SaaS-databas-per-klient-självstudier. Det finns mer än bara några databaser att arbeta med.

1. I PowerShell ISE, öppna ... \\Utbildningsmoduler\\ProvisionAndCatalog\\*Demo-ProvisionAndCatalog.ps1*. Ändra *parametern $DemoScenario* till 3:

   * **$DemoScenario****3** *, Etablera en batch med klienter*. = 
2. Om du vill köra skriptet trycker du på F5.

Skriptet etablerar en batch med ytterligare klienter. Den använder en [Azure Resource Manager-mall](../azure-resource-manager/resource-manager-template-walkthrough.md) som styr batch- och ombudsetablering av varje databas till en länkad mall. Om du använder mallar på det här sättet, kan Azure Resource Manager mäkla etableringsprocessen för ditt skript. Mallarna etablerar databaser parallellt och hanterar om det behövs igen. Skriptet är idempotent, så om det misslyckas eller stoppas av någon anledning, kör det igen.

### <a name="verify-the-batch-of-tenants-that-successfully-deployed"></a>Verifiera batchen med klienter som har distribuerats

* I [Azure-portalen](https://portal.azure.com)bläddrar du till din lista över servrar och öppnar *klienten1-servern.* Välj **SQL-databaser**och kontrollera att batchen med ytterligare 17 databaser nu finns i listan.

   ![Lista över databaser](media/saas-dbpertenant-provision-and-catalog/database-list.png)



## <a name="other-provisioning-patterns"></a>Andra etableringsmönster

Andra etableringsmönster som inte ingår i den här självstudien:

**Databaser före etablering**: Mönstret för företablering utnyttjar det faktum att databaser i en elastisk pool inte lägger till extra kostnad. Fakturering är för den elastiska poolen, inte databaserna. Inaktiva databaser förbrukar inga resurser. Genom att företablera databaser i en pool och allokera dem när det behövs kan du minska tiden för att lägga till klienter. Antalet databaser som företableras kan justeras efter behov för att hålla en buffert som är lämplig för den förväntade etableringsgraden.

**Automatisk etablering**: I mönstret för automatisk etablering avsersättningar, en etableringstjänst avsättningar servrar, pooler och databaser automatiskt efter behov. Om du vill kan du inkludera företablering av databaser i elastiska pooler. Om databaser inaktiveras och tas bort kan luckor i elastiska pooler fyllas i av etableringstjänsten. En sådan tjänst kan vara enkel eller komplex, till exempel att hantera etablering över flera geografiska områden och konfigurera geo-replikering för haveriberedskap.

Med mönstret för automatisk etablering skickar ett klientprogram eller skript en etableringsbegäran till en kö som ska bearbetas av etableringstjänsten. Det sedan opinionsundersökningar tjänsten för att avgöra slutförandet. Om företablering används hanteras begäranden snabbt. Tjänsten avsersättningar en ersättningsdatabas i bakgrunden.


## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
>
> * Etablera en enda ny klient.
> * Etablera en bunt ytterligare klienter.
> * Gå in i information om att etablera klienter och registrera dem i katalogen.

Prova [självstudiekursen För prestandaövervakning](saas-dbpertenant-performance-monitoring.md).

## <a name="additional-resources"></a>Ytterligare resurser

* Ytterligare [självstudier som bygger på Wingtip Tickets SaaS-databas-per-klient-program](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Klientbibliotek för elastiska databaser](sql-database-elastic-database-client-library.md)
* [Felsöka skript i Windows PowerShell ISE](https://docs.microsoft.com/powershell/scripting/components/ise/how-to-debug-scripts-in-windows-powershell-ise)
