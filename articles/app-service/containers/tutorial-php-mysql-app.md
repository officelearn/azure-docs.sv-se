---
title: "Skapa en PHP- och MySQL-webbapp i Azure App Service på Linux | Microsoft Docs"
description: "Lär dig hur du hämtar en PHP-app som arbetar i Azure, med anslutning till en MySQL-databas i Azure."
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
ms.service: app-service-web
ms.workload: web
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 11/28/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: bf6efd96bea8a6f563ec72d5469d91b4cbfbd5fe
ms.sourcegitcommit: 3fca41d1c978d4b9165666bb2a9a1fe2a13aabb6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2017
---
# <a name="build-a-php-and-mysql-web-app-in-azure-app-service-on-linux"></a>Skapa en PHP- och MySQL-webbapp i Azure App Service på Linux

> [!NOTE]
> Den här artikeln distribuerar en app till App Service på Linux. Du distribuerar till App Service på _Windows_, se [skapa en PHP- och MySQL-webbapp i Azure](../app-service-web-tutorial-php-mysql.md).
>

Med [App Service i Linux](app-service-linux-intro.md) får du en mycket skalbar och automatiskt uppdaterad webbvärdtjänst som utgår från operativsystemet Linux. Den här kursen visar hur du skapar ett PHP-webbprogram och ansluta till en MySQL-databas. När du är klar har du en [Laravel](https://laravel.com/) app som körs i Apptjänsten på Linux.

![PHP-app som körs i Azure App Service](./media/tutorial-php-mysql-app/complete-checkbox-published.png)

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Skapa en MySQL-databas i Azure
> * Ansluta en PHP-app till MySQL
> * Distribuera appen till Azure
> * Uppdatera datamodellen och distribuera appen
> * Dataströmmen diagnostiska loggar från Azure
> * Hantera appen i Azure-portalen

## <a name="prerequisites"></a>Krav

För att slutföra den här kursen behöver du:

* [Installera Git](https://git-scm.com/)
* [Installera PHP 5.6.4 eller senare](http://php.net/downloads.php)
* [Installera Composer](https://getcomposer.org/doc/00-intro.md)
* Aktivera följande PHP-tillägg Laravel behov: OpenSSL, PDO MySQL, Mbstring, Tokenizer, XML
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

### <a name="create-a-database-locally"></a>Skapa en databas lokalt

På den `mysql` uppmanar, skapa en databas.

```sql 
CREATE DATABASE sampledb;
```

Avsluta anslutningen till servern genom att skriva `quit`.

```sql
quit
```

<a name="step2"></a>

## <a name="create-a-php-app-locally"></a>Skapa en PHP-app lokalt
I det här steget hämta ett Laravel exempelprogram, konfigurera dess databasanslutningen och köra det lokalt. 

### <a name="clone-the-sample"></a>Klona exemplet

I fönstret terminal `cd` till en arbetskatalog.

Klona exempellagringsplatsen med följande kommando.

```bash
git clone https://github.com/Azure-Samples/laravel-tasks
```

`cd`till klonade-katalogen.
Installera de nödvändiga paketen.

```bash
cd laravel-tasks
composer install
```

### <a name="configure-mysql-connection"></a>Konfigurera MySQL-anslutning

Skapa en fil med namnet i Lagringsplatsens rot *.env*. Kopiera följande variabler i den *.env* fil. Ersätt den  _&lt;root_password >_ med MySQL rotanvändarens lösenord.

```txt
APP_ENV=local
APP_DEBUG=true
APP_KEY=SomeRandomString

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_DATABASE=sampledb
DB_USERNAME=root
DB_PASSWORD=<root_password>
```

Mer information om hur Laravel använder den _.env_ fil, se [Laravel miljö Configuration](https://laravel.com/docs/5.4/configuration#environment-configuration).

### <a name="run-the-sample-locally"></a>Köra exemplet lokalt

Kör [Laravel databasen migreringar](https://laravel.com/docs/5.4/migrations) skapa tabellerna programmet behöver. Om du vill se vilka tabeller skapas av migreringar finns i den _databasen/migreringar_ katalog i Git-lagringsplats.

```bash
php artisan migrate
```

Skapa en ny Laravel programmet nyckel.

```bash
php artisan key:generate
```

Kör appen.

```bash
php artisan serve
```

Gå till `http://localhost:8000` i en webbläsare. Lägg till några åtgärder på sidan.

![PHP ansluter har till MySQL](./media/tutorial-php-mysql-app/mysql-connect-success.png)

Om du vill stoppa PHP skriver `Ctrl + C` i terminalen.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-mysql-in-azure"></a>Skapa MySQL i Azure

I det här steget skapar du en MySQL-databas i [Azure-databas för MySQL (förhandsgranskning)](/azure/mysql). Senare kan konfigurera du PHP-program att ansluta till den här databasen.

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-no-h.md)] 

### <a name="create-a-mysql-server"></a>Skapa en MySQL-server

Skapa en server i Azure-databas för MySQL (förhandsversion) med den [az mysql-servern skapa](/cli/azure/mysql/server?view=azure-cli-latest#az_mysql_server_create) kommando.

Ersätt namnet på MySQL-servern där du ser i följande kommando i  _&lt;mysql_server_name >_ platshållare (giltiga tecken är `a-z`, `0-9`, och `-`). Det här namnet är en del av MySQL-serverns värdnamn (`<mysql_server_name>.database.windows.net`), den måste vara globalt unika.

```azurecli-interactive
az mysql server create --name <mysql_server_name> --resource-group myResourceGroup --location "North Europe" --admin-user adminuser --admin-password MySQLAzure2017 --ssl-enforcement Disabled
```

När MySQL-servern har skapats visas Azure CLI information liknar följande exempel:

```json
{
  "administratorLogin": "adminuser",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "<mysql_server_name>.database.windows.net",
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
mysql -u adminuser@<mysql_server_name> -h <mysql_server_name>.database.windows.net -P 3306 -p
```

När du uppmanas att ange ett lösenord, Använd _tr0ngPa $$ w0rd!_, som du angav när du skapade databasen.

### <a name="create-a-production-database"></a>Skapa en produktionsdatabas

På den `mysql` uppmanar, skapa en databas.

```sql
CREATE DATABASE sampledb;
```

### <a name="create-a-user-with-permissions"></a>Skapa en användare med behörighet

Skapa en databasanvändare som kallas _phpappuser_ och ge alla behörigheter i den `sampledb` databas.

```sql
CREATE USER 'phpappuser' IDENTIFIED BY 'MySQLAzure2017'; 
GRANT ALL PRIVILEGES ON sampledb.* TO 'phpappuser';
```

Avsluta server-anslutningen genom att skriva `quit`.

```sql
quit
```

## <a name="connect-app-to-azure-mysql"></a>Anslut appen till Azure MySQL

I det här steget kan ansluta du PHP-program på MySQL-databas som du skapade i Azure-databas för MySQL (förhandsversion).

<a name="devconfig"></a>

### <a name="configure-the-database-connection"></a>Konfigurera anslutningen till databasen

I Lagringsplatsens rot, skapa en _. env.production_ filen och kopiera följande variabler i den. Ersätt platshållaren  _&lt;mysql_server_name >_.

```txt
APP_ENV=production
APP_DEBUG=true
APP_KEY=SomeRandomString

DB_CONNECTION=mysql
DB_HOST=<mysql_server_name>.database.windows.net
DB_DATABASE=sampledb
DB_USERNAME=phpappuser@<mysql_server_name>
DB_PASSWORD=MySQLAzure2017
```
<!-- MYSQL_SSL=true -->

Spara ändringarna.

> [!TIP]
> Om du vill skydda din MySQL anslutningsinformationen den här filen har redan exkluderats från Git-lagringsplats (se _.gitignore_ i Lagringsplatsens rot). Senare kan du lära dig hur du konfigurerar miljövariabler i App Service för att ansluta till databasen i Azure-databas för MySQL (förhandsversion). Med miljövariabler, behöver du inte den *.env* filen i App Service.
>

<!-- ### Configure SSL certificate

By default, Azure Database for MySQL enforces SSL connections from clients. To connect to your MySQL database in Azure, you must use a _.pem_ SSL certificate.

Open _config/database.php_ and add the _sslmode_ and _options_ parameters to `connections.mysql`, as shown in the following code.

```php
'mysql' => [
    ...
    'sslmode' => env('DB_SSLMODE', 'prefer'),
    'options' => (env('MYSQL_SSL')) ? [
        PDO::MYSQL_ATTR_SSL_KEY    => '/ssl/certificate.pem', 
    ] : []
],
```

To learn how to generate this _certificate.pem_, see [Configure SSL connectivity in your application to securely connect to Azure Database for MySQL](../../mysql/howto-configure-ssl.md).

> [!TIP]
> The path _/ssl/certificate.pem_ points to an existing _certificate.pem_ file in the Git repository. This file is provided for convenience in this tutorial. For best practice, you should not commit your _.pem_ certificates into source control. 
> -->

### <a name="test-the-application-locally"></a>Testa programmet lokalt

Kör Laravel databasen migreringar med _. env.production_ som filen miljö att skapa tabellerna i din MySQL-databas på Azure-databas för MySQL (förhandsversion). Kom ihåg att _. env.production_ har anslutningsinformationen så att MySQL-databas i Azure.

```bash
php artisan migrate --env=production --force
```

_. env.production_ inte redan har en giltig App-nyckel. Generera en ny för det i terminalen.

```bash
php artisan key:generate --env=production --force
```

Kör exempelprogrammet med _. env.production_ som filen miljö.

```bash
php artisan serve --env=production
```

Navigera till `http://localhost:8000`. Om sidan läses in utan fel, ansluter PHP-program till MySQL-databas i Azure.

Lägg till några åtgärder på sidan.

![PHP kan upprätta anslutningen till Azure-databas för MySQL (förhandsgranskning)](./media/tutorial-php-mysql-app/mysql-connect-success.png)

Om du vill stoppa PHP skriver `Ctrl + C` i terminalen.

### <a name="commit-your-changes"></a>Genomför ändringarna

Kör följande Git-kommandon för att genomföra ändringarna:

```bash
git add .
git commit -m "database.php updates"
```

Appen är redo att distribueras.

## <a name="deploy-to-azure"></a>Distribuera till Azure

I det här steget kan distribuera du MySQL-anslutna PHP-program till Azure App Service.

### <a name="configure-a-deployment-user"></a>Konfigurera en distributionsanvändare

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Skapa en App Service-plan

[!INCLUDE [Create app service plan no h](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Skapa en webbapp

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-php-no-h.md)] 

### <a name="configure-database-settings"></a>Konfigurera databasinställningar för

I App Service som du anger miljövariabler som _appinställningar_ med hjälp av den [az webapp appsettings konfigurationsuppsättning](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) kommando.

Följande kommando konfigurerar appinställningarna `DB_HOST`, `DB_DATABASE`, `DB_USERNAME`, och `DB_PASSWORD`. Ersätt platshållarna  _&lt;appname >_ och  _&lt;mysql_server_name >_.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DB_HOST="<mysql_server_name>.database.windows.net" DB_DATABASE="sampledb" DB_USERNAME="phpappuser@<mysql_server_name>" DB_PASSWORD="MySQLAzure2017"
```
 <!-- MYSQL_SSL="true" -->

Du kan använda PHP [getenv](http://www.php.net/manual/function.getenv.php) metod för att komma åt inställningarna. Laravel koden använder en [env](https://laravel.com/docs/5.4/helpers#method-env) wrapper över PHP `getenv`. Till exempel MySQL konfigurationen i _config/database.php_ ser ut som följande kod:

```php
'mysql' => [
    'driver'    => 'mysql',
    'host'      => env('DB_HOST', 'localhost'),
    'database'  => env('DB_DATABASE', 'forge'),
    'username'  => env('DB_USERNAME', 'forge'),
    'password'  => env('DB_PASSWORD', ''),
    ...
],
```

### <a name="configure-laravel-environment-variables"></a>Konfigurera Laravel miljövariabler

Laravel måste en tangent i App Service. Du kan konfigurera den med app-inställningar.

Använd `php artisan` att generera en ny nyckel för program utan att spara den till _.env_.

```bash
php artisan key:generate --show
```

Ange nyckeln för programmet i App Service webbapp med hjälp av den [az webapp konfigurationsuppsättning appsettings](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) kommando. Ersätt platshållarna  _&lt;appname >_ och  _&lt;outputofphpartisankey: Generera >_.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings APP_KEY="<output_of_php_artisan_key:generate>" APP_DEBUG="true"
```

`APP_DEBUG="true"`Anger Laravel returnerar felsökningsinformation när den distribuerade webbappen påträffar ett fel. När du kör ett produktionsprogram värdet `false`, vilket är säkrare.

### <a name="set-the-virtual-application-path"></a>Ange sökväg till virtuella program

Ange den virtuella sökvägen för webbprogrammet. Det här steget är nödvändigt eftersom den [Laravel programmet livscykel](https://laravel.com/docs/5.4/lifecycle) börjar i den _offentliga_ katalog i stället för i tillämpningsprogrammets rotkatalog. Andra PHP-ramverk vars livscykel startar i rotkatalogen kan arbeta utan manuell konfiguration av den virtuella sökvägen.

Ange sökvägen till virtuella programmet med hjälp av den [az resurs uppdaterades](/cli/azure/resource?view=azure-cli-latest#az_resource_update) kommando. Ersätt den  _&lt;appname >_ platshållare.

```azurecli-interactive
az resource update --name web --resource-group myResourceGroup --namespace Microsoft.Web --resource-type config --parent sites/<app_name> --set properties.virtualApplications[0].physicalPath="site\wwwroot\public" --api-version 2015-06-01
```

Som standard pekar rotsökvägen för virtuella program i Azure App Service (_/_) till rotkatalogen för distribuerade programfilerna (_sites\wwwroot_).

### <a name="push-to-azure-from-git"></a>Skicka till Azure från Git

Lägg till en Azure-fjärrdatabas till din lokala Git-databas.

```bash
git remote add azure <paste_copied_url_here>
```

Skicka till Azure remote distribuera PHP-program. Du ombeds ange lösenordet du angav tidigare som en del av skapandet av distribution av användaren.

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

> [!NOTE]
> Det kan hända att distributionsprocessen installerar [Composer](https://getcomposer.org/) paket i slutet. App Service körs inte dessa automatiseringar under distributionen av standard, så det här exemplet databasen har tre ytterligare filer i rotkatalogen för att den:
>
> - `.deployment`-Den här filen talar om App Service för att köra `bash deploy.sh` som anpassade distributions-skriptet.
> - `deploy.sh`-Anpassade skriptet för distribution. Om du granska filen ser du att den körs `php composer.phar install` när `npm install`.
> - `composer.phar`-Composer package manager.
>
> Du kan använda den här metoden för att lägga till något steg i distributionen Git-baserade till App Service. Mer information finns i [anpassat distributionsskriptet](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).
>

### <a name="browse-to-the-azure-web-app"></a>Bläddra till Azure-webbappen

Bläddra till `http://<app_name>.azurewebsites.net` och lägga till några åtgärder i listan.

![PHP-app som körs i Azure App Service](./media/tutorial-php-mysql-app/php-mysql-in-azure.png)

Grattis, du kör en datadrivna PHP-app i Azure App Service.

## <a name="update-model-locally-and-redeploy"></a>Uppdatera modellen lokalt och distribuera

I det här steget du ändrar enkla till den `task` datamodellen och webapp, och sedan publicera uppdateringen till Azure.

För scenariot uppgifter ändra programmet så att du kan markera en aktivitet som slutförd.

### <a name="add-a-column"></a>Lägg till en kolumn

Navigera till roten för Git-lagringsplats i terminalen.

Generera en ny Databasmigrering för den `tasks` tabellen:

```bash
php artisan make:migration add_complete_column --table=tasks
```

Det här kommandot visar namnet på migreringsfilen som genereras. Den här filen i _databasen/migreringar_ och öppna den.

Ersätt den `up` metoden med följande kod:

```php
public function up()
{
    Schema::table('tasks', function (Blueprint $table) {
        $table->boolean('complete')->default(False);
    });
}
```

Föregående kod lägger till en boolesk kolumn i den `tasks` tabell som kallas `complete`.

Ersätt den `down` metod med följande kod för Återföringsåtgärd:

```php
public function down()
{
    Schema::table('tasks', function (Blueprint $table) {
        $table->dropColumn('complete');
    });
}
```

I terminalen och kör Laravel databasen migrering för att göra ändringen i den lokala databasen.

```bash
php artisan migrate
```

Baserat på den [Laravel namngivningskonvention](https://laravel.com/docs/5.4/eloquent#defining-models), modellen `Task` (se _app/Task.php_) mappas till den `tasks` tabellen som standard.

### <a name="update-application-logic"></a>Uppdatera programlogik

Öppna den *routes/web.php* fil. Programmet definierar dess vägar och affärslogik här.

Lägga till en väg med följande kod i slutet av filen:

```php
/**
 * Toggle Task completeness
 */
Route::post('/task/{id}', function ($id) {
    error_log('INFO: post /task/'.$id);
    $task = Task::findOrFail($id);

    $task->complete = !$task->complete;
    $task->save();

    return redirect('/');
});
```

Föregående kod gör en enkel uppdatering till datamodellen genom att klicka på värdet för `complete`.

### <a name="update-the-view"></a>Uppdatera vyn

Öppna den *resources/views/tasks.blade.php* fil. Hitta de `<tr>` startkoden och Ersätt den med:

```html
<tr class="{{ $task->complete ? 'success' : 'active' }}" >
```

Föregående kod ändrar färg raden beroende på om aktiviteten är klar.

På nästa rad kan du använda följande kod:

```html
<td class="table-text"><div>{{ $task->name }}</div></td>
```

Ersätt hela raden med följande kod:

```html
<td>
    <form action="{{ url('task/'.$task->id) }}" method="POST">
        {{ csrf_field() }}

        <button type="submit" class="btn btn-xs">
            <i class="fa {{$task->complete ? 'fa-check-square-o' : 'fa-square-o'}}"></i>
        </button>
        {{ $task->name }}
    </form>
</td>
```

Föregående kod lägger till skickaknappen som refererar till det flöde som du angav tidigare.

### <a name="test-the-changes-locally"></a>Testa ändringar lokalt

Kör utvecklingsservern från rotkatalogen på Git-lagringsplats.

```bash
php artisan serve
```

Att se aktivitetsstatus ändra, gå till `http://localhost:8000` och markera kryssrutan.

![Tillagda kryssrutan till aktivitet](./media/tutorial-php-mysql-app/complete-checkbox.png)

Om du vill stoppa PHP skriver `Ctrl + C` i terminalen.

### <a name="publish-changes-to-azure"></a>Publicera ändringar i Azure

I terminalen och kör Laravel databasen migreringar med anslutningssträngen produktion göra ändringar i Azure-databasen.

```bash
php artisan migrate --env=production --force
```

Genomför alla ändringar i Git och skicka sedan koden ändringarna till Azure.

```bash
git add .
git commit -m "added complete checkbox"
git push azure master
```

En gång i `git push` är klar, gå till Azure webbapp och testa de nya funktionerna.

![Ändringar i modellen och databasen publiceras på Azure](media/tutorial-php-mysql-app/complete-checkbox-published.png)

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
> * Ansluta en PHP-app till MySQL
> * Distribuera appen till Azure
> * Uppdatera datamodellen och distribuera appen
> * Dataströmmen diagnostiska loggar från Azure
> * Hantera appen i Azure-portalen

Gå vidare till nästa kurs att lära dig hur du mappar en anpassad DNS-namn till ett webbprogram.

> [!div class="nextstepaction"]
> [Mappa ett befintligt anpassat DNS-namn till Azure Web Apps](../app-service-web-tutorial-custom-domain.md)
