---
title: Konfigurera Azure Active Directory-autentisering
description: Lär dig hur du ansluter till SQL Database, hanterad instans och SQL Data Warehouse med hjälp av Azure Active Directory Authentication – efter att ha konfigurerat Azure AD.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: data warehouse
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, carlrab
ms.date: 01/07/2020
ms.openlocfilehash: 881c7076d5131746c730757a07da6fb938429c38
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80125039"
---
# <a name="configure-and-manage-azure-active-directory-authentication-with-sql"></a>Konfigurera och hantera Azure Active Directory-autentisering med SQL

Den här artikeln visar hur du skapar och fyller i Azure AD och sedan använder Azure AD med Azure [SQL Database](sql-database-technical-overview.md), [hanterad instans](sql-database-managed-instance.md)och SQL Data [Warehouse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md). En översikt finns i [Azure Active Directory Authentication](sql-database-aad-authentication.md).

> [!NOTE]
> Den här artikeln gäller Azure SQL-server och både SQL Database- och SQL Data Warehouse-databaser som skapas på Azure SQL-servern. För enkelhetens skull används SQL Database när det gäller både SQL Database och SQL Data Warehouse.

> [!IMPORTANT]  
> Det går inte att ansluta till SQL Server som körs på en Virtuell Azure-dator med ett Azure Active Directory-konto. Använd ett Active Directory-konto i domänen i stället.

## <a name="create-and-populate-an-azure-ad"></a>Skapa och fylla i en Azure AD

Skapa en Azure AD och fyll den med användare och grupper. Azure AD kan vara den första Azure AD-hanterade domänen. Azure AD kan också vara en lokal Active Directory Domain Services som är federerad med Azure AD.

Mer information finns i [Integrera dina lokala identiteter med Azure Active Directory](../active-directory/hybrid/whatis-hybrid-identity.md), [Add your own domain name to Azure AD](../active-directory/active-directory-domains-add-azure-portal.md) (Lägga till dina egna domännamn i Azure AD), [Microsoft Azure now supports federation with Windows Server Active Directory](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/) (Microsoft stöder nu federation med Windows Server Active Directory), [Administering your Azure AD directory](../active-directory/fundamentals/active-directory-administer.md) (Administrera Azure Active Directory), [Manage Azure AD using Windows PowerShell](/powershell/azure/overview) (Hantera Azure AD med Windows PowerShell) och [Hybrid Identity Required Ports and Protocols](../active-directory/hybrid/reference-connect-ports.md) (Portar och protokoll som krävs för hybrididentiet).

## <a name="associate-or-add-an-azure-subscription-to-azure-active-directory"></a>Associera eller lägg till en Azure-prenumeration till Azure Active Directory

1. Associera din Azure-prenumeration till Azure Active Directory genom att göra katalogen till en betrodd katalog för Azure-prenumerationen som är värd för databasen. Mer information finns i [Hur Azure-prenumerationer är associerade med Azure AD](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

2. Använd katalogväxeln i Azure-portalen för att växla till prenumerationen som är associerad med domänen.

   > [!IMPORTANT]
   > Alla Azure-prenumerationer har en förtroenderelation med en Azure AD-instans. Det innebär att den litar på den katalogen för att autentisera användare, tjänster och enheter. Flera prenumerationer kan lita på samma katalog, men en prenumeration litar bara på en katalog. Den här förtroenderelationen mellan en prenumeration och en katalog skiljer sig från relationen mellan en prenumeration och alla andra resurser i Azure (webbplatser, databaser och så vidare), som är mer som underordnade resurser till en prenumeration. Om en prenumeration går ut stoppas även åtkomsten till de resurser som är associerade med prenumerationen. Men katalogen finns kvar i Azure och du kan associera en annan prenumeration med katalogen och fortsätta hantera kataloganvändarna. Mer information om resurser finns [i Förstå resursåtkomst i Azure](../active-directory/active-directory-b2b-admin-add-users.md). Mer information om den här betrodda relationen finns [i Så här associerar eller lägger du till en Azure-prenumeration i Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="create-an-azure-ad-administrator-for-azure-sql-server"></a>Skapa en Azure AD-administratör för Azure SQL-server

Varje Azure SQL-server (som är värd för en SQL Database eller SQL Data Warehouse) börjar med ett enda serveradministratörskonto som är administratör för hela Azure SQL-servern. En andra SQL Server-administratör måste skapas, det vill säga ett Azure AD-konto. Det här huvudnamnet skapas som en innesluten databasanvändare i huvuddatabasen. Som administratörer är serveradministratörskontona medlemmar i **den db_owner** rollen i varje användardatabas och anger varje användardatabas som **dbo-användare.** Mer information om serveradministratörskontona finns [i Hantera databaser och inloggningar i Azure SQL Database](sql-database-manage-logins.md).

När du använder Azure Active Directory med geo-replikering måste Azure Active Directory-administratören konfigureras för både de primära och de sekundära servrarna. Om en server inte har en Azure Active Directory-administratör visas Azure Active Directory-inloggningar och användare får ett "Kan inte ansluta" till serverfel.

> [!NOTE]
> Användare som inte baseras på ett Azure AD-konto (inklusive Azure SQL-serveradministratörskontot) kan inte skapa Azure AD-baserade användare, eftersom de inte har behörighet att validera föreslagna databasanvändare med Azure AD.

## <a name="provision-an-azure-active-directory-administrator-for-your-managed-instance"></a>Etablera en Azure Active Directory-administratör för din hanterade instans

> [!IMPORTANT]
> Följ bara dessa steg om du etablerar en hanterad instans. Den här åtgärden kan endast utföras av Global/Company-administratör eller en privilegierad rolladministratör i Azure AD. I följande steg beskrivs processen för att bevilja behörigheter för användare med olika behörigheter i katalogen.

> [!NOTE]
> För Azure AD-administratörer för MI som skapats före GA, men fortsätta att använda post GA, finns det ingen funktionell ändring av det befintliga beteendet. Mer information finns i [avsnittet Ny Azure AD-administratör för MI](#new-azure-ad-admin-functionality-for-mi) för mer information.

Din hanterade instans behöver behörigheter för att läsa Azure AD för att kunna utföra uppgifter som autentisering av användare via medlemskap i säkerhetsgruppen eller skapande av nya användare. För att detta ska fungera måste du bevilja behörigheter till hanterad instans för att läsa Azure AD. Det finns två sätt att göra det: från Portal och PowerShell. Följande steg båda metoderna.

1. På Azure-portalen väljer du din anslutning i det övre högra hörnet för att visa en lista över möjliga Active Directories.

2. Välj rätt Active Directory som standard-Azure AD.

   Det här steget länkar prenumerationen som är associerad med Active Directory med hanterad instans och ser till att samma prenumeration används för både Azure AD och den hanterade instansen.

3. Navigera till Hanterad instans och välj en som du vill använda för Azure AD-integrering.

   ![Aad](./media/sql-database-aad-authentication/aad.png)

4. Välj banderollen ovanpå administrationssidan i Active Directory och ge den aktuella användaren behörighet. Om du är inloggad som global/företagsadministratör i Azure AD kan du göra det från Azure-portalen eller använda PowerShell med skriptet nedan.

    ![bevilja behörighetsportal](./media/sql-database-aad-authentication/grant-permissions.png)

    ```powershell
    # Gives Azure Active Directory read permission to a Service Principal representing the managed instance.
    # Can be executed only by a "Company Administrator", "Global Administrator", or "Privileged Role Administrator" type of user.

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
    if ($roleMember -eq $null) {
        Write-Output "Error: No Service Principals with name '$    ($managedInstanceName)', make sure that managedInstanceName parameter was     entered correctly."
        exit
    }
    if (-not ($roleMember.Count -eq 1)) {
        Write-Output "Error: More than one service principal with name pattern '$    ($managedInstanceName)'"
        Write-Output "Dumping selected service principals...."
        $roleMember
        exit
    }

    # Check if service principal is already member of readers role
    $allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
    $selDirReader = $allDirReaders | where{$_.ObjectId -match     $roleMember.ObjectId}

    if ($selDirReader -eq $null) {
        # Add principal to readers role
        Write-Output "Adding service principal '$($managedInstanceName)' to     'Directory Readers' role'..."
        Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId     $roleMember.ObjectId
        Write-Output "'$($managedInstanceName)' service principal added to     'Directory Readers' role'..."

        #Write-Output "Dumping service principal '$($managedInstanceName)':"
        #$allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
        #$allDirReaders | where{$_.ObjectId -match $roleMember.ObjectId}
    }
    else {
        Write-Output "Service principal '$($managedInstanceName)' is already     member of 'Directory Readers' role'."
    }
    ```

5. När åtgärden har slutförts visas följande meddelande i det övre högra hörnet:

    ![lyckades](./media/sql-database-aad-authentication/success.png)

6. Nu kan du välja din Azure AD-administratör för din hanterade instans. På administrationssidan i Active Directory **Set admin** väljer du Ange administratörskommando.

    ![set-admin](./media/sql-database-aad-authentication/set-admin.png)

7. På adminsidan för AAD söker du efter en användare, väljer den användare eller grupp som ska vara administratör och väljer sedan **Välj**.

   På sidan Active Directory-administratör visas alla medlemmar och grupper i Active Directory. Användare eller grupper som är nedtonade kan inte väljas eftersom de inte stöds som Azure AD-administratörer. Se listan över administratörer som stöds i [Azure AD-funktioner och begränsningar](sql-database-aad-authentication.md#azure-ad-features-and-limitations). Rollbaserad åtkomstkontroll (RBAC) gäller endast för Azure-portalen och sprids inte till SQL Server.

    ![Lägga till Azure Active Directory-administratör](./media/sql-database-aad-authentication/add-azure-active-directory-admin.png)

8. Högst upp på administrationssidan i Active Directory väljer du **Spara**.

    ![save](./media/sql-database-aad-authentication/save.png)

    Processen med att ändra administratör kan ta några minuter. Sedan visas den nya administratören i rutan Active Directory-administratör.

När du har etablerat en Azure AD-administratör för din hanterade instans kan du börja skapa Azure AD-serverobjekt (inloggningar) med syntaxen <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">skapa inloggning.</a> Mer information finns i [översikt över hanterade instanser](sql-database-managed-instance.md#azure-active-directory-integration).

> [!TIP]
> Om du senare vill ta bort en administratör väljer du **Ta bort administratör**högst upp på administrationssidan i Active Directory och väljer sedan **Spara**.

### <a name="new-azure-ad-admin-functionality-for-mi"></a>Ny Azure AD-administratörsfunktion för MI

Tabellen nedan sammanfattar funktionerna för den offentliga förhandsversionen av Azure AD-inloggningsadministratören för MI, jämfört med en ny funktion som levereras med GA för Azure AD-inloggningar.

| Azure AD-inloggningsadministratör för MI under offentlig förhandsversion | GA-funktioner för Azure AD-administratör för MI |
| --- | ---|
| Fungerar på ett liknande sätt som Azure AD-administratör för SQL Database, som aktiverar Azure AD-autentisering, men Azure AD-administratören kan inte skapa Azure AD- eller SQL-inloggningar i huvud db för MI. | Azure AD-administratör har sysadmin-behörighet och kan skapa AAD- och SQL-inloggningar i huvud db för MI. |
| Finns inte i vyn sys.server_principals | Finns i vyn sys.server_principals |
| Gör att enskilda Azure AD-gästanvändare kan konfigureras som Azure AD-administratör för MI. Mer information finns [i Lägga till Azure Active Directory B2B-samarbetsanvändare i Azure-portalen](../active-directory/b2b/add-users-administrator.md). | Kräver att du skapar en Azure AD-grupp med gästanvändare som medlemmar för att konfigurera den här gruppen som En Azure AD-administratör för MI. Mer information finns i [Azure AD business to business support](sql-database-ssms-mfa-authentication.md#azure-ad-business-to-business-support). |

Som bästa praxis för befintliga Azure AD-administratörer för MI som skapats före GA, och fortfarande använder post GA, återställer Azure AD-administratören med alternativet "Ta bort administratör" och "Ange administratör" för samma Azure AD-användare eller grupp.

### <a name="known-issues-with-the-azure-ad-login-ga-for-mi"></a>Kända problem med Azure AD-inloggning ga för MI

- Om det finns en Azure AD-inloggning i huvuddatabasen `CREATE LOGIN [myaadaccount] FROM EXTERNAL PROVIDER`för MI, som skapats med kommandot T-SQL, kan den inte konfigureras som En Azure AD-administratör för MI. Ett fel uppstår när du anger inloggningen som en Azure AD-administratör med azure-portal-, PowerShell- eller CLI-kommandona för att skapa Azure AD-inloggningen.
  - Inloggningen måste tas bort i huvuddatabasen med kommandot `DROP LOGIN [myaadaccount]`, innan kontot kan skapas som en Azure AD-administratör.
  - Konfigurera Azure AD-administratörskontot i `DROP LOGIN` Azure-portalen när det har lyckats. 
  - Om du inte kan konfigurera Azure AD-administratörskontot kontrollerar du huvuddatabasen för den hanterade instansen för inloggningen. Använd följande kommando:`SELECT * FROM sys.server_principals`
  - Om du konfigurerar en Azure AD-administratör för MI skapas automatiskt en inloggning i huvuddatabasen för det här kontot. Om du tar bort Azure AD-administratören släpps inloggningen automatiskt från huvuddatabasen.

- Enskilda Azure AD-gästanvändare stöds inte som Azure AD-administratörer för MI. Gästanvändare måste ingå i en Azure AD-grupp som ska konfigureras som Azure AD-administratör. Azure-portalbladet är för närvarande inte att jämna ut gästanvändare för en annan Azure AD, vilket gör att användarna kan fortsätta med administratörskonfigurationen. Om du sparar gästanvändare som en Azure AD-administratör misslyckas installationen.
  - Om du vill göra en gästanvändare till En Azure AD-administratör för MI, inkludera gästanvändaren i en Azure AD-grupp och ange den här gruppen som en Azure AD-administratör.

### <a name="powershell-for-sql-managed-instance"></a>PowerShell för SQL-hanterad instans

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Om du vill köra PowerShell-cmdletar måste du ha Azure PowerShell installerat och körs. Mer information finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/overview).

> [!IMPORTANT]
> PowerShell Azure Resource Manager (RM) stöds fortfarande av Azure SQL Database, men all framtida utveckling är för Az.Sql-modulen. AzureRM-modulen fortsätter att ta emot buggfixar fram till åtminstone december 2020.  Argumenten för kommandona i Az-modulen och i AzureRm-modulerna är i stort sett identiska. Mer information om deras kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](/powershell/azure/new-azureps-module-az).

Om du vill etablera en Azure AD-administratör kör du följande Azure PowerShell-kommandon:

- Anslut-AzAccount
- Välj-azabonnemang

Cmdlets som används för att etablera och hantera Azure AD-administratör för SQL-hanterad instans:

| Cmdlet-namn | Beskrivning |
| --- | --- |
| [Set-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlinstanceactivedirectoryadministrator) |Etablerar en Azure AD-administratör för SQL-hanterad instans i den aktuella prenumerationen. (Måste vara från den aktuella prenumerationen)|
| [Ta bort-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlinstanceactivedirectoryadministrator) |Tar bort en Azure AD-administratör för SQL-hanterad instans i den aktuella prenumerationen. |
| [Get-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlinstanceactivedirectoryadministrator) |Returnerar information om en Azure AD-administratör för SQL-hanterad instans i den aktuella prenumerationen.|

Följande kommando hämtar information om en Azure AD-administratör för en hanterad instans med namnet ManagedInstance01 som är associerad med en resursgrupp med namnet ResourceGroup01.

```powershell
Get-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstance01"
```

Följande kommando avsättningar en Azure AD-administratörsgrupp med namnet DBAs för den hanterade instansen med namnet ManagedInstance01. Den här servern är associerad med resursgruppen ResourceGroup01.

```powershell
Set-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstance01" -DisplayName "DBAs" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353b"
```

Följande kommando tar bort Azure AD-administratören för den hanterade instansen som heter ManagedInstanceName01 som är associerad med resursgruppen ResourceGroup01.

```powershell
Remove-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstanceName01" -Confirm -PassThru
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Du kan också etablera en Azure AD-administratör för SQL-hanterad instans genom att anropa följande CLI-kommandon:

| Kommando | Beskrivning |
| --- | --- |
|[az sql mi ad-admin skapa](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-create) | Etablerar en Azure Active Directory-administratör för SQL-hanterad instans. (Måste vara från den aktuella prenumerationen) |
|[az sql mi ad-admin delete az sql mi ad-admin delete az sql mi ad-admin delete az sql](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-delete) | Tar bort en Azure Active Directory-administratör för SQL-hanterad instans. |
|[az sql mi ad-admin lista](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-list) | Returnerar information om en Azure Active Directory-administratör som för närvarande är konfigurerad för SQL-hanterad instans. |
|[az sql mi ad-admin uppdatering](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-update) | Uppdaterar Active Directory-administratören för en SQL-hanterad instans. |

Mer information om CLI-kommandon finns i [az sql mi](/cli/azure/sql/mi).

* * *

## <a name="provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server"></a>Etablera en Azure Active Directory-administratör för din Azure SQL Database-server

> [!IMPORTANT]
> Följ bara dessa steg om du etablerar en Azure SQL Database-server eller Data Warehouse.

Följande två procedurer visar hur du etablerar en Azure Active Directory-administratör för din Azure SQL-server i Azure-portalen och med hjälp av PowerShell.

### <a name="azure-portal"></a>Azure Portal

1. På [Azure-portalen](https://portal.azure.com/) väljer du din anslutning i det övre högra hörnet för att visa en lista över möjliga Active Directories. Välj rätt Active Directory som standard-Azure AD. Det här steget länkar prenumerationsassocierad Active Directory till Azure SQL-servern, vilket gör att samma prenumeration används för både Azure AD och SQL Server. (Azure SQL-servern kan vara värd för antingen Azure SQL Database eller Azure SQL Data Warehouse.)

    ![choose-ad][8]

2. Sök efter och välj **SQL-server**.

    ![Söka efter och välj SQL-servrar](media/sql-database-aad-authentication/search-for-and-select-sql-servers.png)

    >[!NOTE]
    > På den här sidan, innan du väljer **SQL-servrar,** kan du välja **stjärnan** bredvid namnet för att *favoritkategorin* och lägga till **SQL-servrar** i det vänstra navigeringsfältet.

3. På **SQL Server-sidan** väljer du **Active Directory-administratör**.

4. På **sidan Active Directory-administratör** väljer du **Ange administratör**.

    ![SQL-servrar anger Active Directory-administratör](./media/sql-database-aad-authentication/sql-servers-set-active-directory-admin.png)  

5. på sidan **Lägg till administratör** söker du efter en användare, väljer den användare eller den grupp som ska vara administratör och väljer sedan **Välj**. (Active Directory-administratörssidan visar alla medlemmar och grupper för din Active Directory. Användare eller grupper som är nedtonade kan inte väljas eftersom de inte stöds som Azure AD-administratörer. (Se listan över administratörer som stöds i avsnittet **Azure AD-funktioner och begränsningar** i Använd Azure Active [Directory-autentisering för autentisering med SQL Database eller SQL Data Warehouse](sql-database-aad-authentication.md).) Rollbaserad åtkomstkontroll (RBAC) gäller endast för portalen och sprids inte till SQL Server.

    ![Välj Azure Active Directory-administratör](./media/sql-database-aad-authentication/select-azure-active-directory-admin.png)  

6. Längst upp på sidan **Active Directory-administratör** väljer du **SPARA**.

    ![spara admin](./media/sql-database-aad-authentication/save-admin.png)

Processen med att ändra administratör kan ta några minuter. Sedan visas den nya administratören i rutan **Active Directory-administratör**.

   > [!NOTE]
   > När Azure AD-administratören konfigureras får namnet för den nya administratören (användare eller grupp) inte redan finnas i den virtuella huvuddatabasen som SQL Server-autentiseringsanvändare. Om det finns misslyckas Azure AD-administratörskonfigurationen. Den återställer skapande och anger att den administratören (namnet) redan finns. Eftersom en sådan SQL Server-autentiseringsanvändare inte är en del av Azure AD misslyckas alla försök att ansluta till servern med hjälp av Azure AD-autentisering.

Om du senare vill ta bort en administratör väljer du **Ta bort administratör**högst upp på **administrationssidan** i Active Directory och väljer sedan **Spara**.

### <a name="powershell-for-azure-sql-database-and-azure-sql-data-warehouse"></a>PowerShell för Azure SQL Database och Azure SQL Data Warehouse

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Om du vill köra PowerShell-cmdletar måste du ha Azure PowerShell installerat och körs. Mer information finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/overview). Om du vill etablera en Azure AD-administratör kör du följande Azure PowerShell-kommandon:

- Anslut-AzAccount
- Välj-azabonnemang

Cmdlets som används för att etablera och hantera Azure AD-administratör för Azure SQL Database och Azure SQL Data Warehouse:

| Cmdlet-namn | Beskrivning |
| --- | --- |
| [Set-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlserveractivedirectoryadministrator) |Etablerar en Azure Active Directory-administratör för Azure SQL-server eller Azure SQL Data Warehouse. (Måste vara från den aktuella prenumerationen) |
| [Ta bort-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlserveractivedirectoryadministrator) |Tar bort en Azure Active Directory-administratör för Azure SQL-server eller Azure SQL Data Warehouse. |
| [Få-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator) |Returnerar information om en Azure Active Directory-administratör som för närvarande är konfigurerad för Azure SQL-servern eller Azure SQL Data Warehouse. |

Använd PowerShell-kommandot get-help för att se mer information för vart och ett av dessa kommandon. Till exempel `get-help Set-AzSqlServerActiveDirectoryAdministrator`.

Följande skript etablerar en Azure AD-administratörsgrupp med `40b79501-b343-44ed-9ce7-da4c8cc7353f`namnet **DBA_Group** (objekt-ID) för **demo_server** server i en resursgrupp med namnet **Grupp-23:**

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" -DisplayName "DBA_Group"
```

Indataparametern **DisplayName** accepterar antingen Azure AD-visningsnamnet eller användarnamnet. Till exempel ``DisplayName="John Smith"`` ``DisplayName="johns@contoso.com"``och . För Azure AD-grupper stöds endast Azure AD-visningsnamnet.

> [!NOTE]
> Azure PowerShell-kommandot ```Set-AzSqlServerActiveDirectoryAdministrator``` hindrar dig inte från att etablera Azure AD-administratörer för användare som inte stöds. En användare som inte stöds kan etableras, men kan inte ansluta till en databas.

I följande exempel används det valfria **ObjectID:**

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" `
    -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
```

> [!NOTE]
> Azure AD **ObjectID** krävs när **DisplayName** inte är unikt. Om du vill hämta **värdena ObjectID** och **DisplayName** använder du active directory-avsnittet i Azure Classic Portal och visar egenskaperna för en användare eller grupp.

I följande exempel returneras information om den aktuella Azure AD-administratören för Azure SQL-servern:

```powershell
Get-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" | Format-List
```

I följande exempel tas en Azure AD-administratör bort:

```powershell
Remove-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Du kan etablera en Azure AD-administratör genom att anropa följande CLI-kommandon:

| Kommando | Beskrivning |
| --- | --- |
|[az sql server ad-admin skapa](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-create) | Etablerar en Azure Active Directory-administratör för Azure SQL-server eller Azure SQL Data Warehouse. (Måste vara från den aktuella prenumerationen) |
|[az sql server ad-admin delete az sql server ad-admin delete az sql server ad-admin delete az sql](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-delete) | Tar bort en Azure Active Directory-administratör för Azure SQL-server eller Azure SQL Data Warehouse. |
|[az sql server ad-admin lista](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-list) | Returnerar information om en Azure Active Directory-administratör som för närvarande är konfigurerad för Azure SQL-servern eller Azure SQL Data Warehouse. |
|[az sql server ad-admin uppdatering](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-update) | Uppdaterar Active Directory-administratören för en Azure SQL-server eller Azure SQL Data Warehouse. |

Mer information om CLI-kommandon finns i [az sql server](/cli/azure/sql/server).

* * *

> [!NOTE]
> Du kan också etablera en Azure Active Directory-administratör med hjälp av REST-API:erna. Mer information finns i [REST-API-referens och åtgärder för TJÄNSThantering FÖR Azure SQL Database Operations för Azure SQL Database](/rest/api/sql/)

## <a name="configure-your-client-computers"></a>Konfigurera klientdatorerna

På alla klientdatorer, från vilka dina program eller användare ansluter till Azure SQL Database eller Azure SQL Data Warehouse med Hjälp av Azure AD-identiteter, måste du installera följande programvara:

- .NET Framework 4.6 [https://msdn.microsoft.com/library/5a4x27ek.aspx](https://msdn.microsoft.com/library/5a4x27ek.aspx)eller senare från .
- Azure Active Directory Authentication Library för SQL Server (*ADAL. DLL*). Nedan finns hämtningslänkarna för att installera den senaste SSMS-, ODBC- och OLE DB-drivrutinen som innehåller *ADAL. DLL-bibliotek.*
    1. [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)
    1. [ODBC-drivrutin 17 för SQL Server](https://www.microsoft.com/download/details.aspx?id=56567)
    1. [OLE DB-drivrutin 18 för SQL Server](https://www.microsoft.com/download/details.aspx?id=56730)

Du kan uppfylla dessa krav genom att:

- Om du installerar den senaste versionen av [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) eller SQL Server Data [Tools](/sql/ssdt/download-sql-server-data-tools-ssdt) uppfylls .NET Framework 4.6-kravet.
    - SSMS installerar x86-versionen av *ADAL. DLL*.
    - SSDT installerar amd64-versionen av *ADAL. DLL*.
    - Den senaste Visual Studio från [Visual Studio Downloads](https://www.visualstudio.com/downloads/download-visual-studio-vs) uppfyller .NET Framework 4.6-kravet, men installerar inte den nödvändiga amd64-versionen av *ADAL. DLL*.

## <a name="create-contained-database-users-in-your-database-mapped-to-azure-ad-identities"></a>Skapa innehållna databasanvändare i databasen mappade till Azure AD-identiteter

> [!IMPORTANT]
> Hanterad instans stöder nu Azure AD-serverhuvudnamn (inloggningar), vilket gör att du kan skapa inloggningar från Azure AD-användare, grupper eller program. Azure AD-serverhuvudnamn (inloggningar) ger möjlighet att autentisera till din hanterade instans utan att kräva att databasanvändare skapas som en innesluten databasanvändare. Mer information finns i [Översikt över hanterade instanser](sql-database-managed-instance.md#azure-active-directory-integration). Syntax för hur du skapar Azure AD-serverobjekt (inloggningar) finns i <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">SKAPA INLOGGNING</a>.

Azure Active Directory-autentisering kräver att databasanvändare skapas som användare av oberoende databas. En användare av oberoende databas baserad på en Azure AD-identitet är en databasanvändare som inte har någon inloggning i huvuddatabasen, och som mappas till en identitet i den Azure AD-katalog som är associerad med databasen. Azure AD-identiteten kan vara antingen ett enskilt användarkonto eller en grupp. Mer information om användare av oberoende databas finns i avsnittet om [användare av oberoende databas – så gör du din databas portabel](https://msdn.microsoft.com/library/ff929188.aspx).

> [!NOTE]
> Databasanvändare (med undantag för administratörer) kan inte skapas med hjälp av Azure-portalen. RBAC-roller sprids inte till SQL Server, SQL Database eller SQL Data Warehouse. Azure RBAC-roller används för att hantera Azure-resurser och gäller inte för databasbehörigheter. Till exempel beviljar rollen **SQL Server-deltagare** inte åtkomst för att ansluta till SQL Database eller SQL Data Warehouse. Åtkomstbehörigheten måste beviljas direkt i databasen med hjälp av Transact-SQL-instruktioner.

> [!WARNING]
> Specialtecken som kolon `:` eller et-tecken `&` stöds inte när de ingår i användarnamn i instruktionerna T-SQL CREATE LOGIN och CREATE USER.

Om du vill skapa en Azure AD-baserad databasanvändare (förutom serveradministratören som äger databasen) ansluter du till databasen med en Azure AD-identitet, som en användare med åtminstone **behörigheten ÄNDRA ALLA ANVÄNDARE.** Använd sedan följande transact-SQL-syntax:

```sql
CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
```

*Azure_AD_principal_name* kan vara användarens huvudnamn för en Azure AD-användare eller visningsnamnet för en Azure AD-grupp.

**Exempel:** Så här skapar du en innehållen databasanvändare som representerar en Azure AD-federerad eller hanterad domänanvändare:

```sql
CREATE USER [bob@contoso.com] FROM EXTERNAL PROVIDER;
CREATE USER [alice@fabrikam.onmicrosoft.com] FROM EXTERNAL PROVIDER;
```

Om du vill skapa en innehållen databasanvändare som representerar en Azure AD- eller federerad domängrupp anger du visningsnamnet för en säkerhetsgrupp:

```sql
CREATE USER [ICU Nurses] FROM EXTERNAL PROVIDER;
```

Så här skapar du en innehållen databasanvändare som representerar ett program som ansluter med en Azure AD-token:

```sql
CREATE USER [appName] FROM EXTERNAL PROVIDER;
```

> [!NOTE]
> Det här kommandot kräver att SQL Access Azure AD (den "externa providern") på uppdrag av den inloggade användaren. Ibland uppstår omständigheter som gör att Azure AD returnerar ett undantag tillbaka till SQL. I dessa fall kommer användaren att se SQL-fel 33134, som bör innehålla AAD-specifika felmeddelande. För det mesta kommer felet att säga att åtkomst nekas, eller att användaren måste registrera sig i MFA för att komma åt resursen, eller att åtkomst mellan första parts program måste hanteras via förauktorisering. I de två första fallen orsakas problemet vanligtvis av principer för villkorlig åtkomst som anges i användarens AAD-klientorganisation: de hindrar användaren från att komma åt den externa providern. Uppdatering av CA-principerna för att ge åtkomst till programmet "00000002-0000-0000-c000-00000000000" (program-ID för AAD Graph API) bör lösa problemet. Om felet säger att åtkomst mellan förstapartsprogram måste hanteras via förauktorisering beror problemet på att användaren är inloggad som tjänsthuvudnamn. Kommandot bör lyckas om det körs av en användare i stället.

> [!TIP]
> Du kan inte direkt skapa en användare från en annan Azure Active Directory än Azure Active Directory som är associerad med din Azure-prenumeration. Medlemmar i andra aktiva kataloger som importeras användare i den associerade Active Directory (så kallade externa användare) kan dock läggas till i en Active Directory-grupp i Active Directory. Genom att skapa en innehållen databasanvändare för den AD-gruppen kan användarna från den externa Active Directory få åtkomst till SQL Database.

Mer information om hur du skapar innehållna databasanvändare baserat på Azure Active Directory-identiteter finns i [SKAPA ANVÄNDARE (Transact-SQL).](https://msdn.microsoft.com/library/ms173463.aspx)

> [!NOTE]
> Om du tar bort Azure Active Directory-administratören för Azure SQL-servern hindras alla Azure AD-autentiseringsanvändare från att ansluta till servern. Om det behövs kan oanvändbara Azure AD-användare tas bort manuellt av en SQL-databasadministratör.

> [!NOTE]
> Om du får en **timeout för anslutning** `TransparentNetworkIPResolution` som har upphört att gälla kan du behöva ange att parametern för anslutningssträngen ska false. Mer information finns i [Timeout för anslutning med .NET Framework 4.6.1 - TransparentNetworkIPResolution](https://blogs.msdn.microsoft.com/dataaccesstechnologies/20../../connection-timeout-issue-with-net-framework-4-6-1-transparentnetworkipresolution/).

När du skapar en databasanvändare får användaren **CONNECT-behörigheten** och kan ansluta till databasen som medlem i rollen **OFFENTLIG.** Inledningsvis är de enda behörigheter som är tillgängliga för användaren alla behörigheter som beviljas till **public-rollen,** eller behörigheter som beviljas till alla Azure AD-grupper som de är medlemmar i. När du har etablerat en Azure AD-baserad databasanvändare kan du ge användaren ytterligare behörigheter, på samma sätt som du ger behörighet till någon annan typ av användare. Bevilja vanligtvis behörigheter till databasroller och lägga till användare i roller. Mer information finns i [Grunderna för behörighet för databasmotor](https://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx). Mer information om särskilda SQL Database-roller finns [i Hantera databaser och inloggningar i Azure SQL Database](sql-database-manage-logins.md).
Ett federerat domänanvändarkonto som importeras till en hanterad domän som en extern användare måste använda den hanterade domänidentiteten.

> [!NOTE]
> Azure AD-användare markeras i databasmetadata med typen E (EXTERNAL_USER) och för grupper med typen X (EXTERNAL_GROUPS). Mer information finns i [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx).

## <a name="connect-to-the-user-database-or-data-warehouse-by-using-ssms-or-ssdt"></a>Ansluta till användardatabasen eller informationslagret med hjälp av SSMS eller SSDT  

Om du vill bekräfta att Azure AD-administratören är korrekt konfigurerad ansluter du till **huvuddatabasen** med azure AD-administratörskontot.
Om du vill etablera en Azure AD-baserad databasanvändare (förutom serveradministratören som äger databasen) ansluter du till databasen med en Azure AD-identitet som har åtkomst till databasen.

> [!IMPORTANT]
> Stöd för Azure Active Directory-autentisering är tillgängligt med [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) och SQL Server Data [Tools](https://msdn.microsoft.com/library/mt204009.aspx) i Visual Studio 2015. Lanseringen av SSMS i augusti 2016 innehåller också stöd för Universell Active Directory-autentisering, vilket gör det möjligt för administratörer att kräva multifaktorautentisering med ett telefonsamtal, sms, smartkort med pin eller mobilappsavisering.

## <a name="using-an-azure-ad-identity-to-connect-using-ssms-or-ssdt"></a>Använda en Azure AD-identitet för att ansluta med SSMS eller SSDT

Följande procedurer visar hur du ansluter till en SQL-databas med en Azure AD-identitet med hjälp av SQL Server Management Studio eller SQL Server Database Tools.

### <a name="active-directory-integrated-authentication"></a>Active Directory-integrerad autentisering

Använd den här metoden om du är inloggad i Windows med dina Azure Active Directory-autentiseringsuppgifter från en federerad domän.

1. Starta Hanteringsstudion eller dataverktygen och välj **Active Directory - Integrated**i dialogrutan Anslut till **server** (eller Anslut **till databasmotor)** i rutan **Autentisering** . Inget lösenord behövs eller kan anges eftersom dina befintliga autentiseringsuppgifter visas för anslutningen.

    ![Välj AD-integrerad autentisering][11]

2. Välj knappen **Alternativ** och skriv namnet på den användardatabas som du vill ansluta till i rutan Anslut till databas på sidan **Anslutningsegenskaper.** **Connect to database** (ALTERNATIVET **AD-domännamn eller klient-ID**" stöds endast för **Universell med MFA-anslutningsalternativ,** annars är det nedtonat.)  

    ![Markera databasnamnet][13]

## <a name="active-directory-password-authentication"></a>Active Directory-lösenordsautentisering

Använd den här metoden när du ansluter till ett Azure AD-huvudnamn med den Azure AD-hanterade domänen. Du kan också använda den för federerade konton utan åtkomst till domänen, till exempel när du arbetar på distans.

Använd den här metoden för att autentisera till SQL DB/DW med Azure AD för inbyggda eller federerade Azure AD-användare. En inbyggd användare är en som uttryckligen skapas i Azure AD och autentiseras med användarnamn och lösenord, medan en federerad användare är en Windows-användare vars domän är federerad med Azure AD. Den senare metoden (med hjälp av & lösenord) kan användas när en användare vill använda sina Windows-autentiseringsuppgifter, men deras lokala dator är inte ansluten till domänen (till exempel med hjälp av en fjärråtkomst). I det här fallet kan en Windows-användare ange sitt domänkonto och lösenord och autentisera till SQL DB/DW med federerade autentiseringsuppgifter.

1. Starta Hanteringsstudion eller dataverktygen och välj **Active Directory - Lösenord**i dialogrutan Anslut till **server** (eller Anslut **till databasmotor)** i rutan **Autentisering** .

2. Skriv **User name** ditt Azure Active Directory-användarnamn i formatet **användarnamn\@domain.com**. Användarnamn måste vara ett konto från Azure Active Directory eller ett konto från en domän federerar med Azure Active Directory.

3. Skriv ditt användarlösenord för Azure Active Directory-kontot eller det federerade domänkontot i rutan **Lösenord.**

    ![Välj AD-lösenordsautentisering][12]

4. Välj knappen **Alternativ** och skriv namnet på den användardatabas som du vill ansluta till i rutan Anslut till databas på sidan **Anslutningsegenskaper.** **Connect to database** (Se bilden i föregående alternativ.)

## <a name="using-an-azure-ad-identity-to-connect-from-a-client-application"></a>Använda en Azure AD-identitet för att ansluta från ett klientprogram

Följande procedurer visar hur du ansluter till en SQL-databas med en Azure AD-identitet från ett klientprogram.

### <a name="active-directory-integrated-authentication"></a>Active Directory-integrerad autentisering

Om du vill använda integrerad Windows-autentisering måste domänens Active Directory vara federerat med Azure Active Directory. Klientprogrammet (eller en tjänst) som ansluter till databasen måste köras på en domänansluten dator under en användares domänautentiseringsuppgifter.

Om du vill ansluta till en databas med integrerad autentisering och en Azure AD-identitet måste nyckelordet Authentication i databasanslutningssträngen anges till Active Directory Integrated. Följande C#-kodexempel använder ADO .NET.

```csharp
string ConnectionString = @"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated; Initial Catalog=testdb;";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

Nyckelordet anslutningssträng `Integrated Security=True` stöds inte för anslutning till Azure SQL Database. När du gör en ODBC-anslutning måste du ta bort blanksteg och ange autentisering till "ActiveDirectoryIntegrated".

### <a name="active-directory-password-authentication"></a>Active Directory-lösenordsautentisering

Om du vill ansluta till en databas med integrerad autentisering och en Azure AD-identitet måste nyckelordet Authentication anges till Active Directory-lösenord. Anslutningssträngen måste innehålla nyckelord och värden för användar-ID/UID och lösenord/PWD. Följande C#-kodexempel använder ADO .NET.

```csharp
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Password; Initial Catalog=testdb;  UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

Läs mer om Azure AD-autentiseringsmetoder med hjälp av de demokodsexempel som finns på [Azure AD Authentication GitHub Demo](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth).

## <a name="azure-ad-token"></a>Azure AD-token

Med den här autentiseringsmetoden kan medelnivåtjänster ansluta till Azure SQL Database eller Azure SQL Data Warehouse genom att hämta en token från Azure Active Directory (AAD). Det möjliggör avancerade scenarier, inklusive certifikatbaserad autentisering. Du måste utföra fyra grundläggande steg för att kunna använda Azure AD-tokenautentisering:

1. Registrera ditt program med Azure Active Directory och hämta klient-ID:et för din kod.
2. Skapa en databasanvändare som representerar programmet. (Slutförd tidigare i steg 6.)
3. Skapa ett certifikat på klientdatorn kör programmet.
4. Lägg till certifikatet som en nyckel för ditt program.

Exempel på anslutningssträng:

```csharp
string ConnectionString =@"Data Source=n9lxnyuzhv.database.windows.net; Initial Catalog=testdb;"
SqlConnection conn = new SqlConnection(ConnectionString);
conn.AccessToken = "Your JWT token"
conn.Open();
```

Mer information finns i [SQL Server Security Blog](https://blogs.msdn.microsoft.com/sqlsecurity/20../../token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth/). Information om hur du lägger till ett certifikat finns [i Komma igång med certifikatbaserad autentisering i Azure Active Directory](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md).

### <a name="sqlcmd"></a>sqlcmd

Följande satser, anslut med version 13.1 av sqlcmd, som är tillgänglig från [Download Center](https://www.microsoft.com/download/details.aspx?id=53591).

> [!NOTE]
> `sqlcmd`med `-G` kommandot fungerar inte med systemidentiteter och kräver en användarens huvudinloggning.

```cmd
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -G  
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -U bob@contoso.com -P MyAADPassword -G -l 30
```

## <a name="troubleshooting-azure-ad-authentication"></a>Felsöka Azure AD-autentisering

Vägledning om felsökningsproblem med Azure AD-autentisering finns i följande blogg:<https://techcommunity.microsoft.com/t5/azure-sql-database/troubleshooting-problems-related-to-azure-ad-authentication-with/ba-p/1062991>

## <a name="next-steps"></a>Nästa steg

- En översikt över inloggningar, användare, databasroller och behörigheter i SQL Database finns i [Inloggningar, användare, databasroller och användarkonton](sql-database-manage-logins.md).
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
