---
title: Cache-Utdatacacheprovider för ASP.NET
description: Lär dig hur du cachelagrar ASP.NET sidans utdata med Azure Redis Cache
services: redis-cache
documentationcenter: na
author: wesmc7777
manager: cfowler
editor: tysonn
ms.assetid: 78469a66-0829-484f-8660-b2598ec60fbf
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 02/14/2017
ms.author: wesmc
ms.openlocfilehash: 3cf906830965959709a8c7e8dc7d2acc3f3a6f32
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2018
ms.locfileid: "47096980"
---
# <a name="aspnet-output-cache-provider-for-azure-redis-cache"></a>ASP.NET Utdatacacheprovider för Azure Redis-Cache
Redis-Utdatacacheprovider är en molnlagringsmekanism för out-of-process för cache-utdata. Dessa data är specifikt för fullständig HTTP-svar (sidan cachelagring av utdata). Providern ansluts till den nya utökningsbarhet punkten för utdata cache-providern som introducerades i ASP.NET 4.

För att använda Redis Utdatacacheprovider måste först konfigurera din cache och konfigurera ASP.NET-program med hjälp av Redis utdata Cache-providern NuGet-paketet. Det här avsnittet innehåller information om hur du konfigurerar programmet att använda Redis Utdatacacheprovider. Läs mer om att skapa och konfigurera en Azure Redis Cache-instans, [skapa ett cacheminne](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

## <a name="store-aspnet-page-output-in-the-cache"></a>Store ASP.NET sidutdata i cacheminnet
Om du vill konfigurera ett klientprogram i Visual Studio med Redis Cache-Session tillstånd NuGet-paketet, klickar du på **NuGet-Pakethanteraren**, **Pakethanterarkonsolen** från den **verktyg**menyn.

Kör följande kommando från fönstret `Package Manager Console`.
    
```
Install-Package Microsoft.Web.RedisOutputCacheProvider
```

Redis utdata Cache-providern NuGet-paketet har ett beroende på StackExchange.Redis.StrongName-paketet. Om StackExchange.Redis.StrongName paketet inte är tillgänglig i ditt projekt, installeras. Mer information om Redis utdata Cache-providern NuGet-paketet finns i den [RedisOutputCacheProvider](https://www.nuget.org/packages/Microsoft.Web.RedisOutputCacheProvider/) NuGet-sidan.

>[!NOTE]
>Förutom starkt krypterad StackExchange.Redis.StrongName paketet finns det också StackExchange.Redis icke-starkt krypterad version. Om projektet använder den icke-starkt krypterad StackExchange.Redis version måste du avinstallera den, hämta annars du namnkonflikter i projektet. Mer information om dessa paket finns i [konfigurerar .NET-cacheklienter](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).
>
>

NuGet-paketet hämtar och lägger till de nödvändiga sammansättningsreferenserna och lägger till följande avsnitt i web.config-filen. Det här avsnittet innehåller konfigurationen som krävs för ASP.NET-program att använda Redis Utdatacacheprovider.

```xml
<caching>
  <outputCachedefault Provider="MyRedisOutputCache">
    <providers>
      <!-- For more details check https://github.com/Azure/aspnet-redis-providers/wiki -->
      <!-- Either use 'connectionString' OR 'settingsClassName' and 'settingsMethodName' OR use 'host','port','accessKey','ssl','connectionTimeoutInMilliseconds' and 'operationTimeoutInMilliseconds'. -->
      <!-- 'databaseId' and 'applicationName' can be used with both options. -->
      <!--
      <add name="MyRedisOutputCache" 
        host = "127.0.0.1" [String]
        port = "" [number]
        accessKey = "" [String]
        ssl = "false" [true|false]
        databaseId = "0" [number]
        applicationName = "" [String]
        connectionTimeoutInMilliseconds = "5000" [number]
        operationTimeoutInMilliseconds = "1000" [number]
        connectionString = "<Valid StackExchange.Redis connection string>" [String]
        settingsClassName = "<Assembly qualified class name that contains settings method specified below. Which basically return 'connectionString' value>" [String]
        settingsMethodName = "<Settings method should be defined in settingsClass. It should be public, static, does not take any parameters and should have a return type of 'String', which is basically 'connectionString' value.>" [String]
        loggingClassName = "<Assembly qualified class name that contains logging method specified below>" [String]
        loggingMethodName = "<Logging method should be defined in loggingClass. It should be public, static, does not take any parameters and should have a return type of System.IO.TextWriter.>" [String]
        redisSerializerType = "<Assembly qualified class name that implements Microsoft.Web.Redis.ISerializer>" [String]
      />
      -->
      <add name="MyRedisOutputCache" type="Microsoft.Web.Redis.RedisOutputCacheProvider"
           host=""
           accessKey=""
           ssl="true" />
  </outputCache>
</caching>
```

Kommenterade avsnittet innehåller ett exempel på de attribut och exempel på inställningar för varje attribut.

Konfigurera attribut med värden från din cachebladet i Microsoft Azure-portalen och konfigurera de andra värdena efter behov. Anvisningar om hur du använder cache-egenskaper finns i [konfigurera Redis-cacheinställningarna](cache-configure.md#configure-redis-cache-settings).

* **värden** – ange cache-slutpunkten.
* **port** – använda icke-SSL-port eller SSL-porten, beroende på vilka ssl-inställningar.
* **accessKey** – Använd den primära eller sekundära nyckeln för ditt cacheminne.
* **SSL** – SANT om du vill skydda cache/klientkommunikation med ssl, annars false. Glöm inte att ange rätt port.
  * Observera att icke-SSL-porten är inaktiverad som standard för nya cacheminnen. Ange som SANT för den här inställningen för att använda SSL-porten. Mer information om hur du aktiverar icke-SSL-porten finns i den [Åtkomstportar](cache-configure.md#access-ports) i avsnittet den [konfigurera en cache](cache-configure.md) avsnittet.
* **databaseId** – angivna vilken databas som ska användas för cache-utdata. Om den inte anges används standardvärdet 0.
* **applicationName** – nycklar lagras i redis som `<AppName>_<SessionId>_Data`. Den här namngivningsschemat kan flera program delar samma nyckel. Den här parametern är valfri och om du inte anger den ett standardvärde används.
* **connectionTimeoutInMilliseconds** – den här inställningen kan du åsidosätta inställningen connectTimeout i StackExchange.Redis-klienten. Om den inte anges används standardinställningen connectTimeout 5000. Mer information finns i [Konfigurationsmodell](http://go.microsoft.com/fwlink/?LinkId=398705).
* **operationTimeoutInMilliseconds** – den här inställningen kan du åsidosätta inställningen syncTimeout i StackExchange.Redis-klienten. Om den inte anges används standardinställningen för syncTimeout på 1 000. Mer information finns i [Konfigurationsmodell](http://go.microsoft.com/fwlink/?LinkId=398705).

Lägga till ett OutputCache-direktiv i varje sida som du vill cachelagra utdata.

```
<%@ OutputCache Duration="60" VaryByParam="*" %>
```

I föregående exempel cachelagrade siddata behålls på cacheplatsen i 60 sekunder och en annan version av sidan cachelagras för varje kombination av parametern. Läs mer om direktivet OutputCache [ @OutputCache ](http://go.microsoft.com/fwlink/?linkid=320837).

När dessa steg utförs har ditt program konfigurerats för att använda Redis Utdatacacheprovider.

## <a name="next-steps"></a>Nästa steg
Kolla in den [ASP.NET-Sessionstillståndsprovider för Azure Redis Cache](cache-aspnet-session-state-provider.md).

