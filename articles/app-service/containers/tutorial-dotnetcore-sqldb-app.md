---
title: "Skapa en .NET Core och SQL Database-webbapp i Azure App Service på Linux | Microsoft Docs"
description: "Lär dig hur du hämtar en app för .NET Core som arbetar i Azure App Service på Linux, med anslutning till en SQL-databas."
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: syntaxc4
editor: 
ms.assetid: 0b4d7d0e-e984-49a1-a57a-3c0caa955f0e
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 10/10/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: d6c679518bfc712e6a08ffae722b0cc5d2b038aa
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2017
---
# <a name="build-a-net-core-and-sql-database-web-app-in-azure-app-service-on-linux"></a>Skapa en .NET Core och SQL Database-webbapp i Azure App Service på Linux

Med [App Service i Linux](app-service-linux-intro.md) får du en mycket skalbar och automatiskt uppdaterad webbvärdtjänst som utgår från operativsystemet Linux. Den här kursen visar hur du skapar ett .NET Core-webbprogram och ansluta till en SQL-databas. När du är klar har du en .NET Core MVC-app som körs i Apptjänst i Linux.

![appar som körs i App Service på Linux](./media/tutorial-dotnetcore-sqldb-app/azure-app-in-browser.png)

Du lär dig hur du:

> [!div class="checklist"]
> * Skapa en SQL-databas i Azure
> * Ansluta en .NET Core app till SQL-databas
> * Distribuera appen till Azure
> * Uppdatera datamodellen och distribuera appen
> * Dataströmmen diagnostiska loggar från Azure
> * Hantera appen i Azure-portalen

## <a name="prerequisites"></a>Krav

För att slutföra den här kursen behöver du:

1. [Installera Git](https://git-scm.com/)
1. [Installera .NET Core SDK 1.1.2](https://github.com/dotnet/core/blob/master/release-notes/download-archives/1.1.2-download.md)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-local-net-core-app"></a>Skapa lokal .NET Core-app

I det här steget kan du ställa in det lokala .NET Core-projektet.

### <a name="clone-the-sample-application"></a>Klona exempelprogrammet

I fönstret terminal `cd` till en arbetskatalog.

Kör följande kommandon för att klona lagringsplatsen exempel och ändra till roten.

```bash
git clone https://github.com/azure-samples/dotnetcore-sqldb-tutorial
cd dotnetcore-sqldb-tutorial
```

Exempelprojektet innehåller en grundläggande CRUD (skapa-Läs-Uppdatera-ta bort) app med hjälp av [Entity Framework Core](https://docs.microsoft.com/ef/core/).

### <a name="run-the-application"></a>Köra programmet

Kör följande kommandon för att installera de nödvändiga paketen, kör databasen migreringar och starta programmet.

```bash
dotnet restore
dotnet ef database update
dotnet run
```

Gå till `http://localhost:5000` i en webbläsare. Välj den **Skapa nytt** länka och skapa ett par _uppgiften_ objekt.

![upprätta anslutningen till SQL-databas](./media/tutorial-dotnetcore-sqldb-app/local-app-in-browser.png)

Stoppa .NET Core när som helst genom att trycka på `Ctrl+C` i terminalen.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-production-sql-database"></a>Skapa produktion SQL-databas

I det här steget skapar du en SQL-databas i Azure. När appen har distribuerats till Azure, använder den här databasen i molnet.

För SQL-databas som den här kursen använder [Azure SQL Database](/azure/sql-database/).

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-no-h.md)]

### <a name="create-a-sql-database-logical-server"></a>Skapa en logisk SQL Database-server

Molnet Shell, skapa en logisk SQL Database-server med den [az sql-servern skapa](/cli/azure/sql/server?view=azure-cli-latest#az_sql_server_create) kommando.

Ersätt den  *\<servernamn >* med ett unikt namn för SQL-databas. Det här namnet används som en del av SQL Database-slutpunkten `<server_name>.database.windows.net`, så namnet måste vara unikt för alla logiska servrar i Azure. Namnet måste innehålla endast små bokstäver, siffror och bindestreck (-) och måste vara mellan 3 och 50 tecken. Dessutom måste du ersätta  *\<db_username >* och  *\<db_password >* med ett användarnamn och lösenord du väljer. 


```azurecli-interactive
az sql server create --name <server_name> --resource-group myResourceGroup --location "West Europe" --admin-user <db_username> --admin-password <db_password>
```

När den logiska SQL Database-servern har skapats visas Azure CLI information liknar följande exempel:

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

Skapa en [Azure SQL Database-brandväggsregel på servernivå](../../sql-database/sql-database-firewall-configure.md) med kommandot [az sql server firewall create](/cli/azure/sql/server/firewall-rule?view=azure-cli-latest#az_sql_server_firewall_rule_create). När både första IP- och slut-IP är inställda på 0.0.0.0 ska öppnas i brandväggen endast för andra Azure-resurser. 

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server <server_name> --name AllowYourIp --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

### <a name="create-a-database"></a>Skapa en databas

Skapa en databas med en [S0-prestandanivå](../../sql-database/sql-database-service-tiers.md) på servern med kommandot [az sql db create](/cli/azure/sql/db?view=azure-cli-latest#az_sql_db_create).

```azurecli-interactive
az sql db create --resource-group myResourceGroup --server <server_name> --name coreDB --service-objective S0
```

### <a name="create-connection-string"></a>Skapa anslutningssträng

Ersätt följande sträng med den  *\<servernamn >*,  *\<db_username >*, och  *\<db_password >* du använda tidigare.

```
Server=tcp:<server_name>.database.windows.net,1433;Initial Catalog=coreDB;Persist Security Info=False;User ID=<db_username>;Password=<db_password>;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

Detta är anslutningssträngen för .NET Core-app. Kopiera den för användning senare.

## <a name="deploy-app-to-azure"></a>Distribuera appen till Azure

I det här steget kan distribuera du ditt SQL Database-anslutna .NET Core-program till App Service på Linux.

### <a name="configure-local-git-deployment"></a>Konfigurera lokal git-distribution

[!INCLUDE [Configure a deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Skapa en App Service-plan

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Skapa en webbapp

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-dotnetcore-no-h.md)] 

### <a name="configure-an-environment-variable"></a>Konfigurera en miljövariabel

Ange anslutningssträngar för din Azure-app genom att använda den [az webapp konfigurationsuppsättning appsettings](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) i molnet Shell. I följande kommando ersätter  *\<appnamn >*, samt de  *\<anslutningssträng >* parameter med den anslutningssträng som du skapade tidigare.

```azurecli-interactive
az webapp config connection-string set --resource-group myResourceGroup --name <app name> --settings MyDbConnection='<connection_string>' --connection-string-type SQLServer
```

Därefter anger du `ASPNETCORE_ENVIRONMENT` appinställningen _produktion_. Den här inställningen kan du vet om du använder i Azure, eftersom du använder SQLLite för din miljö för lokal utveckling och SQL-databas för Azure-miljön.

I följande exempel konfigureras en `ASPNETCORE_ENVIRONMENT` appinställningen i ditt Azure webbapp. Ersätt den  *\<appnamn >* platshållare.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings ASPNETCORE_ENVIRONMENT="Production"
```

### <a name="connect-to-sql-database-in-production"></a>Anslut till SQL Database i produktion

Öppna Startup.cs i lokala databasen, och Sök efter följande kod:

```csharp
services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlite("Data Source=localdatabase.db"));
```

Ersätt den med följande kod, som använder miljövariabler som du tidigare har konfigurerat.

```csharp
// Use SQL Database if in Azure, otherwise, use SQLLite
if(Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT") == "Production")
    services.AddDbContext<DotNetCoreSqlDbContext>(options =>
            options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection")));
else
    services.AddDbContext<DotNetCoreSqlDbContext>(options =>
            options.UseSqlite("Data Source=MvcMovie.db"));

// Automatically perform database migration
services.BuildServiceProvider().GetService<DotNetCoreSqlDbContext>().Database.Migrate();
```

Om den här koden upptäcker att den körs i produktion (som anger Azure-miljön) och sedan använder anslutningssträngen för den konfigurerats för att ansluta till SQL-databasen.

Den `Database.Migrate()` anrop hjälper dig när det körs i Azure, eftersom den automatiskt skapar databaserna som dina .NET Core app behov, baserat på dess konfiguration för migrering. 

Spara ändringarna.

### <a name="push-to-azure-from-git"></a>Skicka till Azure från Git

[!INCLUDE [app-service-plan-no-h](../../../includes/app-service-web-git-push-to-azure-no-h.md)]

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

Bläddra till den distribuerade webbappens med hjälp av webbläsaren.

```bash
http://<app_name>.azurewebsites.net
```

Lägg till några arbetsuppgifter.

![appar som körs i App Service på Linux](./media/tutorial-dotnetcore-sqldb-app/azure-app-in-browser.png)

**Grattis!** Du kör en datadrivna .NET Core-app i App Service på Linux.

## <a name="update-locally-and-redeploy"></a>Uppdatera lokalt och distribuera

I det här steget gör en ändring i ditt databasschema och publicera den på Azure.

### <a name="update-your-data-model"></a>Uppdatera datamodellen

Öppna _Models\Todo.cs_ i kodredigeraren. Lägg till följande egenskap till den `ToDo` klass:

```csharp
public bool Done { get; set; }
```

### <a name="run-code-first-migrations-locally"></a>Kör Code First Migrations lokalt

Kör några kommandon för att göra uppdateringar till den lokala databasen.

```bash
dotnet ef migrations add AddProperty
```

Uppdatera den lokala databasen:

```bash
dotnet ef database update
```

### <a name="use-the-new-property"></a>Använd den nya egenskapen

Göra några ändringar i din kod för att använda den `Done` egenskapen. För enkelhetens i den här kursen ska du bara ändra det `Index` och `Create` vyer för att se egenskapen i åtgärden.

Öppna _Controllers\TodosController.cs_.

Hitta de `Create()` metod och Lägg till `Done` i listan över egenskaper i den `Bind` attribut. När du är klar din `Create()` Metodsignaturen ser ut som följande kod:

```csharp
public async Task<IActionResult> Create([Bind("ID,Description,CreatedDate,Done")] Todo todo)
```

Öppna _Views\Todos\Create.cshtml_.

I Razor-kod, bör du se en `<div class="form-group">` element för `Description`, och sedan en annan `<div class="form-group">` element för `CreatedDate`. Direkt efter dessa två element, lägga till en annan `<div class="form-group">` element för `Done`:

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

Sök efter tomma `<th></th>` element. Lägg till följande kod i Razor ovanför det här elementet:

```csharp
<th>
    @Html.DisplayNameFor(model => model.Done)
</th>
```

Hitta de `<td>` element som innehåller den `asp-action` tagga hjälpprogram. Lägg till följande kod i Razor ovanför det här elementet:

```csharp
<td>
    @Html.DisplayFor(modelItem => item.CreatedDate)
</td>
```

Det är allt du behöver se ändringarna i den `Index` och `Create` vyer.

### <a name="test-your-changes-locally"></a>Testa ändringarna lokalt

Köra appen lokalt.

```bash
dotnet run
```

I din webbläsare, navigerar du till `http://localhost:5000/`. Du kan nu lägga till en att göra-objekt och kontrollera **klar**. Sedan ska den visas i din startsida som en slutförd artikel. Kom ihåg att den `Edit` vyn inte visa den `Done` fältet, eftersom du inte ändra den `Edit` vyn.

### <a name="publish-changes-to-azure"></a>Publicera ändringar i Azure

```bash

git commit -am "added done field"
git push azure master
```

En gång i `git push` är klar, gå till Azure webbapp och prova att använda de nya funktionerna.

![Azure-webbapp efter koden första migrering](./media/tutorial-dotnetcore-sqldb-app/this-one-is-done.png)

Alla befintliga arbetsuppgifter fortfarande visas. När du publicerar appen .NET Core är befintliga data i SQL-databasen inte förlorade. Dessutom Entity Framework Core migreringar endast ändrar dataschemat och lämnar företaget dina befintliga data.

## <a name="manage-your-azure-web-app"></a>Hantera Azure-webbapp

Gå till den [Azure-portalen](https://portal.azure.com) att se att webbappen som du skapade.

Klicka på **Apptjänster** på menyn till vänster och klicka sedan på namnet på din Azure-webbapp.

![Navigera till webbappen på Azure Portal](./media/tutorial-dotnetcore-sqldb-app/access-portal.png)

Som standard visas på portalen ditt webbprogram **översikt** sidan. På den här sidan får du en översikt över hur det går för appen. Här kan du också utföra grundläggande hanteringsåtgärder som att bläddra, stoppa, starta, starta om och ta bort. Flikar till vänster på sidan Visa sidorna annan konfiguration som du kan öppna.

![App Service-sidan på Azure Portal](./media/tutorial-dotnetcore-sqldb-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

<a name="next"></a>
## <a name="next-steps"></a>Nästa steg

Vad du lärt dig:

> [!div class="checklist"]
> * Skapa en SQL-databas i Azure
> * Ansluta en .NET Core app till SQL-databas
> * Distribuera appen till Azure
> * Uppdatera datamodellen och distribuera appen
> * Dataströmmen loggas från Azure till terminalen
> * Hantera appen i Azure-portalen

Gå vidare till nästa kurs att lära dig hur du mappar en anpassad DNS-namn till ditt webbprogram.

> [!div class="nextstepaction"]
> [Mappa ett befintligt anpassat DNS-namn till Azure Web Apps](../app-service-web-tutorial-custom-domain.md)