---
title: Vanliga frågor om öppen källkod
description: Få svar på vanliga frågor om tekniker med öppen källkod i Azure App Service.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: 33590f9fc7e6c4d46123cbc7088086a3197d52ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74672433"
---
# <a name="open-source-technologies-faqs-for-web-apps-in-azure"></a>Vanliga frågor om öppen källkod för webbappar i Azure

Den här artikeln innehåller svar på vanliga frågor och svar om problem med tekniker med öppen källkod för [funktionen Webbappar](https://azure.microsoft.com/services/app-service/web/)i Azure App Service .

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="how-do-i-turn-on-php-logging-to-troubleshoot-php-issues"></a>Hur aktiverar jag PHP-loggning för att felsöka PHP-problem?

Så här aktiverar du PHP-loggning:

1. Logga in på din [Kudu-webbplats.](https://*yourwebsitename*.scm.azurewebsites.net)
2. Välj **Felsök konsol** > **CMD**på den övre menyn .
3. Markera mappen **Plats.**
4. Välj **wwwroot-mappen.**
5. Markera **+** ikonen och välj sedan **Ny fil**.
6. Ange filnamnet till **.user.ini**.
7. Välj pennikonen bredvid **.user.ini**.
8. Lägg till den här koden i filen:`log_errors=on`
9. Välj **Spara**.
10. Välj pennikonen bredvid **wp-config.php**.
11. Ändra texten till följande kod:
    ```php
    //Enable WP_DEBUG modedefine('WP_DEBUG', true);//Enable debug logging to /wp-content/debug.logdefine('WP_DEBUG_LOG', true);
    //Suppress errors and warnings to screendefine('WP_DEBUG_DISPLAY', false);//Suppress PHP errors to screenini_set('display_errors', 0);
    ```
12. Starta om webbappen på webbapp-menyn i Azure-portalen.

Mer information finns i [Aktivera WordPress-felloggar](https://blogs.msdn.microsoft.com/azureossds/2015/10/09/logging-php-errors-in-wordpress-2/).

## <a name="how-do-i-log-python-application-errors-in-apps-that-are-hosted-in-app-service"></a>Hur loggar jag Python-programfel i appar som finns i App Service?
[!INCLUDE [web-sites-python-troubleshooting-wsgi-error-log](../../includes/web-sites-python-troubleshooting-wsgi-error-log.md)]

## <a name="how-do-i-change-the-version-of-the-nodejs-application-that-is-hosted-in-app-service"></a>Hur ändrar jag versionen av nod.js-programmet som finns i App Service?

Om du vill ändra versionen av nod.js-programmet kan du använda något av följande alternativ:

* Använd **App-inställningar**i Azure-portalen .
  1. Gå till din webbapp i Azure-portalen.
  2. Välj **Programinställningar**på bladet **Inställningar** .
  3. I **Appinställningar**kan du inkludera WEBSITE_NODE_DEFAULT_VERSION som nyckel och den version av Node.js du vill ha som värde.
  4. Gå till din [Kudu-konsol](https://*yourwebsitename*.scm.azurewebsites.net).
  5. Om du vill kontrollera nod.js-versionen anger du följande kommando:  
     ```
     node -v
     ```
* Ändra filen iisnode.yml. Om du ändrar nod.js-versionen i filen iisnode.yml anges bara körningsmiljön som iisnode använder. Din Kudu-cmd och andra använder fortfarande nod.js-versionen som anges i **appinställningarna** i Azure-portalen.

  Om du vill ange iisnode.yml manuellt skapar du en iisnode.yml-fil i appens rotmapp. I filen ska du inkludera följande rad:
  ```yml
  nodeProcessCommandLine: "D:\Program Files (x86)\nodejs\5.9.1\node.exe"
  ```
   
* Ange filen iisnode.yml med package.json under distribution av källkontroll.
  Distributionen av Azure-källkontroll omfattar följande steg:
  1. Flyttar innehåll till Azure-webbappen.
  2. Skapar ett standarddistributionsskript om det inte finns ett (deploy.cmd, .deployment files) i webbapprotmappen.
  3. Kör ett distributionsskript där en iisnode.yml-fil skapas om du nämner node.js-versionen i filen package.json >-motorn`"engines": {"node": "5.9.1","npm": "3.7.3"}`
  4. Filen iisnode.yml har följande kodrad:
      ```yml
      nodeProcessCommandLine: "D:\Program Files (x86)\nodejs\5.9.1\node.exe"
      ```

## <a name="i-see-the-message-error-establishing-a-database-connection-in-my-wordpress-app-thats-hosted-in-app-service-how-do-i-troubleshoot-this"></a>Jag ser meddelandet "Fel att upprätta en databasanslutning" i min WordPress-app som finns i App Service. Hur felsöker jag detta?

Om du ser det här felet i din Azure WordPress-app, för att aktivera php_errors.log och debug.log, slutför du stegen som beskrivs i [Aktivera WordPress-felloggar](https://blogs.msdn.microsoft.com/azureossds/2015/10/09/logging-php-errors-in-wordpress-2/).

När loggarna är aktiverade återskapar du felet och kontrollerar sedan loggarna för att se om du har på anslutningar:
```
[09-Oct-2015 00:03:13 UTC] PHP Warning: mysqli_real_connect(): (HY000/1226): User ‘abcdefghijk79' has exceeded the ‘max_user_connections’ resource (current value: 4) in D:\home\site\wwwroot\wp-includes\wp-db.php on line 1454
```

Om det här felet visas i filerna debug.log eller php_errors.log överskrider appen antalet anslutningar. Om du är värd för ClearDB kontrollerar du antalet anslutningar som är tillgängliga i [serviceplanen](https://www.cleardb.com/pricing.view).

## <a name="how-do-i-debug-a-nodejs-app-thats-hosted-in-app-service"></a>Hur felsöker jag en Node.js-app som finns i App Service?

1.  Gå till din [Kudu-konsol](https://*yourwebsitename*.scm.azurewebsites.net/DebugConsole).
2.  Gå till mappen programloggar (D:\home\LogFiles\Application).
3.  Kontrollera om det finns innehåll i filen logging_errors.txt.

## <a name="how-do-i-install-native-python-modules-in-an-app-service-web-app-or-api-app"></a>Hur installerar jag inbyggda Python-moduler i en App Service-webbapp eller API-app?

Vissa paket kanske inte installeras med pip i Azure. Paketet kanske inte är tillgängligt på Python Package Index, eller så kan en kompilator krävas (en kompilator är inte tillgänglig på datorn som kör webbappen i App Service). Information om hur du installerar inbyggda moduler i App Service-webbappar och API-appar finns [i Installera Python-moduler i App Service](https://blogs.msdn.microsoft.com/azureossds/2015/06/29/install-native-python-modules-on-azure-web-apps-api-apps/).

## <a name="how-do-i-deploy-a-django-app-to-app-service-by-using-git-and-the-new-version-of-python"></a>Hur distribuerar jag en Django-app till App Service med Git och den nya versionen av Python?

Information om hur du installerar Django finns i [Distribuera en Django-app till App Service](https://blogs.msdn.microsoft.com/azureossds/2016/08/25/deploying-django-app-to-azure-app-services-using-git-and-new-version-of-python/).

## <a name="where-are-the-tomcat-log-files-located"></a>Var finns Tomcat-loggfilerna?

För Azure Marketplace och anpassade distributioner:

* Mappplats: D:\home\site\wwwroot\bin\apache-tomcat-8.0.33\logs
* Filer av intresse:
    * Catalina. *yyy-mm-dd*.log
    * värdansvarig. *yyy-mm-dd*.log
    * Localhost. *yyy-mm-dd*.log
    * Manager. *yyy-mm-dd*.log
    * site_access_log. *yyy-mm-dd*.log


För **distributioner av portalappinställningar:**

* Mappplats: D:\home\LogFiles
* Filer av intresse:
    * Catalina. *yyy-mm-dd*.log
    * värdansvarig. *yyy-mm-dd*.log
    * Localhost. *yyy-mm-dd*.log
    * Manager. *yyy-mm-dd*.log
    * site_access_log. *yyy-mm-dd*.log

## <a name="how-do-i-troubleshoot-jdbc-driver-connection-errors"></a>Hur felsöker jag anslutningsfel för JDBC-drivrutiner?

Följande meddelande kan visas i Tomcat-loggarna:

```
The web application[ROOT] registered the JDBC driver [com.mysql.jdbc.Driver] but failed to unregister it when the web application was stopped. To prevent a memory leak,the JDBC Driver has been forcibly unregistered
```

Så här löser du felet:

1. Ta bort filen sqljdbc*.jar från mappen app/lib.
2. Om du använder den anpassade Tomcat- eller Azure Marketplace Tomcat-webbservern kopierar du den här JAR-filen till mappen Tomcat lib.
3. Om du aktiverar Java från Azure-portalen (välj **Java 1.8** > **Tomcat-server**) kopierar du sqljdbc.*-jarfilen i mappen som är parallell med appen. Lägg sedan till följande klasssökvägsinställning i filen web.config:

    ```xml
    <httpPlatform>
    <environmentVariables>
    <environmentVariablename ="JAVA_OPTS" value=" -Djava.net.preferIPv4Stack=true
    -Xms128M -classpath %CLASSPATH%;[Path to the sqljdbc*.jarfile]" />
    </environmentVariables>
    </httpPlatform>
    ```

## <a name="why-do-i-see-errors-when-i-attempt-to-copy-live-log-files"></a>Varför visas fel när jag försöker kopiera loggfiler?

Om du försöker kopiera liveloggfiler för en Java-app (till exempel Tomcat) kan det här FTP-felet visas:

```
Error transferring file [filename] Copying files from remote side failed.
    
The process cannot access the file because it is being used by another process.
```

Felmeddelandet kan variera beroende på FTP-klienten.

Alla Java-appar har det här låsproblemet. Endast Kudu stöder nedladdning av den här filen medan appen körs.

Om du stoppar appen kan FTP-åtkomst till dessa filer.

En annan lösning är att skriva ett WebJob som körs enligt ett schema och kopierar dessa filer till en annan katalog. Ett exempelprojekt finns i [CopyLogsJob-projektet.](https://github.com/kamilsykora/CopyLogsJob)

## <a name="where-do-i-find-the-log-files-for-jetty"></a>Var hittar jag loggfilerna för Brygga?

För Marketplace och anpassade distributioner finns loggfilen i mappen D:\home\site\wwwroot\bin\jetty-distribution-9.1.2.v20140210\logs. Observera att mappplatsen beror på vilken version av Brygga du använder. Till exempel är den väg som anges här för Brygga 9.1.2. Leta efter jetty_*YYYY_MM_DD*.stderrout.log.

För distributioner av portalappinställningar finns loggfilen i D:\home\LogFiles. Leta efter jetty_*YYYY_MM_DD*.stderrout.log

## <a name="can-i-send-email-from-my-azure-web-app"></a>Kan jag skicka e-post från min Azure-webbapp?

App Service har ingen inbyggd e-postfunktion. Några bra alternativ för att skicka e-post från din app finns i den här [diskussionen om stackspill.](https://stackoverflow.com/questions/17666161/sending-email-from-azure)

## <a name="why-does-my-wordpress-site-redirect-to-another-url"></a>Varför omdirigeras min WordPress-webbplats till en annan webbadress?

Om du nyligen har migrerat till Azure kan WordPress omdirigera till den gamla domänadressen. Detta orsakas av en inställning i MySQL-databasen.

WordPress Buddy+ är ett Azure-webbplatstillägg som du kan använda för att uppdatera omdirigeringsadressen direkt i databasen. Mer information om hur du använder WordPress Buddy+, se [WordPress-verktyg och MySQL-migrering med WordPress Buddy+](https://sharepointforum.org/threads/wordpress-tools-and-mysql-migration-with-wordpress-buddy.82929/).

Alternativt, om du föredrar att manuellt uppdatera omdirigering URL med hjälp av SQL-frågor eller PHPMyAdmin, se [WordPress: Omdirigera till fel URL](https://blogs.msdn.microsoft.com/azureossds/2016/07/12/wordpress-redirecting-to-wrong-url/).

## <a name="how-do-i-change-my-wordpress-sign-in-password"></a>Hur ändrar jag mitt WordPress-inloggningslösenord?

Om du har glömt ditt WordPress-inloggningslösenord kan du använda WordPress Buddy+ för att uppdatera det. Om du vill återställa ditt lösenord installerar du Site Extension för WordPress Buddy+ Azure och slutför sedan stegen som beskrivs i [WordPress-verktyg och MySQL-migrering med WordPress Buddy+](https://sharepointforum.org/threads/wordpress-tools-and-mysql-migration-with-wordpress-buddy.82929/).

## <a name="i-cant-sign-in-to-wordpress-how-do-i-resolve-this"></a>Jag kan inte logga in på WordPress. Hur gör jag för att lösa det?

Om du befinner dig utelåst från WordPress efter nyligen installera en plugin, kan du ha en felaktig plugin. WordPress Buddy + är en Azure Site Extension som kan hjälpa dig att inaktivera plugins i WordPress. Mer information finns i [WordPress-verktyg och MySQL-migrering med WordPress Buddy+](https://sharepointforum.org/threads/wordpress-tools-and-mysql-migration-with-wordpress-buddy.82929/).

## <a name="how-do-i-migrate-my-wordpress-database"></a>Hur migrerar jag min WordPress-databas?

Du har flera alternativ för att migrera MySQL-databasen som är ansluten till din WordPress-webbplats:

* Utvecklare: Använd [kommandotolken eller PHPMyAdmin](https://blogs.msdn.microsoft.com/azureossds/2016/03/02/migrating-data-between-mysql-databases-using-kudu-console-azure-app-service/)
* Icke-utvecklare: Använd [WordPress Buddy+](https://sharepointforum.org/threads/wordpress-tools-and-mysql-migration-with-wordpress-buddy.82929/)

## <a name="how-do-i-help-make-wordpress-more-secure"></a>Hur hjälper jag till att göra WordPress säkrare?

Mer information om metodtips för säkerhet för WordPress finns [i Metodtips för WordPress-säkerhet i Azure](https://blogs.msdn.microsoft.com/azureossds/2016/12/26/best-practices-for-wordpress-security-on-azure/).

## <a name="i-am-trying-to-use-phpmyadmin-and-i-see-the-message-access-denied-how-do-i-resolve-this"></a>Jag försöker använda PHPMyAdmin, och jag ser meddelandet "Access nekas." Hur gör jag för att lösa det?

Det här problemet kan uppstå om MySQL-funktionen i appen inte körs ännu i den här App Service-instansen. Försök att komma åt din webbplats för att lösa problemet. Detta startar de processer som krävs, inklusive MySQL-processen i appen. Kontrollera att MySQL i appen körs i Process Explorer och se till att mysqld.exe visas i processerna.

När du har försäkrat att MySQL i appen körs kan du försöka använda PHPMyAdmin.

## <a name="i-get-an-http-403-error-when-i-try-to-import-or-export-my-mysql-in-app-database-by-using-phpmyadmin-how-do-i-resolve-this"></a>Jag får ett HTTP 403-fel när jag försöker importera eller exportera min MySQL-databas i appen med PHPMyadmin. Hur gör jag för att lösa det?

Om du använder en äldre version av Chrome kan det bero på ett känt fel. Lös problemet genom att uppgradera till en nyare version av Chrome. Prova också att använda en annan webbläsare, till exempel Internet Explorer eller Microsoft Edge, där problemet inte uppstår.
