---
title: Azure SQL Database Access Control | Microsoft Docs
description: "Läs om hur man styr åtkomst till Microsoft Azure SQL Database."
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
tags: 
ms.assetid: 8e71b04c-bc38-4153-8f83-f2b14faa31d9
ms.service: sql-database
ms.custom: overview
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 10/18/2016
ms.author: rickbyh
translationtype: Human Translation
ms.sourcegitcommit: 356cc4c6d8e25d36880e4b12bf471326e61990c3
ms.openlocfilehash: f12ed9d76e7c6db5e14ed3c00d7d4087dbd4069c


---
# <a name="azure-sql-database-access-control"></a>Azure SQL Database-åtkomstkontroll
För att erbjuda säkerhet, kontrollerar SQL Database åtkomsten med hjälp av brandväggsregler som begränsar anslutning efter IP-adress, autentiseringsmekanismer kräver att användare bevisar sin identitet och auktoriseringsmekanismer begränsar användare till specifika åtgärder och data. 

> [!IMPORTANT]
> En översikt över säkerhetsfunktionerna i SQL Database finns i [SQL Säkerhetsöversikt](sql-database-security-overview.md). En självstudiekurs om hur du använder SQL Server-autentisering finns i [SQL Database tutorial: SQL Server authentication, logins and user accounts, database roles, permissions, server-level firewall rules, and database-level firewall rules](sql-database-control-access-sql-authentication-get-started.md) (Självstudiekurs om SQL Database: SQL Server-autentisering, inloggningar och användarkonton, databasroller, behörigheter, brandväggsregler på servernivå och brandväggsregler på databasnivå). En självstudiekurs om hur du använder Azure Active Directory-autentisering finns i [SQL Database tutorial: AAD authentication, logins and user accounts, database roles, permissions, server-level firewall rules, and database-level firewall rules](sql-database-control-access-aad-authentication-get-started.md) (Självstudiekurs om SQL Database: AAD-autentisering, inloggningar och användarkonton, databasroller, behörigheter, brandväggsregler på servernivå och brandväggsregler på databasnivå).

>

## <a name="firewall-and-firewall-rules"></a>Brandvägg och brandväggsregler
Microsoft Azure SQL Database tillhandahåller en relationsdatabastjänst för Azure och andra Internetbaserade program. För att skydda dina data förhindrar brandväggar all åtkomst till din databasserver tills du anger vilka datorer som har behörighet. Brandväggen ger åtkomst till databaser baserat på vilken IP-adress som varje begäran kommer från. Mer information finns i [Översikt över Azure SQL Database-brandväggsregler](sql-database-firewall-configure.md)

Azure SQL Database-tjänsten är endast tillgänglig via TCP-port 1433. Se till att klientdatorns brandvägg tillåter utgående TCP-kommunikation på TCP-port 1433 för att komma åt en SQL Database från datorn. Om det inte behövs för andra program, blockera inkommande anslutningar på TCP-port 1433. 

Som en del av anslutningen omdirigeras anslutningar från virtuella Azure-datorer till en annan IP-adress och port, som är unik för varje arbetsroll. Portnumret är i intervallet 11000 till 11999. Mer information om TCP-portar finns [Portar utanför 1433 för ADO.NET 4.5 och SQL Database V12](sql-database-develop-direct-route-ports-adonet-v12.md).

## <a name="authentication"></a>Autentisering

SQL Database stöder två typer av autentisering:

* **SQL-autentisering**, som använder ett användarnamn och lösenord. När du skapade den logiska servern för databasen angav du en "serveradministratörsinloggning” med ett användarnamn och lösenord. Med dessa autentiseringsuppgifter kan du autentisera till en databas på servern som databasens ägare eller "dbo." 
* **Azure Active Directory-autentisering**, som använder identiteter som hanteras av Azure Active Directory och stöder hanterade och integrerade domäner. Använd Active Directory-autentisering (integrerad säkerhet) [närhelst det går](https://msdn.microsoft.com/library/ms144284.aspx). Om du vill använda Azure Active Directory-autentisering måste du skapa en annan serveradministratör som kallas "Azure AD-admin," som tillåts administrera Azure AD-användare och -grupper. Den här administratören kan också utföra alla åtgärder som en vanlig serveradministratören kan. Se [Ansluta till SQL Database genom att använda Azure Active Directory-autentisering](sql-database-aad-authentication.md) för en genomgång av hur du skapar en Azure AD-administratör för att aktivera Azure Active Directory-autentisering.

Databasmotorn stänger anslutningar som är inaktiva under mer än 30 minuter. Anslutningen måste logga in igen innan den kan användas. Kontinuerligt aktiva anslutningar till SQL Database kräver omauktorisering (utförs av databasmotorn) minst var 10:e timma. Databasmotorn försöker omauktorisera det ursprungligen skickade lösenordet och inga indata från användaren krävs. Av prestandaskäl omauktoriseras inte anslutningen när ett lösenord återställs i SQL Database, även om anslutningen har återställts på grund av anslutningspoolning. Detta skiljer sig från beteendet för lokal SQL Server. Om lösenordet har ändrats sedan anslutningen ursprungligen auktoriserades, måste anslutningen avslutas och en ny anslutning skapas med det nya lösenordet. En användare med behörigheten KILL DATABASE CONNECTION kan explicit avsluta en anslutning till SQL Database med hjälp av kommandot [KILL](https://msdn.microsoft.com/library/ms173730.aspx).

Användarkonton kan skapas i huvuddatabasen och kan tilldelas behörigheter i alla databaser på servern, eller så kan de skapas i själva databasen (kallas inneslutna användare). För information om hur man skapar och hanterar inloggningar, se [Hantera inloggningar](sql-database-manage-logins.md). Använd inneslutna databasanvändare för att förbättra portabilitet och skalbarhet. Mer information om inneslutna användare finns i [Inneslutna databasanvändare – Gör din databas portabel](https://msdn.microsoft.com/library/ff929188.aspx), [Skapa användare (Transact-SQL)](https://technet.microsoft.com/library/ms173463.aspx) och [Inneslutna databaser](https://technet.microsoft.com/library/ff929071.aspx).

Ett bra tips är att ditt program bör använda ett annat konto för autentisering – på det här sättet kan du begränsa behörigheterna för programmet och minska riskerna för skadlig programvara om din programkod är sårbar för ett SQL-angrepp. Den rekommenderade metoden är att skapa en [innesluten databasanvändare](https://msdn.microsoft.com/library/ff929188), vilket gör att din app kan autentisera direkt till databasen. Du kan skapa en innesluten databasanvändare som använder SQL-autentisering genom att köra följande T-SQL-kommando när du är ansluten till databasen som serveradministratör:

## <a name="authorization"></a>Auktorisering

Auktorisering hänvisar till vad en användare kan göra inom en Azure SQL-databas och det styrs av ditt användarkontos databas [rollmedlemskap](https://msdn.microsoft.com/library/ms189121) och [behörigheter på objektnivå](https://msdn.microsoft.com/library/ms191291.aspx). Ett bra tips är att du ska ge användare så få behörigheter som möjligt. Serveradministratörskontot som du ansluter med är medlem i db_owner som har behörighet att göra vad som helst i databasen. Spara det här kontot för att distribuera schemauppgraderingar och andra hanteringsåtgärder. Använd kontot "ApplicationUser" med mer begränsade behörigheter för att ansluta från ditt program till databasen med den minsta behörigheten som krävs av programmet. Mer information finns i [Hantera inloggningar](sql-database-manage-logins.md).

Normalt behöver endast administratörer ha åtkomst till huvuddatabasen. Rutinåtkomst till varje användardatabas ska vara via oberoende databasanvändare som inte är administratör, som skapats i varje databas. När du använder oberoende databasanvändare behöver du inte skapa inloggningar i huvuddatabasen. Mer information finns i [Oberoende databasanvändare – göra databasen portabel](https://msdn.microsoft.com/library/ff929188.aspx).

De här funktionerna kan dessutom användas för att begränsa eller höja behörigheter.

* [Personifiering](https://msdn.microsoft.com/library/vstudio/bb669087) och [modulsignering](https://msdn.microsoft.com/library/bb669102) kan användas för att säkert höja behörigheter tillfälligt.
* [Säkerhet på radnivå](https://msdn.microsoft.com/library/dn765131) kan användas som en begränsning av vilka rader en användare kan komma åt.
* [Datamaskning](sql-database-dynamic-data-masking-get-started.md) kan användas för att begränsa exponering av känsliga data.
* [Lagrade procedurer](https://msdn.microsoft.com/library/ms190782) kan användas för att begränsa de åtgärder som kan utföras i databasen.

## <a name="next-steps"></a>Nästa steg

- En översikt över alla säkerhetsfunktioner i SQL Database finns i [SQL Säkerhetsöversikt](sql-database-security-overview.md).
- Om du vill veta mer om brandväggsregler, se [Azure SQL Database-brandväggen](sql-database-firewall-configure.md).
- Mer information om användare och inloggningar finns i [Hantera inloggningar](sql-database-manage-logins.md). 
- En beskrivning av användningen av funktioner för dataskydd i SQL Database finns i [Dataskydd och säkerhet](sql-database-protect-data.md).
- För en diskussion om proaktiv övervakning, se [Kom igång med SQL Database-granskning](sql-database-auditing-get-started.md) och [Kom igång med SQL Database-hotidentifiering](sql-database-threat-detection-get-started.md).
- En självstudiekurs om hur du använder SQL Server-autentisering finns i [SQL Database tutorial: SQL Server authentication, logins and user accounts, database roles, permissions, server-level firewall rules, and database-level firewall rules](sql-database-control-access-sql-authentication-get-started.md) (Självstudiekurs om SQL Database: SQL Server-autentisering, inloggningar och användarkonton, databasroller, behörigheter, brandväggsregler på servernivå och brandväggsregler på databasnivå).
- En självstudiekurs om hur du använder Azure Active Directory-autentisering finns i [SQL Database tutorial: AAD authentication, logins and user accounts, database roles, permissions, server-level firewall rules, and database-level firewall rules](sql-database-control-access-aad-authentication-get-started.md) (Självstudiekurs om SQL Database: AAD-autentisering, inloggningar och användarkonton, databasroller, behörigheter, brandväggsregler på servernivå och brandväggsregler på databasnivå).



<!--HONumber=Jan17_HO3-->


