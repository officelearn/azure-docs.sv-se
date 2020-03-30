---
title: Cache-ASP.NET sessionstillståndsprovider
description: Lär dig hur du lagrar ASP.NET sessionstillstånd i minnet med Azure Cache för Redis.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 05/01/2017
ms.openlocfilehash: 25cef95e2d01012506148f03be45104e455e1fcd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530282"
---
# <a name="aspnet-session-state-provider-for-azure-cache-for-redis"></a>Provider av ASP.NET-sessionstillstånd för Azure Cache for Redis

Azure Cache för Redis tillhandahåller en sessionstillståndsprovider som du kan använda för att lagra ditt sessionstillstånd i minnet med Azure Cache för Redis i stället för en SQL Server-databas. Om du vill använda leverantören för cachelagringssession konfigurerar du först cacheminnet och konfigurerar sedan ASP.NET-programmet för cacheminne med Azure Cache for Redis Session State NuGet-paketet.

Det är ofta inte praktiskt i en verklig molnapp för att undvika att lagra någon form av tillstånd för en användarsession, men vissa metoder påverkar prestanda och skalbarhet mer än andra. Om du måste lagra tillstånd, är den bästa lösningen att hålla mängden tillstånd liten och lagra den i cookies. Om det inte är möjligt är den näst bästa lösningen att använda ASP.NET sessionstillstånd med en provider för distribuerad cacheminne i minnet. Den värsta lösningen från en prestanda- och skalbarhetssynpunkt är att använda en databasstödd sessionstillståndsprovider. Det här avsnittet innehåller vägledning om hur du använder ASP.NET Sessionstillståndsprovider för Azure Cache för Redis. Information om andra alternativ för sessionstillstånd finns [i ASP.NET alternativ för sessionstillstånd](#aspnet-session-state-options).

## <a name="store-aspnet-session-state-in-the-cache"></a>Lagra ASP.NET-sessionstillstånd i cachen

Om du vill konfigurera ett klientprogram i Visual Studio med Azure Cache for Redis Session State NuGet-paketet klickar du på **NuGet Package Manager**, **Package Manager Console** på **Verktyg-menyn.**

Kör följande kommando från fönstret `Package Manager Console`.
    

```powershell
Install-Package Microsoft.Web.RedisSessionStateProvider
```

> [!IMPORTANT]
> Om du använder klusterfunktionen från premiumnivån måste du använda [RedisSessionStateProvider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.1 eller senare eller ett undantag genereras. Att flytta till 2.0.1 eller högre är en bryta förändring; Mer information finns i [v2.0.0 Breaking Change Details](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details). Vid tidpunkten för den här artikeln uppdateringen är den aktuella versionen av detta paket 2.2.3.
> 
> 

Paketet Redis Session State Provider NuGet är beroende av paketet StackExchange.Redis.StrongName. Om paketet StackExchange.Redis.StrongName inte finns i projektet installeras det.

>[!NOTE]
>Förutom det starka namnet StackExchange.Redis.StrongName-paketet finns även StackExchange.Redis icke-starknamnsversion. Om projektet använder den icke-starka namnet StackExchange.Redis-version måste du avinstallera den, annars får du namnge konflikter i projektet. Mer information om dessa paket finns i [Konfigurera .NET-cacheklienter](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).
>
>

NuGet-paketet hämtar och lägger till nödvändiga sammansättningsreferenser och lägger till följande avsnitt i filen web.config. Det här avsnittet innehåller den konfiguration som krävs för att ditt ASP.NET-program ska använda Azure Cache for Redis Session State Provider.

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
  <providers>
    <!-- Either use 'connectionString' OR 'settingsClassName' and 'settingsMethodName' OR use 'host','port','accessKey','ssl','connectionTimeoutInMilliseconds' and 'operationTimeoutInMilliseconds'. -->
    <!-- 'throwOnError','retryTimeoutInMilliseconds','databaseId' and 'applicationName' can be used with both options. -->
    <!--
      <add name="MySessionStateStore" 
        host = "127.0.0.1" [String]
        port = "" [number]
        accessKey = "" [String]
        ssl = "false" [true|false]
        throwOnError = "true" [true|false]
        retryTimeoutInMilliseconds = "5000" [number]
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
    <add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider"
         host=""
         accessKey=""
         ssl="true" />
  </providers>
</sessionState>
```

Det kommenterade avsnittet innehåller ett exempel på attribut och exempelinställningar för varje attribut.

Konfigurera attributen med värdena från cachebladet i Microsoft Azure-portalen och konfigurera de andra värdena efter behov. Instruktioner om hur du kommer åt cacheegenskaperna finns i [Konfigurera Azure Cache för Redis-inställningar](cache-configure.md#configure-azure-cache-for-redis-settings).

* **värd** – ange cacheslutpunkten.
* **port** – använd antingen din icke-SSL-port eller din SSL-port, beroende på ssl-inställningarna.
* **accessKey** – använd antingen den primära eller sekundära nyckeln för cacheminnet.
* **ssl** – sant om du vill skydda cache/klientkommunikation med ssl; annars falskt. Var noga med att ange rätt port.
  * Observera att icke-SSL-porten är inaktiverad som standard för nya cacheminnen. Ange sant för den här inställningen om SSL-porten ska användas. Mer information om hur du aktiverar icke-SSL-porten finns i avsnittet [Access-portar](cache-configure.md#access-ports) i avsnittet [Konfigurera en cache.](cache-configure.md)
* **throwOnError** – sant om du vill att ett undantag ska utlösas om det finns ett fel, eller falskt om du vill att åtgärden ska misslyckas tyst. Du kan söka efter ett fel genom att kontrollera den statiska egenskapen Microsoft.Web.Redis.RedisSessionStateProvider.LastException. Standardinställningen är sann.
* **retryTimeoutInMilliseconds** – Åtgärder som misslyckas görs om under det här intervallet, som anges i millisekunder. Det första återförsöket sker efter 20 millisekunder och sedan försök inträffar varje sekund tills intervallet för återförsökTimeoutInMilliseconds upphör att gälla. Omedelbart efter detta intervall görs åtgärden på nytt en sista gång. Om åtgärden fortfarande misslyckas genereras undantaget tillbaka till anroparen, beroende på inställningen throwOnError. Standardvärdet är 0, vilket innebär inga återförsök.
* **databaseId** – Anger vilken databas som ska användas för cacheutdata. Om inget anges används standardvärdet 0.
* **applicationName** – Nycklar lagras i `{<Application Name>_<Session ID>}_Data`redis som . Med det här namngivningsschemat kan flera program dela samma Redis-instans. Den här parametern är valfri och om du inte anger det används ett standardvärde.
* **connectionTimeoutInMilliseconds** – Med den här inställningen kan du åsidosätta inställningen connectTimeout i StackExchange.Redis-klienten. Om inget anges används standardinställningen connectTimeout på 5000. Mer information finns i [StackExchange.Redis konfigurationsmodell](https://go.microsoft.com/fwlink/?LinkId=398705).
* **operationTimeoutInMilliseconds** – Med den här inställningen kan du åsidosätta inställningen syncTimeout i StackExchange.Redis-klienten. Om inget anges används standardinställningen syncTimeout på 1000. Mer information finns i [StackExchange.Redis konfigurationsmodell](https://go.microsoft.com/fwlink/?LinkId=398705).
* **redisSerializerType** - Med den här inställningen kan du ange anpassad serialisering av sessionsinnehåll som skickas till Redis. Den angivna typen `Microsoft.Web.Redis.ISerializer` måste implementera och deklarera konstruktor utan parametrar för offentliga parameterlösa. Som `System.Runtime.Serialization.Formatters.Binary.BinaryFormatter` standard används.

Mer information om dessa egenskaper finns i det ursprungliga blogginläggsmeddelandet på [Announcing ASP.NET Session State Provider for Redis](https://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx).

Glöm inte att kommentera den vanliga InProc session state provider avsnitt i din web.config.

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

När dessa steg har utförts är ditt program konfigurerat för att använda Azure Cache for Redis Session State Provider. När du använder sessionstillstånd i ditt program lagras det i en Azure Cache for Redis-instans.

> [!IMPORTANT]
> Data som lagras i cacheminnet måste vara serialiserbara, till skillnad från de data som kan lagras i standardminnes- ASP.NET Sessionstillståndsprovidern. När Sessionstillståndsprovidern för Redis används ska du se till att de datatyper som lagras i sessionstillstånd är serialiserbara.
> 
> 

## <a name="aspnet-session-state-options"></a>alternativ för ASP.NET sessionstillstånd

* I Memory Session State Provider - Den här providern lagrar sessionstillståndet i minnet. Fördelen med att använda denna leverantör är det är enkelt och snabbt. Du kan dock inte skala dina webbappar om du använder i minnesprovidern eftersom den inte distribueras.
* Sql Server Sessionstillståndsprovider – Den här providern lagrar sessionstillståndet i Sql Server. Använd den här providern om du vill lagra sessionstillståndet i beständig lagring. Du kan skala din web app men med Sql Server for Session påverkar webbappen prestanda. Du kan också använda den här providern med en [OLTP-konfiguration i minnet](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2017/11/28/asp-net-session-state-with-sql-server-in-memory-oltp/) för att förbättra prestanda.
* Distribuerad i memory session state provider som Azure Cache för Redis Session State Provider - Den här providern ger dig det bästa av två världar. Din webbapp kan ha en enkel, snabb och skalbar sessionstillståndsleverantör. Eftersom den här providern lagrar sessionstillståndet i en cache måste appen ta hänsyn till alla egenskaper som är associerade när du talar med en cache för distribuerade i minnet, till exempel tillfälliga nätverksfel. Metodtips för hur du använder Cache finns i [Caching-vägledning](../best-practices-caching.md) från Microsoft Patterns & Practices [Azure Cloud Application Design and Implementation Guidance](https://github.com/mspnp/azure-guidance).

Mer information om sessionstillstånd och andra metodtips finns i [Bästa praxis för webbutveckling (Skapa verkliga molnappar med Azure).](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices)

## <a name="third-party-session-state-providers"></a>Leverantörer av sessionstillstånd från tredje part

* [NCache (på andra sätt)](https://www.alachisoft.com/ncache/session-index.html)
* [Apache antända](https://apacheignite-net.readme.io/docs/aspnet-session-state-caching)

## <a name="next-steps"></a>Nästa steg

Kolla in [ASP.NET Utdatacachen för Azure Cache för Redis](cache-aspnet-output-cache-provider.md).
