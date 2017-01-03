---
title: "Stöds inte i Azure SQL Database T-SQL | Microsoft Docs"
description: "Transact-SQL-uttryck som inte stöds helt i Azure SQL Database"
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
tags: 
ms.assetid: c05abd9e-28a7-4c97-9bdf-bc60d08fc92e
ms.service: sql-database
ms.custom: overview
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 11/28/2016
ms.author: rick.byham@microsoft.com
translationtype: Human Translation
ms.sourcegitcommit: 7db5939c88045ab59d7d9fb4b58f07374d7bca01
ms.openlocfilehash: ebec6ca87cfd5e9d2c4ea726d5d31d20998ef33f


---
# <a name="azure-sql-database-transact-sql-differences"></a>Azure SQL Database Transact-SQL skillnader   
De flesta av Transact-SQL-funktioner som program är beroende av stöds i både Microsoft SQL Server och Azure SQL Database. Till exempel fungerar SQL-kärnkomponenter såsom datatyper, operatörer, sträng, aritmetiska, logiska och markörfunktioner o.s.v. utan några skillnader från SQL Server.

## <a name="why-some-transact-sql-is-not-supported"></a>Varför stöds inte vissa Transact-SQL
Azure SQL Database är utformad för att isolera funktioner från beroenden på huvuddatabasen och operativsystemet. Följaktligen är många aktiviteter på servernivå olämpliga för SQL Database. Transact-SQL-uttryck är vanligtvis inte tillgängliga om de konfigurerar servernivåalternativ, operativsystemets komponenter eller anger systemkonfigurationen för filen. När funktioner som är utanför användardatabasen krävs, är ett lämpligt alternativ ofta tillgänglig på annat sätt från SQL Database eller från en annan Azure-funktion eller -tjänst. 

Till exempel ersätts Alltid på med Aktiv geo-replikering. Därför stöds eventuella Transact-SQL-uttryck som rör tillgänglighetsgrupper inte av SQL Database och vyer för dynamisk hantering som rör Alltid på stöds inte.  

En lista över de funktioner som stöds och som inte stöds av SQL Database finns i [Överväganden, riktlinjer och funktioner för Azure SQL Database](sql-database-features.md).

Syntax som är inaktuell i SQL Server stöds vanligtvis inte i SQL Database.

## <a name="transact-sql-syntax-partially-supported-in-sql-database"></a>Transact-SQL-syntaxen stöds delvis i SQL Database
SQL Database stöder vissa men inte alla argument som finns i motsvarande SQL Server 2016 Transact-SQL-uttryck. Till exempel är `CREATE PROCEDURE`-uttrycket tillgängligt, men alla alternativ i `CREATE PROCEDURE` är inte tillgängliga. Att beskriva fullständig syntax skulle bli förvirrande och redundant. Referera till de länkade syntaxavsnitten för mer information om områdena som stöds för varje uttryck.

- Databaser: [CREATE](https://msdn.microsoft.com/library/dn268335.aspx)/[ALTER DATABASE](https://msdn.microsoft.com/library/ms174269.aspx)   
- Funktioner: [CREATE](https://msdn.microsoft.com/library/ms186755.aspx)/[ALTER FUNCTION](https://msdn.microsoft.com/library/ms186967.aspx)   
- Inloggningar: [CREATE](https://msdn.microsoft.com/library/ms189751.aspx)/[ALTER LOGIN](https://msdn.microsoft.com/library/ms189828.aspx)   
- Lagrade procedurer: [CREATE](https://msdn.microsoft.com/library/ms187926.aspx)/[ALTER PROCEDURE](https://msdn.microsoft.com/library/ms189762.aspx)   
- Tabeller: [CREATE](https://msdn.microsoft.com/library/dn305849.aspx)/[ALTER TABLE](https://msdn.microsoft.com/library/ms190273.aspx)   
- Typer (anpassade): [CREATE TYPE](https://msdn.microsoft.com/library/ms175007.aspx)   
- Användare: [CREATE](https://msdn.microsoft.com/library/ms173463.aspx)/[ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx)   
- Vyer: [CREATE](https://msdn.microsoft.com/library/ms187956.aspx)/[ALTER VIEW](https://msdn.microsoft.com/library/ms173846.aspx)   

## <a name="transact-sql-syntax-not-supported-in-sql-database"></a>Transact-SQL-syntaxen stöds inte i SQL Database   
Förutom Transact-SQL-uttryck som rör de funktioner som inte stöds som beskrivs i [Överväganden, riktlinjer och funktioner för Azure SQL Database](sql-database-features.md), stöds inte följande uttryck och grupper av uttryck.
- Sortering av systemobjekt
- Anslutningsrelaterade: Slutpunktsrapporter, `ORIGINAL_DB_NAME`. SQL Database har inte stöd för Windows-autentisering, men stöder den liknande Azure Active Directory-autentiseringen. Vissa typer av autentiseringar kräver den senaste versionen av SSMS. Du hittar mer information i [Ansluta till SQL Database eller SQL Data Warehouse med Azure Active Directory-autentisering](sql-database-aad-authentication.md).
- Frågor mellan databaser med tre eller fyra delnamn. (Skrivskyddade frågor över flera databaser stöds med hjälp av [Elastic Database-fråga](sql-database-elastic-query-overview.md).)
- Länkning av ägarskap mellan databaser, `TRUSTWORTHY`-inställning
- `DATABASEPROPERTY` Använd `DATABASEPROPERTYEX` i stället.
- `EXECUTE AS LOGIN` Använd EXECUTE AS USER i stället.
- Kryptering stöds utom för Extensible Key Management
- Eventing: händelser, händelsemeddelanden, frågemeddelanden
- Syntax som avser databasens filplacering, storlek och databasfiler som hanteras automatiskt av Microsoft Azure.
- Syntax som avser hög tillgänglighet, som hanteras av Microsoft Azure-kontot. Detta inkluderar syntax för säkerhetskopiering, återställa, Alltid på, databasspegling, loggöverföring återställningslägen.
- Syntax som använder Log Reader, som inte är tillgänglig på SQL Database: Push-replikering, registrering av ändringsdata. SQL Database kan vara en prenumerant för en artikel för push-replikering.
- Syntax som bygger på SQL Server Agent eller MSDB-databasen: aviseringar, operatörer, centrala hanteringsservrar. Använd skript, till exempel Azure PowerShell i stället.
- Funktioner: `fn_get_sql`, `fn_virtualfilestats`,`fn_virtualservernodes`
- Globala temporära tabeller
- Syntax för maskinvarurelaterade serverinställningar: minne, arbetstrådar, processortillhörighet, spårningsflaggor o.s.v. Använd i stället tjänstenivåer.
- `HAS_DBACCESS`
- `KILL STATS JOB`
- `OPENQUERY`, `OPENROWSET`, `OPENDATASOURCE`, `BULK INSERT` och fyrdelade namn
- .NET Framework [CLR-integrering med SQL Server](http://msdn.microsoft.com/library/ms254963.aspx)
- Semantisk sökning
- Serverautentiseringsuppgifter. Använd databasbegränsade autentiseringsuppgifter i stället.
- Servernivåobjekt: serverroller, `IS_SRVROLEMEMBER`, `sys.login_token`. `GRANT`, `REVOKE` och `DENY` av serverbehörigheter inte är tillgängliga även om vissa ersätts med databasbehörigheter. Vissa användbara DMV:er på servernivå har motsvarande DMV:er på databasnivå.
- `SET REMOTE_PROC_TRANSACTIONS`
- `SHUTDOWN`
- `sp_addmessage`
- `sp_configure`-alternativ och `RECONFIGURE`. Vissa alternativ är tillgängliga med [ALTER DATABASE SCOPED CONFIGURATION](https://msdn.microsoft.com/library/mt629158.aspx).
- `sp_helpuser`
- `sp_migrate_user_to_contained`
- Granskning av SQL Server. Använd granskning av SQL Database i stället.
- Spårning av SQL Server
- Spåra flaggor. Vissa spårade flaggobjekt har flyttats till kompatibilitetsläge.
- Felsökning av Transact-SQL
- Utlösare: Serveromfattande eller inloggningsutlösare
- `USE`-uttryck: Om du vill ändra databasens kontext till en annan databas måste du göra en ny anslutning till den nya databasen.

## <a name="full-transact-sql-reference"></a>Fullständig referens för Transact-SQL
Mer information om Transact-SQL-grammatik, -användning och -exempel finns i [Transact-SQL Reference (Database Engine)](https://msdn.microsoft.com/library/bb510741.aspx) i SQL Server Books Online. 

### <a name="about-the-applies-to-tags"></a>Om "Gäller för"-taggar
Transact-SQL-referensen innehåller information om versioner av SQL Server från 2008 fram till dags dato. Under ämnesrubriken finns ett ikonfält som visar en lista över de fyra SQL Server-plattformar och som anger tillämplighet. Till exempel introducerades tillgänglighetsgrupper i SQL Server 2012. Avsnittet [SKAPA TILLGÄNGLIGHETSGRUPP](https://msdn.microsoft.com/library/ff878399.aspx) anger uttrycket som gäller för **SQL Server (startar med 2012)**. Uttrycket gäller inte för SQL Server 2008, SQL Server 2008 R2, Azure SQL Database, Azure SQL Data Warehouse eller Parallel Data Warehouse.

I vissa fall kan det allmänna ämnet för ett avsnitt användas i en produkt, men det finns mindre skillnader mellan produkter. Skillnaderna anges vid mittpunkter i avsnittet om det behövs.



<!--HONumber=Jan17_HO1-->


