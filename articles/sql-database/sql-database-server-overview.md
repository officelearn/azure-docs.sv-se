---
title: "Översikt över logiska Azure SQL Database-servrar | Microsoft Docs"
description: "Den här sidan innehåller riktlinjer och saker som du bör tänka på när du arbetar med logiska Azure SQL-servrar."
services: sql-database
documentationcenter: na
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: servers
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 02/01/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: e5b5751facb68ae4a62e3071fe4dfefc02434a9f
ms.openlocfilehash: 17e2830dceeaa313dd0fd7d406bf68a75b6f900e


---
# <a name="azure-sql-database-logical-servers"></a>Logiska Azure SQL Database-servrar

Det här avsnittet innehåller riktlinjer och saker som du bör tänka på när du arbetar med logiska Azure SQL-servrar. Information om Azure SQL-databaser finns i [SQL-databaser](sql-database-overview.md).

## <a name="what-is-an-azure-sql-database-logical-server"></a>Vad är en logisk Azure SQL Database-server?
En logisk Azure SQL Database-server fungerar som en central administrativ plats för flera databaser. I SQL Database är en server en logisk konstruktion som skiljer sig från en SQL Server-instans, som du kanske är bekant med i den lokala miljön. Mer specifikt ger SQL Database-tjänsten inga garantier avseende platsen för databaserna i förhållande till deras logiska servrar och exponerar inga funktioner eller åtkomst på instansnivå. Mer information om logiska Azure SQL-servrar finns i avsnittet om [logiska servrar](sql-database-server-overview.md). 

En logisk Azure Database-server:

- Skapas i en Azure-prenumeration, men kan flyttas till en annan prenumeration tillsammans med de resurser som den innehåller.
- Är den överordnade resursen för databaser, elastiska pooler och informationslager.
- Tillhandahåller ett namnområde för databaser, elastiska pooler och informationslager.
- Är en logisk behållare med stark livslängdssemantik – om du tar bort en server så tas de databaser, elastiska pooler och informationslager som den innehåller bort.
- Använder Azures rollbaserade åtkomstkontroll (RBAC). Databaser och elastiska pooler på en server ärver åtkomsträttigheter från servern.
- Är det högsta elementet för identiteten för databaser och elastiska pooler vid Azure-resurshantering (se URL-schemat för databaser och pooler).
- Samlar resurser i en region.
- Tillhandahåller en anslutningsslutpunkt för databasåtkomst (<serverName>.database.windows.net)
- Ger åtkomst till metadata för inneslutna resurser via DMV:er genom att ansluta till en huvuddatabas. 
- Tillhandahåller definitionsområdet för hanteringsprinciper som tillämpas på dess databaser: inloggningar, brandvägg, granskning, hotidentifiering osv. 
- Är begränsad av en kvot i den överordnade prenumerationen (sex servrar per prenumeration – [se prenumerationsbegränsningar här](../azure-subscription-service-limits.md)).
- Tillhandahåller definitionsområdet för databaskvoten och DTU-kvoten för de resurser som den innehåller (till exempel 45 000 DTU:er i V12).
- Är versionshanteringsomfånget för funktioner som är aktiverade för de resurser som den innehåller (senaste versionen är V12).
- Huvudkontoinloggningar på servernivå kan hantera alla databaser på en server.
- Kan innehålla inloggningar som liknar de i instanser av SQL Server i din lokala miljö som har åtkomst till en eller flera databaser på servern, samt kan beviljas begränsade administrativa rättigheter. Mer information finns i avsnittet om [inloggningar](sql-database-manage-logins.md).

## <a name="how-do-i-connect-and-authenticate-to-an-azure-sql-database-logical-server"></a>Hur ansluter jag och autentiserar till en logisk Azure SQL Database-server?

- **Autentisering och auktorisering**: Azure SQL Database stöder SQL-autentisering och Azure Active Directory-autentisering (med vissa begränsningar – se [Connect to SQL Database with Azure Active Directory Authentication](sql-database-aad-authentication.md) (Ansluta till SQL Database med Azure Active Directory-autentisering)). Du kan ansluta och autentisera till Azure SQL-databaser via serverns huvuddatabas eller direkt till en användardatabas. Mer information finns i [Hantera databaser och inloggningar i Azure SQL Database](sql-database-manage-logins.md). Windows-autentisering stöds inte. 
- **TDS**: Microsoft Azure SQL Database stöder klientversion 7.3 eller senare av TDS-protokollet (Tabular Data Stream).
- **TCP/IP**: Endast TCP/IP-anslutningar tillåts.
- **SQL Database-brandvägg**: En SQL Database-brandvägg skyddar dina data genom att förhindra all åtkomst till databasservern eller dess databaser tills du anger vilka datorer som har behörighet. Mer information finns i avsnittet om [brandväggar](sql-database-firewall-configure.md)

## <a name="what-collations-are-supported"></a>Vilka sorteringar stöds?

Standarddatabassorteringen som används av Microsoft Azure SQL Database (inklusive huvuddatabasen) är **SQL_LATIN1_GENERAL_CP1_CI_AS**, där **LATIN1_GENERAL** är engelska (USA), **CP1** är teckentabell 1252, **CI** är skiftlägeskänslig och **AS** är accentkänslig. Vi rekommenderar att du inte ändrar sorteringen för V12-databaser när de har skapats. Mer information om sorteringar finns i [COLLATE (Transact-SQL)](https://msdn.microsoft.com/library/ms184391.aspx).

## <a name="what-are-the-naming-requirements-for-database-objects"></a>Vilka är namngivningskraven för databasobjekt?

Namnen för alla nya objekt måste följa SQL Servers regler för identifierare. Mer information finns i avsnittet om [identifierare](https://msdn.microsoft.com/library/ms175874.aspx).

## <a name="what-features-are-supported"></a>Vilka funktioner stöds?

Information om vilka funktioner som stöds finns i avsnittet [Funktioner](sql-database-features.md). Mer information om varför vissa funktioner inte stöds finns i avsnittet [Skillnader mellan Azure SQL Database och Transact-SQL](sql-database-transact-sql-information.md).

## <a name="how-do-i-manage-a-logical-server"></a>Hur hanterar jag en logisk server?

Du kan hantera logiska Azure SQL Database-servrar på flera olika sätt:
- [Azure-portal](sql-database-manage-portal.md)
- [PowerShell](sql-database-manage-powershell.md)
- [REST](/rest/api/sql/)

## <a name="next-steps"></a>Nästa steg

- Information om Azure SQL Database-tjänsten finns i [Vad är SQL Database?](sql-database-technical-overview.md)
- Information om vilka funktioner som stöds finns i avsnittet [Funktioner](sql-database-features.md).
- En översikt över Azure SQL-databaser finns i [Översikt över SQL-databaser](sql-database-overview.md).
- Information om stöd för och skillnader i Transact-SQL finns i [Skillnader mellan Azure SQL Database och Transact-SQL](sql-database-transact-sql-information.md).
- Information om specifika resurskvoter och begränsningar baserat på din **tjänstnivå**. En översikt över tjänstnivåer finns i avsnittet om [tjänstnivåer för SQL Database](sql-database-service-tiers.md).
- En översikt över säkerhet finns i [Azure SQL Database-säkerhetsöversikt](sql-database-security-overview.md).
- Mer information om tillgängliga drivrutiner och stöd för SQL Database finns i [Connection Libraries for SQL Database and SQL Server](sql-database-libraries.md) (Anslutningsbibliotek för SQL Database och SQL Server).




<!--HONumber=Dec16_HO4-->


