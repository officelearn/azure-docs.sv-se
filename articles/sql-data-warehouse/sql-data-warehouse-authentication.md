---
title: Autentisering
description: Lär dig hur du autentiserar till Azure SQL Data Warehouse genom att använda Azure Active Directory (AAD) eller SQL Server autentisering.
services: sql-data-warehouse
author: julieMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 04/02/2019
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: fda29e432fbd952261893f3c32a4df7b9990ae66
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692928"
---
# <a name="authenticate-to-azure-sql-data-warehouse"></a>Autentisera till Azure SQL Data Warehouse
Lär dig hur du autentiserar till Azure SQL Data Warehouse genom att använda Azure Active Directory (AAD) eller SQL Server autentisering.

Om du vill ansluta till SQL Data Warehouse måste du skicka säkerhets uppgifter för autentisering. När du upprättar en anslutning konfigureras vissa anslutnings inställningar som en del av upprättandet av sessionen.  

Mer information om säkerhet och hur du aktiverar anslutningar till data lagret finns [i skydda en databas i SQL Data Warehouse][Secure a database in SQL Data Warehouse].

## <a name="sql-authentication"></a>SQL-autentisering
För att ansluta till SQL Data Warehouse måste du ange följande information:

* Fullständigt kvalificerat Server namn
* Ange SQL-autentisering
* Användarnamn
* Lösenord
* Standard databas (valfritt)

Som standard ansluter anslutningen till *huvud* databasen och inte till din användar databas. Om du vill ansluta till din användar databas kan du välja att göra något av följande:

* Ange standard databasen när du registrerar servern med SQL Server Object Explorer i SSDT, SSMS eller i din program anslutnings sträng. Inkludera till exempel parametern InitialCatalog för en ODBC-anslutning.
* Markera användar databasen innan du skapar en session i SSDT.

> [!NOTE]
> Transact-SQL-instruktionen **använder-databasen;** stöds inte för att ändra databasen för en anslutning. Anvisningar om hur du ansluter till SQL Data Warehouse med SSDT finns i artikeln [fråga med Visual Studio][Query with Visual Studio] .
> 
> 

## <a name="azure-active-directory-aad-authentication"></a>Azure Active Directory autentisering (AAD)
[Azure Active Directory][What is Azure Active Directory] autentisering är en mekanism för att ansluta till Microsoft Azure SQL Data Warehouse med hjälp av identiteter i Azure Active Directory (Azure AD). Med Azure Active Directory autentisering kan du centralt hantera identiteter för databas användare och andra Microsoft-tjänster på en central plats. Hantering av centrala ID ger en enda plats för att hantera SQL Data Warehouse användare och fören klar behörighets hanteringen. 

### <a name="benefits"></a>Fördelar
Azure Active Directory fördelarna är:

* Är ett alternativ till att SQL Server autentisering.
* Hjälper till att stoppa spridningen av användar identiteter mellan databas servrar.
* Lösenord kan roteras på ett och samma ställe
* Hantera databas behörigheter med hjälp av externa grupper (AAD).
* Eliminerar lagring av lösen ord genom att aktivera integrerad Windows-autentisering och andra former av autentisering som stöds av Azure Active Directory.
* Använder inneslutna databas användare för att autentisera identiteter på databas nivå.
* Stöder tokenbaserad autentisering för program som ansluter till SQL Data Warehouse.
* Stöder Multi-Factor Authentication via Active Directory Universal Authentication för olika verktyg, inklusive [SQL Server Management Studio](../sql-database/sql-database-ssms-mfa-authentication.md) och [SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/azure-active-directory?toc=/azure/sql-data-warehouse/toc.json).

> [!NOTE]
> Azure Active Directory är fortfarande relativt nytt och har vissa begränsningar. För att säkerställa att Azure Active Directory passar bra för din miljö kan du läsa mer i [Azure AD-funktioner och begränsningar][Azure AD features and limitations], särskilt ytterligare överväganden.
> 
> 

### <a name="configuration-steps"></a>Konfigurationssteg
Följ dessa steg om du vill konfigurera Azure Active Directory autentisering.

1. Skapa och fylla i en Azure Active Directory
2. Valfritt: associera eller ändra Active Directory som för närvarande är associerat med din Azure-prenumeration
3. Skapa en Azure Active Directory administratör för Azure SQL Data Warehouse.
4. Konfigurera klient datorerna
5. Skapa inneslutna databas användare i databasen som har mappats till Azure AD-identiteter
6. Ansluta till informations lagret med hjälp av Azure AD-identiteter

Azure Active Directory användare visas för närvarande inte i SSDT Object Explorer. Som en lösning kan du Visa användarna i [sys. database_principals](https://msdn.microsoft.com/library/ms187328.aspx).

### <a name="find-the-details"></a>Hitta informationen
* Stegen för att konfigurera och använda Azure Active Directory autentisering är nästan identiska för Azure SQL Database och Azure SQL Data Warehouse. Följ de detaljerade stegen i avsnittet [ansluta till SQL Database eller SQL Data Warehouse med Azure Active Directory autentisering](../sql-database/sql-database-aad-authentication.md).
* Skapa anpassade databas roller och Lägg till användare i rollerna. Ge sedan detaljerade behörigheter till rollerna. Mer information finns i [komma igång med databas motor behörigheter](https://msdn.microsoft.com/library/mt667986.aspx).

## <a name="next-steps"></a>Nästa steg
Om du vill börja ställa frågor till datalagret med Visual Studio och andra program hittar du mer information i [Fråga med Visual Studio][Query with Visual Studio].

<!-- Article references -->
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md
[Query with Visual Studio]: ./sql-data-warehouse-query-visual-studio.md
[What is Azure Active Directory]:../active-directory/fundamentals/active-directory-whatis.md
[Azure AD features and limitations]: ../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations
