---
title: Autentisering
description: Lär dig hur du autentiserar till Azure Synapse Analytics med hjälp av Azure Active Directory (Azure AD) eller SQL Server-autentisering.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/02/2019
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tag: azure-synapse
ms.openlocfilehash: ed8dd902ac490b4e6a0f172029bf3ffa1d44acee
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81251851"
---
# <a name="authenticate-to-azure-synapse-analytics"></a>Autentisera till Azure Synapse Analytics

Lär dig hur du autentiserar till SQL Analytics i Azure Synapse med hjälp av Azure Active Directory (AAD) eller SQL Server-autentisering.

Om du vill ansluta till en SQL-pool måste du skicka in säkerhetsreferenser för autentiseringsändamål. När du upprättar en anslutning konfigureras vissa anslutningsinställningar som en del av konfigurationen av frågesessionen.  

Mer information om säkerhet och hur du aktiverar anslutningar till ditt informationslager finns i [skydda en databasdokumentation](sql-data-warehouse-overview-manage-security.md).

## <a name="sql-authentication"></a>SQL-autentisering

Om du vill ansluta till SQL-poolen måste du ange följande information:

* Fullständigt kvalificerat servernamn
* Ange SQL-autentisering
* Användarnamn
* lösenord
* Standarddatabas (valfritt)

Som standard ansluter anslutningen till *huvuddatabasen* och inte till användardatabasen. Om du vill ansluta till användardatabasen kan du välja att göra en av två saker:

* Ange standarddatabasen när du registrerar servern med SQL Server Object Explorer i SSDT, SSMS eller i programanslutningssträngen. Inkludera till exempel parametern InitialCatalog för en ODBC-anslutning.
* Markera användardatabasen innan du skapar en session i SSDT.

> [!NOTE]
> Transact-SQL-uttrycket **USE MyDatabase;** stöds inte för att ändra databasen för en anslutning. Instruktioner för anslutning till en SQL-pool med SSDT finns i artikeln [Fråga med Visual Studio.](sql-data-warehouse-query-visual-studio.md)

## <a name="azure-active-directory-aad-authentication"></a>AAD-autentisering (Azure Active Directory)

[Azure Active](../../active-directory/fundamentals/active-directory-whatis.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) Directory-autentisering är en mekanism för anslutning till SQL-pool med hjälp av identiteter i Azure Active Directory (Azure AD). Med Azure Active Directory-autentisering kan du centralt hantera identiteterna för databasanvändare och andra Microsoft-tjänster på en central plats. Central ID-hantering är en enda plats för att hantera Azure Synapse-användare och förenklar behörighetshantering.

### <a name="benefits"></a>Fördelar

Azure Active Directory-förmåner inkluderar:

* Ger ett alternativ till SQL Server-autentisering.
* Hjälper till att stoppa spridningen av användaridentiteter över databasservrar.
* Lösenord kan roteras på ett och samma ställe
* Hantera databasbehörigheter med externa (Azure AD)-grupper.
* Eliminerar lagring av lösenord genom att aktivera integrerad Windows-autentisering och andra former av autentisering som stöds av Azure Active Directory.
* Använder innehållna databasanvändare för att autentisera identiteter på databasnivå.
* Stöder tokenbaserad autentisering för program som ansluter till SQL-pool.
* Stöder multifaktorautentisering via Universell Active Directory-autentisering för olika verktyg, inklusive [SQL Server Management Studio](../../sql-database/sql-database-ssms-mfa-authentication.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) och SQL Server Data [Tools](/sql/ssdt/azure-active-directory?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

> [!NOTE]
> Azure Active Directory är fortfarande relativt ny och har vissa begränsningar. För att säkerställa att Azure Active Directory passar din miljö, se [Azure AD-funktioner och begränsningar](../../sql-database/sql-database-aad-authentication.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#azure-ad-features-and-limitations), särskilt ytterligare överväganden.

### <a name="configuration-steps"></a>Konfigurationssteg

Följ dessa steg för att konfigurera Azure Active Directory-autentisering.

1. Skapa och fylla i en Azure Active Directory
2. Valfritt: Associera eller ändra active directory som för närvarande är associerad med din Azure-prenumeration
3. Skapa en Azure Active Directory-administratör för Azure Synapse
4. Konfigurera klientdatorerna
5. Skapa innehållna databasanvändare i databasen mappade till Azure AD-identiteter
6. Ansluta till din SQL-pool med hjälp av Azure AD-identiteter

Azure Active Directory-användare visas inte i Utforskaren för SSDT-objekt. Som en lösning visar du användarna i [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

### <a name="find-the-details"></a>Hitta information

* Stegen för att konfigurera och använda Azure Active Directory-autentisering är nästan identiska för Azure SQL Database och SQL Analytics i Azure Synapse. Följ de detaljerade stegen i avsnittet [Ansluta till SQL Database eller SQL Pool med hjälp av Azure Active Directory Authentication](../../sql-database/sql-database-aad-authentication.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
* Skapa anpassade databasroller och lägg till användare i rollerna. Bevilja sedan detaljerade behörigheter till rollerna. Mer information finns i [Komma igång med behörigheter för databasmotor](/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="next-steps"></a>Nästa steg

Mer om du vill börja fråga med Visual Studio och andra program finns i [Fråga med Visual Studio](sql-data-warehouse-query-visual-studio.md).
