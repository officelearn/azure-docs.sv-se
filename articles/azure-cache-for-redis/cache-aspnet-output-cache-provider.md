---
title: ASP.NET utdatacacheprovider för Azure Cache för Redis
description: Lär dig hur du cachelagrar ASP.NET Sidutdata med Azure Cache för Redis. Redis Output Cache Provider är en processfri lagringsmekanism för utdatacachedata.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 04/22/2018
ms.openlocfilehash: f1d8189068278b46e3ec3ea66875d79bb91e5e16
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010213"
---
# <a name="aspnet-output-cache-provider-for-azure-cache-for-redis"></a>ASP.NET utdatacacheprovider för Azure Cache för Redis

Redis Output Cache Provider är en processfri lagringsmekanism för utdatacachedata. Dessa data är specifikt för fullständiga HTTP-svar (cachelagring av sidutdata). Providern ansluts till den nya utdatacacheprovidern utökningsbarhet punkt som infördes i ASP.NET 4.

Om du vill använda Redis Output Cache Provider konfigurerar du först cacheminnet och konfigurerar sedan ASP.NET-programmet med paketet Redis Output Cache Provider NuGet. Det här avsnittet innehåller vägledning om hur du konfigurerar programmet så att det använder Redis Output Cache Provider. Mer information om hur du skapar och konfigurerar en Azure Cache för Redis-instans finns i [Skapa en cache](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

## <a name="store-aspnet-page-output-in-the-cache"></a>Lagra ASP.NET sidutdata i cacheminnet

Om du vill konfigurera ett klientprogram i Visual Studio med Azure Cache for Redis Session State NuGet-paketet klickar du på **NuGet Package Manager**, **Package Manager Console** på **Verktyg-menyn.**

Kör följande kommando från fönstret `Package Manager Console`.

```
Install-Package Microsoft.Web.RedisOutputCacheProvider
```

Paketet Redis Output Cache Provider NuGet är beroende av paketet StackExchange.Redis.StrongName. Om paketet StackExchange.Redis.StrongName inte finns i projektet installeras det. Mer information om Paketet Redis Output Cache Provider NuGet finns på sidan [RedisOutputCacheProvider](https://www.nuget.org/packages/Microsoft.Web.RedisOutputCacheProvider/) NuGet.

>[!NOTE]
>Förutom det starka namnet StackExchange.Redis.StrongName-paketet finns även StackExchange.Redis icke-starknamnsversion. Om projektet använder den icke-starka namnet StackExchange.Redis-version måste du avinstallera den. Annars kommer du att uppleva namngivning konflikter i ditt projekt. Mer information om dessa paket finns i [Konfigurera .NET-cacheklienter](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

NuGet-paketet hämtar och lägger till nödvändiga sammansättningsreferenser och lägger till följande avsnitt i filen web.config. Det här avsnittet innehåller den konfiguration som krävs för att ditt ASP.NET-program ska använda Redis Output Cache Provider.

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

Konfigurera attributen med värdena från cachebladet i Microsoft Azure-portalen och konfigurera de andra värdena efter behov. Instruktioner om hur du kommer åt cacheegenskaperna finns i [Konfigurera Azure Cache för Redis-inställningar](cache-configure.md#configure-azure-cache-for-redis-settings).

| Attribut | Typ | Default | Beskrivning |
| --------- | ---- | ------- | ----------- |
| *Värd* | sträng | "Lokalt" | Redis-serverns IP-adress eller värdnamn |
| *Port* | positivt heltal | 6379 (icke-TLS/SSL)<br/>6380 (TLS/SSL) | Redis-serverport |
| *Accesskey* | sträng | "" | Redis-serverlösenord när Redis-auktorisering är aktiverat. Värdet är tom sträng som standard, vilket innebär att sessionstillståndsprovidern inte använder något lösenord när du ansluter till Redis-servern. **Om Redis-servern finns i ett allmänt tillgängligt nätverk som Azure Redis Cache måste du aktivera Redis-auktorisering för att förbättra säkerheten och ange ett säkert lösenord.** |
| *Ssl* | boolean | **Falska** | Om du vill ansluta till Redis-servern via TLS. Det här värdet är **falskt** som standard eftersom Redis inte stöder TLS-läge. **Om du använder Azure Redis Cache som stöder SSL utanför ramrutan, se till att ställa in detta på true för att förbättra säkerheten.**<br/><br/>Den icke-TLS-porten är inaktiverad som standard för nya cacheminnen. Ange **sant** för den här inställningen om du vill använda TLS-porten. Mer information om hur du aktiverar icke-TLS-porten finns i avsnittet [Access-portar](cache-configure.md#access-ports) i avsnittet [Konfigurera en cache.](cache-configure.md) |
| *databasIdNumber* | positivt heltal | 0 | *Det här attributet kan bara anges via antingen web.config eller AppSettings.*<br/><br/>Ange vilken Redis-databas som ska användas. |
| *anslutningTimeoutInMilliseconds* | positivt heltal | Tillhandahålls av StackExchange.Redis | Används för att ställa in *ConnectTimeout* när du skapar StackExchange.Redis.ConnectionMultiplexer. |
| *operationTimeoutInMilliseconds* | positivt heltal | Tillhandahålls av StackExchange.Redis | Används för att ange *SyncTimeout* när du skapar StackExchange.Redis.ConnectionMultiplexer. |
| *connectionString* (giltig StackExchange.Redis-anslutningssträng) | sträng | *n/a* | Antingen en parameterreferens till AppSettings eller web.config, eller en giltig StackExchange.Redis-anslutningssträng. Det här attributet kan tillhandahålla värden för *värden,* *port,* *accessKey,* *ssl*och andra StackExchange.Redis-attribut. En närmare titt på *connectionString*finns i [Ange anslutningSträngning](#setting-connectionstring) i avsnittet [Attributanteckningar.](#attribute-notes) |
| *inställningarClassName*<br/>*inställningarMethodName* | sträng<br/>sträng | *n/a* | *Dessa attribut kan bara anges via antingen web.config eller AppSettings.*<br/><br/>Använd dessa attribut för att tillhandahålla en anslutningssträng. *settingsClassName* ska vara ett sammansättningskvalificerat klassnamn som innehåller den metod som anges av *settingsMethodName*.<br/><br/>Den metod som anges av *settingsMethodName* ska vara offentlig, statisk och ogiltig (ta inga parametrar), med en returtyp av **sträng**. Den här metoden returnerar den faktiska anslutningssträngen. |
| *loggningKlassNamn*<br/>*loggaMethodName* | sträng<br/>sträng | *n/a* | *Dessa attribut kan bara anges via antingen web.config eller AppSettings.*<br/><br/>Använd dessa attribut för att felsöka ditt program genom att tillhandahålla loggar från Sessionstillstånd/utdatacache tillsammans med loggar från StackExchange.Redis. *loggingClassName* ska vara ett sammansättningskvalificerat klassnamn som innehåller den metod som anges av *loggningMethodName*.<br/><br/>Den metod som anges av *loggningMethodName* ska vara offentlig, statisk och ogiltig (ta inga parametrar), med en returtyp av **System.IO.TextWriter**. |
| *applicationName (programName)* | sträng | Modulnamnet på den aktuella processen eller "/" | *Endast SessionStateProvider*<br/>*Det här attributet kan bara anges via antingen web.config eller AppSettings.*<br/><br/>Det appnamnprefix som ska användas i Redis-cachen. Kunden kan använda samma Redis-cache för olika ändamål. Om du vill försäkra dig om att sessionsnycklarna inte kolliderar kan de föregås av programnamnet. |
| *throwOnError* | boolean | true | *Endast SessionStateProvider*<br/>*Det här attributet kan bara anges via antingen web.config eller AppSettings.*<br/><br/>Om ett undantag ska genereras när ett fel inträffar.<br/><br/>Mer information om *throwOnError*finns [i Anteckningar om *throwOnError* ](#notes-on-throwonerror) i avsnittet [Attributanteckningar.](#attribute-notes) |>*Microsoft.Web.Redis.RedisSessionStateProvider.LastException*. |
| *försöktimeoutInMilliseconds* | positivt heltal | 5000 | *Endast SessionStateProvider*<br/>*Det här attributet kan bara anges via antingen web.config eller AppSettings.*<br/><br/>Hur lång tid det tar att försöka igen när en åtgärd misslyckas. Om det här värdet är mindre än *operationTimeoutInMilliseconds*kommer providern inte att försöka igen.<br/><br/>Mer information om *återförsökTimeoutInMilliseconds*finns i [Anteckningar om *återförsökTimeoutInMilliseconds* ](#notes-on-retrytimeoutinmilliseconds) i avsnittet [Attributanteckningar.](#attribute-notes) |
| *redisSerializerType* | sträng | *n/a* | Anger namnet på sammansättningens kvalificerade typ för en klass som implementerar Microsoft.Web.Redis. ISerializer och som innehåller den anpassade logiken för att serialisera och avserialisera värdena. Mer information finns i [Om *återisSerializerType* ](#about-redisserializertype) i avsnittet [Attributanteckningar.](#attribute-notes) |

## <a name="attribute-notes"></a>Attributanteckningar

### <a name="setting-connectionstring"></a>Ställa in *anslutningSträngning*

Värdet *för connectionString* används som nyckel för att hämta den faktiska anslutningssträngen från AppSettings, om det finns en sådan sträng i AppSettings. Om det inte finns i AppSettings används värdet *för connectionString* som nyckel för att hämta verklig anslutningssträng från avsnittet web.config **ConnectionString,** om det avsnittet finns. Om anslutningssträngen inte finns i AppSettings eller avsnittet web.config **ConnectionString** används det litterala värdet för *connectionString* som anslutningssträng när stackExchange.Redis.ConnectionMultiplexer skapas.

Följande exempel illustrerar hur *connectionString* används.

#### <a name="example-1"></a>Exempel 1

```xml
<connectionStrings>
    <add name="MyRedisConnectionString" connectionString="mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False" />
</connectionStrings>
```

Använd `web.config`ovanstående nyckel som parametervärde i stället för verkligt värde.

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

Använd `web.config`ovanstående nyckel som parametervärde i stället för verkligt värde.

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

### <a name="notes-on-throwonerror"></a>Anteckningar om *throwOnError*

Om ett fel inträffar under en sessionsåtgärd kommer sessionstillståndsprovidern att utlösa ett undantag. Detta stänger av programmet.

Detta har ändrats på ett sätt som stöder förväntningarna hos befintliga ASP.NET session state provider användare samtidigt som möjligheten att agera på undantag, om så önskas. Standardbeteendet genererar fortfarande ett undantag när ett fel inträffar, i enlighet med andra ASP.NET sessionstillståndsleverantörer. befintlig kod ska fungera på samma sätt som tidigare.

Om du ställer *in throwOnError* till **false**, så i stället för att kasta ett undantag när ett fel inträffar, kommer det att misslyckas tyst. Om du vill se om det uppstod ett fel och i så fall ta reda på vad undantaget var, kontrollera den statiska egenskapen *Microsoft.Web.Redis.RedisSessionStateProvider.LastException*.

### <a name="notes-on-retrytimeoutinmilliseconds"></a>Anmärkningar om *återförsökTimeoutInMilliseconds*

Detta ger några återförsök logik för att förenkla fallet där vissa session operation bör försöka på fel på grund av saker som nätverk glitch, samtidigt som du kan styra återförsök timeout eller välja bort återförsök helt.

Om du ställer in ett tal på *nyttTimeoutInMillisekunder,* till exempel 2000, försöker den igen i 2 000 millisekunder innan den behandlas som ett fel. Så för att ha sessionstillståndsprovidern för att tillämpa logiken för återförsök konfigurerar du bara tidsgränsen. Det första återförsöket kommer att ske efter 20 millisekunder, vilket är tillräckligt i de flesta fall när ett nätverksfel inträffar. Efter det kommer det att försöka igen varje sekund tills det time out. Direkt efter timeout, kommer det att försöka en gång till för att se till att det inte kommer att skära av timeout av (högst) en sekund.

Om du inte tror att du behöver försöka igen (till exempel när du kör Redis-servern på samma dator som ditt program) eller om du vill hantera logiken för återförsök själv, ange *återförsökTimeoutInMilliseconds* till 0.

### <a name="about-redisserializertype"></a>Om *redisSerializerType*

Som standard görs serialiseringen för att lagra värdena på Redis i binärformat som tillhandahålls av klassen **BinaryFormatter.** Använd *redisSerializerType* för att ange sammansättningens kvalificerade typnamn för en klass som implementerar **Microsoft.Web.Redis.ISerializer** och har den anpassade logiken för att serialisera och deserialisera värdena. Här är till exempel en Json serializer-klass som använder JSON.NET:

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

Förutsatt att den här klassen definieras i en sammansättning med namnet **MyCompanyDll**kan du ställa in parametern *redisSerializerType* så att den används:

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

## <a name="output-cache-directive"></a>Direktivet om utdatacache

Lägg till ett OutputCache-direktiv på varje sida som du vill cachelagra utdata för.

```xml
<%@ OutputCache Duration="60" VaryByParam="*" %>
```

I föregående exempel finns cachelagrade siddata kvar i cacheminnet i 60 sekunder och en annan version av sidan cachelagras för varje parameterkombination. Mer information om direktivet OutputCache [@OutputCache](https://go.microsoft.com/fwlink/?linkid=320837)finns i .

När dessa steg har utförts är programmet konfigurerat för att använda Redis Output Cache Provider.

## <a name="third-party-output-cache-providers"></a>Leverantörer av utdatacachen från tredje part

* [NCache (på andra sätt)](https://www.alachisoft.com/blogs/how-to-use-a-distributed-cache-for-asp-net-output-cache/)
* [Apache Antända](https://apacheignite-net.readme.io/docs/aspnet-output-caching)


## <a name="next-steps"></a>Nästa steg

Kolla in [ASP.NET Sessionstillståndsprovider för Azure Cache för Redis](cache-aspnet-session-state-provider.md).
