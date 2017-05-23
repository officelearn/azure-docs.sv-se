---
title: "Etablera nya klienter i en app för flera klienter med Azure SQL Database | Microsoft Docs"
description: Etablera och katalogisera nya klienter i Wingtip Tickets (WTP) SaaS-programexemplet i SQL Database
keywords: sql database tutorial
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: tutorial
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 05/10/2017
ms.author: billgib; sstein
ms.translationtype: Human Translation
ms.sourcegitcommit: fc4172b27b93a49c613eb915252895e845b96892
ms.openlocfilehash: aae5d85a18f93b7821a6ef8fc7161dd9a6ebe533
ms.contentlocale: sv-se
ms.lasthandoff: 05/12/2017


---
# <a name="provision-new-tenants-and-register-them-in-the-catalog"></a>Etablera nya klienter och registrera dem i katalogen

I den här guiden får du etablera nya klienter i SaaS-programmet Wingtip biljettplattform (WTP). Du skapar klienter, klientdatabaser och registrerar klienter i katalogen. *Katalogen* är en databas som upprätthåller mappningen mellan SaaS-programmets många klienter och deras data. Använd de här skripten för att utforska de etablerings- och katalogmönster som används och hur registrering av nya klienter i katalogen implementeras. Katalogen spelar en viktig roll i att styra programbegäranden till rätt databas.

I den här guiden får du lära dig hur man:

> [!div class="checklist"]

> * Etablera en enskild ny klient
> * Etablera en batch med ytterligare klienter
> * Ta del av information om hur man etablerar nya klienter och registrerar dem i katalogen.


Se till att följande förhandskrav är slutförda för att kunna slutföra den här guiden:

* WTP-appen ska ha distribuerats. Du kan distribuera appen på mindre än fem minuter genom att följa anvisningarna i [Distribuera och utforska WTP SaaS-appen](sql-database-saas-tutorial.md)
* Azure PowerShell ska ha installerats. Mer information finns i [Kom igång med Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-the-saas-catalog-pattern"></a>Introduktion till SaaS-katalogmönstret

I ett multi-klient SaaS-program med databasstöd, är det viktigt att veta var informationen för varje klient lagras. I SaaS-katalogmönstret, används en katalogdatabas för att hålla reda på mappningen mellan klienter och var deras data lagras. WTP-appen använder en databasarkitektur med en enda klient, men det grundläggande mönstret för att lagra mappning i en katalog gäller oavsett om en databas med en eller flera klienter används.

Varje klient tilldelas en nyckel som särskiljer deras data i katalogen. I WTP-programmet, är nyckeln skapad från en hash av klientens namn. Det här mönstret gör att klientnamnsdelen av program-URL:en används för att skapa nyckeln och hämta en specifik klients anslutning. Andra ID-scheman skulle kunna användas utan att påverka det övergripande mönstret.

Katalogen i WTP-appen implementeras med fragmenthanteringsteknik i [klientbiblioteket för den elastiska databasen (EDCL)](sql-database-elastic-database-client-library.md). EDCL ansvarar för att skapa och hantera en databasstödd _katalog_ där en _fragmentkarta_ upprätthålls. Katalogen innehåller mappningen mellan nycklar (klienter) och deras databaser (fragment).

> [!IMPORTANT]
> Mappningsdata är tillgängliga i katalogdatabasen, men *redigera den inte*! Redigera endast mappningsdata med API:er för klientbiblioteket för den elastiska databasen. Direkt manipulering av mappningsdatan riskerar att skada katalogen och stöds inte.

Wingtip SaaS-appen etablerar nya klienter genom att kopiera en *gyllene* databas.

## <a name="get-the-wingtip-application-scripts"></a>Hämta Wingtip-programskripten

Wingtip biljettskripten och programmets källkod finns tillgängliga från github-repon [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS). Skriptfilerna finns i [mappen inlärningsmoduler](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules). Ladda ner mappen **inlärningsmoduler** till din lokala dator med sin mappstruktur intakt.

## <a name="provision-a-new-tenant"></a>Etablera en ny klient

Om du redan har skapat en klient i den första WTP-guiden, kan du gå vidare till nästa avsnitt: [etablera en batch med klienter](#provision-a-batch-of-tenants).

Kör skriptet *Demo-ProvisionAndCatalog* för att snabbt skapa en klient och registrera den i katalogen:

1. Öppna **Demo-ProvisionAndCatalog.ps1** i PowerShell ISE:n och ange följande värden:
   * **$TenantName** = namnet på den nya platsen (till exempel *Bushwillow Blues*). 
   * **$VenueType** = en av de fördefinierade platstyperna: blues, classicalmusic, dance, jazz, judo, motorracing, multipurpose, opera, rockmusic, soccer.
   * **$DemoScenario** = 1, lämna det här värdet som _1_ för att **Etablera en enda klient**.

1. Tryck på **F5** och kör skriptet.

Den nya innehavaren etableras när skriptet har körts, och deras *Evenemang*-app öppnas i webbläsaren:

![Ny klient](./media/sql-database-saas-tutorial-provision-and-catalog/new-tenant.png)


## <a name="provision-a-batch-of-tenants"></a>Etablera en batch med klienter

Den här övningen etablerar en batch med ytterligare klienter. Du bör göra det här innan du gör de andra WTP-guiderna.

1. Öppna ...\\Inlärningsmoduler\\Verktyg\\*Demo-ProvisionAndCatalog.ps1* i *PowerShell ISE:n* och ange följande värde:
   * **$DemoScenario** = **3**, ställ in som **3** för att **Etablera en batch med klienter**.
1. Tryck på **F5** och kör skriptet.

Skriptet etablerar en batch med ytterligare klienter. Den använder en [Azure Resource Manager-mall](../azure-resource-manager/resource-manager-template-walkthrough.md) som styr batchen och sedan delegerar etableringen av varje databas till en länkad mall. Om du använder mallar på det här sättet, kan Azure Resource Manager mäkla etableringsprocessen för ditt skript. Mallar etablerar databaser parallellt när det går och hanterar omförsök vid behov, vilket optimerar processen. Skriptet är idempotent så kör det igen om det avbryts.

### <a name="verify-the-batch-of-tenants-successfully-deployed"></a>Verifiera att batchen med klienter etablerades

* Öppna servern *tenants1* i [Azure-portalen](https://portal.azure.com) och klicka på **SQL-databaser**:

   ![databaslista](media/sql-database-saas-tutorial-provision-and-catalog/database-list.png)


## <a name="provision-and-catalog-detailed-walkthrough"></a>Etablera och katalogisera detaljerad genomgång

För en bättre förståelse av hur Wingtip-programmet implementerar etableringen av nya klienter, kör skriptet *Demo-ProvisionAndCatalog* igen och etablera en till klient. Den här gången lägger du till en brytpunkt och stegar dig igenom arbetsflödet:

1. Öppna... \\inlärningsmoduler\verktyg\_Demo-ProvisionAndCatalog.ps1_ och ange följande som de nya klientvärdena som inte finns i den aktuella katalogen:
   * **$TenantName** = ange ett nytt namn (till exempel, *Hackberry Hitters*).
   * **$VenueType** = Använd en av de fördefinierade plats typerna (till exempel *judo*).
   * **$DemoScenario** = 1, lämna det här värdet som **1** för att **Etablera en enda klient**.

1. Lägg till en brytpunkt genom att placera markören på följande rad: *New-Tenant*  och tryck på **F9**.

   ![brytpunkt](media/sql-database-saas-tutorial-provision-and-catalog/breakpoint.png)

1. Tryck **F5** för att köra skriptet. När brytpunkten kommer, trycker du på **F11** för att stega vidare. Spåra de skriptkörningen med **F10** och **F11** för att stega över eller in i de anropade funktionerna. [Tips om att arbeta med och felsöka PowerShell-skript](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)

### <a name="examine-the-provision-and-catalog-implementation-in-detail-by-stepping-through-the-script"></a>Kontrollera implementeringen av etablera och katalogisera i detalj genom att stega dig igenom skriptet

Skriptet etablerar och katalogiserar nya klienter genom att utföra följande steg:

1. **Importera modulen SubscriptionManagement.psm1** vilken innehåller funktioner för att logga in på Azure och markera den Azure-prenumeration du arbetar med.
1. **Importera modulen CatalogAndDatabaseManagement.psm1** vilken tillhandahåller en abstraktion på katalog- och klientnivå över [fragmenthanterings](sql-database-elastic-scale-shard-map-management.md)-funktionerna. Det här är en viktig modul som innehåller en stor del av katalogmönstret och är värd att utforska.
1. **Hämta konfigurationsinformationen**. Stega in i _Get-Configuration_ (med **F11**) och se hur appkonfigurationen specificeras. Resursnamn och andra appspecifika värden definieras här, men ändra inte några av värdena innan du bekantat dig med skriptet.
1. **Hämta katalogobjektet**. Stega in i *Get-Catalog* för att se hur katalogen initieras med fragmenthanterings-funktioner som importeras från **AzureShardManagement.psm1**. Katalogen består av följande objekt:
   * $catalogServerFullyQualifiedName konstrueras med standardstammen plus ditt användarnamn: _catalog-\<användare\>.database.windows.net_.
   * $catalogDatabaseName hämtas från konfigurationen: *tenantcatalog*.
   * $shardMapManager-objektet initieras från katalogdatabasen.
   * $shardMap-objektet initieras från fragmentkartan *tenantcatalog* i katalogdatabasen.
   Ett katalogobjekt skapas och returneras för att sedan användas i skriptet på högre nivå.
1. **Beräkna den nya klientnyckeln**. En hash-funktion används för att skapa klientnyckeln från klientnamnet.
1. **Kontrollera om klientnyckeln redan finns**. Katalogen kontrolleras för att försäkra om att nyckeln är tillgänglig.
1. **Klientdatabasen etableras med New-TenantDatabase.** Använd **F11** för att stega in och se hur databasen etableras med en Resource Manager-mall.
    
Databasnamnet skapas från klientnamnet för att klargöra vilket fragment som tillhör vilken klient. (Andra strategier för namngivning av databaser kan enkelt användas.)

En Resource Manager-mall används att **skapa en klientdatabas genom att kopiera en *gyllene*-databas** (baseTenantDB) på katalogservern.  En annan metod kan vara att skapa en tom databas och initiera den genom att importera en bacpac.

Resource Manager-mallen finns i mappen ...\\inlärningsmoduler\\vanliga\\: *tenantdatabasecopytemplate.json*

När klientdatabasen har skapats, initieras den ytterligare med plats(klient)-namnet och platstypen. Övrig initialisering kan också göras här.

Klientdatabasen registreras i katalogen med *Add-TenantDatabaseToCatalog* med klientnyckeln. Använd **F11** för att stega in i detaljerna:

* Katalogdatabasen läggs till i fragmentkartan (listan över kända databaser).
* Mappningen som länkar nyckelvärdet till fragmentet skapas.
* Ytterligare metadata (platsnamnet) om klienten läggs till.

När etableringen har slutförts, återgår körningen till det ursprungliga *Demo-ProvisionAndCatalog*-skriptet och **evenemang**-sidan för den nya klienten öppnas i webbläsaren:

   ![evenemang](media/sql-database-saas-tutorial-provision-and-catalog/new-tenant2.png)


## <a name="other-provisioning-patterns"></a>Andra etableringsmönster

Andra etableringsmönster som inte ingår i den här guiden inkluderar:

**Företablering av databaser.** Det här mönstret utnyttjar faktumet att databaser i en elastisk pool inte ökar kostnaden (faktureringen sker för den elastiska poolen, inte databaserna) och att inaktiva databaser inte konsumerar några resurser. Genom att företablera databaser i en pool och därefter allokera dem efter behov, kan integrationstiden minskas avsevärt. Antalet företablerade databaser kan justeras efter behov för att ha en buffert som är lämplig för den förväntade etableringstakten.

**Automatisk etablering.** I det här mönstret används en dedikerad etableringstjänst för att etablera servrar, pooler och databaser automatiskt efter behov. Inklusive företablering av databaser i elastiska pooler om så önskas. Om databaserna därefter tas ur drift och tas bort, kan luckorna i de elastiska poolerna fyllas av etableringstjänsten efter behov. En sådan tjänst kan vara enkel eller komplex. Den kan exempelvis hantera etablering över flera geografier och automatiskt ställa in geo-replikering om den strategin används för katastrofåterställning. Med mönstret för automatisk etablering, skickar ett klientprogram eller ett skript in en etableringsbegäran till en kö för att behandlas av etableringstjänsten och frågar sedan tjänsten för att avgöra när det slutförts. Om företablering används, hanteras begäranden snabbt när tjänsten hanterar etableringen av en ersättningsdatabas som körs i bakgrunden.


## <a name="stopping-wingtip-saas-application-related-billing"></a>Stoppa Wingtip SaaS-programrelaterad fakturering

Om du inte planerar att fortsätta med en annan guide, rekommenderar vi att du tar bort alla resurser för att stoppa fakturering. Ta bort resursgruppen som WTP-programmet distribuerades till så tas alla dess resurser bort.

* Bläddra till programmets resursgrupp i portalen och ta bort den för att stoppa all fakturering relaterad till den här WTP-distributionen.

## <a name="tips"></a>Tips

* EDCL innehåller också viktiga funktioner som låter klientprogram ansluta till och manipulera katalogen. Du kan även använda EDCL för att hämta en ADO.NET-anslutning för ett givet nyckelvärde, vilket låter programmet ansluta till rätt databas. Klienten cachelagrar den här anslutningsinformationen för att minimera trafiken till katalogdatabasen och snabba upp programmet.



## <a name="next-steps"></a>Nästa steg

I den här guiden lärde du dig hur man:

> [!div class="checklist"]

> * Etablera en enskild ny klient
> * Etablera en batch med ytterligare klienter
> * Ta del av information om hur man etablerar nya klienter och registrerar dem i katalogen.

[Guide för prestandaövervakning](sql-database-saas-tutorial-performance-monitoring.md)

## <a name="additional-resources"></a>Ytterligare resurser

* [Ytterligare guider som bygger på den initiala programdistributionen av Wingtip biljettplattformen (WTP)](sql-database-wtp-overview.md#sql-database-wtp-saas-tutorials)
* [Klientbibliotek för elastiska databaser](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-database-client-library)
* [Felsök skript i Windows PowerShell ISE](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)

