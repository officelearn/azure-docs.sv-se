---
title: "Översikt över Azure SQL-databaser | Microsoft Docs"
description: "Den här sidan innehåller en översikt över Azure SQL-databaser."
services: sql-database
documentationcenter: na
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: single databases
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 11/28/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: e5b5751facb68ae4a62e3071fe4dfefc02434a9f
ms.openlocfilehash: 315fb49ba25c46afaa6571d9249ecd1c8da13e91


---
# <a name="azure-sql-database-overview"></a>Översikt över Azure SQL-databaser
Det här avsnittet innehåller en översikt över Azure SQL-databaser. Information om logiska Azure SQL-servrar finns i avsnittet om [logiska servrar](sql-database-server-overview.md).

## <a name="what-is-azure-sql-database"></a>Vad är en Azure SQL-databas?
Varje databas i Azure SQL Database är associerad med en logisk server. Databasen kan vara:

- En enkel databas med dess [uppsättning resurser](sql-database-what-is-a-dtu.md#what-are-database-transaction-units-dtus) (DTU:er)
- En del av en [elastisk pool](sql-database-elastic-pool.md) som [delar en uppsättning resurser](sql-database-what-is-a-dtu.md#what-are-elastic-database-transaction-units-edtus) (eDTU:er)
- En del av en [utskalad uppsättning delade databaser](sql-database-elastic-scale-introduction.md#horizontal-and-vertical-scaling), som kan vara enskilda databaser eller databaser i en pool
- En del av en uppsättning databaser som ingår i ett [SaaS-designmönster för flera klienter](sql-database-design-patterns-multi-tenancy-saas-applications.md), vars databaser kan vara enskilda databaser eller databaser i en pool (eller båda) 

## <a name="how-do-i-connect-and-authenticate-to-an-azure-sql-database"></a>Hur ansluter jag och autentiserar till en Azure SQL-databas?

- **Autentisering och auktorisering**: Azure SQL Database stöder SQL-autentisering och Azure Active Directory-autentisering (med vissa begränsningar – se [Connect to SQL Database with Azure Active Directory Authentication](sql-database-aad-authentication.md) (Ansluta till SQL Database med Azure Active Directory-autentisering)). Du kan ansluta och autentisera till Azure SQL-databaser via serverns huvuddatabas eller direkt till en användardatabas. Mer information finns i [Hantera databaser och inloggningar i Azure SQL Database](sql-database-manage-logins.md). Windows-autentisering stöds inte. 
- **TDS**: Microsoft Azure SQL Database stöder klientversion 7.3 eller senare av TDS-protokollet (Tabular Data Stream).
- **TCP/IP**: Endast TCP/IP-anslutningar tillåts.
- **SQL Database-brandvägg**: En SQL Database-brandvägg skyddar dina data genom att förhindra all åtkomst till databasservern eller dess databaser tills du anger vilka datorer som har behörighet. Mer information finns i avsnittet om [brandväggar](sql-database-firewall-configure.md)

## <a name="what-collations-are-supported"></a>Vilka sorteringar stöds?
Standarddatabassorteringen som används av Microsoft Azure SQL Database är **SQL_LATIN1_GENERAL_CP1_CI_AS**, där **LATIN1_GENERAL** är engelska (USA), **CP1** är teckentabell 1252, **CI** är skiftlägeskänslig och **AS** är accentkänslig. Det går inte att ändra sorteringen för V12-databaser. Mer information om hur du konfigurerar sorteringen finns i [COLLATE (Transact-SQL)](https://msdn.microsoft.com/library/ms184391.aspx).

## <a name="what-are-the-naming-requirements-for-database-objects"></a>Vilka är namngivningskraven för databasobjekt?

Namnen för alla nya objekt måste följa SQL Servers regler för identifierare. Mer information finns i avsnittet om [identifierare](https://msdn.microsoft.com/library/ms175874.aspx).

## <a name="what-features-are-supported-by-azure-sql-databases"></a>Vilka funktioner stöds av Azure SQL-databaser?

Information om vilka funktioner som stöds finns i avsnittet [Funktioner](sql-database-features.md). Mer information om varför vissa funktioner inte stöds finns i avsnittet [Skillnader mellan Azure SQL Database och Transact-SQL](sql-database-transact-sql-information.md).

## <a name="how-do-i-manage-an-azure-sql-database"></a>Hur hanterar jag en Azure SQL-databas?

Du kan hantera logiska Azure SQL Database-servrar på flera olika sätt:
- [Azure-portal](sql-database-manage-portal.md)
- [PowerShell](sql-database-manage-powershell.md)
- [Transact-SQL](sql-database-manage-azure-ssms.md)
- [REST](/rest/api/sql/)

## <a name="next-steps"></a>Nästa steg

- Information om Azure SQL Database-tjänsten finns i [Vad är SQL Database?](sql-database-technical-overview.md)
- Information om vilka funktioner som stöds finns i avsnittet [Funktioner](sql-database-features.md)
- En översikt över logiska Azure SQL-servrar finns i [SQL Database logical server overview](sql-database-server-overview.md) (Översikt över logiska SQL Database-servrar)
- Information om stöd för och skillnader i Transact-SQL finns i [Skillnader mellan Azure SQL Database och Transact-SQL](sql-database-transact-sql-information.md).
- Information om specifika resurskvoter och begränsningar baserat på din **tjänstnivå**. En översikt över tjänstnivåer finns i avsnittet om [tjänstnivåer för SQL Database](sql-database-service-tiers.md).
- En översikt över säkerhet finns i [Azure SQL Database-säkerhetsöversikt](sql-database-security-overview.md).
- Mer information om tillgängliga drivrutiner och stöd för SQL Database finns i [Connection Libraries for SQL Database and SQL Server](sql-database-libraries.md) (Anslutningsbibliotek för SQL Database och SQL Server).




<!--HONumber=Dec16_HO4-->


