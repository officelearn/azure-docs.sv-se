---
title: ASP.NET för cachelagring för Azure cache för Redis
description: Lär dig hur du cachelagrar ASP.NET sid utdata med Azure cache för Redis
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
ms.openlocfilehash: d3babb213f633586786c0015c27fae50e44369df
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2019
ms.locfileid: "71815650"
---
# <a name="aspnet-output-cache-provider-for-azure-cache-for-redis"></a>ASP.NET för cachelagring för Azure cache för Redis

Redis för utdata är en process som är utanför processen för cachelagring av utdata. Dessa data är specifika för fullständiga HTTP-svar (cachelagring av sidutdata). Providern ansluts till den nya utöknings punkten för utdataports-providern som introducerades i ASP.NET 4.

Om du vill använda Redis måste du först konfigurera cachen och sedan konfigurera ditt ASP.NET-program med Redis output cache Provider NuGet-paketet. Det här avsnittet innehåller information om hur du konfigurerar ditt program att använda Redis-providern för utdata. Mer information om hur du skapar och konfigurerar en Azure-cache för Redis-instans finns i [skapa en cache](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

## <a name="store-aspnet-page-output-in-the-cache"></a>Lagra ASP.NET-sidans utdata i cachen

Om du vill konfigurera ett klient program i Visual Studio med hjälp av NuGet-paketet Azure cache för Redis session State, klickar du på **NuGet Package Manager**, **Package Manager-konsolen** på menyn **verktyg** .

Kör följande kommando från fönstret `Package Manager Console`.

```
Install-Package Microsoft.Web.RedisOutputCacheProvider
```

NuGet-paketet för Redis-utdatacache har ett beroende av paketet StackExchange. Redis. StrongName. Om paketet StackExchange. Redis. StrongName inte finns i projektet är det installerat. Mer information om NuGet-paketet för Redis-utdatacache finns på sidan [RedisOutputCacheProvider](https://www.nuget.org/packages/Microsoft.Web.RedisOutputCacheProvider/) NuGet.

>[!NOTE]
>Förutom det starkt namngivna StackExchange. Redis. StrongName-paketet finns även StackExchange. Redis-versionen som inte är starkt namngiven. Om projektet använder den icke-säkra StackExchange. Redis-versionen måste du avinstallera den. annars kommer du att få namn konflikter i projektet. Mer information om dessa paket finns i [Konfigurera .net-cache-klienter](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

NuGet-paketet hämtar och lägger till de nödvändiga sammansättnings referenserna och lägger till följande avsnitt i din Web. config-fil. Det här avsnittet innehåller den konfiguration som krävs för ditt ASP.NET-program för att använda Redis-providern för utdata.

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

Konfigurera attributen med värdena från ditt cache-blad i Microsoft Azure-portalen och konfigurera de andra värdena efter behov. Anvisningar om hur du kommer åt dina cache-egenskaper finns i [Konfigurera Azure cache för Redis-inställningar](cache-configure.md#configure-azure-cache-for-redis-settings).

| Attribut | type | Standard | Beskrivning |
| --------- | ---- | ------- | ----------- |
| *värd* | sträng | värd | IP-adressen eller värd namnet för Redis-servern |
| *lastning* | positivt heltal | 6379 (ej SSL)<br/>6380 (SSL) | Redis server-port |
| *accessKey* | sträng | "" | Redis server-lösenord när Redis-auktorisering har Aktiver ATS. Värdet är en tom sträng som standard, vilket innebär att providern för sessionstillstånd inte använder lösen ord när de ansluter till Redis-servern. **Om din redis-server finns i ett offentligt tillgängligt nätverk som Azure Redis Cache, se till att aktivera Redis-auktorisering för att förbättra säkerheten och ange ett säkert lösen ord.** |
| *ssl* | boolean | **!** | Om du vill ansluta till Redis-servern via SSL. Värdet är **false** som standard eftersom Redis inte har stöd för SSL out of box. **Om du använder Azure Redis Cache som har stöd för SSL från rutan, måste du ange värdet sant för att förbättra säkerheten.**<br/><br/>Observera att icke-SSL-porten är inaktiverad som standard för nya cacheminnen. Ange **True** för den här inställningen för att använda SSL-porten. Mer information om hur du aktiverar icke-SSL-porten finns i avsnittet [åtkomst portar](cache-configure.md#access-ports) i avsnittet [Konfigurera ett cacheminne](cache-configure.md) . |
| *databaseIdNumber* | positivt heltal | 0 | *Det här attributet kan bara anges genom antingen Web. config eller AppSettings.*<br/><br/>Ange vilken Redis-databas som ska användas. |
| *connectionTimeoutInMilliseconds* | positivt heltal | Tillhandahålls av StackExchange. Redis | Används för att ange *ConnectTimeout* när du skapar stackexchange. Redis. ConnectionMultiplexer. |
| *operationTimeoutInMilliseconds* | positivt heltal | Tillhandahålls av StackExchange. Redis | Används för att ange *SyncTimeout* när du skapar stackexchange. Redis. ConnectionMultiplexer. |
| *ConnectionString* (giltig anslutnings sträng för stackexchange. Redis) | sträng | *ej tillämpligt* | Antingen en parameter referens till AppSettings eller Web. config, eller en giltig anslutnings sträng för StackExchange. Redis. Det här attributet kan ange värden för *Host*, *port*, *Accesskey*, *SSL*och andra stackexchange. Redis-attribut. En närmare titt på *ConnectionString*finns i [ställa in ConnectionString](#setting-connectionstring) i avsnittet [attribut anmärkningar](#attribute-notes) . |
| *settingsClassName*<br/>*settingsMethodName* | sträng<br/>sträng | *ej tillämpligt* | *De här attributen kan endast anges via antingen Web. config eller AppSettings.*<br/><br/>Använd de här attributen för att ange en anslutnings sträng. *settingsClassName* ska vara ett kvalificerat klass namn för sammansättningen som innehåller den metod som anges av *settingsMethodName*.<br/><br/>Metoden som anges av *settingsMethodName* ska vara offentlig, statisk och void (tar inte med parametrar) med en retur **sträng**typ. Den här metoden returnerar den faktiska anslutnings strängen. |
| *loggingClassName*<br/>*loggingMethodName* | sträng<br/>sträng | *ej tillämpligt* | *De här attributen kan endast anges via antingen Web. config eller AppSettings.*<br/><br/>Använd de här attributen för att felsöka ditt program genom att tillhandahålla loggar från session State/utdatacache tillsammans med loggar från StackExchange. Redis. *loggingClassName* ska vara ett kvalificerat klass namn för sammansättningen som innehåller den metod som anges av *loggingMethodName*.<br/><br/>Metoden som anges av *loggingMethodName* ska vara offentlig, statisk och void (tar inte med parametrar), med retur typen **system. io. TextWriter**. |
| *applicationName* | sträng | Namnet på den aktuella processen eller "/" | *Endast SessionStateProvider*<br/>*Det här attributet kan bara anges genom antingen Web. config eller AppSettings.*<br/><br/>Det App Name-prefix som ska användas i Redis-cache. Kunden kan använda samma Redis-cache för olika användnings sätt. För att säkerställa att sessionsnycklarna inte kolliderar, kan det föregås av programmets namn. |
| *throwOnError* | boolean | true | *Endast SessionStateProvider*<br/>*Det här attributet kan bara anges genom antingen Web. config eller AppSettings.*<br/><br/>Om ett undantag ska utlöses när ett fel uppstår.<br/><br/>Mer information om *throwOnError*finns i [kommentarer om *throwOnError* ](#notes-on-throwonerror) i avsnittet [Attribute Notes](#attribute-notes) . |>*Microsoft.Web.Redis.RedisSessionStateProvider.LastException*. |
| *retryTimeoutInMilliseconds* | positivt heltal | 5000 | *Endast SessionStateProvider*<br/>*Det här attributet kan bara anges genom antingen Web. config eller AppSettings.*<br/><br/>Hur lång tid det tar att försöka igen när en åtgärd Miss lyckas. Om det här värdet är mindre än *operationTimeoutInMilliseconds*kommer providern inte att försöka igen.<br/><br/>Mer information om *retryTimeoutInMilliseconds*finns i [kommentarer om *retryTimeoutInMilliseconds* ](#notes-on-retrytimeoutinmilliseconds) i avsnittet [Attribute Notes](#attribute-notes) . |
| *redisSerializerType* | sträng | *ej tillämpligt* | Anger sammansättningens kvalificerade typnamn för en klass som implementerar Microsoft. Web. Redis. ISerializer och som innehåller den anpassade logiken för att serialisera och deserialisera värdena. Mer information finns i [About *redisSerializerType* ](#about-redisserializertype) i avsnittet [Attribute Notes](#attribute-notes) . |
|

## <a name="attribute-notes"></a>Attribut-kommentarer

### <a name="setting-connectionstring"></a>Ställer in *ConnectionString*

Värdet för *ConnectionString* används som nyckel för att hämta den faktiska anslutnings strängen från appSettings, om en sådan sträng finns i appSettings. Om det inte går att hitta i AppSettings används värdet för *ConnectionString* som nyckel för att hämta den faktiska anslutnings strängen från avsnittet Web. config **ConnectionString** om det avsnittet finns. Om anslutnings strängen inte finns i AppSettings eller Web. config **ConnectionString** -avsnittet, används literalt värde för *ConnectionString* som anslutnings sträng när du skapar stackexchange. Redis. ConnectionMultiplexer.

I följande exempel visas hur *ConnectionString* används.

#### <a name="example-1"></a>Exempel 1

```xml
<connectionStrings>
    <add name="MyRedisConnectionString" connectionString="mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False" />
</connectionStrings>
```

I `web.config` använder du ovanstående nyckel som parameter värde i stället för det faktiska värdet.

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

I `web.config` använder du ovanstående nyckel som parameter värde i stället för det faktiska värdet.

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

För närvarande, om ett fel inträffar under en session, kommer providern för sessionstillstånd att utlösa ett undantag. Detta avslutar programmet.

Det här beteendet har ändrats på ett sätt som har stöd för förväntningarna hos befintliga ASP.NET för sessionstillstånd, samtidigt som det ger möjlighet att agera på undantag, om så önskas. Standard beteendet genererar fortfarande ett undantag när ett fel inträffar, konsekvent med andra ASP.NET-providers för sessionstillstånd; befintlig kod ska fungera på samma sätt som tidigare.

Om du anger *throwOnError* till **falskt**kommer det att Miss förkastas i stället för att ett undantag utlöses när ett fel uppstår. För att se om det uppstod ett fel och, i så fall, identifiera vad undantaget var, kontrollerar du den statiska egenskapen *Microsoft. Web. Redis. RedisSessionStateProvider. LastException*.

### <a name="notes-on-retrytimeoutinmilliseconds"></a>Anteckningar på *retryTimeoutInMilliseconds*

Detta ger en del omprövnings logik för att förenkla det fall där vissa åtgärder i sessionen ska försöka utföras på grund av sådant som nätverks fel, samtidigt som du kan kontrol lera timeout-värdet för återförsök eller välja att helt få ett nytt försök.

Om du anger *retryTimeoutInMilliseconds* till ett nummer, till exempel 2000, och sedan en session Miss lyckas, försöker den igen om 2000 millisekunder innan den behandlar det som ett fel. Så om du vill att sessionens tillstånds leverantör ska tillämpa den här omprövnings logiken konfigurerar du bara tids gränsen. Det första omförsöket sker efter 20 millisekunder, vilket är tillräckligt i de flesta fall när ett nätverks fel inträffar. Därefter görs ett nytt försök varje sekund tills tids gränsen uppnås. Direkt efter timeout-värdet försöker den igen en gång för att se till att den inte kapas av tids gränsen (högst) en sekund.

Om du inte tror att du behöver försöka igen (till exempel när du kör Redis-servern på samma dator som ditt program), eller om du vill hantera omprövnings logiken själv, anger du *retryTimeoutInMilliseconds* till 0.

### <a name="about-redisserializertype"></a>Om *redisSerializerType*

Som standard görs serialiseringen för att lagra värdena i Redis i ett binärformat som tillhandahålls av **BinaryFormatter** -klassen. Använd *redisSerializerType* för att ange sammansättningens kvalificerade typnamn för en klass som implementerar **Microsoft. Web. Redis. ISerializer** och har den anpassade logiken för att serialisera och deserialisera värdena. Här är till exempel en JSON-serialiserare med hjälp av JSON.NET:

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
            if (data == null)
            {
                return null;
            }
            return JsonConvert.DeserializeObject(Encoding.UTF8.GetString(data), _settings);
        }
    }
}
```

Om den här klassen är definierad i en sammansättning med namnet **MyCompanyDll**kan du ange parametern *redisSerializerType* för att använda den:

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

## <a name="output-cache-directive"></a>Utgående cache-direktiv

Lägg till ett OutputCache-direktiv på varje sida som du vill cachelagra utdata för.

```xml
<%@ OutputCache Duration="60" VaryByParam="*" %>
```

I föregående exempel finns cachelagrade sid data kvar i cachen i 60 sekunder och en annan version av sidan cachelagras för varje parameter kombination. Mer information om OutputCache-direktivet finns i [@OutputCache](https://go.microsoft.com/fwlink/?linkid=320837).

När dessa steg har utförts är ditt program konfigurerat för att använda Redis-providern för utdata.

## <a name="next-steps"></a>Nästa steg

Kolla in [providern för ASP.net för Azure cache för Redis](cache-aspnet-session-state-provider.md).
