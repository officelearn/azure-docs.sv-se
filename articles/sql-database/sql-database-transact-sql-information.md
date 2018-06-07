---
title: Lösa T-SQL skillnader migrering Azure SQL Database | Microsoft Docs
description: Transact-SQL-uttryck som inte stöds helt i Azure SQL Database
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: migrate
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: e89c863ac50a8b906b388c505f444cd60fdbaad3
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34649082"
---
# <a name="resolving-transact-sql-differences-during-migration-to-sql-database"></a>Lösa Transact-SQL-skillnader under migreringen till SQL-databas   
När [migrera din databas](sql-database-cloud-migrate.md) från SQL Server till Azure SQL Server, kan du upptäcka att databasen kräver vissa omarbetningar innan SQL Server kan migreras. Den här artikeln innehåller vägledning för att hjälpa dig i både utför den här omarbetningar och förstå underliggande orsaker varför den omarbetningar krävs. Identifiera inkompatibiliteter genom att använda den [Data migrering Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595).

## <a name="overview"></a>Översikt
De flesta Transact-SQL-funktioner som använder program stöds helt i både Microsoft SQL Server och Azure SQL Database. Kärnkomponenter för SQL, till exempel datatyper, operatorer, string, aritmetiska, logiska, och markören funktioner fungerar till exempel identiskt i SQL Server och SQL-databas. Det finns dock några skillnader i T-SQL i DDL (data definition language) och DML (data manipulation language)-element som resulterar i T-SQL-uttryck och frågor som bara delvis (som senare i den här artikeln tar vi upp).

Det finns dessutom vissa funktioner och syntax som inte stöds i alla eftersom Azure SQL Database är utformat för att isolera funktioner från beroenden på master-databasen och operativsystemet. Därför är de flesta servernivå aktiviteter olämpligt för SQL-databas. T-SQL-uttryck och alternativ är inte tillgängliga om de konfigurera alternativ för servernivå komponenter för operativsystemet, eller ange filen systemkonfigurationen. När sådana funktioner krävs ett lämpligt alternativ ofta är tillgänglig på annat sätt från SQL-databas eller en annan Azure-funktion eller tjänst. 

Exempelvis är hög tillgänglighet inbyggda i Azure, så konfigurerar alltid på inte behövs (även om du kanske vill konfigurera aktiv geo-replikering för snabbare återställning vid en katastrof). Så, T-SQL-uttryck som rör Tillgänglighetsgrupper stöds inte av SQL-databas och dynamiska hanteringsvyer som rör alltid på också stöds inte.

En lista över de funktioner som stöds och som inte stöds av SQL-databasen, se [Azure SQL Database funktionsjämförelse](sql-database-features.md). I listan på den här sidan kompletterar artikeln riktlinjer och funktioner och fokuserar på Transact-SQL-uttryck.

## <a name="transact-sql-syntax-statements-with-partial-differences"></a>Transact-SQL-syntaxen instruktioner med partiellt skillnader
Core DDL (data definition language)-uttryck är tillgängliga, men vissa DDL-instruktioner har tillägg relaterade till disk placering och stöds inte funktioner. 

- Skapa och ändra databas har över tre 12 alternativ. Instruktionerna är filplacering, FILESTREAM och service broker-alternativ som gäller endast för SQL Server. Detta kan ingen roll om du skapar databaser innan du migrerar, men om du migrerar T-SQL-kod som skapar databaser du jämföra [CREATE DATABASE (Azure SQL Database)](https://msdn.microsoft.com/library/dn268335.aspx) med SQL Server-syntax på [skapa DATABAS (SQL Server Transact-SQL)](https://msdn.microsoft.com/library/ms176061.aspx) att kontrollera att alla de alternativ som du använder stöds. Skapa databas för Azure SQL Database har också tjänstmålet och elastisk skalbarhet alternativen som gäller bara för SQL-databas.
- Skapa och ALTER TABLE-instruktioner har FileTable alternativ som inte kan användas på SQL-databas eftersom FILESTREAM inte stöds.
- Skapa och ändra inloggnings-satser stöds men SQL Database erbjuder inte alla alternativ. Om du vill göra databasen mer bärbara uppmuntrar SQL-databas med oberoende databasanvändare i stället för inloggningar när det är möjligt. Mer information finns i [CREATE/ALTER inloggningen](https://msdn.microsoft.com/library/ms189828.aspx) och [styra och bevilja åtkomst till databasen](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins).

## <a name="transact-sql-syntax-not-supported-in-azure-sql-database"></a>Transact-SQL-syntax som inte stöds i Azure SQL Database   
Förutom Transact-SQL-instruktioner som är relaterade till funktionerna stöds inte som beskrivs i [Azure SQL Database funktionsjämförelse](sql-database-features.md), följande instruktioner och grupper av rapporter, stöds inte. Därför om databasen som ska migreras använder någon av följande funktioner, nytt tekniker T-SQL för att ta bort dessa T-SQL-funktioner och rapporter.

- Sortering av systemobjekt
- Anslutningen relaterade: Endpoint-instruktioner. SQL Database har inte stöd för Windows-autentisering, men stöder den liknande Azure Active Directory-autentiseringen. Vissa typer av autentiseringar kräver den senaste versionen av SSMS. Du hittar mer information i [Ansluta till SQL Database eller SQL Data Warehouse med Azure Active Directory-autentisering](sql-database-aad-authentication.md).
- Frågor mellan databaser med tre eller fyra delnamn. (Skrivskyddade frågor över flera databaser stöds med hjälp av [Elastic Database-fråga](sql-database-elastic-query-overview.md).)
- Länkning av ägarskap mellan databaser, `TRUSTWORTHY`-inställning
- `EXECUTE AS LOGIN` Använd EXECUTE AS USER i stället.
- Kryptering stöds utom för Extensible Key Management
- Eventing: Händelser, meddelanden om händelser, frågemeddelanden
- Filplacering: Syntax som är relaterade till databasen filplacering, storlek och databasfiler som hanteras automatiskt av Microsoft Azure.
- Hög tillgänglighet: Syntax som är relaterade till hög tillgänglighet, som hanteras via Microsoft Azure-konto. Detta inkluderar syntax för säkerhetskopiering, återställa, Alltid på, databasspegling, loggöverföring återställningslägen.
- Logga reader: Syntax som förlitar sig på log reader, som inte är tillgängligt på SQL-databas: Push-replikering, registrering av ändringsdata. SQL Database kan vara en prenumerant för en artikel för push-replikering.
- Funktioner: `fn_get_sql`, `fn_virtualfilestats`,`fn_virtualservernodes`
- Maskinvara: Syntax rör maskinvarurelaterade serverinställningar: till exempel minne, trådar, processortillhörighet trace flaggor. Använd i stället tjänstenivåer.
- `KILL STATS JOB`
- `OPENQUERY`, `OPENROWSET`, `OPENDATASOURCE`, och fyrdelat namn
- .NET framework: CLR-integrering med SQL Server
- Semantisk sökning
- Autentiseringsuppgifter: Använd [databasbegränsade autentiseringsuppgifter](https://msdn.microsoft.com/library/mt270260.aspx) i stället.
- Servernivå objekt: serverroller, `sys.login_token`. `GRANT`, `REVOKE` och `DENY` av serverbehörigheter inte är tillgängliga även om vissa ersätts med databasbehörigheter. Vissa användbara DMV:er på servernivå har motsvarande DMV:er på databasnivå.
- `SET REMOTE_PROC_TRANSACTIONS`
- `SHUTDOWN`
- `sp_addmessage`
- `sp_configure`-alternativ och `RECONFIGURE`. Vissa alternativ är tillgängliga med [ALTER DATABASE SCOPED CONFIGURATION](https://msdn.microsoft.com/library/mt629158.aspx).
- `sp_helpuser`
- `sp_migrate_user_to_contained`
- SQL ServerAgent: Syntax som bygger på SQL Server Agent eller MSDB-databas: aviseringar, operatorer, centrala hanteringsservrar. Använd skript, till exempel Azure PowerShell i stället.
- SQL Server audit: Använd SQL Database auditing i stället.
- Spårning av SQL Server
- Spårningsflaggor: vissa trace flagga objekt har flyttats till kompatibilitetsläge.
- Felsökning av Transact-SQL
- Utlösare: Serveromfattande eller inloggningsutlösare
- `USE`-uttryck: Om du vill ändra databasens kontext till en annan databas måste du göra en ny anslutning till den nya databasen.

## <a name="full-transact-sql-reference"></a>Fullständig referens för Transact-SQL
Mer information om Transact-SQL-grammatik, -användning och -exempel finns i [Transact-SQL Reference (Database Engine)](https://msdn.microsoft.com/library/bb510741.aspx) i SQL Server Books Online. 

### <a name="about-the-applies-to-tags"></a>Om "Gäller för"-taggar
Transact-SQL-referens innehåller artiklar som är relaterade till SQL Server-versioner 2008 aktuella. Under artikelrubriken det är en ikon, visar en lista över de fyra SQL Server-plattformarna och som anger tillämplighet. Till exempel introducerades tillgänglighetsgrupper i SQL Server 2012. Den [CREATE AVAILABILTY GROUP](https://msdn.microsoft.com/library/ff878399.aspx) artikeln anger att instruktionen gäller **SQL Server (startar med 2012)**. Uttrycket gäller inte för SQL Server 2008, SQL Server 2008 R2, Azure SQL Database, Azure SQL Data Warehouse eller Parallel Data Warehouse.

Allmän föremål för en artikel kan användas i en produkt i vissa fall, men det finns mindre skillnader mellan produkter. Skillnaderna visas på mittpunkter i artikeln efter behov. Allmän föremål för en artikel kan användas i en produkt i vissa fall, men det finns mindre skillnader mellan produkter. Skillnaderna visas på mittpunkter i artikeln efter behov. Till exempel finns CREATE TRIGGER-artikel i SQL-databas. Men **alla SERVER** för servernivå utlösare, anger att servernivå utlösare inte kan användas i SQL-databas. Använd databasnivå utlösare i stället.

## <a name="next-steps"></a>Nästa steg

En lista över de funktioner som stöds och som inte stöds av SQL-databasen, se [Azure SQL Database funktionsjämförelse](sql-database-features.md). I listan på den här sidan kompletterar artikeln riktlinjer och funktioner och fokuserar på Transact-SQL-uttryck.

