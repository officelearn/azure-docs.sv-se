---
title: Konfigurera PHP-appar
description: Läs om hur du konfigurerar en förbyggd PHP-behållare för din app. Den här artikeln visar de vanligaste konfigurationsuppgifterna.
ms.devlang: php
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: ad121d605e521704597471b446fa79cb43dfccc7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255835"
---
# <a name="configure-a-linux-php-app-for-azure-app-service"></a>Konfigurera en Linux PHP-app för Azure App Service

Den här guiden visar hur du konfigurerar den inbyggda PHP-körningen för webbappar, mobila bakåtslut och API-appar i Azure App Service.

Den här guiden innehåller viktiga begrepp och instruktioner för PHP-utvecklare som använder en inbyggd Linux-behållare i App Service. Om du aldrig har använt Azure App Service följer du [PHP-snabbstarten](quickstart-php.md) och [PHP med MySQL-självstudien](tutorial-php-mysql-app.md) först.

## <a name="show-php-version"></a>Visa PHP-version

Om du vill visa den aktuella PHP-versionen kör du följande kommando i [Cloud Shell:](https://shell.azure.com)

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Om du vill visa alla PHP-versioner som stöds kör du följande kommando i [Cloud Shell:](https://shell.azure.com)

```azurecli-interactive
az webapp list-runtimes --linux | grep PHP
```

## <a name="set-php-version"></a>Ställ in PHP-version

Kör följande kommando i [Cloud Shell](https://shell.azure.com) för att ställa in PHP-versionen till 7.2:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "PHP|7.2"
```

## <a name="customize-build-automation"></a>Anpassa byggautomation

Om du distribuerar din app med Git- eller zip-paket med build automation aktiverat, steg apptjänstens automatisering genom följande sekvens:

1. Kör anpassat skript om `PRE_BUILD_SCRIPT_PATH`det anges av .
1. Kör `php composer.phar install`.
1. Kör anpassat skript om `POST_BUILD_SCRIPT_PATH`det anges av .

`PRE_BUILD_COMMAND`och `POST_BUILD_COMMAND` är miljövariabler som är tomma som standard. Om du vill köra förbyggningskommandon definierar du `PRE_BUILD_COMMAND`. Om du vill köra kommandon `POST_BUILD_COMMAND`efter build definierar du .

I följande exempel anges de två variablerna till en serie kommandon, avgränsade med kommatecken.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

Ytterligare miljövariabler för att anpassa byggautomatisering finns i [Oryx-konfiguration](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md).

Mer information om hur App Service körs och bygger PHP-appar i Linux finns i [Oryx dokumentation: Hur PHP-appar identifieras och byggs](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/php.md).

## <a name="customize-start-up"></a>Anpassa start

Som standard kör den inbyggda PHP-behållaren Apache-servern. Vid uppstart, det `apache2ctl -D FOREGROUND"`körs . Om du vill kan du köra ett annat kommando vid start genom att köra följande kommando i [Cloud Shell:](https://shell.azure.com)

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
```

## <a name="access-environment-variables"></a>Få åtkomst till miljövariabler

I App Service kan du [ställa in appinställningar](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) utanför appkoden. Då kan du komma åt dem med hjälp av standard [getenv()](https://secure.php.net/manual/function.getenv.php) mönster. Om du till exempel vill få åtkomst till en appinställning med namnet `DB_HOST` använder du följande kod:

```php
getenv("DB_HOST")
```

## <a name="change-site-root"></a>Ändra platsrot

Webbramverket som du väljer kan använda en underkatalog som platsrot. [Laravel](https://laravel.com/)använder till exempel `public/` underkatalogen som platsrot.

Standard-PHP-avbildningen för App Service använder Apache, och du kan inte anpassa webbplatsroten för din app. Du kan kringgå den här begränsningen genom att lägga till en *HTAccess-fil* i databasroten med följande innehåll:

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

## <a name="customize-phpini-settings"></a>Anpassa php.ini-inställningar

Om du behöver göra ändringar i din PHP-installation kan du ändra något av [php.ini-direktiven](https://www.php.net/manual/ini.list.php) genom att följa dessa steg.

> [!NOTE]
> Det bästa sättet att se PHP-versionen och den nuvarande *php.ini* konfigurationen är att ringa [phpinfo()](https://php.net/manual/function.phpinfo.php) i din app.
>

### <a name="customize-non-php_ini_system-directives"></a><a name="Customize-non-PHP_INI_SYSTEM directives"></a>Direktiv som inte PHP_INI_SYSTEM

Om du vill anpassa PHP_INI_USER, PHP_INI_PERDIR och PHP_INI_ALL direktiv (se [php.ini-direktiv](https://www.php.net/manual/ini.list.php)) lägger du till en *HTACCESS-fil* i appens rotkatalog.

Lägg till direktiven med syntaxen `php_value <directive-name> <value>` i *HTACCESS-filen.* Ett exempel:

```
php_value upload_max_filesize 1000M
php_value post_max_size 2000M
php_value memory_limit 3000M
php_value max_execution_time 180
php_value max_input_time 180
php_value display_errors On
php_value upload_max_filesize 10M
```

Distribuera om appen med ändringarna och starta om den. Om du distribuerar den med Kudu (till exempel med [Git)](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)startas den automatiskt om efter distributionen.

Som ett alternativ till att använda *.htaccess*kan du använda [ini_set()](https://www.php.net/manual/function.ini-set.php) i appen för att anpassa dessa icke-PHP_INI_SYSTEM-direktiv.

### <a name="customize-php_ini_system-directives"></a><a name="customize-php_ini_system-directives"></a>Anpassa PHP_INI_SYSTEM direktiv

Om du vill anpassa PHP_INI_SYSTEM direktiv (se [php.ini-direktiv](https://www.php.net/manual/ini.list.php)) kan du inte använda *.htaccess-metoden.* App Service tillhandahåller en `PHP_INI_SCAN_DIR` separat mekanism med hjälp av appinställningen.

Kör först följande kommando i [Cloud Shell](https://shell.azure.com) för `PHP_INI_SCAN_DIR`att lägga till en appinställning som heter:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PHP_INI_SCAN_DIR="/usr/local/etc/php/conf.d:/home/site/ini"
```

`/usr/local/etc/php/conf.d`är standardkatalogen där *php.ini* finns. `/home/site/ini`är den anpassade katalogen där du lägger till en anpassad *INI-fil.* Du avgränsar värdena med en `:`.

Navigera till webben SSH-session med`https://<app-name>.scm.azurewebsites.net/webssh/host`din Linux-behållare ( ).

Skapa en `/home/site` katalog `ini`i den anropade och `/home/site/ini` skapa sedan en *INI-fil* i katalogen (till exempel *settings.ini)* med de direktiv som du vill anpassa. Använd samma syntax som du skulle använda i en *php.ini-fil.* 

> [!TIP]
> I de inbyggda Linux-behållarna i App Service används */home* som beständig delad lagring. 
>

Om du till exempel vill ändra värdet [för expose_php](https://php.net/manual/ini.core.php#ini.expose-php) köra följande kommandon:

```bash
cd /home/site
mkdir ini
echo "expose_php = Off" >> ini/setting.ini
```

Starta om appen för att ändringarna ska börja gälla.

## <a name="enable-php-extensions"></a>Aktivera PHP-tillägg

De inbyggda PHP-installationerna innehåller de vanligaste tilläggen. Du kan aktivera ytterligare tillägg på samma sätt som du [anpassar php.ini-direktiv](#customize-php_ini_system-directives).

> [!NOTE]
> Det bästa sättet att se PHP-versionen och den nuvarande *php.ini* konfigurationen är att ringa [phpinfo()](https://php.net/manual/function.phpinfo.php) i din app.
>

Så här aktiverar du ytterligare tillägg genom att följa dessa steg:

Lägg `bin` till en katalog i appens `.so` rotkatalog och placera tilläggsfilerna i den (till exempel *mongodb.so*). Kontrollera att tilläggen är kompatibla med PHP-versionen i Azure och är VC9- och icke-trådsäkra (nts) kompatibla.

Distribuera dina ändringar.

Följ stegen i [Anpassa PHP_INI_SYSTEM direktiv](#customize-php_ini_system-directives)lägger du till tilläggen i den anpassade *INI-filen* med [tillägget](https://www.php.net/manual/ini.core.php#ini.extension) eller [zend_extension](https://www.php.net/manual/ini.core.php#ini.zend-extension) direktiven.

```ini
extension=/home/site/wwwroot/bin/mongodb.so
zend_extension=/home/site/wwwroot/bin/xdebug.so
```

Starta om appen för att ändringarna ska börja gälla.

## <a name="access-diagnostic-logs"></a>Få åtkomst till diagnostikloggar

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Öppna SSH-session i webbläsaren

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>Felsökning

När en fungerande PHP-app fungerar annorlunda i App Service eller har fel kan du prova följande:

- [Öppna loggströmmen](#access-diagnostic-logs).
- Testa appen lokalt i produktionsläge. App Service kör nod.js-apparna i produktionsläge, så du måste se till att projektet fungerar som förväntat i produktionsläge lokalt. Ett exempel:
    - Beroende på *din composer.json*kan olika paket installeras`require` för `require-dev`produktionsläge ( vs. ).
    - Vissa webbramverk kan distribuera statiska filer på olika sätt i produktionsläge.
    - Vissa webbramverk kan använda anpassade startskript när de körs i produktionsläge.
- Kör appen i apptjänsten i felsökningsläge. I [Laravel](https://meanjs.org/)kan du till exempel konfigurera appen så att den skickar ut felsökningsmeddelanden i produktion genom [att ange appinställningen `APP_DEBUG` till `true` ](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings).

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Handledning: PHP app med MySQL](tutorial-php-mysql-app.md)

> [!div class="nextstepaction"]
> [Vanliga frågor och svar om App Service Linux](app-service-linux-faq.md)
