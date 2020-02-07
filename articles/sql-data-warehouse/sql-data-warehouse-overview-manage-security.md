---
title: Skydda en databas
description: Tips för att skydda en databas i Azure SQL Data Warehouse för att utveckla lösningar.
services: sql-data-warehouse
author: julieMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 04/17/2018
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 5eeb1c25264c36909774ec689b7410765881c8e2
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2020
ms.locfileid: "77064741"
---
# <a name="secure-a-database-in-sql-data-warehouse"></a>Skydda en databas i SQL Data Warehouse
> [!div class="op_single_selector"]
> * [Säkerhets översikt](sql-data-warehouse-overview-manage-security.md)
> * [Autentisering](sql-data-warehouse-authentication.md)
> * [Kryptering (portal)](sql-data-warehouse-encryption-tde.md)
> * [Kryptering (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

Den här artikeln vägleder dig genom grunderna för att skydda Azure SQL Data Warehouse-databasen. I synnerhet hjälper den här artikeln dig att komma igång med resurser för att begränsa åtkomst, skydda data och övervaka aktiviteter på en databas.

## <a name="connection-security"></a>Anslutningssäkerhet
Anslutningssäkerhet avser hur du begränsar och säkrar anslutningar till databasen med hjälp av brandväggsregler och krypterad anslutning.

Brand Väggs regler används av både-servern och databasen för att avvisa anslutnings försök från IP-adresser som inte har vit listas uttryckligen. Om du vill tillåta anslutningar från ditt program eller klient datorns offentliga IP-adress måste du först skapa en brand Väggs regel på server nivå med hjälp av Azure Portal, REST API eller PowerShell. 

Ett bra tips är att du begränsar de IP-adressintervall som tillåts via serverbrandväggen så mycket som möjligt.  För att få åtkomst till Azure SQL Data Warehouse från den lokala datorn, se till att brand väggen på nätverket och den lokala datorn tillåter utgående kommunikation på TCP-port 1433.  

Azure-Synapse använder IP-brandvägg på server nivå. Den har inte stöd för IP-brandväggs regler på databas nivå. Mer information finns i [Azure SQL Database brand Väggs regler](../sql-database/sql-database-firewall-configure.md)

Anslutningar till SQL Data Warehouse krypteras som standard.  Ändring av anslutnings inställningar för att inaktivera kryptering ignoreras.

## <a name="authentication"></a>Autentisering
Autentisering refererar till hur du styrkt din identitet vid anslutning till databasen. SQL Data Warehouse stöder för närvarande SQL Server autentisering med ett användar namn och lösen ord och med Azure Active Directory. 

När du skapade den logiska servern för databasen angav du en "serveradministratörsinloggning” med ett användarnamn och lösenord. Med dessa autentiseringsuppgifter kan du autentisera till valfri databas på servern som databasens ägare, eller "dbo" genom SQL Server autentisering.

Men som bästa praxis bör din organisations användare använda ett annat konto för att autentisera. På så sätt kan du begränsa de behörigheter som beviljats för programmet och minska riskerna med skadlig aktivitet, om program koden är utsatt för en SQL-attack. 

Om du vill skapa en SQL Server autentiserad användare ansluter du till **huvud** databasen på servern med Server administratörs inloggningen och skapar en ny server inloggning.  Det är en bra idé att även skapa en användare i huvud databasen Azure Synapse-användare. Genom att skapa en användare i Master kan en användare logga in med hjälp av verktyg som SSMS utan att ange ett databas namn.  Det gör det också möjligt för dem att använda Object Explorer för att visa alla databaser på en SQL-Server.

```sql
-- Connect to master database and create a login
CREATE LOGIN ApplicationLogin WITH PASSWORD = 'Str0ng_password';
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Anslut sedan till din **SQL Data Warehouse databas** med inloggningen för Server administratören och skapa en databas användare baserat på den server inloggning du skapade.

```sql
-- Connect to SQL DW database and create a database user
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Om du vill ge en användare behörighet att utföra ytterligare åtgärder, till exempel skapa inloggningar eller skapa nya databaser, tilldelar du användaren till `Loginmanager` och `dbmanager` roller i huvud databasen. 

Mer information om dessa ytterligare roller och autentisering till en SQL Database finns [i hantera databaser och inloggningar i Azure SQL Database](../sql-database/sql-database-manage-logins.md).  Mer information om hur du ansluter med hjälp av Azure Active Directory finns i [ansluta med hjälp av Azure Active Directory autentisering](sql-data-warehouse-authentication.md).

## <a name="authorization"></a>Auktorisering
Auktorisering syftar på vad du kan göra i en databas när du är autentiserad och ansluten. Behörigheterna för auktorisering bestäms av roll medlemskap och behörigheter. Ett bra tips är att du ska ge användare så få behörigheter som möjligt. Du kan använda följande lagrade procedurer för att hantera roller:

```sql
EXEC sp_addrolemember 'db_datareader', 'ApplicationUser'; -- allows ApplicationUser to read data
EXEC sp_addrolemember 'db_datawriter', 'ApplicationUser'; -- allows ApplicationUser to write data
```

Serveradministratörskontot som du ansluter med är medlem i db_owner som har behörighet att göra vad som helst i databasen. Spara det här kontot för att distribuera schemauppgraderingar och andra hanteringsåtgärder. Använd kontot "ApplicationUser" med mer begränsade behörigheter för att ansluta från ditt program till databasen med den minsta behörigheten som krävs av programmet.

Det finns sätt att ytterligare begränsa vad en användare kan göra i ett informations lager:

* Med detaljerade [behörigheter](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine?view=sql-server-ver15) kan du styra vilka åtgärder du kan utföra på enskilda kolumner, tabeller, vyer, scheman, procedurer och andra objekt i databasen. Använd detaljerade behörigheter för att få ut mesta möjliga kontroll och bevilja de lägsta behörigheter som krävs. 
* Andra [databas roller](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles?view=sql-server-ver15) än db_datareader och db_datawriter kan användas för att skapa mer kraftfulla program användar konton eller mindre kraftfulla hanterings konton. De inbyggda fasta databas rollerna ger ett enkelt sätt att bevilja behörigheter, men kan resultera i att ge fler behörigheter än vad som behövs.
* [Lagrade procedurer](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine?redirectedfrom=MSDN&view=sql-server-ver15) kan användas för att begränsa de åtgärder som kan utföras i databasen.

I följande exempel beviljas Läs behörighet till ett användardefinierat schema.
```sql
--CREATE SCHEMA Test
GRANT SELECT ON SCHEMA::Test to ApplicationUser
```

Hantering av databaser och logiska servrar från Azure Portal eller användning av Azure Resource Manager-API: t styrs av ditt användar kontos roll tilldelningar för portalen. Mer information finns i [rollbaserad åtkomst kontroll i Azure Portal](https://azure.microsoft.com/documentation/articles/role-based-access-control-configure).

## <a name="encryption"></a>Kryptering
Transparent datakryptering (TDE) skyddar mot hot mot skadlig aktivitet genom att kryptera och dekryptera data i vila. När du krypterar din databas krypteras tillhör ande säkerhets kopior och transaktionsloggfiler utan att det krävs några ändringar i dina program. TDE krypterar lagringen av en hel databas med hjälp av en symmetrisk nyckel som kallas databas krypterings nyckel. 

I SQL Database skyddas databas krypterings nyckeln av ett inbyggt Server certifikat. Det inbyggda Server certifikatet är unikt för varje SQL Database Server. Microsoft roterar dessa certifikat automatiskt minst var 90: e dag. Den krypteringsalgoritm som används är AES-256. En allmän beskrivning av TDE finns [Transparent datakryptering](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-ver15).

Du kan kryptera databasen med hjälp av [Azure Portal](sql-data-warehouse-encryption-tde.md) eller [T-SQL](sql-data-warehouse-encryption-tde-tsql.md).

## <a name="next-steps"></a>Nästa steg
Mer information och exempel på hur du ansluter till ditt lager med olika protokoll finns i [Anslut till SQL Data Warehouse](sql-data-warehouse-connect-overview.md).
