---
title: "Etablera nya klienter i en app för flera innehavare som använder Azure SQL Database | Microsoft Docs"
description: "Lär dig hur du etablera och katalogen nya klienter i en Azure SQL Database med flera innehavare SaaS-app"
keywords: sql database tutorial
services: sql-database
documentationcenter: 
author: stevestein
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2017
ms.author: sstein
ms.openlocfilehash: eda330a7202de8a325d645b37a0d05ef8df8985b
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2017
---
# <a name="learn-how-to-provision-and-catalog-new-tenants-and-register-them-in-the-catalog"></a>Lär dig hur du etablera och katalogen nya klienter och registrerar dem i katalogen

I kursen får du lära dig om SaaS-mönster etablera och katalogen och hur de implementeras i Wingtip SaaS-program. Skapa och initiera den nya innehavaren databaser och registrera dem i programkatalogen klient. Katalogen är en databas som underhåller mappningen mellan många hyresgäster i SaaS-program och data. Katalogen spelar en viktig roll dirigera programförfrågningar till rätt databas.  

I den här guiden får du lära dig hur man:

> [!div class="checklist"]

> * Etablera en enda ny klient, inklusive stega igenom hur detta implementeras
> * Etablera en batch med ytterligare klienter


Följande krav måste uppfyllas för att kunna köra den här självstudiekursen:

* Wingtip SaaS-appen har distribuerats. För att distribuera på mindre än fem minuter finns [distribuera och utforska Wingtip SaaS-program](sql-database-saas-tutorial.md)
* Azure PowerShell ska ha installerats. Mer information finns i [Kom igång med Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-the-saas-catalog-pattern"></a>Introduktion till SaaS-katalogmönstret

I ett databasen har säkerhetskopierats flera innehavare SaaS-program är det viktigt att veta var informationen för varje klient lagras. I mönster för SaaS-katalog är en katalog databas används för att lagra mappningen mellan varje klient och där deras data lagras. Wingtip SaaS-appen använder en enskild klient per databas arkitektur, men det grundläggande mönstret för lagring av mappning för klient-till-databasen i en katalog gäller om en eller flera single-klient databas används.

Varje klient tilldelas en nyckel som identifierar dem i katalogen och som är mappad till platsen för en lämplig databas. Nyckeln har ett format från en hash av klientens namn i Wingtip SaaS-app. På så sätt kan klienten del av programmets URL som används för att skapa nyckeln. Andra viktiga klient-system kan användas.  

Katalogen kan namn eller plats på databasen som ska ändras med minimal påverkan på programmet.  I en databasmodell för flera innehavare, detta kan även hantera, flytta' en klient mellan databaser.  Katalogen kan också användas för att ange om en klient eller databasen är offline för underhåll eller andra åtgärder. Detta är utforskade i den [Återställ enstaka klient kursen](sql-database-saas-tutorial-restore-single-tenant.md).

Dessutom erbjudas katalog, som är i praktiken en management-databas för ett SaaS-program, kan lagra ytterligare klient eller databasmetadata, till exempel nivå eller utgåva av en databas, schemaversionen, service-plan eller serviceavtal till klienter och annan information som gör att programhantering, kundsupport eller devops-processer.  

Katalogen kan aktivera Databasverktyg utöver SaaS-program.  I exemplet Wingtip SaaS katalogen används för att aktivera mellan klient-fråga, utforskade i den [ad hoc-analytics kursen](sql-database-saas-tutorial-adhoc-analytics.md). Hantering av flera databaser utskriftsjobb utforskade i den [schemat management](sql-database-saas-tutorial-schema-management.md) och [klient analytics](sql-database-saas-tutorial-tenant-analytics.md) självstudier. 

I appen Wingtip SaaS katalogen implementeras med hjälp av Fragmentera hanteringsfunktionerna i den [elastisk databas klienten bibliotek (EDCL)](sql-database-elastic-database-client-library.md). EDCL kan ett program för att skapa, hantera och använda en databasen har säkerhetskopierats Fragmentera karta. En Fragmentera-mappning innehåller en lista över delar (databaser) och mappningen mellan nycklar (klienter) och databaser.  EDCL funktioner kan användas från program eller PowerShell-skript under etablering för att skapa poster i kartan Fragmentera klient och program för att effektivt ansluta till rätt databas. EDCL cachelagrar anslutningsinformationen för att minimera trafiken till katalogdatabasen och snabba upp programmet.  

> [!IMPORTANT]
> Mappningsdata är tillgängliga i katalogdatabasen, men *redigera den inte*! Redigera endast mappningsdata med API:er för klientbiblioteket för den elastiska databasen. Direkt manipulering av mappningsdatan riskerar att skada katalogen och stöds inte.


## <a name="introduction-to-the-saas-provisioning-pattern"></a>Introduktion till mönstret SaaS-etablering

När onboarding en ny klient i ett SaaS-program som använder måste en enskild klient databasmodell en ny klient-databas etableras.  Det måste skapas i rätt plats och tjänstnivån initierats med lämpligt schema och referensdata och registreras sedan på katalogen under lämpliga klientnyckeln.  

Olika metoder kan användas till att databasen etablering, som kan omfatta SQL-skript körs, distribution av en bacpac eller kopiera en 'gyllene' mall-databas.  

Etablering metod du använder måste vara comprehended i din övergripande schemat hanteringsstrategi som skall se till att nya databaser är etablerad med det senaste schemat.  Detta är utforskade i den [schemat management kursen](sql-database-saas-tutorial-schema-management.md).  

Wingtip SaaS app tillhandahåller nya klienter genom att kopiera en gyllene databas med namnet basetenantdb, distribueras på en katalog-server.  Etablering kan vara integrerat i programmet som en del av en registrering och/eller stöds offline med hjälp av skript. Den här självstudiekursen kommer utforska etablering med hjälp av PowerShell. Etablering skripten kopiera basetenantdb för att skapa en ny klient-databas i en elastisk pool och sedan initiera med klient-specifik information och registrera det i katalogen Fragmentera kartan.  I sample-appen databaser får namn baserat på innehavarens namn, men detta är inte en viktig del av mönstret – med katalogen kan valfritt namn kan tilldelas till databasen. + 


## <a name="get-the-wingtip-application-scripts"></a>Hämta Wingtip-programskript

Wingtip SaaS-skript och programmets källkod är tillgängliga i den [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS) github-lagringsplatsen. [Steg för att hämta Wingtip SaaS-skripten](sql-database-wtp-overview.md#download-and-unblock-the-wingtip-saas-scripts).


## <a name="provision-and-catalog-detailed-walkthrough"></a>Etablera och katalogisera detaljerad genomgång

Lägg till en brytpunkt och stega igenom arbetsflödet vid etablering av en klient för att förstå hur programmet Wingtip implementerar ny klient etablering:

1. Öppna... \\Learning moduler\\ProvisionAndCatalog\\_Demo-ProvisionAndCatalog.ps1_ och ange följande parametrar:
   * **$TenantName** = namnet på den nya platsen (till exempel *Bushwillow Blues*).
   * **$VenueType** = en av de fördefinierade plats typerna: *blått*, classicalmusic, webbsidor, jazz, judo, motorracing, multipurpose, opera, rockmusic, fotboll.
   * **$DemoScenario** = **1**, inställd på **1** till *etablera en enskild klient*.

1. Lägga till en brytpunkt genom att placera markören på raden 48, raden: *ny klient '*, och tryck på **F9**.

   ![brytpunkt](media/sql-database-saas-tutorial-provision-and-catalog/breakpoint.png)

1. Att köra skriptet tryck **F5**.

1. Körningen av skriptet slutar vid brytpunkten, tryck på **F11** till steg i koden.

   ![brytpunkt](media/sql-database-saas-tutorial-provision-and-catalog/debug.png)



Spåra körning av skript med hjälp av den **felsöka** menyalternativen - **F10** och **F11** till steg via eller som kallas funktioner. Mer information om felsökning av PowerShell-skript finns [Tips om att arbeta med och felsöka PowerShell-skript](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise).


Följande är inte steg att följa explicit, men en förklaring av arbetsflöde du steg för steg när du felsöker skriptet:

1. **Importera modulen SubscriptionManagement.psm1** vilken innehåller funktioner för att logga in på Azure och markera den Azure-prenumeration du arbetar med.
1. **Importera modulen CatalogAndDatabaseManagement.psm1** vilken tillhandahåller en abstraktion på katalog- och klientnivå över [fragmenthanterings](sql-database-elastic-scale-shard-map-management.md)-funktionerna. Det här är en viktig modul som innehåller en stor del av katalogmönstret och är värd att utforska.
1. **Hämta konfigurationsinformationen**. Gå till Get-konfiguration (med F11) och se hur programmets konfiguration har angetts. Resursnamn och andra appspecifika värden definieras här, men ändra inte några av värdena innan du bekantat dig med skriptet.
1. **Hämta katalogobjektet**. Gå till Get-katalog composes och returnerar ett objekt i katalogen som används i skriptet på högre nivå.  Den här funktionen används Fragmentera hanteringsfunktioner som importeras från **AzureShardManagement.psm1**. Katalogobjektet består av följande:
   * $catalogServerFullyQualifiedName konstrueras med standardstammen plus ditt användarnamn: _catalog-\<användare\>.database.windows.net_.
   * $catalogDatabaseName hämtas från konfigurationen: *tenantcatalog*.
   * $shardMapManager-objektet initieras från katalogdatabasen.
   * $shardMap-objektet initieras från fragmentkartan *tenantcatalog* i katalogdatabasen.
   Ett katalogobjekt skapas och returneras för att sedan användas i skriptet på högre nivå.
1. **Beräkna den nya klientnyckeln**. En hash-funktion används för att skapa klientnyckeln från klientnamnet.
1. **Kontrollera om klientnyckeln redan finns**. Katalogen kontrolleras för att försäkra om att nyckeln är tillgänglig.
1. **Klientdatabasen etableras med New-TenantDatabase.** Använd **F11** till steg och se hur databasen är etablerats med hjälp av en [Azure Resource Manager-mall](../azure-resource-manager/resource-manager-template-walkthrough.md).

Databasnamnet skapas från klientnamnet för att klargöra vilket fragment som tillhör vilken klient. (Andra strategier för namngivning av databasen enkelt kunde användas.) + En Resource Manager-mall används för att skapa en klient-databas genom att kopiera en gyllene databas (baseTenantDB) på katalogservern. En annan metod kan vara att skapa en tom databas och sedan starta den genom att importera en bacpac eller köra ett Initieringsskript för från en känd plats.  

Resource Manager-mallen finns i mappen ...\Learning Modules\Common\: *tenantdatabasecopytemplate.json*

När klient-databasen har skapats, är det sedan ytterligare **initieras med namnet på platsen (klient) och vilken plats**. Övrig initialisering kan också göras här.

Den **databas för klienten har registrerats i katalogen** med *Lägg till TenantDatabaseToCatalog* med hjälp av klientnyckeln. Använd **F11** för att stega in i detaljerna:

* Katalogdatabasen läggs till i fragmentkartan (listan över kända databaser).
* Mappningen som länkar nyckelvärdet till fragmentet skapas.
* Ytterligare metadata (namn på den platsen) om klienten har lagts till tabellen hyresgäster i katalogen.  Tabellen klienter är inte en del av ShardManagement schema och installeras inte som EDCL.  I följande tabell visas hur databasen katalog kan utökas för att stödja ytterligare programspecifika data.   


När etableringen är klar körning returnerar till ursprungligt *Demo-ProvisionAndCatalog* skript, vilket öppnar den **händelser** för den nya innehavaren i webbläsaren:

   ![evenemang](media/sql-database-saas-tutorial-provision-and-catalog/new-tenant.png)


## <a name="provision-a-batch-of-tenants"></a>Etablera en grupp med klienter

Den här övningen etablerar en batch med 17 innehavare. Det rekommenderas att du etablerar den här gruppen med klienter innan du startar andra Wingtip SaaS-självstudier, så det finns fler än några databaser du arbetar med.

1. Öppna... \\Learning moduler\\ProvisionAndCatalog\\*Demo-ProvisionAndCatalog.ps1* i den *PowerShell ISE* och ändra den *$ DemoScenario* parameter till 3:
   * **$DemoScenario** = **3**, ändra till **3** till *etablera en grupp med klienter*.
1. Tryck på **F5** och kör skriptet.

Skriptet etablerar en batch med ytterligare klienter. Den använder en [Azure Resource Manager-mall](../azure-resource-manager/resource-manager-template-walkthrough.md) som styr batchen och sedan delegerar etableringen av varje databas till en länkad mall. Om du använder mallar på det här sättet, kan Azure Resource Manager mäkla etableringsprocessen för ditt skript. Mallar etablerar databaser parallellt när det går och hanterar omförsök vid behov, vilket optimerar processen. Skriptet är idempotent så om den misslyckas eller avbryts av någon anledning, kör det igen.

### <a name="verify-the-batch-of-tenants-successfully-deployed"></a>Verifiera att batchen med klienter etablerades

* Öppna den *tenants1* servern genom att bläddra i listan över servrar i den [Azure-portalen](https://portal.azure.com), klickar du på **SQL-databaser**, och verifiera batch 17 nya databaser är nu i den lista:

   ![databaslista](media/sql-database-saas-tutorial-provision-and-catalog/database-list.png)



## <a name="other-provisioning-patterns"></a>Andra etableringsmönster

Andra etableringsmönster som inte ingår i den här guiden inkluderar:

**Företablering av databaser.** Före etablering mönstret utnyttjar det faktum att databaser i en elastisk pool inte Lägg till extra kostnad. Faktureringen är för den elastiska poolen inte databaser och inaktiv databaser förbrukar några resurser. Genom före etablering databaser i poolen och tilldela dem vid behov, minskas tid för klient onboarding avsevärt. Antalet företablerade databaser kan justeras efter behov för att ha en buffert som är lämplig för den förväntade etableringstakten.

**Automatisk etablering.** I mönstret Automatisk etablering används en dedikerad etablering tjänst för att etablera servrar, pooler och databaserna automatiskt vid behov – inklusive före etablering databaser i elastiska pooler om så önskas. Om databaserna därefter tas ur drift och tas bort, kan luckorna i de elastiska poolerna fyllas av etableringstjänsten efter behov. Dessa tjänster kan vara enkla eller avancerade – till exempel hantera etablering över flera områden, och kan ställa in geo-replikering automatiskt om den strategin som används för katastrofåterställning. Med mönstret för automatisk etablering, skickar ett klientprogram eller ett skript in en etableringsbegäran till en kö för att behandlas av etableringstjänsten och frågar sedan tjänsten för att avgöra när det slutförts. Om företablering används, hanteras begäranden snabbt när tjänsten hanterar etableringen av en ersättningsdatabas som körs i bakgrunden.



## <a name="next-steps"></a>Nästa steg

I den här guiden lärde du dig hur man:

> [!div class="checklist"]

> * Etablera en enskild ny klient
> * Etablera en batch med ytterligare klienter
> * Gå till information om etablering klienter och registrera dem till katalogen

Försök i [prestanda övervakning kursen](sql-database-saas-tutorial-performance-monitoring.md).

## <a name="additional-resources"></a>Ytterligare resurser

* Ytterligare [självstudier som bygger på Wingtip SaaS-program](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)
* [Klientbibliotek för elastiska databaser](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-database-client-library)
* [Felsök skript i Windows PowerShell ISE](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)
