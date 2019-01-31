---
title: Autentisera till Azure SQL Data Warehouse | Microsoft Docs
description: Lär dig hur du autentiserar till Azure SQL Data Warehouse med hjälp av Azure Active Directory (AAD) eller SQL Server-autentisering.
services: sql-data-warehouse
author: KavithaJonnakuti
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/12/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: 7f26ded5fae1bde5b2e6485ae2a2f99eebb17563
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55463336"
---
# <a name="authenticate-to-azure-sql-data-warehouse"></a>Autentisera till Azure SQL Data Warehouse
Lär dig hur du autentiserar till Azure SQL Data Warehouse med hjälp av Azure Active Directory (AAD) eller SQL Server-autentisering.

För att ansluta till SQL Data Warehouse, måste du skicka in säkerhetsreferenser för autentisering. Vid upprättar en anslutning, kan vissa anslutning konfigureras som en del av upprätta sessionen fråga.  

Läs mer om säkerhet och hur du aktiverar anslutningar till ditt datalager, [skydda en databas i SQL Data Warehouse][Secure a database in SQL Data Warehouse].

## <a name="sql-authentication"></a>SQL-autentisering
För att ansluta till SQL Data Warehouse, måste du ange följande information:

* Fullständigt kvalificerat servernamn
* Ange SQL-autentisering
* Användarnamn
* Lösenord
* Standarddatabas (valfritt)

Som standard ansluter din anslutning till den *master* databasen och inte databasen. För att ansluta till databasen, kan du göra något av följande:

* Ange standarddatabas när du registrerar din server med SQL Server Object Explorer i SSDT och SSMS, eller i anslutningssträngen för programmet. Till exempel inkluderar du parametern InitialCatalog för en ODBC-anslutning.
* Markera databasen innan du skapar en session i SSDT.

> [!NOTE]
> Transact-SQL-instruktionen **Använd MyDatabase;** stöds inte för att ändra databasen för en anslutning. Riktlinjer som ansluter till SQL Data Warehouse med SSDT, referera till den [fråga med Visual Studio] [ Query with Visual Studio] artikeln.
> 
> 

## <a name="azure-active-directory-aad-authentication"></a>Azure Active Directory (AAD)-autentisering
[Azure Active Directory] [ What is Azure Active Directory] autentisering är en mekanism för att ansluta till Microsoft Azure SQL Data Warehouse med hjälp av identiteter i Azure Active Directory (AD Azure). Med Azure Active Directory-autentisering, kan du centralt hantera identiteter för databasanvändare och andra Microsoft-tjänster på en central plats. Central hantering av ID innehåller en enda plats för att hantera användare för SQL Data Warehouse och förenklar hanteringen av behörighet. 

### <a name="benefits"></a>Fördelar
Azure fördelar Active Directory:

* Är ett alternativ till SQL Server-autentisering.
* Hjälper dig att stoppa spridning av användaridentiteter över databasservrar.
* Tillåter lösenordsrotation i en enda plats
* Hantera databasbehörigheter med externa (AAD)-grupper.
* Eliminerar lagra lösenord genom att aktivera integrerad Windows-autentisering och andra former av autentisering som stöds av Azure Active Directory.
* Använder innesluten databasanvändare för att autentisera identiteter på databasnivå.
* Har stöd för tokenbaserad autentisering för program som ansluter till SQL Data Warehouse.
* Stöder multifaktorautentisering via Active Directory Universal-autentisering för SQL Server Management Studio. En beskrivning av Multifaktorautentisering finns i [SSMS-stöd för Azure AD MFA med SQL Database och SQL Data Warehouse](../sql-database/sql-database-ssms-mfa-authentication.md).

> [!NOTE]
> Azure Active Directory är fortfarande relativt nytt och har vissa begränsningar. För att säkerställa att Azure Active Directory är ett bra alternativ för din miljö, se [Azure AD-funktioner och begränsningar][Azure AD features and limitations], särskilt ytterligare överväganden.
> 
> 

### <a name="configuration-steps"></a>Konfigurationssteg
Följ dessa steg för att konfigurera Azure Active Directory-autentisering.

1. Skapa och fylla ett Azure Active Directory
2. Valfritt: Koppla eller ändra active directory som är associerade till din Azure-prenumeration
3. Skapa en Azure Active Directory-administratör för Azure SQL Data Warehouse.
4. Konfigurera klientdatorer
5. Skapa oberoende databasanvändare i din databas som mappats till Azure AD-identiteter
6. Ansluta till data warehouse med hjälp av Azure AD-identiteter

Azure Active Directory-användare visas för närvarande inte i SSDT Object Explorer. Som en lösning kan du visa användarna i [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx).

### <a name="find-the-details"></a>Få information om
* Stegen för att konfigurera och använda Azure Active Directory-autentisering är nästan identiska för Azure SQL Database och Azure SQL Data Warehouse. Följ de detaljerade anvisningarna i avsnittet [ansluta till SQL Database eller SQL Data Warehouse med hjälp av Azure Active Directory-autentisering](../sql-database/sql-database-aad-authentication.md).
* Skapa anpassade databasroller och lägga till användare till databasrollerna. Sedan tilldela detaljerade behörigheter i rollerna. Mer information finns i [komma igång med motorn databasbehörigheter](https://msdn.microsoft.com/library/mt667986.aspx).

## <a name="next-steps"></a>Nästa steg
Om du vill börja ställa frågor till ditt data warehouse med Visual Studio och andra program, se [fråga med Visual Studio][Query with Visual Studio].

<!-- Article references -->
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md
[Query with Visual Studio]: ./sql-data-warehouse-query-visual-studio.md
[What is Azure Active Directory]:../active-directory/fundamentals/active-directory-whatis.md
[Azure AD features and limitations]: ../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations
