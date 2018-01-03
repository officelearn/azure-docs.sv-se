---
title: Etablera i SaaS flera innehavare Azure | Microsoft Docs
description: "Lär dig hur du etablera och katalogen nya klienter i en Azure SQL Database med flera innehavare SaaS-app"
keywords: sql database tutorial
services: sql-database
documentationcenter: 
author: MightyPen
manager: craigg
editor: MightyPen
ms.reviewer: billgib;andrela;genemi
ms.assetid: 
ms.service: sql-database
ms.custom: saas apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/19/2017
ms.author: billgib
ms.openlocfilehash: 42bbb6131aa71520410b22af4d74e99a63fe81cf
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/20/2017
---
# <a name="provision-and-catalog-new-tenants-in-a-saas-application-using-a-sharded-multi-tenant-azure-sql-database"></a>Etablera och katalogen nya klienter i en SaaS-program som använder ett delat flera innehavare Azure SQL-databas

Den här artikeln beskriver etablering och katalogiserar av nya klienter i en *flera innehavare delat databasen* modell eller mönster.

Den här artikeln har två huvuddelar:

- [Diskussion](#goto_2_conceptual) för etablering och katalogiserar av nya klienter.

- [Kursen](#goto_1_tutorial) som visar PowerShell-skriptkod som genomför etablering och omkatalogiseras.
    - I självstudiekursen används Wingtip biljetter SaaS-program, anpassade till flera innehavare delat databasen mönstret.

<a name="goto_2_conceptual"/>

## <a name="database-pattern"></a>Databasen mönster

Det här avsnittet, samt några fler som följer, diskutera begrepp för flera innehavare delat databasen mönster.

I den här modellen med flera innehavare delat är tabellscheman i varje databas en klientnyckel i den primära nyckeln i tabeller som lagrar klientdata. Klientnyckeln gör det möjligt för varje enskild databas för att lagra 0, 1 eller många klienter. Användningen av delat databaser gör det enkelt för systemets program som stöder ett stort antal klienter. Alla data för alla en klient lagras i en databas. Ett stort antal klienter distribueras över flera delat databaser. En katalogdatabasen lagrar mappningen av varje klient i dess databas.

#### <a name="isolation-versus-lower-cost"></a>Isolering jämfört med lägre kostnad

En klient som har en databas till sig själv gillar fördelarna med isolering. Klienten kan ha databasen återställas till ett tidigare datum utan begränsas av påverkan på andra klienter. Databasens prestanda kan anpassas för att optimera för en klient, utan att äventyra med andra klienter. Problemet är att isolering kostar mer än det kostar för att dela en databas med andra klienter.

När en ny klient har etablerats delar en databas med andra klienter eller den kan placeras på en egen ny databas. Du kan senare ångrar dig och flytta databasen till situationen som helst.

Databaser med flera innehavare och enkel hyresgäster blandas i samma SaaS-programmet att optimera kostnad eller isolering för varje klient.

   ![Delat flera innehavare databasen app med klient-katalog](media/saas-multitenantdb-provision-and-catalog/MultiTenantCatalog.png)

## <a name="tenant-catalog-pattern"></a>Mönster för klient-katalog

När du har två eller flera databaser att var och en innehåller minst en klient, måste det ha ett sätt att identifiera vilken databas som lagrar de aktuella intresse. En katalogdatabasen lagrar mappningen.

#### <a name="tenant-key"></a>Klientnyckel

För varje klient härleda Wingtip programmet en unik nyckel som är klientnyckeln. Appen extraherar innehavarens namn från URL: en för webbsidan. Appen hashar namnet att hämta nyckeln. Appen använder nyckeln för att få åtkomst till katalogen. Korsreferenser kataloginformationen om innehavaren databas. Appen använder databasen information för att ansluta. Andra viktiga klient-system kan också användas.

Om du använder en katalog kan namn eller på en klient databas ändras när du har etablerat utan att avbryta programmet. I en databasmodell för flera innehavare innehåller katalogen flytta en klient mellan databaser.

#### <a name="tenant-metadata-beyond-location"></a>Klient metadata efter plats

Katalogen kan också ange om en klient är offline för underhåll eller andra åtgärder. Och katalogen kan utökas för att lagra ytterligare klient eller databasmetadata, till exempel följande:
- Tjänstnivån eller utgåva av en databas.
- Versionen av databasschemat.
- Innehavarens namn och dess SLA (serviceavtal).
- Information för att aktivera hantering av program, kundsupport eller devops-processer.  

Katalogen kan också användas för hantering av flera innehavare rapportering, schemat och extrahering av data för analys syften. 

### <a name="elastic-database-client-library"></a>Klientbibliotek för Elastic Database 

I Wingtip, katalogen är implementerad i den *tenantcatalog* databas. Den *tenantcatalog* skapas med hjälp av Fragmentera hanteringsfunktionerna i den [elastisk databas klienten bibliotek (EDCL)](sql-database-elastic-database-client-library.md). Biblioteket aktiverar ett program att skapa, hantera och använda en *Fragmentera kartan* som lagras i en databas. En karta Fragmentera korsreferenser klientnyckel med dess Fragmentera, vilket innebär att delat databasen.

Under etablering-klient kan EDCL funktioner användas från program eller PowerShell-skript för att skapa poster i kartan Fragmentera. Senare kan EDCL-funktioner användas för att ansluta till rätt databas. EDCL cachelagrar anslutningsinformationen för att minimera trafik på katalogdatabasen och snabbare att ansluta.

> [!IMPORTANT]
> Gör *inte* redigera data i katalogdatabasen genom direkt åtkomst! Direkt uppdateringar stöds inte på grund av hög risk för skadade data. I stället redigera mappning med hjälp av EDCL API: er endast.

## <a name="tenant-provisioning-pattern"></a>Etablering mönster för klient

#### <a name="checklist"></a>Checklista

När du vill etablera en ny klient till en befintlig delad databas måste du ställa följande frågor för den delade databasen:
- Har det tillräckligt med diskutrymme för den nya innehavaren?
- Har företaget tabeller med nödvändiga referensdata för den nya innehavaren eller går att lägga till data?
- Har den lämplig variation av det grundläggande schemat för den nya innehavaren?
- Är det i lämplig geografisk plats nära den nya innehavaren?
- Är det på rätt tjänstnivån för den nya innehavaren?

När du vill att den nya innehavaren till separat i en egen databas, skapar du den så att den uppfyller specifikationerna för innehavaren.

När etableringen är klar måste du registrera klienten i katalogen. Slutligen kan innehavaren mappning läggas till refererar till rätt Fragmentera.

#### <a name="template-database"></a>Mall för databas

Etablera databasen genom att köra SQL-skript, distribution av en bacpac eller kopiera en mall för databas. Wingtip appar kopiera en mall för databas för att skapa den nya innehavaren databaser.

Precis som alla program, kommer Wingtip utvecklas över tid. Ibland kan kräver Wingtip ändringar i databasen. Ändringar kan innehålla följande:
- Nya eller ändrade schemat.
- Nya eller ändrade referensdata.
- Vanliga uppgifter för databasunderhåll att säkerställa optimala prestanda.

Med ett SaaS-program, behöver de här ändringarna distribueras på ett samordnat sätt över en potentiellt massiv mängd klientdatabaser. För att ändringarna ska vara i framtiden klient databaser måste de införlivas i etableringsprocessen. Denna utmaning har arbetat ytterligare i den [schemat management kursen](saas-tenancy-schema-management.md).

#### <a name="scripts"></a>Skript

Klient etablering skripten i den här självstudiekursen har stöd för båda av följande scenarier:
- Etablera en klient till en befintlig databas som delas med andra klienter.
- Etablera en klient till en egen databas.

Klientdata initieras sedan och registrerats i katalogen Fragmentera kartan. I sample-appen databaser som innehåller flera innehavare ges ett allmänt namn som *tenants1* eller *tenants2*. Databaser som innehåller en enda klient ges klientens namn. Särskilda namngivningskonventioner används i samplet som är inte en viktig del av mönstret eftersom med en katalog kan valfritt namn kan tilldelas till databasen.  

<a name="goto_1_tutorial"/>

## <a name="tutorial-begins"></a>Kursen börjar

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Etablera en klient till en databas med flera innehavare
> * Etablera en klient till en enskild klient-databas
> * Etablera en grupp med klienter i både flera innehavare och single-klient
> * Registrera en databas och klient mappning i en katalog

#### <a name="prerequisites"></a>Förutsättningar

Följande krav måste uppfyllas för att kunna köra den här självstudiekursen:

- Azure PowerShell ska ha installerats. Mer information finns i [Komma igång med Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

- Wingtip biljetter SaaS flera innehavare databasen appen har distribuerats. För att distribuera på mindre än fem minuter finns [distribuera och utforska databasprogram Wingtip biljetter SaaS flera innehavare](saas-multitenantdb-get-started-deploy.md)

- Hämta Wingtip skript och kod:
    - Wingtip biljetter SaaS flera innehavare databasen skript och programmets källkod är tillgängliga i den [WingtipTicketsSaaS MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) GitHub-lagringsplatsen.
    - Finns det [allmänna riktlinjer](saas-tenancy-wingtip-app-guidance-tips.md) steg för att ladda ned och avblockera Wingtip skript. 

## <a name="provision-a-tenant-into-a-database-shared-with-other-tenants"></a>Etablera en klient till en databas *delade* med andra klienter

I det här avsnittet finns en lista med viktiga åtgärder för att etablera som utförs av PowerShell-skript. Sedan kan du använda PowerShell ISE-felsökaren till steg för steg skript för att se vilka åtgärder i kod.

#### <a name="major-actions-of-provisioning"></a>Större åtgärder för etablering av

Följande är viktiga delar i etablering arbetsflödet du steg för steg:

- **Beräkna den nya klientnyckeln**: hash-funktion som används för att skapa klientnyckeln från innehavarens namn.
- **Kontrollera om det redan finns klientnyckeln**: katalogen kontrolleras för att se till att nyckeln inte redan har registrerats.
- **Initiera klient i klient standarddatabasen**: klient-databasen uppdateras för att lägga till den nya informationen för klienten.  
- **Registrera klienten i katalogen**: mappningen mellan den nya klientnyckeln och den befintliga databasen tenants1 har lagts till i katalogen. 
- **Lägg till den klientnamn till en katalog tillägget tabell**: namnet på platsen har lagts till i tabellen hyresgäster i katalogen.  Det här tillägget visar hur databasen katalog kan utökas för att stödja ytterligare programspecifika data.
- **Öppna sidan för händelser för den nya innehavaren**: den *Bushwillow blått* händelser sidan öppnas i webbläsaren.

   ![evenemang](media/saas-multitenantdb-provision-and-catalog/bushwillow.png)

#### <a name="debugger-steps"></a>Steg för felsökning

Lägga till en brytpunkt och stega igenom arbetsflödet för att förstå hur appen Wingtip implementerar en ny klient etablering i en delad databas:

1. I den *PowerShell ISE*öppnar... \\Learning moduler\\ProvisionTenants\\*Demo-ProvisionTenants.ps1* och ange följande parametrar:
   - **$TenantName** = **Bushwillow blått**, namnet på en ny plats.
   - **$VenueType** = **blått**, en av de fördefinierade plats typerna: blått, classicalmusic, webbsidor, jazz, judo, motorracing, multipurpose, opera, rockmusic, fotboll (gemener, inga blanksteg).
   - **$DemoScenario** = **1**, för att etablera en klient i en delad databas med andra klienter.

2. Lägga till en brytpunkt genom att placera markören på rad 38, raden: *ny klient '*, och tryck sedan på **F9**.

   ![brytpunkt](media/saas-multitenantdb-provision-and-catalog/breakpoint.png)

3. Kör skript genom att trycka på **F5**.

4. Skriptkörningen slutar vid brytpunkten, tryck på **F11** till steg i koden.

   ![felsök](media/saas-multitenantdb-provision-and-catalog/debug.png)

5. Spåra körning av skript med hjälp av den **felsöka** menyalternativen **F10** och **F11**, för att gå via eller som kallas funktioner.

Mer information om felsökning av PowerShell-skript finns [Tips om att arbeta med och felsöka PowerShell-skript](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise).

## <a name="provision-a-tenant-in-its-own-database"></a>Etablera en klient i dess *egna* databas

#### <a name="major-actions-of-provisioning"></a>Större åtgärder för etablering av

Följande är viktiga delar av arbetsflödet du gå igenom när spårning skriptet:

- **Beräkna den nya klientnyckeln**: hash-funktion som används för att skapa klientnyckeln från innehavarens namn.
- **Kontrollera om det redan finns klientnyckeln**: katalogen kontrolleras för att se till att nyckeln inte redan har registrerats.
- **Skapa en ny klient databas**: databasen har skapats genom att kopiera den *basetenantdb* databasen med en Resource Manager-mall.  Det nya databasnamnet är baserat på klientens namn.
- **Lägga till databasen i katalogen**: ny klient-databas registreras som en Fragmentera i katalogen.
- **Initiera klient i klient standarddatabasen**: klient-databasen uppdateras för att lägga till den nya informationen för klienten.  
- **Registrera klienten i katalogen**: mappningen mellan den nya klientnyckeln och *sequoiasoccer* databas läggs till i katalogen.
- **Innehavarens namn har lagts till i katalogen**: namnet på platsen har lagts till i tabellen hyresgäster tillägg i katalogen.
- **Öppna sidan för händelser för den nya innehavaren**: den *Sequoia fotboll* händelser sidan öppnas i webbläsaren.

   ![evenemang](media/saas-multitenantdb-provision-and-catalog/sequoiasoccer.png)

#### <a name="debugger-steps"></a>Steg för felsökning

Gå igenom skriptprocessen när du skapar en klient i en egen databas:

1. Fortfarande i... \\Learning moduler\\ProvisionTenants\\*Demo-ProvisionTenants.ps1* ange följande parametrar:
   - **$TenantName** = **sequoia fotboll**, namnet på en ny plats.
   - **$VenueType** = **fotboll**, en av de fördefinierade plats typerna: blått, classicalmusic, webbsidor, jazz, judo, motorracing, multipurpose, opera, rockmusic, fotboll (gemen, inga blanksteg).
   - **$DemoScenario** = **2**, för att etablera en klient i en egen databas.

2. Lägga till en ny brytpunkt genom att placera markören på raden 57, raden:  *& &nbsp;$PSScriptRoot\New-TenantAndDatabase '*, och tryck på **F9**.

   ![brytpunkt](media/saas-multitenantdb-provision-and-catalog/breakpoint2.png)

3. Kör skript genom att trycka på **F5**.

4. Körningen av skriptet slutar vid brytpunkten, tryck på **F11** till steg i koden.  Använd **F10** och **F11** att hoppa över och gå till funktioner för att spåra körningen.

## <a name="provision-a-batch-of-tenants"></a>Etablera en grupp med klienter

Den här övningen etablerar en batch med 17 innehavare. Det rekommenderas att du etablerar den här gruppen med klienter innan du startar andra kurser i Wingtip biljetter så att det finns flera databaser du arbetar med.

1. I den *PowerShell ISE*öppnar... \\Learning moduler\\ProvisionTenants\\*Demo-ProvisionTenants.ps1* och ändra den *$DemoScenario* parameter till 4:
   - **$DemoScenario** = **4**, för att etablera en grupp med klienter i en delad databas.

2. Tryck på **F5** och kör skriptet.

### <a name="verify-the-deployed-set-of-tenants"></a>Kontrollera en distribuerad uppsättning klienter 

Du har en blandning av klienter som har distribuerats till en delad databas och klienter som har distribuerats till sina egna databaser i det här skedet. Azure-portalen kan användas för att inspektera de databaser som skapas. I den [Azure-portalen](https://portal.azure.com)öppnar den **tenants1-huvudmålservern -\<användare\>**  servern genom att bläddra i listan över SQL-servrar.  Den **SQL-databaser** listan över ska inkludera den delade **tenants1** databasen och databaserna för klienter som finns i deras egna databasen:

   ![databaslista](media/saas-multitenantdb-provision-and-catalog/Databases.png)

Medan Azure portal visas innehavaren databaser, kan inte se hyresgäster *inuti* den delade databasen. En fullständig lista över klienter kan ses i den **händelser hubb** webbsidan Wingtip och genom att bläddra i katalogen.

#### <a name="using-wingtip-tickets-events-hub-page"></a>Med Wingtip biljetter händelser hubb sida

Öppna sidan händelser hubb i webbläsaren (http:events.wingtip-mt.\<användaren\>. trafficmanager.net)  

#### <a name="using-catalog-database"></a>Med katalog-databas

En fullständig lista över klienter och motsvarande databas för varje finns i katalogen. En SQL-vy är förutsatt att kopplingar innehavaren namn till namnet på databasen. Vyn visar snyggt värdet för att utöka de metadata som lagras i katalogen.
- SQL-vyn finns i tenantcatalog-databasen.
- Innehavarens namn lagras i tabellen innehavare.
- Namnet på databasen lagras i tabellerna Fragmentera Management.

1. I SQL Server Management Studio (SSMS), ansluta till servern för klienter på **om katalogen mt.\<användare\>. database.windows.net**, med inloggningen = **developer**, och lösenord =**P@ssword1**

    ![SSMS anslutningsdialogrutan](media/saas-multitenantdb-provision-and-catalog/SSMSConnection.png)

2. SSMS Object Explorer bläddrar du till vyer i den *tenantcatalog* databas.

3. Högerklicka på vyn *TenantsExtended* och välj **Välj de 1000 översta raderna**. Observera mappningen mellan klientnamn och databasen för olika klienter.

    ![ExtendedTenants vyn i SSMS](media/saas-multitenantdb-provision-and-catalog/extendedtenantsview.png)
      
## <a name="other-provisioning-patterns"></a>Andra etableringsmönster

Det här avsnittet beskrivs andra intressanta mönster för etablering.

#### <a name="pre-provisioning-databases-in-elastic-pools"></a>Före etablering databaser i elastiska pooler

Före etablering mönstret utnyttjar det faktum att när du använder elastiska pooler, fakturering för poolen inte är databaserna. Därmed kan databaser läggas till en elastisk pool innan de behövs utan att det medför extra kostnad. Den här före visioning avsevärt minskar den tid det tar att etablera en klient till en databas. Antalet databaser som etableras i förväg kan justeras efter behov för att hålla en buffert lämpar sig för den förväntade etablering hastigheten.

#### <a name="auto-provisioning"></a>Automatisk etablering

I mönstret Automatisk etablering används en dedikerad etablering tjänst för att etablera servrar, pooler och databaserna automatiskt efter behov. Det här automation innehåller före etablering av databaser i elastiska pooler. Och om databaser inaktiveras och tas bort, luckor skapas i elastiska pooler kan fyllas av tjänsten etablering efter behov.

Den här typen av automatiserad tjänst kan vara enkla eller komplexa. Automatisering kan hantera etablering över flera geografiska områden och konfigurera geo-replikering för katastrofåterställning. Med Automatisk etablering-mönster skulle ett client-program eller skript begära ett allokering till en kö som ska bearbetas av en tjänst för etablering. Skriptet skulle sedan söka för att identifiera slutförande. Om före etablering används skulle begäranden hanteras snabbt, medan en Bakgrundstjänster skulle hantera etablering av en databas för ersättning.

## <a name="additional-resources"></a>Ytterligare resurser

<!-- - Additional [tutorials that build upon the Wingtip SaaS application](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
- [Klientbibliotek för elastiska databaser](sql-database-elastic-database-client-library.md)
- [Felsök skript i Windows PowerShell ISE](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)


## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Etablera en enda ny klient till en delad databas för flera innehavare och en egen databas
> * Etablera en batch med ytterligare klienter
> * Gå igenom informationen om etablering klienter och registrera dem till katalogen

Försök i [prestanda övervakning kursen](saas-multitenantdb-performance-monitoring.md).

