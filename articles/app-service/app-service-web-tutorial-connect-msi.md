---
title: Skydda SQL Database-anslutningar med en hanterad identitet – Azure App Service | Microsoft Docs
description: Lär dig hur du gör databas anslutningen säkrare med hjälp av en hanterad identitet och hur du tillämpar den på andra Azure-tjänster.
services: app-service\web
documentationcenter: dotnet
author: cephalin
manager: syntaxc4
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 09/16/2019
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: b39c1596dd16f8ec6235878abdbf37492abd1ea8
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177075"
---
# <a name="tutorial-secure-azure-sql-database-connection-from-app-service-using-a-managed-identity"></a>Självstudie: Säkra Azure SQL Database-anslutningar från App Service med en hanterad identitet

Med [App Service ](overview.md) får du en automatiskt uppdaterad webbvärdtjänst i Azure med hög skalbarhet. Dessutom får du en [hanterad identitet](overview-managed-identity.md) för din app. Det här är en användningsklar lösning som skyddar åtkomsten till [Azure SQL Database](/azure/sql-database/) och andra Azure-tjänster. Med hanterade identiteter i App Service blir dina appar säkrare eftersom du inte har några hemligheter i dina appar. Du har till exempel inga inloggningsuppgifter i anslutningssträngarna. I den här självstudien lägger du till hanterad identitet till den webbapp som du skapade i någon av följande själv studie kurser: 

- [Självstudie: Bygg en ASP.NET-app i Azure med SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md)
- [Självstudie: Bygg en ASP.NET Core-och SQL Database-app i Azure App Service](app-service-web-tutorial-dotnetcore-sqldb.md)

När du är färdig ansluter exempelappen säkert till SQL Database utan att du behöver använda användarnamn och lösenord.

> [!NOTE]
> De steg som beskrivs i den här självstudien har stöd för följande versioner:
> 
> - .NET Framework 4.7.2 och senare.
> - .NET Core 2,2 och senare.
>

Vad du kommer att lära dig:

> [!div class="checklist"]
> * Aktivera hanterade identiteter
> * Ge SQL Database åtkomst till den hanterade identiteten
> * Konfigurera Entity Framework att använda Azure AD-autentisering med SQL Database
> * Ansluta till SQL Database från Visual Studio med Azure AD-autentisering

> [!NOTE]
>Azure AD-autentisering _skiljer sig_ från [integrerad Windows-autentisering](/previous-versions/windows/it-pro/windows-server-2003/cc758557(v=ws.10)) i lokala Active Directory (AD DS). AD DS och Azure AD använder helt olika autentiseringsprotokoll. Mer information finns i [Azure AD Domain Services-dokumentationen](https://docs.microsoft.com/azure/active-directory-domain-services/).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Krav

Den här artikeln fortsätter där du slutade i [Självstudier: Bygg en ASP.net-app i Azure med SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md) eller [Självstudier: Bygg en ASP.NET Core-och SQL Database-app i Azure App Service](app-service-web-tutorial-dotnetcore-sqldb.md). Om du inte redan har gjort det följer du en av de två självstudierna först. Du kan också anpassa stegen för din egen .NET-app med SQL Database.

Om du vill felsöka din app med SQL Database som Server del kontrollerar du att du har tillåtit klient anslutning från datorn. Om inte, lägger du till klientens IP-adress genom att följa stegen i [Hantera IP-brandväggens regler på server nivå med hjälp av Azure Portal](../sql-database/sql-database-firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="grant-database-access-to-azure-ad-user"></a>Bevilja databas åtkomst till Azure AD-användare

Börja med att aktivera Azure AD-autentisering till SQL Database genom att tilldela en Azure AD-användare som Active Directorys administratör för SQL Database-servern. Den här användaren skiljer sig från Microsoft-konto som du använde för att registrera dig för din Azure-prenumeration. Det måste vara en användare som du har skapat, importerat, synkroniserat eller bjudit in till Azure AD. Mer information om tillåtna Azure AD-användare finns i [Azure AD-funktioner och begränsningar i SQL Database](../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations).

Om din Azure AD-klient inte har en användare ännu, skapar du en genom att följa stegen i [lägga till eller ta bort användare med Azure Active Directory](../active-directory/fundamentals/add-users-azure-active-directory.md).

Hitta objekt-ID: t för Azure AD-användaren med hjälp av [`az ad user list`](/cli/azure/ad/user?view=azure-cli-latest#az-ad-user-list) och Ersätt *\<user-huvud namn >* . Resultatet sparas i en variabel.

```azurecli-interactive
azureaduser=$(az ad user list --filter "userPrincipalName eq '<user-principal-name>'" --query [].objectId --output tsv)
```
> [!TIP]
> Om du vill se en lista över alla huvud namn för användare i Azure AD, kör `az ad user list --query [].userPrincipalName`.
>

Lägg till den här Azure AD-användaren som en Active Directory administratör som använder kommandot [`az sql server ad-admin create`](/cli/azure/sql/server/ad-admin?view=azure-cli-latest#az-sql-server-ad-admin-create) i Cloud Shell. I följande kommando ersätter du *\<server-name >* med SQL Database Server namnet (utan suffixet `.database.windows.net`).

```azurecli-interactive
az sql server ad-admin create --resource-group myResourceGroup --server-name <server-name> --display-name ADMIN --object-id $azureaduser
```

Mer information om hur du lägger till en Active Directory-administratör finns i [etablera en Azure Active Directory administratör för din Azure SQL Database Server](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)

## <a name="set-up-visual-studio"></a>Konfigurera Visual Studio

### <a name="windows"></a>Windows
Visual Studio för Windows är integrerat med Azure AD-autentisering. Om du vill aktivera utveckling och fel sökning i Visual Studio lägger du till din Azure AD-användare i Visual Studio genom att välja **fil** > **konto inställningar** på menyn och klicka på **Lägg till ett konto**.

Om du vill ställa in Azure AD-användaren för Azure-tjänsteautentisering väljer du **verktyg** > **alternativ** på menyn och väljer sedan **Azure Service Authentication** > **konto val**. Välj den Azure AD-användare som du har lagt till och klicka på **OK**.

Nu är du redo att utveckla och felsöka din app med SQL Database som Server del, med Azure AD-autentisering.

### <a name="macos"></a>MacOS

Visual Studio för Mac är inte integrerat med Azure AD-autentisering. Men det [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) -bibliotek som du kommer att använda senare kan använda tokens från Azure CLI. Om du vill aktivera utveckling och fel sökning i Visual Studio måste du först [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) på den lokala datorn.

När Azure CLI är installerat på den lokala datorn loggar du in på Azure CLI med följande kommando med hjälp av din Azure AD-användare:

```bash
az login --allow-no-subscriptions
```
Nu är du redo att utveckla och felsöka din app med SQL Database som Server del, med Azure AD-autentisering.

## <a name="modify-your-project"></a>Ändra ditt projekt

De steg som du följer för projektet beror på om det är ett ASP.NET-projekt eller ett ASP.NET Core-projekt.

- [Ändra ASP.NET](#modify-aspnet)
- [Ändra ASP.NET Core](#modify-aspnet-core)

### <a name="modify-aspnet"></a>Ändra ASP.NET

I Visual Studio öppnar du Package Manager-konsolen och lägger till NuGet-paketet [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication):

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.3.0
```

I *Web. config*arbetar du från början av filen och gör följande ändringar:

- Lägg till följande avsnitts deklaration i `<configSections>`:

    ```xml
    <section name="SqlAuthenticationProviders" type="System.Data.SqlClient.SqlAuthenticationProviderConfigurationSection, System.Data, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" />
    ```

- under taggen för att stänga `</configSections>` lägger du till följande XML-kod för `<SqlAuthenticationProviders>`.

    ```xml
    <SqlAuthenticationProviders>
      <providers>
        <add name="Active Directory Interactive" type="Microsoft.Azure.Services.AppAuthentication.SqlAppAuthenticationProvider, Microsoft.Azure.Services.AppAuthentication" />
      </providers>
    </SqlAuthenticationProviders>
    ```    

- Hitta anslutnings strängen med namnet `MyDbConnection` och ersätt dess `connectionString`-värde med `"server=tcp:<server-name>.database.windows.net;database=<db-name>;UID=AnyString;Authentication=Active Directory Interactive"`. Ersätt _\<server-name >_ och _\<db-Name >_ med Server namnet och databas namnet.

Det var allt du behöver för att ansluta till SQL Database. Vid fel sökning i Visual Studio använder din kod den Azure AD-användare som du konfigurerade i [Konfigurera Visual Studio](#set-up-visual-studio). Du ställer in SQL Database servern senare för att tillåta anslutning från den hanterade identiteten för din App Service-app.

Skriv `Ctrl+F5` om du vill köra appen igen. Samma CRUD-app i webbläsaren ansluter nu till Azure SQL Database direkt med Azure AD-autentisering. Med den här installationen kan du köra databas migreringar från Visual Studio.

### <a name="modify-aspnet-core"></a>Ändra ASP.NET Core

I Visual Studio öppnar du Package Manager-konsolen och lägger till NuGet-paketet [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication):

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.3.0
```

I [självstudierna ASP.net Core och SQL Database](app-service-web-tutorial-dotnetcore-sqldb.md)används inte `MyDbConnection`-anslutningssträngen alls eftersom den lokala utvecklings miljön använder en sqlite-databasfil och Azures produktions miljö använder en anslutnings sträng från App Service. Med Active Directory autentisering vill du att båda miljöerna ska använda samma anslutnings sträng. I *appSettings. JSON*ersätter du värdet för anslutnings strängen `MyDbConnection` med:

```json
"Server=tcp:<server-name>.database.windows.net,1433;Database=<database-name>;"
```

I *startup.cs*tar du bort kod avsnittet som du lade till innan:

```csharp
// Use SQL Database if in Azure, otherwise, use SQLite
if (Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT") == "Production")
    services.AddDbContext<MyDatabaseContext>(options =>
            options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection")));
else
    services.AddDbContext<MyDatabaseContext>(options =>
            options.UseSqlite("Data Source=localdatabase.db"));

// Automatically perform database migration
services.BuildServiceProvider().GetService<MyDatabaseContext>().Database.Migrate();
```

Och Ersätt det med följande kod:

```csharp
services.AddDbContext<MyDatabaseContext>(options => {
    options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection"));
});
```

Sedan anger du Entity Framework databas kontext med åtkomsttoken för SQL Database. I *Data\MyDatabaseContext.cs*lägger du till följande kod innanför klammerparenteserna i den tomma `MyDatabaseContext (DbContextOptions<MyDatabaseContext> options)`-konstruktorn:

```csharp
var conn = (System.Data.SqlClient.SqlConnection)Database.GetDbConnection();
conn.AccessToken = (new Microsoft.Azure.Services.AppAuthentication.AzureServiceTokenProvider()).GetAccessTokenAsync("https://database.windows.net/").Result;
```

> [!TIP]
> Den här demonstrations koden är synkron för tydlighets skull. Mer information finns i [asynkron guide för konstruktorer](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#constructors).

Det var allt du behöver för att ansluta till SQL Database. Vid fel sökning i Visual Studio använder din kod den Azure AD-användare som du konfigurerade i [Konfigurera Visual Studio](#set-up-visual-studio). Du ställer in SQL Database servern senare för att tillåta anslutning från den hanterade identiteten för din App Service-app. Klassen `AzureServiceTokenProvider` cachelagrar token i minnet och hämtar den från Azure AD precis före förfallo datum. Du behöver ingen anpassad kod för att uppdatera token.

> [!TIP]
> Om den Azure AD-användare som du har konfigurerat har åtkomst till flera klienter anropar du `GetAccessTokenAsync("https://database.windows.net/", tenantid)` med önskat klient-ID för att hämta rätt åtkomsttoken.

Skriv `Ctrl+F5` om du vill köra appen igen. Samma CRUD-app i webbläsaren ansluter nu till Azure SQL Database direkt med Azure AD-autentisering. Med den här installationen kan du köra databas migreringar från Visual Studio.

## <a name="use-managed-identity-connectivity"></a>Använd hanterad identitets anslutning

Sedan konfigurerar du App Service-appen så att den ansluter till SQL Database med en systemtilldelad hanterad identitet.

### <a name="enable-managed-identity-on-app"></a>Aktivera hanterad identitet i appen

När du ska aktivera en hanterad identitet för din Azure-app använder du kommandot [az webapp identity assign](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign) i Cloud Shell. I följande kommando ersätter du *\<APP-name >* .

```azurecli-interactive
az webapp identity assign --resource-group myResourceGroup --name <app-name>
```

Här är ett exempel på utdata:

```json
{
  "additionalProperties": {},
  "principalId": "21dfa71c-9e6f-4d17-9e90-1d28801c9735",
  "tenantId": "72f988bf-86f1-41af-91ab-2d7cd011db47",
  "type": "SystemAssigned"
}
```

### <a name="add-managed-identity-to-an-azure-ad-group"></a>Lägg till hanterad identitet i en Azure AD-grupp

Om du vill ge den här identiteten åtkomst till din SQL Database måste du lägga till den i en [Azure AD-grupp](../active-directory/fundamentals/active-directory-manage-groups.md). I Cloud Shell lägger du till den i en ny grupp med namnet _myAzureSQLDBAccessGroup_, som visas i följande skript:

```azurecli-interactive
groupid=$(az ad group create --display-name myAzureSQLDBAccessGroup --mail-nickname myAzureSQLDBAccessGroup --query objectId --output tsv)
msiobjectid=$(az webapp identity show --resource-group myResourceGroup --name <app-name> --query principalId --output tsv)
az ad group member add --group $groupid --member-id $msiobjectid
az ad group member list -g $groupid
```

Om du vill se fullständiga JSON-utdata för varje kommando använder du parametrarna `--query objectId --output tsv`.

### <a name="grant-permissions-to-azure-ad-group"></a>Bevilja behörighet till Azure AD-grupp

Öppna Cloud Shell och logga in på SQL Database med kommandot SQLCMD. Ersätt _\<server->_ med SQL Database-servernamnet _\<DB >_ med det databas namn som appen använder och _\<aad-user-name >_ och _\<AAD-Password >_ med din Azure AD-användare klientautentiseringsuppgifter.

```azurecli-interactive
sqlcmd -S <server-name>.database.windows.net -d <db-name> -U <aad-user-name> -P "<aad-password>" -G -l 30
```

I SQL-prompten för den databas som du vill använda kör du följande kommandon för att lägga till Azure AD-gruppen och bevilja de behörigheter som appen behöver. Exempel: 

```sql
CREATE USER [myAzureSQLDBAccessGroup] FROM EXTERNAL PROVIDER;
ALTER ROLE db_datareader ADD MEMBER [myAzureSQLDBAccessGroup];
ALTER ROLE db_datawriter ADD MEMBER [myAzureSQLDBAccessGroup];
ALTER ROLE db_ddladmin ADD MEMBER [myAzureSQLDBAccessGroup];
GO
```

Skriv `EXIT` för att återgå till Cloud Shell-prompten.

### <a name="modify-connection-string"></a>Ändra anslutningssträngen

Kom ihåg att samma ändringar som du gjorde i *Web. config* eller *appSettings. JSON* fungerar med den hanterade identiteten, så det enda du behöver göra är att ta bort den befintliga anslutnings strängen i App Service, som Visual Studio har skapat för att distribuera din app den första tid. Använd följande kommando, men Ersätt *\<APP-name >* med namnet på din app.

```azurecli-interactive
az webapp config connection-string delete --resource-group myResourceGroup --name <app-name> --setting-names MyDbConnection
```

## <a name="publish-your-changes"></a>Publicera dina ändringar

Nu behöver du bara publicera ändringarna till Azure.

**Om du kom från [Självstudier: bygg en ASP.net-app i Azure med SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md)** , publicera dina ändringar i Visual Studio. I **Solution Explorer**: Högerklicka på projektet **DotNetAppSqlDb** och välj **Publicera**.

![Publicera från Solution Explorer](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

Klicka på **Publicera** på publiceringssidan. 

**Om du kom från [Självstudier: bygg en ASP.NET Core och SQL Database app i Azure App Service](app-service-web-tutorial-dotnetcore-sqldb.md)** , publicera dina ändringar med hjälp av Git med följande kommandon:

```bash
git commit -am "configure managed identity"
git push azure master
```

När du ser din att göra-lista på den nya webbsidan ansluter din app till databasen med hjälp av den hanterade identiteten.

![Azure-app efter Code First Migration](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

Du ska nu kunna redigera att göra-listan som innan.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Nästa steg

Vad du lärt dig:

> [!div class="checklist"]
> * Aktivera hanterade identiteter
> * Ge SQL Database åtkomst till den hanterade identiteten
> * Konfigurera Entity Framework att använda Azure AD-autentisering med SQL Database
> * Ansluta till SQL Database från Visual Studio med Azure AD-autentisering

Gå vidare till nästa självstudie där du får lära dig att mappa ett anpassat DNS-namn till webbappen.

> [!div class="nextstepaction"]
> [Mappa ett befintligt anpassat DNS-namn till Azure App Service](app-service-web-tutorial-custom-domain.md)
