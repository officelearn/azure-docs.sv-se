---
title: ASP.NET Utdatacacheprovider för Azure Cache för Redis
description: Lär dig hur du sidutdata för ASP.NET med hjälp av Azure Cache för Redis-cache
services: cache
documentationcenter: na
author: yegu-ms
manager: jhubbard
editor: tysonn
ms.assetid: 78469a66-0829-484f-8660-b2598ec60fbf
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache
ms.workload: tbd
ms.date: 04/22/2018
ms.author: yegu
ms.openlocfilehash: a93d21b07dc486f743694ee99f60018ed4ef517c
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64943876"
---
# <a name="aspnet-output-cache-provider-for-azure-cache-for-redis"></a>ASP.NET Utdatacacheprovider för Azure Cache för Redis

Redis-Utdatacacheprovider är en molnlagringsmekanism för out-of-process för cache-utdata. Dessa data är specifikt för fullständig HTTP-svar (sidan cachelagring av utdata). Providern ansluts till den nya utökningsbarhet punkten för utdata cache-providern som introducerades i ASP.NET 4.

För att använda Redis Utdatacacheprovider måste först konfigurera din cache och konfigurera ASP.NET-program med hjälp av Redis utdata Cache-providern NuGet-paketet. Det här avsnittet innehåller information om hur du konfigurerar programmet att använda Redis Utdatacacheprovider. Läs mer om att skapa och konfigurera en Azure-Cache för Redis-instans, [skapa ett cacheminne](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

## <a name="store-aspnet-page-output-in-the-cache"></a>Store ASP.NET sidutdata i cacheminnet

Om du vill konfigurera ett klientprogram i Visual Studio med hjälp av Azure Cache för Redis-Session tillstånd NuGet-paket, klickar du på **NuGet-Pakethanteraren**, **Pakethanterarkonsolen** från den **verktyg**  menyn.

Kör följande kommando från fönstret `Package Manager Console`.

```
Install-Package Microsoft.Web.RedisOutputCacheProvider
```

Redis utdata Cache-providern NuGet-paketet har ett beroende på StackExchange.Redis.StrongName-paketet. Om StackExchange.Redis.StrongName paketet inte är tillgänglig i ditt projekt, installeras. Mer information om Redis utdata Cache-providern NuGet-paketet finns i den [RedisOutputCacheProvider](https://www.nuget.org/packages/Microsoft.Web.RedisOutputCacheProvider/) NuGet-sidan.

>[!NOTE]
>Förutom starkt krypterad StackExchange.Redis.StrongName paketet finns det också StackExchange.Redis icke-starkt krypterad version. Om ditt projekt använder icke-starkt krypterad StackExchange.Redis-version måste du avinstallera den. Annars uppstår namnkonflikter i projektet. Mer information om dessa paket finns i [konfigurerar .NET-cacheklienter](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

NuGet-paketet hämtar och lägger till de nödvändiga sammansättningsreferenserna och lägger till följande avsnitt i web.config-filen. Det här avsnittet innehåller konfigurationen som krävs för ASP.NET-program att använda Redis Utdatacacheprovider.

```xml
<caching>
  <outputCache defaultProvider="MyRedisOutputCache">
    <providers>
      <add name="MyRedisOutputCache" type="Microsoft.Web.Redis.RedisOutputCacheProvider"
           host=""
           accessKey=""
           ssl="true" />
    </providers>
  </outputCache>
</caching>
```

Konfigurera attribut med värden från din cachebladet i Microsoft Azure-portalen och konfigurera de andra värdena efter behov. Anvisningar om hur du använder cache-egenskaper finns i [konfigurera Azure Cache för Redis-inställningar](cache-configure.md#configure-azure-cache-for-redis-settings).

| Attribut | Typ | Standard | Beskrivning |
| --------- | ---- | ------- | ----------- |
| *host* | string | "localhost" | Redis IP-adressen eller värdnamnet servernamnet |
| *port* | positivt heltal | 6379 (icke-SSL)<br/>6380 (SSL) | Redis-serverport |
| *accessKey* | string | "" | Redis server lösenord när Redis-auktorisering har aktiverats. Värdet är tom sträng som standard, vilket innebär att sessionstillståndsprovidern inte kommer att använda alla lösenord när du ansluter till Redis-server. **Om Redis-servern är i ett allmänt tillgängligt nätverk som Azure Redis Cache, se till att aktivera Redis-auktorisering för ökad säkerhet och ange ett säkert lösenord.** |
| *ssl* | boolesk | **false** | Om du vill ansluta till Redis-servern via SSL. Det här värdet är **FALSKT** som standard eftersom Redis inte stöder SSL direkt ur lådan. **Om du använder Azure Redis Cache som stöder SSL direkt, måste du ange detta till true till ökad säkerhet.**<br/><br/>Observera att icke-SSL-porten är inaktiverad som standard för nya cacheminnen. Ange **SANT** för den här inställningen för att använda SSL-porten. Mer information om hur du aktiverar icke-SSL-porten finns i den [Åtkomstportar](cache-configure.md#access-ports) i avsnittet den [konfigurera en cache](cache-configure.md) avsnittet. |
| *databaseIdNumber* | positivt heltal | 0 | *Det här attributet kan anges endast via web.config eller AppSettings.*<br/><br/>Ange vilka Redis-databas som ska användas. |
| *connectionTimeoutInMilliseconds* | positivt heltal | Tillhandahålls av StackExchange.Redis | Används för att ange *ConnectTimeout* när du skapar StackExchange.Redis.ConnectionMultiplexer. |
| *operationTimeoutInMilliseconds* | positivt heltal | Tillhandahålls av StackExchange.Redis | Används för att ange *SyncTimeout* när du skapar StackExchange.Redis.ConnectionMultiplexer. |
| *connectionString* (Valid StackExchange.Redis connection string) | string | *n/a* | Antingen en parameter referens till AppSettings eller web.config, annars en giltig StackExchange.Redis-anslutningssträng. Det här attributet kan du ange värden för *värden*, *port*, *accessKey*, *ssl*, och andra StackExchange.Redis-attribut. En närmare titt på *connectionString*, se [inställningen connectionString](#setting-connectionstring) i den [attributet anteckningar](#attribute-notes) avsnittet. |
| *settingsClassName*<br/>*settingsMethodName* | string<br/>string | *n/a* | *Dessa attribut kan anges endast via web.config eller AppSettings.*<br/><br/>Använd dessa attribut för att ange en anslutningssträng. *settingsClassName* ska vara en av sammansättningen kvalificerade klassnamnet som innehåller den används av *settingsMethodName*.<br/><br/>Den används av *settingsMethodName* ska vara offentlig, statiskt och void (inte tar några parametrar), med Returtypen för **sträng**. Den här metoden returnerar den faktiska anslutningssträngen. |
| *loggingClassName*<br/>*loggingMethodName* | string<br/>string | *n/a* | *Dessa attribut kan anges endast via web.config eller AppSettings.*<br/><br/>Använd dessa attribut för att felsöka ditt program genom att tillhandahålla loggar från sessionstillstånd/utdatacachen tillsammans med loggar från StackExchange.Redis. *loggingClassName* ska vara en av sammansättningen kvalificerade klassnamnet som innehåller den används av *loggingMethodName*.<br/><br/>Den används av *loggingMethodName* ska vara offentlig, statiskt och void (inte tar några parametrar), med Returtypen för **System.IO.TextWriter**. |
| *applicationName* | string | Modulnamnet på den aktuella processen eller ”/” | *Endast SessionStateProvider*<br/>*Det här attributet kan anges endast via web.config eller AppSettings.*<br/><br/>App-namnprefix ska användas i Redis-cache. Kunden kan använda samma Redis-cache för olika syften. Om du vill se till att sessionsnycklarna inte hamnar i konflikt, kan det ha prefixet med namnet på programmet. |
| *throwOnError* | boolesk | true | *Endast SessionStateProvider*<br/>*Det här attributet kan anges endast via web.config eller AppSettings.*<br/><br/>Om du utlöser ett undantag när ett fel uppstår.<br/><br/>Mer information om *throwOnError*, se [anteckningar på *throwOnError* ](#notes-on-throwonerror) i den [attributet anteckningar](#attribute-notes) avsnittet. |>*Microsoft.Web.Redis.RedisSessionStateProvider.LastException*. |
| *retryTimeoutInMilliseconds* | positivt heltal | 5000 | *Endast SessionStateProvider*<br/>*Det här attributet kan anges endast via web.config eller AppSettings.*<br/><br/>Hur lång tid att försöka igen när en åtgärd misslyckas. Om värdet är mindre än *operationTimeoutInMilliseconds*, providern försöker inte.<br/><br/>Mer information om *retryTimeoutInMilliseconds*, se [anteckningar på *retryTimeoutInMilliseconds* ](#notes-on-retrytimeoutinmilliseconds) i den [attributet anteckningar](#attribute-notes) avsnittet. |
| *redisSerializerType* | string | *n/a* | Anger det kvalificerade typnamnet sammansättning för en klass som implementerar Microsoft.Web.Redis. ISerializer och som innehåller den anpassade logiken för att serialisera och deserialisera värdena. Mer information finns i [om *redisSerializerType* ](#about-redisserializertype) i den [attributet anteckningar](#attribute-notes) avsnittet. |
|

## <a name="attribute-notes"></a>Attributet anteckningar

### <a name="setting-connectionstring"></a>Ange *connectionString*

Värdet för *connectionString* används som nyckel för att hämta den faktiska anslutningssträngen från AppSettings, om sådana en sträng som finns i AppSettings. Om inte hittas i AppSettings, värdet för *connectionString* används som nyckel för att hämta faktiska anslutningssträngen från web.config **ConnectionString** om finns i avsnittet. Om strängen som inte finns i AppSettings eller web.config **ConnectionString** avsnittet literalvärde av *connectionString* ska användas som anslutningssträngen när du skapar StackExchange.Redis.ConnectionMultiplexer.

I följande exempel visas hur *connectionString* används.

#### <a name="example-1"></a>Exempel 1

```xml
<connectionStrings>
    <add name="MyRedisConnectionString" connectionString="mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False" />
</connectionStrings>
```

I `web.config`, använda ovanför nyckel som parametervärde i stället för att faktiskt värde.

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvide"
             name = "MySessionStateStore"
             connectionString = "MyRedisConnectionString"/>
    </providers>
</sessionState>
```

#### <a name="example-2"></a>Exempel 2

```xml
<appSettings>
    <add key="MyRedisConnectionString" value="mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False" />
</appSettings>
```

I `web.config`, använda ovanför nyckel som parametervärde i stället för att faktiskt värde.

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvide"
             name = "MySessionStateStore"
             connectionString = "MyRedisConnectionString"/>
    </providers>
</sessionState>
```

#### <a name="example-3"></a>Exempel 3

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvide"
             name = "MySessionStateStore"
             connectionString = "mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False"/>
    </providers>
</sessionState>
```

### <a name="notes-on-throwonerror"></a>Anteckningar på *throwOnError*

För närvarande, om ett fel inträffar under en session, undantagsfel sessionstillståndsprovidern ett. Detta stänger av programmet.

Det här beteendet har ändrats på ett sätt som stöder förväntningarna på befintliga ASP.NET session tillstånd providern användare samtidigt som man får möjlighet att använda undantag, om så önskas. Standardbeteendet fortfarande genereras ett undantag när ett fel uppstår, konsekvent med andra ASP.NET session tillstånd providers; befintliga koden fungera på samma sätt som tidigare.

Om du ställer in *throwOnError* till **FALSKT**, i stället för att ett undantag som utlöses när ett fel uppstår, den kommer att misslyckas tyst. För att se om det uppstod ett fel och i så fall, identifiera undantaget var, kontrollera den statiska egenskapen *Microsoft.Web.Redis.RedisSessionStateProvider.LastException*.

### <a name="notes-on-retrytimeoutinmilliseconds"></a>Anteckningar på *retryTimeoutInMilliseconds*

Detta ger vissa logik för omprövning av för att förenkla fallet där sessionen åtgärder bör försöka vid fel på grund av till exempel nätverk glapp också låter dig styra timeout för återförsök eller väljer bort återförsök helt samtidigt.

Om du ställer in *retryTimeoutInMilliseconds* till ett tal, till exempel 2000 sedan när en session misslyckas, kommer att försöka för 2000 millisekunder innan du behandlar det som ett fel. Så för att få sessionstillståndsprovidern att tillämpa logik för omprövning ska bara konfigurera timeout-värde. Det första återförsöket sker efter 20 millisekunder, som räcker för de flesta fall när ett nätverk glapp händer. Efter det kommer att försöka varje sekund tills tidsgränsen uppnås. Direkt efter timeout försöker den en gång för att se till att det inte skärs bort tidsgränsen (högst) en sekund.

Om du inte tror att du behöver göra om (till exempel när du använder Redis-servern på samma dator som ditt program) eller om du vill hantera omprövningslogiken själv ange *retryTimeoutInMilliseconds* till 0.

### <a name="about-redisserializertype"></a>Om *redisSerializerType*

Som standard serialisering att lagra värdena på Redis görs i ett binärformat som tillhandahålls av den **BinaryFormatter** klass. Använd *redisSerializerType* att ange det kvalificerade typnamnet sammansättning för en klass som implementerar **Microsoft.Web.Redis.ISerializer** och har den anpassade logiken för att serialisera och deserialisera värdena. Här är till exempel en Json-serialiseringsklass med JSON.NET:

```cs
namespace MyCompany.Redis
{
    public class JsonSerializer : ISerializer
    {
        private static JsonSerializerSettings _settings = new JsonSerializerSettings() { TypeNameHandling = TypeNameHandling.All };

        public byte[] Serialize(object data)
        {
            return Encoding.UTF8.GetBytes(JsonConvert.SerializeObject(data, _settings));
        }

        public object Deserialize(byte[] data)
        {
            if (data == null)6t6
            {
                return null;
            }
            return JsonConvert.DeserializeObject(Encoding.UTF8.GetString(data), _settings);
        }
    }
}
```

Förutsatt att den här klassen är definierad i en sammansättning med namnet **MyCompanyDll**, du kan ange parametern *redisSerializerType* att använda den:

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvider"
             name = "MySessionStateStore"
             redisSerializerType = "MyCompany.Redis.JsonSerializer,MyCompanyDll"
             ... />
    </providers>
</sessionState>
```

## <a name="output-cache-directive"></a>Cache-direktivet för utdata

Lägga till ett OutputCache-direktiv i varje sida som du vill cachelagra utdata.

```xml
<%@ OutputCache Duration="60" VaryByParam="*" %>
```

I föregående exempel cachelagrade siddata behålls på cacheplatsen i 60 sekunder och en annan version av sidan cachelagras för varje kombination av parametern. Läs mer om direktivet OutputCache [ @OutputCache ](https://go.microsoft.com/fwlink/?linkid=320837).

När dessa steg utförs har ditt program konfigurerats för att använda Redis Utdatacacheprovider.

## <a name="next-steps"></a>Nästa steg

Kolla in den [ASP.NET-Sessionstillståndsprovider för Azure Cache för Redis](cache-aspnet-session-state-provider.md).
