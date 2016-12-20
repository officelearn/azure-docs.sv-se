---
title: Ansluta en webbapp i Azure App Service till Redis-cache via Memcache-protokollet | Microsoft Docs
description: Ansluta en webbapp i Azure App Service till Redis-cache med Memcache-protokollet
services: app-service\web
documentationcenter: php
author: SyntaxC4
manager: wpickett
editor: riande
ms.assetid: 0fcdf9fa-2995-4839-ba4d-cfa389c4ba06
ms.service: app-service-web
ms.devlang: php
ms.topic: get-started-article
ms.tgt_pltfrm: windows
ms.workload: na
ms.date: 02/29/2016
ms.author: cfowler
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: d8a177074d6b7671fe04081c5547665ec892f244


---
# <a name="connect-a-web-app-in-azure-app-service-to-redis-cache-via-the-memcache-protocol"></a>Ansluta en webbapp i Azure App Service till Redis-cache via Memcache-protokollet
I den här artikeln får du veta hur du ansluter en WordPress-webbapp i [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) till [Azure Redis Cache][12] med hjälp av protokollet [Memcache][13]. Om du har en befintlig webbapp som använder en Memcache-lagrad server till att cachelagra i minnet, kan du migrera den till Azure App Service och använda förstapartslösningen för cachelagring i Microsoft Azure med liten eller ingen ändring av programkoden. Du kan använda dina befintliga kunskaper om Memcache och skapa skalbara, distribuerade appar i Azure App Service med Azure Redis-cache för att cachelagra i minnet. Samtidigt kan du använda populära programramverk som .NET, PHP, Node.js, Java och Python.  

Med App Service Web Apps blir det här programscenariot möjligt tack vare Web Apps Memcache-shim. Det är en lokal Memcache-lagrad server som fungerar som en Memcache-proxy för cachelagring av anrop till Azure Redis-cache. Det gör att alla typer av appar kan kommunicera via Memcache-protokollet och cachelagra data med Redis-cache. Detta Memcache-shim fungerar på protokollnivå. Det gör att det kan användas av alla program och programramverk som kommunicerar med hjälp av Memcache-protokollet.

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="prerequisites"></a>Krav
Web Apps Memcache-shim kan användas med alla program som kommunicerar med hjälp av Memcache-protokollet. I det här exemplet är referensprogrammet en skalbar WordPress-webbplats som kan etableras via Azure Marketplace.

Följ anvisningarna i de här artiklarna:

* [Etablera en instans av tjänsten Azure Redis Cache][0]
* [Distribuera en skalbar WordPress-webbplats i Azure][1]

När du har distribuerat den skalbara WordPress-webbplatsen och etablerat en Redis-cacheinstans kan du fortsätta med att aktivera Memcache-shim i Azure App Service Web Apps.

## <a name="enable-the-web-apps-memcache-shim"></a>Aktivera Web Apps Memcache-shim
Du måste skapa tre appinställningar för att konfigurera Memcache-shim. Det kan du göra på flera olika sätt, bland annat via [Azure Portal](http://go.microsoft.com/fwlink/?LinkId=529715), den [klassiska portalen][3], [Azure PowerShell-Cmdlets][5] eller [Azure-kommandoradsgränssnittet][5]. I det här exemplet ska vi konfigurera appinställningarna med hjälp av [Azure Portal][4]. Följande värden kan hämtas från Redis-Cache-instansens **inställnings**blad.

![Inställningsblad för Azure Redis-cache](./media/web-sites-connect-to-redis-using-memcache-protocol/1-azure-redis-cache-settings.png)

### <a name="add-redishost-app-setting"></a>Lägga till appinställningen REDIS_HOST
Den första appinställning du behöver skapa är appinställningen **REDIS\_HOST**. Den här inställningen anger målet dit shimmet vidarebefordrar cacheinformationen. Värdet som krävs för appinställningen REDIS_HOST kan hämtas från bladet **Egenskaper** för Redis-Cache-instansen.

![Värdnamn för Azure Redis-cache](./media/web-sites-connect-to-redis-using-memcache-protocol/2-azure-redis-cache-hostname.png)

Ange **REDIS\_HOST** som nyckel för appinställningen och **värdnamnet** för Redis-cacheinstansen som värde för appinställningen.

![Appinställningen REDIS_HOST för Webbappar](./media/web-sites-connect-to-redis-using-memcache-protocol/3-azure-website-appsettings-redis-host.png)

### <a name="add-rediskey-app-setting"></a>Lägga till appinställningen REDIS_KEY
Den andra appinställning du behöver skapa är appinställningen **REDIS\_KEY**. Den här inställningen ger den autentiseringstoken som krävs för säker åtkomst till Redis-cacheinstansen. Du kan hämta värdet som krävs för appinställningen REDIS_KEY från bladet **Åtkomstnycklar** för Redis-cacheinstansen.

![Primär nyckel för Azure Redis-cache](./media/web-sites-connect-to-redis-using-memcache-protocol/4-azure-redis-cache-primarykey.png)

Ange **REDIS\_KEY** som nyckel för appinställningen och Redis-cacheinstansens **primära nyckel** som värde för appinställningen.

![Azure-webbplats, AppSetting REDIS_KEY](./media/web-sites-connect-to-redis-using-memcache-protocol/5-azure-website-appsettings-redis-primarykey.png)

### <a name="add-memcacheshimredisenable-app-setting"></a>Lägga till appinställningen MEMCACHESHIM_REDIS_ENABLE
Den sista appinställningen används till att aktivera Memcache-shim i Web Apps. Det ansluter till Azure Redis-cache och vidarebefordrar cacheanrop med hjälp av REDIS_HOST och REDIS_KEY. Ange **MEMCACHESHIM\_REDIS\_ENABLE** som nyckel för appinställningen och värdet till **sant**.

![Webbapp, AppSetting MEMCACHESHIM_REDIS_ENABLE](./media/web-sites-connect-to-redis-using-memcache-protocol/6-azure-website-appsettings-enable-shim.png)

När du har lagt till de tre appinställningarna klickar du på **Spara**.

## <a name="enable-memcache-extension-for-php"></a>Aktivera Memcache-tillägget för PHP
Om programmet ska kunna kommunicera med Memcache-protokollet måste du installera Memcache-tillägget för PHP – språkramverket för WordPress-webbplatsen.

### <a name="download-the-phpmemcache-extension"></a>Ladda ned tillägget php_memcache
Bläddra till [PECL][6]. Under cachelagringskategorin klickar du på [memcache][7]. Klicka på DLL-länken under kolumnen för nedladdningar.

![PHP PECL-webbplats](./media/web-sites-connect-to-redis-using-memcache-protocol/7-php-pecl-website.png)

Ladda ned länken till Non-Thread Safe (NTS) x86 för den PHP-version som är aktiverad i Web Apps. (Standard är PHP 5.4)

![PHP PECL-webbplats, Memcache-paket](./media/web-sites-connect-to-redis-using-memcache-protocol/8-php-pecl-memcache-package.png)

### <a name="enable-the-phpmemcache-extension"></a>Aktivera tillägget php_memcache
När du har laddat ned filen packar du upp och laddar upp **php\_memcache.dll** till katalogen **d:\\home\\site\\wwwroot\\bin\\ext\\**. När du har laddat upp php_memcache.dll till webbappen, måste du aktivera tillägget för PHP-körning. Om du vill aktivera Memcache-tillägget i Azure Portal öppnar du bladet **Programinställningar** för webbappen. Sedan lägger du till en ny appinställning med nyckeln **PHP\_EXTENSIONS** och värdet **bin\\ext\\php_memcache.dll**.

> [!NOTE]
> Om webbappen behöver läsa in flera PHP-tillägg, ska värdet PHP_EXTENSIONS vara en kommaavgränsad lista med relativa sökvägar till DLL-filer.
> 
> 

![Webbapp, AppSetting PHP_EXTENSIONS](./media/web-sites-connect-to-redis-using-memcache-protocol/9-azure-website-appsettings-php-extensions.png)

När du är klar klickar du på **Spara**.

## <a name="install-memcache-wordpress-plugin"></a>Installera WordPress-plugin-programmet för Memcache
> [!NOTE]
> Du kan också ladda ned [plugin-programmet Memcached Object Cache](https://wordpress.org/plugins/memcached/) från WordPress.org.
> 
> 

På sidan med plugin-program för WordPress klickar du på **Add New** (Lägg till nytt).

![WordPress – plugin-sida](./media/web-sites-connect-to-redis-using-memcache-protocol/10-wordpress-plugin.png)

I sökrutan skriver du **memcached** och trycker på **Retur**.

![WordPress – lägga till nytt plugin-program](./media/web-sites-connect-to-redis-using-memcache-protocol/11-wordpress-add-new-plugin.png)

Leta upp **Memcached Object Cache** i listan och klicka på **Install now** (Installera nu).

![WordPress – installera plugin-programmet för Memcache ](./media/web-sites-connect-to-redis-using-memcache-protocol/12-wordpress-install-memcache-plugin.png)

### <a name="enable-the-memcache-wordpress-plugin"></a>Aktivera WordPress-plugin-programmet för Memcache
> [!NOTE]
> Installera Visual Studio Team Services genom att följa anvisningarna i den här bloggen om att [Aktivera ett webbplatstillägg i Web Apps][8].
> 
> 

I slutet av `wp-config.php`-filen lägger du till följande kod över texten om att sluta redigera.

```php
$memcached_servers = array(
    'default' => array('localhost:' . getenv("MEMCACHESHIM_PORT"))
);
```

När du klistrat in koden sparas dokumentet automatiskt.

Nästa steg är att aktivera plugin-programmet för objektcache. Det gör du genom att dra och släppa **object-cache.php** från mappen **wp-content/plugins/memcached** till mappen **wp-content** så aktiveras funktionerna i Memcache Object Cache.

![Leta upp memcache-plugin-filen object-cache.php](./media/web-sites-connect-to-redis-using-memcache-protocol/13-locate-memcache-object-cache-plugin.png)

När filen **object-cache.php** ligger i mappen **wp-content** är Memcached Object Cache aktiverat.

![Aktivera memcache-plugin-filen object-cache.php](./media/web-sites-connect-to-redis-using-memcache-protocol/14-enable-memcache-object-cache-plugin.png)

## <a name="verify-the-memcache-object-cache-plugin-is-functioning"></a>Kontrollera att plugin-programmet Memcache Object Cache fungerar
Nu har du utfört alla steg för att aktivera Memcache-shim för Web Apps. Det enda som återstår nu är att kontrollera att data fylls på i Redis-cacheinstansen.

### <a name="enable-the-non-ssl-port-support-in-azure-redis-cache"></a>Aktivera stöd för icke-SSL-port i Azure Redis-cache
> [!NOTE]
> Vid tidpunkten när den här artikeln skrivs har Redis CLI inte stöd för SSL-anslutning. Därför är följande steg nödvändiga.
> 
> 

I Azure Portal går du till den Redis-cacheinstans du skapat för webbappen. När cachens blad är öppet klickar du på ikonen för **Inställningar**.

![Inställningsknapp för Azure Redis-cache](./media/web-sites-connect-to-redis-using-memcache-protocol/15-azure-redis-cache-settings-button.png)

Välj **Åtkomstportar** i listan.

![Åtkomstport för Azure Redis-cache](./media/web-sites-connect-to-redis-using-memcache-protocol/16-azure-redis-cache-access-port.png)

Klicka på **Nej** för **Tillåt åtkomst endast via SSL**.

![Åtkomstport för Azure Redis-cache, endast SSL](./media/web-sites-connect-to-redis-using-memcache-protocol/17-azure-redis-cache-access-port-ssl-only.png)

Du ser att icke-SSL-porten har angetts. Klicka på **Spara**.

![Azure Redis-cache, Redis-åtkomstportal, icke-SSL](./media/web-sites-connect-to-redis-using-memcache-protocol/18-azure-redis-cache-access-port-non-ssl.png)

### <a name="connect-to-azure-redis-cache-from-redis-cli"></a>Ansluta till Azure Redis-cache från redis-cli
> [!NOTE]
> Det här steget förutsätter att Redis har installerats lokalt på utvecklingsdatorn. [Installera Redis lokalt med hjälp av de här anvisningarna][9].
> 
> 

Öppna en kommandoradskonsol och skriv följande kommando:

```shell
redis-cli –h <hostname-for-redis-cache> –a <primary-key-for-redis-cache> –p 6379
```

Ersätt **&lt;hostname-for-redis-cache&gt;** med det faktiska värdnamnet för xxxxx.redis.cache.windows.net och **&lt;primary-key-for-redis-cache&gt;** med åtkomstnyckeln för cachen och tryck på **Retur**. När CLI har anslutit till Redis-cacheinstansen kör du ett Redis-kommando. På följande skärmbild har jag valt att lista nycklarna.

![Ansluta till Azure Redis-cache från Redis-CLI i terminal](./media/web-sites-connect-to-redis-using-memcache-protocol/19-redis-cli-terminal.png)

Kommandot för att lista nycklarna bör returnera ett värde. Annars kan du prova med att navigera till webbappen och försöka igen.

## <a name="conclusion"></a>Slutsats
Gratulerar! Nu har WordPress-appen en centraliserad minnescache som ger ökat genomflöde. Kom ihåg att Web Apps Memcache-shim kan användas med alla Memcache-klienter oavsett programmeringsspråk och programramverk. Om du vill ge feedback eller ställa frågor om Web Apps Memcache-shim kan du göra inlägg på [MSDN-forum][10] eller [Stackoverflow][11].

> [!NOTE]
> Om du vill komma igång med Azure App Service innan du registrerar dig för ett Azure-konto kan du gå till [Prova App Service](http://go.microsoft.com/fwlink/?LinkId=523751). Där kan du direkt skapa en tillfällig startwebbapp i App Service. Inget kreditkort krävs, och du gör inga åtaganden.
> 
> 

## <a name="whats-changed"></a>Nyheter
* En guide till övergången från Websites till App Service finns i: [Azure App Service och dess påverkan på befintliga Azure-tjänster](http://go.microsoft.com/fwlink/?LinkId=529714)

[0]: ../redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache
[1]: http://bit.ly/1t0KxBQ
[2]: http://manage.windowsazure.com
[3]: http://portal.azure.com
[4]: /powershell/azureps-cmdlets-docs
[5]: /downloads
[6]: http://pecl.php.net
[7]: http://pecl.php.net/package/memcache
[8]: http://blog.syntaxc4.net/post/2015/02/05/how-to-enable-a-site-extension-in-azure-websites.aspx
[9]: http://redis.io/download#installation
[10]: https://social.msdn.microsoft.com/Forums/home?forum=windowsazurewebsitespreview
[11]: http://stackoverflow.com/questions/tagged/azure-web-sites
[12]: /services/cache/
[13]: http://memcached.org



<!--HONumber=Dec16_HO1-->


