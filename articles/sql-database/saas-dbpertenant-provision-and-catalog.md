---
title: "Etablera nya klienter i en app för flera innehavare som använder Azure SQL Database | Microsoft Docs"
description: "Lär dig hur du etablera och katalogen nya klienter i en Azure SQL Database med flera innehavare SaaS-app"
keywords: sql database tutorial
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 08/11/2017
ms.author: sstein
ms.openlocfilehash: 21f0bca3a16164ead4e0990842a968fd9b95c33f
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="learn-how-to-provision-new-tenants-and-register-them-in-the-catalog"></a>Lär dig att etablera nya klienter och registrerar dem i katalogen

I kursen får information du om SaaS-mönster etablera och katalogen och hur de implementeras i Wingtip biljetter SaaS databasen per klient program. Skapa och initiera den nya innehavaren databaser och registrera dem i programkatalogen klient. Katalogen är en databas som underhåller mappningen mellan många hyresgäster i SaaS-program och data. Katalogen spelar en viktig roll dirigera programmet och av hanteringsbegäranden till rätt databas.  

I den här guiden får du lära dig hur man:

> [!div class="checklist"]

> * Etablera en enskild ny klient
> * Etablera en batch med ytterligare klienter


Följande krav måste uppfyllas för att kunna köra den här självstudiekursen:

* Wingtip biljetter SaaS databas per klient appen har distribuerats. För att distribuera på mindre än fem minuter finns [distribuera och utforska Wingtip biljetter SaaS databasen per klient program](saas-dbpertenant-get-started-deploy.md)
* Azure PowerShell ska ha installerats. Mer information finns i [Komma igång med Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-the-saas-catalog-pattern"></a>Introduktion till SaaS-katalogmönstret

I ett databasen har säkerhetskopierats flera innehavare SaaS-program är det viktigt att veta var informationen för varje klient lagras. I mönster för SaaS-katalog är en katalog databas används för att lagra mappningen mellan varje klient och databasen där deras data lagras. Det här mönstret gäller när klientdata distribueras över flera databaser.

Varje klient identifieras med en nyckel i katalogen, som är mappad till platsen för databasen. I appen Wingtip biljetter format nyckeln från en hash av klientens namn. Detta tillåter appen att skapa nyckeln från innehavarens namn som ingår i programmets URL. Andra viktiga klient-system kan användas.  

Katalogen kan namn eller plats på databasen som ska ändras med minimal påverkan på programmet.  I en databasmodell för flera innehavare, detta kan även hantera, flytta' en klient mellan databaser.  Katalogen kan också användas för att ange om en klient eller databasen är offline för underhåll eller andra åtgärder. Detta är utforskade i den [Återställ enstaka klient kursen](saas-dbpertenant-restore-single-tenant.md).

Katalogen kan också lagra ytterligare klient eller databasen metadata, till exempel schemaversionen, service-plan eller serviceavtal erbjudas till klienter, samt annan information som gör det möjligt för programhantering, kundsupport eller devops.  

Katalogen kan aktivera Databasverktyg utöver SaaS-program.  I Wingtip biljetter SaaS-databasen per klient prov katalogen används för att aktivera mellan klient-fråga, utforskade i den [ad hoc-rapportering kursen](saas-tenancy-cross-tenant-reporting.md). Hantering av flera databaser utskriftsjobb utforskade i den [schemat management](saas-tenancy-schema-management.md) och [klient analytics](saas-tenancy-tenant-analytics.md) självstudier. 

I exemplen Wingtip biljetter SaaS katalogen implementeras med hjälp av Fragmentera hanteringsfunktionerna i den [elastisk databas klienten bibliotek (EDCL)](sql-database-elastic-database-client-library.md). EDCL finns i Java och .net Framework. EDCL kan ett program för att skapa, hantera och använda en databasen har säkerhetskopierats Fragmentera karta. En Fragmentera-mappning innehåller en lista över delar (databaser) och mappningen mellan nycklar (klienter) och delar.  EDCL funktioner används under klientorganisationens Etableringsläge för att skapa poster i kartan Fragmentera och sedan vid körning av program för att ansluta till rätt databas. EDCL cachelagrar anslutningsinformationen för att minimera trafik till katalogdatabasen och snabba upp programmet.  

> [!IMPORTANT]
> Mappningsdata är tillgängliga i katalogdatabasen, men *redigera den inte*! Redigera endast mappningsdata med API:er för klientbiblioteket för den elastiska databasen. Direkt manipulering av mappningsdatan riskerar att skada katalogen och stöds inte.


## <a name="introduction-to-the-saas-provisioning-pattern"></a>Introduktion till mönstret för SaaS-etablering

När onboarding en ny klient i ett SaaS-program som använder en enskild klient databasmodell, en ny klient-databas måste etableras.  Databasen måste skapas i rätt plats och tjänstnivån initierats med lämpligt schema- och data och registrerats i katalogen under lämpliga klientnyckeln.  

Olika sätt att databasetablering kan användas som innehåller SQL-skript körs, distribution av en bacpac eller kopiera en mall för databas.  

Databasetablering av måste vara en del av din schemat hanteringsstrategi som skall se till att nya databaser är etablerad med det senaste schemat. Det här kravet utforskade i den [schemat management kursen](saas-tenancy-schema-management.md).  

Wingtip biljetter databas per klient app etablerar nya klienter genom att kopiera en mall för databas med namnet _basetenantdb_, distribuerad på katalog-server.  Etablering kan vara integrerat i programmet som en del av en registrering och/eller stöds offline med hjälp av skript. Den här självstudiekursen utforskar etablering med hjälp av PowerShell. Etablering skript kopiera den _basetenantdb_ databasen för att skapa en ny klient-databas i en elastisk pool och sedan initiera databasen med klient-specifik information och registrera den i katalogen Fragmentera kartan.  Klient databaser är angivet namn baserat på innehavarens namn, men den här namngivningsschemat är inte en viktig del av mönstret – som katalogen mappar klientnyckeln till namnet på databasen som en namngivningskonvention kan användas. 


## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Hämta programskript Wingtip biljetter SaaS databas Per klient

Wingtip biljetter SaaS-skript och programmets källkod är tillgängliga i den [WingtipTicketsSaaS DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub-lagringsplatsen. Kolla in den [allmänna riktlinjer](saas-tenancy-wingtip-app-guidance-tips.md) steg för att ladda ned och avblockera Wingtip biljetter SaaS-skript.


## <a name="provision-and-catalog-detailed-walkthrough"></a>Etablera och katalogisera detaljerad genomgång

Lägg till en brytpunkt och stega igenom arbetsflödet vid etablering av en klient för att förstå hur programmet Wingtip biljetter implementerar ny klient etablering:

1. I _PowerShell ISE_öppnar... \\Learning moduler\\ProvisionAndCatalog\\_Demo-ProvisionAndCatalog.ps1_ och ange följande parametrar:
   * **$TenantName** = namnet på den nya platsen (till exempel *Bushwillow Blues*).
   * **$VenueType** = en av de fördefinierade plats typerna: _blått, classicalmusic, webbsidor, jazz, judo, motor tävling, multipurpose, opera, rockmusic, fotboll_.
   * **$DemoScenario** = **1**, *etablera en enskild klient*.

1. Lägga till en brytpunkt genom att placera markören på raden: *ny klient '*, och tryck på **F9**.

   ![brytpunkt](media/saas-dbpertenant-provision-and-catalog/breakpoint.png)

1. Att köra skriptet tryck **F5**.

1. Körningen av skriptet slutar vid brytpunkten, tryck på **F11** till steg i koden.

   ![Felsökning](media/saas-dbpertenant-provision-and-catalog/debug.png)



Spåra körning av skript med hjälp av den **felsöka** menyalternativen - **F10** och **F11** till steg via eller som kallas funktioner. Mer information om felsökning av PowerShell-skript finns [Tips om att arbeta med och felsöka PowerShell-skript](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise).


Följande är inte steg att följa explicit, men en förklaring av arbetsflöde du steg för steg när du felsöker skriptet:

1. **Importera modulen CatalogAndDatabaseManagement.psm1** vilken tillhandahåller en abstraktion på katalog- och klientnivå över [fragmenthanterings](sql-database-elastic-scale-shard-map-management.md)-funktionerna. Den här modulen kapslar mycket av mönstret katalog och är värt att utforska.
1. **Importera modulen SubscriptionManagement.psm1** vilken innehåller funktioner för att logga in på Azure och markera den Azure-prenumeration du arbetar med.
1. **Hämta konfigurationsinformationen**. Gå till Get-konfiguration (med F11) och se hur programmets konfiguration har angetts. Resursnamn och andra app-specifik värden definieras här, men ändra inte dessa värden tills du är bekant med skript.
1. **Hämta katalogobjektet**. Gå till Get-katalog composes och returnerar ett objekt i katalogen som används i skriptet på högre nivå.  Den här funktionen används Fragmentera hanteringsfunktioner som importeras från **AzureShardManagement.psm1**. Katalogobjektet består av följande element:
   * $catalogServerFullyQualifiedName konstrueras standard stam plus ditt användarnamn: _katalog -\<användare\>. database.windows .net_.
   * $catalogDatabaseName hämtas från konfigurationen: *tenantcatalog*.
   * $shardMapManager-objektet initieras från katalogdatabasen.
   * $shardMap-objektet initieras från fragmentkartan _tenantcatalog_ i katalogdatabasen.
   Ett katalogobjekt skapas och returneras för att sedan användas i skriptet på högre nivå.
1. **Beräkna den nya klientnyckeln**. En hash-funktion används för att skapa klientnyckeln från klientnamnet.
1. **Kontrollera om klientnyckeln redan finns**. Katalogen kontrolleras för att försäkra om att nyckeln är tillgänglig.
1. **Klientdatabasen etableras med New-TenantDatabase.** Använd **F11** till steg och se hur databasen är etablerats med hjälp av en [Azure Resource Manager-mall](../azure-resource-manager/resource-manager-template-walkthrough.md).

    Namnet på databasen konstrueras utifrån innehavarens namn för att klargöra vilka Fragmentera hör till vilken klient, även om andra namngivningskonventioner för databasen kan användas.
    En mall för hanteraren för filserverresurser skapar en klient-databas genom att kopiera en mall för databas (_baseTenantDB_) på katalogservern. Som ett alternativ kan du skapa en databas och initiera genom att importera en bacpac eller köra ett Initieringsskript för från en känd plats.

    Resource Manager-mallen finns i mappen ...\Learning Modules\Common\: *tenantdatabasecopytemplate.json*

1. När klient-databasen har skapats, är det sedan ytterligare **initieras med namnet på platsen (klient) och vilken plats**. Övrig initialisering kan också göras här.

1. Den **databas för klienten har registrerats i katalogen** med *Lägg till TenantDatabaseToCatalog* med hjälp av klientnyckeln. Använd **F11** för att stega in i detaljerna:

    * Katalogdatabasen läggs till i fragmentkartan (listan över kända databaser).
    * Mappningen som länkar nyckelvärdet till fragmentet skapas.
    * Ytterligare metadata om klienten (namn på den platsen) har lagts till i den *hyresgäster* tabellen i katalogen.  Tabellen klienter är inte en del av ShardManagement schema och installeras inte som EDCL.  I följande tabell visas hur databasen katalog kan utökas för att stödja ytterligare programspecifika data.   


När etableringen är klar körning returnerar till ursprungligt *Demo-ProvisionAndCatalog* skript, vilket öppnar den **händelser** för den nya innehavaren i webbläsaren:

   ![evenemang](media/saas-dbpertenant-provision-and-catalog/new-tenant.png)


## <a name="provision-a-batch-of-tenants"></a>Etablera en grupp med klienter

Den här övningen etablerar en batch med 17 innehavare. Det rekommenderas att du etablerar den här gruppen med klienter innan du startar andra Wingtip biljetter SaaS databas per klient självstudier, så det finns fler än några databaser du arbetar med.

1. I den *PowerShell ISE*öppnar... \\Learning moduler\\ProvisionAndCatalog\\*Demo-ProvisionAndCatalog.ps1* och ändra den *$DemoScenario* parameter till 3:
   * **$DemoScenario** = **3**, *etablera en grupp med klienter*.
1. Tryck **F5** för att köra skriptet.

Skriptet etablerar en batch med ytterligare klienter. Den använder en [Azure Resource Manager-mall](../azure-resource-manager/resource-manager-template-walkthrough.md) som styr batch och som delegerar etablering av varje databas för en länkad mall. Om du använder mallar på det här sättet, kan Azure Resource Manager mäkla etableringsprocessen för ditt skript. Mallar etablera databaser parallellt och om det behövs, hanterar återförsök. Skriptet är idempotent, så om den misslyckas eller avbryts av någon anledning, kör det igen.

### <a name="verify-the-batch-of-tenants-successfully-deployed"></a>Verifiera att batchen med klienter etablerades

* I den [Azure-portalen](https://portal.azure.com)genom att bläddra i listan över servrar och öppna den *tenants1* server. Klicka på **SQL-databaser** och verifiera batch 17 nya databaser finns nu i listan:

   ![databaslista](media/saas-dbpertenant-provision-and-catalog/database-list.png)



## <a name="other-provisioning-patterns"></a>Andra etableringsmönster

Andra etableringsmönster som inte ingår i den här guiden inkluderar:

**Företablering av databaser.** Före etablering mönstret utnyttjar det faktum att databaser i en elastisk pool inte Lägg till extra kostnad. Faktureringen är för den elastiska poolen inte databaser och inaktiv databaser förbrukar några resurser. Genom före etablering databaser i poolen och tilldela dem vid behov, minskas tid för klient onboarding avsevärt. Antalet databaser som etableras i förväg kan justeras efter behov för att hålla en buffert lämpar sig för den förväntade etablering hastigheten.

**Automatisk etablering.** Inklusive före etablering databaser i elastiska pooler i mönstret Automatisk etablering, en allokering bestämmelser server, pooler och databaserna automatiskt efter behov – om så önskas. Och om databaser Frigör uppdrag och tas bort, luckor i elastiska pooler fyllas av tjänsten etablering. Dessa tjänster kan vara enkla eller avancerade – till exempel hantera etablering över flera områden, och kan ställa in geo-replikering för katastrofåterställning. Ett klientprogram eller skript med Automatisk etablering-mönster skickar en begäran om allokering till en kö som ska bearbetas av tjänsten etablering och avsöker tjänsten för att fastställa slutförande. Om före etablering används, skulle begäranden hanteras snabbt, med tjänsten etablerar en ersättning databas i bakgrunden.


## <a name="next-steps"></a>Nästa steg

I den här guiden lärde du dig hur man:

> [!div class="checklist"]

> * Etablera en enskild ny klient
> * Etablera en batch med ytterligare klienter
> * Gå till information om etablering klienter och registrera dem till katalogen

Försök i [prestanda övervakning kursen](saas-dbpertenant-performance-monitoring.md).

## <a name="additional-resources"></a>Ytterligare resurser

* Ytterligare [självstudier som bygger på Wingtip biljetter SaaS databasen per klient program](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Klientbibliotek för elastiska databaser](sql-database-elastic-database-client-library.md)
* [Felsök skript i Windows PowerShell ISE](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)
