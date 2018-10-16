---
title: Lösa T-SQL skillnader från migrering Azure SQL Database | Microsoft Docs
description: Transact-SQL-uttryck som inte stöds helt i Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 10/15/2018
ms.openlocfilehash: 89466d8774698028c8574e90f5a58e1678c9b938
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2018
ms.locfileid: "49343562"
---
# <a name="resolving-transact-sql-differences-during-migration-to-sql-database"></a>Lösa Transact-SQL skillnader vid migrering till SQL-databas

När [migrera din databas](sql-database-cloud-migrate.md) från SQL Server till Azure SQL Server, kanske du upptäcker att databasen kräver viss omkonstruktion innan SQL Server kan migreras. Den här artikeln innehåller riktlinjer som hjälper dig att utföra den här bakåtkompilering såväl förstå de underliggande orsaker till varför den bakåtkompilering krävs. För att identifiera inkompatibiliteter, använda den [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595).

## <a name="overview"></a>Översikt

De flesta Transact-SQL-funktioner som använder program stöds helt i både Microsoft SQL Server och Azure SQL Database. Till exempel fungerar SQL-kärnkomponenter, till exempel datatyper, operatörer, sträng, aritmetiska, logiska och markören functions identiskt i SQL Server och SQL-databas. Det finns dock några T-SQL skillnader i DDL (data definition language) och DML (data manipulation language) element, vilket resulterar i T-SQL-uttryck och frågor som stöds endast delvis (vilket beskrivs senare i den här artikeln).

Dessutom finns vissa funktioner och syntax som inte stöds på alla eftersom Azure SQL Database har utformats för att isolera funktioner från beroenden på huvuddatabasen och operativsystemet. Därför är de flesta aktiviteter på servernivå olämpliga för SQL Database. T-SQL-uttryck och alternativ är inte tillgängliga om de konfigurerar servernivåalternativ, operativsystemets komponenter eller anger systemkonfigurationen för filen. När sådana funktioner krävs ett lämpligt alternativ ofta är tillgänglig på annat sätt från SQL Database eller från en annan Azure-funktion eller -tjänst.

Till exempel hög tillgänglighet är inbyggd i Azure SQL Database med hjälp av teknik som liknar [ständigt aktiverade Tillgänglighetsgrupper](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server). T-SQL-uttryck som rör Tillgänglighetsgrupper stöds inte av SQL Database och vyer för dynamisk hantering som rör alltid på Tillgänglighetsgrupper stöds inte också.

En lista över de funktioner som stöds och som inte stöds av SQL-databas finns i [Azure SQL Database funktionsjämförelse](sql-database-features.md). I listan på den här sidan kompletterar artikeln riktlinjer och funktioner och fokuserar på Transact-SQL-uttryck.

## <a name="transact-sql-syntax-statements-with-partial-differences"></a>Instruktioner för Transact-SQL-syntax med partiella skillnader

Core DDL (data definition language)-instruktionerna är tillgängliga, men vissa DDL-instruktionerna har tillägg rör disk placering och stöds inte funktioner.

- Skapa och ändra databas har över tre dussin alternativ. Instruktionerna är filplacering, FILESTREAM och service broker-alternativ som endast gäller för SQL Server. Detta inte har någon betydelse om du skapar databaser innan du migrerar, men om du migrerar T-SQL-kod som skapar databaser du bör jämföra [Skapa databas (Azure SQL Database)](https://msdn.microsoft.com/library/dn268335.aspx) med SQL Server-syntax på [skapa DATABAS (SQL Server Transact-SQL)](https://msdn.microsoft.com/library/ms176061.aspx) att kontrollera att alla alternativ som du använder stöds. Skapa databas för Azure SQL Database har även tjänstmålet och elastisk skalningsalternativ som endast tillämpas på SQL-databas.
- Skapa och ALTER TABLE-instruktioner har FileTable alternativ som inte kan användas i SQL-databas eftersom FILESTREAM inte stöds.
- Skapa och ändra inloggnings-satser stöds men SQL Database erbjuder inte alla alternativ. Om du vill göra databasen mer portabel uppmuntrar SQL Database använder oberoende databasanvändare i stället för inloggningar när det är möjligt. Mer information finns i [CREATE/ALTER LOGIN](https://msdn.microsoft.com/library/ms189828.aspx) och [styra och bevilja åtkomst till databasen](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins).

## <a name="transact-sql-syntax-not-supported-in-azure-sql-database"></a>Transact-SQL-syntaxen stöds inte i Azure SQL Database

Förutom Transact-SQL-uttryck som rör stöds inte funktioner som beskrivs i [Azure SQL Database funktionsjämförelse](sql-database-features.md), följande uttryck och grupper av uttryck stöds inte. Därför om databasen som ska migreras använder någon av följande funktioner, nytt supporttekniker T-SQL för att ta bort dessa T-SQL-funktioner och -uttryck.

- Sortering av systemobjekt
- Anslutningsrelaterade: slutpunktsrapporter. SQL Database har inte stöd för Windows-autentisering, men stöder den liknande Azure Active Directory-autentiseringen. Vissa typer av autentiseringar kräver den senaste versionen av SSMS. Du hittar mer information i [Ansluta till SQL Database eller SQL Data Warehouse med Azure Active Directory-autentisering](sql-database-aad-authentication.md).
- Frågor mellan databaser med tre eller fyra delnamn. (Skrivskyddade frågor över flera databaser stöds med hjälp av [Elastic Database-fråga](sql-database-elastic-query-overview.md).)
- Länkning av ägarskap mellan databaser, `TRUSTWORTHY`-inställning
- `EXECUTE AS LOGIN` Använd EXECUTE AS USER i stället.
- Kryptering stöds utom för Extensible Key Management
- Eventing: Händelser, händelsemeddelanden, frågemeddelanden
- Filplacering: Syntax som avser databasens filplacering, storlek och databasfiler som hanteras automatiskt av Microsoft Azure.
- Hög tillgänglighet: Syntax som avser hög tillgänglighet, som hanteras av Microsoft Azure-kontot. Detta inkluderar syntax för säkerhetskopiering, återställa, Alltid på, databasspegling, loggöverföring återställningslägen.
- Logga läsare: Syntax som använder log Reader, som inte är tillgänglig på SQL Database: Push-replikering, sammanställning av ändringsdata. SQL Database kan vara en prenumerant för en artikel för push-replikering.
- Funktioner: `fn_get_sql`, `fn_virtualfilestats`,`fn_virtualservernodes`
- Maskinvara: Syntax för maskinvarurelaterade maskinvarurelaterade serverinställningar: till exempel minne, arbetstrådar, processortillhörighet, spåra flaggor. Använd tjänstnivåer och compute storlekar i stället.
- `KILL STATS JOB`
- `OPENQUERY`, `OPENROWSET`, `OPENDATASOURCE`, och fyrdelade namn
- .NET framework: CLR-integrering med SQL Server
- Semantisk sökning
- Serverautentiseringsuppgifter: Använd [databasbegränsade autentiseringsuppgifter](https://msdn.microsoft.com/library/mt270260.aspx) i stället.
- Servernivåobjekt: serverroller, `sys.login_token`. `GRANT`, `REVOKE` och `DENY` av serverbehörigheter inte är tillgängliga även om vissa ersätts med databasbehörigheter. Vissa användbara DMV:er på servernivå har motsvarande DMV:er på databasnivå.
- `SET REMOTE_PROC_TRANSACTIONS`
- `SHUTDOWN`
- `sp_addmessage`
- `sp_configure`-alternativ och `RECONFIGURE`. Vissa alternativ är tillgängliga med [ALTER DATABASE SCOPED CONFIGURATION](https://msdn.microsoft.com/library/mt629158.aspx).
- `sp_helpuser`
- `sp_migrate_user_to_contained`
- SQL ServerAgent: Syntax som bygger på SQL Server Agent eller MSDB-databasen: aviseringar, operatörer, centrala hanteringsservrar. Använd skript, till exempel Azure PowerShell i stället.
- Granskning av SQL Server: Använd SQL Database-granskning i stället.
- Spårning av SQL Server
- Spåra flaggor: vissa spårade flaggobjekt har flyttats till kompatibilitetsläge.
- Felsökning av Transact-SQL
- Utlösare: Serveromfattande eller inloggningsutlösare
- `USE`-uttryck: Om du vill ändra databasens kontext till en annan databas måste du göra en ny anslutning till den nya databasen.

## <a name="full-transact-sql-reference"></a>Fullständig referens för Transact-SQL

Mer information om Transact-SQL-grammatik, -användning och -exempel finns i [Transact-SQL Reference (Database Engine)](https://msdn.microsoft.com/library/bb510741.aspx) i SQL Server Books Online.

### <a name="about-the-applies-to-tags"></a>Om "Gäller för"-taggar

Transact-SQL-referensen innehåller artiklar som rör SQL Server-versioner 2008 aktuella. Under artikelrubriken det är en ikon, visa en lista över de fyra SQL Server-plattformarna och som anger tillämplighet. Till exempel introducerades tillgänglighetsgrupper i SQL Server 2012. Den [skapa TILLGÄNGLIGHETSGRUPP](https://msdn.microsoft.com/library/ff878399.aspx) artikeln anger att instruktionen gäller **SQL Server (startar med 2012)**. Uttrycket gäller inte för SQL Server 2008, SQL Server 2008 R2, Azure SQL Database, Azure SQL Data Warehouse eller Parallel Data Warehouse.

I vissa fall kan det allmänna ämnet för en artikel kan användas i en produkt, men det finns mindre skillnader mellan produkter. Skillnaderna anges vid mittpunkter i artikeln efter behov. I vissa fall kan det allmänna ämnet för en artikel kan användas i en produkt, men det finns mindre skillnader mellan produkter. Skillnaderna anges vid mittpunkter i artikeln efter behov. Till exempel finns CREATE TRIGGER-artikel i SQL-databas. Men **alla SERVER** för servernivå utlösare, anger att servernivå utlösare inte kan användas i SQL-databas. Använd på databasnivå utlösare i stället.

## <a name="next-steps"></a>Nästa steg

En lista över de funktioner som stöds och som inte stöds av SQL-databas finns i [Azure SQL Database funktionsjämförelse](sql-database-features.md). I listan på den här sidan kompletterar artikeln riktlinjer och funktioner och fokuserar på Transact-SQL-uttryck.
