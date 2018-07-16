---
title: Säkra Azure SQL Database-anslutningar från App Service med hanterade tjänstidentiteter | Microsoft Docs
description: Lär dig hur du gör dina databasanslutningar säkrare med hjälp av hanterade tjänstidentiteter, och hur du använder detta även i andra Azure-tjänster.
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
ms.date: 04/17/2018
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: f1388843f2c5d3ea607b876ece288db1370329a2
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38461545"
---
# <a name="tutorial-secure-sql-database-connection-with-managed-service-identity"></a>Självstudie: Säkra SQL Database-anslutningar med hanterade tjänstidentiteter

Med [App Service ](app-service-web-overview.md) får du en automatiskt uppdaterad webbvärdtjänst i Azure med hög skalbarhet. Dessutom får du en [hanterad tjänstidentitet](app-service-managed-service-identity.md) för din app. Det här är en användningsklar lösning som skyddar åtkomsten till [Azure SQL Database](/azure/sql-database/) och andra Azure-tjänster. Med hanterade tjänstidentiteter i App Service blir dina appar säkrare eftersom du inte har några hemligheter i dina appar. Du har till exempel inga inloggningsuppgifter i anslutningssträngarna. I den här självstudien kommer du att lägga till en hanterad tjänstidentitet i den ASP.NET-exempelapp du skapade i [Självstudie: Skapa en ASP.NET-app i Azure med SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md). När du är färdig ansluter exempelappen säkert till SQL Database utan att du behöver använda användarnamn och lösenord.

Du lär dig att:

> [!div class="checklist"]
> * Aktivera hanterade tjänstidentiteter
> * Ge SQL Database åtkomst till tjänstidentiteten
> * Konfigurera programkoden för autentisering med SQL Database med hjälp av Azure Active Directory-autentisering
> * Ge lägsta möjliga behörighetsnivå till tjänstidentiteten i SQL Database

> [!NOTE]
> Azure Active Directory-autentisering är _annorlunda_ jämfört med [integrerad Windows-autentisering](/previous-versions/windows/it-pro/windows-server-2003/cc758557(v=ws.10)) i lokal Active Directory (AD DS). AD DS och Azure Active Directory använder helt olika autentiseringsprotokoll. Mer information finns i [Skillnaden mellan Windows Server AD DS och AD Azure](../active-directory/fundamentals/understand-azure-identity-solutions.md#the-difference-between-windows-server-ad-ds-and-azure-ad).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

Den här artikeln fortsätter där du slutade i [Självstudie: Skapa en ASP.NET-app i Azure med SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md). Om du inte redan gjort det ska du gå igenom den självstudien först. Du kan också anpassa stegen för en egen ASP.NET-app med SQL Database.

<!-- ![app running in App Service](./media/app-service-web-tutorial-dotnetcore-sqldb/azure-app-in-browser.png) -->

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="enable-managed-service-identity"></a>Aktivera hanterade tjänstidentiteter

När du ska aktivera en tjänstidentitet för din Azure-app använder du kommandot [az webapp identity assign](/cli/azure/webapp/identity?view=azure-cli-latest#az_webapp_identity_assign) i Cloud Shell. Ersätt *\<app name>* i följande kommando.

```azurecli-interactive
az webapp identity assign --resource-group myResourceGroup --name <app name>
```

Här är ett exempel på utdata när identiteten har skapats i Azure Active Directory:

```json
{
  "additionalProperties": {},
  "principalId": "21dfa71c-9e6f-4d17-9e90-1d28801c9735",
  "tenantId": "72f988bf-86f1-41af-91ab-2d7cd011db47",
  "type": "SystemAssigned"
}
```

Du använder värdet för `principalId` i nästa steg. Om du vill se information om den nya identiteten i Azure Active Directory kör du följande kommando med värdet för `principalId`:

```azurecli-interactive
az ad sp show --id <principalid>
```

## <a name="grant-database-access-to-identity"></a>Bevilja databasåtkomst till identiteten

Nu ger du databasåtkomst till appens tjänstidentitet med hjälp av kommandot [`az sql server ad-admin create`](/cli/azure/sql/server/ad-admin?view=azure-cli-latest#az_sql_server_ad-admin_create) i Cloud Shell. Ersätt *\<server_name>* och <principalid_from_last_step > i följande kommando. Skriv ett administratörsnamn för *\<admin_user>*.

```azurecli-interactive
az sql server ad-admin create --resource-group myResourceGroup --server-name <server_name> --display-name <admin_user> --object-id <principalid_from_last_step>
```

Den hanterade tjänstidentiteten har nu åtkomst till din Azure SQL Database-server.

## <a name="modify-connection-string"></a>Ändra anslutningssträngen

Ändra anslutningen du ställde in för appen tidigare med kommandot [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) i Cloud Shell. Ersätt *\<app name>* i följande kommando med namnet på din app, och ersätt *\<server_name>* och *\<db_name>* med värdena för din SQL-databas.

```azurecli-interactive
az webapp config connection-string set --resource-group myResourceGroup --name <app name> --settings MyDbConnection='Server=tcp:<server_name>.database.windows.net,1433;Database=<db_name>;' --connection-string-type SQLAzure
```

## <a name="modify-aspnet-code"></a>Ändra ASP.NET-koden

Öppna **DotNetAppSqlDb**-projektet i Visual Studio, öppna _packages.config_ och lägg till följande rad i listan med paket.

```xml
<package id="Microsoft.Azure.Services.AppAuthentication" version="1.1.0-preview" targetFramework="net461" />
```

Öppna _Models\MyDatabaseContext.cs_ och lägg till följande `using`-instruktioner överst i filen:

```csharp
using System.Data.SqlClient;
using Microsoft.Azure.Services.AppAuthentication;
using System.Web.Configuration;
```

Lägg till följande konstruktor i klassen `MyDatabaseContext`:

```csharp
public MyDatabaseContext(SqlConnection conn) : base(conn, true)
{
    conn.ConnectionString = WebConfigurationManager.ConnectionStrings["MyDbConnection"].ConnectionString;
    // DataSource != LocalDB means app is running in Azure with the SQLDB connection string you configured
    if(conn.DataSource != "(localdb)\\MSSQLLocalDB")
        conn.AccessToken = (new AzureServiceTokenProvider()).GetAccessTokenAsync("https://database.windows.net/").Result;

    Database.SetInitializer<MyDatabaseContext>(null);
}
```

Den här konstruktorn konfigurerar ett anpassat SqlConnection-objekt så att en åtkomsttoken för Azure SQL Database från App Service används. Med denna åtkomsttoken autentiseras App Service-appen hos Azure SQL Database med den hanterade tjänstidentiteten. Mer information finns i [Hämta token för Azure-resurser](app-service-managed-service-identity.md#obtaining-tokens-for-azure-resources). Med instruktionen `if` kan du fortsätta att testa appen lokalt med LocalDB.

> [!NOTE]
> `SqlConnection.AccessToken` stöds för närvarande bara i .NET Framework 4.6 och senare, inte i [.NET Core](https://www.microsoft.com/net/learn/get-started/windows).
>

Om du vill använda den här nya konstruktorn öppnar du `Controllers\TodosController.cs` och letar rätt på raden `private MyDatabaseContext db = new MyDatabaseContext();`. I den befintliga koden används standardkontrollanten `MyDatabaseContext` till att skapa en databas med standardanslutningssträngen, där användarnamn och lösenord stod i klartext innan [du ändrade det](#modify-connection-string).

Ersätt hela raden med följande kod:

```csharp
private MyDatabaseContext db = new MyDatabaseContext(new SqlConnection());
```

### <a name="publish-your-changes"></a>Publicera dina ändringar

Nu behöver du bara publicera ändringarna till Azure.

I **Solution Explorer**: Högerklicka på projektet **DotNetAppSqlDb** och välj **Publicera**.

![Publicera från Solution Explorer](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

Klicka på **Publicera** på publiceringssidan. När du ser din att göra-lista på den nya webbsidan ansluter din app till databasen med hjälp av den hanterade tjänstidentiteten.

![Azure-webbapp efter Code First Migration](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

Du ska nu kunna redigera att göra-listan som innan.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="grant-minimal-privileges-to-identity"></a>Ge lägsta möjliga behörigheter till identiteten

I de föregående stegen har du antagligen lagt märke till att din hanterade tjänstidentitet är ansluten till SQL Server som Azure AD-administratör. Om du vill ge lägsta möjliga behörighetsnivå till din hanterade tjänstidentitet måste du logga in på Azure SQL Database-servern som Azure AD-administratör och sedan lägga till Azure Active Directory-gruppen som innehåller tjänstidentiteten. 

### <a name="add-managed-service-identity-to-an-azure-active-directory-group"></a>Lägga till den hanterade tjänstidentiteten i en Azure Active Directory-grupp

Öppna Cloud Shell och lägg till den hanterade tjänstidentiteten för din app i en ny Azure Active Directory-grupp med namnet _myAzureSQLDBAccessGroup_, på det sätt som visas i följande skript:

```azurecli-interactive
groupid=$(az ad group create --display-name myAzureSQLDBAccessGroup --mail-nickname myAzureSQLDBAccessGroup --query objectId --output tsv)
msiobjectid=$(az webapp identity show --resource-group <group_name> --name <app_name> --query principalId --output tsv)
az ad group member add --group $groupid --member-id $msiobjectid
az ad group member list -g $groupid
```

Om du vill se fullständiga JSON-utdata för varje kommando använder du parametrarna `--query objectId --output tsv`.

### <a name="reconfigure-azure-ad-administrator"></a>Konfigurera om Azure AD-administratören

Tidigare tilldelade du den hanterade tjänstidentiteten som Azure AD-administratör för SQL Database. Du kan inte använda den här identiteten för interaktiv inloggning (när du ska lägga till databasanvändare), så du måste använda en riktig Azure AD-användare. Om du vill lägga till din Azure AD-användare följer du stegen i [Etablera en Azure Active Directory-administratör för din Azure SQL Database-server](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server). 

### <a name="grant-permissions-to-azure-active-directory-group"></a>Ge behörigheter till Azure Active Directory-gruppen

Öppna Cloud Shell och logga in på SQL Database med kommandot SQLCMD. Ersätt _\<servername>_ med SQL Database-servernamnet samt _\<AADusername>_ och _\<AADpassword>_ med Azure AD-användarens autentiseringsuppgifter.

```azurecli-interactive
sqlcmd -S <server_name>.database.windows.net -U <AADuser_name> -P "<AADpassword>" -G -l 30
```

Kör följande kommandon vid SQL-prompten. De lägger till Azure Active Directory-gruppen du skapade tidigare som en användare.

```sql
CREATE USER [myAzureSQLDBAccessGroup] FROM EXTERNAL PROVIDER;
GO
```

Skriv `EXIT` för att återgå till Cloud Shell-prompten. Kör sedan SQLCMD igen, men ange namnet på databasen för _\<dbname>_.

```azurecli-interactive
sqlcmd -S <server_name>.database.windows.net -d <db_name> -U <AADuser_name> -P "<AADpassword>" -G -l 30
```

Kör följande kommandon vid SQL-prompten för den aktuella databasen så att Azure Active Directory-gruppen får läs- och skrivbehörighet.

```sql
ALTER ROLE db_datareader ADD MEMBER [myAzureSQLDBAccessGroup];
ALTER ROLE db_datawriter ADD MEMBER [myAzureSQLDBAccessGroup];
GO
```

## <a name="next-steps"></a>Nästa steg

Vad du lärt dig:

> [!div class="checklist"]
> * Aktivera hanterade tjänstidentiteter
> * Ge SQL Database åtkomst till tjänstidentiteten
> * Konfigurera programkoden för autentisering med SQL Database med hjälp av Azure Active Directory-autentisering
> * Ge lägsta möjliga behörighetsnivå till tjänstidentiteten i SQL Database

Gå vidare till nästa självstudie där du får lära dig att mappa ett anpassat DNS-namn till webbappen.

> [!div class="nextstepaction"]
> [Mappa ett befintligt anpassat DNS-namn till Azure Web Apps](app-service-web-tutorial-custom-domain.md)
