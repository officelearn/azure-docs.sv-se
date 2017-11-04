---
title: Skydda en databas i SQL Data Warehouse | Microsoft Docs
description: "Tips för att skydda en databas i Azure SQL Data Warehouse för utveckling av lösningar."
services: sql-data-warehouse
documentationcenter: NA
author: ronortloff
manager: jhubbard
editor: 
ms.assetid: 8fa2f5ca-4cf5-4418-99a2-4dc745799850
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: security
ms.date: 10/31/2016
ms.author: rortloff;barbkess
ms.openlocfilehash: 6ea45c40bc428282faf24b4a08f8b0d345adb3fd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="secure-a-database-in-sql-data-warehouse"></a>Skydda en databas i SQL Data Warehouse
> [!div class="op_single_selector"]
> * [Översikt över säkerheten](sql-data-warehouse-overview-manage-security.md)
> * [Autentisering](sql-data-warehouse-authentication.md)
> * [Kryptering (Portal)](sql-data-warehouse-encryption-tde.md)
> * [Kryptering (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

Den här artikeln beskriver hur grunderna för att skydda din Azure SQL Data Warehouse-databas. I synnerhet i den här artikeln hjälper dig att komma igång med resurser för att begränsa åtkomst, skyddar data och övervakar aktiviteter på en databas.

## <a name="connection-security"></a>Anslutningssäkerhet
Anslutningssäkerhet avser hur du begränsar och säkrar anslutningar till databasen med hjälp av brandväggsregler och krypterad anslutning.

Brandväggsregler används av både servern och databasen för att avvisa anslutningsförsök från IP-adresser som inte uttryckligen finns på listan över godkända. Om du vill tillåta anslutningar från ditt program eller klienten datorns offentliga IP-adress, måste du först skapa en brandväggsregel på servernivå med hjälp av Azure Portal, REST-API och PowerShell. Ett bra tips är att du begränsar de IP-adressintervall som tillåts via serverbrandväggen så mycket som möjligt.  Kontrollera att brandväggen på ditt nätverk och en lokal dator tillåter utgående kommunikation på TCP-port 1433 för att komma åt Azure SQL Data Warehouse från den lokala datorn.  Mer information finns i [Azure SQL Database-brandvägg][Azure SQL Database firewall], [sp_set_firewall_rule][sp_set_firewall_rule], och [sp_set_database_ firewall_rule][sp_set_database_firewall_rule].

Anslutningar till SQL Data Warehouse krypterad som standard.  Ändra anslutningsinställningar för att inaktivera kryptering ignoreras.

## <a name="authentication"></a>Autentisering
Autentisering refererar till hur du styrkt din identitet vid anslutning till databasen. SQL Data Warehouse stöder för närvarande SQL Server-autentisering med ett användarnamn och lösenord som en Azure Active Directory. 

När du skapade den logiska servern för databasen angav du en "serveradministratörsinloggning” med ett användarnamn och lösenord. Med dessa autentiseringsuppgifter, kan du autentisera till en databas på den servern som databasens ägare eller ”dbo” via SQL Server-autentisering.

Dock som bästa praxis bör bör användare i organisationen använda ett annat konto för autentisering. Det här sättet kan du begränsa behörigheterna för programmet och minska riskerna med skadliga aktiviteter om din programkod är utsatt för en attack med SQL injection. 

Om du vill skapa en SQL Server-autentiserad användare ansluta till den **master** databasen på servern med din inloggning för serveradministratör och skapa en ny server-inloggning.  Dessutom är det en bra idé att skapa en användare i master-databasen för Azure SQL Data Warehouse-användare. Skapa en användare i master tillåter en användare att logga in med verktyg som SSMS utan att ange ett databasnamn.  Det gör också att de använder object explorer för att visa alla databaser på en SQLServer.

```sql
-- Connect to master database and create a login
CREATE LOGIN ApplicationLogin WITH PASSWORD = 'Str0ng_password';
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Anslut till din **SQL Data Warehouse-databas** med din inloggning för serveradministratör och skapa en databasanvändare baserat på server-inloggning som du nyss skapade.

```sql
-- Connect to SQL DW database and create a database user
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Om en användare kommer att göra ytterligare åtgärder som till exempel skapa inloggningar eller skapa nya databaser, måste de också tilldelas det `Loginmanager` och `dbmanager` roller i master-databasen. Mer information om dessa ytterligare roller och autentisera till en SQL-databas finns [hantera databaser och inloggningar i Azure SQL Database][Managing databases and logins in Azure SQL Database].  Mer information om Azure AD för SQL Data Warehouse finns [ansluter till SQL Data Warehouse med hjälp av Azure Active Directory Authentication][Connecting to SQL Data Warehouse By Using Azure Active Directory Authentication].

## <a name="authorization"></a>Auktorisering
Auktorisering hänvisar till vad du kan göra i en Azure SQL Data Warehouse-databas och detta styrs av ditt användarkonto rollmedlemskap och behörigheter. Ett bra tips är att du ska ge användare så få behörigheter som möjligt. Azure SQL Data Warehouse gör det enkelt att hantera roller i T-SQL:

```sql
EXEC sp_addrolemember 'db_datareader', 'ApplicationUser'; -- allows ApplicationUser to read data
EXEC sp_addrolemember 'db_datawriter', 'ApplicationUser'; -- allows ApplicationUser to write data
```

Serveradministratörskontot som du ansluter med är medlem i db_owner som har behörighet att göra vad som helst i databasen. Spara det här kontot för att distribuera schemauppgraderingar och andra hanteringsåtgärder. Använd kontot "ApplicationUser" med mer begränsade behörigheter för att ansluta från ditt program till databasen med den minsta behörigheten som krävs av programmet.

Det finns olika sätt att ytterligare begränsa vad en användare kan göra med Azure SQL Database:

* Detaljerade [behörigheter] [ Permissions] kan du kontrollen vilka åtgärder som du kan på enskilda kolumner, tabeller, vyer, procedurer och andra objekt i databasen. Använda detaljerade behörigheter har störst kontroll och bevilja lägsta behörigheten som krävs. Detaljerad behörighet system är ganska komplex och kräver vissa undersökning du använder effektivt.
* [Databasrollerna] [ Database roles] än db_datareader och db_datawriter kan användas för att skapa mer kraftfulla program användarkonton eller mindre kraftfulla hanteringskonton. Inbyggda fasta databasroller ger ett enkelt sätt att bevilja behörighet, men kan leda till att bevilja fler behörigheter än nödvändigt.
* [Lagrade procedurer] [ Stored procedures] kan användas för att begränsa de åtgärder som kan göras på databasen.

Att hantera databaser och logiska servrar från den klassiska Azure-portalen eller via Azure Resource Manager API styrs av portalanvändarkontots rolltilldelningar. Mer information om det här avsnittet finns [rollbaserad åtkomstkontroll i Azure Portal][Role-based access control in Azure Portal].

## <a name="encryption"></a>Kryptering
Azure SQL Data Warehouse Transparent Data kryptering (TDE) skyddar mot hot från skadlig aktivitet genom att utföra realtid kryptering och dekryptering av data i vila.  När du krypterar din databas, krypteras tillhörande säkerhetskopior och transaktionsloggfiler utan ändringar i dina program. TDE krypterar lagring av en hel databas med hjälp av en symmetrisk nyckel som heter databaskrypteringsnyckeln. Databaskrypteringsnyckeln skyddas av en inbyggd servercertifikat i SQL-databas. Inbyggda certifikatet är unikt för varje SQL Database-server. Microsoft roteras automatiskt dessa certifikat minst var 90: e dag. Krypteringsalgoritmen som används av SQL Data Warehouse är AES-256. En allmän beskrivning av TDE finns [Transparent datakryptering][Transparent Data Encryption].

Du kan kryptera din databas med hjälp av den [Azure Portal] [ Encryption with Portal] eller [T-SQL][Encryption with TSQL].

## <a name="next-steps"></a>Nästa steg
Information och exempel på hur du ansluter till ditt SQL Data Warehouse med olika protokoll finns [Anslut till SQL Data Warehouse][Connect to SQL Data Warehouse].

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
[Role-based access control in Azure Portal]: https://azure.microsoft.com/documentation/articles/role-based-access-control-configure
