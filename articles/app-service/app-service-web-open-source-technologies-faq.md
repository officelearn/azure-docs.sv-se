---
title: "Öppen källkod tekniker vanliga frågor och svar för Azure-webbappar | Microsoft Docs"
description: "Få svar på vanliga frågor och svar om öppen källkod tekniker i funktionen Web Apps i Azure App Service."
services: app-service\web
documentationcenter: 
author: genlin
manager: cshepard
editor: 
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: 4fb443691e216169dd1322b96d77139ffde752d4
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/23/2017
---
# <a name="open-source-technologies-faqs-for-web-apps-in-azure"></a>Öppen källkod tekniker vanliga frågor och svar för Web Apps i Azure

Den här artikeln innehåller svar på vanliga frågor och svar (FAQ) om problem med öppen källkod tekniker för den [funktionen Web Apps i Azure App Service](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="my-cleardb-database-is-down-how-do-i-resolve-this"></a>ClearDB databasen är inte tillgänglig. Hur lösa problemet?

Problem med databasen Kontakta [ClearDB support](https://www.cleardb.com/developers/help/support). 

Svar på vanliga frågor om ClearDB finns [ClearDB vanliga frågor och svar](https://docs.microsoft.com/azure/store-cleardb-faq/).

## <a name="why-wasnt-my-cleardb-database-migrated-during-my-subscription-migration"></a>Varför inte ClearDB databasen migreras under prenumerationsmigreringen min?

Vissa begränsningar gäller när du utför resursmigrering över prenumerationer. En ClearDB MySQL-databas är en tjänst från tredje part och migreras inte under migreringen för en Azure-prenumeration.

Om du inte hantera migreringen av MySQL-databasen innan du migrerar resurserna i Azure, kan ClearDB MySQL-databasen vara otillgänglig. Om du vill undvika detta först manuellt migrera ClearDB-databasen och sedan migrera Azure-prenumeration för din webbapp.

Mer information finns i [vanliga frågor och svar för ClearDB MySQL-databaser med Azure App Service](https://docs.microsoft.com/azure/store-cleardb-faq/).

## <a name="how-do-i-turn-on-php-logging-to-troubleshoot-php-issues"></a>Hur aktiverar jag PHP loggning för att felsöka problem med PHP?

Aktivera PHP loggning:

1. Logga in på ditt [Kudu webbplats](https://*yourwebsitename*.scm.azurewebsites.net).
2. Välj i den översta menyn **Felsökningskonsolen** > **CMD**.
3. Välj den **plats** mapp.
4. Välj den **wwwroot** mapp.
5. Välj den  **+**  ikon och väljer sedan **ny fil**.
6. Ange filnamnet till **. user.ini**.
7. Välj på pennikonen bredvid **. user.ini**.
8. Lägg till den här koden i filen:`log_errors=on`
9. Välj **Spara**.
10. Välj på pennikonen bredvid **wp config.php**.
11. Ändra texten till följande kod:
   ```
   //Enable WP_DEBUG modedefine('WP_DEBUG', true);//Enable debug logging to /wp-content/debug.logdefine('WP_DEBUG_LOG', true);
   //Supress errors and warnings to screendefine('WP_DEBUG_DISPLAY', false);//Supress PHP errors to screenini_set('display_errors', 0);
   ```
12. Starta om din webbapp i Azure-portalen på menyn web app.

Mer information finns i [aktivera WordPress felloggar](https://blogs.msdn.microsoft.com/azureossds/2015/10/09/logging-php-errors-in-wordpress-2/).

## <a name="how-do-i-log-python-application-errors-in-apps-that-are-hosted-in-app-service"></a>Hur loggar Python-programfel i appar som finns i App Service?

Att samla in Python-programfel:

1. Välj i Azure-portalen i ditt webbprogram **inställningar**.
2. På den **inställningar** väljer **programinställningar**.
3. Under **appinställningar**, anger du följande nyckel/värde-par:
    * Nyckel: WSGI_LOG
    * Värde: D:\home\site\wwwroot\logs.txt (Ange önskat filnamn)

Du bör nu se fel i logs.txt-filen i Wwwroot-mappen.

## <a name="how-do-i-change-the-version-of-the-nodejs-application-that-is-hosted-in-app-service"></a>Hur ändrar versionen av Node.js-program som finns i App Service?

Du kan använda något av följande alternativ om du vill ändra versionen av Node.js-program:

*   I Azure-portalen använder **appinställningar**.
    1. Gå till din webbapp i Azure-portalen.
    2. På den **inställningar** bladet väljer **programinställningar**.
    3. I **appinställningar**, du kan inkludera WEBSITE_NODE_DEFAULT_VERSION som nyckeln och versionen av Node.js som du vill använda som värde.
    4. Gå till din [Kudu konsolen](https://*yourwebsitename*.scm.azurewebsites.net).
    5. Ange följande kommando för att kontrollera Node.js-version:  
   ```
   node -v
   ```
*   Ändra filen iisnode.yml. Ändrar Node.js-version i filen iisnode.yml endast anger körningsmiljön där iisnode används. Kudu-cmd och andra fortfarande använda Node.js-version som har angetts i **appinställningar** i Azure-portalen.

    Ange iisnode.yml manuellt genom att skapa en iisnode.yml-fil i rotmappen för din app. Innehåller följande rad i filen:
   ```
   nodeProcessCommandLine: "D:\Program Files (x86)\nodejs\5.9.1\node.exe"
   ```
   
*   Ange filen iisnode.yml med package.json under distributionen av käll-kontroll.
    Distributionsprocessen för Azure källa kontroll omfattar följande steg:
    1. Flyttar innehåll till Azure webbapp.
    2. Skapar en standard-distributionsskriptet, om det inte finns något (deploy.cmd, .deployment-filer) i rotmappen för web app.
    3. Kör ett skript för distribution där den skapar en fil i iisnode.yml om du nämna Node.js-version i package.json-fil > motorn`"engines": {"node": "5.9.1","npm": "3.7.3"}`
    4. Filen iisnode.yml har följande kodrad:
        ```
        nodeProcessCommandLine: "D:\Program Files (x86)\nodejs\5.9.1\node.exe"
        ```

## <a name="i-see-the-message-error-establishing-a-database-connection-in-my-wordpress-app-thats-hosted-in-app-service-how-do-i-troubleshoot-this"></a>Meddelandet ”fel vid upprättande av en databasanslutning” visas i min WordPress-appen i App Service. Hur felsöker jag?

Om du ser felet i Azure WordPress-appen för att aktivera php_errors.log och debug.log, fullständig stegen beskrivs i [aktivera WordPress felloggar](https://blogs.msdn.microsoft.com/azureossds/2015/10/09/logging-php-errors-in-wordpress-2/).

När loggarna har aktiverats kan återskapa felet och sedan kontrollera loggfilerna för att se om du använder out-of-anslutningar:
```
[09-Oct-2015 00:03:13 UTC] PHP Warning: mysqli_real_connect(): (HY000/1226): User ‘abcdefghijk79' has exceeded the ‘max_user_connections’ resource (current value: 4) in D:\home\site\wwwroot\wp-includes\wp-db.php on line 1454
```

Om du ser felet i filerna debug.log eller php_errors.log överstiger appen antalet anslutningar. Om du är värd för ClearDB kontrollera antalet anslutningar som är tillgängliga i din [serviceplan](https://www.cleardb.com/pricing.view).

## <a name="how-do-i-debug-a-nodejs-app-thats-hosted-in-app-service"></a>Hur jag för att felsöka en Node.js-app i App Service?

1.  Gå till din [Kudu konsolen](https://*yourwebsitename*.scm.azurewebsites.net/DebugConsole).
2.  Gå till mappen program loggar (D:\home\LogFiles\Application).
3.  Kontrollera innehållet i filen logging_errors.txt.

## <a name="how-do-i-install-native-python-modules-in-an-app-service-web-app-or-api-app"></a>Hur installera ursprungliga Python-moduler i en App Service webbapp eller API-app?

Vissa paket kan inte installeras med pip i Azure. Paketet kanske inte är tillgängligt på Python Package Index eller kan krävas en kompilator (en kompilator är inte tillgängligt på den dator som kör webbappen i App Service). Information om hur du installerar inbyggda moduler i App Service web apps och API apps finns i [installera Python-moduler i App Service](https://blogs.msdn.microsoft.com/azureossds/2015/06/29/install-native-python-modules-on-azure-web-apps-api-apps/).

## <a name="how-do-i-deploy-a-django-app-to-app-service-by-using-git-and-the-new-version-of-python"></a>Hur distribuerar en Django-app till App Service med hjälp av Git och den nya versionen av Python?

Information om hur du installerar Django finns [distribuerar en Django-appen till App Service](https://blogs.msdn.microsoft.com/azureossds/2016/08/25/deploying-django-app-to-azure-app-services-using-git-and-new-version-of-python/).

## <a name="where-are-the-tomcat-log-files-located"></a>Var finns i loggfilerna för Tomcat?

För Azure Marketplace och anpassade distributioner:

* Mapp: D:\home\site\wwwroot\bin\apache-tomcat-8.0.33\logs
* Filer av intresse:
    * catalina. *åååå-mm-dd*.log
    * värd-hanteraren. *åååå-mm-dd*.log
    * localhost. *åååå-mm-dd*.log
    * Manager. *åååå-mm-dd*.log
    * site_access_log. *åååå-mm-dd*.log


För portalen **appinställningar** distributioner:

* Mapp: D:\home\LogFiles
* Filer av intresse:
    * catalina. *åååå-mm-dd*.log
    * värd-hanteraren. *åååå-mm-dd*.log
    * localhost. *åååå-mm-dd*.log
    * Manager. *åååå-mm-dd*.log
    * site_access_log. *åååå-mm-dd*.log

## <a name="how-do-i-troubleshoot-jdbc-driver-connection-errors"></a>Hur felsöker jag JDBC driver-anslutningsfel

Du kan se följande meddelande i Tomcat-loggar:

```
The web application[ROOT] registered the JDBC driver [com.mysql.jdbc.Driver] but failed to unregister it when the web application was stopped. To prevent a memory leak,the JDBC Driver has been forcibly unregistered
```

Så här löser du felet:

1. Ta bort filen sqljdbc*.jar från din app/lib mapp.
2. Om du använder anpassade Tomcat eller Azure Marketplace Tomcat webbservern, kopiera den här .jar-filen till mappen Tomcat lib.
3. Om du aktiverar Java från Azure-portalen (Välj **Java 1.8** > **Tomcat server**), kopiera sqljdbc.* jar-filen i mappen som är parallellt med din app. Lägg sedan till följande classpath-inställningen i filen web.config:

    ```
    <httpPlatform>
    <environmentVariables>
    <environmentVariablename ="JAVA_OPTS" value=" -Djava.net.preferIPv4Stack=true
    -Xms128M -classpath %CLASSPATH%;[Path to the sqljdbc*.jarfile]" />
    </environmentVariables>
    </httpPlatform>
    ```

## <a name="why-do-i-see-errors-when-i-attempt-to-copy-live-log-files"></a>Varför visas fel när jag försöker live loggfiler ska kopieras?

Om du försöker kopiera live loggfiler för Java-app (t.ex, Tomcat) kan du se den här FTP-fel:

```
Error transferring file [filename] Copying files from remote side failed.
    
The process cannot access the file because it is being used by another process.
```

Felmeddelandet kan variera beroende på FTP-klienten.

Alla Java-appar har problemet låsning. Endast Kudu stöder hämta filen medan programmet körs.

Stoppa appen kan FTP-åtkomst till dessa filer.

En annan lösning är att skriva ett Webbjobb som körs enligt ett schema och kopierar filerna till en annan katalog. Ett exempel på projekt, finns det [CopyLogsJob](https://github.com/kamilsykora/CopyLogsJob) projekt.

## <a name="where-do-i-find-the-log-files-for-jetty"></a>Var hittar jag loggfilerna för Jetty

Loggfilen finns i mappen D:\home\site\wwwroot\bin\jetty-distribution-9.1.2.v20140210\logs för Marketplace och anpassade distributioner. Observera att mappen beror på vilken version av Jetty som du använder. Till exempel sökvägen som angetts här för Jetty 9.1.2. Leta efter jetty_*YYYY_MM_DD*. stderrout.log.

Loggfilen finns i D:\home\LogFiles för portalen Appinställningen distributioner. Leta efter jetty_*YYYY_MM_DD*. stderrout.log

## <a name="can-i-send-email-from-my-azure-web-app"></a>Kan jag skicka e-post från mitt Azure webbapp?

Apptjänst har inte en inbyggda e-funktion. För några bra alternativ för att skicka e-post från din app se den här [Stack Overflow diskussion](http://stackoverflow.com/questions/17666161/sending-email-from-azure).

## <a name="why-does-my-wordpress-site-redirect-to-another-url"></a>Varför min WordPress-webbplats omdirigera till en annan URL?

Om du nyligen har migrerat till Azure, kan WordPress omdirigera till den gamla Webbadressen för domänen. Detta orsakas av en inställning i MySQL-databas.

WordPress-representant + är ett tillägg för Azure plats som du kan använda för att uppdatera omdirigerings-URL direkt i databasen. Mer information om hur du använder WordPress-representant + finns [WordPress verktyg och MySQL-migrering med WordPress-representant +](https://blogs.msdn.microsoft.com/azureossds/2016/12/21/wordpress-tools-and-mysql-migration-with-wordpress-buddy/).

Om du föredrar att manuellt uppdatera omdirigeringen URL med hjälp av SQL-frågor eller PHPMyAdmin, se [WordPress: omdirigering till felaktigt URL](https://blogs.msdn.microsoft.com/azureossds/2016/07/12/wordpress-redirecting-to-wrong-url/).

## <a name="how-do-i-change-my-wordpress-sign-in-password"></a>Hur ändrar lösenordet för WordPress?

Om du har glömt lösenordet för WordPress, kan du använda WordPress-representant + uppdatera den. Installera WordPress representant + Azure plats tillägget om du vill återställa ditt lösenord och slutför stegen som beskrivs i [WordPress verktyg och MySQL-migrering med WordPress-representant +](https://blogs.msdn.microsoft.com/azureossds/2016/12/21/wordpress-tools-and-mysql-migration-with-wordpress-buddy/).

## <a name="i-cant-sign-in-to-wordpress-how-do-i-resolve-this"></a>Jag kan inte logga in på WordPress. Hur lösa problemet?

Om du märker att du har låsts ute från WordPress när du har installerat nyligen ett plugin-program kan kanske du en felaktig plugin-programmet. WordPress-representant + är ett tillägg för Azure plats som kan hjälpa dig att inaktivera plugin-program i WordPress. Mer information finns i [WordPress verktyg och MySQL-migrering med WordPress-representant +](https://blogs.msdn.microsoft.com/azureossds/2016/12/21/wordpress-tools-and-mysql-migration-with-wordpress-buddy/).

## <a name="how-do-i-migrate-my-wordpress-database"></a>Hur migrerar WordPress databasen?

Har du flera alternativ för att migrera MySQL-databas som är ansluten till din WordPress-webbplats:

* Utvecklare: Använd den [kommandotolk eller PHPMyAdmin](https://blogs.msdn.microsoft.com/azureossds/2016/03/02/migrating-data-between-mysql-databases-using-kudu-console-azure-app-service/)
* Icke-utvecklare: Använd [WordPress-representant +](https://blogs.msdn.microsoft.com/azureossds/2016/12/21/wordpress-tools-and-mysql-migration-with-wordpress-buddy/)

## <a name="how-do-i-help-make-wordpress-more-secure"></a>Hur jag gör WordPress säkrare?

Mer information om rekommenderade säkerhetsmetoder för WordPress finns [bästa praxis för WordPress-säkerhet i Azure](https://blogs.msdn.microsoft.com/azureossds/2016/12/26/best-practices-for-wordpress-security-on-azure/).

## <a name="i-am-trying-to-use-phpmyadmin-and-i-see-the-message-access-denied-how-do-i-resolve-this"></a>Jag försöker använda PHPMyAdmin och visas meddelandet ”åtkomst nekad” visas. Hur lösa problemet?

Det här problemet kan uppstå om funktionen app MySQL ännu inte körs i den här Apptjänst-instansen. Lös problemet genom att försöka komma åt din webbplats. Detta startar krävs processer, inklusive MySQL i appen process. Se till att mysqld.exe ingår i processerna för att verifiera att MySQL i appen körs i processen Explorer.

När du har kontrollerat att MySQL app körs, kan du försöka använda PHPMyAdmin.

## <a name="i-get-an-http-403-error-when-i-try-to-import-or-export-my-mysql-in-app-database-by-using-phpmyadmin-how-do-i-resolve-this"></a>Jag får felmeddelandet HTTP 403 när jag försöker importera eller exportera min app MySQL-databas med hjälp av PHPMyadmin. Hur lösa problemet?

Om du använder en äldre version av Chrome, kan som uppstå ett känt fel. Lös problemet genom att uppgradera till en nyare version av Chrome. Också försöka med en annan webbläsare som Internet Explorer eller Edge, där problemet inte uppstår.
