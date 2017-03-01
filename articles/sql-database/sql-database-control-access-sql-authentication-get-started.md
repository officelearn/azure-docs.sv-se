---
title: "SQL-autentisering: Azure SQL Database-brandväggar, autentisering, åtkomst | Microsoft Docs"
description: "I den här självstudien får du lära dig hantera brandväggsregler på server- och databasnivå, SQL-autentisering, inloggningar, användare samt regler för att bevilja kontroll och åtkomst till Azure SQL Database-servrar och -databaser med hjälp av SQL Server Management Studio och Transact-SQL."
keywords: 
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 67797b09-f5c3-4ec2-8494-fe18883edf7f
ms.service: sql-database
ms.custom: authentication and authorization
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 02/17/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: c70b3b23fa95af6614c34bd951943f0559409220
ms.openlocfilehash: cf43790c329ef156ae17579d2281c861533ec201


---
# <a name="sql-server-authentication-access-and-database-level-firewall-rules"></a>SQL Server-autentisering, åtkomst och brandväggsregler på databasnivå

I den här självstudien får du lära dig att hantera SQL Server-autentisering, inloggningar, användare och databasroller för att bevilja åtkomst och behörighet till Azure SQL Database-servrar och -databaser med hjälp av SQL Server Management Studio. När du har slutfört den här självstudien vet du hur du:

- Skapa inloggningar och användare baserat på SQL Server-autentisering
- Lägger till användare till roller och tilldela behörigheter till roller
- Använder en T-SQL för att skapa en brandväggsregel på databasnivå och en på servernivå 
- Ansluter som en användare till en viss databas med SSMS
- Visa användarbehörighet i huvuddatabasen och i användardatabaser

**Uppskattad tidsåtgång**: Den här kursen tar cirka 45 minuter att slutföra (förutsatt att du redan har uppfyllt kraven).

> [!NOTE]
> I den här självstudien går vi igenom följande: [Åtkomst och kontroll för SQL Database](sql-database-control-access.md), [inloggningar, användare och databasroller](sql-database-manage-logins.md), [huvudkonton](https://msdn.microsoft.com/library/ms181127.aspx), [databasroller](https://msdn.microsoft.com/library/ms189121.aspx) och [SQL Database-brandväggsregler](sql-database-firewall-configure.md). En självstudiekurs om Azure Active Directory-autentisering finns i [Komma igång med Azure AD-autentisering](sql-database-control-access-aad-authentication-get-started.md).
>  

## <a name="prerequisites"></a>Krav

* **Ett Azure-konto**. Du behöver ett Azure-konto. Du kan [öppna ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) eller [aktivera Visual Studio-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/). 

* **Azure skapa behörigheter**. Du måste kunna ansluta till Azure Portal med ett konto som är medlem i rollen som prenumerationsägare eller deltagare. Mer information om rollbaserad åtkomstkontroll (RBAC) finns i [Getting started with access management in the Azure portal](../active-directory/role-based-access-control-what-is.md) (Komma igång med åtkomsthantering på Azure Portal).

* **SQL Server Management Studio**. Du kan hämta och installera den senaste versionen av SQL Server Management Studio (SSMS) på [Download SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) (Hämta SQL Server Management Studio). Använd alltid den senaste versionen av SSMS när du ansluter till Azure SQL Database eftersom nya funktioner släpps kontinuerligt.

* **Slutfört de grundläggande självstudierna**. Du har slutfört [Komma igång med Azure SQL Database-servrar, databaser och brandväggsregler med hjälp av Azure Portal och SQL Server Management Studio](sql-database-get-started.md) eller motsvarande [PowerShell-version](sql-database-get-started-powershell.md) av den här självstudiekursen. Om du inte har gjort det slutför du kursen eller kör PowerShell-skriptet i slutet av [PowerShell-versionen](sql-database-get-started-powershell.md) av den här kursen innan du fortsätter.



## <a name="sign-in-to-the-azure-portal-using-your-azure-account"></a>Logga in på Azure Portal med ditt Azure-konto
Stegen i den här proceduren visar hur du ansluter till Azure Portal med ditt [Azure-konto] (https://account.windowsazure.com/Home/Index).

1. Öppna din webbläsare och anslut till [Azure-portalen](https://portal.azure.com/).
2. Logga in på [Azure-portalen](https://portal.azure.com/).
3. Ange autentiseringsuppgifterna för din prenumeration på sidan **Logga in**.
   
   ![Logga in](./media/sql-database-get-started/login.png)


<a name="create-logical-server-bk"></a>

## <a name="view-logical-server-security-information-in-the-azure-portal"></a>Se säkerhetsinformation om logiska servrar i Azure Portal

Stegen i den här proceduren visar hur du ser information om säkerhetskonfigurationen för din logiska server i Azure Portal.

1. Öppna bladet **SQL Server** för servern och se informationen på sidan **Översikt**.

   ![Serveradministratörskontot i Azure Portal](./media/sql-database-control-access-sql-authentication-get-started/sql_admin_portal.png)

2. Anteckna namnet på serveradministratören på den logiska servern. 

3. Om du inte kommer ihåg lösenordet klickar du på **Återställ lösenord** för att ställa in ett nytt lösenord.

4. Om du behöver hämta anslutningsinformationen om den här servern klickar du på **Egenskaper**.

## <a name="view-server-admin-permissions-using-ssms"></a>Se serveradministratörsbehörigheter med SSMS

Stegen i den här proceduren visar dig hur du ser information om serveradministratörskontot och dess behörigheter i huvuddatabasen och användardatabaserna.

1. Öppna SQL Server Management Studio och anslut till servern som serveradministratör med hjälp av SQL Server-autentisering och serveradministratörskontot.

   ![Anslut till server](./media/sql-database-get-started/connect-to-server.png)

2. Klicka på **anslut**.

   ![Ansluten till server](./media/sql-database-get-started/connected-to-server.png)

3. I Object Explorer utökar du **Säkerhet** och sedan **Inloggningar** för att se befintliga inloggningar för din server. Den enda inloggningen på en ny server är inloggningen för serveradministratörskontot.

   ![inloggning för serveradministratör](./media/sql-database-control-access-sql-authentication-get-started/server_admin_login.png)

4. I Object Explorer expanderar du **Databaser**, **Systemdatabaser**, **Huvud**, **Säkerhet** och sedan **Användare** för att se användarkontot som skapades för inloggningen för serveradministratören i den här databasen.

   ![användarkonto för serveradministratör i huvuddatabasen](./media/sql-database-control-access-sql-authentication-get-started/master_database_user_account_for_server_admin.png)

   > [!NOTE]
   > Information om andra användarkonton som visas i noden Användare finns i [Huvudkonton](https://msdn.microsoft.com/library/ms181127.aspx).
   >

5. I Object Explorer högerklickar du på **Huvud** och klickar sedan på **Ny fråga** för att öppna ett frågefönster som är kopplat till huvuddatabasen.
6. I frågefönstret kör du följande fråga för att returnera information om användaren som körde frågan. 

   ```
   SELECT USER;
   ```

   ![välja användarfråga i huvuddatabasen](./media/sql-database-control-access-sql-authentication-get-started/select_user_query_in_master_database.png)

7. I frågefönstret kör du följande fråga för att returnera information om sqladmin-användarens behörigheter i **huvuddatabasen**. 

   ```
   SELECT prm.permission_name
      , prm.class_desc
      , prm.state_desc
      , p2.name as 'Database role'
      , p3.name as 'Additional database role' 
   FROM sys.database_principals p
   JOIN sys.database_permissions prm
      ON p.principal_id = prm.grantee_principal_id
      LEFT JOIN sys.database_principals p2
      ON prm.major_id = p2.principal_id
      LEFT JOIN sys.database_role_members r
      ON p.principal_id = r.member_principal_id
      LEFT JOIN sys.database_principals p3
      ON r.role_principal_id = p3.principal_id
   WHERE p.name = 'sqladmin';
   ```

   ![serveradministratörsbehörigheter i huvuddatabasen](./media/sql-database-control-access-sql-authentication-get-started/server_admin_permissions_in_master_database.png)

   >[!NOTE]
   > Serveradministratören har behörigheter att ansluta till huvuddatabasen, skapa inloggningar och användare, välja information från tabellen sys.sql_logins och lägga till användare till databasrollerna dbmanager och dbcreator. Dessa behörigheter finns utöver de behörigheter som beviljas den allmänna rollen som alla användare ärver behörigheter från (till exempel behörighet att välja information från vissa tabeller). Mer information finns i [Behörigheter](https://msdn.microsoft.com/library/ms191291.aspx).
   >

8. I Object Explorer expanderar du **blankdb**, **Säkerhet** och sedan **Användare** för att se användarkontot som skapades för inloggningen för serveradministratören i den här databasen (och i varje databas).

   ![användarkonton i blankdb](./media/sql-database-control-access-sql-authentication-get-started/user_accounts_in_blankdb.png)

9. I Object Explorer högerklickar du på **blankdb** och klickar sedan på **Ny fråga**.

10. I frågefönstret kör du följande fråga för att returnera information om användaren som körde frågan.

   ```
   SELECT USER;
   ```

   ![välja användarfråga i blankdb-databasen](./media/sql-database-control-access-sql-authentication-get-started/select_user_query_in_blankdb_database.png)

11. I frågefönstret kör du följande fråga för att returnera information om behörigheter för dbo-användaren. 

   ```
   SELECT prm.permission_name
      , prm.class_desc
      , prm.state_desc
      , p2.name as 'Database role'
      , p3.name as 'Additional database role' 
   FROM sys.database_principals AS p
   JOIN sys.database_permissions AS prm
      ON p.principal_id = prm.grantee_principal_id
      LEFT JOIN sys.database_principals AS p2
      ON prm.major_id = p2.principal_id
      LEFT JOIN sys.database_role_members r
      ON p.principal_id = r.member_principal_id
      LEFT JOIN sys.database_principals AS p3
      ON r.role_principal_id = p3.principal_id
   WHERE p.name = 'dbo';
   ```

   ![serveradministratörsbehörigheter i blankdb-databasen](./media/sql-database-control-access-sql-authentication-get-started/server_admin_permissions_in_blankdb_database.png)

   > [!NOTE]
   > Dbo-användaren är medlem i den allmänna rollen och även medlem i den fasta databasrollen för db_owner. Mer information finns i [Roller på databasnivå](https://msdn.microsoft.com/library/ms189121.aspx).
   >

## <a name="create-a-new-user-with-select-permissions"></a>Skapa en ny användare med SELECT-behörigheter

Stegen i den här proceduren visar dig hur du skapar en användare på databasnivå, testar standardbehörigheterna för en ny användare (via den allmänna rollen), beviljar en användare **SELECT**-behörigheter och att se de ändrade behörigheterna.

> [!NOTE]
> Användare på databasnivå kallas även [inneslutna användare](https://msdn.microsoft.com/library/ff929188.aspx) och de ökar databasens flexibilitet. Information om fördelarna med flexibilitet finns i [Configure and manage Azure SQL Database security for geo-restore or failover to a secondary server](sql-database-geo-replication-security-config.md) (Konfigurera och hantera säkerhet för geo-återställning eller redundans till en sekundär server i Azure SQL Database).
>

1. I Object Explorer högerklickar du på **sqldbtutorialdb** och klickar sedan på **Ny fråga**.
2. Kör följande instruktion i det här frågefönstret för att skapa en användare som kallas **user1** i sqldbtutorialdb-databasen.

   ```
   CREATE USER user1
   WITH PASSWORD = 'p@ssw0rd';
   ```
   ![new user user1 sqldbtutorialdb](./media/sql-database-control-access-sql-authentication-get-started/new_user_user1_aw.png)

3. I frågefönstret kör du följande fråga för att returnera information om behörigheter för user1.

   ```
   SELECT prm.permission_name
      , prm.class_desc
      , prm.state_desc
      , p2.name as 'Database role'
      , p3.name as 'Additional database role' 
   FROM sys.database_principals AS p
   JOIN sys.database_permissions AS prm
      ON p.principal_id = prm.grantee_principal_id
      LEFT JOIN sys.database_principals AS p2
      ON prm.major_id = p2.principal_id
      LEFT JOIN sys.database_role_members r
      ON p.principal_id = r.member_principal_id
      LEFT JOIN sys.database_principals AS p3
      ON r.role_principal_id = p3.principal_id
   WHERE p.name = 'user1';
   ```

   ![nya användarbehörigheter i en användardatabas](./media/sql-database-control-access-sql-authentication-get-started/new_user_permissions_in_user_database.png)

   > [!NOTE]
   > En ny användare i en databas har bara de behörigheter som ärvs från den allmänna rollen.
   >

4. Kör följande frågor med hjälp av instruktionen **EXECUTE AS USER** för att göra ett försök att fråga tabellen SalesLT.ProductCategory i sqldbtutorialdb-databas som **user1** med endast de behörigheter som har ärvts från den allmänna rollen.

   ```
   EXECUTE AS USER = 'user1';  
   SELECT * FROM [SalesLT].[ProductCategory];
   REVERT;
   ```

   ![inga select-behörigheter](./media/sql-database-control-access-sql-authentication-get-started/no_select_permissions.png)

   > [!NOTE]
   > Som standard ger den allmänna rollen inte **SELECT**-behörigheter för användarobjekt.
   >

5. Kör följande instruktion för att bevilja **SELECT**-behörigheter i SalesLT.ProductCategory-tabellen till **user1**.

   ```
   GRANT SELECT ON OBJECT::[SalesLT].[ProductCategory] to user1;
   ```

   ![bevilja select-behörigheter](./media/sql-database-control-access-sql-authentication-get-started/grant_select_permissions.png)

6. Kör följande frågor för att kunna ställa frågor till tabellen SalesLT.ProductCategory i sqldbtutorialdb-databasen som **user1**.

   ```
   EXECUTE AS USER = 'user1';  
   SELECT * FROM [SalesLT].[ProductCategory];
   REVERT;
   ```

   ![select-behörigheter](./media/sql-database-control-access-sql-authentication-get-started/select_permissions.png)

## <a name="create-a-database-level-firewall-rule-using-t-sql"></a>Skapa en brandväggsregel på databasnivå med hjälp av T-SQL

Stegen i den här proceduren visar dig hur du skapar en brandväggsregel på databasnivå med den systemlagrade proceduren [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx). En brandväggsregel på databasnivå låter serveradministratören att tillåta att användare får åtkomst till vissa databaser genom Azure SQL Database brandväggen.

> [!NOTE]
> Databasen blir mer flexibel med [brandväggsregler på databasnivå](sql-database-firewall-configure.md). Information om fördelarna med flexibilitet finns i [Configure and manage Azure SQL Database security for geo-restore or failover to a secondary server](sql-database-geo-replication-security-config.md) (Konfigurera och hantera säkerhet för geo-återställning eller redundans till en sekundär server i Azure SQL Database).
>

> [!IMPORTANT]
> Testa en brandväggsregel på databasnivå genom att ansluta från en annan dator (eller ta bort brandväggsregeln på servernivå i Azure Portal.)
>

1. Öppna SQL Server Management Studio på en annan dator som du inte redan har en brandväggsregel på servernivå på.

2. I fönstret **Anslut till server** anger du servernamnet och autentiseringsinformation för att ansluta med SQL Server-autentisering med **user1**-kontot. 
    
   ![Ansluta som user1 utan brandväggsregel&1;](./media/sql-database-control-access-sql-authentication-get-started/connect-user1_no_rule1.png)

3. Klicka på **Alternativ** för att ange databasen som du vill ansluta till och ange sedan **sqldbtutorialdb** i listrutan **Anslut till databas** på fliken **Anslutningsegenskaper**.
   
   ![Ansluta som user1 utan brandväggsregel&2;](./media/sql-database-control-access-sql-authentication-get-started/connect-user1_no_rule2.png)

4. Klicka på **Anslut**. 

   En dialogruta visas som talar om att den dator som du försöker ansluta till SQL Database inte har någon brandväggsregel som ger åtkomst till databasen. 

   ![Ansluta som user1 utan brandväggsregel&4;](./media/sql-database-control-access-sql-authentication-get-started/connect-user1_no_rule4.png)


5. Kopiera klientens IP-adress från den här dialogrutan (den används i steg 8).
6. Klicka på **OK** för att stänga feldialogrutan, men stäng inte dialogrutan **Anslut till server**.
7. Återgå till den dator som du redan har skapat en brandväggsregel på servernivå på. 
8. Anslut till sqldbtutorialdb-databasen i SSMS som serveradministratör och kör följande instruktion för att skapa en brandväggsregel på databasnivå med IP-adressen (eller adressintervallet) från steg 5.  

   ```
   EXEC sp_set_database_firewall_rule @name = N'sqldbtutorialdbFirewallRule', 
     @start_ip_address = 'x.x.x.x', @end_ip_address = 'x.x.x.x';
   ```

   ![lägga till en brandväggsregel](./media/sql-database-control-access-sql-authentication-get-started/user1_add_rule_aw.png)

9. Byt dator igen och klicka på **Anslut** i dialogrutan **Anslut till server** för att ansluta till sqldbtutorialdb som user1. 

   > [!NOTE]
   > När du har skapat brandväggsregeln på databasnivå kan det ta upp till fem minuter att bli aktiv.
   >

10. Expandera **Databaser** i Object Explorer när du har anslutit. Observera att **user1** bara kan se **sqldbtutorialdb**-databasen.

   ![Ansluta som user1 med brandväggsregel&1;](./media/sql-database-control-access-sql-authentication-get-started/connect-user1_rule1.png)

11. Expandera **sqldbtutorialdb** och sedan **Tabeller**. Meddelande om att user1 endast har behörighet att visa en enskild tabell (tabellen **SalesLT.ProductCategory**). 

   ![Ansluta som user1 och visa objects1](./media/sql-database-control-access-sql-authentication-get-started/connect-user1_view_objects1.png)

## <a name="create-a-new-user-as-dbowner-and-a-database-level-firewall-rule"></a>Skapa en ny användare som db_owner och en brandväggsregel på databasnivå

Stegen i den här proceduren visar dig hur du skapar en användare i en annan databas med rollbehörigheten db_owner och skapar en brandväggsregel på databasnivå för den här andra databasen. Den här nya användaren med **db_owner**-roll kommer bara att kunna ansluta till och hantera den här enkla databasen.

1. Byt till datorn med en anslutning till SQL Database med serveradministratörskontot.
2. Öppna ett frågefönster med anslutning till databasen **blankdb** och kör följande instruktion för att skapa en användare med namnet blankdbadmin i databasen blankdb.

   ```
   CREATE USER blankdbadmin
   WITH PASSWORD = 'p@ssw0rd';
   ```

3. I samma frågefönster kör du följande instruktion för att lägga till blankdbadmin-användaren till rollen db_owner. Nu kan användaren utföra alla åtgärder som behövs för att hantera databasen blankdb.

   ```
   ALTER ROLE db_owner ADD MEMBER blankdbadmin; 
   ```

4. I samma frågefönster kör du följande instruktion för att skapa en brandväggsregel på databasnivå genom att köra [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) med IP-adressen från steg 4 i föregående procedur (eller ett intervall av IP-adresser för användare av databasen):

   ```
   EXEC sp_set_database_firewall_rule @name = N'blankdbFirewallRule', 
     @start_ip_address = 'x.x.x.x', @end_ip_address = 'x.x.x.x';
   ```

5. Byt dator (till den som du har skapat en brandväggsregel på databasnivå för) och anslut till databasen blankdb med blankdbadmin-användarkontot.
6. Öppna ett frågefönster till databasen blankdb och kör följande instruktion för att skapa en användare med namnet blankdbuser1 i databasen blankdb.

   ```
   CREATE USER blankdbuser1
   WITH PASSWORD = 'p@ssw0rd';
   ```
 
7. Om det behövs för din utbildningsmiljö skapar du ytterligare en brandväggsregel på databasnivå för användaren. Men om du har skapat brandväggsregeln på databasnivå med hjälp av ett IP-adressintervall kanske det inte behövs.

## <a name="grant-dbmanager-permissions-and-create-a-server-level-firewall-rule"></a>Ge dbmanager-behörigheter och skapa en brandväggsregel på servernivå

Stegen i den här proceduren visar dig hur du skapar en inloggning och användare i huvuddatabasen med behörighet att skapa och hantera nya användardatabaser. Stegen visar dig även hur du skapar ytterligare en brandväggsregel på servernivå med Transact-SQL med hjälp av [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx). 

> [!IMPORTANT]
>Den första brandväggsregeln på servernivå måste alltid skapas i Azure (i Azure Portal, med hjälp av PowerShell eller med REST API:t).
>

> [!IMPORTANT]
> Du måste skapa inloggningar i huvuddatabasen och skapa ett användarkonto från en inloggning för att serveradministratören ska kunna delegera behörighet att skapa databaser till en annan användare. Men att skapa inloggningar och sedan skapa användare från inloggningar minskar flexibiliteten av din miljö.
>

1. Byt till datorn med en anslutning till SQL Database med serveradministratörskontot.
2. Öppna ett frågefönster anslutet till huvuddatabasen och kör följande instruktion för att skapa en inloggning med namnet dbcreator i huvuddatabasen.

   ```
   CREATE LOGIN dbcreator
   WITH PASSWORD = 'p@ssw0rd';
   ```

3. I samma frågefönster 

   ```
   CREATE USER dbcreator
   FROM LOGIN dbcreator;
   ```

3. I samma frågefönster kör du följande fråga för att lägga till dbcreator-användaren till rollen dbmanager. Den här användaren kan nu skapa och hantera databaser som skapats av användaren.

   ```
   ALTER ROLE dbmanager ADD MEMBER dbcreator; 
   ```

4. I samma frågefönster kör du följande fråga för att skapa en brandväggsregel på servernivå genom att köra [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx) med lämplig IP-adress för din miljö:

   ```
   EXEC sp_set_firewall_rule @name = N'dbcreatorFirewallRule', 
     @start_ip_address = 'x.x.x.x', @end_ip_address = 'x.x.x.x';
   ```

5. Byt dator (till den som du har skapat en brandväggsregel på servernivå för) och anslut till huvuddatabasen med dbcreator-användarkontot.
6. Öppna ett frågefönster till huvuddatabasen och kör följande fråga för att skapa en databas som heter foo.

   ```
   CREATE DATABASE FOO (EDITION = 'basic');
   ```
 7. Du kan också ta bort den här databasen om du vill spara pengar med hjälp av följande instruktion:

   ```
   DROP DATABASE FOO;
   ```

## <a name="complete-script"></a>Fullständigt skript

Om du vill skapa inloggningar och användare, lägga till dem i roller, ge dem behörighet, skapa brandväggsregler på databasnivå och skapa brandväggsregler på servernivå, kör du följande instruktioner i lämpliga databaser på din server.

### <a name="master-database"></a>Huvuddatabas
Kör dessa instruktioner i huvuddatabasen med serveradministratörskontot för att lägga till lämpliga IP-adresser eller ett intervall av IP-adresser.

```
CREATE LOGIN dbcreator WITH PASSWORD = 'p@ssw0rd';
CREATE USER dbcreator FROM LOGIN dbcreator;
ALTER ROLE dbmanager ADD MEMBER dbcreator;
EXEC sp_set_firewall_rule @name = N'dbcreatorFirewallRule', 
     @start_ip_address = 'x.x.x.x', @end_ip_address = 'x.x.x.x';
```

### <a name="sqldbtutorialdb-database"></a>sqldbtutorialdb-databas
Kör dessa instruktioner i sqldbtutorialdb-databasen med serveradministratörskontot för att lägga till lämpliga IP-adresser eller ett intervall av IP-adresser.

```
CREATE USER user1 WITH PASSWORD = 'p@ssw0rd';
GRANT SELECT ON OBJECT::[SalesLT].[ProductCategory] to user1;
EXEC sp_set_database_firewall_rule @name = N'sqldbtutorialdbFirewallRule', 
     @start_ip_address = 'x.x.x.x', @end_ip_address = 'x.x.x.x';
```

### <a name="blankdb-database"></a>blankdb-databas
Kör dessa instruktioner i blankdb-databasen med serveradministratörskontot för att lägga till lämpliga IP-adresser eller ett intervall av IP-adresser.

```
CREATE USER blankdbadmin
   WITH PASSWORD = 'p@ssw0rd';
ALTER ROLE db_owner ADD MEMBER blankdbadmin;
EXEC sp_set_database_firewall_rule @name = N'blankdbFirewallRule', 
     @start_ip_address = 'x.x.x.x', @end_ip_address = 'x.x.x.x';
CREATE USER blankdbuser1
   WITH PASSWORD = 'p@ssw0rd';
```

## <a name="next-steps"></a>Nästa steg
- En översikt över åtkomst och kontroll i SQL Database finns i [Åtkomst och kontroll för SQL Database](sql-database-control-access.md).
- En översikt över inloggningar, användare och databasroller i SQL Database finns i [Inloggningar, användare och databasroller](sql-database-manage-logins.md).
- Mer information om huvudkonton finns i [Huvudkonton](https://msdn.microsoft.com/library/ms181127.aspx).
- Mer information om databasroller finns [Databasroller](https://msdn.microsoft.com/library/ms189121.aspx).
- Mer information om brandväggsregler i SQL Database finns [SQL Database-brandväggsregler](sql-database-firewall-configure.md).
- En självstudiekurs som hjälper dig att använda Azure Active Directory-autentisering finns i [Azure AD-autentisering och -auktorisering](sql-database-control-access-aad-authentication-get-started.md).




<!--HONumber=Feb17_HO3-->


