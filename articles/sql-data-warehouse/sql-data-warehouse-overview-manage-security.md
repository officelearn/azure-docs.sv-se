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
ms.openlocfilehash: c51a945bae7cc0b03c219bc041d64f4703baef19
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692575"
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

Brandväggsregler används av både servern och databasen för att avvisa anslutningsförsök från IP-adresser som inte uttryckligen finns på listan över godkända. Om du vill tillåta anslutningar från ditt program eller klient datorns offentliga IP-adress måste du först skapa en brand Väggs regel på server nivå med hjälp av Azure Portal, REST API eller PowerShell. Ett bra tips är att du begränsar de IP-adressintervall som tillåts via serverbrandväggen så mycket som möjligt.  För att få åtkomst till Azure SQL Data Warehouse från den lokala datorn, se till att brand väggen på nätverket och den lokala datorn tillåter utgående kommunikation på TCP-port 1433.  

SQL Data Warehouse använder brand Väggs regler på server nivå. Den har inte stöd för brand Väggs regler på databas nivå. Mer information finns i [Azure SQL Database-brandvägg][Azure SQL Database firewall], [sp_set_firewall_rule][sp_set_firewall_rule].

Anslutningar till SQL Data Warehouse krypteras som standard.  Ändring av anslutnings inställningar för att inaktivera kryptering ignoreras.

## <a name="authentication"></a>Autentisering
Autentisering refererar till hur du styrkt din identitet vid anslutning till databasen. SQL Data Warehouse stöder för närvarande SQL Server autentisering med ett användar namn och lösen ord och med Azure Active Directory. 

När du skapade den logiska servern för databasen angav du en "serveradministratörsinloggning” med ett användarnamn och lösenord. Med dessa autentiseringsuppgifter kan du autentisera till valfri databas på servern som databasens ägare, eller "dbo" genom SQL Server autentisering.

Men som bästa praxis bör din organisations användare använda ett annat konto för att autentisera. På så sätt kan du begränsa de behörigheter som beviljats för programmet och minska riskerna med skadlig aktivitet, om program koden är utsatt för en SQL-attack. 

Om du vill skapa en SQL Server autentiserad användare ansluter du till **huvud** databasen på servern med Server administratörs inloggningen och skapar en ny server inloggning.  Dessutom är det en bra idé att skapa en användare i huvud databasen för Azure SQL Data Warehouse användare. Genom att skapa en användare i Master kan en användare logga in med hjälp av verktyg som SSMS utan att ange ett databas namn.  Det gör det också möjligt för dem att använda Object Explorer för att visa alla databaser på en SQL-Server.

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

Om du vill ge en användare behörighet att utföra ytterligare åtgärder, till exempel skapa inloggningar eller skapa nya databaser, tilldelar du användaren till `Loginmanager` och `dbmanager` roller i huvud databasen. Mer information om dessa ytterligare roller och autentisering till en SQL Database finns [i hantera databaser och inloggningar i Azure SQL Database][Managing databases and logins in Azure SQL Database].  Mer information finns i [ansluta till SQL Data Warehouse med Azure Active Directory autentisering][Connecting to SQL Data Warehouse By Using Azure Active Directory Authentication].

## <a name="authorization"></a>Auktorisering
Auktorisering syftar på vad du kan göra i en Azure SQL Data Warehouse databas. Behörigheterna för auktorisering bestäms av roll medlemskap och behörigheter. Ett bra tips är att du ska ge användare så få behörigheter som möjligt. Du kan använda följande lagrade procedurer för att hantera roller:

```sql
EXEC sp_addrolemember 'db_datareader', 'ApplicationUser'; -- allows ApplicationUser to read data
EXEC sp_addrolemember 'db_datawriter', 'ApplicationUser'; -- allows ApplicationUser to write data
```

Serveradministratörskontot som du ansluter med är medlem i db_owner som har behörighet att göra vad som helst i databasen. Spara det här kontot för att distribuera schemauppgraderingar och andra hanteringsåtgärder. Använd kontot "ApplicationUser" med mer begränsade behörigheter för att ansluta från ditt program till databasen med den minsta behörigheten som krävs av programmet.

Det finns sätt att ytterligare begränsa vad en användare kan göra med Azure SQL Data Warehouse:

* Med detaljerade [behörigheter][Permissions] kan du styra vilka åtgärder du kan utföra på enskilda kolumner, tabeller, vyer, scheman, procedurer och andra objekt i databasen. Använd detaljerade behörigheter för att få ut mesta möjliga kontroll och bevilja de lägsta behörigheter som krävs. 
* Andra [databas roller][Database roles] än db_datareader och db_datawriter kan användas för att skapa mer kraftfulla program användar konton eller mindre kraftfulla hanterings konton. De inbyggda fasta databas rollerna ger ett enkelt sätt att bevilja behörigheter, men kan resultera i att ge fler behörigheter än vad som behövs.
* [Lagrade procedurer][Stored procedures] kan användas för att begränsa de åtgärder som kan utföras i databasen.

I följande exempel beviljas Läs behörighet till ett användardefinierat schema.
```sql
--CREATE SCHEMA Test
GRANT SELECT ON SCHEMA::Test to ApplicationUser
```

Hantering av databaser och logiska servrar från Azure Portal eller användning av Azure Resource Manager-API: t styrs av ditt användar kontos roll tilldelningar för portalen. Mer information finns i [rollbaserad åtkomst kontroll i Azure Portal][Role-based access control in Azure portal].

## <a name="encryption"></a>Kryptering
Azure SQL Data Warehouse transparent datakryptering (TDE) skyddar mot hot mot skadlig aktivitet genom att kryptera och dekryptera data i vila.  När du krypterar din databas krypteras tillhör ande säkerhets kopior och transaktionsloggfiler utan att det krävs några ändringar i dina program. TDE krypterar lagringen av en hel databas med hjälp av en symmetrisk nyckel som kallas databas krypterings nyckel. 

I SQL Database skyddas databas krypterings nyckeln av ett inbyggt Server certifikat. Det inbyggda Server certifikatet är unikt för varje SQL Database Server. Microsoft roterar dessa certifikat automatiskt minst var 90: e dag. Krypteringsalgoritmen som används av SQL Data Warehouse är AES-256. En allmän beskrivning av TDE finns [Transparent datakryptering][Transparent Data Encryption].

Du kan kryptera databasen med hjälp av [Azure Portal][Encryption with Portal] eller [T-SQL][Encryption with TSQL].

## <a name="next-steps"></a>Nästa steg
Mer information och exempel på hur du ansluter till SQL Data Warehouse med olika protokoll finns i [Anslut till SQL Data Warehouse][Connect to SQL Data Warehouse].

<!--Image references-->

<!--Article references-->
[Connect to SQL Data Warehouse]: ./sql-data-warehouse-connect-overview.md
[Encryption with Portal]: ./sql-data-warehouse-encryption-tde.md
[Encryption with TSQL]: ./sql-data-warehouse-encryption-tde-tsql.md
[Connecting to SQL Data Warehouse By Using Azure Active Directory Authentication]: ./sql-data-warehouse-authentication.md

<!--MSDN references-->
[Azure SQL Database firewall]: https://msdn.microsoft.com/library/ee621782.aspx
[sp_set_firewall_rule]: https://msdn.microsoft.com/library/dn270017.aspx
[sp_set_database_firewall_rule]: https://msdn.microsoft.com/library/dn270010.aspx
[Database roles]: https://msdn.microsoft.com/library/ms189121.aspx
[Managing databases and logins in Azure SQL Database]: https://msdn.microsoft.com/library/ee336235.aspx
[Permissions]: https://msdn.microsoft.com/library/ms191291.aspx
[Stored procedures]: https://msdn.microsoft.com/library/ms190782.aspx
[Transparent Data Encryption]: https://msdn.microsoft.com/library/bb934049.aspx
[Azure portal]: https://portal.azure.com/

<!--Other Web references-->
[Role-based access control in Azure portal]: https://azure.microsoft.com/documentation/articles/role-based-access-control-configure
