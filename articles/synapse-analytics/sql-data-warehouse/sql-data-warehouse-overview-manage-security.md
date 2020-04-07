---
title: Skydda en databas
description: Tips för att skydda en databas och utveckla lösningar i en Synapse SQL-poolresurs.
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tags: azure-synapse
ms.openlocfilehash: 0c30294f2ca139a602074a980810e7c6737c4e2d
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80742984"
---
# <a name="secure-a-database-in-azure-synapse"></a>Skydda en databas i Azure Synapse

> [!div class="op_single_selector"]
>
> * [Översikt över säkerhet](sql-data-warehouse-overview-manage-security.md)
> * [Autentisering](sql-data-warehouse-authentication.md)
> * [Kryptering (portal)](sql-data-warehouse-encryption-tde.md)
> * [Kryptering (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

Den här artikeln kommer att gå igenom grunderna för att skydda din Synapse SQL-pool. I den här artikeln kommer du i synnerhet igång med resurser för att begränsa åtkomst, skydda data och övervaka aktiviteter på en databas som etablerats med SQL-pool.

## <a name="connection-security"></a>Anslutningssäkerhet

Anslutningssäkerhet avser hur du begränsar och säkrar anslutningar till databasen med hjälp av brandväggsregler och krypterad anslutning.

Brandväggsregler används av både servern och databasen för att avvisa anslutningsförsök från IP-adresser som inte uttryckligen har vitlistats. Om du vill tillåta anslutningar från programmets eller klientdatorns offentliga IP-adress måste du först skapa en brandväggsregel på servernivå med Azure-portalen, REST API eller PowerShell.

Ett bra tips är att du begränsar de IP-adressintervall som tillåts via serverbrandväggen så mycket som möjligt.  Om du vill komma åt SQL-poolen från den lokala datorn kontrollerar du att brandväggen i nätverket och den lokala datorn tillåter utgående kommunikation på TCP-port 1433.  

Azure Synapse Analytics använder IP-brandväggsregler på servernivå. Det stöder inte ip-brandväggsregler på databasnivå. Mer information finns i [Azure SQL Database-brandväggsregler](../../sql-database/sql-database-firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

Anslutningar till SQL-poolen krypteras som standard.  Ändra anslutningsinställningar för att inaktivera kryptering ignoreras.

## <a name="authentication"></a>Autentisering

Autentisering refererar till hur du styrkt din identitet vid anslutning till databasen. SQL-pool stöder för närvarande SQL Server-autentisering med ett användarnamn och lösenord och med Azure Active Directory.

När du skapade den logiska servern för databasen angav du en "serveradministratörsinloggning” med ett användarnamn och lösenord. Med hjälp av dessa autentiseringsuppgifter kan du autentisera till valfri databas på den servern som databasägare, eller "dbo" via SQL Server-autentisering.

Som bästa praxis bör dock organisationens användare använda ett annat konto för att autentisera. På så sätt kan du begränsa de behörigheter som beviljas till programmet och minska riskerna för skadlig aktivitet om programkoden är sårbar för en SQL-injektionsattack.

Om du vill skapa en SQL Server-autentiserade användare ansluter du till **huvuddatabasen** på servern med serveradministratörsinloggningen och skapar en ny serverinloggning.  Det är en bra idé att också skapa en användare i huvuddatabasen. Genom att skapa en användare i huvudverktyget kan en användare logga in med verktyg som SSMS utan att ange ett databasnamn.  Det gör det också möjligt för dem att använda objektet explorer för att visa alla databaser på en SQL-server.

```sql
-- Connect to master database and create a login
CREATE LOGIN ApplicationLogin WITH PASSWORD = 'Str0ng_password';
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Anslut sedan till **SQL-pooldatabasen** med serveradministratörsinloggningen och skapa en databasanvändare baserat på serverinloggningen du skapade.

```sql
-- Connect to the database and create a database user
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Om du vill ge en användare behörighet att utföra ytterligare åtgärder, till `Loginmanager` exempel `dbmanager` skapa inloggningar eller skapa nya databaser, tilldelar du användaren till och roller i huvuddatabasen.

Mer information om dessa ytterligare roller och autentisering till en SQL-databas finns [i Hantera databaser och inloggningar i Azure SQL Database](../../sql-database/sql-database-manage-logins.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).  Mer information om hur du ansluter med Azure Active Directory finns i [Ansluta med hjälp av Azure Active Directory Authentication](sql-data-warehouse-authentication.md).

## <a name="authorization"></a>Auktorisering

Auktorisering avser vad du kan göra i en databas när du har autentiserats och anslutits. Behörigheter för auktorisering bestäms av rollmedlemskap och behörigheter. Ett bra tips är att du ska ge användare så få behörigheter som möjligt. Om du vill hantera roller kan du använda följande lagrade procedurer:

```sql
EXEC sp_addrolemember 'db_datareader', 'ApplicationUser'; -- allows ApplicationUser to read data
EXEC sp_addrolemember 'db_datawriter', 'ApplicationUser'; -- allows ApplicationUser to write data
```

Serveradministratörskontot som du ansluter med är medlem i db_owner som har behörighet att göra vad som helst i databasen. Spara det här kontot för att distribuera schemauppgraderingar och andra hanteringsåtgärder. Använd kontot "ApplicationUser" med mer begränsade behörigheter för att ansluta från ditt program till databasen med den minsta behörigheten som krävs av programmet.

Det finns sätt att ytterligare begränsa vad en användare kan göra i databasen:

* Med detaljerade [behörigheter](/sql/relational-databases/security/permissions-database-engine?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) kan du styra vilka åtgärder du kan utföra på enskilda kolumner, tabeller, vyer, scheman, procedurer och andra objekt i databasen. Använd detaljerade behörigheter för att ha mest kontroll och bevilja de minsta behörigheter som krävs.
* [Andra databasroller](/sql/relational-databases/security/authentication-access/database-level-roles?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) än db_datareader och db_datawriter kan användas för att skapa mer kraftfulla programanvändarkonton eller mindre kraftfulla hanteringskonton. De inbyggda fasta databasrollerna är ett enkelt sätt att bevilja behörigheter, men kan resultera i att fler behörigheter beviljas än vad som krävs.
* [Lagrade procedurer](/sql/relational-databases/stored-procedures/stored-procedures-database-engine?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) kan användas för att begränsa de åtgärder som kan utföras i databasen.

I följande exempel får du läsbehörighet till ett användardefinierat schema.

```sql
--CREATE SCHEMA Test
GRANT SELECT ON SCHEMA::Test to ApplicationUser
```

Hantera databaser och logiska servrar från Azure-portalen eller använda Azure Resource Manager API styrs av portalanvändarkontots rolltilldelningar. Mer information finns [i Rollbaserad åtkomstkontroll i Azure Portal](../../role-based-access-control/role-assignments-portal.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

## <a name="encryption"></a>Kryptering

Transparent datakryptering (TDE) hjälper till att skydda mot hotet om skadlig aktivitet genom att kryptera och dekryptera dina data i vila. När du krypterar databasen krypteras associerade säkerhetskopior och transaktionsloggfiler utan att dina program ändras. Transparent datakryptering (TDE) krypterar lagringen av en hel databas med hjälp av en symmetrisk nyckel kallad databaskrypteringsnyckeln.

I SQL Database skyddas databaskrypteringsnyckeln av ett inbyggt servercertifikat. Det inbyggda servercertifikatet är unikt för varje SQL Database-server. Microsoft roterar automatiskt dessa certifikat minst var 90:e dag. Krypteringsalgoritmen som används är AES-256. En allmän beskrivning av TDE finns i [Transparent datakryptering](/sql/relational-databases/security/encryption/transparent-data-encryption?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

Du kan kryptera databasen med [Azure-portalen](sql-data-warehouse-encryption-tde.md) eller [T-SQL](sql-data-warehouse-encryption-tde-tsql.md).

## <a name="next-steps"></a>Nästa steg

Mer information och exempel på hur du ansluter till ditt lagerställe med olika protokoll finns i [Anslut till SQL-pool](sql-data-warehouse-connect-overview.md).
