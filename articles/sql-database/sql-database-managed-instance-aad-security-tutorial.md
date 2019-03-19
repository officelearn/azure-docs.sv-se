---
title: Säkerhet för hanterad Azure SQL Database-instans med hjälp av Azure AD-serverhuvudkonton (inloggningar) | Microsoft Docs
description: Lär dig olika tekniker och funktioner för att skydda en hanterad instans i Azure SQL Database och använda Azure AD-serverhuvudkonton (inloggningar)
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
manager: craigg
ms.date: 02/20/2019
ms.openlocfilehash: 59971454af4fa2b43d24df1d5876b30524d65d4b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57904003"
---
# <a name="tutorial-managed-instance-security-in-azure-sql-database-using-azure-ad-server-principals-logins"></a>Självstudier: Säkerhet för hanterad instans i Azure SQL Database med hjälp av Azure AD-serverhuvudkonton (inloggningar)

Hanterade instanser har nästan samma säkerhetsfunktioner som den senaste lokala SQL Server-databasmotorn (Enterprise Edition):

- Begränsa åtkomst i en isolerad miljö
- Använd autentiseringsmekanismer som kräver identitet (Azure AD, SQL-autentisering)
- Använda auktorisering med rollbaserade medlemskap och behörigheter
- Aktivera säkerhetsfunktioner

I den här guiden får du lära dig att:

> [!div class="checklist"]
> - Skapa ett serverhuvudkonto (inloggning) för Azure Active Directory (AD) för en hanterad instans
> - Bevilja behörigheter till Azure AD-serverhuvudkonton (inloggningar) i en hanterad instans
> - Skapa Azure AD-användare från Azure AD-serverhuvudkonton (inloggningar)
> - Tilldela behörigheter till Azure AD-användare och hantera databassäkerhet
> - Använda personifiering med Azure AD-användare
> - Använda frågor över flera databaser med Azure AD-användare
> - Läs om säkerhetsfunktioner som hotskydd, granskning, datamaskering och kryptering

> [!NOTE]
> Azure AD-serverhuvudkonton (inloggningar) för hanterade instanser är i **offentlig förhandsversion**.

Mer information finns i artiklarna [Azure SQL Database Managed Instance overview](sql-database-managed-instance-index.yml) (Översikt över Azure SQL Database Managed Instance) och [Funktioner](sql-database-managed-instance.md).

## <a name="prerequisites"></a>Förutsättningar

För att kunna slutföra den här självstudien behöver du följande:

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS)
- En hanterad Azure SQL Database-instans
  - Följ den här artikeln: [Snabbstart: Skapa en hanterad Azure SQL Database-instans](sql-database-managed-instance-get-started.md)
- Kunna komma åt din hanterade instans och [ha etablerat en Azure AD-administratör för den hanterade instansen](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance). Du kan läsa mer här:
    - [Ansluta program till en hanterad instans](sql-database-managed-instance-connect-app.md) 
    - [Anslutningsarkitektur för hanterade instanser](sql-database-managed-instance-connectivity-architecture.md)
    - [Konfigurera och hantera Azure Active Directory-autentisering med SQL](sql-database-aad-authentication-configure.md)

## <a name="limiting-access-to-your-managed-instance"></a>Begränsa åtkomsten till din hanterade instans

Hanterade instanser kan endast nås via en privat IP-adress. Det finns inga tjänstslutpunkter som är tillgängliga för att ansluta till en hanterad instans utanför instansens nätverk. Ungefär som i en isolerad lokal SQL Server-miljö behöver program eller användare åtkomst till den hanterade instansens nätverk (VNet) innan en anslutning kan upprättas. Mer information finns i artikeln [Ansluta program till en hanterad instans](sql-database-managed-instance-connect-app.md).

> [!NOTE] 
> Eftersom hanterade instanser bara kan nås från sitt VNET gäller inte [SQL Database-brandväggsreglerna](sql-database-firewall-configure.md). Hanterade instanser har en egen [inbyggd brandvägg](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md).

## <a name="create-an-azure-ad-server-principal-login-for-a-managed-instance-using-ssms"></a>Skapa ett Azure AD-serverhuvudkonto (inloggning) för en hanterad instans med hjälp av SSMS

Det första Azure AD-serverhuvudkontot (inloggning) måste skapas av det SQL Server-standardkonto (icke-Azure AD) som är `sysadmin`. I följande artiklar finns exempel på hur du ansluter till en hanterad instans:

- [Snabbstart: Konfigurera en virtuell Azure-dator för att ansluta till en hanterad instans](sql-database-managed-instance-configure-vm.md)
- [Snabbstart: Konfigurera en punkt-till-plats-anslutning till en hanterad instans från den lokala miljön](sql-database-managed-instance-configure-p2s.md)

> [!IMPORTANT]
> Den Azure AD-administratör som konfigurerade den hanterade instansen kan inte användas för att skapa ett Azure AD-serverhuvudkonto (inloggning) i den hanterade instansen. Du måste skapa det första Azure AD-serverhuvudkontot (inloggning) med hjälp av ett SQL Server-konto som är `sysadmin`. Det här är en tillfällig begränsning som kommer att tas bort när Azure AD-serverhuvudkonton (inloggningar) blir allmänt tillgängliga. Följande felmeddelande visas om du försöker använda ett Azure AD-administratörskonto för att skapa inloggningen: `Msg 15247, Level 16, State 1, Line 1 User does not have permission to perform this action.`

1. Logga in på din hanterade instans med ett SQL Server-konto av standardtyp (inte Azure AD) som är en `sysadmin`, med hjälp av [SQL Server Management Studio](sql-database-managed-instance-configure-p2s.md#use-ssms-to-connect-to-the-managed-instance).

2. I **Object Explorer** högerklickar du på servern och väljer **Ny fråga**.

3. Använd följande syntax i frågefönstret för att skapa en inloggning för ett lokalt Azure AD-konto:

    ```sql
    USE master
    GO
    CREATE LOGIN login_name FROM EXTERNAL PROVIDER
    GO
    ```

    Det här exemplet skapar en inloggning för kontot nativeuser@aadsqlmi.onmicrosoft.com.

    ```sql
    USE master
    GO
    CREATE LOGIN [nativeuser@aadsqlmi.onmicrosoft.com] FROM EXTERNAL PROVIDER
    GO
    ```

4. Välj **Kör** i verktygsfältet för att skapa inloggningen.

5. Kontrollera den nyligen tillagda inloggningen genom att köra följande T-SQL-kommando:

    ```sql
    SELECT *  
    FROM sys.server_principals;  
    GO
    ```

    ![native-login.png](media/sql-database-managed-instance-security-tutorial/native-login.png)

Mer information finns i [SKAPA INLOGGNING](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current).

## <a name="granting-permissions-to-allow-the-creation-of-managed-instance-logins"></a>Ge behörighet att skapa inloggningar för hanterade instanser

För att andra Azure AD-serverhuvudkonton (inloggningar) ska kunna skapas måste SQL Server-roller eller -behörigheter tilldelas till huvudkontot (SQL eller Azure AD).

### <a name="sql-authentication"></a>SQL-autentisering

- Om inloggningen är ett SQL-huvudkonto kan inloggningar som ingår i rollen `sysadmin` använda kommandot create för att skapa inloggningar för en Azure AD-konto.

### <a name="azure-ad-authentication"></a>Azure AD-autentisering

- För att ge det nyligen skapade Azure AD-serverhuvudkontot (inloggning) möjlighet att skapa andra inloggningar för andra Azure AD-användare, grupper eller program beviljar du inloggningen serverrollen `sysadmin` eller `securityadmin`. 
- Som minst måste behörigheten **ALTER ANY LOGIN** ges till Azure AD-serverhuvudkontot (inloggning) för att skapa andra Azure AD-serverhuvudkonton (inloggningar). 
- Som standard är den standardbehörighet som beviljas för nyligen skapade Azure AD-serverhuvudkonton (inloggningar) i huvuddatabasen följande: **CONNECT SQL** och **VIEW ANY DATABASE**.
- Serverrollen `sysadmin` kan ges till många Azure AD-serverhuvudkonton (inloggningar) i en hanterad instans.

Så här lägger du till inloggningen till serverrollen `sysadmin`:

1. Logga in på den hanterade instansen igen eller använd den befintliga anslutningen med SQL-huvudkontot som är en `sysadmin`.

1. I **Object Explorer** högerklickar du på servern och väljer **Ny fråga**.

1. Bevilja Azure AD-serverhuvudkontot (inloggning) serverrollen `sysadmin` med hjälp av följande T-SQL-syntax:

    ```sql
    ALTER SERVER ROLE sysadmin ADD MEMBER login_name
    GO
    ```

    I följande exempel beviljas serverrollen `sysadmin` till inloggningen nativeuser@aadsqlmi.onmicrosoft.com

    ```sql
    ALTER SERVER ROLE sysadmin ADD MEMBER [nativeuser@aadsqlmi.onmicrosoft.com]
    GO
    ```

## <a name="create-additional-azure-ad-server-principals-logins-using-ssms"></a>Skapa ytterligare Azure AD-serverhuvudkonton (inloggningar) med hjälp av SSMS

När Azure AD-serverhuvudkontot (inloggning) har skapats och har getts `sysadmin`-privilegier kan den inloggningen skapa ytterligare inloggningar med hjälp av satsen **FROM EXTERNAL PROVIDER** (Från extern provider) med **CREATE LOGIN** (Skapa inloggning).

1. Anslut till den hanterade instansen med Azure AD-serverhuvudkontot (inloggning) med hjälp av SQL Server Management Studio. Ange den hanterade instansens värdnamn. Det finns tre alternativ att välja mellan när du loggar in med ett Azure AD-konto för autentisering i SSMS:

   - Active Directory – Universell med stöd för MFA
   - Active Directory – lösenord
   - Active Directory – integrerad </br>

     ![ssms-login-prompt.png](media/sql-database-managed-instance-security-tutorial/ssms-login-prompt.png)

     Mer information finns i följande artikel: [Universell autentisering med SQL Database och SQL Data Warehouse (SSMS-stöd för MFA)](sql-database-ssms-mfa-authentication.md)

1. Välj **Active Directory – Universell med stöd för MFA**. Sedan öppnas ett inloggningsfönster för Multi-Factor Authentication (MFA). Logga in med ditt Azure AD-lösenord.

    ![mfa-login-prompt.png](media/sql-database-managed-instance-security-tutorial/mfa-login-prompt.png)

1. I SSMS **Object Explorer** högerklickar du på servern och väljer **Ny fråga**.
1. Använd följande syntax i frågefönstret för att skapa en inloggning för ytterligare ett Azure AD-konto:

    ```sql
    USE master
    GO
    CREATE LOGIN login_name FROM EXTERNAL PROVIDER
    GO
    ```

    Det här exemplet skapar en inloggning för Azure AD-användaren bob@aadsqlmi.net, vars domän aadsqlmi.net är federerad med Azure AD:s aadsqlmi.onmicrosoft.com.

    Kör följande T-SQL-kommando. Federerade Azure AD-konton är motsvarigheten till hanterade instanser när det gäller lokala inloggningar och användare i Windows.

    ```sql
    USE master
    GO
    CREATE LOGIN [bob@aadsqlmi.net] FROM EXTERNAL PROVIDER
    GO
    ```

1. Skapa en databas i den hanterade instansen med syntaxen [CREATE DATABASE](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-mi-current). Den här databasen används för att testa användarinloggningar i nästa avsnitt.
    1. I **Object Explorer** högerklickar du på servern och väljer **Ny fråga**.
    1. Använd följande syntax i frågefönstret för att skapa en databas med namnet **MyMITestDB**.

        ```sql
        CREATE DATABASE MyMITestDB;
        GO
        ```

1. Skapa en inloggning till en hanterad instans för en grupp i Azure AD. Gruppen måste finnas i Azure AD innan du kan lägga till inloggningen till den hanterade instansen. Läs [Skapa en basgrupp och lägga till medlemmar med hjälp av Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Skapa en grupp _mygroup_ och lägg till medlemmar i den här gruppen.

1. Öppna ett nytt frågefönster i SQL Server Management Studio.

    Det här exemplet förutsätter att det finns en grupp som heter _mygroup_ i Azure AD. Kör följande kommando:

    ```sql
    USE master
    GO
    CREATE LOGIN [mygroup] FROM EXTERNAL PROVIDER
    GO
    ```

1. Testa att logga in på den hanterade instansen med den nyligen skapade inloggningen eller gruppen. Öppna en ny anslutning till den hanterade instansen och använd den nya inloggningen som autentisering.
1. I **Object Explorer** högerklickar du på servern och väljer **Ny fråga** för den nya anslutningen.
1. Kontrollera serverbehörigheter för det nyligen skapade Azure AD-serverhuvudkontot (inloggning) genom att köra följande kommando:

     ```sql
     SELECT * FROM sys.fn_my_permissions (NULL, 'DATABASE')
     GO
     ```

> [!NOTE]
> Azure AD-gästanvändare kan bara ges inloggningar till hanterade instanser när de läggs till som en del av en Azure AD-grupp. En Azure AD-gästanvändare är ett konto som bjudits in till den Azure AD som den hanterade instansen tillhör, från en annan Azure AD. Till exempel kan joe@contoso.com (Azure AD-konto) eller steve@outlook.com (MSA-konto) läggas till en grupp i Azure AD aadsqlmi. När användare läggs till i en grupp kan du skapa en inloggning för gruppen i den hanterade instansens **huvuddatabas** med syntaxen **CREATE LOGIN**. Gästanvändare som är medlemmar i gruppen kan ansluta till den hanterade instansen med sin aktuella inloggning (som joe@contoso.com eller steve@outlook.com).

## <a name="create-an-azure-ad-user-from-the-azure-ad-server-principal-login-and-give-permissions"></a>Skapa en Azure AD-användare från Azure AD-serverhuvudkontot (inloggning) och ge behörigheter

Behörighet till enskilda databaser fungerar ungefär på samma sätt i hanterade instanser som i SQL Server lokalt. En användare kan skapas från en befintlig inloggning i en databas och få behörigheter för databasen eller läggas till i en databasroll.

Nu när vi har skapat en databas som heter **MyMITestDB**, och en inloggning som endast har standardbehörigheterna, är nästa steg att skapa en användare från den inloggningen. För tillfället kan inloggningen ansluta till den hanterade instansen och se alla databaser, men inte interagera med databaserna. Om du loggar in med Azure AD-kontot som har standardbehörigheterna och försöker expandera den nyligen skapade databasen visas följande fel:

![ssms-db-not-accessible.png](media/sql-database-managed-instance-security-tutorial/ssms-db-not-accessible.png)

Läs mer om hur du beviljar databasbehörigheter i [Getting Started with Database Engine Permissions](/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions) (Komma igång med behörigheter för databasmotorn).

### <a name="create-an-azure-ad-user-and-create-a-sample-table"></a>Skapa en Azure AD-användare och skapa en exempeltabell

1. Logga in på den hanterade instansen med ett `sysadmin`-konto i SQL Server Management Studio.
1. I **Object Explorer** högerklickar du på servern och väljer **Ny fråga**.
1. I frågefönstret använder du följande syntax för att skapa en Azure AD-användare från ett Azure AD-serverhuvudkonto (inloggning):

    ```sql
    USE <Database Name> -- provide your database name
    GO
    CREATE USER user_name FROM LOGIN login_name
    GO
    ```

    I följande exempel skapas en användare bob@aadsqlmi.net från inloggningen bob@aadsqlmi.net:

    ```sql
    USE MyMITestDB
    GO
    CREATE USER [bob@aadsqlmi.net] FROM LOGIN [bob@aadsqlmi.net]
    GO
    ```

1. Det finns även stöd för att skapa en Azure AD-användare från ett Azure AD-serverhuvudkonto (inloggning) som är en grupp.

    I följande exempel skapas en inloggning för Azure AD-gruppen _mygroup_ som finns i din Azure AD.

    ```sql
    USE MyMITestDB
    GO
    CREATE USER [mygroup] FROM LOGIN [mygroup]
    GO
    ```

    Alla användare som tillhör **mygroup** kan komma åt databasen **MyMITestDB**.

    > [!IMPORTANT]
    > När du skapar en **USER** (Användare) från ett Azure AD-serverhuvudkonto (inloggning) anger du samma user_name som login_name från **LOGIN** (Inloggning).

    Mer information finns i [SKAPA ANVÄNDARE](/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current).

1. Skapa en testtabell med följande T-SQL-kommando i ett nytt frågefönster:

    ```sql
    USE MyMITestDB
    GO
    CREATE TABLE TestTable
    (
    AccountNum varchar(10),
    City varchar(255),
    Name varchar(255),
    State varchar(2)
    );
    ```

1. Skapa en anslutning i SSMS med användaren som har skapats. Lägg märke till att du inte kan se tabellen **TestTable** som skapades av `sysadmin` tidigare. Vi behöver ge användaren behörigheter att läsa data från databasen.

1. Du kan kontrollera användarens aktuella behörighet genom att köra följande kommando:

    ```sql
    SELECT * FROM sys.fn_my_permissions('MyMITestDB','DATABASE')
    GO
    ```

### <a name="add-users-to-database-level-roles"></a>Lägga till användare till roller på databasnivå

För att användaren ska kunna se data i databasen ger vi användaren [roller på databasnivå](/sql/relational-databases/security/authentication-access/database-level-roles).

1. Logga in på den hanterade instansen med ett `sysadmin`-konto i SQL Server Management Studio.

1. I **Object Explorer** högerklickar du på servern och väljer **Ny fråga**.

1. Bevilja Azure AD-användaren `db_datareader`-databasrollen med hjälp av följande T-SQL-syntax:

    ```sql
    Use <Database Name> -- provide your database name
    ALTER ROLE db_datareader ADD MEMBER user_name
    GO
    ```

    I följande exempel får användaren bob@aadsqlmi.net och gruppen _mygroup_ `db_datareader` behörigheter för **MyMITestDB**-databasen:

    ```sql
    USE MyMITestDB
    GO
    ALTER ROLE db_datareader ADD MEMBER [bob@aadsqlmi.net]
    GO
    ALTER ROLE db_datareader ADD MEMBER [mygroup]
    GO
    ```

1. Kontrollera att Azure AD-användaren som har skapats i databasen finns genom att köra följande kommando:

    ```sql
    SELECT * FROM sys.database_principals
    GO
    ```

1. Skapa en ny anslutning till den hanterade instansen med användaren som lades till i rollen `db_datareader`.
1. Expandera databasen i **Object Explorer** du vill se tabellen.

    ![ssms-test-table.png](media/sql-database-managed-instance-security-tutorial/ssms-test-table.png)

1. Öppna ett nytt frågefönster och kör följande SELECT-sats:

    ```sql
    SELECT *
    FROM TestTable
    ```

    Kan du se data från tabellen? Du bör se de kolumner som returneras.

    ![ssms-test-table-query.png](media/sql-database-managed-instance-security-tutorial/ssms-test-table-query.png)

## <a name="impersonating-azure-ad-server-level-principals-logins"></a>Personifiera Azure AD-huvudkonton på servernivå (inloggningar)

Hanterade instanser har stöd för personifiering av Azure AD-huvudkonton på servernivå (inloggningar).

### <a name="test-impersonation"></a>Testa personifiering

1. Logga in på den hanterade instansen med ett `sysadmin`-konto i SQL Server Management Studio.

1. I **Object Explorer** högerklickar du på servern och väljer **Ny fråga**.

1. Kör följande kommando i frågefönstret för att skapa en lagrad procedur:

    ```sql
    USE MyMITestDB
    GO  
    CREATE PROCEDURE dbo.usp_Demo  
    WITH EXECUTE AS 'bob@aadsqlmi.net'  
    AS  
    SELECT user_name();  
    GO
    ```

1. Använd följande kommando för att se när användare som du personifiera när du kör den lagrade proceduren **bob\@aadsqlmi.net**.

    ```sql
    Exec dbo.usp_Demo
    ```

1. Testa personifieringen med hjälp av instruktionen KÖRA SOM INLOGGNING:

    ```sql
    EXECUTE AS LOGIN = 'bob@aadsqlmi.net'
    GO
    SELECT SUSER_SNAME()
    REVERT
    GO
    ```

> [!NOTE]
> Endast SQL-huvudkonton på servernivå (inloggningar) som ingår i rollen `sysadmin` kan utföra följande åtgärder som riktar in sig på Azure AD-huvudkonton: 
> - KÖRA SOM ANVÄNDARE
> - KÖRA SOM INLOGGNING

## <a name="using-cross-database-queries-in-managed-instances"></a>Använda databasöverskridande frågor i hanterade instanser

Databasöverskridande frågor stöds för Azure AD-konton med Azure AD-serverhuvudkonton (inloggningar). Vi måste skapa en annan databas och tabell för att testa en databasöverskridande fråga med en Azure AD-grupp. Du behöver inte skapa en annan databas och tabell om det redan finns.

1. Logga in på den hanterade instansen med ett `sysadmin`-konto i SQL Server Management Studio.
1. I **Object Explorer** högerklickar du på servern och väljer **Ny fråga**.
1. I frågefönstret använder du följande kommando för att skapa en databas med namnet **MyMITestDB2** och en tabell med namnet **TestTable2**:

    ```sql
    CREATE DATABASE MyMITestDB2;
    GO
    USE MyMITestDB2
    GO
    CREATE TABLE TestTable2
    (
    EmpId varchar(10),
    FirstName varchar(255),
    LastName varchar(255),
    Status varchar(10)
    );
    ```

1. I ett nytt frågefönster kör du följande kommando för att skapa användaren _mygroup_ i den nya databasen **MyMITestDB2**, och beviljar SELECT-behörigheter för databasen för _mygroup_:

    ```sql
    USE MyMITestDB2
    GO
    CREATE USER [mygroup] FROM LOGIN [mygroup]
    GO
    GRANT SELECT TO [mygroup]
    GO
    ```

1. Logga in på den hanterade instansen i SQL Server Management Studio som medlem i Azure AD-gruppen _mygroup_. Öppna ett nytt frågefönster och kör instruktionen SELECT över flera databaser:

    ```sql
    USE MyMITestDB
    SELECT * FROM MyMITestDB2..TestTable2
    GO
    ```

    Du bör se tabellresultaten från **TestTable2**.

## <a name="additional-scenarios-supported-for-azure-ad-server-principals-logins-public-preview"></a>Fler scenarier som stöds för Azure AD-serverhuvudkonton (inloggningar) (offentlig förhandsversion) 

- SQL Agent-hantering och -jobbkörningar stöds för Azure AD-serverhuvudkonton (inloggningar).
- Åtgärder för databassäkerhetskopiering och -återställning kan köras av Azure AD-serverhuvudkonton (inloggningar).
- [Granskning](sql-database-managed-instance-auditing.md) av alla instruktioner som är relaterade till Azure AD-serverhuvudkonton (inloggningar) och autentiseringshändelser.
- Dedikerad administratörsanslutning för Azure AD-serverhuvudkonton (inloggningar) som är medlemmar i serverrollen `sysadmin`.
- Azure AD-serverhuvudkonton (inloggningar) stöds med hjälp av den [sqlcmd-verktyget](/sql/tools/sqlcmd-utility) och [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)-verktyget.
- Inloggningsutlösare stöds för inloggningshändelser som kommer från Azure AD-serverhuvudkonton (inloggningar).
- Service Broker och DBMail kan konfigureras med hjälp av Azure AD-serverhuvudkonton (inloggningar).


## <a name="next-steps"></a>Nästa steg

### <a name="enable-security-features"></a>Aktivera säkerhetsfunktioner

I artikeln om [säkerhetsfunktioner för hanterade instanser](sql-database-managed-instance.md#azure-sql-database-security-features) finns en omfattande lista med sätt att skydda databasen. Följande säkerhetsfunktioner diskuteras:

- [Granskning av hanterad instans](sql-database-managed-instance-auditing.md) 
- [Alltid krypterad](/sql/relational-databases/security/encryption/always-encrypted-database-engine)
- [Hotidentifiering](sql-database-managed-instance-threat-detection.md) 
- [Dynamisk datamaskning](/sql/relational-databases/security/dynamic-data-masking)
- [Säkerhet på radnivå](/sql/relational-databases/security/row-level-security) 
- [Transparent datakryptering (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)

### <a name="managed-instance-capabilities"></a>Hanterade instansfunktioner

En fullständig översikt över funktionerna för hanterade instanser finns i:

> [!div class="nextstepaction"]
> [Hanterade instansfunktioner](sql-database-managed-instance.md)
