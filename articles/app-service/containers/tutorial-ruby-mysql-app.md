---
title: "Skapa en Ruby och MySQL-webbapp i Azure App Service på Linux | Microsoft Docs"
description: "Lär dig hur du hämtar en Ruby app arbetar i Azure, med anslutning till en MySQL-databas i Azure."
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: cfowler
ms.service: app-service-web
ms.workload: web
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 12/21/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 03b2b4e8f8827a08e1414512d848bd5bed48d674
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2018
---
# <a name="build-a-ruby-and-mysql-web-app-in-azure-app-service-on-linux"></a>Skapa en Ruby och MySQL-webbapp i Azure App Service på Linux

Med [App Service i Linux](app-service-linux-intro.md) får du en mycket skalbar och automatiskt uppdaterad webbvärdtjänst som utgår från operativsystemet Linux. Den här kursen visar hur du skapar ett Ruby webbprogram och ansluta till en MySQL-databas. När du är klar har du en [Ruby spår](http://rubyonrails.org/) app som körs i Apptjänsten på Linux.

![Ruby på spår appen körs i Azure App Service](./media/tutorial-ruby-mysql-app/complete-checkbox-published.png)

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Skapa en MySQL-databas i Azure
> * Ansluta en Ruby på spår app till MySQL
> * Distribuera appen till Azure
> * Uppdatera datamodellen och distribuera appen
> * Dataströmmen diagnostiska loggar från Azure
> * Hantera appen i Azure-portalen

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här kursen behöver du:

* [Installera Git](https://git-scm.com/)
* [Installera Ruby 2.3](https://www.ruby-lang.org/documentation/installation/)
* [Installera Ruby spår 5.1](http://guides.rubyonrails.org/v5.1/getting_started.html)
* [Installera och starta MySQL](https://dev.mysql.com/doc/refman/5.7/en/installing.html) 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prepare-local-mysql"></a>Förbereda lokala MySQL

I det här steget kan skapa du en databas i din lokala MySQL-server för användning i den här kursen.

### <a name="connect-to-local-mysql-server"></a>Ansluta till lokala MySQL-servern

Anslut till din lokala MySQL-server i ett terminalfönster. Du kan använda den här terminalfönster för att köra alla kommandon i den här självstudiekursen.

```bash
mysql -u root -p
```

Om du uppmanas att ange ett lösenord anger du lösenordet för den `root` konto. Om du inte kommer ihåg rotlösenordet, se [MySQL: hur du återställer Rotlösenordet](https://dev.mysql.com/doc/refman/5.7/en/resetting-permissions.html).

Om kommandot körs utan problem, är MySQL-servern igång. Om inte, kontrollera att den lokala MySQL-servern är igång genom att följa den [MySQL efter installationssteg](https://dev.mysql.com/doc/refman/5.7/en/postinstallation.html).

Avsluta anslutningen till servern genom att skriva `quit`.

```sql
quit
```

<a name="step2"></a>

## <a name="create-a-ruby-on-rails-app-locally"></a>Skapa en Ruby på spår appen lokalt
I det här steget kan få en Ruby på spår exempelprogrammet, konfigurera dess databasanslutningen och köra det lokalt. 

### <a name="clone-the-sample"></a>Klona exemplet

I fönstret terminal `cd` till en arbetskatalog.

Klona exempellagringsplatsen med följande kommando.

```bash
git clone https://github.com/Azure-Samples/rubyrails-tasks.git
```

`cd`till klonade-katalogen. Installera de nödvändiga paketen.

```bash
cd rubyrails-tasks
bundle install --path vendor/bundle
```

### <a name="configure-mysql-connection"></a>Konfigurera MySQL-anslutning

Öppna i databasen, _config/database.yml_ och ange den lokala MySQL rot-användarnamn och lösenord (rad 13). Om du har installerat MySQL med ett verktyg som [Homebrew](https://brew.sh/), och sedan autentiseringsuppgifter i filen har redan ställts in till standardvärden (vilket är `root` och ett tomt lösenord).

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

Kör [spår migreringar](http://guides.rubyonrails.org/active_record_migrations.html#running-migrations) skapa tabellerna programmet behöver. Om du vill se vilka tabeller skapas av migreringar finns i den _db eller flytta_ katalog i Git-lagringsplats.

```bash
rake db:create
rake db:migrate
```

Kör appen.

```bash
rails server
```

Gå till `http://localhost:3000` i en webbläsare. Lägg till några åtgärder på sidan.

![Ruby spår ansluter har till MySQL](./media/tutorial-ruby-mysql-app/mysql-connect-success.png)

Om du vill stoppa spår servern skriver `Ctrl + C` i terminalen.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-mysql-in-azure"></a>Skapa MySQL i Azure

I det här steget skapar du en MySQL-databas i [Azure-databas för MySQL (förhandsgranskning)](/azure/mysql). Senare kan konfigurera du Ruby i spår programmet för att ansluta till databasen.

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-no-h.md)] 

### <a name="create-a-mysql-server"></a>Skapa en MySQL-server

Skapa en server i Azure-databas för MySQL (förhandsversion) med den [az mysql-servern skapa](/cli/azure/mysql/server?view=azure-cli-latest#az_mysql_server_create) kommando.

Ersätt namnet på MySQL-servern där du ser i följande kommando i  _&lt;mysql_server_name >_ platshållare (giltiga tecken är `a-z`, `0-9`, och `-`). Det här namnet är en del av MySQL-serverns värdnamn (`<mysql_server_name>.mysql.database.azure.com`), den måste vara globalt unika.

```azurecli-interactive
az mysql server create --name <mysql_server_name> --resource-group myResourceGroup --location "North Europe" --admin-user adminuser --admin-password My5up3r$tr0ngPa$w0rd!
```

När MySQL-servern har skapats visas Azure CLI information liknar följande exempel:

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

### <a name="configure-server-firewall"></a>Konfigurera server-brandväggen

Skapa en brandväggsregel för MySQL-servern att tillåta klientanslutningar med hjälp av den [az mysql-brandväggsregel skapa](/cli/azure/mysql/server/firewall-rule?view=azure-cli-latest#az_mysql_server_firewall_rule_create) kommando.

```azurecli-interactive
az mysql server firewall-rule create --name allIPs --server <mysql_server_name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

> [!NOTE]
> Azure-databas för MySQL (förhandsgranskning) begränsa inte för närvarande anslutningar endast till Azure-tjänster. IP-adresser i Azure tilldelas dynamiskt, är det bättre att aktivera alla IP-adresser. Tjänsten är i förhandsgranskningen. Bättre metoder för att skydda databasen planeras.
>

### <a name="connect-to-production-mysql-server-locally"></a>Ansluta till MySQL produktionsservern lokalt

Anslut till MySQL-server i Azure i fönstret terminal. Använd värdet du angav tidigare för  _&lt;mysql_server_name >_.

```bash
mysql -u adminuser@<mysql_server_name> -h <mysql_server_name>.mysql.database.azure.com -P 3306 -p
```

När du uppmanas att ange ett lösenord, Använd _My5up3r tr0ngPa$ $w0rd!_, som du angav när du skapade databasservern.

### <a name="create-a-production-database"></a>Skapa en produktionsdatabas

På den `mysql` uppmanar, skapa en databas.

```sql
CREATE DATABASE sampledb;
```

### <a name="create-a-user-with-permissions"></a>Skapa en användare med behörighet

Skapa en databasanvändare som kallas _railsappuser_ och ge alla behörigheter i den `sampledb` databas.

```sql
CREATE USER 'railsappuser' IDENTIFIED BY 'MySQLAzure2017'; 
GRANT ALL PRIVILEGES ON sampledb.* TO 'railsappuser';
```

Avsluta server-anslutningen genom att skriva `quit`.

```sql
quit
```

## <a name="connect-app-to-azure-mysql"></a>Anslut appen till Azure MySQL

I det här steget kan ansluta du Ruby i programmet för spår att MySQL-databas som du skapade i Azure-databas för MySQL (förhandsversion).

<a name="devconfig"></a>

### <a name="configure-the-database-connection"></a>Konfigurera anslutningen till databasen

Öppna i databasen, _config/database.yml_. Ersätt variablerna produktion med följande kod längst ned i filen. För den  _&lt;mysql_server_name >_ platshållare, Använd namnet på MySQL-server som du skapade.

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
> Den `sslca` har lagts till och pekar på ett befintligt _.pem_ filen i exempel-databasen. Som standard tillämpar Azure-databas för MySQL SSL-anslutningar från klienter. Detta `.pem` certifikatet är hur du ställer in SSL-anslutningar till Azure-databas för MySQL. Se [Konfigurera SSL-anslutning i din app för säker anslutning till Azure Database för MySQL](../../mysql/howto-configure-ssl.md) för mer information.
>

### <a name="test-the-application-locally"></a>Testa programmet lokalt

Ange följande miljövariabler i den lokala terminalen:

```bash
export DB_HOST=<mysql_server_name>.mysql.database.azure.com
export DB_DATABASE=sampledb 
export DB_USERNAME=railsappuser@<mysql_server_name>
export DB_PASSWORD=MySQLAzure2017
```

Kör spår databasen migreringar med produktion värdena som du precis har konfigurerat för att skapa tabeller i din MySQL-databas på Azure-databas för MySQL (förhandsversion). 

```bash
rake db:migrate RAILS_ENV=production
```

När du kör i produktionsmiljön, måste programmet spår förkompilerade tillgångar. Generera krävs tillgångar med följande kommando:

```bash
rake assets:precompile
```

Spår produktionsmiljön använder också hemligheter för att hantera säkerhet. Generera en hemlig nyckel.

```bash
rails secret
```

Spara den hemliga nyckeln för respektive variabler som används av spår produktionsmiljön. För enkelhetens skull använder du samma nyckel för både variabler.

```bash
export RAILS_MASTER_KEY=<output_of_rails_secret>
export SECRET_KEY_BASE=<output_of_rails_secret>
```

Aktivera spår produktionsmiljön för att hantera JavaScript och CSS-filer.

```bash
export RAILS_SERVE_STATIC_FILES=true
```

Kör exempelprogrammet i produktionsmiljön.

```bash
rails server -e production
```

Navigera till `http://localhost:3000`. Om sidan läses in utan fel, ansluter Ruby på spår programmet till MySQL-databas i Azure.

Lägg till några åtgärder på sidan.

![Ruby spår ansluter har till Azure-databas för MySQL (förhandsgranskning)](./media/tutorial-ruby-mysql-app/azure-mysql-connect-success.png)

Om du vill stoppa spår servern skriver `Ctrl + C` i terminalen.

### <a name="commit-your-changes"></a>Genomför ändringarna

Kör följande Git-kommandon för att genomföra ändringarna:

```bash
git add .
git commit -m "database.yml updates"
```

Appen är redo att distribueras.

## <a name="deploy-to-azure"></a>Distribuera till Azure

I det här steget kan du distribuera programmet till Azure App Service MySQL-anslutna spår.

### <a name="configure-a-deployment-user"></a>Konfigurera en distributionsanvändare

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Skapa en App Service-plan

[!INCLUDE [Create app service plan no h](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Skapa en webbapp

I Cloud Shell skapar du en webbapp i `myAppServicePlan` App Service-planen med kommandot [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create). 

Ersätt `<app_name>` med ett globalt unikt appnamn (giltiga tecken är `a-z`, `0-9` och `-`) i följande exempel. Körningen har angetts till `RUBY|2.3`, som distribuerar den [standard Ruby avbildningen](https://hub.docker.com/r/appsvc/ruby/). Om du vill se alla körningar som stöds kör du [az webapp list-runtimes](/cli/azure/webapp?view=azure-cli-latest#az_webapp_list_runtimes). 

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

I App Service som du anger miljövariabler som _appinställningar_ med hjälp av den [az webapp appsettings konfigurationsuppsättning](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) i molnet Shell.

Följande molntjänster Shell-kommando konfigurerar appinställningarna `DB_HOST`, `DB_DATABASE`, `DB_USERNAME`, och `DB_PASSWORD`. Ersätt platshållarna  _&lt;appname >_ och  _&lt;mysql_server_name >_.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DB_HOST="<mysql_server_name>.mysql.database.azure.com" DB_DATABASE="sampledb" DB_USERNAME="railsappuser@<mysql_server_name>" DB_PASSWORD="MySQLAzure2017"
```

### <a name="configure-rails-environment-variables"></a>Konfigurera spår miljövariabler

Skapa en ny hemlig nyckel för produktionsmiljön spår i Azure till lokala terminalen.

```bash
rails secret
```

Konfigurera de variabler som krävs av spår produktionsmiljön.

I följande molntjänster Shell-kommando ersätter två  _&lt;output_of_rails_secret >_ platshållarna med den nya hemliga nyckeln som du genererade på den lokala terminalen.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings RAILS_MASTER_KEY="<output_of_rails_secret>" SECRET_KEY_BASE="<output_of_rails_secret>" RAILS_SERVE_STATIC_FILES="true" ASSETS_PRECOMPILE="true"
```

`ASSETS_PRECOMPILE="true"`Anger standard Ruby-behållare för att förkompilera tillgångar vid varje Git-distribution.

### <a name="push-to-azure-from-git"></a>Skicka till Azure från Git

Lägg till en Azure remote till din lokala Git-lagringsplats i lokala terminalen.

```bash
git remote add azure <paste_copied_url_here>
```

Skicka till Azure remote distribuera Ruby på spår program. Du ombeds ange lösenordet du angav tidigare som en del av skapandet av distribution av användaren.

```bash
git push azure master
```

Under distributionen kommunicerar förloppet med Git i Azure App Service.

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

Bläddra till `http://<app_name>.azurewebsites.net` och lägga till några åtgärder i listan.

![Ruby på spår appen körs i Azure App Service](./media/tutorial-ruby-mysql-app/ruby-mysql-in-azure.png)

Grattis, du kör en datadrivna Ruby på spår app i Azure App Service.

## <a name="update-model-locally-and-redeploy"></a>Uppdatera modellen lokalt och distribuera

I det här steget du ändrar enkla till den `task` datamodellen och webapp, och sedan publicera uppdateringen till Azure.

För scenariot uppgifter ändra programmet så att du kan markera en aktivitet som slutförd.

### <a name="add-a-column"></a>Lägg till en kolumn

Navigera till roten för Git-lagringsplats i terminalen.

Generera en ny migrering som lägger till en boolesk kolumn med namnet `Done` till den `Tasks` tabellen:

```bash
rails generate migration add_Done_to_Tasks Done:boolean
```

Det här kommandot genererar en ny fil för migrering i den _db eller flytta_ directory.


I terminalen och kör spår databas migrering för att göra ändringen i den lokala databasen.

```bash
rake db:migrate
```

### <a name="update-application-logic"></a>Uppdatera programlogik

Öppna den *app/controllers/tasks_controller.rb* fil. Sök efter följande rad i slutet av filen:

```rb
params.require(:task).permit(:Description)
```

Ändra raden för att inkludera den nya `Done` parameter.

```rb
params.require(:task).permit(:Description, :Done)
```

### <a name="update-the-views"></a>Uppdatera vyer

Öppna den *app/views/tasks/_form.html.erb* fil som är Redigeringsformulär.

Hitta raden `<%=f.error_span(:Description) %>` och infoga följande kod direkt under den:

```erb
<%= f.label :Done, :class => 'control-label col-lg-2' %>
<div class="col-lg-10">
  <%= f.check_box :Done, :class => 'form-control' %>
</div>
```

Öppna den *app/views/tasks/show.html.erb* fil som är sidan Visa en post. 

Hitta raden `<dd><%= @task.Description %></dd>` och infoga följande kod direkt under den:

```erb
  <dt><strong><%= model_class.human_attribute_name(:Done) %>:</strong></dt>
  <dd><%= check_box "task", "Done", {:checked => @task.Done, :disabled => true}%></dd>
```

Öppna den *app/views/tasks/index.html.erb* fil som är indexsidan för alla poster.

Hitta raden `<th><%= model_class.human_attribute_name(:Description) %></th>` och infoga följande kod direkt under den:

```erb
<th><%= model_class.human_attribute_name(:Done) %></th>
```

Hitta raden i filen samma `<td><%= task.Description %></td>` och infoga följande kod direkt under den:

```erb
<td><%= check_box "task", "Done", {:checked => task.Done, :disabled => true} %></td>
```

### <a name="test-the-changes-locally"></a>Testa ändringar lokalt

Kör spår servern i den lokala terminalen.

```bash
rails server
```

Att se aktivitetsstatus ändra, gå till `http://localhost:3000` och lägga till eller redigera objekt.

![Tillagda kryssrutan till aktivitet](./media/tutorial-ruby-mysql-app/complete-checkbox.png)

Om du vill stoppa spår servern skriver `Ctrl + C` i terminalen.

### <a name="publish-changes-to-azure"></a>Publicera ändringar i Azure

I terminalen och kör spår databasen migreringar för produktionsmiljön för att utföra ändringen i Azure-databas.

```bash
rake db:migrate RAILS_ENV=production
```

Genomför alla ändringar i Git och skicka sedan koden ändringarna till Azure.

```bash
git add .
git commit -m "added complete checkbox"
git push azure master
```

En gång i `git push` är klar, gå till Azure webbapp och testa de nya funktionerna.

![Ändringar i modellen och databasen publiceras på Azure](media/tutorial-ruby-mysql-app/complete-checkbox-published.png)

Om du har lagt till alla uppgifter som finns kvar i databasen. Uppdateringar till dataschemat lämna befintliga data intakta.

## <a name="manage-the-azure-web-app"></a>Hantera Azure-webbappen

Gå till [Azure Portal](https://portal.azure.com) för att hantera den webbapp som du skapade.

Klicka på **Apptjänster** i menyn till vänster och sedan på namnet på din Azure-webbapp.

![Navigera till webbappen på Azure Portal](./media/tutorial-php-mysql-app/access-portal.png)

Nu visas sidan Översikt för din webbapp. Här kan du utföra grundläggande hanteringsuppgifter som att stoppa, starta, omstart, bläddra och ta bort.

Den vänstra menyn innehåller sidor för att konfigurera din app.

![App Service-sidan på Azure Portal](./media/tutorial-php-mysql-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

<a name="next"></a>

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa en MySQL-databas i Azure
> * Ansluta en Ruby på spår app till MySQL
> * Distribuera appen till Azure
> * Uppdatera datamodellen och distribuera appen
> * Dataströmmen diagnostiska loggar från Azure
> * Hantera appen i Azure-portalen

Gå vidare till nästa kurs att lära dig hur du mappar en anpassad DNS-namn till ett webbprogram.

> [!div class="nextstepaction"]
> [Mappa ett befintligt anpassat DNS-namn till Azure Web Apps](../app-service-web-tutorial-custom-domain.md)
