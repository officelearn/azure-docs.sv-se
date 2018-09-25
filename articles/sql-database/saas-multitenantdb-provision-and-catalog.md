---
title: Etablera i SaaS med flera innehavare Azure | Microsoft Docs
description: Lär dig hur du etablera och katalogisera nya klienter i en Azure SQL Database med flera innehavare SaaS-app
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: billgib,andrela,stein
manager: craigg
ms.date: 04/01/2018
ms.openlocfilehash: e37bc5f46a1a56357e3dff9d1f67de7dcc2537b0
ms.sourcegitcommit: 715813af8cde40407bd3332dd922a918de46a91a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47055313"
---
# <a name="provision-and-catalog-new-tenants-in-a-saas-application-using-a-sharded-multi-tenant-azure-sql-database"></a>Etablera och katalogisera nya klienter i ett SaaS-program med hjälp av ett delat Azure SQL-databas för flera innehavare

Den här artikeln beskriver den etablera och katalogisera av nya klienter i en *fragmenterade (sharded) databas för flera innehavare* modell eller mönster.

Den här artikeln har två huvuddelar:

- [Diskussion](#goto_2_conceptual) av etablera och katalogisera av nya klienter.

- [Självstudien](#goto_1_tutorial) som visar PowerShell-skriptkod som genomför etablera och katalogisera.
    - I självstudiekursen används Wingtip biljetter SaaS-program, anpassade till mönstret fragmenterade (sharded) databas för flera innehavare.

<a name="goto_2_conceptual"/>

## <a name="database-pattern"></a>Mönster för databasen

Det här avsnittet, plus några mer som följer, diskutera begreppet mönstret fragmenterade (sharded) databas för flera innehavare.

I den här modellen med flera innehavare shardade innehåller tabellscheman i varje databas en klientnyckel i den primära nyckeln i tabeller som lagrar data för klientorganisationen. Klientnyckeln gör det möjligt för varje enskild databas för att lagra 0, 1 eller många klienter. Användning av shardade databaser gör det enkelt för program-system för ett mycket stort antal klienter. Alla data för en klientorganisation lagras i en databas. Det stora antalet klienter är fördelade över många shardade databaser. En katalogdatabas lagrar mappningen av varje klient i dess databas.

#### <a name="isolation-versus-lower-cost"></a>Isolering och lägre kostnader

En klient som har en databas till sig själv kan utnyttja fördelarna isolering. Klienten kan ha databasen återställs till ett tidigare datum utan att begränsas av påverkan på andra klienter. Databasprestanda kan ställa in för att optimera för en klient, utan kompromisser med andra klienter. Problemet är att isolering kostar mer än vad det kostar för att dela en databas med andra klienter.

När en ny klient har etablerats, det kan dela en databas med andra klienter eller den kan placeras i en egen ny databas. Du kan senare ångrar och flytta databasen till det andra fallet.

Databaser med flera klienter och enkel klienter blandas i samma SaaS-program, att optimera kostnader och isolering för varje klient.

   ![Fragmenterade (sharded) databas för flera innehavare app med klientkatalog](media/saas-multitenantdb-provision-and-catalog/MultiTenantCatalog.png)

## <a name="tenant-catalog-pattern"></a>Klient-katalogmönstret

När du har två eller flera databaser som alla innehåller minst en klient kan måste programmet ha ett sätt att identifiera vilken databas som lagrar klient aktuella intressanta. En katalogdatabas lagrar den här mappningen.

#### <a name="tenant-key"></a>klientnyckel

Wingtip-programmet kan härleda en unik nyckel, vilket är klientnyckel för varje klient. Appen extraherar klientnamnet från URL: en för webbsidan. Appen hashar namnet att hämta nyckeln. Appen använder nyckeln med åtkomst till katalogen. Katalogen korsreferenser information om den databas där klienten är lagrad. Appen använder informationen som databasen för att ansluta. Andra viktiga klient-system kan också användas.

Om du använder en katalog kan namn eller plats för en klientdatabas ändras när du har etablerat utan att avbryta programmet. I en databas för flera innehavare-modell har katalogen en klient flyttar mellan databaser.

#### <a name="tenant-metadata-beyond-location"></a>Klient-metadatan utöver plats

Katalogen kan också indikera om en klient är offline för underhåll och andra åtgärder. Och katalogen kan utökas för att lagra ytterligare klient eller databasmetadata, till exempel följande:
- Tjänstnivå eller versionen av en databas.
- Versionen av databasschemat.
- Innehavarens namn och dess SLA (serviceavtal).
- Information om att aktivera programhantering, kundsupport eller devops-processer.  

Katalogen kan också användas för hantering av flera klienter rapportering, schemat och data extrahera för analysändamål. 

### <a name="elastic-database-client-library"></a>Klientbibliotek för Elastic Database 

I Wingtip, katalogen implementeras i den *tenantcatalog* databas. Den *tenantcatalog* skapas med hjälp av Fragmenthanterings funktionerna i den [Elastic Database-klientbibliotek (EDCL)](sql-database-elastic-database-client-library.md). Biblioteket gör det möjligt för ett program för att skapa, hantera och använda en *fragmentkartan* som lagras i en databas. En skärvkarta korsreferenser klientnyckel med dess fragment, vilket innebär att dess fragmenterade (sharded) databas.

Under klient etablering, användas EDCL funktioner från program eller PowerShell-skript om du ska skapa poster i fragmentkartan. EDCL-funktioner kan senare användas för att ansluta till rätt databas. EDCL cachelagrar anslutningsinformationen för att minimera trafiken i katalogdatabasen och snabba upp processen för att ansluta.

> [!IMPORTANT]
> Gör *inte* redigera data i katalogdatabasen via direktåtkomst! Direct uppdateringar stöds inte på grund av hög risken för korrupta data. I stället redigera mappningsdata genom att endast använda EDCL API: er.

## <a name="tenant-provisioning-pattern"></a>Mönster för etablering av klient

#### <a name="checklist"></a>Checklista

När du vill etablera en ny klient till en befintlig delad databas, måste du ställa följande frågor för den delade databasen:
- Har det tillräckligt med diskutrymme för den nya innehavaren?
- Har företaget tabeller med nödvändiga referensdata för den nya innehavaren eller kan data läggas till?
- Har lämplig variation av det grundläggande schemat för den nya innehavaren?
- Är det i rätt geografisk plats nära den nya innehavaren?
- Är det på rätt tjänstnivå för den nya innehavaren?

När du vill att den nya klienten att separat i en egen databas, skapar du den så att den uppfyller specifikationerna för klienten.

När etableringen har slutförts, måste du registrera klienten i katalogen. Slutligen kan den klientmappning läggas till refererar till rätt fragment.

#### <a name="template-database"></a>Mall för databas

Etablera databasen genom att köra SQL-skript, distribuerar en bacpac eller kopiera en mall-databas. Wingtip appar kopiera en mall för databas för att skapa nya klientdatabaserna.

Wingtip precis som alla program kommer att utvecklas över tid. Ibland kräver Wingtip ändringar i databasen. Ändringarna kan inkludera följande objekt:
- Nya eller ändrade scheman.
- Nya eller ändrade referensdata.
- Vanliga underhållsaktiviteter för att säkerställa optimal apprestanda.

Med ett SaaS-program, behöver de här ändringarna distribueras på ett samordnat sätt över en potentiellt massiv mängd klientdatabaser. För att ändringarna ska vara i framtida klientdatabaser, behöver de inkluderas i etableringsprocessen. Den här utmaningen utforskat ytterligare i den [schemat självstudiekurs om enhetshantering](saas-tenancy-schema-management.md).

#### <a name="scripts"></a>Skript

Klient etablering skripten i den här självstudien har stöd för båda av följande scenarier:
- Etablera en klient till en befintlig databas som delas med andra klienter.
- Etablera en klient i en egen databas.

Klientdata är sedan initierats och registrerats i katalogen fragmentkartan. I exempelappen, databaser med flera klienter ges ett allmänt namn, till exempel *tenants1* eller *tenants2*. Databaser som innehåller en enda klient ges klientens namn. Särskilda namngivningskonventioner som används i det här exemplet är inte en viktig del av mönstret som användning av en katalog tillåter vilket namn som ska tilldelas till databasen.  

<a name="goto_1_tutorial"/>

## <a name="tutorial-begins"></a>Självstudien börjar

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Etablera en klient till en databas för flera innehavare
> * Etablera en klient till en enda klient-databas
> * Etablera en batch med klienter i en enda klient- och flera innehavare-databaser
> * Registrera en databas och klientmappning i en katalog

#### <a name="prerequisites"></a>Förutsättningar

Följande krav måste uppfyllas för att kunna köra den här självstudiekursen:

- Azure PowerShell ska ha installerats. Mer information finns i [Komma igång med Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

- Databas för flera klienter i Wingtip biljetter SaaS-appen har distribuerats. Om du vill distribuera i mindre än fem minuter [distribuera och utforska databas för flera klienter i Wingtip biljetter SaaS-program](saas-multitenantdb-get-started-deploy.md)

- Hämta Wingtip-skript och källkoden:
    - Databas för flera klienter i Wingtip biljetter SaaS-skript och programmets källkod finns tillgängliga i den [WingtipTicketsSaaS MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) GitHub-lagringsplatsen.
    - Se den [allmänna riktlinjer](saas-tenancy-wingtip-app-guidance-tips.md) steg att ladda ned och avblockera Wingtip-skript. 

## <a name="provision-a-tenant-into-a-database-shared-with-other-tenants"></a>Etablera en klient till en databas *delade* med andra klienter

I det här avsnittet ska se du en lista med viktiga åtgärder för etablering som vidtas av PowerShell-skript. Sedan använder du PowerShell ISE-felsökare för att stega igenom skripten för att se åtgärderna i kod.

#### <a name="major-actions-of-provisioning"></a>Viktiga åtgärder för att etablera

Här följer nyckelelement för etablering arbetsflödet du går igenom:

- **Beräkna den nya klientnyckeln**: hash-funktion som används för att skapa klientnyckeln från klientnamnet.
- **Kontrollera om klientnyckeln redan finns**: katalogen kontrolleras för att se till att nyckeln inte redan har registrerats.
- **Initiera klienten i standard klientdatabasen**: klientdatabasen har uppdaterats för att lägga till nya klientinformation.  
- **Registrera klient i katalogen**: mappningen mellan den nya klientnyckeln och den befintliga tenants1-databasen har lagts till i katalogen. 
- **Lägga till klientens namn i en katalog tillägget tabellen**: namnet på platsen har lagts till i tabellen klienter i katalogen.  Här beskrivs hur katalogdatabasen kan utökas för att stödja ytterligare programspecifik data.
- **Öppna sidan för evenemang för den nya innehavaren**: den *Bushwillow Blues* händelser sidan öppnas i webbläsaren.

   ![evenemang](media/saas-multitenantdb-provision-and-catalog/bushwillow.png)

#### <a name="debugger-steps"></a>Felsökning av steg

För att förstå hur Wingtip-programmet implementerar etableringen i en delad databas av nya klienter, lägger du till en brytpunkt och stegar dig igenom arbetsflödet:

1. I den *PowerShell ISE*öppnar... \\Inlärningsmoduler\\ProvisionTenants\\*Demo-ProvisionTenants.ps1* och ange följande parametrar:
   - **$TenantName** = **Bushwillow Blues**, namnet på en ny plats.
   - **$VenueType** = **blues**, en av de fördefinierade platstyperna: blues, classicalmusic, dance, jazz, judo, motorracing, multipurpose, opera, rockmusic, soccer (gemener, utan blanksteg).
   - **$DemoScenario** = **1**, för att etablera en klient i en delad databas med andra klienter.

2. Lägg till en brytpunkt genom att placera markören var som helst på rad 38, raden: *New-Tenant ”*, och tryck sedan på **F9**.

   ![brytpunkt](media/saas-multitenantdb-provision-and-catalog/breakpoint.png)

3. Kör skriptet genom att trycka på **F5**.

4. När skriptkörningen stannar vid brytpunkten, trycker du **F11** till steg i koden.

   ![felsök](media/saas-multitenantdb-provision-and-catalog/debug.png)

5. Spåra de skriptkörningen med hjälp av den **felsöka** menyalternativen **F10** och **F11**, för att stega över eller in anropade funktionerna.

Mer information om hur du felsöker PowerShell-skript finns i [Tips om att arbeta med och felsöka PowerShell-skript](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise).

## <a name="provision-a-tenant-in-its-own-database"></a>Etablera en klient i dess *egna* databas

#### <a name="major-actions-of-provisioning"></a>Viktiga åtgärder för att etablera

Följande är de viktigaste elementen i arbetsflödet som du går igenom vid spårning av skriptet:

- **Beräkna den nya klientnyckeln**: hash-funktion som används för att skapa klientnyckeln från klientnamnet.
- **Kontrollera om klientnyckeln redan finns**: katalogen kontrolleras för att se till att nyckeln inte redan har registrerats.
- **Skapa en ny klientdatabas**: databasen har skapats genom att kopiera den *basetenantdb* databasen med en Resource Manager-mall.  Det nya databasnamnet baseras på klientens namn.
- **Lägga till databasen i katalogen**: nya klientdatabasen registreras som ett fragment i katalogen.
- **Initiera klienten i standard klientdatabasen**: klientdatabasen har uppdaterats för att lägga till nya klientinformation.  
- **Registrera klient i katalogen**: mappningen mellan den nya klientnyckeln och *sequoiasoccer* databas läggs till i katalogen.
- **Klientnamn har lagts till i katalogen**: namnet på platsen har lagts till i tabellen klienter tillägget i katalogen.
- **Öppna sidan för evenemang för den nya innehavaren**: den *Sequoia fotboll* händelser sidan öppnas i webbläsaren.

   ![evenemang](media/saas-multitenantdb-provision-and-catalog/sequoiasoccer.png)

#### <a name="debugger-steps"></a>Felsökning av steg

Gå igenom skriptprocessen när du skapar en klient i en egen databas:

1. Fortfarande är i... \\Inlärningsmoduler\\ProvisionTenants\\*Demo-ProvisionTenants.ps1* ange följande parametrar:
   - **$TenantName** = **sequoia fotboll**, namnet på en ny plats.
   - **$VenueType** = **fotboll**, en av de fördefinierade platstyperna: blues, classicalmusic, dance, jazz, judo, motorracing, multipurpose, opera, rockmusic, soccer (gemener, utan blanksteg).
   - **$DemoScenario** = **2**, för att etablera en klient i en egen databas.

2. Lägg till en ny brytpunkt genom att placera markören var som helst på rad 57, raden:  *& &nbsp;$PSScriptRoot\New-TenantAndDatabase '*, och tryck på **F9**.

   ![brytpunkt](media/saas-multitenantdb-provision-and-catalog/breakpoint2.png)

3. Kör skriptet genom att trycka på **F5**.

4. När körningen av skriptet stannar vid brytpunkten, trycker du **F11** till steg i koden.  Använd **F10** och **F11** att stega över och gå till funktioner för att spåra körningen.

## <a name="provision-a-batch-of-tenants"></a>Etablera en batch med klienter

Den här övningen etablerar en batch med 17 klienter. Vi rekommenderar att du etablerar den här batchen med klienter innan du startar andra Wingtip biljetter självstudier så att det finns fler databaser du arbetar med.

1. I den *PowerShell ISE*öppnar... \\Inlärningsmoduler\\ProvisionTenants\\*Demo-ProvisionTenants.ps1* och ändra den *$DemoScenario* parametern till 4:
   - **$DemoScenario** = **4**, för att etablera en batch med klienter i en delad databas.

2. Tryck på **F5** och kör skriptet.

### <a name="verify-the-deployed-set-of-tenants"></a>Kontrollera den distribuerade uppsättningen klienter 

I det här skedet kan du ha en blandning av klienter som distribueras till en delad databas och klienter som distribueras till sina egna databaser. Azure-portalen kan användas för att granska de databaser som har skapats. I den [Azure-portalen](https://portal.azure.com)öppnar den **tenants1-mt -\<användaren\>**  servern genom att bläddra i listan över SQL-servrar.  Den **SQL-databaser** listan ska innehålla den delade **tenants1** databasen och databaser för klienter som finns i sin egen databas:

   ![databaslista](media/saas-multitenantdb-provision-and-catalog/Databases.png)

Medan Azure-portalen visar klienten databaser, kan inte se hyresgäster *inuti* den delade databasen. En fullständig lista över klienter kan ses i den **Evenemangshubben** webbsidan Wingtip och genom att bläddra i katalogen.

#### <a name="using-wingtip-tickets-events-hub-page"></a>Med hjälp av Wingtip biljetter händelser-hubbsida

Öppna sidan Evenemangshubben i webbläsaren (http:events.wingtip-mt.\<användaren\>. trafficmanager.net)  

#### <a name="using-catalog-database"></a>Med hjälp av katalogdatabasen

En fullständig lista över klienter och motsvarande databas för var och en finns i katalogen. En SQL-vy är förutsatt att kopplingar klienten namn namnet på databasen. Vyn visar ett snyggt sätt värdet för att utöka de metadata som lagras i katalogen.
- SQL-vyn finns i tenantcatalog-databasen.
- Innehavarens namn lagras i tabellen klienter.
- Namnet på databasen lagras i Fragmenthanterings tabeller.

1. I SQL Server Management Studio (SSMS), ansluta till servern för klienter på **catalog mt.\<användaren\>. database.windows.net**, med inloggningen = **developer**, och lösenord = **P@ssword1**

    ![SSMS anslutningsdialogrutan](media/saas-multitenantdb-provision-and-catalog/SSMSConnection.png)

2. I SSMS Object Explorer bläddrar du till vyer i den *tenantcatalog* databas.

3. Högerklicka på vyn *TenantsExtended* och välj **Välj de 1000 översta raderna**. Observera mappningen mellan klientnamn och databasen för olika klienter.

    ![ExtendedTenants vyn i SSMS](media/saas-multitenantdb-provision-and-catalog/extendedtenantsview.png)
      
## <a name="other-provisioning-patterns"></a>Andra etableringsmönster

Det här avsnittet beskrivs andra intressanta etableringsmönster.

#### <a name="pre-provisioning-databases-in-elastic-pools"></a>Företablering av databaser i elastiska pooler

Företablering mönstret utnyttjar faktumet att när du använder elastiska pooler, fakturering för poolen inte är databaserna. Därför kan databaser läggas till en elastisk pool innan de behövs utan att fakturera extra kostnader. Den här före visioning avsevärt minskar den tid det tar att etablera en klient till en databas. Antalet företablerade databaser kan justeras efter behov för att ha en buffert som är lämpliga för den förväntade etableringstakten.

#### <a name="auto-provisioning"></a>Automatisk etablering

I mönstret för automatisk etablering används en dedikerad etableringstjänst för att etablera servrar, pooler och databaser automatiskt efter behov. Det här automation innehåller företablering av databaser i elastiska pooler. Och om databaser inaktiveras och tas bort, luckor som skapas i elastiska pooler kan fyllas av etableringstjänsten efter behov.

Den här typen av automatiserad tjänst kan vara enkla eller komplexa. Automation kunde hantera etablering över flera geografier och konfigurera geo-replikering för haveriberedskap. Ett program eller skript skulle skicka en etableringsbegäran till en kö som ska bearbetas av en etableringstjänst med mönstret Automatisk etablering. Skriptet frågar sedan för att identifiera slutförande. Om företablering används, hanteras begäranden snabbt, medan en bakgrundstjänst skulle hantera etablering av en ersättningsdatabas.

## <a name="additional-resources"></a>Ytterligare resurser

<!-- - Additional [tutorials that build upon the Wingtip SaaS application](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
- [Klientbibliotek för elastiska databaser](sql-database-elastic-database-client-library.md)
- [Felsök skript i Windows PowerShell ISE](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)


## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Etablera en enskild ny klient i en delad databas för flera innehavare och en egen databas
> * Etablera en batch med ytterligare klienter
> * Gå igenom informationen om etablerar klienter och registrerar dem i katalogen

Prova den [guide för prestandaövervakning](saas-multitenantdb-performance-monitoring.md).

