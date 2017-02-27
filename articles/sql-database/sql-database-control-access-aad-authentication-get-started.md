---
title: "AAD-autentisering: Azure SQL Database-brandväggar, autentisering, åtkomst | Microsoft Docs"
description: "I den här självstudien får du lära dig hantera brandväggsregler på server- och databasnivå, Azure Active Directory-autentisering, inloggningar, användare samt regler för att bevilja kontroll och åtkomst till Azure SQL Database-servrar och -databaser med hjälp av SQL Server Management Studio och Transact-SQL. "
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
ms.date: 01/17/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 7d061c083b23de823d373c30f93cccfe1c856ba3
ms.openlocfilehash: 8a6dc7d3dca80782a55e13b53180b1542b61544b


---
# <a name="azure-ad-authentication-access-and-database-level-firewall-rules"></a>Azure AD-autentisering, åtkomst och brandväggsregler på databasnivå
I den här självstudien får du lära dig att hantera Azure Active Directory-autentisering, inloggningar, användare och databasroller för att bevilja åtkomst och behörighet till Azure SQL Database-servrar och -databaser med hjälp av SQL Server Management Studio. Du får lära dig att:

- Visa användarbehörighet i huvuddatabasen och i användardatabaser
- Skapa inloggningar och användare baserat på Azure Active Directory-autentisering
- Bevilja användare serveromfattande och databasspecifika behörigheter
- Logga in på en användardatabas som användare (inte administratör)
- Skapa brandväggsregler på databasnivå för databasanvändare
- Skapa brandväggsregler på servernivå för serveradministratörer.

**Uppskattad tidsåtgång**: Den här kursen tar cirka 45 minuter att slutföra (förutsatt att du redan har uppfyllt kraven).

## <a name="prerequisites"></a>Krav

* Du behöver ett Azure-konto. Du kan [öppna ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) eller [aktivera Visual Studio-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/). 

* Du måste kunna ansluta till Azure Portal med ett konto som är medlem i rollen som prenumerationsägare eller deltagare. Mer information om rollbaserad åtkomstkontroll (RBAC) finns i [Getting started with access management in the Azure portal](../active-directory/role-based-access-control-what-is.md) (Komma igång med åtkomsthantering på Azure Portal).

* Du har slutfört [Komma igång med Azure SQL Database-servrar, databaser och brandväggsregler med hjälp av Azure Portal och SQL Server Management Studio](sql-database-get-started.md) eller motsvarande [PowerShell-version](sql-database-get-started-powershell.md) av den här självstudiekursen. Om du inte har gjort det slutför du kursen eller kör PowerShell-skriptet i slutet av [PowerShell-versionen](sql-database-get-started-powershell.md) av den här kursen innan du fortsätter.

   > [!NOTE]
   > Slutförandet av den relaterade självstudien för SQL Server-autentisering, [SQL-autentisering, inloggningar och användarkonton, databasroller, behörigheter, brandväggsregler på servernivå och databasnivå](sql-database-control-access-sql-authentication-get-started.md), är valfritt – det finns dock begrepp i självstudien som inte upprepas här. Procedurerna i den här självstudien som är kopplade till brandväggar på server- och databasnivå krävs inte om du har slutfört den här relaterade självstudien på samma datorer (med samma IP-adresser). De är därför markerade som valfria. Dessutom utgår skärmbilderna i den här självstudien ifrån att du har slutfört den här relaterade självstudien. 
   >

* Du har skapat och fyllt i en Azure Active Directory. Mer information finns i [Integrera dina lokala identiteter med Azure Active Directory](../active-directory/active-directory-aadconnect.md), [Add your own domain name to Azure AD](../active-directory/active-directory-add-domain.md) (Lägga till dina egna domännamn i Azure AD), [Microsoft Azure now supports federation with Windows Server Active Directory](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/) (Microsoft stöder nu federation med Windows Server Active Directory), [Administering your Azure AD directory](https://msdn.microsoft.com/library/azure/hh967611.aspx) (Administrera Azure Active Directory), [Manage Azure AD using Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx) (Hantera Azure AD med Windows PowerShell) och [Hybrid Identity Required Ports and Protocols](../active-directory/active-directory-aadconnect-ports.md) (Portar och protokoll som krävs för hybrididentiet).

> [!NOTE]
> I den här kursen går vi igenom följande: [Åtkomst och kontroll för SQL Database](sql-database-control-access.md), [inloggningar, användare och databasroller](sql-database-manage-logins.md), [huvudkonton](https://msdn.microsoft.com/library/ms181127.aspx), [databasroller](https://msdn.microsoft.com/library/ms189121.aspx), [SQL Database-brandväggsregler](sql-database-firewall-configure.md) och [Azure Active Directory-autentisering](sql-database-aad-authentication.md). 
>  

## <a name="sign-in-to-the-azure-portal-using-your-azure-account"></a>Logga in på Azure Portal med ditt Azure-konto
Använd din [befintliga prenumeration](https://account.windowsazure.com/Home/Index) och följ dessa steg för att ansluta till Azure Portal.

1. Öppna din webbläsare och anslut till [Azure-portalen](https://portal.azure.com/).
2. Logga in på [Azure-portalen](https://portal.azure.com/).
3. Ange autentiseringsuppgifterna för din prenumeration på sidan **Logga in**.
   
   ![Logga in](./media/sql-database-get-started/login.png)


<a name="create-logical-server-bk"></a>

## <a name="provision-an-azure-active-directory-admin-for-your-sql-logical-server"></a>Etablera en Azure Active Directory-administratör för din logiska SQL-server

I det här avsnittet får du läsa om säkerhetskonfigurationen för din logiska server i Azure Portal.

1. Öppna bladet **SQL Server** för den logiska servern och visa informationen på sidan **Översikt**. Observera att ingen Azure Active Directory-administratör har konfigurerats.

   ![Serveradministratörskontot i Azure Portal](./media/sql-database-control-access-aad-authentication-get-started/sql_admin_portal.png)

2. Klicka på **Ej konfigurerad** i fönstret **Essentials** och öppna bladet **Active Directory-administratör**.

   ![AAD-blad](./media/sql-database-control-access-aad-authentication-get-started/aad_blade.png)

3. Klicka på **Konfigurera administratör** för att öppna bladet **Lägg till administratör** och välj sedan en Active Directory-användare eller ett  gruppkonto som Active Directory-administratör för servern.

   ![Välja AAD-administratörskonto](./media/sql-database-control-access-aad-authentication-get-started/aad_admin.png)

4. Klicka på **Välj** och sedan på **Spara**.

   ![Spara valt AAD-administratörskonto](./media/sql-database-control-access-aad-authentication-get-started/aad_admin_save.png)

> [!NOTE]
> Om du vill granska anslutningsinformationen för den här servern går du till [Hantera servrar](sql-database-manage-servers-portal.md). I den här självstudieserien är det fullständiga servernamnet ”sqldbtutorialserver.database.windows.net”.
>

## <a name="connect-to-sql-server-using-sql-server-management-studio-ssms"></a>Ansluta till SQL-servern med SQL Server Management Studio (SSMS)

1. Om du inte redan har gjort det hämtar du och installerar den senaste versionen av SSMS på [Download SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) (Hämta SQL Server Management Studio). Om du vill hålla programmen uppdaterade, meddelar den senaste versionen av SSMS dig om när det finns en ny version tillgänglig för nedladdning.

2. När installationen är klar skriver du **Microsoft SQL Server Management Studio** i sökrutan i Windows och klickar på **Retur** för att öppna SSMS.

   ![SQL Server Management Studio](./media/sql-database-get-started/ssms.png)

3. I dialogrutan **Anslut till server** väljer du någon av autentiseringsmetoderna för Active Directory och anger sedan lämplig autentiseringsinformation. Information om hur du väljer en metod finns [Azure Active Directory authentication](sql-database-aad-authentication.md) (Azure Active Directory-autentisering) och [SSMS support for Azure AD MFA](sql-database-ssms-mfa-authentication.md) (SSMS-stöd för Azure AD MFA).

   ![anslut till server med aad](./media/sql-database-control-access-aad-authentication-get-started/connect_to_server_with_aad.png)

4. Ange nödvändig information för att ansluta till SQL-servern med hjälp av SQL Server-autentisering och serveradministratörskontot.

5. Klicka på **Anslut**.

   ![ansluten till server med aad](./media/sql-database-control-access-aad-authentication-get-started/connected_to_server_with_aad.png)

## <a name="view-the-server-admin-account-and-its-permissions"></a>Visa serveradministratörskontot och dess behörigheter 
I den här delen av kursen visar du information om serveradministratörskontot och dess behörigheter i huvuddatabasen och användardatabaserna.

1. I Object Explorer expanderar du **Databaser**, **Systemdatabaser**, **Huvud**, **Säkerhet** och sedan **Användare**. Observera att ett användarkonto har skapats i huvuddatabasen för Active Directory-administratören. Observera också att en inloggning inte har skapats för användarkontot i Active Directory-administratörskontot.

   ![användarkonto för AAD-administratör i huvuddatabasen](./media/sql-database-control-access-aad-authentication-get-started/master_database_user_account_for_AAD_admin.png)

   > [!NOTE]
   > Information om andra användarkonton som visas finns i [Huvudkonton](https://msdn.microsoft.com/library/ms181127.aspx).
   >

2. I Object Explorer högerklickar du på **Huvud** och klickar sedan på **Ny fråga** för att öppna ett frågefönster som är kopplat till huvuddatabasen.
3. I frågefönstret kör du följande fråga för att returnera information om användaren som körde frågan. Observera att user@microsoft.com returneras för det användarkonto som kör den här frågan (ett annat resultat visas när vi frågar en användardatabas senare i den här proceduren).

   ```
   SELECT USER;
   ```

   ![välja användarfråga i huvuddatabasen](./media/sql-database-control-access-aad-authentication-get-started/select_user_query_in_master_database.png)

4. I frågefönstret kör du följande fråga för att returnera information om Active Directory-användarens behörigheter. Observera att Active Directory-adminanvändaren har behörighet att ansluta till huvuddatabasen, skapa inloggningar och användare, välja information från tabellen sys.sql_logins och lägga till användare till databasrollerna dbmanager och dbcreator. Dessa behörigheter finns utöver de behörigheter som beviljas den allmänna rollen som alla användare ärver behörigheter från (till exempel behörighet att välja information från vissa tabeller). Mer information finns i [Behörigheter](https://msdn.microsoft.com/library/ms191291.aspx).

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
   WHERE p.name = 'user@microsoft.com';
   ```

   ![aad-administratörsbehörigheter i huvuddatabasen](./media/sql-database-control-access-aad-authentication-get-started/aad_admin_permissions_in_master_database.png)

6. I Object Explorer expanderar du **blankdb**, **Säkerhet** och sedan **Användare**. Observera att det inte finns något användarkonto som heter user@microsoft.com i den här databasen.

   ![användarkonton i blankdb](./media/sql-database-control-access-aad-authentication-get-started/user_accounts_in_blankdb.png)

7. I Object Explorer högerklickar du på **blankdb** och klickar sedan på **Ny fråga**.

8. I frågefönstret kör du följande fråga för att returnera information om användaren som körde frågan. Observera att dbo returneras för det användarkonto som kör frågan (som standard mappas serveradministratörsinloggningen till dbo-användarkontot i varje användardatabas).

   ```
   SELECT USER;
   ```

   ![välja användarfråga i blankdb-databasen](./media/sql-database-control-access-aad-authentication-get-started/select_user_query_in_blankdb_database.png)

9. I frågefönstret kör du följande fråga för att returnera information om behörigheter för dbo-användaren. Observera att dbo är medlem i den allmänna rollen och även medlem i den fasta databasrollen för db_owner. Mer information finns i [Roller på databasnivå](https://msdn.microsoft.com/library/ms189121.aspx).

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

   ![serveradministratörsbehörigheter i blankdb-databasen](./media/sql-database-control-access-aad-authentication-get-started/aad_admin_permissions_in_blankdb_database.png)

10. Du kan också upprepa föregående tre steg för AdventureWorksLT-användardatabasen.

## <a name="create-a-new-user-in-the-adventureworkslt-database-with-select-permissions"></a>Skapa en ny användare i AdventureWorksLT-databasen med SELECT-behörigheter

I det här avsnittet skapar du ett användarkonto i AdventureWorksLT-databasen baserat på en användares huvudnamn på en Azure AD-användare eller visningsnamn för en Azure AD-grupp, testar användarens behörigheter som medlem i den allmänna rollen, beviljar användaren SELECT-behörighet och testar sedan användarens behörigheter på nytt.

> [!NOTE]
> Databasen blir mer flexibel med användare på databasnivå ([inneslutna användare](https://msdn.microsoft.com/library/ff929188.aspx)). Den funktionen ska vi utforska vidare i senare självstudier.
>

1. I Object Explorer högerklickar du på **AdventureWorksLT** och klickar sedan på **Ny fråga** för att öppna ett frågefönster som är kopplat till AdventureWorksLT-databasen.
2. Kör följande sats för att skapa ett användarkonto i databasen AdventureWorksLT för en användare i Microsoft-domänen aaduser1.

   ```
   CREATE USER [aaduser1@microsoft.com]
   FROM EXTERNAL PROVIDER;
   ```
   ![ny användare aaduser1@microsoft.com AdventureWorksLT](./media/sql-database-control-access-aad-authentication-get-started/new_user_aaduser1@microsoft.com_aw.png)

3. I frågefönstret kör du följande fråga för att returnera information om behörigheter för user1. Observera att user1 endast har behörigheter som har ärvts från den allmänna rollen.

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
   WHERE p.name = 'aaduser1@microsoft.com';
   ```

   ![nya användarbehörigheter i en användardatabas](./media/sql-database-control-access-aad-authentication-get-started/new_user_permissions_in_user_database.png)

4. Kör följande frågor som user1 för att fråga en tabell i AdventureWorksLT-databasen.

   ```
   EXECUTE AS USER = 'aaduser1@microsoft.com';  
   SELECT * FROM [SalesLT].[ProductCategory];
   REVERT;
   ```

   ![inga select-behörigheter](./media/sql-database-control-access-aad-authentication-get-started/no_select_permissions.png)

5. Kör följande instruktion för att bevilja SELECT-behörigheter i ProductCategory-tabellen i SalesLT-schema till user1.

   ```
   GRANT SELECT ON OBJECT::[SalesLT].[ProductCategory] to [aaduser1@microsoft.com];
   ```

   ![bevilja select-behörigheter](./media/sql-database-control-access-aad-authentication-get-started/grant_select_permissions.png)

6. Kör följande frågor som user1 för att fråga en tabell i AdventureWorksLT-databasen.

   ```
   EXECUTE AS USER = 'aaduser1@microsoft.com';  
   SELECT * FROM [SalesLT].[ProductCategory];
   REVERT;
   ```

   ![select-behörigheter](./media/sql-database-control-access-aad-authentication-get-started/select_permissions.png)

## <a name="create-a-database-level-firewall-rule-for-adventureworkslt-database-users"></a>Skapa en brandväggsregel på databasnivå för AdventureWorksLT-databasanvändare

> [!NOTE]
> Du behöver inte utföra den här proceduren om du har slutfört motsvarande procedur i den relaterade självstudien för SQL Server-autentisering, [SQL-autensiering och -auktorisering](sql-database-control-access-sql-authentication-get-started.md) och utbildar dig på samma dator med samma IP-adress.
>

I det här avsnittet försöker du att logga in med det nya användarkontot från en dator med en annan IP-adress, skapar en brandväggsregel på databasnivå som serveradministratör och loggar sedan in med den nya brandväggsregeln. 

> [!NOTE]
> Databasen blir mer flexibel med [brandväggsregler på databasnivå](sql-database-firewall-configure.md). Den funktionen ska vi utforska vidare i senare självstudiekurser.
>

1. Öppna SQL Server Management Studio på en annan dator (som du inte redan har skapat en brandväggsregel på servernivå på).

   > [!IMPORTANT]
   > Använd alltid den senaste versionen av SSMS. Den hittar du på [Hämta SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx). 
   >

2. I fönstret **Anslut till server** anger du servernamnet och autentiseringsinformation för att ansluta med SQL Server-autentisering med aaduser1@microsoft.com-kontot. 
    
   ![Ansluta som aaduser1@microsoft.com utan brandväggsregel&1;](./media/sql-database-control-access-aad-authentication-get-started/connect_aaduser1_no_rule1.png)

3. Klicka på **Alternativ** för att ange databasen som du vill ansluta till och ange sedan **AdventureWorksLT** i listrutan **Anslut till databas** på fliken **Anslutningsegenskaper**.
   
   ![Ansluta som aaduser1 utan brandväggsregel&2;](./media/sql-database-control-access-aad-authentication-get-started/connect_aaduser1_no_rule2.png)

4. Klicka på **Anslut**. En dialogruta visas som talar om att den dator som du försöker ansluta till SQL Database inte har någon brandväggsregel som ger åtkomst till databasen. Dialogrutan kan se ut på två sätt beroende på vilka steg du har utfört tidigare för brandväggar, men vanligtvis visas den första dialogrutan.

   ![Ansluta som user1 utan brandväggsregel&3;](./media/sql-database-control-access-aad-authentication-get-started/connect_aaduser1_no_rule3.png)

   ![Ansluta som user1 utan brandväggsregel&4;](./media/sql-database-control-access-aad-authentication-get-started/connect_aaduser1_no_rule4.png)

   > [!NOTE]
   > De senaste versionerna av SSMS innehåller funktioner för att tillåta prenumerationsägare och prenumerationsdeltagare att logga in på Microsoft Azure och skapa en brandväggsregel på servernivå.
   > 

4. Kopiera klientens IP-adress från den här dialogrutan (den används i steg 7).
5. Klicka på **Avbryt**, men stäng inte dialogrutan **Anslut till server**.
6. Återgå till den dator som du redan har skapat en brandväggsregel på servernivå för och anslut till servern med serveradministratörskontot.
7. I ett nytt frågefönster (med anslutning till databasen AdventureWorksLT-databasen som serveradministratör) kör du följande instruktion för att skapa en brandväggsregel på databasnivå genom att köra [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) med IP-adressen från steg 4:

   ```
   EXEC sp_set_database_firewall_rule @name = N'AdventureWorksLTFirewallRule', 
     @start_ip_address = 'x.x.x.x', @end_ip_address = 'x.x.x.x';
   ```

   ![lägga till brandväggsregel4 på databasnivå](./media/sql-database-control-access-aad-authentication-get-started/aaduser1_add_rule_aw.png)

8. Byt dator igen och klicka på **Anslut** i dialogrutan **Anslut till server** för att ansluta till AdventureWorksLT som aaduser1. 

9. I Object Explorer expanderar du **Databaser**, **AdventureWorksLT**, och sedan **Tabeller**. Meddelande om att user1 endast har behörighet att visa en enskild tabell (tabellen **SalesLT.ProductCategory**). 

10. I Object Explorer högerklickar du på **SalesLT.ProductCategory** och klickar på alternativet för att **välja de 1 000 första raderna**.   

## <a name="next-steps"></a>Nästa steg
- En översikt över åtkomst och kontroll i SQL Database finns i [Åtkomst och kontroll för SQL Database](sql-database-control-access.md).
- En översikt över inloggningar, användare och databasroller i SQL Database finns i [Inloggningar, användare och databasroller](sql-database-manage-logins.md).
- Mer information om huvudkonton finns i [Huvudkonton](https://msdn.microsoft.com/library/ms181127.aspx).
- Mer information om databasroller finns [Databasroller](https://msdn.microsoft.com/library/ms189121.aspx).
- Mer information om brandväggsregler i SQL Database finns [SQL Database-brandväggsregler](sql-database-firewall-configure.md).




<!--HONumber=Feb17_HO3-->


