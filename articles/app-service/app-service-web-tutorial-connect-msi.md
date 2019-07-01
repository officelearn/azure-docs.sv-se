---
title: Skydda SQL Database-anslutningar med en hanterad identitet – Azure App Service | Microsoft Docs
description: Lär dig hur du gör dina databasanslutningar säkrare med hjälp av en hanterad identitet, och hur du använder den i andra Azure-tjänster.
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
ms.date: 06/21/2019
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 31535642526c608ad0ae29e5c0e3c93368e184ad
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/01/2019
ms.locfileid: "67481018"
---
# <a name="tutorial-secure-azure-sql-database-connection-from-app-service-using-a-managed-identity"></a>Självstudie: Säkra Azure SQL Database-anslutningar från App Service med en hanterad identitet

Med [App Service ](overview.md) får du en automatiskt uppdaterad webbvärdtjänst i Azure med hög skalbarhet. Dessutom får du en [hanterad identitet](overview-managed-identity.md) för din app. Det här är en användningsklar lösning som skyddar åtkomsten till [Azure SQL Database](/azure/sql-database/) och andra Azure-tjänster. Med hanterade identiteter i App Service blir dina appar säkrare eftersom du inte har några hemligheter i dina appar. Du har till exempel inga inloggningsuppgifter i anslutningssträngarna. I den här självstudien kommer du att lägga till en hanterad identitet i den ASP.NET-exempelapp du skapade i [Självstudie: Skapa en ASP.NET-app i Azure med SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md). När du är färdig ansluter exempelappen säkert till SQL Database utan att du behöver använda användarnamn och lösenord.

> [!NOTE]
> Det här scenariot stöds för närvarande av .NET Framework 4.7.2 och senare. [.NET Core 2.2](https://www.microsoft.com/net/download/dotnet-core/2.2) stöder det här scenariot men ingår ännu inte i standardavbildningarna i App Service. 
>

Du lär dig att:

> [!div class="checklist"]
> * Aktivera hanterade identiteter
> * Ge SQL Database åtkomst till den hanterade identiteten
> * Konfigurera Entity Framework för att använda Azure AD-autentisering med SQL-databas
> * Anslut till SQL Database från Visual Studio med hjälp av Azure AD-autentisering

> [!NOTE]
>Azure AD-autentisering är _olika_ från [integrerad Windows-autentisering](/previous-versions/windows/it-pro/windows-server-2003/cc758557(v=ws.10)) i den lokala Active Directory (AD DS). AD DS och Azure AD kan du använda olika autentiseringsprotokoll. Mer information finns i [Azure AD Domain Services-dokumentationen](https://docs.microsoft.com/azure/active-directory-domain-services/).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln fortsätter där du slutade i [Självstudie: Skapa en ASP.NET-app i Azure med SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md). Om du inte redan gjort det ska du gå igenom den självstudien först. Du kan också anpassa stegen för en egen ASP.NET-app med SQL Database.

För att felsöka din app med SQL Database som backend-servern, se till att du har [tillåtna klientanslutning från datorn](app-service-web-tutorial-dotnet-sqldatabase.md#allow-client-connection-from-your-computer).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="grant-azure-ad-user-access-to-database"></a>Bevilja Azure AD-användare åtkomst till databasen

Först aktivera Azure AD-autentisering till SQL-databas genom att tilldela en Azure AD-användare som Active Directory-administratör för SQL Database-servern. Den här användaren är skiljer sig från Microsoft-konto som du använde för att registrera dig för din Azure-prenumeration. Det måste vara en användare som du har skapats, importeras, synkroniseras eller bjuds in till Azure AD. Mer information om tillåtna Azure AD-användare finns i [Azure AD-funktioner och begränsningar i SQL Database](../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations). 

Hitta objekt-ID för Azure AD-användare via den [ `az ad user list` ](/cli/azure/ad/user?view=azure-cli-latest#az-ad-user-list) och Ersätt  *\<användarens huvudnamn >* . Resultatet sparas till en variabel.

```azurecli-interactive
azureaduser=$(az ad user list --filter "userPrincipalName eq '<user-principal-name>'" --query [].objectId --output tsv)
```
> [!TIP]
> Om du vill se en lista över alla huvudnamn för användare i Azure AD, kör `az ad user list --query [].userPrincipalName`.
>

Lägg till den här Azure AD-användare som en Active Directory-administratör med [ `az sql server ad-admin create` ](/cli/azure/sql/server/ad-admin?view=azure-cli-latest#az-sql-server-ad-admin-create) i Cloud Shell. I följande kommando, ersätter  *\<servernamn >* .

```azurecli-interactive
az sql server ad-admin create --resource-group myResourceGroup --server-name <server-name> --display-name ADMIN --object-id $azureaduser
```

Läs mer om att lägga till Active Directory-administratör, [etablera en Azure Active Directory-administratör för din Azure SQL Database-Server](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)

## <a name="set-up-visual-studio"></a>Konfigurera Visual Studio

Om du vill aktivera utveckling och felsökning i Visual Studio, lägga till din Azure AD-användare i Visual Studio genom att välja **filen** > **kontoinställningar** i menyn och klicka på **Lägg till en kontot**.

Om du vill ange Azure AD-användare för Azure-tjänst-autentisering, Välj **verktyg** > **alternativ** från menyn, Välj **Azure Service-autentisering**  >  **Konto val av**. Välj Azure AD-användare som du har lagt till och klicka på **OK**.

Du är nu redo att utveckla och felsöka din app med SQL-databasen som backend-servern med hjälp av Azure AD-autentisering.

## <a name="modify-aspnet-project"></a>Ändra ASP.NET-projekt

I Visual Studio öppnar du Package Manager-konsolen och lägger till NuGet-paketet [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication):

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.2.0
```

I *Web.config*, arbetar från överst i filen och gör följande ändringar:

- I `<configSections>`, Lägg till följande avsnitt försäkran i den:

    ```xml
    <section name="SqlAuthenticationProviders" type="System.Data.SqlClient.SqlAuthenticationProviderConfigurationSection, System.Data, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" />
    ```

- under avslutande `</configSections>` tagga, Lägg till följande XML-koden för `<SqlAuthenticationProviders>`.

    ```xml
    <SqlAuthenticationProviders>
      <providers>
        <add name="Active Directory Interactive" type="Microsoft.Azure.Services.AppAuthentication.SqlAppAuthenticationProvider, Microsoft.Azure.Services.AppAuthentication" />
      </providers>
    </SqlAuthenticationProviders>
    ```    

- Hitta den anslutningssträng som heter `MyDbConnection` och Ersätt dess `connectionString` värde med `"server=tcp:<server-name>.database.windows.net;database=<db-name>;UID=AnyString;Authentication=Active Directory Interactive"`. Ersätt  _\<servernamn >_ och  _\<db-name >_ med servernamnet och databasnamnet.

Typ `Ctrl+F5` att köra appen igen. Samma CRUD-app i din webbläsare nu ansluta till Azure SQL Database direkt, med hjälp av Azure AD-autentisering. Den här konfigurationen kan du köra databasmigreringar. Senare när du distribuerar dina ändringar till App Service, på samma inställningar fungera med appens hanterad identitet.

## <a name="use-managed-identity-connectivity"></a>Använd hanterad identitet anslutning

Därefter konfigurerar du App Service-app för att ansluta till SQL-databas med en automatiskt genererad hanterad identitet.

### <a name="enable-managed-identity-on-app"></a>Aktivera hanterad identitet i appen

När du ska aktivera en hanterad identitet för din Azure-app använder du kommandot [az webapp identity assign](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign) i Cloud Shell. I följande kommando, ersätter  *\<appens namn->* .

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

Om du vill bevilja åtkomst identitet till din SQL-databas, du måste lägga till den till en [Azure AD-grupp](../active-directory/fundamentals/active-directory-manage-groups.md). I Cloud Shell, lägga till den i en ny grupp med namnet _myAzureSQLDBAccessGroup_, visas i följande skript:

```azurecli-interactive
groupid=$(az ad group create --display-name myAzureSQLDBAccessGroup --mail-nickname myAzureSQLDBAccessGroup --query objectId --output tsv)
msiobjectid=$(az webapp identity show --resource-group myResourceGroup --name <app-name> --query principalId --output tsv)
az ad group member add --group $groupid --member-id $msiobjectid
az ad group member list -g $groupid
```

Om du vill se fullständiga JSON-utdata för varje kommando använder du parametrarna `--query objectId --output tsv`.

### <a name="grant-permissions-to-azure-ad-group"></a>Ge behörigheter till Azure AD-grupp

Öppna Cloud Shell och logga in på SQL Database med kommandot SQLCMD. Ersätt  _\<servernamn >_ med din SQL Database-servernamn  _\<db-name >_ namnge din app använder med databasen och  _\< aad-användarnamn >_ och  _\<aad-password >_ med autentiseringsuppgifter för din Azure AD-användare.

```azurecli-interactive
sqlcmd -S <server-name>.database.windows.net -d <db-name> -U <aad-user-name> -P "<aad-password>" -G -l 30
```

Kör följande kommandon för att lägga till Azure AD-gruppen och bevilja appen måste SQL-prompten för den databas du vill. Exempel: 

```sql
CREATE USER [myAzureSQLDBAccessGroup] FROM EXTERNAL PROVIDER;
ALTER ROLE db_datareader ADD MEMBER [myAzureSQLDBAccessGroup];
ALTER ROLE db_datawriter ADD MEMBER [myAzureSQLDBAccessGroup];
ALTER ROLE db_ddladmin ADD MEMBER [myAzureSQLDBAccessGroup];
GO
```

Skriv `EXIT` för att återgå till Cloud Shell-prompten.

### <a name="modify-connection-string"></a>Ändra anslutningssträngen

Kom ihåg att samma ändringar du gjort i `Web.config` fungerar med den hanterade identitet, så det enda du ska göra är att ta bort den befintliga anslutningssträngen i din app, som Visual Studio skapade och distribuera din app första gången. Använd följande kommando och Ersätt  *\<appens namn->* med namnet på din app.

```azurecli-interactive
az webapp config connection-string delete --resource-group myResourceGroup --name <app-name> --setting-names MyDbConnection
```

## <a name="publish-your-changes"></a>Publicera dina ändringar

Nu behöver du bara publicera ändringarna till Azure.

I **Solution Explorer**: Högerklicka på projektet **DotNetAppSqlDb** och välj **Publicera**.

![Publicera från Solution Explorer](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

Klicka på **Publicera** på publiceringssidan. När du ser din att göra-lista på den nya webbsidan ansluter din app till databasen med hjälp av den hanterade identiteten.

![Azure-app efter Code First Migration](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

Du ska nu kunna redigera att göra-listan som innan.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Nästa steg

Vad du lärt dig:

> [!div class="checklist"]
> * Aktivera hanterade identiteter
> * Ge SQL Database åtkomst till den hanterade identiteten
> * Konfigurera Entity Framework för att använda Azure AD-autentisering med SQL-databas
> * Anslut till SQL Database från Visual Studio med hjälp av Azure AD-autentisering

Gå vidare till nästa självstudie där du får lära dig att mappa ett anpassat DNS-namn till webbappen.

> [!div class="nextstepaction"]
> [Mappa ett befintligt anpassat DNS-namn till Azure App Service](app-service-web-tutorial-custom-domain.md)
