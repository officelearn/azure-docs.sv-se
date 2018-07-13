---
title: Skapa en Ruby- och Postgres-webbapp i Azure App Service i Linux | Microsoft Docs
description: Lär dig hur du får igång en Ruby-app som fungerar i Azure med anslutning till en PostgreSQL-databas i Azure.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
ms.service: app-service-web
ms.workload: web
ms.devlang: ruby
ms.topic: tutorial
ms.date: 06/15/2018
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 927c8f6d0fefbc592999487217c41aeecc96b0d9
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2018
ms.locfileid: "37950989"
---
# <a name="build-a-ruby-and-postgres-web-app-in-azure-app-service-on-linux"></a>Skapa en Ruby- och Postgres-webbapp i Azure App Service i Linux

Med [App Service i Linux](app-service-linux-intro.md) får du en mycket skalbar och automatiskt uppdaterad webbvärdtjänst som utgår från operativsystemet Linux. I den här självstudien visas hur du skapar en Ruby-webbapp och ansluter den till en PostgreSQL-databas. När du är klar har du en [Ruby on Rails](http://rubyonrails.org/)-app som körs i App Service på Linux.

![Ruby on Rails-appen körs i Azure App Service](./media/tutorial-ruby-postgres-app/complete-checkbox-published.png)

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Skapa en PostgreSQL-databas i Azure
> * Ansluta en Ruby on Rails-app till PostgreSQL
> * distribuera appen till Azure
> * uppdatera datamodellen och distribuera om appen
> * strömma diagnostikloggar från Azure
> * hantera appen i Azure-portalen.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

För att slutföra den här självstudien behöver du:

* [Installera Git](https://git-scm.com/)
* [Installera Ruby 2.3](https://www.ruby-lang.org/en/documentation/installation/)
* [Installera Ruby on Rails 5.1](http://guides.rubyonrails.org/v5.1/getting_started.html)
* [Installera och kör PostgreSQL](https://www.postgresql.org/download/)

## <a name="prepare-local-postgres"></a>Förbereda lokala Postgres

I det här steget skapar du en databas för självstudien på din lokala Postgres-server.

### <a name="connect-to-local-postgres-server"></a>Anslut till den lokala Postgres-servern

Öppna terminalfönstret och kör `psql` för att ansluta till din lokala Postgres-server.

```bash
sudo -u postgres psql
```

Om anslutningen lyckas körs Postgres-databasen. Om inte, kontrollerar du att den lokala Postgres-databasen har startats genom att följa stegen i [Hämtningar – PostgreSQL Core Distribution](https://www.postgresql.org/download/).

Skriv `\q` om du vill avsluta Postgres-klienten. 

Skapa en Postgres-användare som kan skapa databaser genom att köra följande kommando, med ditt inloggade Linux-användarnamn.

```bash
sudo -u postgres createuser -d <signed_in_user>
```

<a name="step2"></a>

## <a name="create-a-ruby-on-rails-app-locally"></a>Skapa en Ruby on Rails-app lokalt
I det här steget hämtar du en Ruby on Rails-exempelapp, konfigurerar dess databasanslutning och kör den lokalt. 

### <a name="clone-the-sample"></a>Klona exemplet

Använd kommandot `cd` för att komma till en arbetskatalog i terminalfönstret.

Klona exempellagringsplatsen med följande kommando.

```bash
git clone https://github.com/Azure-Samples/rubyrails-tasks.git
```

`cd` till den klonade katalogen. Installera de paket som behövs.

```bash
cd rubyrails-tasks
bundle install --path vendor/bundle
```

### <a name="run-the-sample-locally"></a>Köra exemplet lokalt

Kör [Rails-migreringen](http://guides.rubyonrails.org/active_record_migrations.html#running-migrations) för att skapa de tabeller som behövs för appen. Du kan se vilka tabeller som skapas i migreringarna i katalogen _db/migrations_ på Git-lagringsplatsen.

```bash
rake db:create
rake db:migrate
```

Kör appen.

```bash
rails server
```

Gå till `http://localhost:3000` i en webbläsare. Lägg till några uppgifter på sidan.

![Ruby on Rails ansluter till Postgres](./media/tutorial-ruby-postgres-app/postgres-connect-success.png)

Om du vill stoppa Rails-servern skriver du `Ctrl + C` i terminalen.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-postgres-in-azure"></a>Skapa Postgres i Azure

I det här steget skapar du en Postgres-databas i [Azure Database for PostgreSQL](/azure/postgresql/). Senare kommer du att konfigurera Ruby on Rails-appen för att ansluta till den här databasen.

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)] 

### <a name="create-a-postgres-server"></a>Skapa en Postgres-server

Skapa en PostgreSQL-server med kommandot [`az postgres server create`](/cli/azure/postgres/server?view=azure-cli-latest#az_postgres_server_create).

Kör följande kommando i Cloud Shell och ersätt ett unikt servernamn för platshållaren *\<postgres_server_name >*. Servernamnet måste vara unikt för alla servrar i Azure. 

```azurecli-interactive
az postgres server create --location "West Europe" --resource-group myResourceGroup --name <postgres_server_name> --admin-user adminuser --admin-password My5up3r$tr0ngPa$w0rd! --sku-name GP_Gen4_2
```

När den logiska Azure Database for PostgreSQL-servern har skapats visar Azure CLI information som liknar följande exempel:

```json
{
  "administratorLogin": "adminuser",
  "earliestRestoreDate": "2018-06-15T12:38:25.280000+00:00",
  "fullyQualifiedDomainName": "<postgres_server_name>.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/<postgres_server_name>",
  "location": "westeurope",
  "name": "<postgres_server_name>",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "capacity": 2,
    "family": "Gen4",
    "name": "GP_Gen4_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  < Output has been truncated for readability >
}
```

### <a name="configure-server-firewall"></a>Konfigurera serverbrandväggen

Skapa en brandväggsregel för Postgres-servern i Cloud Shell för att tillåta klientanslutningar med hjälp av kommandot [`az postgres server firewall-rule create`](/cli/azure/postgres/server/firewall-rule?view=azure-cli-latest#az_postgres_server_firewall_rule_create). När både start-IP och slut-IP har angetts till 0.0.0.0 öppnas brandväggen endast för andra Azure-resurser. Ersätt ett unikt servernamn för platshållaren *\<postgres_server_name >*.

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server <postgres_server_name> --name AllowAllIps --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

> [!TIP] 
> Du kan begränsa brandväggsregeln ännu mer genom att [endast använda de utgående IP-adresser som används av din app](../app-service-ip-addresses.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips).
>

### <a name="connect-to-production-postgres-server-locally"></a>Anslut till Postgres produktionsserver lokalt

I Cloud Shell, anslut till Postgres-servern i Azure. Använd värdet du angav tidigare för platshållaren _&lt;postgres_server_name >_.

```bash
psql -U adminuser@<postgres_server_name> -h <postgres_server_name>.postgres.database.azure.com postgres
```

När du uppmanas att ange ett lösenord använder du _My5up3r$tr0ngPa$w0rd!_, som du angav när du skapade databasservern.

### <a name="create-a-production-database"></a>Skapa en produktionsdatabas

Skapa en databas i `postgres`-prompten.

```sql
CREATE DATABASE sampledb;
```

### <a name="create-a-user-with-permissions"></a>Skapa en användare med behörigheter

Skapa en databasanvändare med namnet _railsappuser_ och ge användaren alla privilegier i `sampledb`-databasen.

```sql
CREATE USER railsappuser WITH PASSWORD 'MyPostgresAzure2017'; 
GRANT ALL PRIVILEGES ON DATABASE sampledb TO railsappuser;
```

Skriv `\q` för att avsluta serveranslutningen.

## <a name="connect-app-to-azure-postgres"></a>Anslut appen till Azure Postgres

I det här steget ansluter du Ruby on Rails-appen till Postgres-databasen du skapade i Azure Database för PostgreSQL.

<a name="devconfig"></a>

### <a name="configure-the-database-connection"></a>Konfigurera databasanslutningen

På lagringsplatsen öppnar du _config/database.yml_. Ersätt produktionsvariablerna med följande kod längst ned i filen. 

```txt
production:
  <<: *default
  host: <%= ENV['DB_HOST'] %>
  database: <%= ENV['DB_DATABASE'] %>
  username: <%= ENV['DB_USERNAME'] %>
  password: <%= ENV['DB_PASSWORD'] %>
```

Spara ändringarna.

### <a name="test-the-application-locally"></a>Testa appen lokalt

Tillbaka i den lokala terminalen anger du följande miljövariabler:

```bash
export DB_HOST=<postgres_server_name>.postgres.database.azure.com
export DB_DATABASE=sampledb 
export DB_USERNAME=railsappuser@<postgres_server_name>
export DB_PASSWORD=MyPostgresAzure2017
```

Kör Rails-databasmigreringar med de produktionsvärden du just konfigurerade för att skapa tabellerna i din Postgres-databas i Azure Database for PostgreSQL. 

```bash
rake db:migrate RAILS_ENV=production
```

När Rails-programmet körs i produktionsmiljön behöver det förkompilerade tillgångar. Generera de tillgångar som krävs med följande kommando:

```bash
rake assets:precompile
```

Rails-produktionsmiljön använder också hemligheter för att hantera säkerhet. Generera en hemlig nyckel.

```bash
rails secret
```

Spara den hemliga nyckeln för respektive variabler som används av Rails-produktionsmiljön. För enkelhetens skull använder du samma nyckel för de båda variablerna.

```bash
export RAILS_MASTER_KEY=<output_of_rails_secret>
export SECRET_KEY_BASE=<output_of_rails_secret>
```

Aktivera Rails-produktionsmiljön för att hantera JavaScript- och CSS-filer.

```bash
export RAILS_SERVE_STATIC_FILES=true
```

Kör exempelprogrammet i produktionsmiljön.

```bash
rails server -e production
```

Navigera till `http://localhost:3000`. Om sidan läses in utan fel ansluter Ruby on Rails-appen till Postgres-databasen i Azure.

Lägg till några uppgifter på sidan.

![Ruby on Rails ansluter till Azure Database för PostgreSQL](./media/tutorial-ruby-postgres-app/azure-postgres-connect-success.png)

Om du vill stoppa Rails-servern skriver du `Ctrl + C` i terminalen.

### <a name="commit-your-changes"></a>Genomföra ändringarna

Kör följande Git-kommandon för att genomföra ändringarna:

```bash
git add .
git commit -m "database.yml updates"
```

Din app är klar att distribueras.

## <a name="deploy-to-azure"></a>Distribuera till Azure

I det här steget, distribuerar du ditt Postgres-anslutna Rails-program till Azure App Service.

### <a name="configure-a-deployment-user"></a>Konfigurera en distributionsanvändare

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Skapa en App Service-plan

[!INCLUDE [Create app service plan no h](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Skapa en webbapp

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-ruby-linux-no-h.md)] 

### <a name="configure-database-settings"></a>Konfigurera databasinställningarna

I App Service ställer du in miljövariabler som _appinställningar_ med kommandot [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) i Cloud Shell.

Följande Cloud Shell-kommando konfigurerar appinställningarna `DB_HOST`, `DB_DATABASE`, `DB_USERNAME` och `DB_PASSWORD`. Ersätt platshållarna _&lt;appname>_ och _&lt;postgres_server_name>_.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DB_HOST="<postgres_server_name>.postgres.database.azure.com" DB_DATABASE="sampledb" DB_USERNAME="railsappuser@<postgres_server_name>" DB_PASSWORD="MyPostgresAzure2017"
```

### <a name="configure-rails-environment-variables"></a>Konfigurera Rails-miljövariabler

I den lokala terminalen genererar du en ny hemlig nyckel för produktionsmiljön i Azure.

```bash
rails secret
```

Konfigurera variablerna som krävs för Rails-produktionsmiljön.

I följande Cloud Shell-kommando ersätter du de två platshållarna _&lt;output_of_rails_secret>_ med den nya hemliga nyckeln du genererade i den lokala terminalen.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings RAILS_MASTER_KEY="<output_of_rails_secret>" SECRET_KEY_BASE="<output_of_rails_secret>" RAILS_SERVE_STATIC_FILES="true" ASSETS_PRECOMPILE="true"
```

`ASSETS_PRECOMPILE="true"` uppmanar Ruby-standardbehållaren att förkompilera tillgångar vid varje Git-distribution.

### <a name="push-to-azure-from-git"></a>Skicka till Azure från Git

I den lokala terminalen kan du lägga till en Azure-fjärrdatabas till din lokala Git-databas.

```bash
git remote add azure <paste_copied_url_here>
```

Skicka till Azure-fjärrdatabasen för att distribuera Ruby on Rails-appen. Du uppmanas att ange lösenordet du angav tidigare. Det behövs för att skapa distributionsanvändaren.

```bash
git push azure master
```

Under distributionen meddelar Azure App Service förloppet till Git.

```bash
Counting objects: 3, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 291 bytes | 0 bytes/s, done.
Total 3 (delta 2), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'a5e076db9c'.
remote: Running custom deployment command...
remote: Running deployment command...
...
< Output has been truncated for readability >
```

### <a name="browse-to-the-azure-web-app"></a>Bläddra till Azure-webbappen

Bläddra till `http://<app_name>.azurewebsites.net` och lägg till några uppgifter i listan.

![Ruby on Rails-appen körs i Azure App Service](./media/tutorial-ruby-postgres-app/ruby-postgres-in-azure.png)

Grattis! Du kör en datadriven Ruby on Rails-app i Azure App Service.

## <a name="update-model-locally-and-redeploy"></a>Uppdatera modell lokalt och distribuera om

I det här steget gör du en enkel ändring i `task`-datamodellen och webbappen och publicerar sedan uppdateringen till Azure.

För uppgiftsscenariot ändrar du programmet så att du kan markera en uppgift som slutförd.

### <a name="add-a-column"></a>Lägg till en kolumn

I terminalen går du till roten för Git-lagringsplatsen.

Generera en ny migrering som lägger till en boolesk kolumn med namnet `Done` i tabellen `Tasks`:

```bash
rails generate migration AddDoneToTasks Done:boolean
```

Det här kommandot genererar en ny migrationsfil i katalogen _db/migrate_.


I terminalen kör du Rails-databasemigreringar för att göra ändringen i den lokala databasen.

```bash
rake db:migrate
```

### <a name="update-application-logic"></a>Uppdatera programlogik

Öppna filen *app/controllers/tasks_controller.rb*. Lägg till följande rad i slutet av filen:

```rb
params.require(:task).permit(:Description)
```

Ändra raden så att den inkluderar den nya parametern `Done`.

```rb
params.require(:task).permit(:Description, :Done)
```

### <a name="update-the-views"></a>Uppdatera vyerna

Öppna filen *app/views/tasks/_form.html.erb*, som är i redigeringsform.

Leta reda på raden `<%=f.error_span(:Description) %>` och infoga följande kod direkt under den:

```erb
<%= f.label :Done, :class => 'control-label col-lg-2' %>
<div class="col-lg-10">
  <%= f.check_box :Done, :class => 'form-control' %>
</div>
```

Öppna filen *app/views/tasks/show.html.erb*, som är den enskilda postens visningssida. 

Leta reda på raden `<dd><%= @task.Description %></dd>` och infoga följande kod direkt under den:

```erb
  <dt><strong><%= model_class.human_attribute_name(:Done) %>:</strong></dt>
  <dd><%= check_box "task", "Done", {:checked => @task.Done, :disabled => true}%></dd>
```

Öppna filen *app/views/tasks/index.html.erb*, som är indexsidan för alla poster.

Leta reda på raden `<th><%= model_class.human_attribute_name(:Description) %></th>` och infoga följande kod direkt under den:

```erb
<th><%= model_class.human_attribute_name(:Done) %></th>
```

I samma fil letar du reda på raden `<td><%= task.Description %></td>` och infogar följande kod direkt under den:

```erb
<td><%= check_box "task", "Done", {:checked => task.Done, :disabled => true} %></td>
```

### <a name="test-the-changes-locally"></a>Testa ändringarna lokalt

Kör Rails-servern i den lokala terminalen.

```bash
rails server
```

Om du vill se ändringen i uppgiftsstatusen går du till `http://localhost:3000` och lägger till eller redigerar objekt.

![Kryssruta lades till för uppgift](./media/tutorial-ruby-postgres-app/complete-checkbox.png)

Om du vill stoppa Rails-servern skriver du `Ctrl + C` i terminalen.

### <a name="publish-changes-to-azure"></a>Publicera ändringar till Azure

I terminalen kör du Rails-databasmigreringar för produktionsmiljön för att genomföra ändringen i Azure-databasen.

```bash
rake db:migrate RAILS_ENV=production
```

Spara alla ändringar på Git och skicka sedan kodändringarna till Azure.

```bash
git add .
git commit -m "added complete checkbox"
git push azure master
```

När `git push` har slutförts kan du gå till Azure-webbappen och prova de nya funktionerna.

![Modell- och databasändringar som är publicerade i Azure](media/tutorial-ruby-postgres-app/complete-checkbox-published.png)

Om du har lagt till några uppgifter finns de kvar i databasen. Uppdateringar i dataschemat påverkar inte befintliga data.

## <a name="manage-the-azure-web-app"></a>Hantera Azure-webbappen

Gå till [Azure Portal](https://portal.azure.com) för att hantera den webbapp som du skapade.

Klicka på **App Services** i menyn till vänster och sedan på namnet på din Azure-webbapp.

![Navigera till webbappen på Azure Portal](./media/tutorial-php-mysql-app/access-portal.png)

Nu visas sidan Översikt för din webbapp. Här kan du utföra grundläggande hanteringsåtgärder som att stoppa, starta, starta om, bläddra och ta bort.

Menyn till vänster innehåller sidor för att konfigurera appen.

![App Service-sidan på Azure Portal](./media/tutorial-php-mysql-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

<a name="next"></a>

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa en Postgres-databas i Azure
> * Ansluta en Ruby on Rails-app till Postgres
> * distribuera appen till Azure
> * uppdatera datamodellen och distribuera om appen
> * strömma diagnostikloggar från Azure
> * hantera appen i Azure-portalen.

Gå vidare till nästa självstudie där du får lära dig att mappa ett anpassat DNS-namn till webbappen.

> [!div class="nextstepaction"]
> [Mappa ett befintligt anpassat DNS-namn till Azure Web Apps](../app-service-web-tutorial-custom-domain.md)
