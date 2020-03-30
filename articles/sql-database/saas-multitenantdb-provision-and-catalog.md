---
title: Etablera i SaaS flera innehavare
description: Lär dig hur du etablerar och katalogiserar nya klienter i en Azure SQL Database-saaS-app med flera innehavare
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: billgib,andrela,stein
ms.date: 09/24/2018
ms.openlocfilehash: 4ea18ee23d845b2d16209b23de14dc3cd70aaa59
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74133143"
---
# <a name="provision-and-catalog-new-tenants-in-a-saas-application-using-a-sharded-multi-tenant-azure-sql-database"></a>Etablera och katalogisera nya klienter i ett SaaS-program med en fragmenterad Azure SQL-databas med flera innehavare

Den här artikeln beskriver etablering och katalogisering av nya klienter, i en *sharded databasmodell* eller mönster med flera innehavare.

Denna artikel har två huvuddelar:

- [Begreppsmässig diskussion om](#goto_2_conceptual) etablering och katalogisering av nya klienter.

- [Självstudiekurs](#goto_1_tutorial) som belyser PowerShell-skriptkoden som åstadkommer etableringen och katalogiseringen.
  - Självstudien använder Wingtip Tickets SaaS-programmet, anpassat till databasmönstret för flera innehavare.

<a name="goto_2_conceptual"/>

## <a name="database-pattern"></a>Databasmönster

Det här avsnittet, plus några fler som följer, diskuterar begreppen i det fragmenterade databasmönstret med flera innehavare.

I den här fragmenterade modellen med flera innehavare innehåller tabellschemana i varje databas en klientnyckel i primärnyckeln för tabeller som lagrar klientdata. Klientnyckeln gör det möjligt för varje enskild databas att lagra 0, 1 eller många klienter. Användningen av fragmenterade databaser gör det enkelt för programsystemet att stödja ett mycket stort antal klienter. Alla data för en klient lagras i en databas. Det stora antalet klienter distribueras över de många fragmenterade databaserna. En katalogdatabas lagrar mappningen av varje klient till sin databas.

#### <a name="isolation-versus-lower-cost"></a>Isolering kontra lägre kostnad

En klient som har en databas för sig själv har fördelarna med isolering. Klienten kan få databasen återställd till ett tidigare datum utan att begränsas av påverkan på andra klienter. Databasprestanda kan justeras för att optimera för en klient, igen utan att behöva kompromissa med andra klienter. Problemet är att isolering kostar mer än det kostar att dela en databas med andra klienter.

När en ny klient etableras kan den dela en databas med andra klienter eller placeras i en egen ny databas. Senare kan du ändra dig och flytta databasen till den andra situationen.

Databaser med flera klienter och enskilda klienter blandas i samma SaaS-program för att optimera kostnad eller isolering för varje klient.

   ![Fragmenterad databasapp för flera innehavare med klientkatalog](media/saas-multitenantdb-provision-and-catalog/MultiTenantCatalog.png)

## <a name="tenant-catalog-pattern"></a>Mönster för klientkatalog

När du har två eller flera databaser som var och en innehåller minst en klient måste programmet ha ett sätt att identifiera vilken databas som lagrar klienten av aktuellt intresse. En katalogdatabas lagrar den här mappningen.

#### <a name="tenant-key"></a>Klientnyckel

För varje klient kan Wingtip-programmet härleda en unik nyckel, som är klientnyckeln. Appen extraherar klientnamnet från webbadressen till webbsidan. Appen hashar namnet för att hämta nyckeln. Appen använder nyckeln för att komma åt katalogen. Katalogen korsrefererar information om databasen där klienten lagras. Appen använder databasinformationen för att ansluta. Andra klientnyckelscheman kan också användas.

Med hjälp av en katalog kan namnet eller platsen för en klientdatabas ändras efter etablering utan att störa programmet. I en databasmodell med flera innehavare kan katalogen flytta en klient mellan databaser.

#### <a name="tenant-metadata-beyond-location"></a>Klientmetadata utanför platsen

Katalogen kan också ange om en klient är offline för underhåll eller andra åtgärder. Och katalogen kan utökas för att lagra ytterligare klient- eller databasmetadata, till exempel följande objekt:
- Tjänstnivån eller upplagan av en databas.
- Versionen av databasschemat.
- Klientnamnet och dess serviceavtal (servicenivåavtal).
- Information för att möjliggöra programhantering, kundsupport eller devops-processer.

Katalogen kan också användas för att aktivera rapportering mellan innehavare, schemahantering och dataextrahera för analysändamål.

### <a name="elastic-database-client-library"></a>Klientbibliotek för elastisk databas

I Wingtip implementeras katalogen i *klientkategoridatabasen.* *Klientkatalogen* skapas med hjälp av shardhanteringsfunktionerna i [EDCL (Elastic Database Client Library).](sql-database-elastic-database-client-library.md) Biblioteket gör det möjligt för ett program att skapa, hantera och använda en *fragmentkarta* som lagras i en databas. En fragmentkarta korsreferenser klientnyckeln med dess fragment, vilket innebär dess fragmenterade databas.

Under klientetablering kan EDCL-funktioner användas från program eller PowerShell-skript för att skapa posterna i fragmentkartan. Senare kan EDCL-funktionerna användas för att ansluta till rätt databas. EDCL cachelagrar anslutningsinformation för att minimera trafiken i katalogdatabasen och påskynda anslutningsprocessen.

> [!IMPORTANT]
> Redigera *inte* data i katalogdatabasen via direkt åtkomst! Direkta uppdateringar stöds inte på grund av den höga risken för dataskador. Redigera i stället mappningsdata med hjälp av EDCL API:er.

## <a name="tenant-provisioning-pattern"></a>Etableringsmönster för klient

#### <a name="checklist"></a>Checklista

När du vill etablera en ny klient i en befintlig delad databas måste du av den delade databasen ställa följande frågor:
- Har den tillräckligt med utrymme kvar för den nya hyresgästen?
- Har den tabeller med nödvändiga referensdata för den nya klienten eller kan data läggas till?
- Har den rätt variant av basschemat för den nya klienten?
- Är det på lämplig geografisk plats nära den nya klienten?
- Är det på rätt servicenivå för den nya klienten?

När du vill att den nya klienten ska isoleras i sin egen databas kan du skapa den för att uppfylla specifikationerna för klienten.

När etableringen är klar måste du registrera klienten i katalogen. Slutligen kan klientmappningen läggas till för att referera till rätt shard.

#### <a name="template-database"></a>Malldatabas

Etablera databasen genom att köra SQL-skript, distribuera en bacpac eller kopiera en malldatabas. Wingtip-apparna kopierar en malldatabas för att skapa nya klientdatabaser.

Precis som alla program kommer Wingtip att utvecklas med tiden. Ibland kommer Wingtip kräver ändringar i databasen. Ändringar kan omfatta följande punkter:
- Nytt eller ändrat schema.
- Nya eller ändrade referensdata.
- Rutinmässiga databasunderhållsuppgifter för att säkerställa optimal appprestanda.

Med ett SaaS-program, behöver de här ändringarna distribueras på ett samordnat sätt över en potentiellt massiv mängd klientdatabaser. För att dessa ändringar ska vara i framtida klientdatabaser måste de införlivas i etableringsprocessen. Den här utmaningen utforskas ytterligare i [schemahanteringshandledningen](saas-tenancy-schema-management.md).

#### <a name="scripts"></a>Skript

Klientens etableringsskript i den här självstudien stöder båda följande scenarier:
- Etablera en klient i en befintlig databas som delas med andra klienter.
- Etablera en klient i sin egen databas.

Klientdata initieras sedan och registreras i katalogshard-kartan. I exempelappen får databaser som innehåller flera klienter ett allmänt namn, till exempel *tenants1* eller *tenants2*. Databaser som innehåller en enda klient får klientens namn. De specifika namngivningskonventioner som används i exemplet är inte en viktig del av mönstret, eftersom användningen av en katalog gör att alla namn kan tilldelas databasen.

<a name="goto_1_tutorial"/>

## <a name="tutorial-begins"></a>Handledningen börjar

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Etablera en klient i en databas med flera innehavare
> * Etablera en klient i en databas med en enda klient
> * Etablera en batch med klienter i databaser med både flera innehavare och en enda klient
> * Registrera en databas- och klientmappning i en katalog

#### <a name="prerequisites"></a>Krav

Följande krav måste uppfyllas för att kunna köra den här självstudiekursen:

- Azure PowerShell ska ha installerats. Mer information finns i [Kom igång med Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

- Wingtip-biljetterna SaaS Databasapp för flera innehavare distribueras. Information om hur du distribuerar på mindre än fem minuter finns i [Distribuera och utforska Wingtip Tickets SaaS-databasprogrammet för flera innehavare](saas-multitenantdb-get-started-deploy.md)

- Hämta Wingtip-skript och källkod:
    - Wingtip-biljetterna SaaS-databasskript för flera innehavare och programkällakod finns i [WingtipTicketsSAaS-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) GitHub-repo.
    - Se den [allmänna vägledningen](saas-tenancy-wingtip-app-guidance-tips.md) för steg för att hämta och låsa upp Wingtip-skripten.

## <a name="provision-a-tenant-into-a-database-shared-with-other-tenants"></a>Etablera en klient i en databas *som delas* med andra klienter

I det här avsnittet visas en lista över de viktigaste åtgärderna för etablering som vidtas av PowerShell-skripten. Sedan använder du PowerShell ISE-felsökningen för att gå igenom skripten för att se åtgärderna i koden.

#### <a name="major-actions-of-provisioning"></a>Större åtgärder för avsättning

Följande är viktiga delar i det etableringsarbetsflöde som du går igenom:

- **Beräkna den nya klientnyckeln**: En hash-funktion används för att skapa klientnyckeln från klientnamnet.
- **Kontrollera om klientnyckeln redan finns**: Katalogen är markerad för att säkerställa att nyckeln inte redan har registrerats.
- **Initiera klienten i standardklientdatabasen**: Klientdatabasen uppdateras för att lägga till den nya klientinformationen.
- **Registrera klienten i katalogen**: Mappningen mellan den nya klientnyckeln och den befintliga klienten1-databasen läggs till i katalogen.
- **Lägg till klientens namn i en katalogtilläggstabell:** Platsnamnet läggs till i tabellen Klienter i katalogen.  Det här tillägget visar hur katalogdatabasen kan utökas för att stödja ytterligare programspecifika data.
- **Öppna evenemang sida för den nya hyresgästen:** The *Bushwillow Blues* händelser sidan öppnas i webbläsaren.

   ![händelser](media/saas-multitenantdb-provision-and-catalog/bushwillow.png)

#### <a name="debugger-steps"></a>Felsökningssteg

Om du vill förstå hur Wingtip-appen implementerar ny klientetablering i en delad databas lägger du till en brytpunkt och går igenom arbetsflödet:

1. I *PowerShell ISE*, öppna ... \\Utbildningsmoduler\\ProvisionTenants\\*Demo-ProvisionTenants.ps1* och ange följande parametrar:
   - **$TenantName****Bushwillow Blues**, namnet på en ny plats. = 
   - **$VenueType**blues , en av de fördefinierade plats typer: blues, klassiskmusik, dans, jazz, judo, motorracing, mångsidig, opera, rockmusik, fotboll (gemener, inga utrymmen).**blues** = 
   - **$DemoScenario**1 för att etablera en klient i en delad databas med andra klienter.**1** = 

2. Lägg till en brytpunkt genom att sätta markören var som helst på rad 38, raden som säger: *Ny-Klient "* och tryck sedan på **F9**.

   ![brytpunkt](media/saas-multitenantdb-provision-and-catalog/breakpoint.png)

3. Kör skriptet genom att trycka på **F5**.

4. När skriptkörningen stoppas vid brytpunkten trycker du på **F11** för att gå in i koden.

   ![felsökning](media/saas-multitenantdb-provision-and-catalog/debug.png)

5. Spåra skriptets körning med hjälp av debug-menyalternativen **F10** och **Debug** **F11**för att gå över eller till anropade funktioner.

Mer information om felsökning av PowerShell-skript finns i [Tips om hur du arbetar med och felsöker PowerShell-skript](https://docs.microsoft.com/powershell/scripting/components/ise/how-to-debug-scripts-in-windows-powershell-ise).

## <a name="provision-a-tenant-in-its-own-database"></a>Etablera en klient i sin *egen* databas

#### <a name="major-actions-of-provisioning"></a>Större åtgärder för avsättning

Följande är viktiga element i arbetsflödet som du går igenom när du spårar skriptet:

- **Beräkna den nya klientnyckeln**: En hash-funktion används för att skapa klientnyckeln från klientnamnet.
- **Kontrollera om klientnyckeln redan finns**: Katalogen är markerad för att säkerställa att nyckeln inte redan har registrerats.
- **Skapa en ny klientdatabas**: Databasen skapas genom att databasen *kopieras* med hjälp av en Resource Manager-mall.  Det nya databasnamnet baseras på klientens namn.
- **Lägg till databas i katalogen**: Den nya klientdatabasen registreras som en fragment i katalogen.
- **Initiera klienten i standardklientdatabasen**: Klientdatabasen uppdateras för att lägga till den nya klientinformationen.
- **Registrera klienten i katalogen**: Mappningen mellan den nya klientnyckeln och *sequoiasoccer-databasen* läggs till i katalogen.
- **Klientnamn läggs till i katalogen**: Platsnamnet läggs till i tilläggstabellen klienter i katalogen.
- **Öppna evenemangssidan för den nya hyresgästen**: *Sequoia Soccer* Events-sidan öppnas i webbläsaren.

   ![händelser](media/saas-multitenantdb-provision-and-catalog/sequoiasoccer.png)

#### <a name="debugger-steps"></a>Felsökningssteg

Gå nu igenom skriptprocessen när du skapar en klient i sin egen databas:

1. Fortfarande i ... \\Utbildningsmoduler\\ProvisionTenants\\*Demo-ProvisionTenants.ps1* ange följande parametrar:
   - **$TenantName****Sequoia Soccer**, namnet på en ny plats. = 
   - **$VenueType**fotboll , en av de fördefinierade plats typer: blues, klassiskmusik, dans, jazz, judo, motorracing, mångsidig, opera, rockmusik, fotboll (gemener, inga utrymmen).**soccer** = 
   - **$DemoScenario**2 för att etablera en klient i sin egen databas.**2** = 

2. Lägg till en ny brytpunkt genom att sätta markören var som helst på rad 57, raden som säger: * & &nbsp;$PSScriptRoot\New-TenantAndDatabase '* och tryck på **F9**.

   ![brytpunkt](media/saas-multitenantdb-provision-and-catalog/breakpoint2.png)

3. Kör skriptet genom att trycka på **F5**.

4. När skriptkörningen stoppas vid brytpunkten trycker du på **F11** för att gå in i koden.  Använd **F10** och **F11** för att gå över och gå in i funktioner för att spåra körningen.

## <a name="provision-a-batch-of-tenants"></a>Etablera en batch med klienter

Denna övning avsättningar ett parti med 17 hyresgäster. Det rekommenderas att du etablerar den här batchen av klienter innan du startar andra Wingtip Tickets tutorials så det finns fler databaser att arbeta med.

1. I *PowerShell ISE*, öppna ... \\Utbildningsmoduler\\ProvisionTenants\\*Demo-ProvisionTenants.ps1* och ändra *parametern $DemoScenario* till 4:
   - **$DemoScenario**4 för att etablera en batch med klienter i en delad databas.**4** = 

2. Tryck på **F5** och kör skriptet.

### <a name="verify-the-deployed-set-of-tenants"></a>Verifiera den distribuerade uppsättningen klienter

I det här skedet har du en blandning av klienter som distribueras i en delad databas och klienter distribueras i sina egna databaser. Azure-portalen kan användas för att granska de databaser som skapats. Öppna **server för\<klienter1-mt- USER\> i Azure-portalen** genom att bläddra till listan över SQL-servrar. [Azure portal](https://portal.azure.com)  **SQL-databaslistan** bör innehålla den delade **klientdatabasen1** och databaserna för klienterna som finns i deras egen databas:

   ![databaslista](media/saas-multitenantdb-provision-and-catalog/Databases.png)

Azure-portalen visar klientdatabaserna, men du kan inte se klienterna *i* den delade databasen. Den fullständiga listan över klienter kan ses på **webbsidan Events Hub** i Wingtip och genom att bläddra i katalogen.

#### <a name="using-wingtip-tickets-events-hub-page"></a>Använda hubbsida för evenemangshubbsida för Wingtip-biljetter

Öppna sidan Händelser hubb i webbläsaren (http:events.wingtip-mt.\<USER\>.trafficmanager.net)

#### <a name="using-catalog-database"></a>Använda katalogdatabas

Den fullständiga listan över klienter och motsvarande databas för varje finns i katalogen. En SQL-vy tillhandahålls som kopplar klientnamnet till databasnamnet. Vyn visar snyggt värdet av att utöka metadata som lagras i katalogen.
- SQL-vyn är tillgänglig i klientkategoridatabasen.
- Klientnamnet lagras i tabellen Klienter.
- Databasnamnet lagras i tabellerna Shard Management.

1. I SQL Server Management Studio (SSMS) ansluter du till klientservern på **katalog-mt.\<USER\>.database.windows.net**, med Login = **utvecklare**och Lösenord = P **\@ssword1**

    ![Dialogrutan SSMS-anslutning](media/saas-multitenantdb-provision-and-catalog/SSMSConnection.png)

2. Bläddra till vyerna i *klientkategoridatabasen* i SSMS-objektutforskaren.

3. Högerklicka på visa *TenantsExtended* och välj **Välj Topp 1000 Rader**. Observera mappningen mellan klientnamn och databas för de olika klienterna.

    ![ExtendedTenants-vy i SSMS](media/saas-multitenantdb-provision-and-catalog/extendedtenantsview.png)

## <a name="other-provisioning-patterns"></a>Andra etableringsmönster

I det här avsnittet beskrivs andra intressanta etableringsmönster.

#### <a name="pre-provisioning-databases-in-elastic-pools"></a>Företablering av databaser i elastiska pooler

Företableringsmönstret utnyttjar det faktum att fakturering är för poolen inte för poolen när du använder elastiska pooler. Således databaser kan läggas till en elastisk pool innan de behövs utan att ådra sig extra kostnad. Den här förhandsvisionen minskar avsevärt den tid det tar att etablera en klient i en databas. Antalet databaser som företableras kan justeras efter behov för att hålla en buffert som är lämplig för den förväntade etableringsgraden.

#### <a name="auto-provisioning"></a>Automatisk etablering

I mönstret för automatisk etablering används en dedikerad etableringstjänst för att etablera servrar, pooler och databaser automatiskt efter behov. Den här automatiseringen omfattar företablering av databaser i elastiska pooler. Och om databaser inaktiveras och tas bort, de luckor som detta skapar i elastiska pooler kan fyllas av etableringstjänsten som önskas.

Den här typen av automatiserad tjänst kan vara enkel eller komplicerad. Automatiseringen kan till exempel hantera etablering över flera geografiska områden och kan ställa in geo-replikering för haveriberedskap. Med mönstret för automatisk etablering skickar ett klientprogram eller skript en etableringsbegäran till en kö som ska bearbetas av en etableringstjänst. Skriptet skulle sedan avsöka för att upptäcka slutförande. Om företablering används hanteras begäranden snabbt, medan en bakgrundstjänst hanterar etableringen av en ersättningsdatabas.

## <a name="additional-resources"></a>Ytterligare resurser

<!-- - Additional [tutorials that build upon the Wingtip SaaS application](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
- [Klientbibliotek för elastiska databaser](sql-database-elastic-database-client-library.md)
- [Felsök skript i Windows PowerShell ISE](https://docs.microsoft.com/powershell/scripting/components/ise/how-to-debug-scripts-in-windows-powershell-ise)


## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Etablera en enskild klient i en delad databas med flera innehavare och en egen databas
> * Etablera en batch med ytterligare klienter
> * Gå igenom information om att etablera klienter och registrera dem i katalogen

Prova [självstudiekursen För prestandaövervakning](saas-multitenantdb-performance-monitoring.md).

