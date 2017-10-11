---
title: "Cache ASP.NET-Sessionstillståndsprovider | Microsoft Docs"
description: "Lär dig hur du lagrar sessionstillståndet ASP.NET med hjälp av Azure Redis-Cache"
services: redis-cache
documentationcenter: na
author: steved0x
manager: douge
editor: tysonn
ms.assetid: 192f384c-836a-479a-bb65-8c3e6d6522bb
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 05/01/2017
ms.author: sdanie
ms.openlocfilehash: 0f3683939ac9646565a0669e19b4c82811d621fc
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="aspnet-session-state-provider-for-azure-redis-cache"></a>Provider av ASP.NET-sessionstillstånd för Azure Redis-cache
Azure Redis-Cache ger en sessionstillståndsprovider som du kan använda för att lagra dina sessionstillstånd i cache-minnet i stället för i minnet eller i en SQL Server-databas. Om du vill använda sessionstillståndsprovider för cachelagring först konfigurera din cache och sedan konfigurera ASP.NET-program för cache med Redis-Cache Session tillstånd NuGet-paketet.

Är det ofta inte praktiska i en verklig molnappen att lagra inte någon form av tillståndet för en användarsession, men vissa metoder påverkar prestanda och skalbarhet mer än andra. Om du behöver lagra tillstånd är den bästa lösningen att mängden tillstånd små och lagra den på cookies. Om som inte är möjligt, är den bästa lösningen nästa du använder sessionstillståndet ASP.NET med en provider för distribuerade, InMemory-cachen. Sämsta lösningen från en prestanda och skalbarhet synvinkel är en databas säkerhetskopieras sessionstillståndsprovider. Det här avsnittet innehåller anvisningar om hur du använder ASP.NET-Sessionstillståndsprovider för Azure Redis-Cache. Information om andra alternativ för sessionen tillstånd finns [sessionstillståndet ASP.NET alternativ](#aspnet-session-state-options).

## <a name="store-aspnet-session-state-in-the-cache"></a>Lagra ASP.NET-sessionstillstånd i cachen
Om du vill konfigurera ett klientprogram i Visual Studio med Redis-Cache Session tillstånd NuGet-paketet, klickar du på **NuGet Package Manager**, **Pakethanterarkonsolen** från den **verktyg**menyn.

Kör följande kommando från fönstret `Package Manager Console`.
    
```
Install-Package Microsoft.Web.RedisSessionStateProvider
```

> [!IMPORTANT]
> Om du använder funktionen kluster från premium-nivån, måste du använda [RedisSessionStateProvider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.1 eller högre eller ett undantagsfel genereras. Flytta till 2.0.1 eller högre är en ny ändring; Mer information finns i [v2.0.0 information om ändringar i bryter](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details). Vid tidpunkten för den här artikeln uppdateringen är den aktuella versionen av det här paketet 2.2.3.
> 
> 

Redis-Session tillstånd providern NuGet-paketet har ett beroende på StackExchange.Redis.StrongName-paketet. Om paketet StackExchange.Redis.StrongName inte finns i ditt projekt, installeras.

>[!NOTE]
>Förutom starkt krypterat namn StackExchange.Redis.StrongName paket finns det också StackExchange.Redis icke-starkt krypterat namn version. Om ditt projekt använder icke-starkt krypterat namn StackExchange.Redis versionen måste du avinstallera den, hämta annars du namnkonflikter i projektet. Mer information om dessa paket finns [konfigurerar .NET-cacheklienter](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).
>
>

NuGet-paketet hämtar och lägger till de nödvändiga sammansättningsreferenser och lägger till följande avsnitt i web.config-filen. Det här avsnittet innehåller nödvändig konfiguration för ASP.NET-program att använda Redis-Cache Sessionstillståndsprovider.

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
    <!--
    <add name="MySessionStateStore"
           host = "127.0.0.1" [String]
        port = "" [number]
        accessKey = "" [String]
        ssl = "false" [true|false]
        throwOnError = "true" [true|false]
        retryTimeoutInMilliseconds = "0" [number]
        databaseId = "0" [number]
        applicationName = "" [String]
        connectionTimeoutInMilliseconds = "5000" [number]
        operationTimeoutInMilliseconds = "5000" [number]
    />
    -->
    <add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider" host="127.0.0.1" accessKey="" ssl="false"/>
    </providers>
</sessionState>
```

Kommenterade avsnittet ger ett exempel på de attribut och exempel på inställningar för varje attribut.

Konfigurera attribut med värden från din cachebladet i Microsoft Azure-portalen och konfigurera andra värden efter behov. Anvisningar för att komma åt egenskaper för cache finns [konfigurera Redis cacheinställningarna](cache-configure.md#configure-redis-cache-settings).

* **värden** – ange cache-slutpunkten.
* **port** – använda icke-SSL-port eller SSL-port, beroende på ssl-inställningar.
* **accessKey** – Använd den primära eller sekundära nyckeln för ditt cacheminne.
* **SSL** – SANT om du vill skydda klient och cache-kommunikation med ssl, annars false. Se till att ange rätt port.
  * Observera att icke-SSL-porten är inaktiverad som standard för nya cacheminnen. Ange true för den här inställningen att använda SSL-porten. Mer information om hur du aktiverar icke-SSL-porten finns i [Åtkomstportar](cache-configure.md#access-ports) under den [konfigurera en cache](cache-configure.md) avsnittet.
* **throwOnError** – SANT om du vill använda ett undantagsfel om det finns ett fel eller false om du vill att åtgärden misslyckades tyst. Du kan söka efter ett fel genom att kontrollera egenskapen statiska Microsoft.Web.Redis.RedisSessionStateProvider.LastException. Standardvärdet är true.
* **retryTimeoutInMilliseconds** – åtgärder som misslyckas igen under den här intervall som anges i millisekunder. Den första återförsök görs efter 20 millisekunder och sedan återförsök sker varje sekund tills retryTimeoutInMilliseconds intervallet har gått ut. Omedelbart efter det här intervallet försöks igen en sista gång. Om åtgärden fortfarande misslyckas undantag den tillbaka till anroparen, beroende på inställningen throwOnError. Standardvärdet är 0, vilket innebär att inga nya försök.
* **databaseId** – anger vilken databas som ska användas för cachelagring av utdata. Om inget anges används standardvärdet 0.
* **applicationName** – nycklar lagras i redis som `{<Application Name>_<Session ID>}_Data`. Den här namngivningsschemat kan flera program delar samma Redis-instans. Den här parametern är valfri och om du inte anger den ett standardvärde används.
* **connectionTimeoutInMilliseconds** – den här inställningen kan du åsidosätta inställningen connectTimeout i StackExchange.Redis-klienten. Om inget anges används standardinställningen connectTimeout 5000. Mer information finns i [StackExchange.Redis Konfigurationsmodell](http://go.microsoft.com/fwlink/?LinkId=398705).
* **operationTimeoutInMilliseconds** – den här inställningen kan du åsidosätta inställningen syncTimeout i StackExchange.Redis-klienten. Om inget anges används standardinställningen för syncTimeout 1000. Mer information finns i [StackExchange.Redis Konfigurationsmodell](http://go.microsoft.com/fwlink/?LinkId=398705).

Mer information om dessa egenskaper finns i den ursprungliga blogginlägget vid [om ASP.NET Sessionstillståndsprovider för Redis](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx).

Glöm inte att kommentera ut standard InProc session tillstånd providern avsnittet i filen web.config.

```xml
<!-- <sessionState mode="InProc"
     customProvider="DefaultSessionProvider">
     <providers>
        <add name="DefaultSessionProvider"
              type="System.Web.Providers.DefaultSessionStateProvider,
                    System.Web.Providers, Version=1.0.0.0, Culture=neutral,
                    PublicKeyToken=31bf3856ad364e35"
              connectionStringName="DefaultConnection" />
      </providers>
</sessionState> -->
```

Programmet är konfigurerat för att använda Redis-Cache Sessionstillståndsprovider när dessa steg utförs. När du använder sessionstillstånd i ditt program, är den lagrad i en Azure Redis-Cache-instans.

> [!IMPORTANT]
> Data som lagras i cachen måste kunna serialiseras, till skillnad från de data som kan lagras i standard tillstånd i minnet ASP.NET Session providern. När Sessionstillståndsprovider för Redis används, ska du vilka datatyper av som lagras i sessionens tillstånd som serialiserbar.
> 
> 

## <a name="aspnet-session-state-options"></a>Sessionstillståndet ASP.NET-alternativ
* Den här providern lagrar minne Sessionstillståndsprovider - sessionstillstånd i minnet. Fördelen med att använda den här providern är det är snabbt och enkelt. Men du kan skala Web Apps om du använder i minnet providern eftersom den inte distribueras.
* SQL Server-Sessionstillståndsprovider - providern lagrar sessionens tillstånd i Sql Server. Använd den här providern om du vill lagra beständiga sessionens tillstånd. Du kan skala ditt webbprogram, men använder Sql Server för sessionen har påverka prestanda på ditt webbprogram.
* Distribuerade i minnet Sessionstillståndsprovider som Redis-Cache Sessionstillståndsprovider - providern ger dig bäst hos båda. Ditt webbprogram kan ha en enkel, snabb och skalbar Sessionstillståndsprovider. Eftersom den här providern lagrar sessionens tillstånd i ett cacheminne, har din app att ta hänsyn till de egenskaper som är associerat när kommunicerar med en distribuerad i cacheminnet, till exempel tillfälliga nätverksfel. Metodtips om hur du använder Cache finns [cachelagring vägledning](../best-practices-caching.md) från Microsoft Patterns & Practices [Azure Cloud programdesign och implementering vägledning](https://github.com/mspnp/azure-guidance).

Mer information om sessionens tillstånd och andra metodtips finns [Web Development Best Practices (skapa verkliga Molnappar med Azure)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices).

## <a name="next-steps"></a>Nästa steg
Kolla in den [ASP.NET Utdatacacheprovider för Azure Redis-Cache](cache-aspnet-output-cache-provider.md).

