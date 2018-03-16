---
title: "Bevilja åtkomst till Azure SQL Database | Microsoft Docs"
description: "Läs dig hur du beviljar åtkomst till Microsoft Azure SQL Database."
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: security
ms.topic: article
ms.date: 02/06/2017
ms.author: carlrab
ms.openlocfilehash: 85d3e7819ef06fd943c207e1410c95168f574a7d
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="azure-sql-database-access-control"></a>Azure SQL Database-åtkomstkontroll
För att erbjuda säkerhet, kontrollerar SQL Database åtkomsten med hjälp av brandväggsregler som begränsar anslutning efter IP-adress, autentiseringsmekanismer kräver att användare bevisar sin identitet och auktoriseringsmekanismer begränsar användare till specifika åtgärder och data. 

> [!IMPORTANT]
> En översikt över säkerhetsfunktionerna i SQL Database finns i [SQL Säkerhetsöversikt](sql-database-security-overview.md). En självstudiekurs finns [skydda din Azure SQL Database](sql-database-security-tutorial.md).

## <a name="firewall-and-firewall-rules"></a>Brandvägg och brandväggsregler
Microsoft Azure SQL Database tillhandahåller en relationsdatabastjänst för Azure och andra Internetbaserade program. För att skydda dina data förhindrar brandväggar all åtkomst till din databasserver tills du anger vilka datorer som har behörighet. Brandväggen ger åtkomst till databaser baserat på vilken IP-adress som varje begäran kommer från. Mer information finns i [Översikt över Azure SQL Database-brandväggsregler](sql-database-firewall-configure.md)

Azure SQL Database-tjänsten är endast tillgänglig via TCP-port 1433. Se till att klientdatorns brandvägg tillåter utgående TCP-kommunikation på TCP-port 1433 för att komma åt en SQL Database från datorn. Om det inte behövs för andra program, blockera inkommande anslutningar på TCP-port 1433. 

Som en del av anslutningen omdirigeras anslutningar från virtuella Azure-datorer till en annan IP-adress och port, som är unik för varje arbetsroll. Portnumret är i intervallet 11000 till 11999. Mer information om TCP-portar finns [portar utöver 1433 för ADO.NET 4.5 och SQL Database2](sql-database-develop-direct-route-ports-adonet-v12.md).

## <a name="authentication"></a>Autentisering

SQL Database stöder två typer av autentisering:

* **SQL-autentisering**, som använder ett användarnamn och lösenord. När du skapade den logiska servern för databasen angav du en "serveradministratörsinloggning” med ett användarnamn och lösenord. Med dessa autentiseringsuppgifter kan du autentisera till en databas på servern som databasens ägare eller "dbo." 
* **Azure Active Directory-autentisering**, som använder identiteter som hanteras av Azure Active Directory och stöder hanterade och integrerade domäner. Använd Active Directory-autentisering (integrerad säkerhet) [närhelst det går](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode). Om du vill använda Azure Active Directory-autentisering måste du skapa en annan serveradministratör som kallas "Azure AD-admin," som tillåts administrera Azure AD-användare och -grupper. Den här administratören kan också utföra alla åtgärder som en vanlig serveradministratören kan. Se [Ansluta till SQL Database genom att använda Azure Active Directory-autentisering](sql-database-aad-authentication.md) för en genomgång av hur du skapar en Azure AD-administratör för att aktivera Azure Active Directory-autentisering.

Databasmotorn stänger anslutningar som är inaktiva under mer än 30 minuter. Anslutningen måste logga in igen innan den kan användas. Kontinuerligt aktiva anslutningar till SQL Database kräver omauktorisering (utförs av databasmotorn) minst var 10:e timma. Databasmotorn försöker omauktorisera det ursprungligen skickade lösenordet och inga indata från användaren krävs. Av prestandaskäl omauktoriseras inte anslutningen när ett lösenord återställs i SQL Database, även om anslutningen har återställts på grund av anslutningspoolning. Detta skiljer sig från beteendet för lokal SQL Server. Om lösenordet har ändrats sedan anslutningen ursprungligen auktoriserades, måste anslutningen avslutas och en ny anslutning skapas med det nya lösenordet. En användare med behörigheten `KILL DATABASE CONNECTION` kan explicit avsluta en anslutning till SQL Database med hjälp av kommandot [KILL](https://docs.microsoft.com/sql/t-sql/language-elements/kill-transact-sql).

Användarkonton kan skapas i huvuddatabasen och kan tilldelas behörigheter i alla databaser på servern, eller så kan de skapas i själva databasen (kallas inneslutna användare). För information om hur man skapar och hanterar inloggningar, se [Hantera inloggningar](sql-database-manage-logins.md). Använd inneslutna databasanvändare för att förbättra portabilitet och skalbarhet. Mer information om inneslutna användare finns i [Inneslutna databasanvändare – Gör din databas portabel](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable), [Skapa användare (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql) och [Inneslutna databaser](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases).

Ett bra tips är att ditt program bör använda ett särskilt konto för autentisering – på det här sättet kan du begränsa behörigheterna för programmet och minska riskerna för skadlig programvara om din programkod är sårbar för ett SQL-angrepp. Den rekommenderade metoden är att skapa en [innesluten databasanvändare](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable), vilket gör att din app kan autentisera direkt till databasen. 

## <a name="authorization"></a>Auktorisering

Auktorisering hänvisar till vad en användare kan göra inom en Azure SQL-databas och det styrs av ditt användarkontos databas [rollmedlemskap](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles) och [behörigheter på objektnivå](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine). Ett bra tips är att du ska ge användare så få behörigheter som möjligt. Serveradministratörskontot som du ansluter med är medlem i db_owner som har behörighet att göra vad som helst i databasen. Spara det här kontot för att distribuera schemauppgraderingar och andra hanteringsåtgärder. Använd kontot "ApplicationUser" med mer begränsade behörigheter för att ansluta från ditt program till databasen med den minsta behörigheten som krävs av programmet. Mer information finns i [Hantera inloggningar](sql-database-manage-logins.md).

Normalt behöver endast administratörer ha åtkomst till `master`-databasen. Rutinåtkomst till varje användardatabas ska vara via oberoende databasanvändare som inte är administratör, som skapats i varje databas. När du använder oberoende databasanvändare behöver du inte skapa inloggningar i databasen `master`. Mer information finns i [Oberoende databasanvändare – göra databasen portabel](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable).

Du bör bekanta dig med följande funktioner som kan användas för att begränsa eller utöka behörigheter:   
* [Personifiering](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/customizing-permissions-with-impersonation-in-sql-server) och [modulsignering](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/signing-stored-procedures-in-sql-server) kan användas för att säkert höja behörigheter tillfälligt.
* [Säkerhet på radnivå](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) kan användas som en begränsning av vilka rader en användare kan komma åt.
* [Datamaskning](sql-database-dynamic-data-masking-get-started.md) kan användas för att begränsa exponering av känsliga data.
* [Lagrade procedurer](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) kan användas för att begränsa de åtgärder som kan utföras i databasen.

## <a name="next-steps"></a>Nästa steg

- En översikt över säkerhetsfunktionerna i SQL Database finns i [SQL Säkerhetsöversikt](sql-database-security-overview.md).
- Mer information om brandväggsregler finns [regler i brandväggen](sql-database-firewall-configure.md).
- Mer information om användare och inloggningar finns i [Hantera inloggningar](sql-database-manage-logins.md). 
- En beskrivning av Proaktiv övervakning finns [Database Auditing](sql-database-auditing.md) och [Hotidentifiering för SQL-databasen](sql-database-threat-detection.md).
- En självstudiekurs finns [skydda din Azure SQL Database](sql-database-security-tutorial.md).
