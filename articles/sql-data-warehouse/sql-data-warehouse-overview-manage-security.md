---
title: Skydda en databas i SQL Data Warehouse | Microsoft Docs
description: Tips för att skydda en databas i Azure SQL Data Warehouse för utveckling av lösningar.
services: sql-data-warehouse
author: kavithaj
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: 8abb40b0c1a5b9cd3f8d1e23124090c00e8cfadb
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2018
ms.locfileid: "43302547"
---
# <a name="secure-a-database-in-sql-data-warehouse"></a>Skydda en databas i SQL Data Warehouse
> [!div class="op_single_selector"]
> * [Säkerhetsöversikt](sql-data-warehouse-overview-manage-security.md)
> * [Autentisering](sql-data-warehouse-authentication.md)
> * [Kryptering (Portal)](sql-data-warehouse-encryption-tde.md)
> * [Kryptering (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

Den här artikeln beskriver grunderna för att skydda din Azure SQL Data Warehouse-databas. I synnerhet, den här artikeln kommer du igång med resurser för att begränsa åtkomst, skydda data och övervaka aktiviteter på en databas.

## <a name="connection-security"></a>Anslutningssäkerhet
Anslutningssäkerhet avser hur du begränsar och säkrar anslutningar till databasen med hjälp av brandväggsregler och krypterad anslutning.

Brandväggsregler används av både servern och databasen för att avvisa anslutningsförsök från IP-adresser som inte uttryckligen finns på listan över godkända. Om du vill tillåta anslutningar från programmets eller klientdatorns offentlig IP-adress, måste du först skapa en brandväggsregel för servernivå med hjälp av Azure portal, REST API eller PowerShell. Ett bra tips är att du begränsar de IP-adressintervall som tillåts via serverbrandväggen så mycket som möjligt.  Kontrollera att brandväggen på ditt nätverk och lokala datorn tillåter utgående kommunikation på TCP-port 1433 för att komma åt Azure SQL Data Warehouse från din lokala dator.  

SQL Data Warehouse använder brandväggsregler på servernivå. Det stöder inte brandväggsregler på databasnivå. Mer information finns i [Azure SQL Database-brandvägg][Azure SQL Database firewall], [sp_set_firewall_rule][sp_set_firewall_rule].

Anslutningar till SQL Data Warehouse krypteras som standard.  Ändra anslutningsinställningar till datakrypteringen ignoreras.

## <a name="authentication"></a>Autentisering
Autentisering refererar till hur du styrkt din identitet vid anslutning till databasen. SQL Data Warehouse stöder för närvarande SQL Server-autentisering med användarnamn och lösenord och med Azure Active Directory. 

När du skapade den logiska servern för databasen angav du en "serveradministratörsinloggning” med ett användarnamn och lösenord. Med dessa autentiseringsuppgifter kan autentisera du till valfri databas på servern som databasens ägare eller ”dbo” via SQL Server-autentisering.

Dock ska bör din organisations användare använda ett annat konto för autentisering. Det här sättet kan du begränsa behörigheterna för programmet och minska riskerna för skadlig programvara om din programkod är sårbar för ett SQL-angrepp. 

Om du vill skapa en SQL Server-autentiserad användare ansluta till den **master** databasen på servern med din serveradministratör och skapa en ny server-inloggning.  Dessutom är det en bra idé att skapa en användare i huvuddatabasen för Azure SQL Data Warehouse-användare. Skapa en användare i huvuddatabasen kan användaren logga in med verktyg som SSMS utan att ange ett databasnamn.  Det gör också att de kan använda object explorer för att visa alla databaser på en SQLServer.

```sql
-- Connect to master database and create a login
CREATE LOGIN ApplicationLogin WITH PASSWORD = 'Str0ng_password';
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Anslut till din **SQL Data Warehouse-databas** med ditt användarnamn och skapa en databasanvändare baserat på server-inloggning som du skapade.

```sql
-- Connect to SQL DW database and create a database user
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Om du vill ge en användarbehörighet att utföra ytterligare åtgärder som att skapa inloggningar eller skapa nya databaser, tilldelar du användaren till den `Loginmanager` och `dbmanager` roller i huvuddatabasen. Mer information om dessa ytterligare roller och autentisera till en SQL-databas finns i [hantera databaser och inloggningar i Azure SQL Database][Managing databases and logins in Azure SQL Database].  Mer information finns i [ansluter till SQL Data Warehouse med hjälp av Azure Active Directory-autentisering][Connecting to SQL Data Warehouse By Using Azure Active Directory Authentication].

## <a name="authorization"></a>Auktorisering
Auktorisering hänvisar till vad du kan göra inom en Azure SQL Data Warehouse-databas. Auktorisering behörigheter avgörs av rollmedlemskap och behörigheter. Ett bra tips är att du ska ge användare så få behörigheter som möjligt. Du kan använda följande lagrade procedurer för att hantera roller:

```sql
EXEC sp_addrolemember 'db_datareader', 'ApplicationUser'; -- allows ApplicationUser to read data
EXEC sp_addrolemember 'db_datawriter', 'ApplicationUser'; -- allows ApplicationUser to write data
```

Serveradministratörskontot som du ansluter med är medlem i db_owner som har behörighet att göra vad som helst i databasen. Spara det här kontot för att distribuera schemauppgraderingar och andra hanteringsåtgärder. Använd kontot "ApplicationUser" med mer begränsade behörigheter för att ansluta från ditt program till databasen med den minsta behörigheten som krävs av programmet.

Det finns sätt att ytterligare begränsa vad en användare kan göra med Azure SQL Data Warehouse:

* Detaljerad [behörigheter] [ Permissions] kan du kontrollen vilka åtgärder du kan på enskilda kolumner, tabeller, vyer, scheman, procedurer och andra objekt i databasen. Använd detaljerade behörigheter har de flesta kontroll och ge den lägsta behörigheten som krävs. 
* [Databasroller] [ Database roles] andra än db_datareader och db_datawriter kan användas för att skapa mer kraftfulla programanvändarkonton eller mindre kraftfulla hanteringskonton. Inbyggda fasta databasroller ger ett enkelt sätt att bevilja behörigheter, men kan leda till tillståndsbeviljande mer än nödvändigt.
* [Lagrade procedurer] [ Stored procedures] kan användas för att begränsa de åtgärder som kan utföras i databasen.

I följande exempel beviljas läsbehörighet till ett användardefinierat schema.
```sql
--CREATE SCHEMA Test
GRANT SELECT ON SCHEMA::Test to ApplicationUser
```

Hantera databaser och logiska servrar från Azure portal eller med hjälp av Azure Resource Manager API styrs av din portalanvändarkontos rolltilldelningar. Mer information finns i [rollbaserad åtkomstkontroll i Azure-portalen][Role-based access control in Azure portal].

## <a name="encryption"></a>Kryptering
Azure SQL Data Warehouse Transparent datakryptering (TDE) skyddar mot skadlig aktivitet genom att kryptera och dekryptera data i vila.  När du krypterar din databas, krypteras tillhörande säkerhetskopior och transaktionsloggfiler utan ändringar i dina program. TDE krypterar lagring av en hel databas med hjälp av en symmetrisk nyckel som heter databaskrypteringsnyckeln. 

Databaskrypteringsnyckeln är skyddat av ett certifikat för inbyggd i SQL-databas. Inbyggda servercertifikatet är unikt för varje SQL Database-server. Microsoft roterar dessa certifikat automatiskt minst var 90: e dag. Krypteringsalgoritmen som används av SQL Data Warehouse är AES-256. En allmän beskrivning av TDE Se [Transparent datakryptering][Transparent Data Encryption].

Du kan kryptera din databas med hjälp av den [Azure-portalen] [ Encryption with Portal] eller [T-SQL][Encryption with TSQL].

## <a name="next-steps"></a>Nästa steg
Mer information och exempel om hur du ansluter till SQL Data Warehouse med olika protokoll i [Anslut till SQL Data Warehouse][Connect to SQL Data Warehouse].

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
