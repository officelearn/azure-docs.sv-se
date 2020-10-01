---
title: Lösa T-SQL-skillnader – migrering
description: Transact-SQL-uttryck som är mindre än fullt stöd i Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: reference
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/03/2018
ms.openlocfilehash: 02b589eebb716f5a69b4db9f00faf12401b8de7f
ms.sourcegitcommit: 4bebbf664e69361f13cfe83020b2e87ed4dc8fa2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/01/2020
ms.locfileid: "91619014"
---
# <a name="resolving-transact-sql-differences-during-migration-to-sql-database"></a>Lösa skillnader i Transact-SQL vid migrering till SQL Database

När du [migrerar databasen](migrate-to-database-from-sql-server.md) från SQL Server till Azure SQL Database kan du upptäcka att SQL Servers databasen kräver viss omkonstruktion innan den kan migreras. Den här artikeln innehåller vägledning som hjälper dig att både utföra den här ombyggnaden och förstå de underliggande orsakerna till varför den nya omställningen är nödvändig. Använd [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595)om du vill identifiera inkompatibiliteter.

## <a name="overview"></a>Översikt

De flesta Transact-SQL-funktioner som program använder stöds fullt ut i både Microsoft SQL Server och Azure SQL Database. Till exempel fungerar kärn SQL-komponenter som data typer, operatorer, strängar, aritmetiska, logiska och markör funktioner identiska i SQL Server och SQL Database. Det finns dock några skillnad i T-SQL i DDL (Data Definition Language) och DML-element (Data Manipulation Language) som resulterar i T-SQL-uttryck och frågor som bara delvis stöds (som vi diskuterar senare i den här artikeln).

Dessutom finns det vissa funktioner och syntax som inte stöds alls eftersom Azure SQL Database är utformad för att isolera funktioner från beroenden i huvud databasen och operativ systemet. Därför är de flesta server nivå aktiviteter olämpliga för SQL Database. T-SQL-uttryck och alternativ är inte tillgängliga om de konfigurerar alternativ på server nivå, operativ system komponenter eller ange fil system konfiguration. När sådana funktioner krävs är ett lämpligt alternativ ofta tillgängligt på något annat sätt från SQL Database eller från en annan Azure-funktion eller-tjänst.

Till exempel är hög tillgänglighet inbyggd i Azure SQL Database med teknik som liknar [Always on-tillgänglighetsgrupper](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server). T-SQL-uttryck relaterade till tillgänglighets grupper stöds inte av SQL Database och dynamiska hanterings vyer som rör Always on-tillgänglighetsgrupper stöds inte heller.

En lista över de funktioner som stöds och som inte stöds av SQL Database finns i [Azure SQL Database jämförelse](features-comparison.md)av funktioner. Listan på den här sidan kompletterar rikt linjerna och funktionerna i artikeln och fokuserar på Transact-SQL-uttryck.

## <a name="transact-sql-syntax-statements-with-partial-differences"></a>Transact-SQL-uttryck med partiella skillnader

Huvud-DDL-uttrycket (Data Definition Language) är tillgängliga, men vissa DDL-instruktioner har tillägg som rör disk placering och funktioner som inte stöds.

- CREATE-och ALTER DATABASE-instruktioner har över tre dussin alternativ. Instruktionerna inkluderar alternativ för fil placering, FILESTREAM och Service Broker som endast gäller för SQL Server. Detta kanske inte spelar någon roll om du skapar databaser innan du migrerar, men om du migrerar en T-SQL-kod som skapar databaser bör du jämföra [skapa databas (Azure SQL Database)](https://msdn.microsoft.com/library/dn268335.aspx) med syntaxen SQL Server i [create Database (SQL Server Transact-SQL)](https://msdn.microsoft.com/library/ms176061.aspx) för att se till att alla alternativ som du använder stöds. SKAPA databas för Azure SQL Database även har service mål och elastiska skalnings alternativ som endast gäller för SQL Database.
- CREATE-och ALTER TABLE-instruktionerna innehåller FileTable-alternativ som inte kan användas på SQL Database eftersom FILESTREAM inte stöds.
- CREATE-och ALTER login-instruktioner stöds, men SQL Database erbjuder inte alla alternativ. För att göra databasen mer portabel SQL Database uppmuntrar med inneslutna databas användare i stället för inloggningar närhelst det är möjligt. Mer information finns i [Skapa/ändra inloggning](https://docs.microsoft.com/sql/t-sql/statements/alter-login-transact-sql) och [Hantera inloggningar och användare](logins-create-manage.md).

## <a name="transact-sql-syntax-not-supported-in-azure-sql-database"></a>Transact-SQL-syntaxen stöds inte i Azure SQL Database

Förutom Transact-SQL-uttryck som är relaterade till de funktioner som inte stöds som beskrivs i [Azure SQL Database funktions jämförelse](features-comparison.md)stöds inte följande uttryck och grupper av uttryck. Om din databas som ska migreras använder någon av följande funktioner kan du återställa T-SQL för att eliminera dessa funktioner och uttryck i T-SQL.

- Sortering av systemobjekt
- Anslutning relaterad: slut punkts instruktioner. SQL Database stöder inte Windows-autentisering, men stöder samma Azure Active Directory autentisering. Vissa typer av autentiseringar kräver den senaste versionen av SSMS. Mer information finns i [ansluta till SQL Database eller Azure Azure Synapse Analytics (tidigare SQL Data Warehouse) med Azure Active Directory autentisering](authentication-aad-overview.md).
- Frågor mellan databaser med tre eller fyra delnamn. (Skrivskyddade frågor över flera databaser stöds med hjälp av [Elastic Database-fråga](elastic-query-overview.md).)
- Länkning av ägarskap mellan databaser, `TRUSTWORTHY`-inställning
- `EXECUTE AS LOGIN` Använd EXECUTE AS USER i stället.
- Kryptering stöds utom för Extensible Key Management
- Händelse: händelser, händelse aviseringar, fråge meddelanden
- Fil placering: syntax som är relaterad till databas fil placering, storlek och databasfiler som hanteras automatiskt av Microsoft Azure.
- Hög tillgänglighet: syntax som är relaterad till hög tillgänglighet, som hanteras via ditt Microsoft Azure-konto. Detta inkluderar syntax för säkerhetskopiering, återställa, Alltid på, databasspegling, loggöverföring återställningslägen.
- Logg läsare: syntax som förlitar sig på logg läsaren, som inte är tillgänglig på SQL Database: push-replikering, insamling av ändrings data. SQL Database kan vara en prenumerant för en artikel för push-replikering.
- Funktioner: `fn_get_sql`, `fn_virtualfilestats`,`fn_virtualservernodes`
- Maskin vara: syntax som är relaterad till maskinvarurelaterade Server inställningar: till exempel minne, arbets trådar, processor tillhörighet, spårnings flaggor. Använd tjänst nivåer och beräknings storlekar i stället.
- `KILL STATS JOB`
- `OPENQUERY`,, och `OPENROWSET` `OPENDATASOURCE` namn på fyra delar
- .NET Framework: CLR-integrering med SQL Server
- Semantisk sökning
- Autentiseringsuppgifter för Server: Använd [databasens begränsade autentiseringsuppgifter](https://msdn.microsoft.com/library/mt270260.aspx) i stället.
- Server nivå objekt: Server roller, `sys.login_token` . `GRANT`, `REVOKE` och `DENY` behörigheter på server nivå är inte tillgängliga trots att vissa har ersatts av behörigheter på databas nivå. Vissa användbara DMV:er på servernivå har motsvarande DMV:er på databasnivå.
- `SET REMOTE_PROC_TRANSACTIONS`
- `SHUTDOWN`
- `sp_addmessage`
- `sp_configure`-alternativ och `RECONFIGURE`. Vissa alternativ är tillgängliga med [ALTER DATABASE SCOPED CONFIGURATION](https://msdn.microsoft.com/library/mt629158.aspx).
- `sp_helpuser`
- `sp_migrate_user_to_contained`
- SQL Server Agent: syntax som förlitar sig på SQL Server Agent eller MSDB-databasen: aviseringar, operatörer, centrala hanterings servrar. Använd skript, till exempel Azure PowerShell i stället.
- SQL Server granskning: Använd SQL Database granskning i stället.
- Spårning av SQL Server
- Spårnings flaggor: vissa spårnings flagga objekt har flyttats till kompatibilitetsläge.
- Felsökning av Transact-SQL
- Utlösare: Serveromfattande eller inloggningsutlösare
- `USE`-uttryck: Om du vill ändra databasens kontext till en annan databas måste du göra en ny anslutning till den nya databasen.

## <a name="full-transact-sql-reference"></a>Fullständig referens för Transact-SQL

Mer information om hur du använder Transact-SQL grammatik, användning och exempel finns i [referens för Transact-SQL (databas motor)](https://msdn.microsoft.com/library/bb510741.aspx)   i SQL Server Books Online.

### <a name="about-the-applies-to-tags"></a>Om "Gäller för"-taggar

Transact-SQL-referensen innehåller artiklar relaterade till SQL Server versioner 2008 för närvarande. Under artikel rubriken finns ett ikon fält med fyra SQL Server plattformar och som anger tillämplighet. Till exempel introducerades tillgänglighetsgrupper i SQL Server 2012. I artikeln [skapa tillgänglighets grupp](https://msdn.microsoft.com/library/ff878399.aspx)   anges att instruktionen gäller för **SQL Server (från och med 2012)**. Instruktionen gäller inte SQL Server 2008, SQL Server 2008 R2, Azure SQL Database, Azure Azure Synapse Analytics (tidigare SQL Data Warehouse) eller parallell data lager.

I vissa fall kan en artikels allmänna ämne användas i en produkt, men det finns mindre skillnader mellan produkterna. Skillnaderna anges på mitt punkter i artikeln efter behov. I vissa fall kan en artikels allmänna ämne användas i en produkt, men det finns mindre skillnader mellan produkterna. Skillnaderna anges på mitt punkter i artikeln efter behov. Till exempel är artikeln Skapa utlösare tillgänglig i SQL Database. Men alternativet **alla Server** för utlösare på server nivå anger att utlösare på server nivå inte kan användas i SQL Database. Använd i stället databas nivå utlösare.

## <a name="next-steps"></a>Nästa steg

En lista över de funktioner som stöds och som inte stöds av SQL Database finns i [Azure SQL Database jämförelse](features-comparison.md)av funktioner. Listan på den här sidan kompletterar rikt linjerna och funktionerna i artikeln och fokuserar på Transact-SQL-uttryck.
