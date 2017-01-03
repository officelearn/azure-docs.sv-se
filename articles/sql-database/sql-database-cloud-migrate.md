---
title: Migrering av SQL Server-databas till SQL Database | Microsoft Docs
description: "Lär dig lokal migrering av SQL Server-databas till Azure SQL Database i molnet. Använda databasmigreringsverktyg för att testa kompatibilitet före databasmigrering."
keywords: databasmigrering, sql server-databasmigrering, databasmigreringsverktyg, migrera databas, migrera sql-databas
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 9cf09000-87fc-4589-8543-a89175151bc2
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 11/08/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: d9052bd10693c0b7942c0d90fdf89be37b44842d
ms.openlocfilehash: b0093e48266aedda2b6c88b862c0056ebe3b3114


---
# <a name="sql-server-database-migration-to-sql-database-in-the-cloud"></a>Migrering av SQL Server-databas till SQL Database i molnet
I den här artikeln du lär dig hur du migrerar en lokal SQL Server 2005-databas eller senare till Azure SQL Database. I den här databasmigreringsprocessen kan du migrera ditt schema och dina data från SQL Server-databasen i din nuvarande miljö till SQL Database. För att det ska lyckas måste den befintliga databasen först klara ett kompatibilitetstest. Med SQL Database V12 var det [funktionsparitet](sql-database-features.md)som närmade sig, andra problem relaterade till servernivå och åtgärder över flera databaser. Databaser och program som förlitar sig på [funktioner som delvis eller inte stöds](sql-database-transact-sql-information.md) behöver viss omkonstruktion för att åtgärda dessa inkompatibiliteter innan SQL Server-databasen kan migreras.

Följ dessa steg för att migrera:

* **Testa för kompatibilitet**: Validera databasens kompatibilitet med SQL Database. 
* **Åtgärda kompatibilitetsproblem, om det finns några**: Om valideringen misslyckas måste du åtgärda valideringsfelen.  
* **Utför migreringen**: När databasen är kompatibel kan du använda en eller flera metoder för att utföra migreringen. 

SQL Server erbjuder flera metoder för att åstadkomma dessa aktiviteter. Den här artikeln innehåller en översikt över tillgängliga metoder för varje aktivitet. Följande diagram illustrerar stegen och metoderna.

  ![VSSSDT-migreringsdiagram](./media/sql-database-cloud-migrate/03VSSSDTDiagram.png)

> [!NOTE]
> För att migrera en icke-SQL Server-databas, inklusive Microsoft Access, Sybase, MySQL Oracle och DB2 till Azure SQL Database, se [SQL Server-migreringsassistent](http://blogs.msdn.com/b/ssma/).
> 
> 

## <a name="database-migration-tools-test-sql-server-database-compatibility-with-sql-database"></a>Databasmigreringsverktyg testar SQL Server-databasens kompatibilitet med SQL Database
Använd någon av följande metoder för att testa kompatibilitetsproblem med SQL Database innan du påbörjar migreringen:

> [!div class="op_single_selector"]
> * [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
> * [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
> * [SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md)
> * [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)
> 
> 

* [SQL Server Data Tools for Visual Studio ("SSDT")](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md): SSDT använder de senaste kompatibilitetsreglerna för att identifiera inkompatibiliteter med SQL Database V12. Om inkompatibiliteter identifieras kan du kan åtgärda problemen direkt i det här verktyget. Den här metoden är den som rekommenderas för att testa och lösa kompatibilitetsproblem med SQL Database V12. 
* [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md): SqlPackage är ett kommandoradsverktyg som testar för kompatibilitetsproblem och genererar en rapport som innehåller identifierade problem. Om du använder det här verktyget ska du kontrollera att du använder den senaste versionen, så att du använder de senaste kompatibilitetsregler. Om fel identifieras måste du använda ett annat verktyg för att åtgärda alla identifierade kompatibilitetsproblem – vi rekommenderar SSDT.  
* [Guiden Export Data Tier Application (Exportera datanivåprogram) i SQL Server Management Studio.](sql-database-cloud-migrate-determine-compatibility-ssms.md): Den här guiden identifierar och rapporterar fel på skärmen. Om inga fel rapporteras kan du fortsätta och slutföra migreringen till SQL Database. Om fel identifieras måste du använda ett annat verktyg för att åtgärda alla identifierade kompatibilitetsproblem – vi rekommenderar SSDT.
* [SQL Azure Migration Wizard ("SAMW")](sql-database-cloud-migrate-fix-compatibility-issues.md): SAMW är ett CodePlex-verktyg som använder Azure SQL Database V11-kompatibilitetsregler för att identifiera Azure SQL Database V12-inkompatibiliteter. Om inkompatibiliteter identifieras kan vissa problem åtgärdas direkt i det här verktyget. Verktyget kan hitta inkompatibiliteter som inte behöver åtgärdas. Det var det första migreringsverktyget för Azure SQL Database som fanns tillgängligt och stöds aktivt av SQL Server-communityn. Det här verktyget kan dessutom slutföra migreringen inifrån själva verktyget. 

## <a name="fix-database-migration-compatibility-issues"></a>Åtgärda kompatibilitetsproblem vid databasmigrering
Om kompatibilitetsproblem identifieras måste du åtgärda dem innan du går vidare med migreringen av SQL Server-databasen. Det finns en mängd olika kompatibilitetsproblem som kan uppstå, beroende på både versionen av SQL Server i källdatabasen och komplexiteten i databasen som du migrerar. Äldre versioner av SQL Server har fler kompatibilitetsproblem. Använd följande resurser, utöver en riktad Internetsökning med hjälp av sökmotor:

* [SQL Server-databasfunktioner som inte stöds i Azure SQL Database](sql-database-transact-sql-information.md)
* [Utgångna databasmotorfunktioner i SQL Server 2016](https://msdn.microsoft.com/library/ms144262%28v=sql.130%29)
* [Utgångna databasmotorfunktioner i SQL Server 2014](https://msdn.microsoft.com/library/ms144262%28v=sql.120%29)
* [Utgångna databasmotorfunktioner i SQL Server 2012](https://msdn.microsoft.com/library/ms144262%28v=sql.110%29)
* [Utgångna databasmotorfunktioner i SQL Server 2008 R2](https://msdn.microsoft.com/library/ms144262%28v=sql.105%29)
* [Utgångna databasmotorfunktioner i SQL Server 2005](https://msdn.microsoft.com/library/ms144262%28v=sql.90%29)

Utöver att söka på Internet och använda dessa resurser kan du använda [MSDN SQL Server community-forumen](https://social.msdn.microsoft.com/Forums/sqlserver/home?category=sqlserver) eller [StackOverflow](http://stackoverflow.com/).

Använd något av följande databasmigreringsverktyg för att åtgärda problem som identifieras:

> [!div class="op_single_selector"]
> * [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
> * [SSMS](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)
> * [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)
> 
> 

* Använda [SQL Server Data Tools for Visual Studio ("SSDT")](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md): Om du vill använda SSDT måste du importera ditt databasschema till SQL Server Data Tools for Visual Studio ("SSDT") och bygga projektet för en SQL Database V12-distribution. Därefter åtgärdar du alla identifierade kompatibilitetsproblem i SSDT. När du är klar synkroniserar du ändringarna tillbaka till källdatabasen (eller en kopia av källdatabasen). SSDT är för närvarande den metod som rekommenderas för att testa och lösa kompatibilitetsproblem med SQL Database V12. Följ länken för en [genomgång av SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md).
* Använda [SQL Server Management Studio (”SSMS”)](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md): Om du vill använda SSMS kör du Transact-SQL-kommandon för att korrigera de fel som identifierats med hjälp av ett annat verktyg. Den här metoden är främst för avancerade användare, där de kan ändra databasschemat direkt i källdatabasen. 
* Använda [SQL Azure Migration Wizard ("SAMW")](sql-database-cloud-migrate-fix-compatibility-issues.md): Om du vill använda SAMW skapar du ett Transact-SQL-skript från källdatabasen. Guiden transformerar skriptet, där detta är möjligt, för att göra schemat kompatibelt med SQL Database V12. När det är klart kan SAMW ansluta till SQL Database V12 för att köra skriptet. Det här verktyget analyserar även spårningsfiler för att fastställa kompatibilitetsproblem. Skriptet kan genereras med endast schema, eller kan innehålla data i BCP-format.

## <a name="migrate-a-compatible-sql-server-database-to-sql-database"></a>Migrera en kompatibel SQL Server-databas till Azure SQL Database
Microsoft tillhandahåller flera migreringsmetoder för olika scenarier vid migrering av en kompatibel SQL Server-databas. Den metod du väljer beror på toleransen för avbrott, din SQL Server-databas storlek och komplexitet samt anslutningen till Microsoft Azure-molnet.  

> [!div class="op_single_selector"]
> * [SSMS migreringsguide](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
> * [Export till BACPAC-fil](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
> * [Import från BACPAC-fil](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
> * [Transaktionsreplikering](sql-database-cloud-migrate-compatible-using-transactional-replication.md)
> 
> 

Vid valet av migreringsmetod är den första frågan om du har råd att databasen står utanför produktion under migreringen. Migrering av en databas där aktiva transaktioner sker kan leda till inkonsekvenser i databasen och den kan skadas. Det finns många metoder för att inaktivera en databas, från att inaktivera klientanslutningarna till att skapa en [ögonblicksbild av databasen](https://msdn.microsoft.com/library/ms175876.aspx).

Om du vill migrera med minimal avbrottstid ska du använda [SQL Server transaktionsreplikering](sql-database-cloud-migrate-compatible-using-transactional-replication.md), om din databas uppfyller kraven för transaktionsreplikering. Om du har råd med lite avbrott eller om du utför en testmigrering av en produktionsdatabas för senare migrering, pröva någon av följande tre metoder:

* [SSMS migreringsguide](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md): För små till medelstora databaser. Migrering av en kompatibel SQL Server 2005-databas eller senare är lika enkelt som att köra [guiden Deploy Database to Microsoft Azure Database](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md) i SQL Server Management Studio.
* [Exportera till BACPAC-fil](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md) och därefter [importera från BACPAC-fil](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md): Om du har anslutningsproblem (ingen anslutning, låg bandbredd eller timeout-problem) och det gäller medelstora till stora databaser, använd en [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4)-fil. Med den här metoden exporterar du SQL Server-schemat och data till en BACPAC-fil. Därefter importerar du BACPAC-filen till SQL Database med hjälp av Export Data Tier Application Wizard i SQL Server Management Studio eller [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx)-kommandotolkverktyget.
* Använda BACPAC och BCP tillsammans: Använd en [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4)-fil och [BCP](https://msdn.microsoft.com/library/ms162802.aspx) för mycket större databaser för att uppnå större parallellisering för ökar prestanda, men med ökad komplexitet. Med den här metoden migreras schemat och data separat.
  
  * [Exportera endast schemat till en BACPAC-fil](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md).
  * [Importera endast schemat från BACPAC-filen](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md) till SQL Database.
  * Använd [BCP](https://msdn.microsoft.com/library/ms162802.aspx) för att extrahera data till flat-filer och sedan [parallellinläsa](https://technet.microsoft.com/library/dd425070.aspx) filerna i Azure SQL Database.
    
     ![SQL Server databasmigrering – migrera SQL Database till molnet.](./media/sql-database-cloud-migrate/01SSMSDiagram_new.png)

## <a name="next-steps"></a>Nästa steg
* [Nyaste versionen av SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
* [Nyaste versionen av SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)

## <a name="additional-resources"></a>Ytterligare resurser
* [SQL Database-funktioner](sql-database-features.md)
  [Transact-SQL funktioner som delvis eller inte stöds](sql-database-transact-sql-information.md)
* [Migrera icke-SQL Server-databaser med hjälp av SQL Server Migration Assistant](http://blogs.msdn.com/b/ssma/)




<!--HONumber=Jan17_HO1-->


