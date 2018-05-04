---
title: Så här konfigurerar du PHP i Azure App Service Web Apps
description: Lär dig hur du konfigurerar PHP-standardinstallation eller lägga till en anpassad PHP-installation för Web Apps i Azure App Service.
services: app-service
documentationcenter: php
author: msangapu
manager: cfowler
ms.assetid: 95c4072b-8570-496b-9c48-ee21a223fb60
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 04/11/2018
ms.author: msangapu
ms.openlocfilehash: d7b832659272f01b6e939150fa89626620d5eab7
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="configure-php-in-azure-app-service-web-apps"></a>Så här konfigurerar du PHP i Azure App Service Web Apps

## <a name="introduction"></a>Introduktion

Den här guiden visar hur du konfigurerar för inbyggda PHP-körning för Web Apps i [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714)anger en anpassad PHP-körning och aktivera tillägg. Om du vill använda Apptjänst, registrera dig för den [kostnadsfri utvärderingsversion]. För att få ut mesta möjliga av den här guiden bör du först skapa en PHP-webbapp i App Service.

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="how-to-change-the-built-in-php-version"></a>Så här: ändra inbyggda PHP-version

Som standard är PHP 5.6 installerat och omedelbart tillgängliga för användning när du skapar en webbapp i Apptjänst. Det bästa sättet att se tillgängliga versionen revision, standardkonfigurationen och aktiverade tillägg är att distribuera ett skript som anropar den [phpinfo()] funktion.

PHP 7.0 och PHP 7.2 versioner är också tillgängliga, men inte aktiverad som standard. Uppdatera PHP-version, så en av följande metoder:

### <a name="azure-portal"></a>Azure Portal

1. Bläddra till ditt webbprogram i den [Azure-portalen](https://portal.azure.com) och klicka på den **inställningar** knappen.

    ![Webbprograminställningarna][settings-button]
1. Från den **inställningar** bladet väljer **programinställningar** och välj den nya versionen av PHP.

    ![Programinställningar][application-settings]
1. Klicka på den **spara** längst upp i den **Web app-inställningar** bladet.

    ![Spara inställningar][save-button]

### <a name="azure-powershell-windows"></a>Azure PowerShell (Windows)

1. Öppna Azure PowerShell, och logga in på ditt konto:

        PS C:\> Connect-AzureRmAccount
1. Ange PHP-version för webbprogrammet.

        PS C:\> Set-AzureWebsite -PhpVersion {5.6 | 7.0 | 7.2} -Name {app-name}
1. PHP-version anges nu. Du kan bekräfta inställningarna:

        PS C:\> Get-AzureWebsite -Name {app-name} | findstr PhpVersion

### <a name="azure-cli-20-linux-mac-windows"></a>Azure CLI 2.0 (Linux, Mac, Windows)

Om du vill använda Azure-kommandoradsgränssnittet måste du [installera Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) på datorn.

1. Öppna Terminal och logga in på ditt konto.

        az login

1. Kontrollera listan över stöds körningar.

        az webapp list-runtimes | grep php

1. Ange PHP-version för webbprogrammet.

        az webapp config set --php-version {5.6 | 7.0 | 7.1 | 7.2} --name {app-name} --resource-group {resource-group-name}

1. PHP-version anges nu. Du kan bekräfta inställningarna:

        az webapp show --name {app-name} --resource-group {resource-group-name}

## <a name="how-to-change-the-built-in-php-configurations"></a>Så här: ändra inbyggda PHP-konfigurationer

Du kan ändra någon av konfigurationsalternativ genom att följa de här stegen för alla inbyggda PHP-körning. (Mer information om php.ini-direktiven finns [förteckning över php.ini-direktiv].)

### <a name="changing-phpiniuser-phpiniperdir-phpiniall-configuration-settings"></a>Ändra PHP\_INI\_användare, PHP\_INI\_PERDIR, PHP\_INI\_alla konfigurationsinställningar

1. Lägg till en [. user.ini] filen till din rotkatalog.
1. Lägg till konfigurationsinställningar till de `.user.ini` fil med samma syntax som du vill använda i en `php.ini` fil. Till exempel om du vill aktivera den `display_errors` inställning och ange `upload_max_filesize` till 10M din `.user.ini` filen innehåller den här texten:

        ; Example Settings
        display_errors=On
        upload_max_filesize=10M

        ; OPTIONAL: Turn this on to write errors to d:\home\LogFiles\php_errors.log
        ; log_errors=On
1. Distribuera ditt webbprogram.
1. Starta om webbprogrammet. (Omstart krävs eftersom frekvensen med vilken PHP läser `.user.ini` filer styrs av den `user_ini.cache_ttl` som är en inställning av system och 300 sekunder (fem minuter) som standard. Starta om webbprogrammet tvingar PHP för att läsa de nya inställningarna i den `.user.ini` filen.)

Som ett alternativ till att använda en `.user.ini` filen som du kan använda den [ini_set()] funktion i skript för att ange konfigurationsalternativ som inte är på systemnivå direktiven.

### <a name="changing-phpinisystem-configuration-settings"></a>Ändra PHP\_INI\_SYSTEM konfigurationsinställningar

1. Lägg till en Appinställning i ditt webbprogram med nyckeln `PHP_INI_SCAN_DIR` och värde `d:\home\site\ini`
1. Skapa en `settings.ini` filen med Kudu-konsolen (http://&lt;site-name&gt;. scm.azurewebsite.net) i den `d:\home\site\ini` directory.
1. Lägg till konfigurationsinställningar till de `settings.ini` fil med samma syntax som du vill använda i en php.ini-fil. Om du vill peka till exempel den `curl.cainfo` till en `*.crt` fil och anger ”wincache.maxfilesize-inställningen till 512 kB din `settings.ini` filen innehåller den här texten:

        ; Example Settings
        curl.cainfo="%ProgramFiles(x86)%\Git\bin\curl-ca-bundle.crt"
        wincache.maxfilesize=512
1. Starta om ditt webbprogram för att läsa in ändringarna.

## <a name="how-to-enable-extensions-in-the-default-php-runtime"></a>Så här: aktivera tillägg i standard PHP-körning

Enligt beskrivningen i föregående avsnitt, det bästa sättet att se PHP standardversionen, standardkonfigurationen och aktiverade tillägg är att distribuera ett skript som anropar [phpinfo()]. Att aktivera ytterligare tillägg genom att följa dessa steg:

### <a name="configure-via-ini-settings"></a>Konfigurera via ini-inställningar

1. Lägg till en `ext` katalogen på `d:\home\site` directory.
1. Placera `.dll` tillägget filer i den `ext` katalog (till exempel `php_xdebug.dll`). Se till att tilläggen är kompatibel med standardversionen av PHP och är VC9 och icke-trådsäkra (nts)-kompatibel.
1. Lägg till en Appinställning i ditt webbprogram med nyckeln `PHP_INI_SCAN_DIR` och värde `d:\home\site\ini`
1. Skapa en `ini` filen i `d:\home\site\ini` kallas `extensions.ini`.
1. Lägg till konfigurationsinställningar till de `extensions.ini` fil med samma syntax som du vill använda i en php.ini-fil. Om du vill aktivera MongoDB och XDebug tillägg, till exempel din `extensions.ini` filen innehåller den här texten:

        ; Enable Extensions
        extension=d:\home\site\ext\php_mongo.dll
        zend_extension=d:\home\site\ext\php_xdebug.dll
1. Starta om ditt webbprogram för att läsa in ändringarna.

### <a name="configure-via-app-setting"></a>Konfigurera via Appinställningen

1. Lägg till en `bin` katalogen till rotkatalogen.
1. Placera `.dll` tillägget filer i den `bin` katalog (till exempel `php_xdebug.dll`). Se till att tilläggen är kompatibel med standardversionen av PHP och är VC9 och icke-trådsäkra (nts)-kompatibel.
1. Distribuera ditt webbprogram.
1. Bläddra till webbappen i Azure-portalen och klicka på den **inställningar** knappen.

    ![Webbprograminställningarna][settings-button]
1. Från den **inställningar** bladet väljer **programinställningar** och bläddra till den **appinställningar** avsnitt.
1. I den **appinställningar** avsnittet, skapa en **PHP_EXTENSIONS** nyckel. Värdet för den här nyckeln skulle vara en sökväg i förhållande till webbplatsroten: **bin\your ext fil**.

    ![Aktivera tillägget på app-inställningar][php-extensions]
1. Klicka på den **spara** längst upp i den **Web app-inställningar** bladet.

    ![Spara inställningar][save-button]

Zend tillägg stöds också med hjälp av en **PHP_ZENDEXTENSIONS** nyckel. Om du vill aktivera flera tillägg innehåller en kommaavgränsad lista över `.dll` filer för app inställningens värde.

## <a name="how-to-use-a-custom-php-runtime"></a>Så här: använda en anpassad PHP-körning

I stället för standard PHP-körning kan App Service Web Apps använda en PHP-körning som du anger för att köra PHP-skript. Körningsmiljön som du anger kan konfigureras med en `php.ini` -fil som du också ange. Att använda en anpassad PHP-körning med Webbappar, följa de här stegen.

1. Hämta en icke-trådsäkra, VC9 eller VC11 kompatibel version av PHP för Windows. Nya versioner av PHP för Windows finns här: [ http://windows.php.net/download/ ]. Äldre versioner finns i det här arkivet: [ http://windows.php.net/downloads/releases/archives/ ].
1. Ändra den `php.ini` filen för din runtime. Alla inställningar som är endast system-nivå direktiv ignoreras av Web Apps. (Mer information om endast system-nivå direktiven finns [förteckning över php.ini-direktiv]).
1. Du kan också lägga till tillägg för PHP-körning och aktivera dem i den `php.ini` filen.
1. Lägg till en `bin` katalogen till rotkatalogen och placera den katalog där PHP-körning i den (till exempel `bin\php`).
1. Distribuera ditt webbprogram.
1. Bläddra till webbappen i Azure-portalen och klicka på den **inställningar** knappen.

    ![Webbprograminställningarna][settings-button]
1. Från den **inställningar** bladet väljer **programinställningar** och bläddra till den **Hanterarmappningar** avsnitt. Lägg till `*.php` till tillägget och Lägg till sökvägen till den `php-cgi.exe` körbara. Om du placerar PHP-körning i den `bin` katalogen i roten för ditt program sökvägen är `D:\home\site\wwwroot\bin\php\php-cgi.exe`.

    ![Ange hanterare i Hanterarmappningar][handler-mappings]
1. Klicka på den **spara** längst upp i den **Web app-inställningar** bladet.

    ![Spara inställningar][save-button]

<a name="composer" />

## <a name="how-to-enable-composer-automation-in-azure"></a>Så här: aktivera Composer automatisering i Azure

Som standard göra Apptjänst inte något med composer.json, om du har en i PHP-projektet. Om du använder [Git-distribution](app-service-deploy-local-git.md), kan du aktivera composer.json bearbetning under `git push` genom att aktivera tillägget Composer.

> [!NOTE]
> Du kan [rösta på förstklassigt Composer stöd i App Service här](https://feedback.azure.com/forums/169385-web-apps-formerly-websites/suggestions/6477437-first-class-support-for-composer-and-pip)!
>

1. I din PHP web appens blad i den [Azure-portalen](https://portal.azure.com), klickar du på **verktyg** > **tillägg**.

    ![Azure portal inställningsbladet att aktivera Composer automatisering i Azure](./media/web-sites-php-configure/composer-extension-settings.png)
2. Klicka på **Lägg till**, klicka på **Composer**.

    ![Lägga till tillägget Composer om du vill aktivera Composer automatisering i Azure](./media/web-sites-php-configure/composer-extension-add.png)
3. Klicka på **OK** att acceptera villkoren. Klicka på **OK** igen för att lägga till filnamnstillägget.

    Den **installerat tillägg** bladet visar tillägget Composer.
    ![Acceptera villkoren för att aktivera Composer automatisering i Azure](./media/web-sites-php-configure/composer-extension-view.png)
4. Nu i ett terminalfönster på den lokala datorn, utför `git add`, `git commit`, och `git push` till ditt webbprogram. Observera att Composer installeras beroenden som definierats i composer.json.

    ![Git-distribution med Composer automatisering i Azure](./media/web-sites-php-configure/composer-extension-success.png)

## <a name="next-steps"></a>Nästa steg

Mer information finns i [PHP Developer Center](/develop/php/).

> [!NOTE]
> Om du vill komma igång med Azure App Service innan du registrerar dig för ett Azure-konto kan du gå till [Prova App Service](https://azure.microsoft.com/try/app-service/). Där kan du direkt skapa en tillfällig startwebbapp i App Service. Inga kreditkort krävs. Inga åtaganden.
>

[kostnadsfri utvärderingsversion]: https://www.windowsazure.com/pricing/free-trial/
[phpinfo()]: http://php.net/manual/en/function.phpinfo.php
[select-php-version]: ./media/web-sites-php-configure/select-php-version.png
[förteckning över php.ini-direktiv]: http://www.php.net/manual/en/ini.list.php
[. user.ini]: http://www.php.net/manual/en/configuration.file.per-user.php
[ini_set()]: http://www.php.net/manual/en/function.ini-set.php
[application-settings]: ./media/web-sites-php-configure/application-settings.png
[settings-button]: ./media/web-sites-php-configure/settings-button.png
[save-button]: ./media/web-sites-php-configure/save-button.png
[php-extensions]: ./media/web-sites-php-configure/php-extensions.png
[handler-mappings]: ./media/web-sites-php-configure/handler-mappings.png
[http://windows.php.net/download/]: http://windows.php.net/download/
[http://windows.php.net/downloads/releases/archives/]: http://windows.php.net/downloads/releases/archives/
[SETPHPVERCLI]: ./media/web-sites-php-configure/ChangePHPVersion-XPlatCLI.png
[GETPHPVERCLI]: ./media/web-sites-php-configure/ShowPHPVersion-XplatCLI.png
[SETPHPVERPS]: ./media/web-sites-php-configure/ChangePHPVersion-PS.png
[GETPHPVERPS]: ./media/web-sites-php-configure/ShowPHPVersion-PS.png
