---
title: Tekniker med öppen källkod vanliga frågor och svar - Azure App Service | Microsoft Docs
description: Få svar på vanliga frågor och svar om tekniker för öppen källkod i funktionen Web Apps i Azure App Service.
services: app-service\web
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: 07912dab52cb0569428d070282551eebbdb1c7bc
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2019
ms.locfileid: "54191453"
---
# <a name="open-source-technologies-faqs-for-web-apps-in-azure"></a>Tekniker med öppen källkod frågor och svar om Web Apps i Azure

Den här artikeln innehåller svar på vanliga frågor och svar (FAQ) om kända problem med tekniker för öppen källkod för den [Web Apps-funktionen i Azure App Service](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="how-do-i-turn-on-php-logging-to-troubleshoot-php-issues"></a>Hur aktiverar jag loggning för att felsöka problem med PHP PHP?

Aktivera loggning för PHP:

1. Logga in på din [Kudu-webbplatsen](https://*yourwebsitename*.scm.azurewebsites.net).
2. I menyn högst upp väljer **Felsökningskonsolen** > **CMD**.
3. Välj den **plats** mapp.
4. Välj den **wwwroot** mapp.
5. Välj den **+** ikonen och välj sedan **ny fil**.
6. Ange namnet på filen **. user.ini**.
7. Välj pennikonen bredvid **. user.ini**.
8. Lägg till den här koden i filen: `log_errors=on`
9. Välj **Spara**.
10. Välj pennikonen bredvid **wp-config.php**.
11. Ändra texten till följande kod:
   ```php
   //Enable WP_DEBUG modedefine('WP_DEBUG', true);//Enable debug logging to /wp-content/debug.logdefine('WP_DEBUG_LOG', true);
   //Suppress errors and warnings to screendefine('WP_DEBUG_DISPLAY', false);//Suppress PHP errors to screenini_set('display_errors', 0);
   ```
12. Starta om din webbapp i Azure-portalen via menyn web app.

Mer information finns i [felloggning i WordPress aktivera](https://blogs.msdn.microsoft.com/azureossds/2015/10/09/logging-php-errors-in-wordpress-2/).

## <a name="how-do-i-log-python-application-errors-in-apps-that-are-hosted-in-app-service"></a>Hur loggar jag Python programfel i appar som finns i App Service?
[!INCLUDE [web-sites-python-troubleshooting-wsgi-error-log](../../includes/web-sites-python-troubleshooting-wsgi-error-log.md)]

## <a name="how-do-i-change-the-version-of-the-nodejs-application-that-is-hosted-in-app-service"></a>Hur ändrar jag versionen av Node.js-program som är värd för App Service?

Om du vill ändra versionen av Node.js-program, kan du använda något av följande alternativ:

*   I Azure-portalen använder **appinställningar**.
    1. Gå till din webbapp i Azure-portalen.
    2. På den **inställningar** bladet väljer **programinställningar**.
    3. I **appinställningar**, du kan inkludera WEBSITE_NODE_DEFAULT_VERSION som nyckeln och versionen av Node.js som du vill ha som värdet.
    4. Gå till din [Kudu-konsolen](https://*yourwebsitename*.scm.azurewebsites.net).
    5. Ange följande kommando för att kontrollera Node.js-version:  
   ```
   node -v
   ```
*   Ändra iisnode.yml-filen. Ändra Node.js-version i filen iisnode.yml endast anger runtime-miljö där iisnode använder. Kudu-cmd och andra fortfarande använda Node.js-version som anges i **appinställningar** i Azure-portalen.

    Ange iisnode.yml manuellt genom att skapa en iisnode.yml-fil i rotmappen för din app. Skriver du följande i filen:
   ```yml
   nodeProcessCommandLine: "D:\Program Files (x86)\nodejs\5.9.1\node.exe"
   ```
   
*   Ange filen iisnode.yml package.json under distributionen av käll-kontroll.
    Distributionsprocessen för Azure källa kontroll omfattar följande steg:
    1. Flyttar innehåll till Azure-webbappen.
    2. Skapar en standard-distributionsskriptet, om det inte finns något (deploy.cmd, .deployment filer) i rotmappen för web app.
    3. Kör ett distributionsskript där den skapar en iisnode.yml-fil om du nämner Node.js-version i package.json-fil > motor `"engines": {"node": "5.9.1","npm": "3.7.3"}`
    4. Iisnode.yml-filen har följande rad med kod:
        ```yml
        nodeProcessCommandLine: "D:\Program Files (x86)\nodejs\5.9.1\node.exe"
        ```

## <a name="i-see-the-message-error-establishing-a-database-connection-in-my-wordpress-app-thats-hosted-in-app-service-how-do-i-troubleshoot-this"></a>Meddelandet ”Error upprätta en databasanslutning” visas i min WordPress-app som är värd för App Service. Hur felsöker jag?

Om du ser det här felet i din Azure WordPress-app för att möjliggöra php_errors.log och felsökningsloggen, fullständig stegen beskrivs i [felloggning i WordPress aktivera](https://blogs.msdn.microsoft.com/azureossds/2015/10/09/logging-php-errors-in-wordpress-2/).

När loggarna är aktiverade, återskapa felet och sedan kontrollera loggarna för att se om du kör utan anslutningar:
```
[09-Oct-2015 00:03:13 UTC] PHP Warning: mysqli_real_connect(): (HY000/1226): User ‘abcdefghijk79' has exceeded the ‘max_user_connections’ resource (current value: 4) in D:\home\site\wwwroot\wp-includes\wp-db.php on line 1454
```

Om du ser detta fel i felsökningsloggen eller php_errors.log filerna är din app överstiger antalet anslutningar. Om du är värd på ClearDB, verifiera antalet anslutningar som är tillgängliga i din [service-plan](https://www.cleardb.com/pricing.view).

## <a name="how-do-i-debug-a-nodejs-app-thats-hosted-in-app-service"></a>Hur jag för att felsöka en Node.js-app som är värd för App Service?

1.  Gå till din [Kudu-konsolen](https://*yourwebsitename*.scm.azurewebsites.net/DebugConsole).
2.  Gå till programmappen loggar (D:\home\LogFiles\Application).
3.  Sök efter innehåll i filen logging_errors.txt.

## <a name="how-do-i-install-native-python-modules-in-an-app-service-web-app-or-api-app"></a>Hur installerar jag inbyggda Python-moduler i en App Service webbapp eller API-app?

Vissa paket kanske inte installeras med hjälp av pip i Azure. Paketet är kanske inte tillgänglig på Python Package Index eller kan det krävas en kompilator (en kompilator är inte tillgänglig på den dator som kör webbappen i App Service). Information om hur du installerar inbyggda moduler i App Service web apps och API apps finns i [installera Python-moduler i App Service](https://blogs.msdn.microsoft.com/azureossds/2015/06/29/install-native-python-modules-on-azure-web-apps-api-apps/).

## <a name="how-do-i-deploy-a-django-app-to-app-service-by-using-git-and-the-new-version-of-python"></a>Hur distribuerar jag en Django-app till App Service med hjälp av Git och den nya versionen av Python?

Information om hur du installerar Django finns i [distribuera en Django-app till App Service](https://blogs.msdn.microsoft.com/azureossds/2016/08/25/deploying-django-app-to-azure-app-services-using-git-and-new-version-of-python/).

## <a name="where-are-the-tomcat-log-files-located"></a>Var finns loggfilerna Tomcat?

För Azure Marketplace och anpassade distributioner:

* Plats för mappen: D:\home\site\wwwroot\bin\apache-Tomcat-8.0.33\logs
* Filer av intresse:
    * catalina. *åååå-mm-dd*.log
    * host-manager.*yyyy-mm-dd*.log
    * localhost.*yyyy-mm-dd*.log
    * Manager. *åååå-mm-dd*.log
    * site_access_log. *åååå-mm-dd*.log


För portalen **appinställningar** distributioner:

* Plats för mappen: D:\home\LogFiles
* Filer av intresse:
    * catalina. *åååå-mm-dd*.log
    * host-manager.*yyyy-mm-dd*.log
    * localhost.*yyyy-mm-dd*.log
    * Manager. *åååå-mm-dd*.log
    * site_access_log. *åååå-mm-dd*.log

## <a name="how-do-i-troubleshoot-jdbc-driver-connection-errors"></a>Hur felsöker jag fel med JDBC driver?

Du kan se följande meddelande i loggarna Tomcat:

```
The web application[ROOT] registered the JDBC driver [com.mysql.jdbc.Driver] but failed to unregister it when the web application was stopped. To prevent a memory leak,the JDBC Driver has been forcibly unregistered
```

Att lösa problemet:

1. Ta bort filen sqljdbc*.jar från din app/lib-mappen.
2. Om du använder anpassade Tomcat eller Azure Marketplace Tomcat webbservern kan du kopiera .jar-filen till Tomcat lib-mappen.
3. Om du aktiverar Java i Azure Portal (Välj **Java 1.8** > **Tomcat-servern**), kopiera sqljdbc.* jar-filen i mappen som är parallella till din app. Lägg sedan till följande classpath-inställningen i filen web.config:

    ```xml
    <httpPlatform>
    <environmentVariables>
    <environmentVariablename ="JAVA_OPTS" value=" -Djava.net.preferIPv4Stack=true
    -Xms128M -classpath %CLASSPATH%;[Path to the sqljdbc*.jarfile]" />
    </environmentVariables>
    </httpPlatform>
    ```

## <a name="why-do-i-see-errors-when-i-attempt-to-copy-live-log-files"></a>Varför visas fel när jag försöker kopiera live loggfiler?

Om du försöker kopiera live loggfilerna för en Java-app (t.ex, Tomcat), visas den här FTP-fel:

```
Error transferring file [filename] Copying files from remote side failed.
    
The process cannot access the file because it is being used by another process.
```

Felmeddelandet kan variera beroende på FTP-klienten.

Alla Java-appar har problemet låsning. Endast Kudu stöder ladda ned den här filen medan appen körs.

Stoppar appen gör att FTP-åtkomst till dessa filer.

En annan lösning är att skriva ett Webbjobb som körs enligt ett schema och kopierar filerna till en annan katalog. Exempelprojektet, se den [CopyLogsJob](https://github.com/kamilsykora/CopyLogsJob) projekt.

## <a name="where-do-i-find-the-log-files-for-jetty"></a>Var hittar jag loggfilerna för Jetty?

Loggfilen finns i mappen D:\home\site\wwwroot\bin\jetty-distribution-9.1.2.v20140210\logs för anpassade distributioner och Marketplace. Observera att mappens plats beror på vilken version av Jetty som du använder. Till exempel syftar sökvägen för Jetty 9.1.2. Leta efter jetty_*YYYY_MM_DD*. stderrout.log.

Loggfilen finns i D:\home\LogFiles för portalen Appinställningen-distributioner. Leta efter jetty_*YYYY_MM_DD*. stderrout.log

## <a name="can-i-send-email-from-my-azure-web-app"></a>Kan jag skicka e-post från min Azure-webbapp?

App Service har inte en inbyggd e-post-funktion. Några bra alternativ för att skicka e-post från din app, finns det [Stack Overflow diskussion](https://stackoverflow.com/questions/17666161/sending-email-from-azure).

## <a name="why-does-my-wordpress-site-redirect-to-another-url"></a>Varför min WordPress-webbplats omdirigera till en annan URL?

Om du nyligen har migrerat till Azure, kan WordPress omdirigera till den gamla Webbadressen för domänen. Detta orsakas av en inställning i MySQL-databasen.

WordPress-representant + är ett Webbplatstillägg för Azure som du kan använda för att uppdatera omdirigerings-URL direkt i databasen. Läs mer om hur du använder WordPress-representant + [WordPress verktyg och MySQL-migrering med WordPress-representant +](https://blogs.msdn.microsoft.com/azureossds/2016/12/21/wordpress-tools-and-mysql-migration-with-wordpress-buddy/).

Om du föredrar att manuellt uppdatera omdirigeringen URL med hjälp av SQL-frågor eller PHPMyAdmin, se [WordPress: Omdirigera till fel URL](https://blogs.msdn.microsoft.com/azureossds/2016/07/12/wordpress-redirecting-to-wrong-url/).

## <a name="how-do-i-change-my-wordpress-sign-in-password"></a>Hur jag för att ändra lösenordet för WordPress?

Om du har glömt lösenordet för WordPress, kan du använda WordPress-representant + för att uppdatera den. Installera WordPress representant + Azure-Platstillägget för att återställa ditt lösenord och slutför stegen som beskrivs i [WordPress verktyg och MySQL-migrering med WordPress-representant +](https://blogs.msdn.microsoft.com/azureossds/2016/12/21/wordpress-tools-and-mysql-migration-with-wordpress-buddy/).

## <a name="i-cant-sign-in-to-wordpress-how-do-i-resolve-this"></a>Jag kan inte logga in till WordPress. Hur löser jag det?

Om du märker att du har utelåst från WordPress när du har nyligen har installerat ett plugin-program kan kanske du ett felaktiga plugin-program. WordPress-representant + är ett tillägg för Azure Site som kan hjälpa dig att inaktivera plugin-program i WordPress. Mer information finns i [WordPress verktyg och MySQL-migrering med WordPress-representant +](https://blogs.msdn.microsoft.com/azureossds/2016/12/21/wordpress-tools-and-mysql-migration-with-wordpress-buddy/).

## <a name="how-do-i-migrate-my-wordpress-database"></a>Hur migrerar jag min WordPress-databas?

Du har flera alternativ för att migrera MySQL-databas som är ansluten till din WordPress-webbplats:

* Utvecklare: Använd den [Kommandotolken eller PHPMyAdmin](https://blogs.msdn.microsoft.com/azureossds/2016/03/02/migrating-data-between-mysql-databases-using-kudu-console-azure-app-service/)
* Icke-utvecklare: Använd [WordPress-representant +](https://blogs.msdn.microsoft.com/azureossds/2016/12/21/wordpress-tools-and-mysql-migration-with-wordpress-buddy/)

## <a name="how-do-i-help-make-wordpress-more-secure"></a>Hur jag gör WordPress säkrare?

Mer information om rekommenderade säkerhetsmetoder för WordPress finns [bästa praxis för WordPress-säkerhet i Azure](https://blogs.msdn.microsoft.com/azureossds/2016/12/26/best-practices-for-wordpress-security-on-azure/).

## <a name="i-am-trying-to-use-phpmyadmin-and-i-see-the-message-access-denied-how-do-i-resolve-this"></a>Jag försöker använda PHPMyAdmin och jag ser meddelandet ”åtkomst nekad” visas. Hur löser jag det?

Du kan använda det här problemet om funktionen MySQL via app ännu inte körs i den här App Service-instansen. Lös problemet genom att försöka komma åt din webbplats. Nödvändiga processer, inklusive hur MySQL i appen startas. Se till att mysqld.exe visas i processerna för att verifiera att MySQL via app körs i processen Explorer.

När du har kontrollerat att MySQL via app körs, försök använda PHPMyAdmin.

## <a name="i-get-an-http-403-error-when-i-try-to-import-or-export-my-mysql-in-app-database-by-using-phpmyadmin-how-do-i-resolve-this"></a>Jag får ett HTTP 403-fel när jag försöker importera eller exportera min databas för MySQL-app via PHPMyadmin. Hur löser jag det?

Om du använder en äldre version av Chrome, kan som uppstå ett känt fel. Lös problemet genom att uppgradera till en nyare version av Chrome. Prova också med en annan webbläsare som Internet Explorer eller Microsoft Edge, där problemet inte uppstår.
