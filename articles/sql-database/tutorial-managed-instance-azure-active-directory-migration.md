---
title: Migrera SQL ServerWindows användare och grupper till hanterad instans med T-SQL
description: Lär dig mer om hur du migrerar LOKALA Windows-användare och -grupper i SQL Server till hanterad instans
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019
ms.topic: tutorial
author: GitHubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 10/30/2019
ms.openlocfilehash: 2c8d7252b4e4ca8caa465727c0d2328c4aafaefb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "74227928"
---
# <a name="tutorial-migrate-sql-server-on-premises-windows-users-and-groups-to-azure-sql-database-managed-instance-using-t-sql-ddl-syntax"></a>Självstudiekurs: Migrera SQL Server lokalt Windows-användare och -grupper till Azure SQL Database hanterad instans med T-SQL DDL-syntax

> [!NOTE]
> Syntaxen som används för att migrera användare och grupper till hanterad instans i den här artikeln är i **offentlig förhandsversion**.

Den här artikeln tar dig igenom processen att migrera dina lokala Windows-användare och grupper i SQL Server till en befintlig Azure SQL Database hanterad instans med T-SQL-syntax.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> - Skapa inloggningar för SQL Server
> - Skapa en testdatabas för migrering
> - Skapa inloggningar, användare och roller
> - Säkerhetskopiera och återställa databasen till hanterad instans (MI)
> - Migrera användare till MI manuellt med hjälp av syntaxen ÄNDRA ANVÄNDARE
> - Testa autentisering med de nya mappade användarna

## <a name="prerequisites"></a>Krav

För att slutföra den här självstudien gäller följande förutsättningar:

- Windows-domänen är federerad med Azure Active Directory (Azure AD).
- Tillgång till Active Directory för att skapa användare/grupper.
- En befintlig SQL Server i din lokala miljö.
- En befintlig hanterad instans. Se [Snabbstart: Skapa en hanterad Azure SQL-databas-hanterad instans](sql-database-managed-instance-get-started.md).
  - A `sysadmin` i den hanterade instansen måste användas för att skapa Azure AD-inloggningar.
- [Skapa en Azure AD-administratör för hanterad instans](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance).
- Du kan ansluta till din hanterade instans i nätverket. Mer information finns i följande artiklar: 
    - [Anslut ditt program till Azure SQL Database Managed Instance](sql-database-managed-instance-connect-app.md)
    - [Snabbstart: Konfigurera en point-to-site-anslutning till en Hanterad Azure SQL-databasinstans från lokala](sql-database-managed-instance-configure-p2s.md)
    - [Konfigurera en offentlig slutpunkt för en hanterad Azure SQL Database-instans](sql-database-managed-instance-public-endpoint-configure.md)

## <a name="t-sql-ddl-syntax"></a>T-SQL DDL-syntax

Nedan finns T-SQL DDL-syntaxen som används för att stödja SQL Server lokala Windows-användare och gruppermigrering till hanterad instans med Azure AD-autentisering.

```sql
-- For individual Windows users with logins 
ALTER USER [domainName\userName] WITH LOGIN = [loginName@domainName.com]; 

--For individual groups with logins 
ALTER USER [domainName\groupName] WITH LOGIN=[groupName] 
```

## <a name="arguments"></a>Argument

_Domännamn_</br>
Anger användarens domännamn.

_Användarnamn_</br>
Anger namnet på den användare som identifieras i databasen.

_= loginName\@domainName.com_</br>
Mappa om en användare till Azure AD-inloggningen

_Gruppnamn_</br>
Anger namnet på den grupp som identifieras i databasen.

## <a name="part-1-create-logins-for-sql-server-on-premises-users-and-groups"></a>Del 1: Skapa inloggningar för lokala SQL Server-användare och -grupper

> [!IMPORTANT]
> Följande syntax skapar en användare och en gruppinloggning i SQL Server. Du måste se till att användaren och gruppen finns i Active Directory (AD) innan du kör syntaxen nedan. </br> </br>
> Användare: testUser1, testGroupUser </br>
> Grupp: migrering - testGroupUser måste tillhöra migreringsgruppen i AD

Exemplet nedan skapar en inloggning i SQL Server för ett konto med namnet _testUser1_ under domänen _aadsqlmi_. 

```sql
-- Sign into SQL Server as a sysadmin or a user that can create logins and databases
 
use master;  
go

-- Create Windows login
create login [aadsqlmi\testUser1] from windows;   
go; 

/** Create a Windows group login which contains one user [aadsqlmi\testGroupUser]. 
testGroupUser will need to be added to the migration group in Active Directory
**/
create login [aadsqlmi\migration] from windows;    
go; 


-- Check logins were created
select * from sys.server_principals; 
go; 
```

Skapa en databas för det här testet.

```sql
-- Create a database called [migration]
create database migration 
go
```

## <a name="part-2-create-windows-users-and-groups-then-add-roles-and-permissions"></a>Del 2: Skapa Windows-användare och Windows-grupper och lägg sedan till roller och behörigheter

Använd följande syntax för att skapa testanvändaren.

```sql
use migration;  
go 
 
-- Create Windows user [aadsqlmi\testUser1] with login 
create user [aadsqlmi\testUser1] from login [aadsqlmi\testUser1]; 
go 
```

Kontrollera användarbehörigheterna:

```sql
-- Check the user in the Metadata 
select * from sys.database_principals; 
go 
 
-- Display the permissions – should only have CONNECT permissions
select user_name(grantee_principal_id), * from sys.database_permissions; 
go
```

Skapa en roll och tilldela testanvändaren till den här rollen:

```sql 
-- Create a role with some permissions and assign the user to the role
create role UserMigrationRole; 
go

grant CONNECT, SELECT, View DATABASE STATE, VIEW DEFINITION to UserMigrationRole; 
go 

alter role UserMigrationRole add member [aadsqlmi\testUser1]; 
go 
``` 

Använd följande fråga för att visa användarnamn som tilldelats en viss roll:

```sql
-- Display user name assigned to a specific role 
SELECT DP1.name AS DatabaseRoleName,    
   isnull (DP2.name, 'No members') AS DatabaseUserName    
 FROM sys.database_role_members AS DRM   
 RIGHT OUTER JOIN sys.database_principals AS DP1   
   ON DRM.role_principal_id = DP1.principal_id   
 LEFT OUTER JOIN sys.database_principals AS DP2   
   ON DRM.member_principal_id = DP2.principal_id   
WHERE DP1.type = 'R' 
ORDER BY DP1.name; 
```

Använd följande syntax för att skapa en grupp. Lägg sedan till gruppen `db_owner`i rollen .

```sql
-- Create Windows group
create user [aadsqlmi\migration] from login [aadsqlmi\migration]; 
go

-- ADD 'db_owner' role to this group
sp_addrolemember 'db_owner', 'aadsqlmi\migration'; 
go 

--Check the db_owner role for 'aadsqlmi\migration' group 
select is_rolemember('db_owner', 'aadsqlmi\migration')   
go 
-- Output  ( 1 means YES) 
```

Skapa en testtabell och lägg till data med följande syntax:

```sql
-- Create a table and add data 
create table test ( a int, b int); 
go 

insert into test values (1,10) 
go 

-- Check the table values
select * from test; 
go
```

## <a name="part-3-backup-and-restore-the-individual-user-database-to-managed-instance"></a>Del 3: Säkerhetskopiera och återställa den enskilda användardatabasen till hanterad instans

Skapa en säkerhetskopia av migreringsdatabasen med artikeln [Kopiera databaser med säkerhetskopiering och återställning](/sql/relational-databases/databases/copy-databases-with-backup-and-restore)eller använd följande syntax:

```sql
use master; 
go 
backup database migration to disk = 'C:\Migration\migration.bak'; 
go
```

Följ vår [snabbstart: Återställ en databas till en hanterad instans](sql-database-managed-instance-get-started-restore.md).

## <a name="part-4-migrate-users-to-managed-instance"></a>Del 4: Migrera användare till hanterad instans

> [!NOTE]
> Azure AD-administratören för hanterade instansfunktioner efter att skapandet har ändrats. Mer information finns i [Nya Azure AD-administratörsfunktioner för MI](sql-database-aad-authentication-configure.md#new-azure-ad-admin-functionality-for-mi).

Kör kommandot ALTER USER för att slutföra migreringsprocessen på hanterad instans.

1. Logga in på din hanterade instans med Azure AD-administratörskontot för hanterad instans. Skapa sedan din Azure AD-inloggning i den hanterade instansen med hjälp av följande syntax. Mer information finns [i Självstudiekurs: Hanterad instanssäkerhet i Azure SQL Database med Azure AD-serverobjekt (inloggningar)](sql-database-managed-instance-aad-security-tutorial.md).

    ```sql
    use master 
    go 
    
    -- Create login for AAD user [testUser1@aadsqlmi.net] 
    create login [testUser1@aadsqlmi.net] from external provider 
    go
    
    -- Create login for the Azure AD group [migration]. This group contains one user [testGroupUser@aadsqlmi.net] 
    create login [migration] from external provider 
    go 
     
    --Check the two new logins 
    select * from sys.server_principals 
    go
    ```

1. Kontrollera om du vill ha rätt databas, tabell och huvudnamn.

    ```sql
    -- Switch to the database migration that is already restored for MI 
    use migration;  
    go 
     
    --Check if the restored table test exist and contain a row 
    select * from test; 
    go 
     
    -- Check that the SQL on-premises Windows user/group exists  
    select * from sys.database_principals; 
    go 
    -- the old user aadsqlmi\testUser1 should be there 
    -- the old group aadsqlmi\migration should be there
    ```

1. Använd syntaxen ÄNDRA ANVÄNDARE för att mappa den lokala användaren till Azure AD-inloggningen.

    ```sql
    /** Execute the ALTER USER command to alter the Windows user [aadsqlmi\testUser1]
    to map to the Azure AD user testUser1@aadsqlmi.net
    **/
    alter user [aadsqlmi\testUser1] with login = [testUser1@aadsqlmi.net]; 
    go

    -- Check the principal
    select * from sys.database_principals; 
    go 
    -- New user testUser1@aadsqlmi.net should be there instead 
     
    --Check new user permissions  - should only have CONNECT permissions
    select user_name(grantee_principal_id), * from sys.database_permissions; 
    go

    -- Check a specific role   
    -- Display Db user name assigned to a specific role 
    SELECT DP1.name AS DatabaseRoleName,    
    isnull (DP2.name, 'No members') AS DatabaseUserName    
    FROM sys.database_role_members AS DRM   
    RIGHT OUTER JOIN sys.database_principals AS DP1   
    ON DRM.role_principal_id = DP1.principal_id   
    LEFT OUTER JOIN sys.database_principals AS DP2   
    ON DRM.member_principal_id = DP2.principal_id   
    WHERE DP1.type = 'R' 
    ORDER BY DP1.name;
    ```

1. Använd syntaxen ÄNDRA ANVÄNDARE för att mappa den lokala gruppen till Azure AD-inloggningen.

    ```sql
    /** Execute ALTER USER command to alter the Windows group [aadsqlmi\migration]
    to the Azure AD group login [migration]
    **/
    alter user [aadsqlmi\migration] with login = [migration]; 
    -- old group migration is changed to Azure AD migration group
    go

    -- Check the principal
    select * from sys.database_principals; 
    go 
     
    --Check the group permission - should only have CONNECT permissions
    select user_name(grantee_principal_id), * from sys.database_permissions; 
    go 
     
    --Check the db_owner role for 'aadsqlmi\migration' user 
    select is_rolemember('db_owner', 'migration')   
    go 
    -- Output 1 means 'YES'
    ```

## <a name="part-5-testing-azure-ad-user-or-group-authentication"></a>Del 5: Testa Azure AD-användare- eller gruppautentisering

Testa autentisering till hanterad instans med hjälp av användaren som tidigare mappats till Azure AD-inloggningen med hjälp av syntaxen ÄNDRA ANVÄNDARE.
 
1. Logga in på den federerade virtuella datorn med din MI-prenumeration som`aadsqlmi\testUser1`
1. Med hjälp av SQL Server Management Studio (SSMS) loggar du in `migration`på din hanterade instans med Active Directory **Integrated-autentisering** och ansluter till databasen .
    1. Du kan också logga testUser1@aadsqlmi.net in med autentiseringsuppgifterna med alternativet SSMS **Active Directory – Universal med MFA-stöd**. I det här fallet kan du dock inte använda mekanismen Enkel inloggning och du måste skriva ett lösenord. Du behöver inte använda en federerad virtuell dator för att logga in på din hanterade instans.
1. Som en del **SELECT**av rollmedlemmen SELECT `test` kan du välja från tabellen

    ```sql
    Select * from test  --  and see one row (1,10)
    ```


Testa autentisering till en hanterad instans `migration`med hjälp av en medlem i en Windows-grupp . Användaren `aadsqlmi\testGroupUser` borde ha lagts till `migration` i gruppen före migreringen.

1. Logga in på den federerade virtuella datorn med din MI-prenumeration som`aadsqlmi\testGroupUser` 
1. Ansluta till MI-servern och databasen med SSMS med **integrerad Active Directory-autentisering**`migration`
    1. Du kan också logga testGroupUser@aadsqlmi.net in med autentiseringsuppgifterna med alternativet SSMS **Active Directory – Universal med MFA-stöd**. I det här fallet kan du dock inte använda mekanismen Enkel inloggning och du måste skriva ett lösenord. Du behöver inte använda en federerad virtuell dator för att logga in på din hanterade instans. 
1. Som en `db_owner` del av rollen kan du skapa en ny tabell.

    ```sql
    -- Create table named 'new' with a default schema
    Create table dbo.new ( a int, b int)
    ```
                             
> [!NOTE] 
> På grund av ett känt designproblem för Azure SQL DB misslyckas ett skapa en tabellsats som körs som medlem i en grupp med följande fel: </br> </br>
> `Msg 2760, Level 16, State 1, Line 4 
The specified schema name "testGroupUser@aadsqlmi.net" either does not exist or you do not have permission to use it.` </br> </br>
> Den aktuella lösningen är att skapa en tabell med ett befintligt schema i ärendet ovan <dbo.new>

## <a name="next-steps"></a>Nästa steg

- [Självstudiekurs: Migrera SQL Server till en Azure SQL Database-hanterad instans offline med DMS](../dms/tutorial-sql-server-to-managed-instance.md?toc=/azure/sql-database/toc.json)