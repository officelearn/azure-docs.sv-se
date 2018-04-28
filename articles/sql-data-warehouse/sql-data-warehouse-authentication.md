---
title: Autentisera till Azure SQL Data Warehouse | Microsoft Docs
description: Lär dig hur du autentiserar till Azure SQL Data Warehouse med hjälp av Azure Active Directory (AAD) eller SQL Server-autentisering.
services: sql-data-warehouse
author: kavithaj
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/12/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: 173bc797cb6436decddb68aaf1599ea7a6dd597e
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
---
# <a name="authenticate-to-azure-sql-data-warehouse"></a>Autentisera till Azure SQL Data Warehouse
Lär dig hur du autentiserar till Azure SQL Data Warehouse med hjälp av Azure Active Directory (AAD) eller SQL Server-autentisering.

För att ansluta till SQL Data Warehouse, måste du ange säkerhetsreferenser för autentisering. Vissa inställningar är konfigurerade som en del av upprätta sessionen frågan när en anslutning.  

Mer information om säkerhet och hur du aktiverar anslutningar till data warehouse finns [skydda en databas i SQL Data Warehouse][Secure a database in SQL Data Warehouse].

## <a name="sql-authentication"></a>SQL-autentisering
För att ansluta till SQL Data Warehouse, måste du ange följande information:

* Fullständigt kvalificerade servernamn
* Ange SQL-autentisering
* Användarnamn
* Lösenord
* Standarddatabasen (valfritt)

Din anslutning ansluter som standard till den *master* databasen och inte databasen. Om du vill ansluta till databasen, kan du göra något av följande:

* Ange standarddatabas när du registrerar din server med SQL Server Object Explorer i SSDT SSMS, eller i anslutningssträngen program. Till exempel inkludera parametern InitialCatalog för en ODBC-anslutning.
* Markera databasen innan du skapar en session i SSDT.

> [!NOTE]
> Transact-SQL-instruktionen **Använd mindatabas;** stöds inte för att ändra databasen för en anslutning. För hjälp med att ansluta till SQL Data Warehouse med SSDT, referera till den [fråga med Visual Studio] [ Query with Visual Studio] artikel.
> 
> 

## <a name="azure-active-directory-aad-authentication"></a>Azure Active Directory (AAD)-autentisering
[Azure Active Directory] [ What is Azure Active Directory] autentisering är en mekanism för anslutning till Microsoft Azure SQL Data Warehouse med hjälp av identiteter i Azure Active Directory (AD Azure). Med Azure Active Directory-autentisering, kan du centralt hantera identiteter för databasanvändare och andra Microsoft-tjänster på en central plats. Central ID-hantering ger en enda plats för att hantera användare för SQL Data Warehouse och förenklar hantering av behörighet. 

### <a name="benefits"></a>Fördelar
Azure fördelar Active Directory:

* Är ett alternativ till SQL Server-autentisering.
* Hjälper till att stoppa spridning av användaridentiteter över databasservrar.
* Tillåter lösenord rotation i en enda plats
* Hantera databasbehörigheter använder externa (AAD)-grupper.
* Eliminerar lagra lösenord genom att aktivera integrerad Windows-autentisering och andra former av autentisering som stöds av Azure Active Directory.
* Använder finns databasanvändare att autentisera identiteter på databasnivå.
* Stöder tokenbaserad autentisering för program som ansluter till SQL Data Warehouse.
* Stöder multifaktorautentisering via Active Directory Universal autentisering för SQL Server Management Studio. En beskrivning av Multi-Factor Authentication finns [SSMS stöd för Azure AD MFA med SQL Database och SQL Data Warehouse](../sql-database/sql-database-ssms-mfa-authentication.md).

> [!NOTE]
> Azure Active Directory är fortfarande relativt nytt och har vissa begränsningar. För att säkerställa att Azure Active Directory passar bra för din miljö, se [Azure AD-funktioner och begränsningar][Azure AD features and limitations], särskilt ytterligare överväganden.
> 
> 

### <a name="configuration-steps"></a>Konfigurationssteg
Följ dessa steg för att konfigurera Azure Active Directory-autentisering.

1. Skapa och fylla i ett Azure Active Directory
2. Valfritt: Koppla eller ändra active directory som associeras med din Azure-prenumeration
3. Skapa en Azure Active Directory-administratör för Azure SQL Data Warehouse.
4. Konfigurera klientdatorer
5. Skapa oberoende databasanvändare i din databas som mappats till Azure AD identiteter
6. Ansluta till ditt data warehouse med hjälp av Azure AD identiteter

Azure Active Directory-användare visas för närvarande inte i SSDT Object Explorer. Som en tillfällig lösning kan du visa användarna i [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx).

### <a name="find-the-details"></a>Hitta information
* Stegen för att konfigurera och använda Azure Active Directory-autentisering är nästan identisk för Azure SQL Database och Azure SQL Data Warehouse. Följ stegen i avsnittet detaljerad [ansluta till SQL Database eller SQL Data Warehouse med hjälp av Azure Active Directory Authentication](../sql-database/sql-database-aad-authentication.md).
* Skapa anpassade databasroller och lägga till användare till roller. Ge detaljerade behörigheter till roller. Mer information finns i [komma igång med motorn databasbehörighet](https://msdn.microsoft.com/library/mt667986.aspx).

## <a name="next-steps"></a>Nästa steg
Om du vill starta frågar ditt data warehouse med Visual Studio och andra program, se [fråga med Visual Studio][Query with Visual Studio].

<!-- Article references -->
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md
[Query with Visual Studio]: ./sql-data-warehouse-query-visual-studio.md
[What is Azure Active Directory]: ../active-directory/active-directory-whatis.md
[Azure AD features and limitations]: ../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations
