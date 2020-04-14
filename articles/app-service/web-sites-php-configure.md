---
title: Konfigurera PHP-körning
description: Lär dig hur du konfigurerar standardinstallationen av PHP eller lägger till en anpassad PHP-installation för Azure App Service.
author: msangapu-msft
ms.assetid: 95c4072b-8570-496b-9c48-ee21a223fb60
ms.devlang: php
ms.topic: article
ms.date: 04/13/2020
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 208f4f7b4c2d8562d5237a40f52e4774ea5c5606
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81272482"
---
# <a name="configure-php-in-azure-app-service"></a>Konfigurera PHP i Azure App Service

## <a name="introduction"></a>Introduktion

Den här guiden visar hur du konfigurerar den inbyggda PHP-körningen för webbappar och API-appar i [Azure App Service,](https://go.microsoft.com/fwlink/?LinkId=529714)tillhandahåller en anpassad PHP-körning och aktiverar tillägg. Om du vill använda App Service registrerar du dig för den [kostnadsfria utvärderingsversionen]. För att få ut det mesta av den här guiden bör du först skapa en PHP-app i App Service.

## <a name="how-to-change-the-built-in-php-version"></a>Så här: Ändra den inbyggda PHP-versionen

När du skapar en webbapp kan du välja den version av PHP som ska konfigureras. Se [PHP på App Service](https://github.com/Azure/app-service-linux-docs/blob/master/Runtime_Support/php_support.md) för aktuell information om versioner som stöds för närvarande.

Om du vill kontrollera den befintliga körningsversionen av appen kan du distribuera ett skript som anropar funktionen [phpinfo().]

För att uppdatera PHP-versionen, följ en av dessa metoder:

### <a name="azure-portal"></a>Azure Portal

1. Bläddra till din app i [Azure-portalen](https://portal.azure.com) och bläddra till **konfigurationssidan.**

2. Välj Allmänna **inställningar i** **konfigurationen**och välj den nya PHP-versionen.

3. Klicka på knappen **Spara** högst upp på bladet **Allmänna inställningar.**

### <a name="azure-cli"></a>Azure CLI 

Om du vill använda Azure Command-Line-gränssnittet måste du [installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) på din dator.

1. Öppna Terminal och logga in på ditt konto.

        az login

1. Kontrollera om du vill se listan över runtimes som stöds.

        az webapp list-runtimes | grep php

2. Ställ in PHP-versionen för appen.

        az webapp config set --php-version {5.6 | 7.2 | 7.3} --name {app-name} --resource-group {resource-group-name}

3. PHP-versionen är nu inställd. Du kan bekräfta följande inställningar:

        az webapp show --name {app-name} --resource-group {resource-group-name}

## <a name="how-to-change-the-built-in-php-configurations"></a>Så här ändrar du de inbyggda PHP-konfigurationerna

För alla inbyggda PHP-körning kan du ändra något av konfigurationsalternativen genom att följa dessa steg. (För information om php.ini direktiv, se [Lista över php.ini direktiv].)

### <a name="changing-php_ini_user-php_ini_perdir-php_ini_all-configuration-settings"></a>Ändra\_PHP\_INI-ANVÄNDARE, PHP\_\_INI\_PERDIR, PHP INI\_ALLA konfigurationsinställningar

1. Lägg till en [.user.ini-fil i] rotkatalogen.
1. Lägg till konfigurationsinställningar i `.user.ini` filen med samma `php.ini` syntax som du skulle använda i en fil. Om du till exempel vill `display_errors` aktivera inställningen `upload_max_filesize` och ange inställningen till `.user.ini` 10 M, skulle filen innehålla den här texten:

        ; Example Settings
        display_errors=On
        upload_max_filesize=10M

        ; OPTIONAL: Turn this on to write errors to d:\home\LogFiles\php_errors.log
        ; log_errors=On
2. Distribuera din app.
3. Starta om appen. (Omstart är nödvändigt eftersom frekvensen `.user.ini` med vilken PHP `user_ini.cache_ttl` läser filer styrs av inställningen, vilket är en systemnivåinställning och är 300 sekunder (5 minuter) som standard. Starta om appen tvingar PHP att läsa `.user.ini` de nya inställningarna i filen.)

Som ett alternativ `.user.ini` till att använda en fil kan du använda funktionen [ini_set()] i skript för att ange konfigurationsalternativ som inte är direktiv på systemnivå.

### <a name="changing-php_ini_system-configuration-settings"></a>Ändra\_konfigurationsinställningar för PHP INI\_SYSTEM

1. Lägga till en appinställning i `PHP_INI_SCAN_DIR` appen med nyckeln och värdet`d:\home\site\ini`
1. Skapa `settings.ini` en fil med Kudu Console&lt;(http://&gt;platsnamn .scm.azurewebsite.net) `d:\home\site\ini` i katalogen.
1. Lägg till konfigurationsinställningar i `settings.ini` filen med samma `php.ini` syntax som du skulle använda i en fil. Om du till exempel vill `curl.cainfo` peka `*.crt` inställningen på en fil och ange inställningen "wincache.maxfilesize" till 512 K, skulle `settings.ini` filen innehålla den här texten:

        ; Example Settings
        curl.cainfo="%ProgramFiles(x86)%\Git\bin\curl-ca-bundle.crt"
        wincache.maxfilesize=512
1. Om du vill läsa in ändringarna igen startar du om appen.

## <a name="how-to-enable-extensions-in-the-default-php-runtime"></a>Så här aktiverar du tillägg i standard-PHP-körningen

Som nämnts i föregående avsnitt, det bästa sättet att se standard PHP-versionen, dess standardkonfiguration, och de aktiverade tillägg är att distribuera ett skript som kallar [phpinfo()]. Så här aktiverar du ytterligare tillägg genom att följa dessa steg:

### <a name="configure-via-ini-settings"></a>Konfigurera via ini-inställningar

1. Lägg `ext` till en `d:\home\site` katalog i katalogen.
1. Placera `.dll` tilläggsfiler `ext` i katalogen `php_xdebug.dll`(till exempel ). Se till att tilläggen är kompatibla med standardversionen av PHP och är VC9- och icke-trådsäkra (nts) kompatibla.
1. Lägga till en appinställning i `PHP_INI_SCAN_DIR` appen med nyckeln och värdet`d:\home\site\ini`
1. Skapa `ini` en `d:\home\site\ini` fil `extensions.ini`i namnet .
1. Lägg till konfigurationsinställningar i `extensions.ini` filen med samma `php.ini` syntax som du skulle använda i en fil. Om du till exempel vill aktivera tilläggen MongoDB och `extensions.ini` XDebug innehåller filen den här texten:

        ; Enable Extensions
        extension=d:\home\site\ext\php_mongo.dll
        zend_extension=d:\home\site\ext\php_xdebug.dll
1. Starta om appen för att läsa in ändringarna.

### <a name="configure-via-app-setting"></a>Konfigurera via appinställning

1. Lägg `bin` till en katalog i rotkatalogen.
2. Placera `.dll` tilläggsfiler `bin` i katalogen `php_xdebug.dll`(till exempel ). Se till att tilläggen är kompatibla med standardversionen av PHP och är VC9- och icke-trådsäkra (nts) kompatibla.
3. Distribuera din app.
4. Bläddra till din app i Azure-portalen och klicka på **avsnittet Konfiguration** under **Inställningar.**
5. Välj **Programinställningar**i **bladet Konfiguration** .
6. I avsnittet **Programinställningar** klickar du på **+ Ny programinställning** och skapar en PHP_EXTENSIONS-tangenten. **PHP_EXTENSIONS** Värdet för den här nyckeln skulle vara en sökväg i förhållande till webbplatsroten: **bin\your-ext-file**.
7. Klicka på knappen **Uppdatera** längst ned och klicka sedan på **Spara** ovanför fliken **Programinställningar.**

Zend-tillägg stöds också med hjälp av en **PHP_ZENDEXTENSIONS** nyckel. Om du vill aktivera flera tillägg inkluderar `.dll` du en kommaavgränsad lista med filer för appinställningsvärdet.

## <a name="how-to-use-a-custom-php-runtime"></a>Så här: Använd en anpassad PHP-körning

I stället för standard PHP runtime, apptjänst kan använda en PHP runtime som du tillhandahåller för att köra PHP-skript. Körningen som du anger kan konfigureras av en `php.ini` fil som du också anger. Om du vill använda en anpassad PHP-körning med App Service följer du dessa steg.

1. Skaffa en icke-trådsäker, VC9- eller VC11-kompatibel version av PHP för Windows. Senaste utgåvor av PHP för [https://windows.php.net/download/]Windows kan hittas här: . Äldre utgåvor finns i arkivet [https://windows.php.net/downloads/releases/archives/]här: .
2. Ändra `php.ini` filen för körningen. Alla konfigurationsinställningar som är direktiv med endast systemnivå ignoreras av App Service. (För information om direktiv som endast gäller på systemnivå, se [Lista över php.ini-direktiv]).
3. Du kan också lägga till tillägg till din `php.ini` PHP-körning och aktivera dem i filen.
4. Lägg `bin` till en katalog i rotkatalogen och placera katalogen som innehåller `bin\php`din PHP-körning i den (till exempel).
5. Distribuera din app.
6. Bläddra till din app i Azure-portalen och klicka på **konfigurationsbladet.**
8. Välj **Sökvägsmappningar**i **bladet Konfiguration** . 
9. Klicka på **+ Ny hanterare** och `*.php` lägg till `php-cgi.exe` i fältet Tillägg och lägg till sökvägen till den körbara i **Skriptprocessorn**. Om du sätter din PHP `bin` runtime i katalogen i `D:\home\site\wwwroot\bin\php\php-cgi.exe`roten av ditt program, är sökvägen .
10. Klicka på **Uppdatera** längst ned för att slutföra handtagsmappningen.
11. Klicka på **Spara** för att spara ändringarna.

<a name="composer" />

## <a name="how-to-enable-composer-automation-in-azure"></a>Så här aktiverar du composer automation i Azure

Som standard gör App Service ingenting med composer.json, om du har en i ditt PHP-projekt. Om du använder [Git-distribution](deploy-local-git.md)kan du aktivera `git push` composer.json-bearbetning under genom att aktivera tillägget Composer.

> [!NOTE]
> Du kan [rösta på förstklassigt Composer-stöd i App Service här!](https://feedback.azure.com/forums/169385-web-apps-formerly-websites/suggestions/6477437-first-class-support-for-composer-and-pip)
>

1. Klicka på > **Verktygstillägg**i PHP-appens blad i [Azure-portalen](https://portal.azure.com). **Tools**

    ![Blad för azure-portalinställningar för att aktivera Composer-automatisering i Azure](./media/web-sites-php-configure/composer-extension-settings.png)
2. Klicka på **Lägg till**och sedan på **Composer**.

    ![Lägg till Composer-tillägg för att aktivera Composer-automatisering i Azure](./media/web-sites-php-configure/composer-extension-add.png)
3. Klicka på **OK** om du vill acceptera juridiska villkor. Klicka på **OK** igen om du vill lägga till tillägget.

    Bladet **Installerade tillägg** visar tillägget Composer.
    ![Acceptera juridiska termer för att aktivera Composer automation i Azure](./media/web-sites-php-configure/composer-extension-view.png)
4. Nu, i ett terminalfönster på `git add`din `git commit`lokala `git push` dator, utföra , och till din app. Observera att Composer installerar beroenden som definierats i composer.json.

    ![Git-distribution med Composer-automatisering i Azure](./media/web-sites-php-configure/composer-extension-success.png)

## <a name="next-steps"></a>Nästa steg

Mer information finns i [PHP Developer Center](https://azure.microsoft.com/develop/php/).

[kostnadsfri prov]: https://www.windowsazure.com/pricing/free-trial/
[phpinfo()]: https://php.net/manual/en/function.phpinfo.php
[select-php-version]: ./media/web-sites-php-configure/select-php-version.png
[Lista över php.ini-direktiv]: https://www.php.net/manual/en/ini.list.php
[.user.ini]: https://www.php.net/manual/en/configuration.file.per-user.php
[ini_set()]: https://www.php.net/manual/en/function.ini-set.php
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
