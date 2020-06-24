---
title: Konfigurera PHP-appar
description: Lär dig hur du konfigurerar en fördefinierad PHP-behållare för din app. Den här artikeln visar de vanligaste konfigurations åtgärderna.
ms.devlang: php
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: 9e4237f1eecb9f6542aac946525ff4583e478c2e
ms.sourcegitcommit: 34eb5e4d303800d3b31b00b361523ccd9eeff0ab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/17/2020
ms.locfileid: "84905705"
---
# <a name="configure-a-linux-php-app-for-azure-app-service"></a>Konfigurera en Linux PHP-app för Azure App Service

Den här guiden visar hur du konfigurerar den inbyggda PHP-körningen för webbappar, mobila Server delar och API-appar i Azure App Service.

Den här guiden innehåller viktiga begrepp och instruktioner för PHP-utvecklare som använder en inbyggd Linux-behållare i App Service. Om du aldrig har använt Azure App Service kan du följa självstudien för [php snabb start](quickstart-php.md) och [php med MySQL](tutorial-php-mysql-app.md) först.

## <a name="show-php-version"></a>Visa PHP-version

Om du vill visa den aktuella PHP-versionen kör du följande kommando i [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Om du vill visa alla PHP-versioner som stöds kör du följande kommando i [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep PHP
```

## <a name="set-php-version"></a>Ange PHP-version

Kör följande kommando i [Cloud Shell](https://shell.azure.com) för att ställa in PHP-versionen på 7,2:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "PHP|7.2"
```

## <a name="customize-build-automation"></a>Anpassa Bygg automatisering

Om du distribuerar din app med hjälp av git-eller zip-paket med build-automatisering aktiverat, App Service bygga automatiserings steg i följande ordning:

1. Kör anpassat skript om det anges av `PRE_BUILD_SCRIPT_PATH` .
1. Kör `php composer.phar install`.
1. Kör anpassat skript om det anges av `POST_BUILD_SCRIPT_PATH` .

`PRE_BUILD_COMMAND`och `POST_BUILD_COMMAND` är miljövariabler som är tomma som standard. Definiera för att köra kommandon för att skapa för bygge `PRE_BUILD_COMMAND` . Definiera för att köra kommandon efter kompilering `POST_BUILD_COMMAND` .

I följande exempel anges de två variablerna för en serie kommandon, avgränsade med kommatecken.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

Ytterligare miljövariabler för att anpassa Bygg automatisering finns i [Oryx-konfiguration](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md).

Mer information om hur App Service kör och skapar PHP-appar i Linux finns i [Oryx-dokumentation: så här identifieras och skapas php-appar](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/php.md).

## <a name="customize-start-up"></a>Anpassa start

Som standard kör den inbyggda PHP-behållaren apache-servern. Den körs vid start `apache2ctl -D FOREGROUND"` . Om du vill kan du köra ett annat kommando vid start genom att köra följande kommando i [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
```

## <a name="access-environment-variables"></a>Få åtkomst till miljövariabler

I App Service kan du [Ange inställningar för appar](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) utanför appens kod. Sedan kan du komma åt dem med hjälp av standard mönstret för [getenv ()](https://secure.php.net/manual/function.getenv.php) . Om du till exempel vill få åtkomst till en appinställning med namnet `DB_HOST` använder du följande kod:

```php
getenv("DB_HOST")
```

## <a name="change-site-root"></a>Ändra plats rot

Det webb ramverk som du väljer kan använda en under katalog som plats rot. Till exempel, [Laravel](https://laravel.com/), använder under `public/` katalogen som plats rot.

Standard PHP-avbildningen för App Service använder Apache, och det låter dig inte anpassa plats roten för din app. Undvik den här begränsningen genom att lägga till en *. htaccess* -fil till lagrings platsens rot med följande innehåll:

```
<IfModule mod_rewrite.c>
    RewriteEngine on
    RewriteCond %{REQUEST_URI} ^/$
    RewriteRule ^(.*)$ /public/$1 [NC,L,QSA]
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

## <a name="customize-phpini-settings"></a>Anpassa php.ini inställningar

Om du behöver göra ändringar i PHP-installationen kan du ändra något av [php.ini direktiven](https://www.php.net/manual/ini.list.php) genom att följa dessa steg.

> [!NOTE]
> Det bästa sättet att se PHP-versionen och den aktuella *php.ini* konfigurationen är att anropa [phpinfo ()](https://php.net/manual/function.phpinfo.php) i din app.
>

### <a name="customize-non-php_ini_system-directives"></a><a name="Customize-non-PHP_INI_SYSTEM directives"></a>Anpassa – icke-PHP_INI_SYSTEM direktiv

Om du vill anpassa PHP_INI_USER, PHP_INI_PERDIR och PHP_INI_ALL direktiv (se [php.ini direktiv](https://www.php.net/manual/ini.list.php)) lägger du till en *. htaccess* -fil i rot katalogen för din app.

I *htaccess* -filen lägger du till direktiven med hjälp av `php_value <directive-name> <value>` syntaxen. Ett exempel:

```
php_value upload_max_filesize 1000M
php_value post_max_size 2000M
php_value memory_limit 3000M
php_value max_execution_time 180
php_value max_input_time 180
php_value display_errors On
php_value upload_max_filesize 10M
```

Distribuera om din app med ändringarna och starta om den. Om du distribuerar den med kudu (till exempel med hjälp av [git](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)) startas den om automatiskt efter distributionen.

Som ett alternativ till att använda *. htaccess*kan du använda [ini_set ()](https://www.php.net/manual/function.ini-set.php) i appen för att anpassa de här icke-PHP_INI_SYSTEM direktiven.

### <a name="customize-php_ini_system-directives"></a><a name="customize-php_ini_system-directives"></a>Anpassa PHP_INI_SYSTEM direktiv

Om du vill anpassa PHP_INI_SYSTEM direktiv (se [php.ini direktiv](https://www.php.net/manual/ini.list.php)) kan du inte använda metoden *. htaccess* . App Service ger en separat mekanism som använder `PHP_INI_SCAN_DIR` appens inställning.

Kör först följande kommando i [Cloud Shell](https://shell.azure.com) för att lägga till en app-inställning som heter `PHP_INI_SCAN_DIR` :

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PHP_INI_SCAN_DIR="/usr/local/etc/php/conf.d:/home/site/ini"
```

`/usr/local/etc/php/conf.d`är standard katalogen där *php.ini* finns. `/home/site/ini`är den anpassade katalogen där du lägger till en anpassad *ini* -fil. Du skiljer värdena med en `:` .

Navigera till webbssh-sessionen med Linux-containern ( `https://<app-name>.scm.azurewebsites.net/webssh/host` ).

Skapa en katalog i `/home/site` anropad `ini` och skapa sedan en *ini* -fil i `/home/site/ini` katalogen (till exempel *settings.ini)* med de direktiv som du vill anpassa. Använd samma syntax som du skulle använda i en *php.ini* -fil. 

> [!TIP]
> I de inbyggda Linux-behållarna i App Service används */Home* som Sparad delad lagring. 
>

Om du till exempel vill ändra värdet för [expose_php](https://php.net/manual/ini.core.php#ini.expose-php) kör du följande kommandon:

```bash
cd /home/site
mkdir ini
echo "expose_php = Off" >> ini/setting.ini
```

Starta om appen för att ändringarna ska börja gälla.

## <a name="enable-php-extensions"></a>Aktivera PHP-tillägg

De inbyggda PHP-installationerna innehåller de oftast använda tilläggen. Du kan aktivera ytterligare tillägg på samma sätt som du [anpassar php.ini direktiv](#customize-php_ini_system-directives).

> [!NOTE]
> Det bästa sättet att se PHP-versionen och den aktuella *php.ini* konfigurationen är att anropa [phpinfo ()](https://php.net/manual/function.phpinfo.php) i din app.
>

Följ dessa steg om du vill aktivera ytterligare tillägg:

Lägg till en `bin` katalog i rot katalogen för din app och Lägg till `.so` tilläggsfiler i den (till exempel *MongoDB.so*). Kontrol lera att tilläggen är kompatibla med PHP-versionen i Azure och att de är VC9-kompatibla och icke-tråd säkra (nter)-kompatibla.

Distribuera dina ändringar.

Följ stegen i [anpassa PHP_INI_SYSTEM direktiv](#customize-php_ini_system-directives), Lägg till tilläggen i den anpassade *ini* -filen med [fil namns tillägget](https://www.php.net/manual/ini.core.php#ini.extension) eller [zend_extension](https://www.php.net/manual/ini.core.php#ini.zend-extension) -direktiven.

```ini
extension=/home/site/wwwroot/bin/mongodb.so
zend_extension=/home/site/wwwroot/bin/xdebug.so
```

Starta om appen för att ändringarna ska börja gälla.

## <a name="access-diagnostic-logs"></a>Få åtkomst till diagnostikloggar

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-linux-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Öppna SSH-session i webbläsare

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>Felsökning

Prova följande när en fungerande PHP-app fungerar annorlunda i App Service eller innehåller fel:

- [Åtkomst till logg strömmen](#access-diagnostic-logs).
- Testa appen lokalt i produktions läge. App Service kör din app i produktions läge, så du måste se till att projektet fungerar som förväntat i produktions läge lokalt. Ett exempel:
    - Beroende på din *composer.js*kan olika paket installeras i produktions läge ( `require` vs. `require-dev` ).
    - Vissa webb ramverk kan distribuera statiska filer på ett annat sätt i produktions läge.
    - Vissa webb ramverk kan använda anpassade Start skript när de körs i produktions läge.
- Kör appen i App Service i fel söknings läge. I [Laravel](https://meanjs.org/)kan du till exempel konfigurera appen så att den utvärderar fel söknings meddelanden i produktion genom [att ställa in `APP_DEBUG` appens inställning på `true` ](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings).

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie: PHP-app med MySQL](tutorial-php-mysql-app.md)

> [!div class="nextstepaction"]
> [Vanliga frågor och svar om App Service Linux](app-service-linux-faq.md)
