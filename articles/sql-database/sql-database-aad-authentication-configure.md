---
title: Konfigurera Azure Active Directory autentisering-SQL | Microsoft Docs
description: Lär dig hur du ansluter till SQL Database, hanterad instans och SQL Data Warehouse med hjälp av Azure Active Directory autentisering – när du har konfigurerat Azure AD.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: data warehouse
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, carlrab
ms.date: 03/12/2019
ms.openlocfilehash: b6414ac41b1bb43e3fe1470a7ae2b1358126003a
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569680"
---
# <a name="configure-and-manage-azure-active-directory-authentication-with-sql"></a>Konfigurera och hantera Azure Active Directory autentisering med SQL

Den här artikeln visar hur du skapar och fyller i Azure AD och använder sedan Azure AD med Azure [SQL Database](sql-database-technical-overview.md), [hanterad instans](sql-database-managed-instance.md)och [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md). En översikt finns i [Azure Active Directory autentisering](sql-database-aad-authentication.md).

> [!NOTE]
> Den här artikeln gäller för Azure SQL Server och för både SQL Database och SQL Data Warehouse databaser som skapas på Azure SQL-servern. För enkelhetens skull används SQL Database när det gäller både SQL Database och SQL Data Warehouse.
> [!IMPORTANT]  
> Det går inte att ansluta till SQL Server som körs på en virtuell Azure-dator med ett Azure Active Directory konto. Använd ett domän Active Directory konto i stället.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager-modulen stöds fortfarande av Azure SQL Database, men all framtida utveckling gäller AZ. SQL-modulen. De här cmdletarna finns i [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenten för kommandona i AZ-modulen och i AzureRm-modulerna är i stort sett identiska.

## <a name="create-and-populate-an-azure-ad"></a>Skapa och fylla i en Azure AD

Skapa en Azure AD och fyll i den med användare och grupper. Azure AD kan vara den första Azure AD-hanterade domänen. Azure AD kan också vara en lokal Active Directory Domain Services som är federerad med Azure AD.

Mer information finns i [Integrera dina lokala identiteter med Azure Active Directory](../active-directory/hybrid/whatis-hybrid-identity.md), [Add your own domain name to Azure AD](../active-directory/active-directory-domains-add-azure-portal.md) (Lägga till dina egna domännamn i Azure AD), [Microsoft Azure now supports federation with Windows Server Active Directory](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/) (Microsoft stöder nu federation med Windows Server Active Directory), [Administering your Azure AD directory](../active-directory/fundamentals/active-directory-administer.md) (Administrera Azure Active Directory), [Manage Azure AD using Windows PowerShell](/powershell/azure/overview) (Hantera Azure AD med Windows PowerShell) och [Hybrid Identity Required Ports and Protocols](../active-directory/hybrid/reference-connect-ports.md) (Portar och protokoll som krävs för hybrididentiet).

## <a name="associate-or-add-an-azure-subscription-to-azure-active-directory"></a>Associera eller lägga till en Azure-prenumeration i Azure Active Directory

1. Koppla din Azure-prenumeration till Azure Active Directory genom att göra katalogen till en betrodd katalog för Azure-prenumerationen som är värd för databasen. Mer information finns i [hur Azure-prenumerationer är associerade med Azure AD](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).
2. Använd katalogen växlaren i Azure Portal för att växla till den prenumeration som är kopplad till domänen.

   **Ytterligare information:** Alla Azure-prenumerationer har en förtroenderelation med en Azure AD-instans. Det innebär att den litar på den katalogen för att autentisera användare, tjänster och enheter. Flera prenumerationer kan lita på samma katalog, men en prenumeration litar bara på en katalog. Den här förtroenderelationen mellan en prenumeration och en katalog skiljer sig från relationen mellan en prenumeration och alla andra resurser i Azure (webbplatser, databaser och så vidare), som är mer som underordnade resurser till en prenumeration. Om en prenumeration går ut stoppas även åtkomsten till de resurser som är associerade med prenumerationen. Men katalogen finns kvar i Azure och du kan associera en annan prenumeration med katalogen och fortsätta hantera kataloganvändarna. Mer information om resurser finns i [förstå resurs åtkomst i Azure](../active-directory/active-directory-b2b-admin-add-users.md). Mer information om den här betrodda relationen finns i [så här kopplar du eller lägger till en Azure-prenumeration i Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="create-an-azure-ad-administrator-for-azure-sql-server"></a>Skapa en Azure AD-administratör för Azure SQL Server

Varje Azure SQL Server (som är värd för en SQL Database eller SQL Data Warehouse) börjar med ett enda server administratörs konto som är administratör för hela Azure SQL-servern. En andra SQL Server-administratör måste skapas, det vill säga ett Azure AD-konto. Den här huvud gruppen skapas som en innesluten databas användare i huvud databasen. Som administratörer är Server administratörs kontona medlemmar i rollen **db_owner** i varje användar databas och ange varje användar databas som **dbo** -användare. Mer information om Server administratörs konton finns i [Hantera databaser och inloggningar i Azure SQL Database](sql-database-manage-logins.md).

När du använder Azure Active Directory med geo-replikering måste Azure Active Directorys administratören konfigureras för både den primära och sekundära servern. Om en server inte har en Azure Active Directory administratör kan Azure Active Directory inloggningar och användare ta emot fel meddelandet "det går inte att ansluta till servern".

> [!NOTE]
> Användare som inte är baserade på ett Azure AD-konto (inklusive Azure SQL Server-administratörskontot) kan inte skapa Azure AD-baserade användare eftersom de inte har behörighet att validera föreslagna databas användare med Azure AD.

## <a name="provision-an-azure-active-directory-administrator-for-your-managed-instance"></a>Etablera en Azure Active Directory administratör för din hanterade instans

> [!IMPORTANT]
> Följ bara de här stegen om du konfigurerar en hanterad instans. Den här åtgärden kan bara utföras av global/företags administratör i Azure AD. Följande steg beskriver hur du beviljar behörigheter för användare med olika behörigheter i katalogen.

Din hanterade instans måste ha behörighet att läsa Azure AD för att kunna utföra uppgifter som autentisering av användare via säkerhets grupp medlemskap eller skapande av nya användare. För att detta ska fungera måste du bevilja behörigheter till hanterad instans för att läsa Azure AD. Det finns två sätt att göra det: från portalen och PowerShell. Följande steg båda metoderna.

1. I det övre högra hörnet i Azure Portal väljer du din anslutning för att ta fram en lista över möjliga Active-kataloger.
2. Välj rätt Active Directory som standard-Azure AD.

   Det här steget länkar den prenumeration som är kopplad till Active Directory med hanterad instans som kontrollerar att samma prenumeration används för både Azure AD och den hanterade instansen.
3. Gå till den hanterade instansen och välj en som du vill använda för Azure AD-integrering.

   ![AAD](./media/sql-database-aad-authentication/aad.png)

4. Välj banderollen överst på sidan Active Directory administratör och ge den aktuella användaren behörighet. Om du är inloggad som global/företags administratör i Azure AD kan du göra det från Azure Portal eller använda PowerShell med skriptet nedan.

    ![bevilja behörigheter – Portal](./media/sql-database-aad-authentication/grant-permissions.png)

    ```powershell
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

    ![success](./media/sql-database-aad-authentication/success.png)

6. Nu kan du välja din Azure AD-administratör för din hanterade instans. På sidan Active Directory administratör väljer du **Ange admin** -kommando.

    ![set-admin](./media/sql-database-aad-authentication/set-admin.png)

7. På sidan AAD-administratör söker du efter en användare, väljer den användare eller grupp som ska vara administratör och väljer sedan **Välj**.

   På sidan Active Directory admin visas alla medlemmar och grupper av din Active Directory. Det går inte att välja användare eller grupper som är nedtonade eftersom de inte stöds som Azure AD-administratörer. Se listan över administratörer som stöds i [funktioner och begränsningar i Azure AD](sql-database-aad-authentication.md#azure-ad-features-and-limitations). Rollbaserad åtkomst kontroll (RBAC) gäller endast för Azure Portal och sprids inte till SQL Server.

    ![add-admin](./media/sql-database-aad-authentication/add-admin.png)

8. Välj **Spara**längst upp på sidan Active Directory administratör.

    ![spara](./media/sql-database-aad-authentication/save.png)

    Processen med att ändra administratör kan ta några minuter. Sedan visas den nya administratören i rutan Active Directory administratör.

Efter etableringen av en Azure AD-administratör för din hanterade instans kan du börja skapa Azure AD server-huvudobjekt (inloggningar) (**offentlig för hands version**) med syntaxen för att <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">Skapa inloggning</a> . Mer information finns i [Översikt över hanterade instanser](sql-database-managed-instance.md#azure-active-directory-integration).

> [!TIP]
> Om du senare vill ta bort en administratör väljer du **ta bort administratör**längst upp på sidan Active Directory administratör och väljer sedan **Spara**.

## <a name="provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server"></a>Etablera en Azure Active Directory-administratör för din Azure SQL Database-server

> [!IMPORTANT]
> Följ bara de här stegen om du konfigurerar en Azure SQL Database Server eller ett informations lager.

Följande två procedurer visar hur du etablerar en Azure Active Directory administratör för din Azure SQL-Server i Azure Portal och med hjälp av PowerShell.

### <a name="azure-portal"></a>Azure Portal

1. På [Azure-portalen](https://portal.azure.com/) väljer du din anslutning i det övre högra hörnet för att visa en lista över möjliga Active Directories. Välj rätt Active Directory som standard-Azure AD. Det här steget länkar prenumerationsassocierad Active Directory till Azure SQL-servern, vilket gör att samma prenumeration används för både Azure AD och SQL Server. (Azure SQL Server kan vara värd för antingen Azure SQL Database eller Azure SQL Data Warehouse.) ![Välj AD][8]

2. I den vänstra banderollen väljer du **alla tjänster**och i filter typen i **SQL Server**. Välj **SQL-servrar**.

    ![sqlservers.png](media/sql-database-aad-authentication/sqlservers.png)

    >[!NOTE]
    > På den här sidan, innan du väljer **SQL-servrar**, kan du välja **stjärnan** bredvid namnet för att *favorit* kategorin och lägga till **SQL-servrar** i det vänstra navigerings fältet.

3. Välj **Active Directory admin**på **SQL Server** sida.
4. På sidan **Active Directory administratör** väljer du **Ange administratör**.  ![Välj Active Directory](./media/sql-database-aad-authentication/select-active-directory.png)  

5. på sidan **Lägg till administratör** söker du efter en användare, väljer den användare eller den grupp som ska vara administratör och väljer sedan **Välj**. (Active Directory-administratörssidan visar alla medlemmar och grupper för din Active Directory. Användare eller grupper som är nedtonade kan inte väljas eftersom de inte stöds som Azure AD-administratörer. (Se listan över administratörer som stöds i avsnittet om **Azure AD-funktioner och begränsningar** i artikeln om att [använda Azure Active Directory-autentisering för autentisering med SQL Database eller SQL Data Warehouse](sql-database-aad-authentication.md).) Rollbaserad åtkomstkontroll (RBAC) gäller enbart för portalen och sprids inte till SQL Server.
    ![välja admin](./media/sql-database-aad-authentication/select-admin.png)  

6. Längst upp på sidan **Active Directory-administratör** väljer du **SPARA**.
    ![spara admin](./media/sql-database-aad-authentication/save-admin.png)

Processen med att ändra administratör kan ta några minuter. Sedan visas den nya administratören i rutan **Active Directory-administratör**.

   > [!NOTE]
   > När Azure AD-administratören konfigureras får namnet för den nya administratören (användare eller grupp) inte redan finnas i den virtuella huvuddatabasen som SQL Server-autentiseringsanvändare. Om det finns misslyckas Azure AD-administratörskonfigurationen. Den återställer skapande och anger att den administratören (namnet) redan finns. Eftersom en sådan SQL Server-autentiseringsanvändare inte är en del av Azure AD misslyckas alla försök att ansluta till servern med hjälp av Azure AD-autentisering.

Om du senare vill ta bort en administratör väljer du **ta bort administratör**längst upp på sidan **Active Directory administratör** och väljer sedan **Spara**.

### <a name="powershell"></a>PowerShell

Om du vill köra PowerShell-cmdlets måste Azure PowerShell vara installerad och igång. Mer information finns i [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview). Om du vill etablera en Azure AD-administratör kör du följande Azure PowerShell-kommandon:

- Anslut – AzAccount
- Select-AzSubscription

Cmdletar som används för att etablera och hantera Azure AD-administratör:

| Cmdlet-namn | Beskrivning |
| --- | --- |
| [Set-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlserveractivedirectoryadministrator) |Etablerar en Azure Active Directory administratör för Azure SQL Server eller Azure SQL Data Warehouse. (Måste vara från den aktuella prenumerationen.) |
| [Remove-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlserveractivedirectoryadministrator) |Tar bort en Azure Active Directory administratör för Azure SQL Server eller Azure SQL Data Warehouse. |
| [Get-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator) |Returnerar information om en Azure Active Directory administratör som för närvarande har kon figurer ATS för Azure SQL Server eller Azure SQL Data Warehouse. |

Använd PowerShell-kommandot Get-Help för att se mer information för vart och ett av dessa ``get-help Set-AzSqlServerActiveDirectoryAdministrator``kommandon, till exempel.

Följande skript etablerar en Azure AD-administratörs grupp med namnet DBA_Group `40b79501-b343-44ed-9ce7-da4c8cc7353f`(objekt-ID) för **demo_server** -servern i en resurs grupp med namnet **Group-23**:

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23"
-ServerName "demo_server" -DisplayName "DBA_Group"
```

Indataparametern **DisplayName** -Indataparametern accepterar antingen visnings namnet för Azure AD eller användarens huvud namn. Till exempel ``DisplayName="John Smith"`` och ``DisplayName="johns@contoso.com"``. Endast Azure AD-visnings namn stöds för Azure AD-grupper.

> [!NOTE]
> Kommandot ```Set-AzSqlServerActiveDirectoryAdministrator``` Azure PowerShell förhindrar inte att du konfigurerar Azure AD-administratörer för användare som inte stöds. En användare som inte stöds kan vara etablerad, men kan inte ansluta till en databas.

I följande exempel används det valfria **ObjectID**:

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23"
-ServerName "demo_server" -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
```

> [!NOTE]
> Azure AD **ObjectID** krävs om **DisplayName** inte är unikt. Hämta värdena **ObjectID** och **DisplayName** genom att använda Active Directory avsnittet i klassisk Azure-Portal och visa egenskaperna för en användare eller grupp.

I följande exempel returneras information om den aktuella Azure AD-administratören för Azure SQL Server:

```powershell
Get-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" | Format-List
```

Följande exempel tar bort en Azure AD-administratör:

```powershell
Remove-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server"
```

Du kan också etablera en Azure Active Directory administratör med hjälp av REST-API: er. Mer information finns i [Service Management REST API Reference and Operations for Azure SQL Database Operations for Azure SQL Database](https://docs.microsoft.com/rest/api/sql/)

### <a name="cli"></a>CLI  

Du kan också etablera en Azure AD-administratör genom att anropa följande CLI-kommandon:

| Kommando | Beskrivning |
| --- | --- |
|[AZ SQL Server AD-admin Create](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-create) |Etablerar en Azure Active Directory administratör för Azure SQL Server eller Azure SQL Data Warehouse. (Måste vara från den aktuella prenumerationen.) |
|[AZ SQL Server AD-admin Delete](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-delete) |Tar bort en Azure Active Directory administratör för Azure SQL Server eller Azure SQL Data Warehouse. |
|[AZ SQL Server AD-admin-lista](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-list) |Returnerar information om en Azure Active Directory administratör som för närvarande har kon figurer ATS för Azure SQL Server eller Azure SQL Data Warehouse. |
|[AZ SQL Server AD-Admin Update](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-update) |Uppdaterar Active Directory administratör för en Azure SQL-Server eller Azure SQL Data Warehouse. |

Mer information om CLI-kommandon finns i [SQL-AZ SQL](https://docs.microsoft.com/cli/azure/sql/server).  

## <a name="configure-your-client-computers"></a>Konfigurera klient datorerna

På alla klient datorer, från vilka dina program eller användare ansluter till Azure SQL Database eller Azure SQL Data Warehouse med Azure AD-identiteter, måste du installera följande program vara:

- .NET Framework 4,6 eller senare från [https://msdn.microsoft.com/library/5a4x27ek.aspx](https://msdn.microsoft.com/library/5a4x27ek.aspx).
- Azure Active Directory bibliotek för autentisering för SQL Server (**ADALSQL. DLL**) är tillgängligt på flera språk (både x86 och amd64) från Download Center på [Microsoft Active Directory-autentiseringsbibliotek för Microsoft SQL Server](https://www.microsoft.com/download/details.aspx?id=48742).

Du kan uppfylla dessa krav genom att:

- Att installera antingen [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) eller [SQL Server Data Tools för Visual Studio 2015](https://msdn.microsoft.com/library/mt204009.aspx) uppfyller kraven för .NET Framework 4,6.
- SSMS installerar x86-versionen av **ADALSQL. DLL**.
- SSDT installerar amd64-versionen av **ADALSQL. DLL**.
- Den senaste versionen av Visual Studio från [Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs) uppfyller .NET Framework 4,6-kravet, men installerar inte den nödvändiga amd64-versionen av **ADALSQL. DLL**.

## <a name="create-contained-database-users-in-your-database-mapped-to-azure-ad-identities"></a>Skapa inneslutna databas användare i databasen som har mappats till Azure AD-identiteter

>[!IMPORTANT]
>Hanterad instans har nu stöd för Azure AD server-Huvudkonton (inloggningar) (**offentlig för hands version**), vilket gör att du kan skapa inloggningar från Azure AD-användare,-grupper eller-program. Azure AD server-Huvudkonton (inloggningar) ger möjlighet att autentisera till din hanterade instans utan att databas användare måste skapas som en innesluten databas användare. Mer information finns i [Översikt över hanterade instanser](sql-database-managed-instance.md#azure-active-directory-integration). En syntax för att skapa Azure AD server-huvudobjekt (inloggningar) finns i <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">Skapa inloggning</a>.

Azure Active Directory-autentisering kräver att databasanvändare skapas som användare av oberoende databas. En användare av oberoende databas baserad på en Azure AD-identitet är en databasanvändare som inte har någon inloggning i huvuddatabasen, och som mappas till en identitet i den Azure AD-katalog som är associerad med databasen. Azure AD-identiteten kan vara antingen ett enskilt användarkonto eller en grupp. Mer information om användare av oberoende databas finns i avsnittet om [användare av oberoende databas – så gör du din databas portabel](https://msdn.microsoft.com/library/ff929188.aspx).

> [!NOTE]
> Databasanvändare (med undantag för administratörer) kan inte skapas med hjälp av Azure-portalen. RBAC-roller sprids inte till SQL Server, SQL Database eller SQL Data Warehouse. Azure RBAC-roller används för att hantera Azure-resurser och gäller inte för databasbehörigheter. Till exempel beviljar rollen **SQL Server-deltagare** inte åtkomst för att ansluta till SQL Database eller SQL Data Warehouse. Åtkomstbehörigheten måste beviljas direkt i databasen med hjälp av Transact-SQL-instruktioner.
> [!WARNING]
> Specialtecken som kolon `:` eller et-tecken `&` stöds inte när de ingår i användarnamn i instruktionerna T-SQL CREATE LOGIN och CREATE USER.

Om du vill skapa en Azure AD-baserad databas användare (förutom Server administratören som äger databasen) ansluter du till databasen med en Azure AD-identitet, som en användare med minst **ändra användar** behörighet. Använd sedan följande Transact-SQL-syntax:

```sql
CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
```

*Azure_AD_principal_name* kan vara User Principal Name av en Azure AD-användare eller visnings namnet för en Azure AD-grupp.

**Exempel:** Så här skapar du en innesluten databas användare som representerar en federerad eller hanterad domän användare i Azure AD:

```sql
CREATE USER [bob@contoso.com] FROM EXTERNAL PROVIDER;
CREATE USER [alice@fabrikam.onmicrosoft.com] FROM EXTERNAL PROVIDER;
```

Om du vill skapa en innesluten databas användare som representerar en Azure AD-eller federerad domän grupp anger du visnings namnet för en säkerhets grupp:

```sql
CREATE USER [ICU Nurses] FROM EXTERNAL PROVIDER;
```

Så här skapar du en innesluten databas användare som representerar ett program som ansluter med en Azure AD-token:

```sql
CREATE USER [appName] FROM EXTERNAL PROVIDER;
```

> [!TIP]
> Du kan inte direkt skapa en användare från en annan Azure Active Directory än Azure Active Directory som är associerad med din Azure-prenumeration. Medlemmar i andra Active-kataloger som importeras till användare i den associerade Active Directory (kallas externa användare) kan dock läggas till i en Active Directory grupp i klient Active Directory. Genom att skapa en innesluten databas användare för den AD-gruppen kan användarna från den externa Active Directory få till gång till SQL Database.

Mer information om hur du skapar inneslutna databas användare baserat på Azure Active Directory identiteter finns i [create User (Transact-SQL)](https://msdn.microsoft.com/library/ms173463.aspx).

> [!NOTE]
> Om du tar bort Azure Active Directory administratören för Azure SQL Server förhindras Azure AD-autentisering från att ansluta till servern. Vid behov kan oanvändbara Azure AD-användare släppas manuellt av en SQL Database administratör.
> [!NOTE]
> Om du får en **tids gräns för anslutningen**kan du behöva ange `TransparentNetworkIPResolution` parametern för anslutnings strängen till false. Mer information finns i [anslutnings tids gräns problem med .NET Framework 4.6.1-TransparentNetworkIPResolution](https://blogs.msdn.microsoft.com/dataaccesstechnologies/20../../connection-timeout-issue-with-net-framework-4-6-1-transparentnetworkipresolution/).

När du skapar en databas användare får användaren behörigheten **Connect** och kan ansluta till databasen som en medlem i den **offentliga** rollen. Först är de enda behörigheter som är tillgängliga för användaren alla behörigheter som tilldelas den **offentliga** rollen, eller alla behörigheter som beviljats till alla Azure AD-grupper som de är medlemmar i. När du har etablerat en Azure AD-baserad databas användare kan du ge användaren ytterligare behörigheter, på samma sätt som du beviljar behörighet till någon annan typ av användare. Ger vanligt vis behörighet till databas roller och lägga till användare i roller. Mer information finns i [grunderna för databas motor behörighet](https://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx). Mer information om särskilda SQL Database-roller finns [i hantera databaser och inloggningar i Azure SQL Database](sql-database-manage-logins.md).
Ett federerat domän användar konto som importeras till en hanterad domän som en extern användare måste använda den hanterade domän identiteten.

> [!NOTE]
> Azure AD-användare markeras i databasmetadata med typen E (EXTERNAL_USER) och för grupper med typen X (EXTERNAL_GROUPS). Mer information finns i [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx).
>

## <a name="connect-to-the-user-database-or-data-warehouse-by-using-ssms-or-ssdt"></a>Anslut till användar databasen eller informations lagret med hjälp av SSMS eller SSDT  

För att bekräfta att Azure AD-administratören är korrekt konfigurerad ansluter du till **huvud** databasen med hjälp av Azure AD-administratörskontot.
Om du vill etablera en Azure AD-baserad databas användare (förutom Server administratören som äger databasen) ansluter du till databasen med en Azure AD-identitet som har åtkomst till databasen.

> [!IMPORTANT]
> Stöd för Azure Active Directory-autentisering är tillgängligt med [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) och [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) i Visual Studio 2015. 2016-versionen från augusti innehåller även stöd för Active Directory Universal Authentication, som gör att administratörer kan kräva Multi-Factor Authentication med ett telefonsamtal, textmeddelande, smartkort med PIN-kod eller ett meddelande om mobilapp.

## <a name="using-an-azure-ad-identity-to-connect-using-ssms-or-ssdt"></a>Använda en Azure AD-identitet för att ansluta med SSMS eller SSDT

Följande procedurer visar hur du ansluter till en SQL-databas med en Azure AD-identitet med hjälp av SQL Server Management Studio eller SQL Server databas verktyg.

### <a name="active-directory-integrated-authentication"></a>Active Directory integrerad autentisering

Använd den här metoden om du är inloggad i Windows med dina Azure Active Directory autentiseringsuppgifter från en federerad domän.

1. Starta Management Studio eller data verktyg och välj **Active Directory-integrerad**i rutan **autentisering** i dialog rutan **Anslut till Server** (eller **Anslut till databas motor**). Inget lösen ord krävs eller kan anges eftersom dina befintliga autentiseringsuppgifter visas för anslutningen.

    ![Välj AD-integrerad autentisering][11]
2. Välj knappen **alternativ** och ange namnet på den användar databas som du vill ansluta till i rutan **Anslut till databas** på sidan **anslutnings egenskaper** . ( **AD-domännamnet eller klient-ID: t**stöds bara för **UNIVERSELLa med MFA-anslutnings** alternativ, annars är det nedtonad.)  

    ![Välj databas namnet][13]

## <a name="active-directory-password-authentication"></a>Autentisering av Active Directory-lösenord

Använd den här metoden vid anslutning med ett huvud namn för Azure AD med hjälp av den hanterade Azure AD-domänen. Du kan också använda den för federerade konton utan åtkomst till domänen, till exempel när du arbetar fjärran slutet.

Använd den här metoden för att autentisera till SQL DB/DW med Azure AD för interna eller federerade Azure AD-användare. En inbyggd användare skapas explicit i Azure AD och autentiseras med hjälp av användar namn och lösen ord, medan en federerad användare är en Windows-användare vars domän är federerad med Azure AD. Den senare metoden (med hjälp av användar & lösen ord) kan användas när en användare vill använda sina Windows-autentiseringsuppgifter, men den lokala datorn är inte ansluten till domänen (till exempel med hjälp av fjärråtkomst). I det här fallet kan en Windows-användare ange sitt domän konto och lösen ord och kan autentisera till SQL DB/DW med federerade autentiseringsuppgifter.

1. Starta Management Studio eller data verktyg och i dialog rutan **Anslut till Server** (eller **Anslut till databas motor**) i rutan **autentisering** väljer du **Active Directory-Password**.
2. I rutan **användar namn** skriver du ditt Azure Active Directory användar namn i formatet **användar\@namn domain.com**. Användar namn måste vara ett konto från Azure Active Directory eller ett konto från en domän Federer med Azure Active Directory.
3. I rutan **lösen ord** skriver du ditt användar lösen ord för det Azure Active Directory kontot eller det federerade domän kontot.

    ![Välj AD Password Authentication][12]
4. Välj knappen **alternativ** och ange namnet på den användar databas som du vill ansluta till i rutan **Anslut till databas** på sidan **anslutnings egenskaper** . (Se bilden i föregående alternativ.)

## <a name="using-an-azure-ad-identity-to-connect-from-a-client-application"></a>Använda en Azure AD-identitet för att ansluta från ett klient program

Följande procedurer visar hur du ansluter till en SQL-databas med en Azure AD-identitet från ett klient program.

### <a name="active-directory-integrated-authentication"></a>Active Directory integrerad autentisering

Om du vill använda integrerad Windows-autentisering måste din domäns Active Directory vara federerad med Azure Active Directory. Klient programmet (eller en tjänst) som ansluter till databasen måste köras på en domänansluten dator under användarens domänautentiseringsuppgifter.

Om du vill ansluta till en databas med integrerad autentisering och en Azure AD-identitet måste nyckelordet Authentication i databas anslutnings strängen anges till Active Directory integrerad. I följande C# kod exempel används ADO .net.

```csharp
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated; Initial Catalog=testdb;";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

Nyckelordet ``Integrated Security=True`` för anslutnings strängen stöds inte för anslutning till Azure SQL Database. När du skapar en ODBC-anslutning måste du ta bort blank steg och ange autentiseringen till ' ActiveDirectoryIntegrated '.

### <a name="active-directory-password-authentication"></a>Autentisering av Active Directory-lösenord

Om du vill ansluta till en databas med integrerad autentisering och en Azure AD-identitet måste nyckelordet autentisering anges till Active Directory lösen ord. Anslutnings strängen måste innehålla användar-ID/UID och lösen ord/PWD-nyckelord och-värden. I följande C# kod exempel används ADO .net.

```csharp
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Password; Initial Catalog=testdb;  UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

Lär dig mer om autentiseringsmetoder för Azure AD med hjälp av demonstrations kod exemplen som finns på [Azure AD Authentication GitHub demo](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth).

## <a name="azure-ad-token"></a>Azure AD-token

Med den här autentiseringsmetoden kan tjänster på mellan nivå ansluta till Azure SQL Database eller Azure SQL Data Warehouse genom att hämta en token från Azure Active Directory (AAD). Det möjliggör avancerade scenarier, inklusive certifikatbaserad autentisering. Du måste utföra fyra grundläggande steg för att använda Azure AD-token-autentisering:

1. Registrera ditt program med Azure Active Directory och hämta klient-ID för din kod.
2. Skapa en databas användare som representerar programmet. (Slutfördes tidigare i steg 6.)
3. Skapa ett certifikat på klient datorn kör programmet.
4. Lägg till certifikatet som en nyckel för ditt program.

Exempel på anslutnings sträng:

```csharp
string ConnectionString =@"Data Source=n9lxnyuzhv.database.windows.net; Initial Catalog=testdb;"
SqlConnection conn = new SqlConnection(ConnectionString);
conn.AccessToken = "Your JWT token"
conn.Open();
```

Mer information finns i [SQL Server Security Blogg](https://blogs.msdn.microsoft.com/sqlsecurity/20../../token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth/). Information om hur du lägger till ett certifikat finns [i komma igång med certifikatbaserad autentisering i Azure Active Directory](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md).

### <a name="sqlcmd"></a>sqlcmd

Följande instruktioner är Anslut med version 13,1 av SQLCMD, som är tillgänglig från [Download Center](https://go.microsoft.com/fwlink/?LinkID=825643).

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
