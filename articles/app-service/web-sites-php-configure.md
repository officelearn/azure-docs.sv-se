---
title: Konfigurera PHP-körning
description: Lär dig hur du konfigurerar standard PHP-installationen eller lägger till en anpassad PHP-installation för Azure App Service.
author: msangapu-msft
ms.assetid: 95c4072b-8570-496b-9c48-ee21a223fb60
ms.devlang: php
ms.topic: article
ms.date: 04/11/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: c73fb55e485d0c92d27eac2ac197a81337b9d5e1
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2020
ms.locfileid: "77016807"
---
# <a name="configure-php-in-azure-app-service"></a>Konfigurera PHP i Azure App Service

## <a name="introduction"></a>Introduktion

Den här guiden visar hur du konfigurerar den inbyggda PHP-körningen för webbappar, mobila Server delar och API-appar i [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714), tillhandahåller en anpassad php-körning och aktiverar tillägg. Registrera dig för den [kostnads fria utvärderings versionen]om du vill använda app service. För att få ut mesta möjliga av den här guiden bör du först skapa en PHP-app i App Service.

## <a name="how-to-change-the-built-in-php-version"></a>Gör så här: ändra den inbyggda PHP-versionen

PHP 5,6 är som standard installerat och omedelbart tillgängligt för användning när du skapar en App Service-app. Det bästa sättet att se tillgänglig versions revision, dess standard konfiguration och aktiverade tillägg är att distribuera ett skript som anropar funktionen [phpinfo ()] .

Versionerna PHP 7,0 och PHP 7,2 är också tillgängliga, men inte aktiverade som standard. Om du vill uppdatera PHP-versionen följer du någon av följande metoder:

### <a name="azure-portal"></a>Azure portal

1. Bläddra till din app i [Azure Portal](https://portal.azure.com) och bläddra till **konfigurations** sidan.

2. Från **konfiguration**väljer du **allmänna inställningar** och sedan den nya php-versionen.

3. Klicka på knappen **Spara** överst på bladet **allmänna inställningar** .

### <a name="azure-cli"></a>Azure CLI 

Om du vill använda Azures kommando rads gränssnitt måste du [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) på datorn.

1. Öppna terminalen och logga in på ditt konto.

        az login

1. Kontrol lera om du vill se en lista över körningar som stöds.

        az webapp list-runtimes | grep php

2. Ange PHP-versionen för appen.

        az webapp config set --php-version {5.6 | 7.0 | 7.1 | 7.2} --name {app-name} --resource-group {resource-group-name}

3. PHP-versionen har nu ställts in. Du kan bekräfta de här inställningarna:

        az webapp show --name {app-name} --resource-group {resource-group-name}

## <a name="how-to-change-the-built-in-php-configurations"></a>Gör så här: ändra de inbyggda PHP-konfigurationerna

För alla inbyggda PHP-körningar kan du ändra konfigurations alternativen genom att följa dessa steg. (Information om php. ini-direktiv finns i [lista över php. ini-direktiv].)

### <a name="changing-php_ini_user-php_ini_perdir-php_ini_all-configuration-settings"></a>Att ändra PHP\_INI\_användare, PHP\_INI\_PERDIR, PHP\_INI\_alla konfigurations inställningar

1. Lägg till en [. user. ini] -fil i rot katalogen.
1. Lägg till konfigurations inställningar i `.user.ini`-filen med samma syntax som du skulle använda i en `php.ini`-fil. Om du till exempel vill aktivera inställningen `display_errors` och ange inställningen `upload_max_filesize` till 10 miljoner skulle `.user.ini`-filen innehålla följande text:

        ; Example Settings
        display_errors=On
        upload_max_filesize=10M

        ; OPTIONAL: Turn this on to write errors to d:\home\LogFiles\php_errors.log
        ; log_errors=On
2. Distribuera din app.
3. Starta om appen. (Omstart är nödvändigt eftersom den frekvens med vilken PHP läser `.user.ini`-filer styrs av `user_ini.cache_ttl` inställningen, som är en inställning på system nivå och är 300 sekunder (5 minuter) som standard. Att starta om appen tvingar PHP att läsa de nya inställningarna i `.user.ini`-filen.)

Som ett alternativ till att använda en `.user.ini`-fil kan du använda funktionen [ini_set ()] i skript för att ange konfigurations alternativ som inte är direktiv på system nivå.

### <a name="changing-php_ini_system-configuration-settings"></a>Ändra inställningarna för PHP\_INI-\_SYSTEM konfiguration

1. Lägg till en app-inställning till din app med nyckeln `PHP_INI_SCAN_DIR` och värde `d:\home\site\ini`
1. Skapa en `settings.ini`-fil med kudu-konsolen (http://&lt;plats namn&gt;. scm.azurewebsite.net) i `d:\home\site\ini` katalogen.
1. Lägg till konfigurations inställningar i `settings.ini`-filen med samma syntax som du skulle använda i en `php.ini`-fil. Om du till exempel vill peka `curl.cainfo` inställningen till en `*.crt` fil och ange inställningen "WinCache. MaxFileSize till 512 KB, innehåller `settings.ini`-filen den här texten:

        ; Example Settings
        curl.cainfo="%ProgramFiles(x86)%\Git\bin\curl-ca-bundle.crt"
        wincache.maxfilesize=512
1. Starta om appen om du vill läsa in ändringarna igen.

## <a name="how-to-enable-extensions-in-the-default-php-runtime"></a>Gör så här: aktivera tillägg i standard körningen av PHP

Som vi noterade i föregående avsnitt är det bästa sättet att se standard PHP-versionen, standard konfigurationen och de aktiverade tilläggen är att distribuera ett skript som anropar [phpinfo ()]. Följ dessa steg om du vill aktivera ytterligare tillägg:

### <a name="configure-via-ini-settings"></a>Konfigurera via ini-inställningar

1. Lägg till en `ext` katalog i `d:\home\site`-katalogen.
1. Lägg `.dll` tilläggsfiler i `ext`-katalogen (till exempel `php_xdebug.dll`). Kontrol lera att tilläggen är kompatibla med standard versionen av PHP och att de är VC9-kompatibla och icke-tråd säkra (nter)-kompatibla.
1. Lägg till en app-inställning till din app med nyckeln `PHP_INI_SCAN_DIR` och värde `d:\home\site\ini`
1. Skapa en `ini`-fil i `d:\home\site\ini` som kallas för `extensions.ini`.
1. Lägg till konfigurations inställningar i `extensions.ini`-filen med samma syntax som du skulle använda i en `php.ini`-fil. Om du till exempel vill aktivera tilläggen MongoDB och XDebug skulle din `extensions.ini`-fil innehålla texten:

        ; Enable Extensions
        extension=d:\home\site\ext\php_mongo.dll
        zend_extension=d:\home\site\ext\php_xdebug.dll
1. Starta om appen för att läsa in ändringarna.

### <a name="configure-via-app-setting"></a>Konfigurera via app-inställning

1. Lägg till en `bin` katalog i rot katalogen.
2. Lägg `.dll` tilläggsfiler i `bin`-katalogen (till exempel `php_xdebug.dll`). Kontrol lera att tilläggen är kompatibla med standard versionen av PHP och att de är VC9-kompatibla och icke-tråd säkra (nter)-kompatibla.
3. Distribuera din app.
4. Bläddra till din app i Azure Portal och klicka på avsnittet **konfiguration** som finns under **Inställningar** .
5. Välj **program inställningar**på bladet **konfiguration** .
6. I avsnittet **program inställningar** klickar du på **+ ny program inställning** och skapar en **PHP_EXTENSIONS** nyckel. Värdet för den här nyckeln är en sökväg i förhållande till webbplatsens rot: **bin\your-ext-File**.
7. Klicka på knappen **Uppdatera** längst ned och sedan på **Spara** ovanför fliken **program inställningar** .

Zend-tillägg stöds också med hjälp av en **PHP_ZENDEXTENSIONS** nyckel. Om du vill aktivera flera tillägg inkluderar du en kommaavgränsad lista med `.dll` filer för värdet för appens inställning.

## <a name="how-to-use-a-custom-php-runtime"></a>Gör så här: använda en anpassad PHP-körning

I stället för standard körnings miljön för PHP kan App Service använda en PHP-körning som du anger för att köra PHP-skript. Den körning som du anger kan konfigureras med en `php.ini`-fil som du också anger. Följ dessa steg om du vill använda en anpassad PHP-körning med App Service.

1. Skaffa en icke-tråd säker, VC9-eller VC11-kompatibel version av PHP för Windows. Du hittar de senaste versionerna av PHP för Windows här: [https://windows.php.net/download/]. Du hittar äldre versioner i arkivet här: [https://windows.php.net/downloads/releases/archives/].
2. Ändra `php.ini`-filen för din körning. Alla konfigurations inställningar som är endast på system-nivå-direktiv ignoreras av App Service. (Information om direktiv på endast system nivå finns i [lista över php. ini-direktiv]).
3. Du kan också lägga till tillägg i PHP-körningsmiljön och aktivera dem i `php.ini`-filen.
4. Lägg till en `bin` katalog i rot katalogen och Lägg till den katalog som innehåller din PHP-körning i den (till exempel `bin\php`).
5. Distribuera din app.
6. Bläddra till din app i Azure Portal och klicka på **konfigurations** bladet.
8. Välj **Sök vägs mappningar**på bladet **konfiguration** . 
9. Klicka på **+ ny hanterare** och lägg till `*.php` i fältet tillägg och Lägg till sökvägen till den `php-cgi.exe` körbara filen i **skript processorn**. Om du använder din PHP-körning i `bin`-katalogen i programmets rot är sökvägen `D:\home\site\wwwroot\bin\php\php-cgi.exe`.
10. Klicka på **Uppdatera** längst ned för att lägga till mappningen hanterare.
11. Klicka på **Spara** för att spara ändringarna.

<a name="composer" />

## <a name="how-to-enable-composer-automation-in-azure"></a>Gör så här: Aktivera Composer-automatisering i Azure

Som standard gör App Service inte något med Composer. JSON, om du har en i ditt PHP-projekt. Om du använder [Git-distribution](deploy-local-git.md)kan du aktivera Composer. JSON-bearbetning under `git push` genom att aktivera Composer-tillägget.

> [!NOTE]
> Du kan [rösta på stöd för första klass Composer i App Service här](https://feedback.azure.com/forums/169385-web-apps-formerly-websites/suggestions/6477437-first-class-support-for-composer-and-pip)!
>

1. I PHP-appens blad i [Azure Portal](https://portal.azure.com)klickar du på **verktyg** > **tillägg**.

    ![Azure Portal inställnings bladet för att aktivera Composer-automatisering i Azure](./media/web-sites-php-configure/composer-extension-settings.png)
2. Klicka på **Lägg till**och sedan på **Composer**.

    ![Lägg till Composer-tillägg för att aktivera Composer-automatisering i Azure](./media/web-sites-php-configure/composer-extension-add.png)
3. Klicka på **OK** för att acceptera juridiska villkor. Klicka på **OK** igen för att lägga till tillägget.

    Bladet **installerade tillägg** visar Composer-tillägget.
    ![acceptera juridiska villkor för att aktivera Composer-automatisering i Azure](./media/web-sites-php-configure/composer-extension-view.png)
4. I ett terminalfönster på den lokala datorn utför du nu `git add`, `git commit`och `git push` till din app. Observera att Composer installerar beroenden som definierats i Composer. JSON.

    ![Git-distribution med Composer-automatisering i Azure](./media/web-sites-php-configure/composer-extension-success.png)

## <a name="next-steps"></a>Nästa steg

Mer information finns i [php Developer Center](https://azure.microsoft.com/develop/php/).

[kostnads fria utvärderings versionen]: https://www.windowsazure.com/pricing/free-trial/
[phpinfo ()]: https://php.net/manual/en/function.phpinfo.php
[select-php-version]: ./media/web-sites-php-configure/select-php-version.png
[Lista över php. ini-direktiv]: https://www.php.net/manual/en/ini.list.php
[. user. ini]: https://www.php.net/manual/en/configuration.file.per-user.php
[ini_set ()]: https://www.php.net/manual/en/function.ini-set.php
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
