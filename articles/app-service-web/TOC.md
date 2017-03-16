# Översikt    
## [Om Web Apps](app-service-web-overview.md)
## [Jämför värdalternativ](choose-web-site-cloud-service-vm.md)

# Snabbstart    
## [Skapa en statisk HTML-plats](app-service-web-get-started-html.md)
## [Skapa ASP.NET-app](app-service-web-get-started-dotnet.md)        
## [Skapa PHP-app](app-service-web-get-started-php.md) 
## [Skapa Node.js-app](app-service-web-get-started-nodejs.md) 
## [Skapa Java-app](app-service-web-get-started-java.md)        
## [Skapa Python-app](app-service-web-get-started-python.md)    

# Exempel
## [Azure CLI](app-service-cli-samples.md) 
## [PowerShell](app-service-powershell-samples.md) 

# Självstudier
## [Lägg till funktioner till en webbapp](app-service-web-get-started-2.md)
## [ASP.NET-app med SQL Database](web-sites-dotnet-get-started.md)
## [Laravel-app med MySQL](app-service-web-php-get-started.md)
## [Sails.js-app med NOSQL-DB](app-service-web-nodejs-sails.md)
## [Java-app med Eclipse](app-service-web-eclipse-create-hello-world-web-app.md)
## [Java-app med IntelliJ](app-service-web-intellij-create-hello-world-web-app.md)
## [Django-app med MySQL](web-sites-python-ptvs-django-mysql.md)

# Koncept
## [Så här fungerar App Service](../app-service/app-service-how-works-readme.md?toc=%2fazure%2fapp-service-web%2ftoc.json)    
## [App Service-planer](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md?toc=%2fazure%2fapp-service-web%2ftoc.json)    
## [Apptjänstmiljöer](app-service-app-service-environment-intro.md)
## [Autentisering och auktorisering](../app-service/app-service-authentication-overview.md?toc=%2fazure%2fapp-service-web%2ftoc.json)
## [Autentisering med lokal AD](web-sites-authentication-authorization.md)


# Instruktionsguider
## Utveckla din app    
### ASP.NET

#### [Utveckla en ASP.NET Core-app med VS Code](web-sites-create-web-app-using-vscode.md)
### PHP
#### [Konfigurera PHP-projekt](web-sites-php-configure.md)
#### [Konfigurera WordPress Multisite](web-sites-php-convert-wordpress-multisite.md) 
### Node.js
#### [Använd io.js](web-sites-nodejs-iojs.md)
#### [Felsök Node.js-app](web-sites-nodejs-debug.md)
### Java

#### [Använd Azure SDK för Java](java-create-azure-website-using-java-sdk.md)
#### [Ladda upp befintlig app](web-sites-java-add-app.md)
#### [Fjärrfelsök Eclipse](app-service-web-debug-java-web-app-in-eclipse.md)
#### [Fjärrfelsök IntelliJ](app-service-web-debug-java-web-app-in-intellij.md)

### [Skicka e-post med SendGrid](sendgrid-dotnet-how-to-send-email.md)

### Konfigurera körning    
#### [PHP på Windows](web-sites-php-configure.md)

#### [Java](web-sites-java-add-app.md)
#### [Node.js på Linux](app-service-linux-using-nodejs-pm2.md)
#### [Python](web-sites-python-configure.md)
            
### Konfigurera program
#### [Använda appinställningar](web-sites-configure.md)
            
## [Distribuera till Azure](web-sites-deploy.md)
### [Distribuera via FTP](app-service-deploy-content-sync.md)
### [Distribuera via molnsynkronisering](web-sites-deploy.md)
### [Distribuera kontinuerligt](app-service-continuous-deployment.md)
### [Distribuera till mellanlagring](web-sites-staged-publishing.md)
### [Distribuera från lokal Git](app-service-deploy-local-git.md)
### [Distribuera med mall](app-service-deploy-complex-application-predictably.md)
### [Flexibel distribution](app-service-agile-software-development.md)
### [Betatestning](app-service-web-test-in-production-controlled-test-flight.md)

### [Ange autentiseringsuppgifter för distribution](app-service-deployment-credentials.md)

### Mappa anpassad domän
#### [Köpa domän](custom-dns-web-site-buydomains-web-app.md)
#### [Mappa tredjepartsdomän](web-sites-custom-domain-name.md)
#### [Mappa domäner med Traffic Manager](web-sites-traffic-manager-custom-domain-name.md)
#### [Mappa GoDaddy-domäner](web-sites-godaddy-custom-domain-name.md)
#### [Migrera en aktiv domän](app-service-custom-domain-name-migrate.md)

### [Migrera från IIS](web-sites-migration-from-iis-server.md)
### [Testa i produktion](app-service-web-test-in-production-get-start.md)
### [Köra prestandatester](app-service-web-app-performance-test.md) 

## Ansluta till DB/resurser        

### [Ansluta till lokala data](web-sites-hybrid-connection-get-started.md) 
### [Ansluta till Azure VNet](web-sites-integrate-with-vnet.md)
### [Ansluta till Azure VNet med PowerShell](app-service-vnet-integration-powershell.md)
### [Ansluta till MongoDB på Azure VM](web-sites-dotnet-store-data-mongodb-vm.md)
            
##Skydda app
### Autentisera användare        
#### [Autentisera med Azure AD](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md?toc=%2fazure%2fapp-service-web%2ftoc.json)
#### [Autentisera med Facebook](../app-service-mobile/app-service-mobile-how-to-configure-facebook-authentication.md?toc=%2fazure%2fapp-service-web%2ftoc.json)
#### [Autentisera med Google](../app-service-mobile/app-service-mobile-how-to-configure-google-authentication.md?toc=%2fazure%2fapp-service-web%2ftoc.json)
#### [Autentisera med Microsoft-konto](../app-service-mobile/app-service-mobile-how-to-configure-microsoft-authentication.md?toc=%2fazure%2fapp-service-web%2ftoc.json)
#### [Autentisera med Twitter](../app-service-mobile/app-service-mobile-how-to-configure-twitter-authentication.md?toc=%2fazure%2fapp-service-web%2ftoc.json)
#### [Autentisera med lokal AD](web-sites-authentication-authorization.md)
#### [App med en databas för flera innehavare](web-sites-dotnet-entity-framework-row-level-security.md)
### Tilldela anpassad SSL
#### [Köpa SSL-cert](web-sites-purchase-ssl-web-site.md)
#### [Konfigurera SSL-cert från tredje part](web-sites-configure-ssl-certificate.md)
### [Använda HTTPS](web-sites-configure-ssl-certificate.md#enforce-https-on-your-app)
### [Konfigurera ömsesidig TLS-autentisering](app-service-web-configure-tls-mutual-auth.md)
            
##Skalningsapp        
### [Skala upp](web-sites-scale.md)
### [Skala ut](../monitoring-and-diagnostics/insights-how-to-scale.md?toc=%2fazure%2fapp-service-web%2ftoc.json)
### [Belastningsutjämna med Traffic Manager](web-sites-traffic-manager.md)
### [Hög skalbarhet med App Service-miljöer](../app-service/app-service-app-service-environments-readme.md?toc=%2fazure%2fapp-service-web%2ftoc.json)
### [Använda Azure CDN för global forskning](cdn-websites-with-cdn.md)
### [Ansluta till Redis Cache via Memcache](web-sites-connect-to-redis-using-memcache-protocol.md)
### [Skapa en Redis-cache](../redis-cache/cache-redis-cache-arm-provision.md?toc=%2fazure%2fapp-service-web%2ftoc.json)
### [Hantera sessionstillstånd med Azure Redis Cache](web-sites-dotnet-session-state-caching.md)

## Övervaka 
### [Övervaka appar](web-sites-monitor.md)
### [Aktivera loggar](web-sites-enable-diagnostic-log.md)
### [Strömningsloggar](web-sites-streaming-logs-and-console.md)

## Säkerhetskopiera innehåll        
### [Säkerhetskopiera din app](web-sites-backup.md)
### [Återställa din app från säkerhetskopia](web-sites-restore.md)
### [Säkerhetskopiera med REST API](websites-csm-backup.md)

## Hantera appresurser
### [Klona app med PowerShell](app-service-web-app-cloning.md)
### [Klona app med portal](app-service-web-app-cloning-portal.md)
### [Flytta resurser](app-service-move-resources.md)
### [Använda Azure Resource Manager med PowerShell](app-service-web-app-azure-resource-manager-powershell.md)
### [Hantera appar med Azure Automation](automation-manage-web-app.md)



# Referens    
## [CLI 2.0](/cli/azure/appservice)
## [PowerShell](/powershell)
## [REST-API](/rest/api/appservice/) 
        
# Resurser    
## Felsökning        
### [Felsöka med Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md)
### [Felsöka Node.js-app](app-service-web-nodejs-best-practices-and-troubleshoot-guide.md)
### [Felsöka HTTP 502 och 503](app-service-web-troubleshoot-http-502-http-503.md)
### [Felsöka prestandaproblem](app-service-web-troubleshoot-performance-degradation.md)
## [Prissättning](https://azure.microsoft.com/pricing/details/app-service/)     
## [Kvotinformation](../azure-subscription-service-limits.md#app-service-limits)    
## [Tjänstuppdateringar och viktig information](https://azure.microsoft.com/updates/?product=app-service)    
## [Bästa praxis](app-service-best-practices.md)
## [Exempel](https://azure.microsoft.com/resources/samples/?service=app-service)    
## [Videoklipp](https://azure.microsoft.com/resources/videos/index/?services=app-service)
## Kokböcker    
### [Referensarkitekturer](../guidance/guidance-ra-app-service.md)    
### [Distributionsskript](https://azure.microsoft.com/documentation/scripts/)    
