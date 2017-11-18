---
title: "Etablera och katalogen nya klienter i en SaaS-program med hjälp av en Azure SQL Database SQL-databas med flera innehavare | Microsoft Docs"
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
ms.date: 11/17/2017
ms.author: billgib
ms.openlocfilehash: f6707b85cc80178da663d7e2b95eeb5c9550789c
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/18/2017
---
# <a name="provision-and-catalog-new-tenants-in-a-saas-application-using-a-multi-tenant-sql-database"></a>Etablera och katalogen nya klienter i en SaaS-program som använder en SQL-databas med flera innehavare

I kursen får du lära dig om mönster för etablering och katalogiserar klienter när du arbetar med ett delat databasmodell för flera innehavare. 

Ett schema för flera innehavare kan data från flera klienter som ska lagras i en databas. För att stödja många hyresgäster distribueras klientdata i flera delar eller databaser. Data för alla en klient finns alltid helt i en enskild databas.  En katalog används för att hålla mappningen av klienter till databaser.   

Du kan också välja att fylla i vissa databaser med bara en enskild klient. Databaser som innehåller flera innehavare ge företräde åt en lägre kostnad per klient på bekostnad av klientisolering.  Databaser som innehåller endast en enskild klient ge företräde åt isolering över kostnaden.  Databaser med flera innehavare och enskild innehavare kan blandas i ett enda SaaS-program för att optimera kostnad eller isolering för varje klient. Klienter kan få sina egna databasen när etablerats eller kan flyttas till sina egna databasen senare.

   ![Delat flera innehavare databasen app med klient-katalog](media/saas-multitenantdb-provision-and-catalog/MultiTenantCatalog.png)


## <a name="tenant-catalog-pattern"></a>Mönster för klient-katalog

Om klientdata distribueras över flera databaser, är det viktigt att veta där varje innehavare data lagras. En katalog databas innehåller mappningen mellan en klient och den databas som lagrar data.

Varje klient identifieras med en nyckel i katalogen. Klientnyckeln bildas i Wingtip biljetter SaaS-appar från en hash av klientens namn. Detta innebär att kan extrahera innehavarens namn från en Webbadress och används för att ansluta till rätt databas. Andra viktiga klient-system kan också användas.

Om du använder en katalog kan namn eller på en klient databas ändras när du har etablerat utan att avbryta programmet.  I en databasmodell för flera innehavare har det flytta en klient mellan databaser.  Katalogen kan också användas för att ange att ett program om en klient är offline för underhåll eller andra åtgärder.

Katalogen kan utökas för att lagra ytterligare klient eller databasen metadata, till exempel nivå eller utgåva av en databas, schemaversionen, klientnamn och service-plan eller SLA och annan information att aktivera hantering av program, kundsupport eller devops processer.  

Katalogen kan också användas för hantering av flera innehavare rapportering, schemat och extrahering av data för analys syften. 

### <a name="elastic-database-client-library"></a>Klientbibliotek för Elastic Database 
I Wingtip biljetter SaaS-appar i katalogen är implementerad i den *tenantcatalog* databasen med Fragmentera hanteringsfunktionerna i den [elastisk databas klienten bibliotek (EDCL)](sql-database-elastic-database-client-library.md). Biblioteket aktiverar ett program att skapa, hantera och använda en databas underbyggt 'Fragmentera map'. En Fragmentera-mappning innehåller en lista över delar (databaser) och mappningen mellan nycklar (klienter) och delar.  EDCL funktioner kan användas från program eller PowerShell-skript under klient etablering för att skapa poster i kartan Fragmentera och senare för att ansluta till rätt databas. Biblioteket cachelagrar anslutningsinformationen för att minimera trafik på katalogdatabasen och snabbare anslutning. 

> [!IMPORTANT]
> Mappningsdata är tillgängliga i katalogdatabasen, men *redigera den inte*! Redigera endast mappningsdata med API:er för klientbiblioteket för den elastiska databasen. Direkt manipulering av mappningsdatan riskerar att skada katalogen och stöds inte.


## <a name="tenant-provisioning-pattern"></a>Etablering mönster för klient

Etablera en ny klient i delat flera innehavare databasmodellen, måste det först avgöra om klienten är att etableras i en delad databas eller får en egen databas. Om en delad databas, måste det att avgöra om det finns utrymme i en befintlig databas eller en ny databas krävs. Om det krävs en ny databas, måste den vara etablerade i lämplig plats och tjänstnivån initierats med lämpligt schema- och data och registreras sedan på katalogen. Slutligen kan innehavaren mappning läggas till refererar till rätt Fragmentera.

Databasetablering av kan uppnås genom att köra SQL-skript, distribuera en bacpac eller kopiera en mall för databas. Wingtip biljetter SaaS-program kopiera en mall för databas för att skapa den nya innehavaren databaser.

Databasen etablering metod måste vara comprehended i den övergripande schemat hanteringsstrategi som behöver för att säkerställa att nya databaser är etablerad med det senaste schemat.  Detta är utforskade ytterligare i den [schemat management kursen](saas-tenancy-schema-management.md).  

Klient etablering skripten i den här självstudiekursen inkluderar både etablering av en klient till en befintlig databas för flera innehavare och skapar en ny klient-databas. Klientdata sedan initierats och registreras sedan på katalogen Fragmentera kartan. Databaser med en enskild klient finns i sample-appen förnamn baserat på innehavarens namn. Databaser som innehåller flera innehavare ges en allmän _tenantsN_ namn när databaser med bara en enda-klient har angetts på innehavarens namn. Särskilda namngivningskonventioner används i samplet som är inte en viktig del av mönstret eftersom med en katalog kan valfritt namn kan tilldelas till databasen.  

## <a name="provision-and-catalog-tutorial"></a>Etablera och katalogen självstudiekursen

I den här guiden får du lära dig hur man:

> [!div class="checklist"]

> * Etablera en klient till en databas med flera innehavare
> * Etablera en klient till en enskild klient-databas
> * Etablera en grupp med klienter i både flera innehavare och single-klient
> * Registrera en databas och klient mappning i en katalog

Följande krav måste uppfyllas för att kunna köra den här självstudiekursen:

* Wingtip biljetter SaaS flera innehavare databasen appen har distribuerats. För att distribuera på mindre än fem minuter finns [distribuera och utforska databasprogram Wingtip biljetter SaaS flera innehavare](saas-multitenantdb-get-started-deploy.md)
* Azure PowerShell ska ha installerats. Mer information finns i [Komma igång med Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="get-the-wingtip-tickets-management-scripts"></a>Hämta hanteringsskript Wingtip biljetter

-Hanteringsskript och programmets källkod är tillgängliga i den [WingtipTicketsSaaS MultiTenantDB](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB) GitHub-lagringsplatsen. <!--See [Steps to download the Wingtip SaaS scripts](saas-tenancy-wingtip-app-guidance-tips.md#download-and-unblock-the-wingtip-saas-scripts).-->


## <a name="provision-tenant-walkthrough-1"></a>Etablera klient genomgången #1

För att förstå hur programmet Wingtip biljetter implementerar ny klient etablering med en databas med flera innehavare, lägger du till en brytpunkt och stega igenom arbetsflödet vid etablering av en klient i en delad databas med andra klienter:

1. I den _PowerShell ISE_öppnar... \\Learning moduler\\ProvisionAndCatalog\\_Demo-ProvisionAndCatalog.ps1_ och ange följande parametrar:
   * **$TenantName** = **Bushwillow blått**, namnet på en ny plats.
   * **$VenueType** = **blått**, en av de fördefinierade plats typerna: blått, classicalmusic, webbsidor, jazz, judo, motorracing, multipurpose, opera, rockmusic, fotboll (gemen, inga blanksteg).
   * **$DemoScenario** = **1**, *etablera en klient i en delad databas med andra klienter*.

1. Lägga till en brytpunkt genom att placera markören på rad 38, raden: *ny klient '*, och tryck på **F9**.

   ![brytpunkt](media/saas-multitenantdb-provision-and-catalog/breakpoint.png)

1. Att köra skriptet tryck **F5**.

1. Körningen av skriptet slutar vid brytpunkten, tryck på **F11** till steg i koden.

   ![Felsöka](media/saas-multitenantdb-provision-and-catalog/debug.png)

Spåra körning av skript med hjälp av den **felsöka** menyalternativen - **F10** och **F11** till steg via eller som kallas funktioner. Mer information om felsökning av PowerShell-skript finns [Tips om att arbeta med och felsöka PowerShell-skript](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise).


Följande är viktiga delar av arbetsflödet du gå igenom när spårning skriptet:

* **Beräkna den nya klientnyckeln**. En hash-funktion används för att skapa klientnyckeln från klientnamnet.
* **Kontrollera om klientnyckeln redan finns**. Katalogen kontrolleras för att se till att nyckeln inte redan har registrerats.
* **Initiera klient i klient standarddatabasen**. Klient-databasen uppdateras för att lägga till den nya informationen för klienten.  
* **Registrera klienten i katalogen** mappningen mellan den nya klientnyckeln och den befintliga databasen tenants1 har lagts till i katalogen. 
* **Innehavarens namn har lagts till i katalogen**. Namnet på platsen har lagts till tabellen hyresgäster i katalogen.  Här visas hur databasen katalog kan utökas för att stödja ytterligare programspecifika data.
* **Öppna sidan för händelser för den nya innehavaren**. Den *Bushwillow blått* händelser sidan öppnas i webbläsaren:

   ![evenemang](media/saas-multitenantdb-provision-and-catalog/bushwillow.png)


## <a name="provision-tenant-walkthrough-2"></a>Etablera klient genomgången #2

Nu genomgång processen när du skapar en klient i en egen databas:

1. Fortfarande i... \\Learning moduler\\ProvisionAndCatalog\\_Demo-ProvisionAndCatalog.ps1_ ange följande parametrar:
   * **$TenantName** = **sequoia fotboll**, namnet på en ny plats.
   * **$VenueType** = **fotboll**, en av de fördefinierade plats typerna: blått, classicalmusic, webbsidor, jazz, judo, motorracing, multipurpose, opera, rockmusic, fotboll (gemen, inga blanksteg).
   * **$DemoScenario** = **2**, *etablera en klient i en delad databas med andra klienter*.

1. Lägga till en ny brytpunkt genom att placera markören på raden 57, raden:  *& &nbsp;$PSScriptRoot\New-TenantAndDatabase '*, och tryck på **F9**.

   ![brytpunkt](media/saas-multitenantdb-provision-and-catalog/breakpoint2.png)

1. Tryck **F5** för att köra skriptet.

1. Körningen av skriptet slutar vid brytpunkten, tryck på **F11** till steg i koden.  Använd **F10** och **F11** att hoppa över och gå till funktioner för att spåra körningen.

Följande är viktiga delar av arbetsflödet du gå igenom när spårning skriptet:

* **Beräkna den nya klientnyckeln**. En hash-funktion används för att skapa klientnyckeln från klientnamnet.
* **Kontrollera om klientnyckeln redan finns**. Katalogen kontrolleras för att se till att nyckeln inte redan har registrerats.
* **Skapa en ny klient databas**. Databasen har skapats genom att kopiera den *basetenantdb* databasen med en Resource Manager-mall.  Databasnamnet är baserat på klientens namn.
* **Lägga till databasen i katalogen**. Klient-databas registreras som en Fragmentera i katalogen.
* **Initiera klient i klient standarddatabasen**. Klient-databasen uppdateras för att lägga till den nya informationen för klienten.  
* **Registrera klienten i katalogen** mappningen mellan den nya klientnyckeln och *sequoiasoccer* databas läggs till i katalogen.
* **Innehavarens namn har lagts till i katalogen**. Namnet på platsen har lagts till tabellen hyresgäster i katalogen.
* **Öppna sidan för händelser för den nya innehavaren**. Den *Sequoia fotboll* händelser sidan öppnas i webbläsaren:

   ![evenemang](media/saas-multitenantdb-provision-and-catalog/sequoiasoccer.png)


## <a name="provision-a-batch-of-tenants"></a>Etablera en grupp med klienter

Den här övningen etablerar snabbt en batch med 17 innehavare. Det rekommenderas att du etablerar den här gruppen med klienter innan du startar andra Wingtip biljetter självstudier, så det finns fler än några databaser du arbetar med.

1. I den *PowerShell ISE*öppnar... \\Learning moduler\\ProvisionAndCatalog\\*Demo-ProvisionAndCatalog.ps1* och ändra den *$DemoScenario* parameter till 3:
   * **$DemoScenario** = **3**, *etablera en grupp med klienter i en delad databas*.
1. Tryck på **F5** och kör skriptet.


### <a name="verify-the-deployed-set-of-tenants"></a>Kontrollera en distribuerad uppsättning klienter 
Du har en blandning av klienter som har distribuerats till en delad databas och klienter som har distribuerats till sina egna databaser i det här skedet. Azure-portalen kan användas för att inspektera de databaser som skapas:  

* I den [Azure-portalen](https://portal.azure.com)öppnar den **tenants1-huvudmålservern -\<användare\>**  servern genom att bläddra i listan över SQL-servrar.  Den **SQL-databaser** listan över ska inkludera den delade **tenants1** databasen och databaserna för klienter som finns i deras egna databasen:

   ![databaslista](media/saas-multitenantdb-provision-and-catalog/databases.png)

Under den Azure-portalen visar innehavaren databaser, den vi visas hyresgäster i den delade databasen. En fullständig lista över klienter kan ses i sidan Wingtip biljetter händelser hubb.   

* Öppna sidan händelser hubb i webbläsaren (http:events.wingtip-mt.\<användaren\>. trafficmanager.net)  

En fullständig lista över klienter och deras motsvarande databas finns i katalogen. En SQL-vyn finns i databasen tenantcatalog som ansluter till innehavarens namn lagras i tabellen klienter till databasnamn i tabellerna Fragmentera Management. Den här vyn visar snyggt värdet för att utöka de metadata som lagras i katalogen.

* I *SQL Server Management Studio (SSMS)* ansluta till servern för klienter på **om tenants1 mt.\<användare\>. database.windows.net**, med inloggningen: **utvecklare** , Lösenord:**P@ssword1**

    ![SSMS anslutningsdialogrutan](media/saas-multitenantdb-provision-and-catalog/SSMSConnection.png)

* I den *Object Explorer*, bläddra till vyer i den *tenantcatalog* databas.
* Högerklicka på *ExtendedTenants* och välj **Välj de 1000 översta raderna** och notera mappningen mellan klientnamn och databasen för olika klienter.

    ![ExtendedTenants vyn i SSMS](media/saas-multitenantdb-provision-and-catalog/extendedtenantsview.png)
      
## <a name="other-provisioning-patterns"></a>Andra etableringsmönster

Andra etableringsmönster som inte ingår i den här guiden inkluderar:

**Före etablering databaser i elastiska pooler.** Före etablering mönstret utnyttjar det faktum att databaser i en elastisk pool inte Lägg till extra kostnad. Faktureringen är för den elastiska poolen inte databaser och inaktiv databaser förbrukar några resurser. Före etablering databaser i poolen och tilldela dem vid behov, kan tiden det tar att publicera hyresgäster i sina egna databasen minskas avsevärt. Antalet företablerade databaser kan justeras efter behov för att ha en buffert som är lämplig för den förväntade etableringstakten.

**Automatisk etablering.** I mönstret Automatisk etablering används en dedikerad etablering tjänst för att etablera servrar, pooler och databaserna automatiskt vid behov – inklusive före etablering databaser i elastiska pooler om så önskas. Om databaserna därefter tas ur drift och tas bort, kan luckorna i de elastiska poolerna fyllas av etableringstjänsten efter behov. Dessa tjänster kan vara enkla eller avancerade – till exempel hantera etablering över flera områden, och kan ställa in geo-replikering för katastrofåterställning. Ett client-program eller skript med Automatisk etablering-mönster skulle begära ett allokering till en kö som ska bearbetas av en tjänst för etablering och sedan skulle avsöka för att fastställa slutförande. Om före etablering används, skulle begäranden hanteras snabbt, med den tjänst som hanterar etablering av en ersättning-databas som körs i bakgrunden.



## <a name="next-steps"></a>Nästa steg

I den här guiden lärde du dig hur man:

> [!div class="checklist"]

> * Etablera en enskild ny klient
> * Etablera en batch med ytterligare klienter
> * Gå till information om etablering klienter och registrera dem till katalogen

Försök i [prestanda övervakning kursen](saas-multitenantdb-performance-monitoring.md).

## <a name="additional-resources"></a>Ytterligare resurser

<!--* Additional [tutorials that build upon the Wingtip SaaS application](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
* [Klientbibliotek för elastiska databaser](sql-database-elastic-database-client-library.md)
* [Felsök skript i Windows PowerShell ISE](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)
