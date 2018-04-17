---
title: Skapa en .NET Core- och SQL Database-webbapp i Azure App Service | Microsoft Docs
description: Lär dig hur du får igång en .NET Core-app som fungerar i Azure App Service med anslutning till en SQL Database.
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
ms.date: 01/23/2018
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 2fb966d92dec713d5bf5ca48e8d15ae489227739
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="tutorial-build-a-net-core-and-sql-database-web-app-in-azure-app-service"></a>Självstudie: Skapa en .NET Core- och SQL Database-webbapp i Azure App Service

> [!NOTE]
> I den här artikeln distribueras en app till App Service i Windows. Om du vill distribuera en app till App Service i _Linux_ kan du läsa [Skapa en .NET Core- och SQL Database-webbapp i Azure App Service i Linux](./containers/tutorial-dotnetcore-sqldb-app.md).
>

Med [App Service ](app-service-web-overview.md) får du en automatiskt uppdaterad webbvärdtjänst i Azure med hög skalbarhet. Den här självstudiekursen visar hur du skapar en .NET Core-webbapp och ansluter den till en SQL Database. När du är klar har du en .NET Core MVC-app som körs i App Service.

![app som körs i App Service](./media/app-service-web-tutorial-dotnetcore-sqldb/azure-app-in-browser.png)

Du lär dig att:

> [!div class="checklist"]
> * skapa en SQL Database i Azure
> * ansluta en .NET Core-app till SQL Database
> * distribuera appen till Azure
> * uppdatera datamodellen och distribuera om appen
> * strömma diagnostikloggar från Azure
> * hantera appen i Azure-portalen.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

För att slutföra den här självstudien behöver du:

1. [Installera Git](https://git-scm.com/)
1. [Installera .NET Core SDK 1.1.2](https://github.com/dotnet/core/blob/master/release-notes/download-archives/1.1.2-download.md)

## <a name="create-local-net-core-app"></a>Skapa en lokal .NET Core-app

I det här steget konfigurerar du det lokala .NET Core-projektet.

### <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Använd kommandot `cd` för att komma till en arbetskatalog i terminalfönstret.

Kör följande kommandon för att klona exempellagringsplatsen och ändra dess rot.

```bash
git clone https://github.com/azure-samples/dotnetcore-sqldb-tutorial
cd dotnetcore-sqldb-tutorial
```

Exempelprojektet innehåller en grundläggande CRUD-app (create-read-update-delete) med [Entity Framework Core](https://docs.microsoft.com/ef/core/).

### <a name="run-the-application"></a>Köra programmet

Kör följande kommandon för att installera de nödvändiga paketen, köra databasmigreringar och starta programmet.

```bash
dotnet restore
dotnet ef database update
dotnet run
```

Gå till `http://localhost:5000` i en webbläsare. Välj länken **Skapa nytt** och skapa några _att-göra_-objekt.

![ansluter till SQL Database](./media/app-service-web-tutorial-dotnetcore-sqldb/local-app-in-browser.png)

Du kan när som helst stoppa .NET Core genom att trycka på `Ctrl+C` i terminalen.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-production-sql-database"></a>Skapa SQL Database för produktion

I det här steget skapar du en SQL Database i Azure. När appen har distribuerats till Azure används den här molndatabasen.

För SQL Database används [Azure SQL Database](/azure/sql-database/) i den här självstudien.

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)]

### <a name="create-a-sql-database-logical-server"></a>Skapa en logisk SQL Database-server

Skapa en logisk SQL Database-server i Cloud Shell med kommandot [`az sql server create`](/cli/azure/sql/server?view=azure-cli-latest#az_sql_server_create).

Ersätt platshållaren *\<server_name>* med ett unikt namn för SQL Database. Det här namnet används som en del av SQL Database-slutpunkten `<server_name>.database.windows.net`, så namnet måste vara unikt för alla logiska servrar i Azure. Namnet får endast innehålla gemener, siffror och bindestreck och måste vara mellan 3 och 50 tecken långt. Dessutom måste du ersätta *\<db_username>* och *\<db_password>* med ett giltigt användarnamn och lösenord. 


```azurecli-interactive
az sql server create --name <server_name> --resource-group myResourceGroup --location "West Europe" --admin-user <db_username> --admin-password <db_password>
```

När den logiska SQL Database-servern har skapats visar Azure CLI information som liknar följande exempel:

```json
{
  "administratorLogin": "sqladmin",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "<server_name>.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/<server_name>",
  "identity": null,
  "kind": "v12.0",
  "location": "westeurope",
  "name": "<server_name>",
  "resourceGroup": "myResourceGroup",
  "state": "Ready",
  "tags": null,
  "type": "Microsoft.Sql/servers",
  "version": "12.0"
}
```

### <a name="configure-a-server-firewall-rule"></a>Konfigurera en serverbrandväggsregel

Skapa en [brandväggsregel på servernivå för Azure SQL Database](../sql-database/sql-database-firewall-configure.md) via kommandot[`az sql server firewall create`](/cli/azure/sql/server/firewall-rule?view=azure-cli-latest#az_sql_server_firewall_rule_create). När både start-IP och slut-IP har angetts till 0.0.0.0 öppnas brandväggen endast för andra Azure-resurser. 

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server <server_name> --name AllowYourIp --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

### <a name="create-a-database"></a>Skapa en databas

Skapa en databas med en [S0-prestandanivå](../sql-database/sql-database-service-tiers.md) på servern med kommandot [`az sql db create`](/cli/azure/sql/db?view=azure-cli-latest#az_sql_db_create).

```azurecli-interactive
az sql db create --resource-group myResourceGroup --server <server_name> --name coreDB --service-objective S0
```

### <a name="create-connection-string"></a>Skapa anslutningssträng

Ersätt följande sträng med det *\<server_name>*, *\<db_username>* och *\<db_password>* du använde tidigare.

```
Server=tcp:<server_name>.database.windows.net,1433;Initial Catalog=coreDB;Persist Security Info=False;User ID=<db_username>;Password=<db_password>;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

Detta är anslutningssträngen för .NET Core-appen. Kopiera den för senare bruk.

## <a name="deploy-app-to-azure"></a>Distribuera app till Azure

I det här steget distribuerar du din SQL Database-anslutna .NET Core-app till App Service.

### <a name="configure-local-git-deployment"></a>Konfigurera lokal git-distribution

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Skapa en App Service-plan

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-no-h.md)]

### <a name="create-a-web-app"></a>Skapa en webbapp

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-dotnetcore-win-no-h.md)] 

### <a name="configure-an-environment-variable"></a>Konfigurera en miljövariabel

Ange anslutningssträngar för din Azure-app med hjälp av kommandot [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) i Cloud Shell. I följande kommando ska du ersätta *\<app name>* och parametern *\<connection_string>* med den anslutningssträng du skapade tidigare.

```azurecli-interactive
az webapp config connection-string set --resource-group myResourceGroup --name <app name> --settings MyDbConnection='<connection_string>' --connection-string-type SQLServer
```

Därefter anger du appinställningen `ASPNETCORE_ENVIRONMENT` till _Produktion_. Med den här inställningen kan du hålla reda på om du kör i Azure, eftersom du använder SQLite som lokal utvecklingsmiljö och SQL Database som Azure-miljö.

I följande exempel konfigureras appinställningen `ASPNETCORE_ENVIRONMENT` i Azure-webbappen. Ersätt platshållaren *\<app_name>*.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings ASPNETCORE_ENVIRONMENT="Production"
```

### <a name="connect-to-sql-database-in-production"></a>Ansluta till SQL Database i produktion

Öppna Startup.cs från din lokala lagringsplats och leta upp följande kod:

```csharp
services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlite("Data Source=localdatabase.db"));
```

Ersätt den med följande kod, som använder de miljövariabler du konfigurerade tidigare.

```csharp
// Use SQL Database if in Azure, otherwise, use SQLite
if(Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT") == "Production")
    services.AddDbContext<MyDatabaseContext>(options =>
            options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection")));
else
    services.AddDbContext<MyDatabaseContext>(options =>
            options.UseSqlite("Data Source=MvcMovie.db"));

// Automatically perform database migration
services.BuildServiceProvider().GetService<MyDatabaseContext>().Database.Migrate();
```

Om den här koden identifierar att den körs i produktion (vilket indikeras av Azure-miljön) så används den anslutningssträng du konfigurerade för att ansluta till SQL Database.

Anropet `Database.Migrate()` är till hjälp när det körs i Azure, eftersom de databaser som .NET Core-appen behöver skapas automatiskt baserat på migreringskonfigurationen. 

Spara dina ändringar och genomför den på Git-lagringsplatsen. 

```bash
git commit -am "connect to SQLDB in Azure"
```

### <a name="push-to-azure-from-git"></a>Skicka till Azure från Git

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

```bash
Counting objects: 98, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (92/92), done.
Writing objects: 100% (98/98), 524.98 KiB | 5.58 MiB/s, done.
Total 98 (delta 8), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: .
remote: Updating submodules.
remote: Preparing deployment for commit id '0c497633b8'.
remote: Generating deployment script.
remote: Project file path: ./DotNetCoreSqlDb.csproj
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling ASP.NET Core Web Application deployment.
remote: .
remote: .
remote: .
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
remote: App container will begin restart within 10 seconds.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
 * [new branch]      master -> master
```

### <a name="browse-to-the-azure-web-app"></a>Bläddra till Azure-webbappen

Bläddra till den distribuerade webbappen via webbläsaren.

```bash
http://<app_name>.azurewebsites.net
```

Lägg till några att-göra-uppgifter.

![app som körs i App Service](./media/app-service-web-tutorial-dotnetcore-sqldb/azure-app-in-browser.png)

**Grattis!** Du kör en datadriven .NET Core-app i App Service.

## <a name="update-locally-and-redeploy"></a>Uppdatera lokalt och omdistribuera

I det här steget gör du en ändring i ditt databasschema och publicerar den till Azure.

### <a name="update-your-data-model"></a>Uppdatera datamodellen

Öppna _Models\Todo.cs_ i kodredigeraren. Lägg till följande egenskap i klassen `ToDo`:

```csharp
public bool Done { get; set; }
```

### <a name="run-code-first-migrations-locally"></a>Kör Code First Migrations lokalt

Kör några kommandon och gör uppdateringar i den lokala databasen.

```bash
dotnet ef migrations add AddProperty
```

Uppdatera den lokala databasen:

```bash
dotnet ef database update
```

### <a name="use-the-new-property"></a>Använda den nya egenskapen

Gör några ändringar i koden så att du använder egenskapen `Done`. För att göra självstudien enklare ska du bara ändra vyerna `Index` och `Create` så att du ser hur egenskapen fungerar.

Öppna _Controllers\TodosController.cs_.

Leta rätt på metoden `Create()` och lägg till `Done` i listan med egenskaper för attributet `Bind`. När du är klar ser signaturen för metoden `Create()` ut som följande kod:

```csharp
public async Task<IActionResult> Create([Bind("ID,Description,CreatedDate,Done")] Todo todo)
```

Öppna _Views\Todos\Create.cshtml_.

I Razor-koden bör du se ett `<div class="form-group">`-element för `Description` och sedan ett annat `<div class="form-group">`-element för `CreatedDate`. Direkt efter dessa två element ska du lägga till ett annat `<div class="form-group">`-element för `Done`:

```csharp
<div class="form-group">
    <label asp-for="Done" class="col-md-2 control-label"></label>
    <div class="col-md-10">
        <input asp-for="Done" class="form-control" />
        <span asp-validation-for="Done" class="text-danger"></span>
    </div>
</div>
```

Öppna _Views\Todos\Index.cshtml_.

Sök efter det tomma `<th></th>`-elementet. Lägg till följande Razor-kod direkt ovanför det här elementet:

```csharp
<th>
    @Html.DisplayNameFor(model => model.Done)
</th>
```

Hitta elementet `<td>` som innehåller ”tag helpers” `asp-action`. Lägg till följande Razor-kod direkt ovanför det här elementet:

```csharp
<td>
    @Html.DisplayFor(modelItem => item.Done)
</td>
```

Det är allt som krävs för att du ska se ändringarna i vyerna `Index` och `Create`.

### <a name="test-your-changes-locally"></a>Testa ändringarna lokalt

Kör appen lokalt.

```bash
dotnet run
```

Öppna webbläsaren och navigera till `http://localhost:5000/`. Du kan nu lägga till en att-göra-uppgift och markera **Klart**. Den ska sedan visas på din startsida som en slutförd uppgift. Kom ihåg att vyn `Edit` inte innehåller fältet `Done` eftersom du inte ändrade vyn `Edit`.

### <a name="publish-changes-to-azure"></a>Publicera ändringar till Azure

```bash
git commit -am "added done field"
git push azure master
```

När `git push` har slutförts kan du gå till Azure-webbappen och prova att använda de nya funktionerna.

![Azure-webbapp efter Code First Migration](./media/app-service-web-tutorial-dotnetcore-sqldb/this-one-is-done.png)

Alla befintliga att-göra-uppgifter visas fortfarande. När du publicerar om .NET Core-appen går inte befintliga data i SQL Database förlorade. Med Entity Framework Core Migrations ändras endast dataschemat, så att befintliga data lämnas intakta.

## <a name="manage-your-azure-web-app"></a>Hantera din Azure-webbapp

Gå till [Azure Portal](https://portal.azure.com) för att se den webbapp du skapade.

Klicka på **Apptjänster** på menyn till vänster och klicka sedan på namnet på din Azure-webbapp.

![Navigera till webbappen på Azure Portal](./media/app-service-web-tutorial-dotnetcore-sqldb/access-portal.png)

Som standard visar portalen dina webbappar på sidan **Översikt**. På den här sidan får du en översikt över hur det går för appen. Här kan du också utföra grundläggande hanteringsåtgärder som att bläddra, stoppa, starta, starta om och ta bort. På flikarna till vänster på sidan kan du se olika konfigurationssidor som du kan öppna.

![App Service-sidan på Azure Portal](./media/app-service-web-tutorial-dotnetcore-sqldb/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>
## <a name="next-steps"></a>Nästa steg

Vad du lärt dig:

> [!div class="checklist"]
> * skapa en SQL Database i Azure
> * ansluta en .NET Core-app till SQL Database
> * distribuera appen till Azure
> * uppdatera datamodellen och distribuera om appen
> * strömma loggar från Azure till terminalen
> * hantera appen i Azure-portalen.

Gå vidare till nästa självstudie där du får lära dig att mappa ett anpassat DNS-namn till webbappen.

> [!div class="nextstepaction"]
> [Mappa ett befintligt anpassat DNS-namn till Azure Web Apps](app-service-web-tutorial-custom-domain.md)