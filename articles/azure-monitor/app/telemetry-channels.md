---
title: Telemetrikanaler i Azure Application Insights | Microsoft-dokument
description: Anpassa telemetrikanaler i Azure Application Insights SDK:er för .NET och .NET Core.
ms.topic: conceptual
ms.date: 05/14/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 9c292246f947e4d3a364f79b31fe7a1deebd33d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275703"
---
# <a name="telemetry-channels-in-application-insights"></a>Telemetrikanaler i Application Insights

Telemetrikanaler är en integrerad del av [SDK:erna för Azure Application Insights](../../azure-monitor/app/app-insights-overview.md). De hanterar buffring och överföring av telemetri till application insights-tjänsten. .NET- och .NET Core-versionerna av SDK:erna `InMemoryChannel` har `ServerTelemetryChannel`två inbyggda telemetrikanaler: och . I den här artikeln beskrivs varje kanal i detalj, inklusive hur du anpassar kanalbeteendet.

## <a name="what-are-telemetry-channels"></a>Vad är telemetrikanaler?

Telemetrikanaler ansvarar för buffring av telemetriartiklar och skicka dem till application insights-tjänsten, där de lagras för frågor och analys. En telemetrikanal är en klass [`Microsoft.ApplicationInsights.ITelemetryChannel`](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.channel.itelemetrychannel?view=azure-dotnet) som implementerar gränssnittet.

Metoden `Send(ITelemetry item)` för en telemetrikanal anropas efter att alla telemetriinitierare och telemetriprocessorer anropas. Så, några objekt som tappas av en telemetriprocessor kommer inte att nå kanalen. `Send()`vanligtvis inte skicka objekten till baksidan direkt. Vanligtvis buffrar det dem i minnet och skickar dem i omgångar, för effektiv överföring.

[Live Metrics Stream](live-stream.md) har också en anpassad kanal som driver live streaming av telemetri. Den här kanalen är oberoende av den vanliga telemetrikanalen och det här dokumentet gäller inte för den.

## <a name="built-in-telemetry-channels"></a>Inbyggda telemetrikanaler

Application Insights .NET och .NET Core SDKs levereras med två inbyggda kanaler:

* `InMemoryChannel`: En lätt kanal som buffrar objekt i minnet tills de skickas. Objekt buffras i minnet och spolas en gång var 30:e sekund, eller när 500 objekt buffras. Den här kanalen ger minimala tillförlitlighetsgarantier eftersom den inte försöker skicka telemetri igen efter ett fel. Den här kanalen håller inte heller objekt på disken, så alla objekt som inte har gått förlorade permanent vid avstängning av programmet (graciösa eller inte). Den här kanalen `Flush()` implementerar en metod som kan användas för att tvinga fram all in-memory telemetri objekt synkront. Den här kanalen lämpar sig väl för kortkörningsprogram där en synkron spolning är idealisk.

    Den här kanalen är en del av det större Microsoft.ApplicationInsights NuGet-paketet och är standardkanalen som SDK använder när inget annat är konfigurerat.

* `ServerTelemetryChannel`: En mer avancerad kanal som har principer för återförsök och möjlighet att lagra data på en lokal disk. Den här kanalen försöker skicka telemetri igen om tillfälliga fel uppstår. Den här kanalen använder också lokal disklagring för att hålla objekt på disken under nätverksavbrott eller stora telemetrivolymer. På grund av dessa återförsöksmekanismer och lokal disklagring anses den här kanalen vara mer tillförlitlig och rekommenderas för alla produktionsscenarier. Den här kanalen är standard för [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) och [ASP.NET Core-program](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) som är konfigurerade enligt den officiella dokumentationen. Den här kanalen är optimerad för serverscenarier med tidskrävande processer. Metoden [`Flush()`](#which-channel-should-i-use) som implementeras av den här kanalen är inte synkron.

    Den här kanalen levereras som Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel NuGet-paketet och förvärvas automatiskt när du använder microsoft.applicationInsights.Web eller Microsoft.ApplicationInsights.AspNetCore NuGet Paket.

## <a name="configure-a-telemetry-channel"></a>Konfigurera en telemetrikanal

Du konfigurerar en telemetrikanal genom att ställa in den till den aktiva telemetrikonfigurationen. För ASP.NET program innebär konfigurationen att `TelemetryConfiguration.Active`telemetrikanalinstansen ska `ApplicationInsights.config`anges eller genom att ändra . För ASP.NET Core-program innebär konfigurationen att kanalen läggs till i behållaren för beroendeinsprutning.

I följande avsnitt visas exempel på `StorageFolder` hur du konfigurerar inställningen för kanalen i olika programtyper. `StorageFolder`är bara en av de konfigurerbara inställningarna. En fullständig lista över konfigurationsinställningar finns [i inställningsavsnittet](telemetry-channels.md#configurable-settings-in-channels) senare i den här artikeln.

### <a name="configuration-by-using-applicationinsightsconfig-for-aspnet-applications"></a>Konfiguration med ApplicationInsights.config för ASP.NET program

I följande avsnitt från [ApplicationInsights.config](configuration-with-applicationinsights-config.md) visas den `ServerTelemetryChannel` kanal som konfigurerats med inställd på `StorageFolder` en anpassad plats:

```xml
    <TelemetrySinks>
        <Add Name="default">
            <TelemetryProcessors>
                <!-- Telemetry processors omitted for brevity  -->
            </TelemetryProcessors>
            <TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel, Microsoft.AI.ServerTelemetryChannel">
                <StorageFolder>d:\temp\applicationinsights</StorageFolder>
            </TelemetryChannel>
        </Add>
    </TelemetrySinks>
```

### <a name="configuration-in-code-for-aspnet-applications"></a>Konfiguration i kod för ASP.NET program

Följande kod ställer in en "ServerTelemetryChannel"-instans med `StorageFolder` inställd på en anpassad plats. Lägg till den här koden i `Application_Start()` början av programmet, vanligtvis metod i Global.aspx.cs.

```csharp
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
protected void Application_Start()
{
    var serverTelemetryChannel = new ServerTelemetryChannel();
    serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
    serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
    TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
}
```

### <a name="configuration-in-code-for-aspnet-core-applications"></a>Konfiguration i kod för ASP.NET Core-program

Ändra `ConfigureServices` metoden för `Startup.cs` klassen som visas här:

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

public void ConfigureServices(IServiceCollection services)
{
    // This sets up ServerTelemetryChannel with StorageFolder set to a custom location.
    services.AddSingleton(typeof(ITelemetryChannel), new ServerTelemetryChannel() {StorageFolder = @"d:\temp\applicationinsights" });

    services.AddApplicationInsightsTelemetry();
}

```

> [!IMPORTANT]
> Det rekommenderas inte att `TelemetryConfiguration.Active` konfigurera kanalen med hjälp av ASP.NET Core-program.

### <a name="configuration-in-code-for-netnet-core-console-applications"></a>Konfiguration i kod för .NET/.NET Core-konsolprogram

För konsolappar är koden densamma för både .NET och .NET Core:

```csharp
var serverTelemetryChannel = new ServerTelemetryChannel();
serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
```

## <a name="operational-details-of-servertelemetrychannel"></a>Operativa detaljer om ServerTelemetryChannel

`ServerTelemetryChannel`lagrar ankommande artiklar i en minnesbuffert. Objekten serialiseras, komprimeras och `Transmission` lagras i en instans en gång var 30:e sekund, eller när 500 objekt har buffrats. En `Transmission` enskild instans innehåller upp till 500 artiklar och representerar en batch med telemetri som skickas via ett enda HTTPS-anrop till application insights-tjänsten.

Som standard kan högst 10 `Transmission` instanser skickas parallellt. Om telemetrin kommer med snabbare hastigheter, eller om nätverket eller application `Transmission` insights-serverdelen är långsam, lagras instanser i minnet. Standardkapaciteten för den `Transmission` här minnesbufferten är 5 MB. När minneskapaciteten har överskridits `Transmission` lagras instanser på lokal disk upp till en gräns på 50 MB. `Transmission`instanser lagras på lokal disk även när det finns nätverksproblem. Endast de objekt som lagras på en lokal disk överlever en programkrasch. De skickas när ansökan startar igen.

## <a name="configurable-settings-in-channels"></a>Konfigurerbara inställningar i kanaler

En fullständig lista över konfigurerbara inställningar för varje kanal finns i:

* [InMemoryChannel (InMemoryChannel)](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/BASE/src/Microsoft.ApplicationInsights/Channel/InMemoryChannel.cs)

* [ServerTelemetryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/BASE/src/ServerTelemetryChannel/ServerTelemetryChannel.cs)

Här är de vanligaste inställningarna för: `ServerTelemetryChannel`

1. `MaxTransmissionBufferCapacity`: Den maximala mängden minne, i byte, som används av kanalen för att buffra överföringar i minnet. När den här kapaciteten har uppnåtts lagras nya objekt direkt på lokal disk. Standardvärdet är 5 MB. Om du anger ett högre värde leder det till mindre diskanvändning, men kom ihåg att objekt i minnet går förlorade om programmet kraschar.

1. `MaxTransmissionSenderCapacity`: Det maximala `Transmission` antalet instanser som skickas till Application Insights samtidigt. Standardvärdet är 10. Den här inställningen kan konfigureras till ett högre antal, vilket rekommenderas när en stor mängd telemetri genereras. Hög volym inträffar vanligtvis under belastningstestning eller när provtagningen är avstängd.

1. `StorageFolder`: Mappen som används av kanalen för att lagra objekt på disk efter behov. I Windows används antingen %LOCALAPPDATA% eller %TEMP% om ingen annan sökväg uttryckligen anges. I andra miljöer än Windows måste du ange en giltig plats eller telemetri ska inte lagras på lokal disk.

## <a name="which-channel-should-i-use"></a>Vilken kanal ska jag använda?

`ServerTelemetryChannel`rekommenderas för de flesta produktionsscenarier som involverar tidskrävande program. Den `Flush()` metod som `ServerTelemetryChannel` implementeras av är inte synkron, och det garanterar inte heller att skicka alla väntande objekt från minne eller disk. Om du använder den här kanalen i scenarier där programmet är på väg `Flush()`att stängas av rekommenderar vi att du inför en viss fördröjning efter att du har ringt . Den exakta förseningen som du kan behöva är inte förutsägbar. Det beror på faktorer som `Transmission` hur många objekt eller instanser som finns i minnet, hur många som finns på disken, hur många som överförs till serverdelen och om kanalen är mitt i exponentiell back-off-scenarier.

Om du behöver göra en synkron färg, `InMemoryChannel`rekommenderar vi att du använder .

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="does-the-application-insights-channel-guarantee-telemetry-delivery-if-not-what-are-the-scenarios-in-which-telemetry-can-be-lost"></a>Garanterar kanalen Application Insights telemetrileverans? Om inte, vilka scenarier kan telemetri gå förlorade?

Det korta svaret är att ingen av de inbyggda kanalerna erbjuder en transaktionsgaranti för telemetrileverans till backend. `ServerTelemetryChannel`är mer avancerad `InMemoryChannel` jämfört med för tillförlitlig leverans, men det gör också bara ett bästa försök att skicka telemetri. Telemetri kan fortfarande gå förlorad i flera situationer, inklusive dessa vanliga scenarier:

1. Objekt i minnet går förlorade när programmet kraschar.

1. Telemetri går förlorad under längre perioder av nätverksproblem. Telemetri lagras på lokal disk under nätverksavbrott eller när problem uppstår med Application Insights-backend. Objekt som är äldre än 48 timmar ignoreras dock.

1. Standarddiskplatserna för lagring av telemetri i Windows är %LOCALAPPDATA% eller %TEMP%. Dessa platser är vanligtvis lokala för datorn. Om programmet migreras fysiskt från en plats till en annan går all telemetri som lagras på den ursprungliga platsen förlorad.

1. I Web Apps i Windows är standardplatsen för disklagring D:\local\LocalAppData. Den här platsen sparas inte. Det är utplånat i app omstarter, utskalning och andra sådana åtgärder, vilket leder till förlust av telemetri som lagras där. Du kan åsidosätta standardlagringen och ange lagring till en plats som sparats på samma plats som D:\home. Sådana kvarstående platser betjänas dock av fjärrlagring och kan därför vara långsamma.

### <a name="does-servertelemetrychannel-work-on-systems-other-than-windows"></a>Fungerar ServerTelemetryChannel på andra system än Windows?

Även om namnet på paketet och namnområdet innehåller "WindowsServer", stöds den här kanalen på andra system än Windows, med följande undantag. På andra system än Windows skapar kanalen inte en lokal lagringsmapp som standard. Du måste skapa en lokal lagringsmapp och konfigurera kanalen så att den kan användas. När lokal lagring har konfigurerats fungerar kanalen på samma sätt på alla system.

### <a name="does-the-sdk-create-temporary-local-storage-is-the-data-encrypted-at-storage"></a>Skapar SDK tillfällig lokal lagring? Är data krypterade vid lagring?

SDK lagrar telemetriartiklar i lokal lagring under nätverksproblem eller under begränsning. Dessa data krypteras inte lokalt.

För Windows-system skapar SDK automatiskt en tillfällig lokal mapp i katalogen %TEMP% eller %LOCALAPPDATA% och begränsar åtkomsten till administratörer och den aktuella användaren.

För andra system än Windows skapas ingen lokal lagring automatiskt av SDK och därför lagras inga data lokalt som standard. Du kan själv skapa en lagringskatalog och konfigurera kanalen så att den kan användas. I det här fallet är du ansvarig för att katalogen är säkrad.
Läs mer om [dataskydd och sekretess](data-retention-privacy.md#does-the-sdk-create-temporary-local-storage).

## <a name="open-source-sdk"></a>SDK med öppen källkod
Precis som alla SDK för application insights är kanalerna öppen källkod. Läs och bidra till koden, eller rapportera problem, på [den officiella GitHub repo](https://github.com/Microsoft/ApplicationInsights-dotnet).

## <a name="next-steps"></a>Nästa steg

* [Samling](../../azure-monitor/app/sampling.md)
* [SDK-felsökning](../../azure-monitor/app/asp-net-troubleshoot-no-data.md)
