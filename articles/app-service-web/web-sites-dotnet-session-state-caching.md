---
title: Sessionstillstånd med Azure Redis-cache i Azure App Service
description: Lär dig hur du använder Azure Cache-tjänsten till att hantera cachelagring av ASP.NET-sessionstillstånd.
services: app-service\web
documentationcenter: .net
author: Rick-Anderson
manager: wpickett
editor: none

ms.service: app-service-web
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: get-started-article
ms.date: 06/27/2016
ms.author: riande

---
# Sessionstillstånd med Azure Redis-cache i Azure App Service
I det här avsnittet får du veta hur du använder tjänsten Azure Redis-cache för sessionstillstånd.

Om din ASP.NET-webbapp använder sessionstillstånd måste du konfigurera en extern sessionstillståndsprovider (tjänsten Redis-cache eller en sessionstillståndsprovider för SQL Server). Om du använder sessionstillstånd och inte använder en extern provider begränsas du till en instans av webbappen. Tjänsten Redis-cache är den som det går snabbast och enklast att aktivera.

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a id="createcache"></a>Skapa cachen
Skapa cachen genom att följa [de här anvisningarna](../redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md#create-cache).

## <a id="configureproject"></a>Lägga till NuGet-paketet RedisSessionStateProvider till webbappen
Installera NuGet-paketet `RedisSessionStateProvider`.  Installera från pakethanterarkonsolen med hjälp av följande kommando (**Tools** > **NuGet Package Manager** > **Package Manager Console**) (Verktyg, NuGet-pakethanteraren, Pakethanterarkonsol):

  `PM> Install-Package Microsoft.Web.RedisSessionStateProvider`

Om du vill installera från **Tools** > **NuGet Package Manager** > **Manage NugGet Packages for Solution** (Verktyg, NuGet-pakethanteraren, Hantera NuGet-paket för lösning) söker du efter `RedisSessionStateProvider`.

Mer information finns i [NuGet RedisSessionStateProvider-sidan](http://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider/) och [Konfigurera cacheklienten](../redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md#NuGet).

## <a id="configurewebconfig"></a>Ändra filen web.config
Med NuGet-paketet skapas inte bara sammansättningsreferenser för cachen, med det läggs också stub-poster till i filen *web.config*. 

1. Öppna *web.config* och leta reda på elementet **sessionState**.
2. Ange värden för `host`, `accessKey` och `port` (SSL-porten ska vara 6380), och ange `SSL` till `true`. Du kan hämta de här värdena från bladet för [Azure Portal](http://go.microsoft.com/fwlink/?LinkId=529715) för cacheinstansen. Mer information finns i [Ansluta till cacheminnet](../redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-cache). Observera att icke-SSL-porten är inaktiverad som standard för nya cacheminnen. Mer information om hur du aktiverar icke-SSL-porten finns i avsnittet [Åtkomstportar](https://msdn.microsoft.com/library/azure/dn793612.aspx#AccessPorts) i artikeln [Konfigurera en cache i Azure Redis-cache](https://msdn.microsoft.com/library/azure/dn793612.aspx). Följande markering visar ändringarna i filen *web.config*, särskilt ändringarna av *port*, *värd*, åtkomstnyckel* och *ssl *.
   
          <system.web>;
            <customErrors mode="Off" />;
            <authentication mode="None" />;
            <compilation debug="true" targetFramework="4.5" />;
            <httpRuntime targetFramework="4.5" />;
            <sessionState mode="Custom" customProvider="RedisSessionProvider">;
              <providers>;  
                  <!--<add name="RedisSessionProvider" 
                    host = "127.0.0.1" [String]
                    port = "" [number]
                    accessKey = "" [String]
                    ssl = "false" [true|false]
                    throwOnError = "true" [true|false]
                    retryTimeoutInMilliseconds = "0" [number]
                    databaseId = "0" [number]
                    applicationName = "" [String]
                  />;-->;
                 <add name="RedisSessionProvider" 
                      type="Microsoft.Web.Redis.RedisSessionStateProvider" 
                      port="6380"
                      host="movie2.redis.cache.windows.net" 
                      accessKey="m7PNV60CrvKpLqMUxosC3dSe6kx9nQ6jP5del8TmADk=" 
                      ssl="true" />;
              <!--<add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider" host="127.0.0.1" accessKey="" ssl="false" />;-->;
              </providers>;
            </sessionState>;
          </system.web>;

## <a id="usesessionobject"></a> Använda sessionsobjektet i kod
Det sista steget är att börja använda sessionsobjektet i ASP.NET-koden. Du lägger till objekt till sessionstillstånd med hjälp av metoden **Session.Add**. I den här metoden används nyckel/värde-par till att lagra objekt i sessionstillståndscachen.

    string strValue = "yourvalue";
    Session.Add("yourkey", strValue);

Följande kod hämtar värdet från sessionstillstånd.

    object objValue = Session["yourkey"];
    if (objValue != null)
       strValue = (string)objValue; 

Du kan också använda Redis-cache för cacheobjekt i webbappen. Mer information finns i [MVC-filmapp med Azure Redis-cache på 15 minuter](https://azure.microsoft.com/blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/).
Mer information om hur du använder sessionstillståndet ASP.NET finns i [Översikt över sessionstillståndet ASP.NET][Översikt över sessionstillståndet ASP.NET].

> [!NOTE]
> Om du vill komma igång med Azure App Service innan du registrerar dig för ett Azure-konto kan du gå till [Prova App Service](http://go.microsoft.com/fwlink/?LinkId=523751). Där kan du direkt skapa en tillfällig startwebbapp i App Service. Inget kreditkort krävs, och du gör inga åtaganden.
> 
> 

## Nyheter
* En guide till övergången från Webbplatser till App Service finns i: [Azure App Service och dess påverkan på befintliga Azure-tjänster](http://go.microsoft.com/fwlink/?LinkId=529714)
  
  *Av [Rick Anderson](https://twitter.com/RickAndMSFT)*

[senast installerad]: http://www.windowsazure.com/downloads/?sdk=net  
[Översikt över sessionstillståndet ASP.NET]: http://msdn.microsoft.com/library/ms178581.aspx

[NewIcon]: ./media/web-sites-dotnet-session-state-caching/CacheScreenshot_NewButton.png
[NewCacheDialog]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CreateOptions.png
[CacheIcon]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheIcon.png
[NuGetDialog]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_NuGet.png
[OutputConfig]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_OC_WebConfig.png
[CacheConfig]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheConfig.png
[EndpointURL]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_EndpointURL.png
[Managekeys]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_ManageAccessKeys.png




<!--HONumber=sep16_HO1-->


