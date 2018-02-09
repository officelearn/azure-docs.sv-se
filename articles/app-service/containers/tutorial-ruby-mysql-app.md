---
title: Skapa en Ruby- and MySQL-webbapp i Azure App Service i Linux | Microsoft Docs
description: "Lär dig hur du får igång en Ruby-app som fungerar i Azure med anslutning till en MySQL-databas i Azure."
services: app-service\web
documentationcenter: 
author: cephalin
manager: cfowler
ms.service: app-service-web
ms.workload: web
ms.devlang: ruby
ms.topic: tutorial
ms.date: 12/21/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 951e66e47cf8fbe9d2cdf1606a8d63054bcada13
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2018
---
# <a name="build-a-ruby-and-mysql-web-app-in-azure-app-service-on-linux"></a>Skapa en Ruby- and MySQL-webbapp i Azure App Service i Linux

Med [App Service i Linux](app-service-linux-intro.md) får du en mycket skalbar och automatiskt uppdaterad webbvärdtjänst som utgår från operativsystemet Linux. I den här självstudien visas hur du skapar en Ruby-webbapp och ansluter den till en MySQL-databas. När du är klar har du en [Ruby on Rails](http://rubyonrails.org/)-app som körs i App Service på Linux.

![Ruby on Rails-appen körs i Azure App Service](./media/tutorial-ruby-mysql-app/complete-checkbox-published.png)

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Skapa en MySQL-databas i Azure
> * Ansluta en Ruby on Rails-app till MySQL
> * distribuera appen till Azure
> * uppdatera datamodellen och distribuera om appen
> * strömma diagnostikloggar från Azure
> * hantera appen i Azure-portalen.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

För att slutföra den här kursen behöver du:

* [Installera Git](https://git-scm.com/)
* [Installera Ruby 2.3](https://www.ruby-lang.org/documentation/installation/)
* [Installera Ruby on Rails 5.1](http://guides.rubyonrails.org/v5.1/getting_started.html)
* [Installera och starta MySQL](https://dev.mysql.com/doc/refman/5.7/en/installing.html) 

## <a name="prepare-local-mysql"></a>Förbereda lokal MySQL

I det här steget skapar du en databas på din lokala MySQL-server för användning i den här självstudien.

### <a name="connect-to-local-mysql-server"></a>Ansluta till en lokal MySQL-server

Anslut till din lokala MySQL-server via ett terminalfönster. Du kan använda det här terminalfönstret för att köra alla kommandon i den här självstudien.

```bash
mysql -u root -p
```

Om du uppmanas att ange ett lösenord anger du lösenordet för `root`-kontot. Se [MySQL: Återställa rotlösenordet](https://dev.mysql.com/doc/refman/5.7/en/resetting-permissions.html) om du inte kommer ihåg rotlösenordet för ditt konto.

Om kommandot körs utan problem, är MySQL-servern igång. Om inte, kontrollerar du att den lokala MySQL-servern är igång genom att följa [anvisningarna efter installation av MySQL](https://dev.mysql.com/doc/refman/5.7/en/postinstallation.html).

Skriv `quit` för att avsluta serveranslutningen.

```sql
quit
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

### <a name="configure-mysql-connection"></a>Konfigurera MySQL-anslutning

På lagringsplatsen öppnar du _config/database.yml_ och tillhandahåller det lokala MySQL-rotanvändarnamnet och -lösenordet (rad 13). Om du har installerat MySQL med ett verktyg som [Homebrew](https://brew.sh/) är autentiseringsuppgifterna i filen redan inställda på standardvärdena (som är `root` och ett tomt lösenord).

```txt
default: &default
  adapter: mysql2
  encoding: utf8
  pool: <%= ENV.fetch("RAILS_MAX_THREADS") { 5 } %>
  username: root
  password:
  socket: /tmp/mysql.sock
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

![Ruby on Rails ansluter till MySQL](./media/tutorial-ruby-mysql-app/mysql-connect-success.png)

Om du vill stoppa Rails-servern skriver du `Ctrl + C` i terminalen.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-mysql-in-azure"></a>Skapa MySQL i Azure

I det här steget skapar du en MySQL-databas i [Azure Database för MySQL (förhandsversion)](/azure/mysql). Senare kommer du att konfigurera Ruby on Rails-appen för att ansluta till den här databasen.

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-no-h.md)] 

### <a name="create-a-mysql-server"></a>Skapa en MySQL-server

Skapa en server i Azure Database för MySQL (förhandsversion) med kommandot [`az mysql server create`](/cli/azure/mysql/server?view=azure-cli-latest#az_mysql_server_create).

I följande kommando ersätter du MySQL-servernamnet i platshållaren _&lt;mysql_server_name>_ (giltiga tecken är `a-z`, `0-9` och `-`). Det här namnet är en del av MySQL-serverns värdnamn (`<mysql_server_name>.mysql.database.azure.com`) och den måste vara globalt unik.

```azurecli-interactive
az mysql server create --name <mysql_server_name> --resource-group myResourceGroup --location "North Europe" --admin-user adminuser --admin-password My5up3r$tr0ngPa$w0rd!
```

När MySQL-servern skapas visar Azure CLI information som ser ut ungefär så här:

```json
{
  "administratorLogin": "adminuser",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "<mysql_server_name>.database.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/<mysql_server_name>",
  "location": "northeurope",
  "name": "<mysql_server_name>",
  "resourceGroup": "myResourceGroup",
  ...
}
```

### <a name="configure-server-firewall"></a>Konfigurera serverbrandväggen

Skapa en brandväggsregel för MySQL-servern för att tillåta klientanslutningar med hjälp av kommandot [`az mysql server firewall-rule create`](/cli/azure/mysql/server/firewall-rule?view=azure-cli-latest#az_mysql_server_firewall_rule_create).

```azurecli-interactive
az mysql server firewall-rule create --name allIPs --server <mysql_server_name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

> [!NOTE]
> Azure Database för MySQL (förhandsversion) begränsar för närvarande inte anslutningar till endast Azure-tjänster. Eftersom IP-adresser i Azure tilldelas dynamiskt är det bättre att aktivera alla IP-adresser. Tjänsten är en förhandsversion. Vi planerar att införa bättre metoder för att skydda databasen.
>

### <a name="connect-to-production-mysql-server-locally"></a>Ansluta lokalt till MySQL-produktionsservern

Anslut till MySQL-server i Azure via terminalfönstret. Använd värdet du angav tidigare för _&lt;mysql_server_name>_.

```bash
mysql -u adminuser@<mysql_server_name> -h <mysql_server_name>.mysql.database.azure.com -P 3306 -p
```

När du uppmanas att ange ett lösenord använder du _My5up3r$tr0ngPa$w0rd!_, som du angav när du skapade databasservern.

### <a name="create-a-production-database"></a>Skapa en produktionsdatabas

Välj att skapa en databas i `mysql`.

```sql
CREATE DATABASE sampledb;
```

### <a name="create-a-user-with-permissions"></a>Skapa en användare med behörigheter

Skapa en databasanvändare med namnet _railsappuser_ och ge användaren alla privilegier i `sampledb`-databasen.

```sql
CREATE USER 'railsappuser' IDENTIFIED BY 'MySQLAzure2017'; 
GRANT ALL PRIVILEGES ON sampledb.* TO 'railsappuser';
```

Skriv `quit` för att avsluta serveranslutningen.

```sql
quit
```

## <a name="connect-app-to-azure-mysql"></a>Ansluta appen till Azure MySQL

I det här steget ansluter du Ruby on Rails-appen till MySQL-databasen du skapade i Azure Database för MySQL (förhandsversion).

<a name="devconfig"></a>

### <a name="configure-the-database-connection"></a>Konfigurera databasanslutningen

På lagringsplatsen öppnar du _config/database.yml_. Ersätt produktionsvariablerna med följande kod längst ned i filen. För platshållaren _&lt;mysql_server_name>_ använder du namnet på den MySQL-server du skapade.

```txt
production:
  <<: *default
  host: <%= ENV['DB_HOST'] %>
  port: 3306
  database: <%= ENV['DB_DATABASE'] %>
  username: <%= ENV['DB_USERNAME'] %>
  password: <%= ENV['DB_PASSWORD'] %>
  sslca: ssl/BaltimoreCyberTrustRoot.crt.pem
```

Spara ändringarna.

> [!NOTE]
> `sslca` läggs till och pekar på en befintlig _.pem_-fil i exempellagringsplatsen. Azure Database för MySQL använder som standard SSL-anslutningar från klienter. Med det här `.pem`-certifikatet ställer du in SSL-anslutningar till Azure Database for MySQL. Se [Konfigurera SSL-anslutning i din app för säker anslutning till Azure Database för MySQL](../../mysql/howto-configure-ssl.md) för mer information.
>

### <a name="test-the-application-locally"></a>Testa appen lokalt

I den lokala terminalen anger du följande miljövariabler:

```bash
export DB_HOST=<mysql_server_name>.mysql.database.azure.com
export DB_DATABASE=sampledb 
export DB_USERNAME=railsappuser@<mysql_server_name>
export DB_PASSWORD=MySQLAzure2017
```

Kör Rails-databasmigreringar med de produktionsvärden du just konfigurerade för att skapa tabellerna i din MySQL-databas i Azure Database for MySQL (förhandsversion). 

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

Navigera till `http://localhost:3000`. Om sidan läses in utan fel ansluter Ruby on Rails-appen till MySQL-databasen i Azure.

Lägg till några uppgifter på sidan.

![Ruby on Rails ansluter till Azure Database för MySQL (förhandsversion)](./media/tutorial-ruby-mysql-app/azure-mysql-connect-success.png)

Om du vill stoppa Rails-servern skriver du `Ctrl + C` i terminalen.

### <a name="commit-your-changes"></a>Genomföra ändringarna

Kör följande Git-kommandon för att genomföra ändringarna:

```bash
git add .
git commit -m "database.yml updates"
```

Din app är klar att distribuera.

## <a name="deploy-to-azure"></a>Distribuera till Azure

I det här steget, distribuerar du ditt MySQL-anslutna Rails-program till Azure App Service.

### <a name="configure-a-deployment-user"></a>Konfigurera en distributionsanvändare

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Skapa en App Service-plan

[!INCLUDE [Create app service plan no h](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Skapa en webbapp

I Cloud Shell skapar du en webbapp i `myAppServicePlan` App Service-planen med kommandot [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create). 

Ersätt `<app_name>` med ett globalt unikt appnamn (giltiga tecken är `a-z`, `0-9` och `-`) i följande exempel. Körningen är inställd på `RUBY|2.3`, vilket distribuerar [Ruby-standardavbildningen](https://hub.docker.com/r/appsvc/ruby/). Om du vill se alla körningar som stöds ska du köra [ `az webapp list-runtimes` ](/cli/azure/webapp?view=azure-cli-latest#az_webapp_list_runtimes). 

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --runtime "RUBY|2.3" --deployment-local-git
```

När webbappen har skapats visar Azure CLI utdata liknande den i följande exempel:

```json
Local git is configured with url of 'https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

Du har skapat en tom ny webbapp med git-distribution aktiverad.

> [!NOTE]
> URL för fjärransluten Git visas i egenskapen `deploymentLocalGitUrl` med formatet `https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git`. Spara den här URL:en, eftersom du behöver den senare.
>

### <a name="configure-database-settings"></a>Konfigurera databasinställningarna

I App Service ställer du in miljövariabler som _appinställningar_ med kommandot [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) i Cloud Shell.

Följande Cloud Shell-kommando konfigurerar appinställningarna `DB_HOST`, `DB_DATABASE`, `DB_USERNAME` och `DB_PASSWORD`. Ersätt platshållarnas _&lt;appname>_ och _&lt;mysql_server_name>_.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DB_HOST="<mysql_server_name>.mysql.database.azure.com" DB_DATABASE="sampledb" DB_USERNAME="railsappuser@<mysql_server_name>" DB_PASSWORD="MySQLAzure2017"
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

![Ruby on Rails-appen körs i Azure App Service](./media/tutorial-ruby-mysql-app/ruby-mysql-in-azure.png)

Grattis! Du kör en datadriven Ruby on Rails-app i Azure App Service.

## <a name="update-model-locally-and-redeploy"></a>Uppdatera modell lokalt och distribuera om

I det här steget gör du en enkel ändring i `task`-datamodellen och webbappen och publicerar sedan uppdateringen till Azure.

För uppgiftsscenariot ändrar du programmet så att du kan markera en uppgift som slutförd.

### <a name="add-a-column"></a>Lägg till en kolumn

I terminalen går du till roten för Git-lagringsplatsen.

Generera en ny migrering som lägger till en boolesk kolumn med namnet `Done` i tabellen `Tasks`:

```bash
rails generate migration add_Done_to_Tasks Done:boolean
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

![Kryssruta lades till för uppgift](./media/tutorial-ruby-mysql-app/complete-checkbox.png)

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

När `git push` har slutförts kan du gå till Azure-webbappen och prova att använda de nya funktionerna.

![Modell- och databasändringar som är publicerade i Azure](media/tutorial-ruby-mysql-app/complete-checkbox-published.png)

Om du har lagt till några uppgifter finns de kvar i databasen. Uppdateringar i dataschemat påverkar inte befintliga data.

## <a name="manage-the-azure-web-app"></a>Hantera Azure-webbappen

Gå till [Azure Portal](https://portal.azure.com) för att hantera den webbapp som du skapade.

Klicka på **Apptjänster** i menyn till vänster och sedan på namnet på din Azure-webbapp.

![Navigera till webbappen på Azure Portal](./media/tutorial-php-mysql-app/access-portal.png)

Nu visas sidan Översikt för din webbapp. Här kan du utföra grundläggande hanteringsåtgärder som att stoppa, starta, starta om, bläddra och ta bort.

Menyn till vänster innehåller sidor för att konfigurera appen.

![App Service-sidan på Azure Portal](./media/tutorial-php-mysql-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

<a name="next"></a>

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa en MySQL-databas i Azure
> * Ansluta en Ruby on Rails-app till MySQL
> * distribuera appen till Azure
> * uppdatera datamodellen och distribuera om appen
> * strömma diagnostikloggar från Azure
> * hantera appen i Azure-portalen.

Gå vidare till nästa självstudie där du får lära dig att mappa ett anpassat DNS-namn till webbappen.

> [!div class="nextstepaction"]
> [Mappa ett befintligt anpassat DNS-namn till Azure Web Apps](../app-service-web-tutorial-custom-domain.md)
