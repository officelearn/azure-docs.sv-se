---
title: 'Självstudie: ASP.NET Core med SQL Database'
description: Lär dig hur du skaffar en .NET Core-app som fungerar i Azure App Service, med anslutning till SQL Database.
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 04/23/2020
ms.custom: mvc, cli-validate, seodec18
ms.openlocfilehash: 4e032ae5b5133bf1dab05bdccdc81af7bd9855ca
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84013286"
---
# <a name="tutorial-build-an-aspnet-core-and-sql-database-app-in-azure-app-service"></a>Självstudie: Bygg en ASP.NET Core-och SQL Database-app i Azure App Service

> [!NOTE]
> I den här artikeln distribueras en app till App Service i Windows. Om du vill distribuera en app till App Service i _Linux_ kan du läsa [Skapa en .NET Core- och SQL Database-app i Azure App Service i Linux](./containers/tutorial-dotnetcore-sqldb-app.md).
>

Med [App Service ](overview.md) får du en automatiskt uppdaterad webbvärdtjänst i Azure med hög skalbarhet. I den här självstudien visas hur du skapar en .NET Core-app och ansluter den till SQL Database. När du är klar har du en .NET Core MVC-app som körs i App Service.

![app som körs i App Service](./media/app-service-web-tutorial-dotnetcore-sqldb/azure-app-in-browser.png)

I den här guiden får du lära dig att:

> [!div class="checklist"]
>
> * Skapa en databas i Azure SQL Database
> * ansluta en .NET Core-app till SQL Database
> * distribuera appen till Azure
> * uppdatera datamodellen och distribuera om appen
> * strömma diagnostikloggar från Azure
> * hantera appen i Azure-portalen.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här kursen behöver du:

* <a href="https://git-scm.com/" target="_blank">Installera Git</a>
* <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank">Installera den senaste .NET Core 3,1 SDK</a>

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

### <a name="run-the-application"></a>Kör programmet

Kör följande kommandon för att installera de nödvändiga paketen, köra databasmigreringar och starta programmet.

```bash
dotnet tool install -g dotnet-ef
dotnet ef database update
dotnet run
```

Gå till `http://localhost:5000` i en webbläsare. Välj länken **Skapa nytt** och skapa några _att-göra_-objekt.

![ansluter till SQL Database](./media/app-service-web-tutorial-dotnetcore-sqldb/local-app-in-browser.png)

Du kan när som helst stoppa .NET Core genom att trycka på `Ctrl+C` i terminalen.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-database-in-azure-sql-database"></a>Skapa en databas i Azure SQL Database

I det här steget skapar du en databas i [Azure SQL Database](/azure/sql-database/). När din app distribueras till Azure används den här databasen.

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)]

### <a name="create-a-server-in-azure-sql-database"></a>Skapa en server i Azure SQL Database

I Cloud Shell skapar du en [Server](../azure-sql/database/logical-servers.md) i Azure SQL Database med [`az sql server create`](/cli/azure/sql/server?view=azure-cli-latest#az-sql-server-create) kommandot. En server är en logisk konstruktion som innehåller en grupp databaser som hanteras som en grupp.

Ersätt *\<server-name>* plats hållaren med ett *unikt* namn. Det här namnet används som en del av den globalt unika SQL Database slut punkten `<server-name>.database.windows.net` . Giltiga tecken är `a` - `z` , `0` - `9` , `-` . Du kan också ersätta *\<db-username>* och *\<db-password>* med ett användar namn och lösen ord som du själv väljer.

```azurecli-interactive
az sql server create --name <server-name> --resource-group myResourceGroup --location "West Europe" --admin-user <db-username> --admin-password <db-password>
```

När servern skapas visar Azure CLI information som liknar följande exempel:

<pre>
{
  "administratorLogin": "&lt;db-username&gt;",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "&lt;server-name&gt;.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/&lt;server-name&gt;",
  "identity": null,
  "kind": "v12.0",
  "location": "westeurope",
  "name": "&lt;server-name&gt;",
  "resourceGroup": "myResourceGroup",
  "state": "Ready",
  "tags": null,
  "type": "Microsoft.Sql/servers",
  "version": "12.0"
}
</pre>

### <a name="configure-a-server-firewall-rule"></a>Konfigurera en serverbrandväggsregel

Skapa en [brand Väggs regel på server nivå](../azure-sql/database/firewall-configure.md) med hjälp av [`az sql server firewall create`](/cli/azure/sql/server/firewall-rule?view=azure-cli-latest#az-sql-server-firewall-rule-create) kommandot. När både start-IP och slut-IP har angetts till 0.0.0.0 öppnas brandväggen endast för andra Azure-resurser.

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server <server-name> --name AllowAzureIps --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP]
> Du kan begränsa brandväggsregeln ännu mer genom att [endast använda de utgående IP-adresser som används av din app](overview-inbound-outbound-ips.md#find-outbound-ips).

Kör kommandot igen i Cloud Shell för att tillåta åtkomst från den lokala datorn genom att ersätta *\<your-ip-address>* med [din lokala IPv4 IP-adress](https://www.whatsmyip.org/).

```azurecli-interactive
az sql server firewall-rule create --name AllowLocalClient --server <mysql_server_name> --resource-group myResourceGroup --start-ip-address=<your-ip-address> --end-ip-address=<your-ip-address>
```

### <a name="create-a-database-in-azure-sql-database"></a>Skapa en databas i Azure SQL Database

Skapa en databas med en [S0-prestandanivå](../azure-sql/database/service-tiers-dtu.md) på servern med kommandot [`az sql db create`](/cli/azure/sql/db?view=azure-cli-latest#az-sql-db-create).

```azurecli-interactive
az sql db create --resource-group myResourceGroup --server <server-name> --name coreDB --service-objective S0
```

### <a name="create-connection-string"></a>Skapa anslutningssträng

Hämta anslutnings strängen med hjälp av [`az sql db show-connection-string`](/cli/azure/sql/db?view=azure-cli-latest#az-sql-db-show-connection-string) kommandot.

```azurecli-interactive
az sql db show-connection-string --client ado.net --server cephalin-core --name coreDB
```

I kommandot utdata, Ersätt *\<username>* och *\<password>* med de autentiseringsuppgifter för databas administratören som du använde tidigare.

Detta är anslutningssträngen för .NET Core-appen. Kopiera den för senare bruk.

### <a name="configure-app-to-connect-to-the-database-in-azure"></a>Konfigurera appen för att ansluta till databasen i Azure

Öppna Startup.cs från din lokala lagringsplats och leta upp följande kod:

```csharp
services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlite("Data Source=localdatabase.db"));
```

Ersätt det med följande kod.

```csharp
services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection")));
```

> [!IMPORTANT]
> För produktionsappar som behöver skala ut följer du bästa praxis i avsnittet om att [tillämpa migreringar i produktion](/aspnet/core/data/ef-rp/migrations#applying-migrations-in-production).

### <a name="run-database-migrations-to-the-database-in-azure"></a>Köra databas migreringar till databasen i Azure

Din app är för närvarande ansluten till en lokal SQLite-databas. Nu när du konfigurerade en Azure SQL Database, återskapar du den första migreringen för att rikta den mot målet.

Kör följande kommandon från lagrings platsens rot. Ersätt *\<connection-string>* med den anslutnings sträng som du skapade tidigare.

```
# Delete old migrations
rm Migrations -r
# Recreate migrations
dotnet ef migrations add InitialCreate

# Set connection string to production database
# PowerShell
$env:ConnectionStrings:MyDbConnection="<connection-string>"
# CMD (no quotes)
set ConnectionStrings:MyDbConnection=<connection-string>
# Bash (no quotes)
export ConnectionStrings__MyDbConnection=<connection-string>

# Run migrations
dotnet ef database update
```

### <a name="run-app-with-new-configuration"></a>Kör appen med ny konfiguration

Nu när migreringen av databasen körs på produktions databasen testar du appen genom att köra:

```
dotnet run
```

Gå till `http://localhost:5000` i en webbläsare. Välj länken **Skapa nytt** och skapa några _att-göra_-objekt. Appen läser nu och skriver data till produktions databasen.

Spara dina lokala ändringar och spara dem i git-lagringsplatsen.

```bash
git add .
git commit -m "connect to SQLDB in Azure"
```

Nu är du redo att distribuera din kod.

## <a name="deploy-app-to-azure"></a>Distribuera app till Azure

I det här steget distribuerar du din SQL Database-anslutna .NET Core-app till App Service.

### <a name="configure-local-git-deployment"></a>Konfigurera lokal git-distribution

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Skapa en App Service-plan

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-no-h.md)]

### <a name="create-a-web-app"></a>Skapa en webbapp

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-dotnetcore-win-no-h.md)]

### <a name="configure-connection-string"></a>Konfigurera anslutnings sträng

Om du vill ange anslutnings strängar för din Azure-app använder du [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) kommandot i Cloud Shell. I följande kommando ersätter *\<app-name>* du, samt *\<connection-string>* parametern med den anslutnings sträng som du skapade tidigare.

```azurecli-interactive
az webapp config connection-string set --resource-group myResourceGroup --name <app-name> --settings MyDbConnection="<connection-string>" --connection-string-type SQLAzure
```

I ASP.NET Core kan du använda den här namngivna anslutnings strängen ( `MyDbConnection` ) med standard mönstret, till exempel vilken anslutnings sträng som anges i *appSettings. JSON*. I det här fallet `MyDbConnection` definieras även i din *appSettings. JSON*. När du kör i App Service prioriteras den anslutnings sträng som definieras i App Service över anslutnings strängen som definierats i *appSettings. JSON*. Koden använder *appSettings. JSON* -värdet under lokal utveckling och samma kod använder App Service-värdet när det distribueras.

Information om hur anslutnings strängen refereras i din kod finns i [Konfigurera appen för att ansluta till produktions databasen](#configure-app-to-connect-to-the-database-in-azure).

### <a name="push-to-azure-from-git"></a>Skicka till Azure från Git

[!INCLUDE [push-to-azure-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

<pre>
Enumerating objects: 268, done.
Counting objects: 100% (268/268), done.
Compressing objects: 100% (171/171), done.
Writing objects: 100% (268/268), 1.18 MiB | 1.55 MiB/s, done.
Total 268 (delta 95), reused 251 (delta 87), pack-reused 0
remote: Resolving deltas: 100% (95/95), done.
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '64821c3558'.
remote: Generating deployment script.
remote: Project file path: .\DotNetCoreSqlDb.csproj
remote: Generating deployment script for ASP.NET MSBuild16 App
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling ASP.NET Core Web Application deployment with MSBuild16.
remote: .
remote: .
remote: .
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Triggering recycle (preview mode disabled).
remote: App container will begin restart within 10 seconds.
To https://&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git
 * [new branch]      master -> master
</pre>

### <a name="browse-to-the-azure-app"></a>Bläddra till Azure-appen

Bläddra till den distribuerade appen i webbläsaren.

```bash
http://<app-name>.azurewebsites.net
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

### <a name="rerun-database-migrations"></a>Kör Database-migrering igen

Kör några kommandon för att göra uppdateringar av produktions databasen.

```bash
dotnet ef migrations add AddProperty
dotnet ef database update
```

### <a name="use-the-new-property"></a>Använda den nya egenskapen

Gör några ändringar i koden så att du använder egenskapen `Done`. För att göra självstudien enklare ska du bara ändra vyerna `Index` och `Create` så att du ser hur egenskapen fungerar.

Öppna _Controllers\TodosController.cs_.

Leta rätt på metoden `Create([Bind("ID,Description,CreatedDate")] Todo todo)` och lägg till `Done` i listan med egenskaper för attributet `Bind`. När du är klar ser signaturen för metoden `Create()` ut som följande kod:

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
git add .
git commit -m "added done field"
git push azure master
```

När du `git push` är klar navigerar du till din app service-app och försöker lägga till ett att göra-objekt och checken är **klar**.

![Azure-app efter Code First Migration](./media/app-service-web-tutorial-dotnetcore-sqldb/this-one-is-done.png)

Alla befintliga att-göra-uppgifter visas fortfarande. När du återpublicerar ASP.NET Core-appen går inte befintliga data i SQL Database förlorade. Med Entity Framework Core Migrations ändras endast dataschemat, så att befintliga data lämnas intakta.

## <a name="stream-diagnostic-logs"></a>Strömma diagnostikloggar

När ASP.NET Core-appen körs i Azure App Service kan du skicka konsolloggarna till Cloud Shell. På så sätt kan du få samma diagnostikmeddelanden för att felsöka programfel.

Exempelprojektet följer redan riktlinjerna i [ASP.NET Core-loggning i Azure](https://docs.microsoft.com/aspnet/core/fundamentals/logging#azure-app-service-provider) med två konfigurationsändringar:

* Innehåller en referens till `Microsoft.Extensions.Logging.AzureAppServices` i *DotNetCoreSqlDb.csproj*.
* Anrop `loggerFactory.AddAzureWebAppDiagnostics()` i *program.cs*.

För att ange [loggnivå](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-level) för ASP.NET Core i App Service till `Information` från standardnivån `Error`använder du kommandot [`az webapp log config`](/cli/azure/webapp/log?view=azure-cli-latest#az-webapp-log-config) i Cloud Shell.

```azurecli-interactive
az webapp log config --name <app-name> --resource-group myResourceGroup --application-logging true --level information
```

> [!NOTE]
> Projektets loggnivå är redan inställd på `Information` i *appsettings.json*.

Om du vill starta logg strömningen använder du [`az webapp log tail`](/cli/azure/webapp/log?view=azure-cli-latest#az-webapp-log-tail) kommandot i Cloud Shell.

```azurecli-interactive
az webapp log tail --name <app-name> --resource-group myResourceGroup
```

Uppdatera Azure-app i webbläsaren så hämtas webbtrafik när loggströmningen har startats. Du kan nu se konsolloggarna som skickas till terminalen. Om du inte ser konsolloggarna omedelbart kan du titta efter igen efter 30 sekunder.

Om du vill stoppa logg strömningen när som helst skriver du `Ctrl` + `C` .

Mer information om att anpassa ASP.NET Core-loggar finns i [Loggning i ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging).

## <a name="manage-your-azure-app"></a>Hantera din Azure-app

Om du vill se den app som du skapade går du till [Azure Portal](https://portal.azure.com)och söker efter och väljer **app Services**.

![Välj App Services i Azure Portal](./media/app-service-web-tutorial-dotnetcore-sqldb/app-services.png)

På sidan **app Services** väljer du namnet på din Azure-App.

![Portalnavigering till Azure-app](./media/app-service-web-tutorial-dotnetcore-sqldb/access-portal.png)

Portalen visar som standard dina webbappar på sidan **Översikt**. På den här sidan får du en översikt över hur det går för appen. Här kan du också utföra grundläggande hanteringsåtgärder som att bläddra, stoppa, starta, starta om och ta bort. På flikarna till vänster på sidan kan du se olika konfigurationssidor som du kan öppna.

![App Service-sidan på Azure Portal](./media/app-service-web-tutorial-dotnetcore-sqldb/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>

## <a name="next-steps"></a>Nästa steg

Vad du lärt dig:

> [!div class="checklist"]
>
> * Skapa en databas i Azure SQL Database
> * ansluta en .NET Core-app till SQL Database
> * distribuera appen till Azure
> * uppdatera datamodellen och distribuera om appen
> * strömma loggar från Azure till terminalen
> * hantera appen i Azure-portalen.

Gå vidare till nästa självstudie där du får lära dig att mappa ett anpassat DNS-namn till appen.

> [!div class="nextstepaction"]
> [Självstudie: mappa ett anpassat DNS-namn till din app](app-service-web-tutorial-custom-domain.md)
