---
title: Konfigurera PHP - körning i Azure App Service
description: Lär dig hur du konfigurerar standard PHP-installation eller Lägg till en anpassad PHP-installation för Web Apps i Azure App Service.
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
ms.custom: seodec18
ms.openlocfilehash: d5ad7b392029ae33ee7666b80edfe5b4b7555b41
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53273210"
---
# <a name="configure-php-in-azure-app-service-web-apps"></a>Konfigurera PHP i Azure App Service Web Apps

## <a name="introduction"></a>Introduktion

Den här guiden visar hur du konfigurerar den inbyggda PHP-körningen för Web Apps i [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714)anger en anpassad PHP-körning och aktivera tillägg. Om du vill använda App Service, registrera dig för den [kostnadsfri utvärderingsversion]. För att få ut mesta möjliga av den här guiden kan skapa du först en PHP-webbapp i App Service.

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="how-to-change-the-built-in-php-version"></a>Anvisningar: Ändra den inbyggda PHP-versionen

Som standard är PHP 5.6 installerat och direkt tillgänglig för användning när du skapar en App Service-webbapp. Det bästa sättet att se tillgängliga versionen revision, standardkonfigurationen och aktiverade tilläggen är att distribuera ett skript som anropar den [phpinfo()] funktion.

PHP 7.0 och PHP 7.2-versioner är också tillgängligt, men inte aktiverad som standard. För att uppdatera den PHP-versionen, gör du något av följande metoder:

### <a name="azure-portal"></a>Azure Portal

1. Bläddra till din webbapp i den [Azure-portalen](https://portal.azure.com) och klicka på den **inställningar** knappen.

    ![Inställningarna för webbappen][settings-button]
2. Från den **inställningar** bladet väljer **programinställningar** och välj den nya versionen av PHP.

    ![Programinställningar][application-settings]
3. Klicka på den **spara** längst upp på den **Webbappinställningar** bladet.

    ![Spara konfigurationsinställningarna][save-button]

### <a name="azure-powershell-windows"></a>Azure PowerShell (Windows)

1. Öppna Azure PowerShell och logga in på ditt konto:

        PS C:\> Connect-AzureRmAccount
1. Ange PHP-version för webbappen.

        PS C:\> Set-AzureWebsite -PhpVersion {5.6 | 7.0 | 7.2} -Name {app-name}
1. PHP-version anges nu. Du kan bekräfta inställningarna:

        PS C:\> Get-AzureWebsite -Name {app-name} | findstr PhpVersion

### <a name="azure-cli"></a>Azure CLI 

Om du vill använda Azure-kommandoradsgränssnittet, måste du [installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) på datorn.

1. Öppna terminalen och logga in på ditt konto.

        az login

1. Kontrollera om du vill se en lista över körningar som stöds.

        az webapp list-runtimes | grep php

1. Ange PHP-version för webbappen.

        az webapp config set --php-version {5.6 | 7.0 | 7.1 | 7.2} --name {app-name} --resource-group {resource-group-name}

1. PHP-version anges nu. Du kan bekräfta inställningarna:

        az webapp show --name {app-name} --resource-group {resource-group-name}

## <a name="how-to-change-the-built-in-php-configurations"></a>Anvisningar: Ändra de inbyggda PHP-konfigurationerna

Du kan ändra några av konfigurationsalternativen genom att följa de här stegen för alla inbyggda PHP-körning. (Läs om hur php.ini-direktiv [lista över php.ini-direktiv].)

### <a name="changing-phpiniuser-phpiniperdir-phpiniall-configuration-settings"></a>Ändra PHP\_INI\_användare, PHP\_INI\_PERDIR, PHP\_INI\_alla konfigurationsinställningar

1. Lägg till en [. user.ini] filen till din rotkatalog.
1. Lägg till konfigurationsinställningar för att den `.user.ini` filen med hjälp av samma syntax som du vill använda i en `php.ini` fil. Exempel: Om du vill aktivera den `display_errors` inställning och ange `upload_max_filesize` till 10 miljoner din `.user.ini` filen innehåller den här texten:

        ; Example Settings
        display_errors=On
        upload_max_filesize=10M

        ; OPTIONAL: Turn this on to write errors to d:\home\LogFiles\php_errors.log
        ; log_errors=On
1. Distribuera din webbapp.
1. Starta om webbappen. (Omstart krävs eftersom frekvensen med vilken PHP läser `.user.ini` filer styrs av den `user_ini.cache_ttl` som är en system-nivåinställning och är 300 sekunder (fem minuter) som standard. Starta om webbappen tvingar PHP för att läsa de nya inställningarna i den `.user.ini` filen.)

Som ett alternativ till att använda en `.user.ini` -fil som du kan använda den [ini_set()] funktionen i skript för att ange konfigurationsalternativ som inte är på systemnivå direktiv.

### <a name="changing-phpinisystem-configuration-settings"></a>Ändra PHP\_INI\_systemkonfigurationsinställningar

1. Lägg till en Appinställning till Webbappen med nyckeln `PHP_INI_SCAN_DIR` och värde `d:\home\site\ini`
1. Skapa en `settings.ini` med Kudu-konsolen (http://&lt;site-name&gt;. scm.azurewebsite.net) i den `d:\home\site\ini` directory.
1. Lägg till konfigurationsinställningar för att den `settings.ini` filen med hjälp av samma syntax som du vill använda i en `php.ini` fil. Exempel: Om du vill peka den `curl.cainfo` att ställa in en `*.crt` filen och ange ”wincache.maxfilesize” inställningen till 512 kB din `settings.ini` filen innehåller den här texten:

        ; Example Settings
        curl.cainfo="%ProgramFiles(x86)%\Git\bin\curl-ca-bundle.crt"
        wincache.maxfilesize=512
1. Om du vill läsa in ändringarna, startar du om din Webbapp.

## <a name="how-to-enable-extensions-in-the-default-php-runtime"></a>Anvisningar: Aktivera tillägg i standardkörning för PHP

Enligt vad som anges i föregående avsnitt, det bästa sättet att se PHP-standardversionen, standardkonfigurationen och aktiverade tilläggen är att distribuera ett skript som anropar [phpinfo()]. För att aktivera ytterligare tillägg genom att följa dessa steg:

### <a name="configure-via-ini-settings"></a>Konfigurera via ini-inställningar

1. Lägg till en `ext` katalogen till den `d:\home\site` directory.
1. Placera `.dll` tillägget filer i den `ext` katalog (till exempel `php_xdebug.dll`). Se till att tillägg som är kompatibla med standardversionen av PHP och är VC9 och icke-trådsäkra (nts)-kompatibel.
1. Lägg till en Appinställning till Webbappen med nyckeln `PHP_INI_SCAN_DIR` och värde `d:\home\site\ini`
1. Skapa en `ini` fil i `d:\home\site\ini` kallas `extensions.ini`.
1. Lägg till konfigurationsinställningar för att den `extensions.ini` filen med hjälp av samma syntax som du vill använda i en `php.ini` fil. Om du vill aktivera MongoDB och XDebug tillägg, till exempel din `extensions.ini` filen innehåller den här texten:

        ; Enable Extensions
        extension=d:\home\site\ext\php_mongo.dll
        zend_extension=d:\home\site\ext\php_xdebug.dll
1. Starta om Webbappen för att läsa in ändringarna.

### <a name="configure-via-app-setting"></a>Konfigurera via Appinställning

1. Lägg till en `bin` katalogen till rotkatalogen.
1. Placera `.dll` tillägget filer i den `bin` katalog (till exempel `php_xdebug.dll`). Se till att tillägg som är kompatibla med standardversionen av PHP och är VC9 och icke-trådsäkra (nts)-kompatibel.
1. Distribuera din webbapp.
1. Bläddra till din webbapp i Azure-portalen och klicka på den **inställningar** knappen.

    ![Inställningarna för webbappen][settings-button]
1. Från den **inställningar** bladet väljer **programinställningar** och bläddra till den **appinställningar** avsnittet.
1. I den **appinställningar** skapar du en **PHP_EXTENSIONS** nyckel. Värdet för den här nyckeln skulle vara en sökväg i förhållande till webbplatsroten: **bin\your-ext-file**.

    ![Aktivera tillägget appinställningar][php-extensions]
1. Klicka på den **spara** längst upp på den **Webbappinställningar** bladet.

    ![Spara konfigurationsinställningarna][save-button]

Zend-tillägg stöds även med hjälp av en **PHP_ZENDEXTENSIONS** nyckel. Om du vill aktivera flera tillägg måste innehålla en kommaavgränsad lista över `.dll` filer för app inställningens värde.

## <a name="how-to-use-a-custom-php-runtime"></a>Anvisningar: Använda en anpassad PHP-körning

App Service Web Apps kan använda en PHP-körning som du anger för att köra PHP-skript i stället för standardkörning för PHP. Runtime som du anger kan konfigureras med en `php.ini` -fil som du även ange. Att använda en anpassad PHP-körning med Web Apps, följa dessa steg.

1. Hämta en icke-trådsäkra, VC9 eller VC11 kompatibel version av PHP för Windows. Nya versioner av PHP för Windows finns här: [ https://windows.php.net/download/ ]. Äldre versioner finns i det här arkivet: [ https://windows.php.net/downloads/releases/archives/ ].
1. Ändra den `php.ini` -filen för din runtime. Alla konfigurationsinställningar som är endast system-nivå direktiv ignoreras av Web Apps. (Läs om hur endast system-nivå direktiv [lista över php.ini-direktiv]).
1. Du kan också lägga till tillägg till PHP-körning och aktivera dem i den `php.ini` filen.
1. Lägg till en `bin` katalogen till rotkatalogen och placera den katalog som innehåller din PHP-körning i den (till exempel `bin\php`).
1. Distribuera din webbapp.
1. Bläddra till din webbapp i Azure-portalen och klicka på den **inställningar** knappen.

    ![Inställningarna för webbappen][settings-button]
1. Från den **inställningar** bladet väljer **programinställningar** och bläddra till den **Hanterarmappningar** avsnittet. Lägg till `*.php` till tillägget och lägga till sökvägen till den `php-cgi.exe` körbara. Om du placerar dina PHP-körning den `bin` katalogen i roten för ditt program sökvägen är `D:\home\site\wwwroot\bin\php\php-cgi.exe`.

    ![Ange hanteraren i Hanterarmappningar][handler-mappings]
1. Klicka på den **spara** längst upp på den **Webbappinställningar** bladet.

    ![Spara konfigurationsinställningarna][save-button]

<a name="composer" />

## <a name="how-to-enable-composer-automation-in-azure"></a>Anvisningar: Aktivera Composer-automatisering i Azure

Som standard göra App Service inte något med composer.json, om du har en i PHP-projekt. Om du använder [Git-distribution](app-service-deploy-local-git.md), du kan aktivera composer.json bearbetas under `git push` genom att aktivera tillägget Composer.

> [!NOTE]
> Du kan [rösta på förstklassig Composer stödet i App Service här](https://feedback.azure.com/forums/169385-web-apps-formerly-websites/suggestions/6477437-first-class-support-for-composer-and-pip)!
>

1. I din PHP web appbladet i den [Azure-portalen](https://portal.azure.com), klickar du på **verktyg** > **tillägg**.

    ![Inställningar för Azure portal-bladet för att aktivera Composer-automatisering i Azure](./media/web-sites-php-configure/composer-extension-settings.png)
2. Klicka på **Lägg till**, klicka sedan på **Composer**.

    ![Lägga till tillägget Composer om du vill aktivera Composer-automatisering i Azure](./media/web-sites-php-configure/composer-extension-add.png)
3. Klicka på **OK** att godkänna avtalsvillkoren. Klicka på **OK** igen för att lägga till tillägget.

    Den **installerat tillägg** bladet visar tillägget Composer.
    ![Godkänn juridiska villkor om du vill aktivera Composer-automatisering i Azure](./media/web-sites-php-configure/composer-extension-view.png)
4. Nu kan utföra i ett terminalfönster på din lokala dator `git add`, `git commit`, och `git push` till din Webbapp. Observera att Composer installerar beroenden som definierats i composer.json.

    ![Git-distribution med Composer-automatisering i Azure](./media/web-sites-php-configure/composer-extension-success.png)

## <a name="next-steps"></a>Nästa steg

Mer information finns i den [PHP Developer Center](https://azure.microsoft.com/develop/php/).

> [!NOTE]
> Om du vill komma igång med Azure App Service innan du registrerar dig för ett Azure-konto kan du gå till [Prova App Service](https://azure.microsoft.com/try/app-service/). Där kan du direkt skapa en tillfällig startwebbapp i App Service. Inga kreditkort krävs. Inga åtaganden.
>

[kostnadsfri utvärderingsversion]: https://www.windowsazure.com/pricing/free-trial/
[phpinfo()]: https://php.net/manual/en/function.phpinfo.php
[select-php-version]: ./media/web-sites-php-configure/select-php-version.png
[Lista över php.ini-direktiv]: http://www.php.net/manual/en/ini.list.php
[. user.ini]: http://www.php.net/manual/en/configuration.file.per-user.php
[ini_set()]: http://www.php.net/manual/en/function.ini-set.php
[application-settings]: ./media/web-sites-php-configure/application-settings.png
[settings-button]: ./media/web-sites-php-configure/settings-button.png
[save-button]: ./media/web-sites-php-configure/save-button.png
[php-extensions]: ./media/web-sites-php-configure/php-extensions.png
[handler-mappings]: ./media/web-sites-php-configure/handler-mappings.png
[https://windows.php.net/download/]: https://windows.php.net/download/
[https://windows.php.net/downloads/releases/archives/]: https://windows.php.net/downloads/releases/archives/
[SETPHPVERCLI]: ./media/web-sites-php-configure/ChangePHPVersion-XPlatCLI.png
[GETPHPVERCLI]: ./media/web-sites-php-configure/ShowPHPVersion-XplatCLI.png
[SETPHPVERPS]: ./media/web-sites-php-configure/ChangePHPVersion-PS.png
[GETPHPVERPS]: ./media/web-sites-php-configure/ShowPHPVersion-PS.png
