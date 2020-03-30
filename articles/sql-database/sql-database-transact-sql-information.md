---
title: Lösa T-SQL-differens-migrering
description: Transact-SQL-uttryck som inte stöds helt i Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/03/2018
ms.openlocfilehash: e0870ac9dc818ca07e149421b486136c76dd61a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79208823"
---
# <a name="resolving-transact-sql-differences-during-migration-to-sql-database"></a>Lösa Transact-SQL-skillnader under migreringen till SQL Database

När [du migrerar databasen](sql-database-single-database-migrate.md) från SQL Server till Azure SQL Server kan du upptäcka att databasen kräver viss omkonstruktion innan SQL Server kan migreras. Den här artikeln innehåller vägledning för att hjälpa dig att både utföra denna omkonstruktion och förstå de bakomliggande orsakerna till att omkonstruktion är nödvändigt. Om du vill identifiera inkompatibiliteter använder du [DMA (Data Migration Assistant).](https://www.microsoft.com/download/details.aspx?id=53595)

## <a name="overview"></a>Översikt

De flesta Transact-SQL-funktioner som program använder stöds fullt ut i både Microsoft SQL Server och Azure SQL Database. Till exempel fungerar de centrala SQL-komponenterna, till exempel datatyper, operatorer, sträng-, aritmetiska, logiska och markörfunktioner, på samma sätt i SQL Server och SQL Database. Det finns dock några T-SQL-skillnader i DDL -element (datadefinitionsspråk) och DML-element (datamanipulationsspråk) som resulterar i T-SQL-satser och frågor som bara delvis stöds (som vi diskuterar senare i den här artikeln).

Dessutom finns det vissa funktioner och syntax som inte stöds alls eftersom Azure SQL Database är utformad för att isolera funktioner från beroenden på huvuddatabasen och operativsystemet. De flesta aktiviteter på servernivå är därför olämpliga för SQL Database. T-SQL-satser och -alternativ är inte tillgängliga om de konfigurerar alternativ på servernivå, operativsystemkomponenter eller anger filsystemkonfiguration. När sådana funktioner krävs är ett lämpligt alternativ ofta tillgängligt på annat sätt från SQL Database eller från en annan Azure-funktion eller -tjänst.

Till exempel är hög tillgänglighet inbyggd i Azure SQL Database med hjälp av teknik som liknar [Alltid på tillgänglighetsgrupper](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server). T-SQL-uttryck som är relaterade till tillgänglighetsgrupper stöds inte av SQL Database, och de dynamiska hanteringsvyerna som är relaterade till alltid på tillgänglighetsgrupper stöds inte heller.

En lista över de funktioner som stöds och som inte stöds av SQL Database finns i [jämförelsen för funktionen Azure SQL Database](sql-database-features.md). Listan på den här sidan kompletterar den artikel med riktlinjer och funktioner och fokuserar på Transact-SQL-uttryck.

## <a name="transact-sql-syntax-statements-with-partial-differences"></a>Transact-SQL-syntaxsatser med partiella skillnader

De grundläggande DDL-uttalandena (datadefinitionsspråk) är tillgängliga, men vissa DDL-satser har tillägg relaterade till diskplacering och funktioner som inte stöds.

- SKAPA och ÄNDRA DATABAS satser har över tre dussin alternativ. Satserna omfattar filplacering, FILESTREAM och service broker-alternativ som endast gäller för SQL Server. Detta kanske inte spelar någon roll om du skapar databaser innan du migrerar, men om du migrerar T-SQL-kod som skapar databaser bör du jämföra [SKAPA DATABAS (Azure SQL Database)](https://msdn.microsoft.com/library/dn268335.aspx) med SQL Server-syntaxen på [CREATE DATABASE (SQL Server Transact-SQL)](https://msdn.microsoft.com/library/ms176061.aspx) för att se till att alla alternativ du använder stöds. SKAPA DATABAS för Azure SQL Database har också alternativ för tjänstmål och elastisk skala som endast gäller för SQL Database.
- Skapa- och ALTER TABLE-satserna har filetable-alternativ som inte kan användas i SQL Database eftersom FILESTREAM inte stöds.
- SKAPA och ÄNDRA inloggningssatser stöds men SQL Database erbjuder inte alla alternativ. För att göra databasen mer portabel uppmuntrar SQL Database att använda innehållna databasanvändare i stället för inloggningar när det är möjligt. Mer information finns i [SKAPA/ÄNDRA INLOGGNING](https://docs.microsoft.com/sql/t-sql/statements/alter-login-transact-sql) och [Hantera inloggningar och användare](sql-database-manage-logins.md).

## <a name="transact-sql-syntax-not-supported-in-azure-sql-database"></a>Transact-SQL-syntax stöds inte i Azure SQL Database

Förutom Transact-SQL-uttryck som är relaterade till de funktioner som inte stöds som beskrivs i [Azure SQL Database-funktionsjämförelse](sql-database-features.md)stöds inte följande satser och grupper av satser. Om databasen som ska migreras använder någon av följande funktioner, omforma därför T-SQL för att eliminera dessa T-SQL-funktioner och -satser.

- Sortering av systemobjekt
- Anslutningsrelaterad: Slutpunktssatser. SQL Database har inte stöd för Windows-autentisering, men stöder den liknande Azure Active Directory-autentiseringen. Vissa typer av autentiseringar kräver den senaste versionen av SSMS. Du hittar mer information i [Ansluta till SQL Database eller SQL Data Warehouse med Azure Active Directory-autentisering](sql-database-aad-authentication.md).
- Frågor mellan databaser med tre eller fyra delnamn. (Skrivskyddade frågor över flera databaser stöds med hjälp av [Elastic Database-fråga](sql-database-elastic-query-overview.md).)
- Länkning av ägarskap mellan databaser, `TRUSTWORTHY`-inställning
- `EXECUTE AS LOGIN` Använd EXECUTE AS USER i stället.
- Kryptering stöds utom för Extensible Key Management
- Eventing: Händelser, händelsemeddelanden, frågemeddelanden
- Filplacering: Syntax relaterad till databasfilplacering, storlek och databasfiler som hanteras automatiskt av Microsoft Azure.
- Hög tillgänglighet: Syntax relaterad till hög tillgänglighet, som hanteras via ditt Microsoft Azure-konto. Detta inkluderar syntax för säkerhetskopiering, återställa, Alltid på, databasspegling, loggöverföring återställningslägen.
- Loggläsare: Syntax som är beroende av loggläsaren, som inte är tillgänglig på SQL Database: Push Replication, Ändra datainsamling. SQL Database kan vara en prenumerant för en artikel för push-replikering.
- Funktioner: `fn_get_sql`, `fn_virtualfilestats`,`fn_virtualservernodes`
- Maskinvara: Syntax relaterad till maskinvarurelaterade serverinställningar: till exempel minne, arbetstrådar, CPU-tillhörighet, spårningsflaggor. Använd tjänstnivåer och beräkningsstorlekar i stället.
- `KILL STATS JOB`
- `OPENQUERY`, `OPENROWSET` `OPENDATASOURCE`och fyra delar namn
- .NET Framework: CLR-integrering med SQL Server
- Semantisk sökning
- Serveruppgifter: Använd [autentiseringsuppgifter för databasscope](https://msdn.microsoft.com/library/mt270260.aspx) i stället.
- Objekt på servernivå: `sys.login_token`Serverroller, . `GRANT`, `REVOKE` och `DENY` av serverbehörigheter inte är tillgängliga även om vissa ersätts med databasbehörigheter. Vissa användbara DMV:er på servernivå har motsvarande DMV:er på databasnivå.
- `SET REMOTE_PROC_TRANSACTIONS`
- `SHUTDOWN`
- `sp_addmessage`
- `sp_configure`-alternativ och `RECONFIGURE`. Vissa alternativ är tillgängliga med [ALTER DATABASE SCOPED CONFIGURATION](https://msdn.microsoft.com/library/mt629158.aspx).
- `sp_helpuser`
- `sp_migrate_user_to_contained`
- SQL Server Agent: Syntax som förlitar sig på SQL Server Agent eller MSDB-databasen: aviseringar, operatörer, centrala hanteringsservrar. Använd skript, till exempel Azure PowerShell i stället.
- SQL Server-granskning: Använd SQL Database-granskning i stället.
- Spårning av SQL Server
- Spårningsflaggor: Vissa spårningsflaggobjekt har flyttats till kompatibilitetslägen.
- Felsökning av Transact-SQL
- Utlösare: Serveromfattande eller inloggningsutlösare
- `USE`-uttryck: Om du vill ändra databasens kontext till en annan databas måste du göra en ny anslutning till den nya databasen.

## <a name="full-transact-sql-reference"></a>Fullständig referens för Transact-SQL

Mer information om Transact-SQL-grammatik, användning och exempel finns i [Transact-SQL Reference (Database Engine)](https://msdn.microsoft.com/library/bb510741.aspx) i SQL Server Books Online.

### <a name="about-the-applies-to-tags"></a>Om "Gäller för"-taggar

Transact-SQL-referensen innehåller artiklar relaterade till SQL Server-versioner 2008 till idag. Under artikeltiteln finns ett ikonfält med de fyra SQL Server-plattformarna och som anger tillämplighet. Till exempel introducerades tillgänglighetsgrupper i SQL Server 2012. Artikeln [SKAPA TILLGÄNGLIGHETSGRUPP](https://msdn.microsoft.com/library/ff878399.aspx) anger att uttrycket gäller **för SQL Server (från och med 2012)**. Uttrycket gäller inte för SQL Server 2008, SQL Server 2008 R2, Azure SQL Database, Azure SQL Data Warehouse eller Parallel Data Warehouse.

I vissa fall kan det allmänna ämnet för en vara användas i en produkt, men det finns mindre skillnader mellan produkter. Skillnaderna anges vid mittpunkter i artikeln beroende på vad som är lämpligt. I vissa fall kan det allmänna ämnet för en vara användas i en produkt, men det finns mindre skillnader mellan produkter. Skillnaderna anges vid mittpunkter i artikeln beroende på vad som är lämpligt. Till exempel skapa utlösare artikeln är tillgänglig i SQL Database. Men alternativet **ALLA SERVER** för utlösare på servernivå anger att utlösare på servernivå inte kan användas i SQL Database. Använd utlösare på databasnivå i stället.

## <a name="next-steps"></a>Nästa steg

En lista över de funktioner som stöds och som inte stöds av SQL Database finns i [jämförelsen för funktionen Azure SQL Database](sql-database-features.md). Listan på den här sidan kompletterar den artikel med riktlinjer och funktioner och fokuserar på Transact-SQL-uttryck.
