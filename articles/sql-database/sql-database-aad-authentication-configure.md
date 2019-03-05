---
title: Konfigurera Azure Active Directory-autentisering - SQL | Microsoft Docs
description: Lär dig hur du ansluter till SQL Database Managed Instance och SQL Data Warehouse med hjälp av Azure Active Directory-autentisering - när du har konfigurerat Azure AD.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: data warehouse
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 03/04/2019
ms.openlocfilehash: e4ccb9be5d13ea72086fbaae2ffb2ec63ad55786
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2019
ms.locfileid: "57340328"
---
# <a name="configure-and-manage-azure-active-directory-authentication-with-sql"></a>Konfigurera och hantera Azure Active Directory-autentisering med SQL

Den här artikeln visar hur du skapar och fylla i Azure AD och Använd sedan Azure AD med Azure [SQL Database](sql-database-technical-overview.md), [Managed Instance](sql-database-managed-instance.md), och [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md). En översikt finns i [Azure Active Directory Authentication](sql-database-aad-authentication.md).

> [!NOTE]
> Den här artikeln gäller för Azure SQL-server och att både SQL Database och SQL Data Warehouse-databaser som skapas på Azure SQL-servern. För enkelhetens skull används SQL Database när det gäller både SQL Database och SQL Data Warehouse.
> [!IMPORTANT]  
> Ansluta till SQL Server som körs på en Azure virtuell dator stöds inte med ett Azure Active Directory-konto. Använd en domän Active Directory-konto i stället.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-and-populate-an-azure-ad"></a>Skapa och fylla i en Azure AD

Skapa en Azure AD och fyller den med användare och grupper. Azure AD kan vara den första Azure AD hanterade domänen. Azure AD kan också vara en lokal Active Directory Domain Services som är federerad med Azure AD.

Mer information finns i [Integrera dina lokala identiteter med Azure Active Directory](../active-directory/hybrid/whatis-hybrid-identity.md), [Add your own domain name to Azure AD](../active-directory/active-directory-domains-add-azure-portal.md) (Lägga till dina egna domännamn i Azure AD), [Microsoft Azure now supports federation with Windows Server Active Directory](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/) (Microsoft stöder nu federation med Windows Server Active Directory), [Administering your Azure AD directory](../active-directory/fundamentals/active-directory-administer.md) (Administrera Azure Active Directory), [Manage Azure AD using Windows PowerShell](/powershell/azure/overview?view=azureadps-2.0) (Hantera Azure AD med Windows PowerShell) och [Hybrid Identity Required Ports and Protocols](../active-directory/hybrid/reference-connect-ports.md) (Portar och protokoll som krävs för hybrididentiet).

## <a name="associate-or-add-an-azure-subscription-to-azure-active-directory"></a>Koppla eller lägga till en Azure-prenumeration i Azure Active Directory

1. Koppla din Azure-prenumeration till Azure Active Directory genom att göra katalogen som en betrodd katalog för Azure-prenumerationen som är värd för databasen. Mer information finns i [hur Azure-prenumerationer är associerade med Azure AD](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).
2. Använd katalogväxlaren i Azure-portalen för att växla till den prenumeration som är kopplad till domänen.

   **Ytterligare information:** Alla Azure-prenumerationer har en förtroenderelation med en Azure AD-instans. Det innebär att den litar på den katalogen för att autentisera användare, tjänster och enheter. Flera prenumerationer kan lita på samma katalog, men en prenumeration litar bara på en katalog. Den här förtroenderelationen mellan en prenumeration och en katalog skiljer sig från relationen mellan en prenumeration och alla andra resurser i Azure (webbplatser, databaser och så vidare), som är mer som underordnade resurser till en prenumeration. Om en prenumeration går ut stoppas även åtkomsten till de resurser som är associerade med prenumerationen. Men katalogen finns kvar i Azure och du kan associera en annan prenumeration med katalogen och fortsätta hantera kataloganvändarna. Mer information om resurser finns i [förstå resursåtkomst i Azure](../active-directory/active-directory-b2b-admin-add-users.md). Mer information om detta betrodd relation Se [hur du associerar eller lägga till en Azure-prenumeration i Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="create-an-azure-ad-administrator-for-azure-sql-server"></a>Skapa en Azure AD-administratör för Azure SQL-server

Varje Azure SQL-server (som är värd för en SQL Database eller SQL Data Warehouse) börjar med ett administratörskonto för enskild server som är administratör i hela Azure SQL-servern. Andra SQL Server-administratör måste skapas, som är en Azure AD-konto. Den här huvudnamn skapas som en oberoende databasanvändare i master-databasen. Som administratörer, server-administratörskonton som är medlemmar i den **db_owner** roll i varje användare databasen och ange varje användardatabas som den **dbo** användare. Läs mer om administratörskonton server [hantera databaser och inloggningar i Azure SQL Database](sql-database-manage-logins.md).

När du använder Azure Active Directory med geo-replikering, måste Azure Active Directory-administratör konfigureras för både primärt och sekundära servrar. Om en server inte har en Azure Active Directory-administratör kan sedan får Azure Active Directory-inloggningar och användare ett ”kan inte ansluta” till server-fel.

> [!NOTE]
> Användare som inte är baserade på en Azure AD-konto (inklusive Azure SQL server-administratörskontot) kan inte skapa Azure AD-baserade användare, eftersom de inte har behörighet att validera föreslagna användare med Azure AD.

## <a name="provision-an-azure-active-directory-administrator-for-your-managed-instance"></a>Etablera en Azure Active Directory-administratör för din hanterade instans

> [!IMPORTANT]
> Följ bara dessa steg om du etablerar en hanterad instans. Den här åtgärden kan endast utföras av Global/företagsadministratör i Azure AD. Följande steg beskriver hur du beviljar behörighet för användare med olika behörigheter i katalogen.

Din hanterade instans måste du ha behörighet att läsa Azure AD för att utföra uppgifter, till exempel autentisering av användare via säkerhetsgrupp eller skapa nya användare har. För detta ska fungera måste du bevilja behörigheter till hanterad instans att läsa Azure AD. Det finns två sätt att göra det: från portalen och PowerShell. Följande steg båda metoderna.

1. I Azure-portalen i det övre högra hörnet väljer du din anslutning om du vill se en lista över möjliga Active kataloger.
2. Välj rätt Active Directory som standard-Azure AD.

   Det här steget länkar den prenumeration som är associerad med Active Directory med Managed Instance att se till att samma prenumeration används för både Azure AD och den hanterade instansen.
3. Gå till Managed Instance och välja en som du vill använda för Azure AD-integrering.

   ![aad](./media/sql-database-aad-authentication/aad.png)

4. Välj informationstext högst upp på sidan Active Directory-administratören och bevilja behörighet till den aktuella användaren. Om du är inloggad som Global/företagsadministratör i Azure AD, kan du göra det från Azure portal eller med hjälp av PowerShell med skriptet nedan.

    ![bevilja behörigheter-portal](./media/sql-database-aad-authentication/grant-permissions.png)

    ```PowerShell
    # Gives Azure Active Directory read permission to a Service Principal representing the Managed Instance.
    # Can be executed only by a "Company Administrator" or "Global Administrator" type of user.

    $aadTenant = "<YourTenantId>" # Enter your tenant ID
    $managedInstanceName = "MyManagedInstance"

    # Get Azure AD role "Directory Users" and create if it doesn't exist
    $roleName = "Directory Readers"
    $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq $roleName}
    if ($role -eq $null) {
        # Instantiate an instance of the role template
        $roleTemplate = Get-AzureADDirectoryRoleTemplate | Where-Object {$_.displayName -eq $roleName}
        Enable-AzureADDirectoryRole -RoleTemplateId $roleTemplate.ObjectId
        $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq $roleName}
    }

    # Get service principal for managed instance
    $roleMember = Get-AzureADServicePrincipal -SearchString $managedInstanceName
    $roleMember.Count
    if ($roleMember -eq $null)
    {
        Write-Output "Error: No Service Principals with name '$    ($managedInstanceName)', make sure that managedInstanceName parameter was     entered correctly."
        exit
    }
    if (-not ($roleMember.Count -eq 1))
    {
        Write-Output "Error: More than one service principal with name pattern '$    ($managedInstanceName)'"
        Write-Output "Dumping selected service principals...."
        $roleMember
        exit
    }

    # Check if service principal is already member of readers role
    $allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
    $selDirReader = $allDirReaders | where{$_.ObjectId -match     $roleMember.ObjectId}

    if ($selDirReader -eq $null)
    {
        # Add principal to readers role
        Write-Output "Adding service principal '$($managedInstanceName)' to     'Directory Readers' role'..."
        Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId     $roleMember.ObjectId
        Write-Output "'$($managedInstanceName)' service principal added to     'Directory Readers' role'..."

        #Write-Output "Dumping service principal '$($managedInstanceName)':"
        #$allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
        #$allDirReaders | where{$_.ObjectId -match $roleMember.ObjectId}
    }
    else
    {
        Write-Output "Service principal '$($managedInstanceName)' is already     member of 'Directory Readers' role'."
    }
    ```

5. När åtgärden har slutförts visas följande meddelande i det övre högra hörnet:

    ![lyckades](./media/sql-database-aad-authentication/success.png)

6. Du kan nu välja din Azure AD-administratör för din hanterade instans. För att på sidan Active Directory-administratör väljer **konfigurera administratör** kommando.

    ![set-admin](./media/sql-database-aad-authentication/set-admin.png)

7. AAD-administratören sidan Sök efter en användare väljer du den användare eller grupp som ska vara en administratör och välj sedan **Välj**.

   Sidan Active Directory visas alla medlemmar och grupper för din Active Directory. Användare eller grupper som är nedtonade kan inte väljas eftersom de inte stöds som Azure AD-administratörer. Se listan över stöds administratörer i [Azure AD-funktioner och begränsningar](sql-database-aad-authentication.md#azure-ad-features-and-limitations). Rollbaserad åtkomstkontroll (RBAC) gäller enbart för Azure-portalen och är inte sprids till SQL Server.

    ![add-admin](./media/sql-database-aad-authentication/add-admin.png)

8. Överst på sidan Active Directory-administratör väljer **spara**.

    ![spara](./media/sql-database-aad-authentication/save.png)

    Processen med att ändra administratör kan ta några minuter. Ny administratör visas sedan i rutan Active Directory-administratör.

När du har etablerat en Azure AD-administratör för din hanterade instans, kan du börja skapa Azure AD server-huvudkonton (inloggningar) (**förhandsversion**) med den <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN</a> syntax. Mer information finns i [översikten över Managed-instans](sql-database-managed-instance.md#azure-active-directory-integration).

> [!TIP]
> Om du vill senare tar bort en administratör kan högst upp på sidan Active Directory-administratör väljer **ta bort administratör**, och välj sedan **spara**.

## <a name="provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server"></a>Etablera en Azure Active Directory-administratör för din Azure SQL Database-server

> [!IMPORTANT]
> Följ dessa steg bara om du etablerar en Azure SQL Database-server eller datalagret.

Följande två procedurer visar hur du etablerar en Azure Active Directory-administratör för Azure SQL-servern i Azure-portalen och med hjälp av PowerShell.

### <a name="azure-portal"></a>Azure Portal

1. På [Azure-portalen](https://portal.azure.com/) väljer du din anslutning i det övre högra hörnet för att visa en lista över möjliga Active Directories. Välj rätt Active Directory som standard-Azure AD. Det här steget länkar prenumerationsassocierad Active Directory till Azure SQL-servern, vilket gör att samma prenumeration används för både Azure AD och SQL Server. (Azure SQL-servern kan vara värd för Azure SQL Database eller Azure SQL Data Warehouse.) ![väljer ad][8]

2. I den vänstra banderollen väljer **alla tjänster**, och i vilken filtertyp i **SQLServer**. Välj **Sql-servrar**.

    ![sqlservers.png](media/sql-database-aad-authentication/sqlservers.png)

    >[!NOTE]
    > På den här sidan innan du väljer **SQL-servrar**, kan du välja den **star** bredvid namnet att *favorit* kategori och Lägg till **SQL-servrar**till det vänstra navigeringsfältet.

3. På **SQL Server** väljer **Active Directory-administratör**.
4. I den **Active Directory-administratör** väljer **konfigurera administratör**.  ![Välj active directory](./media/sql-database-aad-authentication/select-active-directory.png)  

5. på sidan **Lägg till administratör** söker du efter en användare, väljer den användare eller den grupp som ska vara administratör och väljer sedan **Välj**. (Active Directory-administratörssidan visar alla medlemmar och grupper för din Active Directory. Användare eller grupper som är nedtonade kan inte väljas eftersom de inte stöds som Azure AD-administratörer. (Se listan över administratörer som stöds i avsnittet om **Azure AD-funktioner och begränsningar** i artikeln om att [använda Azure Active Directory-autentisering för autentisering med SQL Database eller SQL Data Warehouse](sql-database-aad-authentication.md).) Rollbaserad åtkomstkontroll (RBAC) gäller enbart för portalen och sprids inte till SQL Server.
    ![välja admin](./media/sql-database-aad-authentication/select-admin.png)  

6. Längst upp på sidan **Active Directory-administratör** väljer du **SPARA**.
    ![spara admin](./media/sql-database-aad-authentication/save-admin.png)

Processen med att ändra administratör kan ta några minuter. Sedan visas den nya administratören i rutan **Active Directory-administratör**.

   > [!NOTE]
   > När Azure AD-administratören konfigureras får namnet för den nya administratören (användare eller grupp) inte redan finnas i den virtuella huvuddatabasen som SQL Server-autentiseringsanvändare. Om det finns misslyckas Azure AD-administratörskonfigurationen. Den återställer skapande och anger att den administratören (namnet) redan finns. Eftersom en sådan SQL Server-autentiseringsanvändare inte är en del av Azure AD misslyckas alla försök att ansluta till servern med hjälp av Azure AD-autentisering.

Senare ta bort en administratör kan högst upp på den **Active Directory-administratör** väljer **ta bort administratör**, och välj sedan **spara**.

### <a name="powershell"></a>PowerShell

Om du vill köra PowerShell-cmdlets som du behöver ha Azure PowerShell installerad och igång. Mer information finns i [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview). Kör följande Azure PowerShell-kommandon för att etablera en Azure AD-administratör:

- Connect-AzAccount
- Select-AzSubscription

Cmdlet: ar för att etablera och hantera Azure AD-administratör:

| Cmdlet-namn | Beskrivning |
| --- | --- |
| [Set-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlserveractivedirectoryadministrator) |Etablerar en Azure Active Directory-administratör för Azure SQL-server eller Azure SQL Data Warehouse. (Måste vara från den aktuella prenumerationen.) |
| [Remove-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlserveractivedirectoryadministrator) |Tar bort en Azure Active Directory-administratör för Azure SQL-server eller Azure SQL Data Warehouse. |
| [Get-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator) |Returnerar information om en Azure Active Directory-administratör som har konfigurerats för Azure SQL-server eller Azure SQL Data Warehouse. |

Använd PowerShell-kommandot get-help för att se information om var och en av följande kommandon, till exempel ``get-help Set-AzSqlServerActiveDirectoryAdministrator``.

Följande skript etablerar en administratör Azure AD-grupp med namnet **DBA_Group** (objekt-ID `40b79501-b343-44ed-9ce7-da4c8cc7353f`) för den **demo_server** server i en resursgrupp med namnet **grupp-23**:

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23"
-ServerName "demo_server" -DisplayName "DBA_Group"
```

Den **DisplayName** indataparameter accepterar antingen Azure AD-visningsnamn eller UPN-namnet. Till exempel ``DisplayName="John Smith"`` och ``DisplayName="johns@contoso.com"``. Visningsnamn stöds för Azure AD-grupper bara Azure AD.

> [!NOTE]
> Azure PowerShell-kommando ```Set-AzSqlServerActiveDirectoryAdministrator``` förhindrar inte att du etablerar Azure AD-administratörer för användare som inte stöds. En användare som inte stöds kan etableras, men kan inte ansluta till en databas.

I följande exempel används det valfria **ObjectID**:

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23"
-ServerName "demo_server" -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
```

> [!NOTE]
> Azure AD **ObjectID** är obligatorisk när den **DisplayName** är inte unikt. Att hämta den **ObjectID** och **DisplayName** värden, använder Active Directory-avsnittet i klassiska Azure-portalen och visa egenskaperna för en användare eller grupp.

I följande exempel returneras information om aktuellt Azure AD-administratör för Azure SQL-server:

```powershell
Get-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" | Format-List
```

I följande exempel tar bort en Azure AD-administratör:

```powershell
Remove-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server"
```

Du kan också etablera en Azure Active Directory-administratör med hjälp av REST-API: er. Mer information finns i [Service Management REST API-referens och åtgärder för Azure SQL Database-åtgärder för Azure SQL Database](https://docs.microsoft.com/rest/api/sql/)

### <a name="cli"></a>CLI  

Du kan också etablera en Azure AD-administratör genom att anropa följande CLI-kommandon:
| Kommando | Beskrivning |
| --- | --- |
|[Skapa AZ sql server ad-administratör](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-create) |Etablerar en Azure Active Directory-administratör för Azure SQL-server eller Azure SQL Data Warehouse. (Måste vara från den aktuella prenumerationen.) |
|[ta bort AZ sql server ad-administratör](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-delete) |Tar bort en Azure Active Directory-administratör för Azure SQL-server eller Azure SQL Data Warehouse. |
|[AZ sql server ad-administratörslistan](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-list) |Returnerar information om en Azure Active Directory-administratör som har konfigurerats för Azure SQL-server eller Azure SQL Data Warehouse. |
|[Uppdatera för AZ sql server ad-administratör](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-update) |Uppdaterar Active Directory-administratör för en Azure SQL-server eller Azure SQL Data Warehouse. |

Mer information om CLI-kommandon finns i [SQL - az sql](https://docs.microsoft.com/cli/azure/sql/server).  

## <a name="configure-your-client-computers"></a>Konfigurera klientdatorer

På alla klientdatorer som ditt program eller en användare ansluter till Azure SQL Database eller Azure SQL Data Warehouse med hjälp av Azure AD-identiteter, måste du installera följande programvara:

- .NET framework 4.6 eller senare från [ https://msdn.microsoft.com/library/5a4x27ek.aspx ](https://msdn.microsoft.com/library/5a4x27ek.aspx).
- Azure Active Directory Authentication Library för SQLServer (**ADALSQL. DLL-filen**) är tillgänglig på flera språk (x86 och amd64) från download center på [Microsoft Active Directory Authentication Library för Microsoft SQL Server](https://www.microsoft.com/download/details.aspx?id=48742).

Du kan uppfylla dessa krav genom att:

- Installera antingen [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) eller [SQL Server Data Tools för Visual Studio 2015](https://msdn.microsoft.com/library/mt204009.aspx) uppfyller .NET Framework 4.6.
- SSMS installerar x86 version av **ADALSQL. DLL-filen**.
- SSDT installerar amd64-versionen av **ADALSQL. DLL-filen**.
- Senaste Visual Studio från [hämtningsfiler för Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs) uppfyller .NET Framework 4.6, men inte installera den nödvändiga amd64-versionen av **ADALSQL. DLL-filen**.

## <a name="create-contained-database-users-in-your-database-mapped-to-azure-ad-identities"></a>Skapa oberoende databasanvändare i din databas som mappats till Azure AD-identiteter

>[!IMPORTANT]
>Hanterad instans har nu stöd för Azure AD-server-huvudkonton (inloggningar) (**förhandsversion**), vilket gör att du kan skapa inloggningar från Azure AD-användare, grupper eller program. Azure AD-server-huvudkonton (inloggningar) ger möjlighet att autentisera till din hanterade instans utan databasanvändare som ska skapas som en oberoende databasanvändare. Mer information finns i [översikten över Managed-instans](sql-database-managed-instance.md#azure-active-directory-integration). Syntax för att skapa Azure AD server-huvudkonton (inloggningar), se <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN</a>.

Azure Active Directory-autentisering kräver att databasanvändare skapas som användare av oberoende databas. En användare av oberoende databas baserad på en Azure AD-identitet är en databasanvändare som inte har någon inloggning i huvuddatabasen, och som mappas till en identitet i den Azure AD-katalog som är associerad med databasen. Azure AD-identiteten kan vara antingen ett enskilt användarkonto eller en grupp. Mer information om användare av oberoende databas finns i avsnittet om [användare av oberoende databas – så gör du din databas portabel](https://msdn.microsoft.com/library/ff929188.aspx).

> [!NOTE]
> Databasanvändare (med undantag för administratörer) kan inte skapas med hjälp av Azure-portalen. RBAC-roller sprids inte till SQL Server, SQL Database eller SQL Data Warehouse. Azure RBAC-roller används för att hantera Azure-resurser och gäller inte för databasbehörigheter. Till exempel beviljar rollen **SQL Server-deltagare** inte åtkomst för att ansluta till SQL Database eller SQL Data Warehouse. Åtkomstbehörigheten måste beviljas direkt i databasen med hjälp av Transact-SQL-instruktioner.
> [!WARNING]
> Specialtecken som kolon `:` eller et-tecken `&` stöds inte när de ingår i användarnamn i instruktionerna T-SQL CREATE LOGIN och CREATE USER.

Att skapa en Azure AD-baserad innehöll databasanvändare (andra än serveradministratören som äger databasen), ansluta till databasen med en Azure AD-identitet som en användare med minst den **ALTER ANY USER** behörighet. Sedan använder du följande Transact-SQL-syntax:

```sql
CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
```

*Azure_AD_principal_name* kan vara användarens huvudnamn för Azure AD-användare eller visningsnamn för en Azure AD-grupp.

**Exempel:** Skapa en innesluten databas användaren som representerar en Azure AD federerade eller hanterade domänanvändare:

```sql
CREATE USER [bob@contoso.com] FROM EXTERNAL PROVIDER;
CREATE USER [alice@fabrikam.onmicrosoft.com] FROM EXTERNAL PROVIDER;
```

Ange visningsnamnet för en säkerhetsgrupp om du vill skapa en innesluten databasanvändare som representerar en Azure AD eller med federerade domängrupp:

```sql
CREATE USER [ICU Nurses] FROM EXTERNAL PROVIDER;
```

Skapa en innesluten databasanvändare som representerar ett program som ansluter med hjälp av en Azure AD-token:

```sql
CREATE USER [appName] FROM EXTERNAL PROVIDER;
```

> [!TIP]
> Du kan inte skapa en användare direkt från en Azure Active Directory än Azure Active Directory som är associerad med din Azure-prenumeration. Medlemmar i andra Active kataloger som är importerade användare i den associerade Active Directory (kallas externa användare) kan dock läggas till en Active Directory-grupp i Active Directory-klienten. Genom att skapa en oberoende databasanvändare för gruppen AD kan användare från den externa Active Directory få åtkomst till SQL-databas.

Mer information om hur du skapar innehåller databasen användare baserat på Azure Active Directory-identiteter, se [skapa användare (Transact-SQL)](https://msdn.microsoft.com/library/ms173463.aspx).

> [!NOTE]
> Tar bort Azure Active Directory-administratör för Azure SQL-server förhindrar alla Azure AD authentication-användare ansluter till servern. Vid behov går inte att använda Azure AD-användare kan tas bort manuellt av en administratör för SQL-databas.
> [!NOTE]
> Om du får en **anslutning tidsgränsen gick ut**, du kan behöva ange den `TransparentNetworkIPResolution` -parametern för anslutningssträngen till false. Mer information finns i [timeout anslutningsproblem med .NET Framework 4.6.1 - TransparentNetworkIPResolution](https://blogs.msdn.microsoft.com/dataaccesstechnologies/20../../connection-timeout-issue-with-net-framework-4-6-1-transparentnetworkipresolution/).

När du skapar en databasanvändare som användaren tar emot den **CONNECT** behörighet och kan ansluta till databasen som en medlem i den **offentliga** roll. Inledningsvis endast tillgängligt för användaren behörigheter är alla behörigheter som beviljas den **offentliga** roll eller alla behörigheter som tilldelats några grupper i Azure AD att de är medlem i. När du etablerar en Azure AD-baserad innehöll databasanvändare, kan du bevilja användaren ytterligare behörighet på samma sätt som du vill ge behörighet till någon annan typ av användare. Normalt bevilja behörigheter till databasroller och lägga till användare till roller. Mer information finns i [Database Engine behörighet grunderna](https://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx). Läs mer om SQL Database specialroller [hantera databaser och inloggningar i Azure SQL Database](sql-database-manage-logins.md).
En federerad domän-användarkonto som har importerats till en hanterad domän som en extern användare måste använda den hanterade domän identiteten.

> [!NOTE]
> Azure AD-användare markeras i databasmetadata med typen E (EXTERNAL_USER) och för grupper med typen X (EXTERNAL_GROUPS). Mer information finns i [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx).
>

## <a name="connect-to-the-user-database-or-data-warehouse-by-using-ssms-or-ssdt"></a>Ansluta till databasen eller datalagret för användare med hjälp av SSMS eller SSDT  

Kontrollera Azure AD-administratör är rätt konfigurerad genom att ansluta till den **master** databasen med hjälp av Azure AD-administratörskonto.
Ansluta till databasen med en Azure AD-identitet som har åtkomst till databasen för att etablera en Azure AD-baserad innehöll databasanvändare (andra än serveradministratören som äger databasen).

> [!IMPORTANT]
> Support för Azure Active Directory-autentisering är tillgänglig med [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) och [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) i Visual Studio 2015. Augusti 2016-versionen av SSMS innehåller också stöd för Active Directory Universal-autentisering, vilket gör att administratörer kan kräva Multifaktorautentisering med hjälp av ett telefonsamtal, textmeddelande, smartkort och PIN-kod eller mobilapp.

## <a name="using-an-azure-ad-identity-to-connect-using-ssms-or-ssdt"></a>Använda en Azure AD-identitet för att ansluta med hjälp av SSMS eller SSDT

Följande procedurer visar hur du ansluter till en SQL-databas med en Azure AD-identitet med hjälp av SQL Server Management Studio eller SQL Server-databas Tools.

### <a name="active-directory-integrated-authentication"></a>Active Directory-integrerad autentisering

Använd den här metoden om du är inloggad Windows med hjälp av Azure Active Directory-autentiseringsuppgifter från en federerad domän.

1. Starta Management Studio eller Dataverktyg och i den **Anslut till Server** (eller **Anslut till databasmotor**) i dialogrutan den **autentisering** väljer  **Active Directory – integrerad**. Inget lösenord krävs eller kan anges eftersom din befintliga autentiseringsuppgifter kommer att visas för anslutningen.

    ![Välj AD-integrerad autentisering][11]
2. Välj den **alternativ** knapp, och på den **anslutningsegenskaper** sidan den **Anslut till databas** skriver du namnet på den du vill ansluta till databasen. (Den **AD-domän eller klient-ID**”alternativet stöds bara för **Universal med MFA-anslutning** alternativ, annars den är nedtonat.)  

    ![Välj namnet på databasen][13]

## <a name="active-directory-password-authentication"></a>Autentisering av Active Directory-lösenord

Använd den här metoden när du ansluter med en Azure AD-huvudnamn med hjälp av Azure AD hanterade domän. Du kan också använda den för federerad konton utan åtkomst till domänen, till exempel när du arbetar via fjärranslutning.

Använd den här metoden för att autentisera till SQL DB/DW med Azure AD för intern eller med federerade Azure AD-användare. En intern användare är en explicit skapats i Azure AD och som autentiseras med hjälp av användarnamn och lösenord, medan en federerad användare är en Windows-användare vars domäner är federerad med Azure AD. Den andra metoden som (med användare och lösenord) kan användas när en användare vill använda sina windows-autentiseringsuppgifter, men en lokal dator inte är ansluten med domänen (till exempel använder en fjärråtkomst). I det här fallet en Windows-användare kan ange sitt konto och lösenord och kan autentisera till SQL DB/DW med federerad autentiseringsuppgifter.

1. Starta Management Studio eller Dataverktyg och i den **Anslut till Server** (eller **Anslut till databasmotor**) i dialogrutan den **autentisering** väljer  **Active Directory - lösenord**.
2. I den **användarnamn** skriver du ditt Azure Active Directory-användarnamn i formatet **username@domain.com**. Användarnamn måste vara ett konto från Azure Active Directory eller ett konto från en domän federera med Azure Active Directory.
3. I den **lösenord** skriver du ditt lösenord för Azure Active Directory-konto eller federerad domänkonto.

    ![Välj AD-lösenordsautentisering][12]
4. Välj den **alternativ** knapp, och på den **anslutningsegenskaper** sidan den **Anslut till databas** skriver du namnet på den du vill ansluta till databasen. (Se bilden i föregående alternativ.)

## <a name="using-an-azure-ad-identity-to-connect-from-a-client-application"></a>Använda en Azure AD-identitet för att ansluta från ett klientprogram

Följande procedurer visar hur du ansluter till en SQL-databas med en Azure AD-identitet från ett klientprogram.

### <a name="active-directory-integrated-authentication"></a>Active Directory-integrerad autentisering

Om du vill använda integrerad Windows-autentisering, måste domänens Active Directory ska federeras med Azure Active Directory. Klientprogrammet (eller en tjänst) som ansluter till databasen måste köras på en domänansluten dator under en användares domänautentiseringsuppgifter.

Om du vill ansluta till en databas med integrerad autentisering och Azure AD-identitet, måste autentisering-nyckelord i anslutningssträngen för databasen anges till Active Directory-integrerad. Följande C# kodexemplet använder ADO .NET.

```C#
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated; Initial Catalog=testdb;";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

Nyckelordet för anslutningssträngen ``Integrated Security=True`` stöds inte för att ansluta till Azure SQL Database. När du gör en ODBC-anslutning, måste du ta bort blanksteg och ställa in autentisering till 'ActiveDirectoryIntegrated'.

### <a name="active-directory-password-authentication"></a>Autentisering av Active Directory-lösenord

Nyckelordet autentisering måste anges till Active Directory-lösenord för att ansluta till en databas med integrerad autentisering och Azure AD-identitet. Anslutningssträngen måste innehålla användar-ID/UID och lösenord/PWD nyckelord och värden. Följande C# kodexemplet använder ADO .NET.

```C#
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Password; Initial Catalog=testdb;  UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

Läs mer om Azure AD-autentiseringsmetoder med demo kodexempel på [GitHub demonstration av Azure AD Authentication](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth).

## <a name="azure-ad-token"></a>Azure AD-token

Den här autentiseringsmetoden tillåter mellannivå tjänster att ansluta till Azure SQL Database eller Azure SQL Data Warehouse genom att hämta en token från Azure Active Directory (AAD). Det möjliggör avancerade scenarier inklusive certifikatbaserad autentisering. Du måste utföra fyra grundläggande steg för att använda autentisering med Azure AD-token:

1. Registrera ditt program med Azure Active Directory och hämta klient-id för din kod.
2. Skapa en databasanvändare som representerar programmet. (Slutfört tidigare i steg 6).
3. Skapa ett certifikat på klienten dator körs programmet.
4. Lägga till certifikatet som en nyckel för ditt program.

Exempel-anslutningssträng:

```c#
string ConnectionString =@"Data Source=n9lxnyuzhv.database.windows.net; Initial Catalog=testdb;"
SqlConnection conn = new SqlConnection(ConnectionString);
conn.AccessToken = "Your JWT token"
conn.Open();
```

Mer information finns i [SQL Server Security-bloggen](https://blogs.msdn.microsoft.com/sqlsecurity/20../../token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth/). Information om att lägga till ett certifikat finns i [Kom igång med certifikatbaserad autentisering i Azure Active Directory](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md).

### <a name="sqlcmd"></a>sqlcmd

Följande instruktioner, Anslut med hjälp av version 13,1 av sqlcmd som är tillgänglig från den [Download Center](https://go.microsoft.com/fwlink/?LinkID=825643).

```cmd
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net  -G  
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -U bob@contoso.com -P MyAADPassword -G -l 30
```

## <a name="next-steps"></a>Nästa steg

- En översikt över åtkomst och kontroll i SQL Database finns i [Åtkomst och kontroll för SQL Database](sql-database-control-access.md).
- En översikt över inloggningar, användare och databasroller i SQL Database finns i [Inloggningar, användare och databasroller](sql-database-manage-logins.md).
- Mer information om huvudkonton finns i [Huvudkonton](https://msdn.microsoft.com/library/ms181127.aspx).
- Mer information om databasroller finns [Databasroller](https://msdn.microsoft.com/library/ms189121.aspx).
- Mer information om brandväggsregler i SQL Database finns [SQL Database-brandväggsregler](sql-database-firewall-configure.md).

<!--Image references-->

[1]: ./media/sql-database-aad-authentication/1aad-auth-diagram.png
[2]: ./media/sql-database-aad-authentication/2subscription-relationship.png
[3]: ./media/sql-database-aad-authentication/3admin-structure.png
[4]: ./media/sql-database-aad-authentication/4select-subscription.png
[5]: ./media/sql-database-aad-authentication/5ad-settings-portal.png
[6]: ./media/sql-database-aad-authentication/6edit-directory-select.png
[7]: ./media/sql-database-aad-authentication/7edit-directory-confirm.png
[8]: ./media/sql-database-aad-authentication/8choose-ad.png
[10]: ./media/sql-database-aad-authentication/10choose-admin.png
[11]: ./media/sql-database-aad-authentication/active-directory-integrated.png
[12]: ./media/sql-database-aad-authentication/12connect-using-pw-auth2.png
[13]: ./media/sql-database-aad-authentication/13connect-to-db2.png
