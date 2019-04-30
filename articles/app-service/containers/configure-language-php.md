---
title: Konfigurera PHP - appar i Azure App Service | Microsoft Docs
description: Lär dig hur du konfigurerar PHP-appar att fungera i Azure App Service
services: app-service
documentationcenter: ''
author: cephalin
manager: jpconnock
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/28/2019
ms.author: cephalin
ms.openlocfilehash: 11d0648ee5090f02cb96c2d42a8d90cc3ea0ed28
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60853311"
---
# <a name="configure-a-linux-php-app-for-azure-app-service"></a>Konfigurera en Linux-PHP-app för Azure App Service

Den här guiden visar hur du konfigurerar den inbyggda PHP-körningen för web apps, mobila serverdelar och API apps i Azure App Service.

Den här guiden innehåller huvudbegrepp och instruktioner för PHP-utvecklare som använder en inbyggd Linux-behållare i App Service. Om du aldrig har använt Azure App Service, följer du de [PHP-quickstart](quickstart-php.md) och [PHP med MySQL självstudien](tutorial-php-mysql-app.md) första.

## <a name="show-php-version"></a>Visa PHP-version

För att visa den aktuella versionen av PHP, kör du följande kommando den [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

För att visa alla PHP-versioner, kör du följande kommando den [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep PHP
```

## <a name="set-php-version"></a>Ange PHP-version

Kör följande kommando den [Cloud Shell](https://shell.azure.com) att ställa in den PHP-versionen på 7.2:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "PHP|7.2"
```

## <a name="run-composer"></a>Kör Composer

Kudu inte körs som standard [Composer](https://getcomposer.org/). Om du vill aktivera Composer-automatisering under distributionen av Kudu, måste du ange en [anpassat distributionsskript](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).

Ändra katalog till Lagringsplatsens rot från ett lokalt terminalfönster. Följ den [kommandoradsinstallation steg](https://getcomposer.org/download/) att ladda ned *composer.phar*.

Kör följande kommandon:

```bash
npm install kuduscript -g
kuduscript --php --scriptType bash --suppressPrompt
```

Lagringsplatsens rot har nu två nya filer förutom *composer.phar*: *.deployment* och *deploy.sh*. Dessa filer fungerar både för Windows och Linux-varianter av App Service.

Öppna *deploy.sh* och hitta den `Deployment` avsnittet. Ersätt hela avsnittet med följande kod:

```bash
##################################################################################################################################
# Deployment
# ----------

echo PHP deployment

# 1. KuduSync
if [[ "$IN_PLACE_DEPLOYMENT" -ne "1" ]]; then
  "$KUDU_SYNC_CMD" -v 50 -f "$DEPLOYMENT_SOURCE" -t "$DEPLOYMENT_TARGET" -n "$NEXT_MANIFEST_PATH" -p "$PREVIOUS_MANIFEST_PATH" -i ".git;.hg;.deployment;deploy.sh"
  exitWithMessageOnError "Kudu Sync failed"
fi

# 3. Initialize Composer Config
initializeDeploymentConfig

# 4. Use composer
echo "$DEPLOYMENT_TARGET"
if [ -e "$DEPLOYMENT_TARGET/composer.json" ]; then
  echo "Found composer.json"
  pushd "$DEPLOYMENT_TARGET"
  php composer.phar install $COMPOSER_ARGS
  exitWithMessageOnError "Composer install failed"
  popd
fi
##################################################################################################################################
```

Spara dina ändringar och distribuera din kod igen. Composer bör nu köras som en del av distributionsautomatisering.

## <a name="customize-start-up"></a>Anpassa start

Standardvärdet är den inbyggda PHP-behållaren som kör Apache-server. Vid start, kör `apache2ctl -D FOREGROUND"`. Om du vill kan du kan köra ett annat kommando vid start, genom att köra följande kommando i den [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
```

## <a name="access-environment-variables"></a>Få åtkomst till miljövariabler

I App Service kan du [konfigurera appinställningar](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#app-settings) utanför din Appkod. Du kan använda dem med hjälp av standard [getenv()](https://secure.php.net/manual/function.getenv.php) mönster. Om du till exempel vill få åtkomst till en appinställning med namnet `DB_HOST` använder du följande kod:

```php
getenv("DB_HOST")
```

## <a name="change-site-root"></a>Ändra platsens rot

Webbramverk önskar använda en underkatalog som platsens rot. Till exempel [Laravel](https://laravel.com/), använder de `public/` underkatalog som platsens rot.

PHP-standardavbildningen för App Service använder Apache, och kan inte anpassa webbplatsrot för din app. Du kan undvika den här begränsningen genom att lägga till en *.htaccess* filen till Lagringsplatsens rot med följande innehåll:

```
<IfModule mod_rewrite.c>
    RewriteEngine on

    RewriteRule ^.*$ /public/$1 [NC,L,QSA]
</IfModule>
```

Om du inte vill använda *.htaccess*-omskrivning kan du distribuera din Laravel-app med en [anpassad Docker-avbildning](quickstart-docker-go.md) i stället.

## <a name="detect-https-session"></a>Identifiera HTTPS-sessionen

I App Service sker [SSL-avslutning](https://wikipedia.org/wiki/TLS_termination_proxy) på lastbalanserare för nätverk, så alla HTTPS-begäranden når din app som okrypterade HTTP-begäranden. Om din applogik behöver kontrollera om användarbegäranden är krypterade eller inte kan du kontrollera `X-Forwarded-Proto`-rubriken.

```php
if (isset($_SERVER['X-Forwarded-Proto']) && $_SERVER['X-Forwarded-Proto'] === 'https') {
  // Do something when HTTPS is used
}
```

Med populära ramverk får du åtkomst till `X-Forwarded-*` information i standardappens mönster. I [CodeIgniter](https://codeigniter.com/) kontrollerar [is_https()](https://github.com/bcit-ci/CodeIgniter/blob/master/system/core/Common.php#L338-L365) värdet för `X_FORWARDED_PROTO` som standard.

## <a name="customize-phpini-settings"></a>Anpassa inställningar för php.ini

Om du behöver göra ändringar i din PHP-installation kan du ändra någon av de [php.ini-direktiv](http://www.php.net/manual/ini.list.php) genom att följa dessa steg.

> [!NOTE]
> Det bästa sättet att se den PHP-versionen och aktuellt *php.ini* konfiguration är att anropa [phpinfo()](https://php.net/manual/function.phpinfo.php) i din app.
>

### <a name="customize-non-phpinisystem-directives"></a>Anpassa icke PHP_INI_SYSTEM direktiv

Anpassa PHP_INI_USER och PHP_INI_PERDIR PHP_INI_ALL direktiv (se [php.ini-direktiv](http://www.php.net/manual/ini.list.php)), Lägg till en *.htaccess* filen till rotkatalogen för din app.

I den *.htaccess* Lägg till direktiv med hjälp av den `php_value <directive-name> <value>` syntax. Exempel:

```
php_value upload_max_filesize 1000M
php_value post_max_size 2000M
php_value memory_limit 3000M
php_value max_execution_time 180
php_value max_input_time 180
php_value display_errors On
php_value upload_max_filesize 10M
```

Distribuera om din app med ändringar och starta om den. Om du distribuerar den med Kudu (till exempel [Git](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)), den startas automatiskt efter distributionen.

Som ett alternativ till *.htaccess*, du kan använda [ini_set()](http://www.php.net/manual/function.ini-set.php) i din app för att anpassa dessa icke PHP_INI_SYSTEM-direktiv.

### <a name="customize-phpinisystem-directives"></a>Anpassa PHP_INI_SYSTEM direktiv

Anpassa PHP_INI_SYSTEM direktiv (se [php.ini-direktiv](http://www.php.net/manual/ini.list.php)), du kan inte använda den *.htaccess* metod. App Service tillhandahåller en mekanism för separat med hjälp av den `PHP_INI_SCAN_DIR` appinställningen.

Först kör du följande kommando i den [Cloud Shell](https://shell.azure.com) lägga till en app som heter `PHP_INI_SCAN_DIR`:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PHP_INI_SCAN_DIR="/usr/local/etc/php/conf.d:/home/site/ini"
```

`/usr/local/etc/php/conf.d` är standardkatalogen där *php.ini* finns. `/home/site/ini` är den anpassa katalogen där du lägger till en anpassad *ini* fil. Du avgränsa värden med en `:`.

Gå till webbsessionen SSH med Linux-behållare (`https://cephalin-container.scm.azurewebsites.net/webssh/host`).

Skapa en katalog i `/home/site` kallas `ini`, skapas en *ini* fil i den `/home/site/ini` katalog (till exempel *settings.ini)* med direktiv som du vill anpassa. Använd samma syntax som du vill använda i en *php.ini* fil. 

> [!TIP]
> I de inbyggda Linux-behållarna i App Service */home* används som beständiga delad lagring. 
>

Till exempel vill ändra värdet för [expose_php](http://php.net/manual/ini.core.php#ini.expose-php) kör följande kommandon:

```bash
cd /home/site
mkdir ini
echo "expose_php = Off" >> ini/setting.ini
```

Starta om appen för att ändringarna ska börja gälla.

## <a name="enable-php-extensions"></a>Aktivera PHP-tillägg

De inbyggda PHP-installationerna innehåller de vanligaste tilläggen. Du kan aktivera ytterligare tillägg på samma sätt som du [anpassa php.ini-direktiv](#customize-php_ini_system-directives).

> [!NOTE]
> Det bästa sättet att se den PHP-versionen och aktuellt *php.ini* konfiguration är att anropa [phpinfo()](https://php.net/manual/function.phpinfo.php) i din app.
>

För att aktivera ytterligare tillägg genom att följa dessa steg:

Lägg till en `bin` katalogen till rotkatalogen för din app och placera den `.so` tilläggsfiler i den (till exempel *mongodb.so*). Se till att tillägg som är kompatibla med PHP-versionen i Azure och är VC9 och icke-trådsäkra (nts)-kompatibel.

Distribuera dina ändringar.

Följ stegen i [anpassa PHP_INI_SYSTEM direktiv](#customize-php_ini_system-directives), lägga till tillägg i anpassat *ini* filen med den [tillägget](https://www.php.net/manual/ini.core.php#ini.extension) eller [zend_extension ](https://www.php.net/manual/ini.core.php#ini.zend-extension) direktiv.

```ini
extension=/home/site/wwwroot/bin/mongodb.so
zend_extension=/home/site/wwwroot/bin/xdebug.so
```

Starta om appen för att ändringarna ska börja gälla.

## <a name="access-diagnostic-logs"></a>Få åtkomst till diagnostikloggar

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Öppna SSH-session i webbläsare

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>Felsökning

När en fungerande PHP-app fungerar annorlunda i App Service eller innehåller fel, provar du följande:

- [Få åtkomst till loggström](#access-diagnostic-logs).
- Testa appen lokalt i Produktionsläge. App Service kör Node.js-appar i Produktionsläge, så du måste se till att projektet fungerar som förväntat i Produktionsläge lokalt. Exempel:
    - Beroende på din *composer.json*, olika paket kan installeras för Produktionsläge (`require` jämfört med `require-dev`).
    - Vissa webbramverk kan distribuera statiska filer på olika sätt i Produktionsläge.
    - Vissa webbramverk kan använda anpassade startskript vid körning i Produktionsläge.
- Kör din app i App Service i felsökningsläge. Till exempel i [Laravel](http://meanjs.org/), du kan konfigurera din app för att mata ut felsökningsmeddelanden i produktion av [inställningen den `APP_DEBUG` appinställningen `true` ](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).

### <a name="robots933456"></a>robots933456

Du kan se följande meddelande i behållarloggarna:

```
2019-04-08T14:07:56.641002476Z "-" - - [08/Apr/2019:14:07:56 +0000] "GET /robots933456.txt HTTP/1.1" 404 415 "-" "-"
```

Du kan ignorera det här meddelandet. `/robots933456.txt` är en dummy URL-sökväg som App Service använder för att kontrollera om behållaren är kan betjäna begäranden. 404-svar anger bara att sökvägen finns inte, men det kan användas av App Service vet att behållaren är felfri och redo att svara på begäranden.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie: PHP-app med MySQL](tutorial-php-mysql-app.md)

> [!div class="nextstepaction"]
> [App Service Linux vanliga frågor och svar](app-service-linux-faq.md)