---
title: "Cache-Utdatacacheprovider för ASP.NET"
description: "Lär dig att cachelagra ASP.NET sidutdata med hjälp av Azure Redis-Cache"
services: redis-cache
documentationcenter: na
author: steved0x
manager: douge
editor: tysonn
ms.assetid: 78469a66-0829-484f-8660-b2598ec60fbf
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 02/14/2017
ms.author: sdanie
ms.openlocfilehash: 845f25637a0e48460fc76c1ee36060274b3cec38
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="aspnet-output-cache-provider-for-azure-redis-cache"></a>ASP.NET Utdatacacheprovider för Azure Redis-Cache
Redis Utdatacacheprovider är en mekanism för lagring av out-of-process för cache-utdata. Informationen är specifikt för fullständig HTTP-svar (sidan cachelagring av utdata). Providern ansluts till den nya utdata cache providern utökningspunkt som introducerades i ASP.NET 4.

Om du vill använda Redis Utdatacacheprovider först konfigurera din cache och sedan konfigurera ASP.NET-program med hjälp av Redis utdata Cache providern NuGet-paketet. Det här avsnittet ger vägledning om hur du konfigurerar programmet att använda Redis Utdatacacheprovider. Mer information om hur du skapar och konfigurerar Azure Redis-Cache-instansen finns [skapa ett cacheminne](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

## <a name="store-aspnet-page-output-in-the-cache"></a>Lagra ASP.NET sidutdata i cacheminnet
Om du vill konfigurera ett klientprogram i Visual Studio med Redis-Cache Session tillstånd NuGet-paketet, klickar du på **NuGet Package Manager**, **Pakethanterarkonsolen** från den **verktyg**menyn.

Kör följande kommando från fönstret `Package Manager Console`.
    
```
Install-Package Microsoft.Web.RedisOutputCacheProvider
```

Redis utdata Cache providern NuGet-paketet har ett beroende på StackExchange.Redis.StrongName-paketet. Om paketet StackExchange.Redis.StrongName inte finns i ditt projekt, installeras. Mer information om Redis utdata Cache providern NuGet-paketet finns i [RedisOutputCacheProvider](https://www.nuget.org/packages/Microsoft.Web.RedisOutputCacheProvider/) NuGet-sidan.

>[!NOTE]
>Förutom starkt krypterat namn StackExchange.Redis.StrongName paket finns det också StackExchange.Redis icke-starkt krypterat namn version. Om ditt projekt använder icke-starkt krypterat namn StackExchange.Redis versionen måste du avinstallera den, hämta annars du namnkonflikter i projektet. Mer information om dessa paket finns [konfigurerar .NET-cacheklienter](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).
>
>

NuGet-paketet hämtar och lägger till de nödvändiga sammansättningsreferenser och lägger till följande avsnitt i web.config-filen. Det här avsnittet innehåller nödvändig konfiguration för ASP.NET-program att använda Redis Utdatacacheprovider.

```xml
<caching>
  <outputCachedefault Provider="MyRedisOutputCache">
    <providers>
      <!--
      <add name="MyRedisOutputCache"
        host = "127.0.0.1" [String]
        port = "" [number]
        accessKey = "" [String]
        ssl = "false" [true|false]
        databaseId = "0" [number]
        applicationName = "" [String]
        connectionTimeoutInMilliseconds = "5000" [number]
        operationTimeoutInMilliseconds = "5000" [number]
      />
      -->
      <add name="MyRedisOutputCache" type="Microsoft.Web.Redis.RedisOutputCacheProvider" host="127.0.0.1" accessKey="" ssl="false"/>
    </providers>
  </outputCache>
</caching>
```

Kommenterade avsnittet ger ett exempel på de attribut och exempel på inställningar för varje attribut.

Konfigurera attribut med värden från din cachebladet i Microsoft Azure-portalen och konfigurera andra värden efter behov. Anvisningar för att komma åt egenskaper för cache finns [konfigurera Redis cacheinställningarna](cache-configure.md#configure-redis-cache-settings).

* **värden** – ange cache-slutpunkten.
* **port** – använda icke-SSL-port eller SSL-port, beroende på ssl-inställningar.
* **accessKey** – Använd den primära eller sekundära nyckeln för ditt cacheminne.
* **SSL** – SANT om du vill skydda klient och cache-kommunikation med ssl, annars false. Se till att ange rätt port.
  * Observera att icke-SSL-porten är inaktiverad som standard för nya cacheminnen. Ange true för den här inställningen att använda SSL-porten. Mer information om hur du aktiverar icke-SSL-porten finns i [Åtkomstportar](cache-configure.md#access-ports) under den [konfigurera en cache](cache-configure.md) avsnittet.
* **databaseId** – angivna vilken databas som ska användas för cache-utdata. Om inget anges används standardvärdet 0.
* **applicationName** – nycklar lagras i redis som `<AppName>_<SessionId>_Data`. Den här namngivningsschemat kan flera program kan dela samma nyckel. Den här parametern är valfri och om du inte anger den ett standardvärde används.
* **connectionTimeoutInMilliseconds** – den här inställningen kan du åsidosätta inställningen connectTimeout i StackExchange.Redis-klienten. Om inget anges används standardinställningen connectTimeout 5000. Mer information finns i [StackExchange.Redis Konfigurationsmodell](http://go.microsoft.com/fwlink/?LinkId=398705).
* **operationTimeoutInMilliseconds** – den här inställningen kan du åsidosätta inställningen syncTimeout i StackExchange.Redis-klienten. Om inget anges används standardinställningen för syncTimeout 1000. Mer information finns i [StackExchange.Redis Konfigurationsmodell](http://go.microsoft.com/fwlink/?LinkId=398705).

Lägg till en direktivet OutputCache på varje sida som du vill cachelagra utdata.

```
<%@ OutputCache Duration="60" VaryByParam="*" %>
```

I föregående exempel cachelagrade siddata finns kvar i cacheminnet för 60 sekunder, och en annan version av sidan cachelagras för varje parameter-kombination. Läs mer om direktivet OutputCache [ @OutputCache ](http://go.microsoft.com/fwlink/?linkid=320837).

Programmet är konfigurerat för att använda Redis Utdatacacheprovider när dessa steg utförs.

## <a name="next-steps"></a>Nästa steg
Kolla in den [ASP.NET-Sessionstillståndsprovider för Azure Redis-Cache](cache-aspnet-session-state-provider.md).

