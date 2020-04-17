---
title: 'Självstudiekurs: Komma åt data med hanterad identitet'
description: Lär dig hur du gör databasanslutningen säkrare med hjälp av en hanterad identitet och även hur du tillämpar den på andra Azure-tjänster.
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 11/18/2019
ms.custom: mvc, cli-validate
ms.openlocfilehash: 787809e4132defa101bb82659e8af1a2d9f1b9b6
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81453887"
---
# <a name="tutorial-secure-azure-sql-database-connection-from-app-service-using-a-managed-identity"></a>Självstudie: Säkra Azure SQL Database-anslutningar från App Service med en hanterad identitet

Med [App Service ](overview.md) får du en automatiskt uppdaterad webbvärdtjänst i Azure med hög skalbarhet. Dessutom får du en [hanterad identitet](overview-managed-identity.md) för din app. Det här är en användningsklar lösning som skyddar åtkomsten till [Azure SQL Database](/azure/sql-database/) och andra Azure-tjänster. Med hanterade identiteter i App Service blir dina appar säkrare eftersom du inte har några hemligheter i dina appar. Du har till exempel inga inloggningsuppgifter i anslutningssträngarna. I den här självstudien lägger du till hanterad identitet i exempelwebbappen som du har skapat i någon av följande självstudier: 

- [Självstudie: Skapa en ASP.NET-app i Azure med SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md)
- [Självstudiekurs: Skapa en ASP.NET Core- och SQL Database-app i Azure App Service](app-service-web-tutorial-dotnetcore-sqldb.md)

När du är färdig ansluter exempelappen säkert till SQL Database utan att du behöver använda användarnamn och lösenord.

> [!NOTE]
> Stegen i den här självstudien stöder följande versioner:
> 
> - .NET-ramverket 4.7.2
> - .NET Core 2.2
>

Vad du kommer att lära dig:

> [!div class="checklist"]
> * Aktivera hanterade identiteter
> * Ge SQL Database åtkomst till den hanterade identiteten
> * Konfigurera Entity Framework för att använda Azure AD-autentisering med SQL Database
> * Ansluta till SQL Database från Visual Studio med Azure AD-autentisering

> [!NOTE]
>Azure AD-autentisering _skiljer sig_ från [integrerad Windows-autentisering](/previous-versions/windows/it-pro/windows-server-2003/cc758557(v=ws.10)) i lokal Active Directory (AD DS). AD DS och Azure AD använder helt andra autentiseringsprotokoll. Mer information finns i [Azure AD Domain Services-dokumentationen](https://docs.microsoft.com/azure/active-directory-domain-services/).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Krav

Den här artikeln fortsätter där du slutade i [Självstudiekurs: Skapa en ASP.NET-app i Azure med SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md) eller [Självstudiekurs: Skapa en ASP.NET Core- och SQL Database-app i Azure App Service](app-service-web-tutorial-dotnetcore-sqldb.md). Om du inte redan har, följ en av de två tutorials först. Du kan också anpassa stegen för din egen .NET-app med SQL Database.

Om du vill felsöka appen med SQL Database som serverdel kontrollerar du att du har tillåtit klientanslutning från datorn. Om inte, lägg till klient-IP genom att följa stegen på [Hantera IP-brandväggsregler på servernivå med Azure-portalen](../sql-database/sql-database-firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="grant-database-access-to-azure-ad-user"></a>Bevilja databasåtkomst till Azure AD-användare

Aktivera först Azure AD-autentisering till SQL Database genom att tilldela en Azure AD-användare som Active Directory-administratör för SQL Database-servern. Den här användaren skiljer sig från det Microsoft-konto som du använde för att registrera dig för din Azure-prenumeration. Det måste vara en användare som du har skapat, importerat, synkroniserat eller bjudits in till Azure AD. Mer information om tillåtna Azure AD-användare finns [i Azure AD-funktioner och begränsningar i SQL Database](../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations).

Om din Azure AD-klient inte har en användare ännu skapar du en genom att följa stegen på [Lägg till eller ta bort användare med Azure Active Directory](../active-directory/fundamentals/add-users-azure-active-directory.md).

Leta reda på objekt-ID för [`az ad user list`](/cli/azure/ad/user?view=azure-cli-latest#az-ad-user-list) Azure AD-användaren med hjälp av och ersätta * \<>för användarnamn. * Resultatet sparas i en variabel.

```azurecli-interactive
azureaduser=$(az ad user list --filter "userPrincipalName eq '<user-principal-name>'" --query [].objectId --output tsv)
```
> [!TIP]
> Om du vill visa listan över alla `az ad user list --query [].userPrincipalName`användarnamn i Azure AD kör du .
>

Lägg till den här Azure AD-användaren som Active Directory-administratör med kommandot [`az sql server ad-admin create`](/cli/azure/sql/server/ad-admin?view=azure-cli-latest#az-sql-server-ad-admin-create) Cloud Shell. I följande kommando ersätter du `.database.windows.net` * \<servernamn>* med SQL Database-servernamnet (utan suffixet).

```azurecli-interactive
az sql server ad-admin create --resource-group myResourceGroup --server-name <server-name> --display-name ADMIN --object-id $azureaduser
```

Mer information om hur du lägger till en Active Directory-administratör finns i [Etablera en Azure Active Directory-administratör för din Azure SQL Database Server](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)

## <a name="set-up-visual-studio"></a>Konfigurera Visual Studio

### <a name="windows"></a>Windows
Visual Studio för Windows är integrerat med Azure AD-autentisering. Om du vill aktivera utveckling och felsökning i Visual Studio lägger du till din Azure AD-användare i Visual Studio genom att välja > **Filkontoinställningar** på menyn och klickar på **Lägg till ett konto**. **File**

Om du vill ange Azure AD-användare för Azure-tjänstautentisering **Azure Service Authentication** > väljer du > **Verktygsalternativ** på menyn och väljer sedan Val av Azure Service Authentication**Account**. **Tools** Välj den Azure AD-användare som du har lagt till och klicka på **OK**.

Du är nu redo att utveckla och felsöka din app med SQL-databasen som serverdel med Azure AD-autentisering.

### <a name="macos"></a>MacOS

Visual Studio för Mac är inte integrerat med Azure AD-autentisering. [Microsoft.Azure.Services.AppAuthentication-biblioteket](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) som du kommer att använda senare kan dock använda token från Azure CLI. För att aktivera utveckling och felsökning i Visual Studio måste du först [installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) på din lokala dator.

När Azure CLI har installerats på din lokala dator loggar du in på Azure CLI med följande kommando med din Azure AD-användare:

```bash
az login --allow-no-subscriptions
```
Du är nu redo att utveckla och felsöka din app med SQL-databasen som serverdel med Azure AD-autentisering.

## <a name="modify-your-project"></a>Ändra projektet

Vilka steg du följer för projektet beror på om det är ett ASP.NET projekt eller ett ASP.NET Core-projekt.

- [Ändra ASP.NET](#modify-aspnet)
- [Ändra ASP.NET kärna](#modify-aspnet-core)

### <a name="modify-aspnet"></a>Ändra ASP.NET

I Visual Studio öppnar du Package Manager-konsolen och lägger till NuGet-paketet [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication):

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.3.1
```

Arbeta från början av filen och göra följande ändringar i *Web.config*och göra följande ändringar:

- I `<configSections>`lägger du till följande avsnittsdeklaration i den:

    ```xml
    <section name="SqlAuthenticationProviders" type="System.Data.SqlClient.SqlAuthenticationProviderConfigurationSection, System.Data, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" />
    ```

- under den `</configSections>` avslutande taggen lägger `<SqlAuthenticationProviders>`du till följande XML-kod för .

    ```xml
    <SqlAuthenticationProviders>
      <providers>
        <add name="Active Directory Interactive" type="Microsoft.Azure.Services.AppAuthentication.SqlAppAuthenticationProvider, Microsoft.Azure.Services.AppAuthentication" />
      </providers>
    </SqlAuthenticationProviders>
    ```    

- Leta reda på `MyDbConnection` anslutningssträngen som anropas och ersätt dess `connectionString` värde med `"server=tcp:<server-name>.database.windows.net;database=<db-name>;UID=AnyString;Authentication=Active Directory Interactive"`. Ersätt _ \<servernamn>_ och _ \<db-namn>_ med servernamnet och databasnamnet.

> [!NOTE]
> SqlAuthenticationProvider som du just registrerade baseras på toppen av appautentiseringsbiblioteket som du installerade tidigare. Som standard används en systemtilldelad identitet. Om du vill använda en användartilldelad identitet måste du ange ytterligare en konfiguration. Se stöd för [anslutningssträng](../key-vault/general/service-to-service-authentication.md#connection-string-support) för AppAuthentication-biblioteket.

Det är allt du behöver för att ansluta till SQL Database. När du felsöker i Visual Studio använder koden den Azure AD-användare som du har konfigurerat i [Konfigurera Visual Studio](#set-up-visual-studio). Du konfigurerar SQL Database-servern senare för att tillåta anslutning från den hanterade identiteten för apptjänstappen.

Skriv `Ctrl+F5` för att köra appen igen. Samma CRUD-app i din webbläsare ansluter nu till Azure SQL Database direkt med Hjälp av Azure AD-autentisering. Med den här inställningen kan du köra databasmigreringar från Visual Studio.

### <a name="modify-aspnet-core"></a>Ändra ASP.NET kärna

I Visual Studio öppnar du Package Manager-konsolen och lägger till NuGet-paketet [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication):

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.3.1
```

I [självstudien ASP.NET Core och](app-service-web-tutorial-dotnetcore-sqldb.md)SQL `MyDbConnection` Database används inte anslutningssträngen alls eftersom den lokala utvecklingsmiljön använder en Sqlite-databasfil och Azure-produktionsmiljön använder en anslutningssträng från App Service. Med Active Directory-autentisering vill du att båda miljöerna ska använda samma anslutningssträng. I *appsettings.json ersätter*du `MyDbConnection` värdet för anslutningssträngen med:

```json
"Server=tcp:<server-name>.database.windows.net,1433;Database=<database-name>;"
```

I *Startup.cs*tar du bort kodavsnittet som du lade till tidigare:

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

Och ersätta den med följande kod:

```csharp
services.AddDbContext<MyDatabaseContext>(options => {
    options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection"));
});
```

Därefter anger du entitetsramsdatabaskontexten med åtkomsttoken för SQL-databasen. Lägg till följande kod i den tomma `MyDatabaseContext (DbContextOptions<MyDatabaseContext> options)` konstruktorns klammerparenteser i *Data\MyDatabaseContext.cs:*

```csharp
var conn = (System.Data.SqlClient.SqlConnection)Database.GetDbConnection();
conn.AccessToken = (new Microsoft.Azure.Services.AppAuthentication.AzureServiceTokenProvider()).GetAccessTokenAsync("https://database.windows.net/").Result;
```

> [!NOTE]
> Denna demonstrationskod är synkron för tydlighet och enkelhet.

Det är allt du behöver för att ansluta till SQL Database. När du felsöker i Visual Studio använder koden den Azure AD-användare som du har konfigurerat i [Konfigurera Visual Studio](#set-up-visual-studio). Du konfigurerar SQL Database-servern senare för att tillåta anslutning från den hanterade identiteten för apptjänstappen. Klassen `AzureServiceTokenProvider` cachelagrar token i minnet och hämtar den från Azure AD strax före förfallodatum. Du behöver ingen anpassad kod för att uppdatera token.

> [!TIP]
> Om Azure AD-användaren som du konfigurerat `GetAccessTokenAsync("https://database.windows.net/", tenantid)` har åtkomst till flera klienter, anropa med önskat klient-ID för att hämta rätt åtkomsttoken.

Skriv `Ctrl+F5` för att köra appen igen. Samma CRUD-app i din webbläsare ansluter nu till Azure SQL Database direkt med Hjälp av Azure AD-autentisering. Med den här inställningen kan du köra databasmigreringar från Visual Studio.

## <a name="use-managed-identity-connectivity"></a>Använda anslutning till hanterad identitet

Därefter konfigurerar du apptjänstappen för att ansluta till SQL Database med en systemtilldelad hanterad identitet.

> [!NOTE]
> Medan instruktionerna i det här avsnittet gäller en systemtilldelad identitet kan en användartilldelad identitet lika gärna användas. För att göra det här. du skulle behöva `az webapp identity assign command` ändra för att tilldela önskad användartilldelade identitet. När du skapar SQL-användaren ska du se till att använda namnet på den användartilldelade identitetsresursen i stället för platsnamnet.

### <a name="enable-managed-identity-on-app"></a>Aktivera hanterad identitet i appen

När du ska aktivera en hanterad identitet för din Azure-app använder du kommandot [az webapp identity assign](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign) i Cloud Shell. I följande kommando * \< *ersätter du>för appnamn .

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

### <a name="grant-permissions-to-managed-identity"></a>Bevilja behörigheter till hanterad identitet

> [!NOTE]
> Om du vill kan du lägga till identiteten i en [Azure AD-grupp](../active-directory/fundamentals/active-directory-manage-groups.md)och sedan bevilja SQL-databasåtkomst till Azure AD-gruppen i stället för identiteten. Följande kommandon lägger till till exempel till den hanterade identiteten från föregående steg till en ny grupp som heter _myAzureSQLDBAccessGroup:_
> 
> ```azurecli-interactive
> groupid=$(az ad group create --display-name myAzureSQLDBAccessGroup --mail-nickname myAzureSQLDBAccessGroup --query objectId --output tsv)
> msiobjectid=$(az webapp identity show --resource-group myResourceGroup --name <app-name> --query principalId --output tsv)
> az ad group member add --group $groupid --member-id $msiobjectid
> az ad group member list -g $groupid
> ```
>

Öppna Cloud Shell och logga in på SQL Database med kommandot SQLCMD. Ersätt _ \<servernamn>_ med servernamnet i SQL Database, _ \<db-namn>_ med det databasnamn som appen använder och _ \<aad-user-name>_ och _ \<aad-password>_ med din Azure AD-användares autentiseringsuppgifter.

```azurecli-interactive
sqlcmd -S <server-name>.database.windows.net -d <db-name> -U <aad-user-name> -P "<aad-password>" -G -l 30
```

I SQL-prompten för den databas du vill använda kör du följande kommandon för att lägga till Azure AD-gruppen och bevilja de behörigheter som appen behöver. Exempel: 

```sql
CREATE USER [<identity-name>] FROM EXTERNAL PROVIDER;
ALTER ROLE db_datareader ADD MEMBER [<identity-name>];
ALTER ROLE db_datawriter ADD MEMBER [<identity-name>];
ALTER ROLE db_ddladmin ADD MEMBER [<identity-name>];
GO
```

identitetsnamn>är namnet på den hanterade identiteten i Azure AD. * \<* Om identiteten är systemtilldelad är namnet alltid detsamma som namnet på apptjänstappen. Om du vill bevilja behörigheter för en Azure AD-grupp använder du gruppens visningsnamn i stället (till exempel *myAzureSQLDBAccessGroup*).

Skriv `EXIT` för att återgå till Cloud Shell-prompten.

> [!NOTE]
> Backend-tjänsterna för hanterade [identiteter upprätthåller också en tokencache](overview-managed-identity.md#obtain-tokens-for-azure-resources) som uppdaterar token för en målresurs först när den upphör att gälla. Om du gör ett misstag med att konfigurera sql-databasbehörigheterna och försöker ändra behörigheterna *efter* att ha försökt hämta en token med din app, får du faktiskt inte en ny token med de uppdaterade behörigheterna förrän den cachelagrade token upphör att gälla.

### <a name="modify-connection-string"></a>Ändra anslutningssträngen

Kom ihåg att samma ändringar som du har gjort i *Web.config* eller *appsettings.json* fungerar med den hanterade identiteten, så det enda du kan göra är att ta bort den befintliga anslutningssträngen i App Service, som Visual Studio skapade distribuera din app första gången. Använd följande kommando, * \<* men ersätt>med appens namn.

```azurecli-interactive
az webapp config connection-string delete --resource-group myResourceGroup --name <app-name> --setting-names MyDbConnection
```

## <a name="publish-your-changes"></a>Publicera dina ändringar

Nu behöver du bara publicera ändringarna till Azure.

**Om du kom från [Självstudiekurs: Skapa en ASP.NET app i Azure med SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md)** publicerar du dina ändringar i Visual Studio. I **Solution Explorer**: Högerklicka på projektet **DotNetAppSqlDb** och välj **Publicera**.

![Publicera från Solution Explorer](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

Klicka på **Publicera** på publiceringssidan. 

**Om du kom från [Självstudiekurs: Skapa en ASP.NET Core- och SQL Database-app i Azure App Service](app-service-web-tutorial-dotnetcore-sqldb.md)** publicerar du ändringarna med Git med följande kommandon:

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
> * Konfigurera Entity Framework för att använda Azure AD-autentisering med SQL Database
> * Ansluta till SQL Database från Visual Studio med Azure AD-autentisering

Gå vidare till nästa självstudie där du får lära dig att mappa ett anpassat DNS-namn till webbappen.

> [!div class="nextstepaction"]
> [Mappa ett befintligt anpassat DNS-namn till Azure App Service](app-service-web-tutorial-custom-domain.md)
