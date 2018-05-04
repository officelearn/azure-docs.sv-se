---
title: Skapa en PHP- och MySQL-webbapp i Azure | Microsoft Docs
description: Lär dig hur du får igång en PHP-app i Azure med anslutning till en MySQL-databas i Azure.
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: ''
ms.assetid: 14feb4f3-5095-496e-9a40-690e1414bd73
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 10/20/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 6e2803410c50b47fdaa80654e5e6e61a3807fb43
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
---
# <a name="tutorial-build-a-php-and-mysql-web-app-in-azure"></a>Självstudie: Skapa en PHP- och MySQL-webbapp i Azure

> [!NOTE]
> I den här artikeln distribueras en app till App Service i Windows. Om du vill distribuera en app till App Service i _Linux_ kan du läsa [Skapa en PHP- och MySQL-webbapp i Azure App Service på Linux](./containers/tutorial-php-mysql-app.md).
>

Med [Azure Web Apps](app-service-web-overview.md) får du en mycket skalbar och automatiskt uppdaterad webbvärdtjänst. I den här självstudien visas hur du skapar en PHP-webbapp i Azure och ansluter den till en MySQL-databas. När du är klar har du en [Laravel](https://laravel.com/)-app som körs i Azure App Service Web Apps.

![PHP-app som körs i Azure App Service](./media/app-service-web-tutorial-php-mysql/complete-checkbox-published.png)

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * skapa en MySQL-databas i Azure
> * ansluta en PHP-app till MySQL
> * distribuera appen till Azure
> * uppdatera datamodellen och distribuera om appen
> * strömma diagnostikloggar från Azure
> * hantera appen i Azure-portalen.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

För att slutföra den här självstudien behöver du:

* [Installera Git](https://git-scm.com/)
* [Installera PHP 5.6.4 eller senare](http://php.net/downloads.php)
* [Installera Composer](https://getcomposer.org/doc/00-intro.md)
* Aktivera följande PHP-tillägg som behövs för Laravel: OpenSSL, PDO-MySQL, Mbstring, Tokenizer och XML
* [Installera och starta MySQL](https://dev.mysql.com/doc/refman/5.7/en/installing.html) 

## <a name="prepare-local-mysql"></a>Förbereda lokal MySQL

I det här steget skapar du en databas för självstudien på din lokala MySQL-server.

### <a name="connect-to-local-mysql-server"></a>Ansluta till en lokal MySQL-server

Anslut till din lokala MySQL-server via ett terminalfönster. Du kan använda det här terminalfönstret för att köra alla kommandon i den här självstudien.

```bash
mysql -u root -p
```

Om du uppmanas att ange ett lösenord anger du lösenordet för `root`-kontot. Se [MySQL: Återställa rotlösenordet](https://dev.mysql.com/doc/refman/5.7/en/resetting-permissions.html) om du inte kommer ihåg rotlösenordet för ditt konto.

MySQL-servern är igång om kommandot körs utan problem. Om inte, kontrollerar du att den lokala MySQL-servern är igång genom att följa [anvisningarna efter installation av MySQL](https://dev.mysql.com/doc/refman/5.7/en/postinstallation.html).

### <a name="create-a-database-locally"></a>Skapa en lokal databas

Skapa en databas i `mysql`-prompten.

```sql 
CREATE DATABASE sampledb;
```

Skriv `quit` för att avsluta serveranslutningen.

```sql
quit
```

<a name="step2"></a>

## <a name="create-a-php-app-locally"></a>Skapa en PHP-app lokalt
I det här steget hämtar du en Laravel-exempelapp, konfigurerar dess databasanslutning och kör den lokalt. 

### <a name="clone-the-sample"></a>Klona exemplet

Använd kommandot `cd` för att komma till en arbetskatalog i terminalfönstret.

Klona exempellagringsplatsen med följande kommando.

```bash
git clone https://github.com/Azure-Samples/laravel-tasks
```

`cd` till den klonade katalogen.
Installera de paket som behövs.

```bash
cd laravel-tasks
composer install
```

### <a name="configure-mysql-connection"></a>Konfigurera MySQL-anslutningen

Skapa en textfil med namnet *.env* i lagringsplatsens rot. Kopiera in följande variabler i *.env*-filen. Ersätt platshållaren _&lt;root_password>_ med MySQL-rotanvändarens lösenord.

```
APP_ENV=local
APP_DEBUG=true
APP_KEY=

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_DATABASE=sampledb
DB_USERNAME=root
DB_PASSWORD=<root_password>
```

Mer information om hur Laravel använder _.env_-filen finns i [Laravel Environment Configuration](https://laravel.com/docs/5.4/configuration#environment-configuration) (Konfiguration av Laravel-miljö).

### <a name="run-the-sample-locally"></a>Köra exemplet lokalt

Kör [Laravel-databasmigreringar](https://laravel.com/docs/5.4/migrations) för att skapa de tabeller som behövs för appen. Du kan se vilka tabeller som skapas i migreringarna i katalogen _database/migrations_ (databas/migreringar) på Git-lagringsplatsen.

```bash
php artisan migrate
```

Generera en ny Laravel-programnyckel.

```bash
php artisan key:generate
```

Kör appen.

```bash
php artisan serve
```

Gå till `http://localhost:8000` i en webbläsare. Lägg till några uppgifter på sidan.

![PHP ansluter till MySQL](./media/app-service-web-tutorial-php-mysql/mysql-connect-success.png)

Om du vill stoppa PHP-servern skriver du `Ctrl + C` i terminalen.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-mysql-in-azure"></a>Skapa MySQL i Azure

I det här steget skapar du en MySQL-databas i [Azure Database for MySQL](/azure/mysql). Senare kommer du att konfigurera PHP-appen för att ansluta till den här databasen.

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)] 

### <a name="create-a-mysql-server"></a>Skapa en MySQL-server

I Cloud Shell skapar du en server i Azure Database for MySQL med kommandot [`az mysql server create`](/cli/azure/mysql/server?view=azure-cli-latest#az_mysql_server_create).

I följande kommando byter du ut platshållaren *\<mysql_server_name>* mot ett unikt servernamn, platshållaren *\<admin_user>* mot ett användarnamn och platshållaren *\<admin_password>* mot ett lösenord. Det här servernamnet används som en del av PostgreSQL-slutpunkten (`https://<mysql_server_name>.mysql.database.azure.com`), så namnet måste vara unikt för alla servrar i Azure.

```azurecli-interactive
az mysql server create --resource-group myResourceGroup --name mydemoserver --location "West Europe" --admin-user <admin_user> --admin-password <server_admin_password> --sku-name GP_Gen4_2
```

> [!NOTE]
> Eftersom du använder flera olika autentiseringsuppgifter i den här självstudiekursen är `--admin-user` och `--admin-password` angivna till dummy-värden för att undvika förvirring. I en produktionsmiljö bör du följa rekommenderade säkerhetsmetoder när du väljer ett bra användarnamn och lösenord för MySQL-server i Azure.
>
>

När MySQL-servern skapas visar Azure CLI information som ser ut ungefär så här:

```json
{
  "additionalProperties": {},
  "administratorLogin": "<admin_user>",
  "earliestRestoreDate": "2018-04-19T22:56:40.990000+00:00",
  "fullyQualifiedDomainName": "<mysql_server_name>.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/<mysql_server_name>",
  "location": "westeurope",
  "name": "<mysql_server_name>",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "additionalProperties": {},
    "capacity": 2,
    "family": "Gen4",
    "name": "GP_Gen4_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "sslEnforcement": "Enabled",
  "storageProfile": {
    "additionalProperties": {},
    "backupRetentionDays": 7,
    "geoRedundantBackup": "Disabled",
    "storageMb": 5120
  },
  "tags": null,
  "type": "Microsoft.DBforMySQL/servers",
  "userVisibleState": "Ready",
  "version": "5.7"
}
```

### <a name="configure-server-firewall"></a>Konfigurera serverbrandväggen

Skapa i Cloud Shell en brandväggsregel för MySQL-servern för att tillåta klientanslutningar med hjälp av kommandot [`az mysql server firewall-rule create`](/cli/azure/mysql/server/firewall-rule?view=azure-cli-latest#az_mysql_server_firewall_rule_create). När både start-IP och slut-IP har angetts till 0.0.0.0 öppnas brandväggen endast för andra Azure-resurser. 

```azurecli-interactive
az mysql server firewall-rule create --name allAzureIPs --server <mysql_server_name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP] 
> Du kan begränsa brandväggsregeln ännu mer genom att [endast använda de utgående IP-adresser som används av din app](app-service-ip-addresses.md#find-outbound-ips).
>

### <a name="connect-to-production-mysql-server-locally"></a>Ansluta lokalt till MySQL-produktionsservern

Anslut till MySQL-server i Azure via det lokala terminalfönstret. Använd det värde du angav tidigare för _&lt;mysql_server_name>_. När du uppmanas att ange ett lösenord använder du lösenordet som du angav när du skapade databasen i Azure.

```bash
mysql -u <admin_user>@<mysql_server_name> -h <mysql_server_name>.mysql.database.azure.com-P 3306 -p
```

### <a name="create-a-production-database"></a>Skapa en produktionsdatabas

Skapa en databas i `mysql`-prompten.

```sql
CREATE DATABASE sampledb;
```

### <a name="create-a-user-with-permissions"></a>Skapa en användare med behörigheter

Skapa en databasanvändare med namnet _phpappuser_ och ge användaren alla privilegier i `sampledb`-databasen. För enkelhetens skull i kursen, använd _MySQLAzure2017_ som lösenord igen.

```sql
CREATE USER 'phpappuser' IDENTIFIED BY 'MySQLAzure2017'; 
GRANT ALL PRIVILEGES ON sampledb.* TO 'phpappuser';
```

Skriv `quit` för att avsluta serveranslutningen.

```sql
quit
```

## <a name="connect-app-to-azure-mysql"></a>Ansluta appen till Azure MySQL

I det här steget ansluter du PHP-programmet till MySQL-databasen som du skapade i Azure Database for MySQL.

<a name="devconfig"></a>

### <a name="configure-the-database-connection"></a>Konfigurera databasanslutningen

Skapa en _.env.production_-fil i lagringsplatsens rot och kopiera in följande variabler i filen. Ersätt platshållaren _&lt;mysql_server_name>_ i både *DB_HOST* och *DB_USERNAME*.

```
APP_ENV=production
APP_DEBUG=true
APP_KEY=

DB_CONNECTION=mysql
DB_HOST=<mysql_server_name>.mysql.database.azure.com
DB_DATABASE=sampledb
DB_USERNAME=phpappuser@<mysql_server_name>
DB_PASSWORD=MySQLAzure2017
MYSQL_SSL=true
```

Spara ändringarna.

> [!TIP]
> För att skydda din MySQL-anslutningsinformation är den här filen redan undantagen från Git-lagringsplatsen (se _.gitignore_ i lagringsplatsens rot). Senare får du lära dig hur du konfigurerar miljövariabler i App Service för att ansluta till din databas i Azure Database for MySQL. När du använder miljövariabler behöver du inte *.env*-filen i App Service.
>

### <a name="configure-ssl-certificate"></a>Konfigurera ett SSL-certifikat

Azure Database for MySQL använder som standard SSL-anslutningar från klienter. För att ansluta till din MySQL-databas i Azure måste du använda [_.pem_-certifikatet som tillhandahålls av Azure Database for MySQL](../mysql/howto-configure-ssl.md).

Öppna _config/database.php_ och lägg till parametrarna `sslmode` och `options` i `connections.mysql`, som i följande kod.

```php
'mysql' => [
    ...
    'sslmode' => env('DB_SSLMODE', 'prefer'),
    'options' => (env('MYSQL_SSL')) ? [
        PDO::MYSQL_ATTR_SSL_KEY    => '/ssl/BaltimoreCyberTrustRoot.crt.pem', 
    ] : []
],
```

I den här kursen finns certifikatet `BaltimoreCyberTrustRoot.crt.pem` på lagringsplatsen så att du enkelt kommer åt det. 

### <a name="test-the-application-locally"></a>Testa appen lokalt

Kör Laravel-databasmigreringar med _.env.production_ som miljöfil för att skapa tabellerna i din MySQL-databas i Azure Database for MySQL. Tänk på att anslutningsinformationen till din MySQL-databas i Azure finns i _.env.production_.

```bash
php artisan migrate --env=production --force
```

_.env.production_ innehåller inte någon giltig programnyckel ännu. Generera en ny nyckel för den i terminalen.

```bash
php artisan key:generate --env=production --force
```

Kör exempelprogrammet med _.env.production_ som miljöfil.

```bash
php artisan serve --env=production
```

Navigera till `http://localhost:8000`. Om sidan läses in utan fel ansluter PHP-appen till MySQL-databasen i Azure.

Lägg till några uppgifter på sidan.

![PHP har anslutits till Azure Database for MySQL](./media/app-service-web-tutorial-php-mysql/mysql-connect-success.png)

Om du vill stoppa PHP skriver du `Ctrl + C` i terminalen.

### <a name="commit-your-changes"></a>Genomföra ändringarna

Kör följande Git-kommandon för att genomföra ändringarna:

```bash
git add .
git commit -m "database.php updates"
```

Din app är klar att distribueras.

## <a name="deploy-to-azure"></a>Distribuera till Azure

I det här steget distribuerar du din MySQL-anslutna PHP-app till Azure App Service.

### <a name="configure-a-deployment-user"></a>Konfigurera en distributionsanvändare

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Skapa en App Service-plan

[!INCLUDE [Create app service plan no h](../../includes/app-service-web-create-app-service-plan-no-h.md)]

<a name="create"></a>
### <a name="create-a-web-app"></a>Skapa en webbapp

[!INCLUDE [Create web app no h](../../includes/app-service-web-create-web-app-php-no-h.md)] 

### <a name="configure-database-settings"></a>Konfigurera databasinställningarna

Som tidigare nämnts kan du ansluta till din Azure MySQL-databas med miljövariabler i App Service.

Ange i Cloud Shell miljövariabler som _appinställningar_ med kommandot [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set).

Följande kommando konfigurerar appinställningarna `DB_HOST`, `DB_DATABASE`, `DB_USERNAME` och `DB_PASSWORD`. Ersätt platshållarna _&lt;appname>_ och _&lt;mysql_server_name>_.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DB_HOST="<mysql_server_name>.mysql.database.azure.com" DB_DATABASE="sampledb" DB_USERNAME="phpappuser@<mysql_server_name>" DB_PASSWORD="MySQLAzure2017" MYSQL_SSL="true"
```

Du kan komma åt inställningarna med PHP-metoden [getenv](http://www.php.net/manual/function.getenv.php). I Laravel-koden används en [env](https://laravel.com/docs/5.4/helpers#method-env)-omslutning över PHP `getenv`. MySQL-konfigurationen i _config/database.php_ ser till exempel ut som följande kod:

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

### <a name="configure-laravel-environment-variables"></a>Konfigurera Laravel-miljövariabler

Laravel måste ha en programnyckel i App Service. Du kan konfigurera den med appinställningar.

Gå till det lokala terminalfönstret och använd `php artisan` för att generera en ny programnyckel utan att spara den i _.env_.

```bash
php artisan key:generate --show
```

Gå till Cloud Shell och ange programnyckeln i App Service-webbappen hjälp av kommandot [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set). Ersätt platshållarna _&lt;appname>_ och _&lt;outputofphpartisankey:generate>_.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings APP_KEY="<output_of_php_artisan_key:generate>" APP_DEBUG="true"
```

`APP_DEBUG="true"` meddelar Laravel att felsökningsinformation ska returneras om det uppstår problem med den distribuerade webbappen. När du kör en produktionsapp anger du värdet `false`, vilket är säkrare.

### <a name="set-the-virtual-application-path"></a>Ange sökväg till virtuella program

Ange den virtuella sökvägen för webbappen. Det här steget är nödvändigt eftersom [Laravel-programmets livscykel](https://laravel.com/docs/5.4/lifecycle) börjar i den _offentliga_ katalogen i stället för programmets rotkatalog. Andra PHP-ramverk vilkas livscykel startar i rotkatalogen fungerar utan manuell konfiguration av den virtuella programsökvägen.

Ange i Cloud Shell den virtuella sökvägen för appen med hjälp av kommandot [`az resource update`](/cli/azure/resource#az_resource_update). Ersätt platshållaren _&lt;appname>_.

```azurecli-interactive
az resource update --name web --resource-group myResourceGroup --namespace Microsoft.Web --resource-type config --parent sites/<app_name> --set properties.virtualApplications[0].physicalPath="site\wwwroot\public" --api-version 2015-06-01
```

Som standard pekar Azure App Service den virtuella rotsökvägen för appen (_/_) till rotkatalogen för de distribuerade programfilerna (_sites\wwwroot_).

### <a name="push-to-azure-from-git"></a>Skicka till Azure från Git

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

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
> Du kanske märker att distributionsprocessen installerar [Composer](https://getcomposer.org/)-paket i slutet. App Service kör inte dessa automatiseringar under standarddistributionen. Den här exempellagringsplatsen har tre extra filer i rotkatalogen för detta:
>
> - `.deployment` – Den här filen skickar ett meddelande till App Service om att köra `bash deploy.sh` som anpassat distributionsskript.
> - `deploy.sh` – Det anpassade distributionsskriptet. Om du granskar filen ser du att den kör `php composer.phar install` efter `npm install`.
> - `composer.phar` – Composer-pakethanteraren.
>
> Du kan använda den här metoden för att lägga till steg i den Git-baserade distributionen till App Service. Mer information finns i [Anpassat distributionsskript](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).
>

### <a name="browse-to-the-azure-web-app"></a>Bläddra till Azure-webbappen

Bläddra till `http://<app_name>.azurewebsites.net` och lägg till några uppgifter i listan.

![PHP-app som körs i Azure App Service](./media/app-service-web-tutorial-php-mysql/php-mysql-in-azure.png)

Grattis! Du kör en datadriven PHP-app i Azure App Service.

## <a name="update-model-locally-and-redeploy"></a>Uppdatera modellen lokalt och distribuera om

I det här steget gör du en enkel ändring i `task`-datamodellen och webbappen och publicerar sedan uppdateringen till Azure.

För uppgiftsscenariot ändrar du programmet så att du kan markera en uppgift som slutförd.

### <a name="add-a-column"></a>Lägga till en kolumn

Visa det lokala terminalfönstret och gå till roten för Git-lagringsplatsen.

Generera en ny databasmigrering för `tasks`-tabellen:

```bash
php artisan make:migration add_complete_column --table=tasks
```

Det här kommandot visar namnet på migreringsfilen som genereras. Leta reda på filen i _database/migrations_ och öppna den.

Ersätt metoden `up` med följande kod:

```php
public function up()
{
    Schema::table('tasks', function (Blueprint $table) {
        $table->boolean('complete')->default(False);
    });
}
```

Föregående kod lägger till en boolesk kolumn i `tasks`-tabellen med namnet `complete`.

Ersätt metoden `down` med följande kod för återställningsåtgärden:

```php
public function down()
{
    Schema::table('tasks', function (Blueprint $table) {
        $table->dropColumn('complete');
    });
}
```

Visa det lokala terminalfönstret och kör Laravel-databasemigreringar för att göra ändringen i den lokala databasen.

```bash
php artisan migrate
```

Modellen `Task` (se _app/Task.php_) mappar till `tasks`-tabellen som standard, baserat på [Laravel-namngivningskonventionen](https://laravel.com/docs/5.4/eloquent#defining-models).

### <a name="update-application-logic"></a>Uppdatera programlogik

Öppna filen *routes/web.php*. Här definieras programmets vägar och affärslogik.

I slutet av filen lägger du till en väg med följande kod:

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

Föregående kod gör en enkel uppdatering till datamodellen genom att ändra värdet för `complete`.

### <a name="update-the-view"></a>Uppdatera vyn

Öppna filen *resources/views/tasks.blade.php*. Leta reda på starttaggen `<tr>` och ersätt den med:

```html
<tr class="{{ $task->complete ? 'success' : 'active' }}" >
```

Föregående kod ändrar färg på raden beroende på om uppgiften är slutförd.

På nästa rad finns följande kod:

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

Föregående kod lägger till knappen Skicka som refererar till den väg som du angav tidigare.

### <a name="test-the-changes-locally"></a>Testa ändringarna lokalt

Visa det lokala terminalfönstret och kör utvecklingsservern från rotkatalogen på Git-lagringsplatsen.

```bash
php artisan serve
```

Om du vill se ändringen i uppgiftsstatusen går du till `http://localhost:8000` och markerar kryssrutan.

![Kryssruta lades till för uppgift](./media/app-service-web-tutorial-php-mysql/complete-checkbox.png)

Om du vill stoppa PHP skriver du `Ctrl + C` i terminalen.

### <a name="publish-changes-to-azure"></a>Publicera ändringar till Azure

Gå till det lokala terminalfönstret och kör Laravel-databasmigreringar med produktionsanslutningssträngen för att genomföra ändringen i Azure-databasen.

```bash
php artisan migrate --env=production --force
```

Genomför alla ändringar på Git och skicka sedan kodändringarna till Azure.

```bash
git add .
git commit -m "added complete checkbox"
git push azure master
```

När `git push` har slutförts kan du gå till Azure-webbappen och prova de nya funktionerna.

![Modell- och databasändringar som är publicerade i Azure](media/app-service-web-tutorial-php-mysql/complete-checkbox-published.png)

Om du har lagt till några uppgifter finns de kvar i databasen. Uppdateringar i dataschemat påverkar inte befintliga data.

## <a name="stream-diagnostic-logs"></a>Strömma diagnostikloggar

När PHP-appen körs i Azure App Service kan du skicka konsolloggarna till din terminal. På så sätt kan du få samma diagnostikmeddelanden för att felsöka programfel.

Starta loggströmningen genom att använda kommandot [`az webapp log tail`](/cli/azure/webapp/log?view=azure-cli-latest#az_webapp_log_tail) i Cloud Shell.

```azurecli-interactive
az webapp log tail --name <app_name> --resource-group myResourceGroup
```

Uppdatera Azure-webbapp i webbläsaren så hämtas webbtrafik när loggströmningen har startats. Du kan nu se konsolloggarna som skickas till terminalen. Om du inte ser konsolloggarna omedelbart kan du titta efter igen efter 30 sekunder.

Skriv när som helst `Ctrl`+`C` om du vill stoppa loggströmningen.

> [!TIP]
> Ett PHP-program kan använda standarden [error_log()](http://php.net/manual/function.error-log.php) för att skapa utdata till konsolen. Exempelprogrammet använder den här metoden i _app/Http/routes.php_.
>
> [Laravel använder Monolog](https://laravel.com/docs/5.4/errors) som loggningsprovider i webbramverket. Information om hur du får Monolog att skicka meddelanden till konsolen finns i artikeln om [PHP och hur du använder monolog för att skicka loggar till konsolen (php://out)](http://stackoverflow.com/questions/25787258/php-how-to-use-monolog-to-log-to-console-php-out).
>
>

## <a name="manage-the-azure-web-app"></a>Hantera Azure-webbappen

Gå till [Azure Portal](https://portal.azure.com) för att hantera den webbapp som du skapade.

Klicka på **App Services** i menyn till vänster och sedan på namnet på din Azure-webbapp.

![Navigera till webbappen på Azure Portal](./media/app-service-web-tutorial-php-mysql/access-portal.png)

Nu visas sidan Översikt för din webbapp. Här kan du utföra grundläggande hanteringsåtgärder som att stoppa, starta, starta om, bläddra och ta bort.

Menyn till vänster innehåller sidor för att konfigurera appen.

![App Service-sidan på Azure Portal](./media/app-service-web-tutorial-php-mysql/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * skapa en MySQL-databas i Azure
> * ansluta en PHP-app till MySQL
> * distribuera appen till Azure
> * uppdatera datamodellen och distribuera om appen
> * strömma diagnostikloggar från Azure
> * hantera appen i Azure-portalen.

Gå vidare till nästa självstudie där du får lära dig att mappa ett anpassat DNS-namn till webbappen.

> [!div class="nextstepaction"]
> [Mappa ett befintligt anpassat DNS-namn till Azure Web Apps](app-service-web-tutorial-custom-domain.md)
