---
title: Cache ASP.NET-Sessionstillståndsprovider | Microsoft Docs
description: Lär dig att lagra ASP.NET-sessionstillstånd med hjälp av Azure Cache för Redis
services: cache
documentationcenter: na
author: yegu-ms
manager: jhubbard
editor: tysonn
ms.assetid: 192f384c-836a-479a-bb65-8c3e6d6522bb
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache
ms.workload: tbd
ms.date: 05/01/2017
ms.author: yegu
ms.openlocfilehash: 4a51040ecdbf22af03ce1e6edaaa0ff577bbc076
ms.sourcegitcommit: ad3e63af10cd2b24bf4ebb9cc630b998290af467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2019
ms.locfileid: "58793245"
---
# <a name="aspnet-session-state-provider-for-azure-cache-for-redis"></a>ASP.NET-Sessionstillståndsprovider för Azure Cache för Redis

Azure Cache för Redis ger en sessionstillståndsprovider som du kan använda för att lagra din session tillstånd i minnet med Azure Cache för Redis i stället för en SQL Server-databas. För att använda cachelagring sessionstillståndsprovidern måste först konfigurera din cache och konfigurera ASP.NET-program för cache med hjälp av Azure Cache för Redis-Session tillstånd NuGet-paket.

Det är ofta inte praktiskt i en verklig molnapp att undvika att lagra någon form av tillståndet för en användarsession, men vissa metoder påverka prestanda och skalbarhet som är mer än andra. Om du behöver lagra tillstånd är den bästa lösningen att hålla mängden tillstånd små och lagra den på cookies. Om det inte är möjligt, är nästa bästa lösning att använda ASP.NET-sessionstillstånd med en provider för distribuerad, minnesinbyggd cache. Sämsta lösningen från en prestanda och skalbarhet synvinkel är att använda en databas säkerhetskopieras provider av sessionstillstånd. Det här avsnittet innehåller information om hur du använder ASP.NET-Sessionstillståndsprovider för Azure Cache för Redis. Information om andra sessionsalternativ tillstånd finns [ASP.NET-sessionstillstånd alternativ](#aspnet-session-state-options).

## <a name="store-aspnet-session-state-in-the-cache"></a>Lagra ASP.NET-sessionstillstånd i cachen

Om du vill konfigurera ett klientprogram i Visual Studio med hjälp av Azure Cache för Redis-Session tillstånd NuGet-paket, klickar du på **NuGet-Pakethanteraren**, **Pakethanterarkonsolen** från den **verktyg**  menyn.

Kör följande kommando från fönstret `Package Manager Console`.
    

```powershell
Install-Package Microsoft.Web.RedisSessionStateProvider
```

> [!IMPORTANT]
> Om du använder klusterfunktionen från premium-nivån, måste du använda [RedisSessionStateProvider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.1 eller högre eller ett undantagsfel utlöses. Flytta 2.0.1 eller högre är en viktig ändring; Mer information finns i [v2.0.0 viktig information om ändringar](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details). Vid tidpunkten för den här artikeln uppdateringen är den aktuella versionen av det här paketet 2.2.3.
> 
> 

Redis-Session tillstånd providern NuGet-paketet har ett beroende på StackExchange.Redis.StrongName-paketet. Om StackExchange.Redis.StrongName paketet inte är tillgänglig i ditt projekt, installeras.

>[!NOTE]
>Förutom starkt krypterad StackExchange.Redis.StrongName paketet finns det också StackExchange.Redis icke-starkt krypterad version. Om projektet använder den icke-starkt krypterad StackExchange.Redis version måste du avinstallera den, hämta annars du namnkonflikter i projektet. Mer information om dessa paket finns i [konfigurerar .NET-cacheklienter](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).
>
>

NuGet-paketet hämtar och lägger till de nödvändiga sammansättningsreferenserna och lägger till följande avsnitt i web.config-filen. Det här avsnittet innehåller konfigurationen som krävs för ASP.NET-program du använder Azure Cache för Redis-Sessionstillståndsprovider.

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
</sessionState>
```

Kommenterade avsnittet innehåller ett exempel på de attribut och exempel på inställningar för varje attribut.

Konfigurera attribut med värden från din cachebladet i Microsoft Azure-portalen och konfigurera de andra värdena efter behov. Anvisningar om hur du använder cache-egenskaper finns i [konfigurera Azure Cache för Redis-inställningar](cache-configure.md#configure-azure-cache-for-redis-settings).

* **värden** – ange cache-slutpunkten.
* **port** – använda icke-SSL-port eller SSL-porten, beroende på vilka ssl-inställningar.
* **accessKey** – Använd den primära eller sekundära nyckeln för ditt cacheminne.
* **SSL** – SANT om du vill skydda cache/klientkommunikation med ssl, annars false. Glöm inte att ange rätt port.
  * Observera att icke-SSL-porten är inaktiverad som standard för nya cacheminnen. Ange som SANT för den här inställningen för att använda SSL-porten. Mer information om hur du aktiverar icke-SSL-porten finns i den [Åtkomstportar](cache-configure.md#access-ports) i avsnittet den [konfigurera en cache](cache-configure.md) avsnittet.
* **throwOnError** – SANT om du vill att ett undantagsfel om det är något fel eller false om du vill att åtgärden misslyckas tyst. Du kan söka efter ett fel genom att kontrollera egenskapen statiska Microsoft.Web.Redis.RedisSessionStateProvider.LastException. Standardvärdet är sant.
* **retryTimeoutInMilliseconds** – åtgärder som misslyckas görs under den här intervall som anges i millisekunder. Det första återförsöket sker efter 20 millisekunder och sedan återförsök sker varje sekund tills retryTimeoutInMilliseconds intervallet upphör att gälla. Omedelbart efter det här intervallet görs åtgärden en sista gång. Om åtgärden fortfarande misslyckas, undantagsfel den tillbaka till anroparen, beroende på inställningen throwOnError. Standardvärdet är 0, vilket innebär att inga nya försök.
* **databaseId** – anger vilken databas som ska användas för cache-utdata. Om den inte anges används standardvärdet 0.
* **applicationName** – nycklar lagras i redis som `{<Application Name>_<Session ID>}_Data`. Den här namngivningsschemat kan flera program delar samma Redis-instans. Den här parametern är valfri och om du inte anger den ett standardvärde används.
* **connectionTimeoutInMilliseconds** – den här inställningen kan du åsidosätta inställningen connectTimeout i StackExchange.Redis-klienten. Om den inte anges används standardinställningen connectTimeout 5000. Mer information finns i [Konfigurationsmodell](https://go.microsoft.com/fwlink/?LinkId=398705).
* **operationTimeoutInMilliseconds** – den här inställningen kan du åsidosätta inställningen syncTimeout i StackExchange.Redis-klienten. Om den inte anges används standardinställningen för syncTimeout på 1 000. Mer information finns i [Konfigurationsmodell](https://go.microsoft.com/fwlink/?LinkId=398705).
* **redisSerializerType** -den här inställningen kan du ange anpassade serialisering av sessionen innehåll som skickas till Redis. Den angivna typen måste implementera `Microsoft.Web.Redis.ISerializer` och måste deklarera offentliga parameterlös konstruktor. Som standard `System.Runtime.Serialization.Formatters.Binary.BinaryFormatter` används.

Mer information om de här egenskaperna finns i det ursprungliga blogginlägget på [Vi presenterar ASP.NET-Sessionstillståndsprovider för Redis](https://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx).

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

När dessa steg utförs har ditt program konfigurerats för att använda Azure Cache för Redis-Sessionstillståndsprovider. När du använder sessionstillstånd i ditt program, lagras den i en Azure-Cache för Redis-instans.

> [!IMPORTANT]
> Data som lagras i cachen måste vara serialiserbara, till skillnad från de data som kan lagras i standard InMemory-ASP.NET Session State providern. När Sessionstillståndsprovidern för Redis används, är det viktigt att de datatyper som lagras i sessionens tillstånd är serialiseras.
> 
> 

## <a name="aspnet-session-state-options"></a>Alternativ för ASP.NET-sessionstillstånd

* I minnet Sessionstillståndsprovider - lagrar den här providern sessionstillstånd i minnet. Fördelen med att använda den här providern är det är enkelt och snabbt. Men du inte skala Web Apps om du använder i minnet providern eftersom den inte distribueras.
* Provider av sessionstillstånd för SQL Server - den här providern lagrar sessionens tillstånd i Sql Server. Använd den här providern om du vill lagra sessionstillståndet i permanent lagring. Du kan skala din Webbapp, men med hjälp av Sql Server för sessionen har en prestandapåverkan på din Webbapp. Du kan också använda den här providern med en [In-Memory OLTP configuration](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2017/11/28/asp-net-session-state-with-sql-server-in-memory-oltp/) för att förbättra prestanda.
* Distribuerade i minnet Sessionstillståndsprovider, till exempel Azure Cache för Redis-Sessionstillståndsprovider - providern ger dig bäst av båda världar. Din Webbapp kan ha en enkel, snabb och skalbar Sessionstillståndsprovider. Eftersom den här providern lagrar sessionens tillstånd i ett cacheminne, har din app att ta hänsyn till alla egenskaper som är associerade när man talar till en distribuerad i cacheminnet, till exempel tillfälliga nätverksfel. Bästa metoder för att använda Cache finns [riktlinjer för cachelagring](../best-practices-caching.md) från Microsoft Patterns & Practices [Azure Cloud programmets Design och Implementeringsguide](https://github.com/mspnp/azure-guidance).

Läs mer om sessionstillstånd och andra bästa praxis, [Web Development Best Practices (Building Real-World Cloud Apps med Azure)](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices).

## <a name="next-steps"></a>Nästa steg

Kolla in den [ASP.NET-Utdatacacheprovider för Azure Cache för Redis](cache-aspnet-output-cache-provider.md).