---
title: Konfigurera Azure Active Directory - SQL-autentisering | Microsoft Docs
description: Lär dig hur du ansluter till SQL-databas, hanterade-instans och SQL Data Warehouse med hjälp av Azure Active Directory-autentisering - när du har konfigurerat Azure AD.
services: sql-database
author: GithubMirek
manager: craigg
ms.service: sql-database
ms.custom: security
ms.topic: conceptual
ms.date: 03/07/2018
ms.author: mireks
ms.openlocfilehash: 9a0cb3d69cd161a409d0a035be783bb255a83036
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34644373"
---
# <a name="configure-and-manage-azure-active-directory-authentication-with-sql-database-managed-instance-or-sql-data-warehouse"></a>Konfigurera och hantera Azure Active Directory-autentisering med SQL-databas, hanteras instans eller SQL Data Warehouse

Den här artikeln visar hur du skapar och fylla i Azure AD och sedan använda Azure AD med Azure SQL Database och SQL Data Warehouse. En översikt finns [Azure Active Directory Authentication](sql-database-aad-authentication.md).

>  [!NOTE]  
>  Ansluter till SQL Server körs på en Azure VM stöds inte med ett Azure Active Directory-konto. Använd en domän Active Directory-konto i stället.

## <a name="create-and-populate-an-azure-ad"></a>Skapa och fylla i en Azure AD
Skapa en Azure AD och fylla det med användare och grupper. Azure AD kan vara den första Azure AD hanterad domän. Azure AD kan också vara en lokal Active Directory Domain Services som är federerat med Azure AD.

Mer information finns i [Integrera dina lokala identiteter med Azure Active Directory](../active-directory/active-directory-aadconnect.md), [Add your own domain name to Azure AD](../active-directory/active-directory-domains-add-azure-portal.md) (Lägga till dina egna domännamn i Azure AD), [Microsoft Azure now supports federation with Windows Server Active Directory](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/) (Microsoft stöder nu federation med Windows Server Active Directory), [Administering your Azure AD directory](../active-directory/active-directory-administer.md) (Administrera Azure Active Directory), [Manage Azure AD using Windows PowerShell](/powershell/azure/overview?view=azureadps-2.0) (Hantera Azure AD med Windows PowerShell) och [Hybrid Identity Required Ports and Protocols](..//active-directory/connect/active-directory-aadconnect-ports.md) (Portar och protokoll som krävs för hybrididentiet).

## <a name="associate-or-add-an-azure-subscription-to-azure-active-directory"></a>Koppla eller lägga till en Azure-prenumeration i Azure Active Directory

1. Koppla dina Azure-prenumeration i Azure Active Directory genom att göra katalogen som en betrodd katalog för Azure-prenumerationen värd för databasen. Mer information finns i [hur Azure-prenumerationer är associerade med Azure AD](../active-directory/active-directory-how-subscriptions-associated-directory.md).
2. Använd directory switcher i Azure-portalen för att växla till prenumerationen som är kopplade till domänen.

   **Ytterligare information:** var Azure-prenumerationen har en förtroenderelation med en Azure AD-instans. Det innebär att den litar på den katalogen för att autentisera användare, tjänster och enheter. Flera prenumerationer kan lita på samma katalog, men en prenumeration litar bara på en katalog. Den här förtroenderelationen mellan en prenumeration och en katalog skiljer sig från relationen mellan en prenumeration och alla andra resurser i Azure (webbplatser, databaser och så vidare), som är mer som underordnade resurser till en prenumeration. Om en prenumeration går ut stoppas även åtkomsten till de resurser som är associerade med prenumerationen. Men katalogen finns kvar i Azure och du kan associera en annan prenumeration med katalogen och fortsätta hantera kataloganvändarna. Mer information om resurser finns [förstå resursåtkomst i Azure](../active-directory/active-directory-b2b-admin-add-users.md). Läs mer om det här betrodd relation finns [hur koppla eller lägga till en Azure-prenumeration i Azure Active Directory](../active-directory/active-directory-how-subscriptions-associated-directory.md).

## <a name="create-an-azure-ad-administrator-for-azure-sql-server"></a>Skapa en Azure AD-administratör för Azure SQL server
Varje Azure SQL-server (som värd för en SQL Database eller SQL Data Warehouse) börjar med ett administratörskonto för enskild server som är administratör i hela Azure SQL-servern. En andra SQL Server-administratören måste skapas, som är en Azure AD-kontot. Detta säkerhetsobjekt skapas som en innesluten databasanvändare i master-databasen. Som administratör, server-administratörskonton som är medlemmar i den **db_owner** roll i varje användare databasen och ange varje användardatabas som den **dbo** användare. Mer information om server-administratörskonton finns [hantera databaser och inloggningar i Azure SQL Database](sql-database-manage-logins.md).

När du använder Azure Active Directory med geo-replikering, måste Azure Active Directory-administratören konfigureras för både primärt och sekundära servrar. Om en server inte har en Azure Active Directory-administratör, sedan får Azure Active Directory-inloggningar och användare ett ”det går inte att ansluta” till server-fel.

> [!NOTE]
> Användare som inte är baserade på en Azure AD-kontot (inklusive Azure SQL server-administratörskontot) kan inte skapa Azure AD-baserade användare, eftersom de inte har behörighet att validera föreslagna användare med Azure AD.
> 

## <a name="provision-an-azure-active-directory-administrator-for-your-managed-instance"></a>Etablera en Azure Active Directory-administratör för din hanterade instans

> [!IMPORTANT]
> Följ dessa steg bara om du etablerar en hanterad instans. Den här åtgärden kan endast utföras av Global/företagets administratör i Azure AD. Följande steg beskriver processen för att bevilja behörigheter för användare med olika behörigheter i katalogen.

Din hanteras instans behöver behörighet att läsa Azure AD för att kunna utföra aktiviteter, till exempel autentisering av användare via medlemskap i säkerhetsgrupper eller skapa nya användare. För detta ska fungera måste du bevilja behörigheter till hanterade instans att läsa Azure AD. Det finns två sätt att göra det: från portalen och PowerShell. Följande steg båda metoderna.

1. Välj din anslutning till listrutan en lista över möjliga Active kataloger i Azure-portalen i det övre högra hörnet. 
2. Välja rätt Active Directory som standard Azure AD. 

   Det här steget länkar prenumerationen som är associerade med Active Directory med hanterade instans att se till att samma prenumeration används för både Azure AD och hanteras-instans.
3. Navigera till hanterade instansen och välja en som du vill använda för Azure AD-integrering.

   ![aad](./media/sql-database-aad-authentication/aad.png)

4.  Välj på banderoll ovanpå Administrationssida för Active Directory. Om du är inloggad som Global/företagets administratör i Azure AD kan göra du det från Azure-portalen eller med hjälp av PowerShell.

    ![bevilja behörigheter-portal](./media/sql-database-aad-authentication/grant-permissions.png)

    ![bevilja behörigheter powershell](./media/sql-database-aad-authentication/grant-permissions-powershell.png)
    
    Om du är inloggad som Global/företagets administratör i Azure AD, kan du göra det från Azure-portalen eller köra ett PowerShell-skript.

5. När åtgärden har slutförts, visas följande meddelande i övre högra hörnet:

    ![lyckades](./media/sql-database-aad-authentication/success.png)

6.  Du kan nu välja din Azure AD-administratör för din hanterade instans. För att på sidan Active Directory-administratören välja **ange admin** kommando.

    ![set-admin](./media/sql-database-aad-authentication/set-admin.png)

7. Lägg till administratör sidan Sök efter en användare väljer användaren eller gruppen vara administratör och välj sedan **Välj**. 

   Sidan Active Directory visas alla medlemmar och grupper i Active Directory. Användare eller grupper som är nedtonade kan inte väljas eftersom de inte stöds som Azure AD-administratörer. Visa en lista över stöds administratörer i [Azure AD-funktioner och begränsningar](sql-database-aad-authentication.md#azure-ad-features-and-limitations). Rollbaserad åtkomstkontroll (RBAC) gäller bara för Azure-portalen och sprids inte till SQL Server.

    ![Lägg till administratör](./media/sql-database-aad-authentication/add-admin.png)

8. Överst på sidan Active Directory, Välj **spara**.

    ![spara](./media/sql-database-aad-authentication/save.png)

    Processen att ändra administratören kan ta några minuter. Den nya administratören visas sedan i rutan Active Directory-administratör.

> [!IMPORTANT]
> När du konfigurerar Azure AD-administratör kan inte nya admin-namnet (användare eller grupp) redan finnas i virtuella master-databasen som en användare för SQL Server-autentisering. Om den finns, misslyckas installationen för Azure AD-administratör och återställer den skapades, vilket betyder att sådana administratör (namn) redan finns. Alla arbete för att ansluta till servern med hjälp av Azure AD-autentisering misslyckas eftersom SQL Server-autentisering användaren inte är en del av Azure AD.

> [!TIP]
> För att ta bort en administratör överst på sidan för Active Directory-administratör senare väljer **ta bort admin**, och välj sedan **spara**.
 
## <a name="provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server"></a>Etablera en Azure Active Directory-administratör för din Azure SQL Database-server

> [!IMPORTANT]
> Följ dessa steg bara om du etablerar en Azure SQL Database-server eller ett datalager.

Följande två procedurer visar hur du etablerar en Azure Active Directory-administratör för din Azure SQL-server i Azure-portalen och med hjälp av PowerShell.

### <a name="azure-portal"></a>Azure Portal
1. I den [Azure-portalen](https://portal.azure.com/), Välj din anslutning vill se en lista över möjliga Active kataloger i det övre högra hörnet. Välja rätt Active Directory som standard Azure AD. Det här steget länkar prenumeration-associerade Active Directory med Azure SQL server att se till att samma prenumeration används för både Azure AD och SQL Server. (Azure SQL-server kan vara värd för Azure SQL Database eller Azure SQL Data Warehouse.)   
    ![Välj ad][8]   
    
2. Välj i den vänstra banderollen **alla tjänster**, och i filtertypen **SQLServer**. Välj **Sql-servrar**. 

    ![sqlservers.PNG](media/sql-database-aad-authentication/sqlservers.png)    

    >[!NOTE]
    > På den här sidan innan du väljer **SQL-servrar**, kan du välja den **stjärna** bredvid namnet *favorit* kategori och Lägg till **SQL-servrar**till det vänstra navigeringsfältet. 

1. På **SQL Server** väljer **Active Directory-administratör**.   
2. I den **Active Directory-administratör** väljer **ange admin**.   
    ![Välj active directory](./media/sql-database-aad-authentication/select-active-directory.png)  
    
4. I den **lägga till administratören** sidan, söka efter en användare, Välj användaren eller grupp för en administratör och välj sedan **Välj**. (Sidan Active Directory visas alla medlemmar och grupper i Active Directory. Användare eller grupper som är nedtonade kan inte väljas eftersom de inte stöds som Azure AD-administratörer. (Se listan över administratörer som stöds i den **Azure AD-funktioner och begränsningar** avsnitt i [Använd Azure Active Directory-autentisering för autentisering med SQL Database eller SQL Data Warehouse](sql-database-aad-authentication.md).) Rollbaserad åtkomstkontroll (RBAC) gäller enbart för portalen och sprids inte till SQL Server.   
    ![Välj admin](./media/sql-database-aad-authentication/select-admin.png)  
    
5. Längst upp i den **Active Directory-administratör** väljer **spara**.   
    ![Spara admin](./media/sql-database-aad-authentication/save-admin.png)   

Processen att ändra administratören kan ta några minuter. Sedan den nya administratören visas i den **Active Directory-administratör** rutan.

   > [!NOTE]
   > När du konfigurerar Azure AD-administratör kan inte nya admin-namnet (användare eller grupp) redan finnas i virtuella master-databasen som en användare för SQL Server-autentisering. Om den finns, misslyckas installationen för Azure AD admin; återställer skapades och som anger att sådana administratör (namn) redan finns. Alla arbete för att ansluta till servern med hjälp av Azure AD-autentisering misslyckas eftersom SQL Server-autentisering användaren inte är en del av Azure AD.
   > 


Ta bort senare administratör överst i den **Active Directory-administratör** väljer **ta bort admin**, och välj sedan **spara**.

### <a name="powershell"></a>PowerShell
Om du vill köra PowerShell-cmdlets som du behöver ha Azure PowerShell installerade och körs. Mer information finns i [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview).

Kör följande Azure PowerShell-kommandon för att etablera en Azure AD-administratör:

* Ansluta AzureRmAccount
* Select-AzureRmSubscription

Cmdlets som används för att etablera och hantera Azure AD-administratör:

| Cmdlet-namn | Beskrivning |
| --- | --- |
| [Set-AzureRmSqlServerActiveDirectoryAdministrator](/powershell/module/azurerm.sql/set-azurermsqlserveractivedirectoryadministrator) |Etablerar en Azure Active Directory-administratör för Azure SQL server eller Azure SQL Data Warehouse. (Måste vara från aktuell prenumeration.) |
| [Remove-AzureRmSqlServerActiveDirectoryAdministrator](/powershell/module/azurerm.sql/remove-azurermsqlserveractivedirectoryadministrator) |Tar bort en Azure Active Directory-administratör för Azure SQL server eller Azure SQL Data Warehouse. |
| [Get-AzureRmSqlServerActiveDirectoryAdministrator](/powershell/module/azurerm.sql/get-azurermsqlserveractivedirectoryadministrator) |Returnerar information om en administratör för Azure Active Directory som har konfigurerats för Azure SQL server- eller Azure SQL Data Warehouse. |

Använd PowerShell-kommandot get-help för att se information om dessa kommandon, till exempel ``get-help Set-AzureRmSqlServerActiveDirectoryAdministrator``.

Följande skript tillhandahåller en administratör Azure AD-grupp med namnet **DBA_Group** (objekt-id `40b79501-b343-44ed-9ce7-da4c8cc7353f`) för den **demo_server** server i en resursgrupp med namnet **grupp-23**:

```
Set-AzureRmSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23"
-ServerName "demo_server" -DisplayName "DBA_Group"
```

Den **DisplayName** Indataparametern accepterar antingen Azure AD namn eller UPN-namnet. Till exempel ``DisplayName="John Smith"`` och ``DisplayName="johns@contoso.com"``. Visningsnamnet är stöd för Azure AD-grupper bara Azure AD.

> [!NOTE]
> Azure PowerShell-kommandot ```Set-AzureRmSqlServerActiveDirectoryAdministrator``` förhindrar inte att du etablerar Azure AD-administratörer för användare som inte stöds. En användare som inte stöds kan etableras, men kan inte ansluta till en databas. 
> 

I följande exempel används det valfria **ObjectID**:

```
Set-AzureRmSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23"
-ServerName "demo_server" -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
```

> [!NOTE]
> Azure AD **ObjectID** är obligatorisk när den **DisplayName** är inte unikt. Att hämta den **ObjectID** och **DisplayName** värden, Använd Active Directory-avsnittet i klassiska Azure-portalen och visa egenskaperna för en användare eller grupp.
> 

I följande exempel returnerar information om aktuellt Azure AD-administratör för Azure SQL server:

```
Get-AzureRmSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" | Format-List
```

I följande exempel tar bort en Azure AD-administratör:

```
Remove-AzureRmSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server"
```

Du kan också etablera ett Azure Active Directory-administratör med hjälp av REST-API: er. Mer information finns i [Service Management REST API-referens och åtgärder för Azure SQL-databaser för Azure SQL-databaser](https://msdn.microsoft.com/library/azure/dn505719.aspx)

### <a name="cli"></a>CLI  
Du kan också etablera en Azure AD-administratör genom att anropa följande CLI-kommandon:
| Kommando | Beskrivning |
| --- | --- |
|[Skapa AZ sql server ad-administratör](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_create) |Etablerar en Azure Active Directory-administratör för Azure SQL server eller Azure SQL Data Warehouse. (Måste vara från aktuell prenumeration.) |
|[ta bort AZ sql server ad-administratör](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_delete) |Tar bort en Azure Active Directory-administratör för Azure SQL server eller Azure SQL Data Warehouse. |
|[AZ sql server ad-admin-lista](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_list) |Returnerar information om en administratör för Azure Active Directory som har konfigurerats för Azure SQL server- eller Azure SQL Data Warehouse. |
|[AZ sql server ad-admin-uppdatering](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_update) |Uppdaterar Active Directory-administratör för en Azure SQL-server eller Azure SQL Data Warehouse. |

Mer information om CLI-kommandon finns [SQL - az sql](https://docs.microsoft.com/cli/azure/sql/server).  


## <a name="configure-your-client-computers"></a>Konfigurera klientdatorer
På alla klientdatorer som dina program eller användare ansluta till Azure SQL Database eller Azure SQL Data Warehouse med hjälp av Azure AD identiteter, måste du installera följande programvara:

* .NET framework 4.6 eller senare från [ https://msdn.microsoft.com/library/5a4x27ek.aspx ](https://msdn.microsoft.com/library/5a4x27ek.aspx).
* Azure Active Directory Authentication Library för SQLServer (**ADALSQL. DLL-filen**) är tillgängligt på flera språk (x86 och amd64) från download center på [Microsoft Active Directory Authentication Library för Microsoft SQL Server](http://www.microsoft.com/download/details.aspx?id=48742).

Du kan uppfylla dessa krav genom att:

* Installera antingen [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) eller [SQL Server Data Tools för Visual Studio 2015](https://msdn.microsoft.com/library/mt204009.aspx) uppfyller .NET Framework 4.6.
* SSMS installerar x86 versionen av **ADALSQL. DLL-filen**.
* SSDT installeras amd64-versionen av **ADALSQL. DLL-filen**.
* Senaste Visual Studio från [Visual Studio-hämtningar](https://www.visualstudio.com/downloads/download-visual-studio-vs) uppfyller .NET Framework 4.6, men inte installera den nödvändiga amd64-versionen av **ADALSQL. DLL-filen**.

## <a name="create-contained-database-users-in-your-database-mapped-to-azure-ad-identities"></a>Skapa oberoende databasanvändare i din databas som mappats till Azure AD identiteter

Azure Active Directory-autentisering kräver databasanvändare som ska skapas som oberoende databasanvändare. En innesluten databasanvändare baserat på en Azure AD-identitet är en databasanvändare som inte har en inloggning i master-databasen och som mappar till en identitet i Azure AD-katalog som är kopplad till databasen. Azure AD identity kan vara ett eget användarkonto eller en grupp. Läs mer om oberoende databasanvändare [innehöll databasanvändare-göra din databas bärbara](https://msdn.microsoft.com/library/ff929188.aspx).

> [!NOTE]
> Databasanvändare (med undantag för administratörer) kan inte skapas med hjälp av Azure portal. RBAC-roller sprids inte till SQL Server, SQL Database eller SQL Data Warehouse. Azure RBAC-roller som används för att hantera Azure-resurser och gäller inte för databasbehörighet. Till exempel den **SQL Server-deltagare** rollen ger inte behörighet att ansluta till SQL Database eller SQL Data Warehouse. Ha måste beviljats behörigheten åtkomst direkt i databasen med hjälp av Transact-SQL-uttryck.
>

Skapa en Azure AD-baserad finns databasanvändare (andra än serveradministratören som äger databasen), ansluta till databasen med en Azure AD-identitet som en användare med minst **ALTER ANY USER** behörighet. Sedan använder du följande Transact-SQL-syntax:

```
CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
```

*Azure_AD_principal_name* kan vara användarens huvudnamn för en Azure AD-användare eller visningsnamnet för en Azure AD-grupp.

**Exempel:** för att skapa en innesluten databas användaren som representerar en Azure AD federerade eller hanterade domänanvändare:
```
CREATE USER [bob@contoso.com] FROM EXTERNAL PROVIDER;
CREATE USER [alice@fabrikam.onmicrosoft.com] FROM EXTERNAL PROVIDER;
```

Ange visningsnamnet för en säkerhetsgrupp om du vill skapa en innesluten databasanvändare som representerar en Azure AD eller externa domängrupp:
```
CREATE USER [ICU Nurses] FROM EXTERNAL PROVIDER;
```

Skapa en innesluten databasanvändare som representerar ett program som ansluter med hjälp av en Azure AD-token:

```
CREATE USER [appName] FROM EXTERNAL PROVIDER;
```

>  [!TIP]
>  Du kan inte direkt skapa en användare från en Azure Active Directory än Azure Active Directory som är associerad med din Azure-prenumeration. Medlemmar i andra Active kataloger som är importerade användare i den associerade Active Directory (kallas externa användare) går att lägga till en Active Directory-grupp i Active Directory-klient. Genom att skapa en innesluten databasanvändare för den AD-gruppen, kan användare från externa Active Directory får tillgång till SQL-databas.   

Mer information om hur du skapar innehåller databasen användare baserat på Azure Active Directory identiteter, se [skapa användare (Transact-SQL)](http://msdn.microsoft.com/library/ms173463.aspx).

> [!NOTE]
> Tar bort Azure Active Directory-administratör för Azure SQL-servern förhindrar att alla Azure AD authentication-användare ansluter till servern. Vid behov går inte att använda Azure AD-användare kan tas bort manuellt av en administratör för SQL-databas.   

>  [!NOTE]
>  Om du får en **tidsgränsen uppnåddes för anslutningen**, du kan behöva ange den `TransparentNetworkIPResolution` parametern för anslutningssträngen till false. Mer information finns i [timeout anslutningsproblem med .NET Framework 4.6.1 - TransparentNetworkIPResolution](https://blogs.msdn.microsoft.com/dataaccesstechnologies/2016/05/07/connection-timeout-issue-with-net-framework-4-6-1-transparentnetworkipresolution/).   

   
När du skapar en databasanvändare som användare får den **Anslut** behörighet och kan ansluta till databasen som en medlem i den **offentliga** roll. Ursprungligen endast behörigheter som är tillgängliga för användaren är alla behörigheter som tilldelats den **offentliga** , eller alla behörigheter som beviljas åtkomst till alla Azure AD-grupper att de är medlem i. När du etablerar en Azure AD-baserad finns databasanvändare bevilja du användaren ytterligare behörigheter på samma sätt som du vill bevilja behörighet till alla andra typer av användare. Normalt bevilja behörighet till databasroller och lägga till användare till roller. Mer information finns i [Database Engine behörighet grunderna](http://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx). Mer information om den speciella SQL-databas roller finns [hantera databaser och inloggningar i Azure SQL Database](sql-database-manage-logins.md).
En federerad domänanvändarkonto som importeras till en hanterad domän som en extern användare måste använda den hanterade domän identitet.

> [!NOTE]
> Azure AD-användare har markerats i databasmetadata för med typ E (EXTERNAL_USER) och för grupper med typen X (EXTERNAL_GROUPS). Mer information finns i [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx). 
>

## <a name="connect-to-the-user-database-or-data-warehouse-by-using-ssms-or-ssdt"></a>Ansluta till databasen eller data warehouse för användare med hjälp av SSMS eller SSDT  
För att bekräfta att Azure AD-administratör har konfigurerats korrekt, ansluta till den **master** databasen med hjälp av Azure AD-administratörskonto.
Ansluta till databasen med en Azure AD-identitet som har åtkomst till databasen för att etablera en Azure AD-baserad finns databasanvändare (andra än serveradministratören som äger databasen).

> [!IMPORTANT]
> Stöd för Azure Active Directory-autentisering finns i [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) och [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) i Visual Studio 2015. Augusti 2016-versionen av SSMS finns också stöd för Active Directory Universal autentisering, vilket gör att administratörer kan kräva Multi-Factor Authentication med ett telefonsamtal, textmeddelande, smartkort och PIN-kod eller meddelande i mobilappen.
 
## <a name="using-an-azure-ad-identity-to-connect-using-ssms-or-ssdt"></a>Använda en Azure AD-identitet för att ansluta med hjälp av SSMS eller SSDT  

Följande procedurer visar hur du ansluter till en SQL-databas med en Azure AD-identitet med hjälp av SQL Server Management Studio eller verktyg för SQL Server-databasen.

### <a name="active-directory-integrated-authentication"></a>Active Directory-integrerad autentisering

Använd den här metoden om du är inloggad Windows med Azure Active Directory-autentiseringsuppgifter från en federerad domän.

1. Starta Management Studio eller Dataverktyg och i den **Anslut till Server** (eller **Anslut till databasmotor**) i dialogrutan den **autentisering** väljer  **Active Directory - integrerade**. Inget lösenord krävs eller kan anges eftersom din befintliga autentiseringsuppgifter kommer att visas för anslutningen.   

    ![Välj AD-integrerad autentisering][11]
2. Välj den **alternativ** knappen, och på den **anslutningsegenskaper** sidan den **Anslut till databas** skriver du namnet på den du vill ansluta till databasen. (Den **AD domän namn eller klient-ID**”alternativet stöds bara för **Universal med MFA anslutning** alternativ, annars den är nedtonat.)  

    ![Välj databasnamnet][13]

## <a name="active-directory-password-authentication"></a>Autentisering av Active Directory-lösenord

Använd den här metoden när du ansluter med en Azure AD-huvudnamn med hjälp av Azure AD hanterade domän. Du kan också använda den för federerade konton utan att ha tillgång till domänen, till exempel när du arbetar via fjärranslutning.

Använd den här metoden för att autentisera till SQL DB/DW med Azure AD för intern av externa Azure AD-användare.
En intern användare är en explicit skapats i Azure AD och som autentiseras med hjälp av användarnamn och lösenord, när en federerad användare är en Windows-användare vars domän är federerat med Azure AD. Den andra metoden (med användare och lösenord) kan användas när en användare vill använda sina windows-autentiseringsuppgifter, men den lokala datorn inte är ansluten med domänen (d.v.s. med en RAS). I det här fallet en Windows-användare kan ange sina domänkontot och lösenord och kan autentiseras till SQL DB/DW med federerad autentiseringsuppgifter.

1. Starta Management Studio eller Dataverktyg och i den **Anslut till Server** (eller **Anslut till databasmotor**) i dialogrutan den **autentisering** väljer  **Active Directory - lösenord**.
2. I den **användarnamn** Skriv ditt Azure Active Directory-användarnamn i formatet **username@domain.com**. Det här måste vara ett konto från Azure Active Directory eller ett konto från en domän federera med Azure Active Directory.
3. I den **lösenord** Skriv ditt användarnamn lösenord för Azure Active Directory-konto eller federerad domänkonto.

    ![Välj autentisering av AD-lösenord][12]
4. Välj den **alternativ** knappen, och på den **anslutningsegenskaper** sidan den **Anslut till databas** skriver du namnet på den du vill ansluta till databasen. (Se bilden i föregående alternativ.)

## <a name="using-an-azure-ad-identity-to-connect-from-a-client-application"></a>Använda en Azure AD-identitet för att ansluta från ett klientprogram

Följande procedurer visar hur du ansluter till en SQL-databas med en Azure AD-identitet från ett klientprogram.

###  <a name="active-directory-integrated-authentication"></a>Active Directory-integrerad autentisering

Om du vill använda integrerad Windows-autentisering, att Active Directory för din domän federerade med Azure Active Directory. Ditt klientprogram (eller en tjänst) att ansluta till databasen måste köras på en domänansluten dator under en användares domänautentiseringsuppgifter.

För att ansluta till en databas med integrerad autentisering och en Azure AD-identitet, måste autentisering-nyckelord i anslutningssträngen för databasen anges till Active Directory Integrated. Följande C# kodexempel använder ADO .NET.

```
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated; Initial Catalog=testdb;";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

Nyckelordet för anslutningssträngen ``Integrated Security=True`` stöds inte för att ansluta till Azure SQL Database. När du gör en ODBC-anslutning, behöver du ta bort blanksteg och ange autentisering till 'ActiveDirectoryIntegrated'.

### <a name="active-directory-password-authentication"></a>Autentisering av Active Directory-lösenord

Nyckelordet autentisering måste anges till Active Directory-lösenord för att ansluta till en databas med integrerad autentisering och en Azure AD-identitet. Anslutningssträngen måste innehålla användar-ID: T/UID och lösenord/PWD nyckelord och värden. Följande C# kodexempel använder ADO .NET.

```
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Password; Initial Catalog=testdb;  UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

Lär dig mer om Azure AD-autentiseringsmetoder som använder demo kodexempel finns på [Azure AD Authentication GitHub Demo](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth).

## <a name="azure-ad-token"></a>Azure AD-token
Den här autentiseringsmetoden tillåter mellannivå tjänster att ansluta till Azure SQL Database eller Azure SQL Data Warehouse genom att hämta en token från Azure Active Directory (AAD). Det gör det möjligt för avancerade scenarier inklusive certifikatbaserad autentisering. Du måste slutföra fyra grundläggande steg för att använda Azure AD-tokenautentisering:

1. Registrera ditt program med Azure Active Directory och hämta klient-id för din kod. 
2. Skapa en databasanvändare som representerar programmet. (Utföra tidigare i steg 6).
3. Skapa ett certifikat på klienten datorn kör programmet.
4. Lägga till certifikatet som en nyckel för ditt program.

Exempel anslutningssträng:

```
string ConnectionString =@"Data Source=n9lxnyuzhv.database.windows.net; Initial Catalog=testdb;"
SqlConnection conn = new SqlConnection(ConnectionString);
connection.AccessToken = "Your JWT token"
conn.Open();
```

Mer information finns i [SQL Server-Säkerhetsblogg](https://blogs.msdn.microsoft.com/sqlsecurity/2016/02/09/token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth/). Information om att lägga till ett certifikat finns i [komma igång med certifikatbaserad autentisering i Azure Active Directory](../active-directory/active-directory-certificate-based-authentication-get-started.md).

### <a name="sqlcmd"></a>sqlcmd

Följande instruktioner ansluta med version 13,1 av sqlcmd som är tillgängliga från den [Download Center](http://go.microsoft.com/fwlink/?LinkID=825643).

```
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

