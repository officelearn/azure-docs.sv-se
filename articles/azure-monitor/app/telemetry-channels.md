---
title: Telemetri kanaler i Azure Application Insights | Microsoft Docs
description: 'Hur du anpassar telemetri kanaler i Azure Application Insights SDK: er för .NET och .NET Core.'
services: application-insights
documentationcenter: .net
author: cijothomas
manager: carmonm
ms.assetid: 015ab744-d514-42c0-8553-8410eef00368
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/14/2019
ms.reviewer: mbullwin
ms.author: cithomas
ms.openlocfilehash: af00641123354831c7bf174a743ded2886343579
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561352"
---
# <a name="telemetry-channels-in-application-insights"></a>Telemetri kanaler i Application Insights

Telemetri kanaler är en del av den [Azure Application Insights SDK: er](../../azure-monitor/app/app-insights-overview.md). De hanterar buffring och överföring av telemetri till Application Insights-tjänsten. .NET och .NET Core-versioner av SDK: erna har två inbyggda telemetri kanaler: `InMemoryChannel` och `ServerTelemetryChannel`. Den här artikeln beskrivs olika kanaler i detalj, inklusive hur du anpassar kanal beteende.

## <a name="what-are-telemetry-channels"></a>Vad är telemetri kanaler?

Telemetri kanaler som ansvarar för buffring telemetri objekt och skicka dem till Application Insights-tjänsten där de lagras för frågor och analys. En telemetri-kanal är alla klasser som implementerar den [ `Microsoft.ApplicationInsights.ITelemetryChannel` ](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.channel.itelemetrychannel?view=azure-dotnet) gränssnitt.

Den `Send(ITelemetry item)` anropas metoden för en telemetri-kanal när du har all telemetri-initierare och telemetri processorer kallas. Därför kommer inte alla objekt som utelämnats av en telemetri-processor nå kanalen. `Send()` inte vanligtvis skicka objekt till backend-servern direkt. Normalt buffrar dem i minnet och skickar dem i batchar för effektiv överföring.

[Live Metrics Stream](live-stream.md) har också en anpassad kanal som driver liveuppspelning av telemetri. Den här kanalen är oberoende av regelbundna telemetri-kanalen och det här dokumentet gäller inte för den.

## <a name="built-in-telemetry-channels"></a>Inbyggda telemetri kanaler

Application Insights .NET och .NET Core SDK: er levereras med två inbyggda kanaler:

* `InMemoryChannel`: En förenklad kanal som buffrar objekt i minnet tills de har skickats. Objekt som buffras i minnet och rensade var 30: e sekund, eller när 500 objekt är buffrade. Den här kanalen erbjuder garantier för minimal tillförlitlighet eftersom det inte försök skicka telemetri efter ett fel. Den här kanalen också inte spara objekt på disk, så att alla meddelanden som inte går förlorade permanent vid avstängning av program (korrekt eller inte). Den här kanalen implementerar en `Flush()` metod som kan användas för att tvinga-tömning alla objekt i minnet telemetri synkront. Den här kanalen passar bra för kortvarig program där en synkron tömning är idealiskt.

    Den här kanalen är en del av större Microsoft.ApplicationInsights NuGet-paketet och är den standardkanal som SDK: N använder när inget annat har konfigurerats.

* `ServerTelemetryChannel`: En mer avancerad kanal med principer för återförsök och möjligheten att lagra data på en lokal disk. Den här kanalen försöker skicka telemetri om tillfälliga fel inträffar. Den här kanalen använder också lokalt diskutrymme för att spara objekt på disk under nätverks- eller hög telemetri volymer. På grund av dessa metoder för återförsök och lokalt diskutrymme kommer den här kanalen anses mer tillförlitlig och rekommenderas för alla produktionsscenarier. Den här kanalen är standard för [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) och [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) program som är konfigurerade enligt den officiella dokumentationen. Den här kanalen är optimerat för scenarier med tidskrävande processer. Den [ `Flush()` ](#which-channel-should-i-use) metod som implementeras av den här kanalen är inte synkron.

    Den här kanalen levereras som Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel NuGet-paketet och förvärvas automatiskt när du använder antingen Microsoft.ApplicationInsights.Web eller Microsoft.ApplicationInsights.AspNetCore NuGet paketet.

## <a name="configure-a-telemetry-channel"></a>Konfigurera en telemetri-kanal

Du kan konfigurera en kanal för telemetri genom att ange den aktiva telemetri-konfigurationen. För ASP.NET-program, konfigurationen omfattar att ställa in telemetri channel-instans `TelemetryConfiguration.Active`, eller genom att ändra `ApplicationInsights.config`. För ASP.NET Core-program måste konfigurationen du lägga till kanalen till behållaren beroende inmatning.

I följande avsnitt visas exempel på hur du konfigurerar den `StorageFolder` för kanalen i olika programtyper. `StorageFolder` är bara en av de konfigurerbara inställningarna. En fullständig lista över inställningar, se [avsnittet](telemetry-channels.md#configurable-settings-in-channels) senare i den här artikeln.

### <a name="configuration-by-using-applicationinsightsconfig-for-aspnet-applications"></a>Konfiguration med hjälp av ApplicationInsights.config för ASP.NET-program

Följande avsnitt från [ApplicationInsights.config](configuration-with-applicationinsights-config.md) visar den `ServerTelemetryChannel` channel som konfigurerats med `StorageFolder` inställd på en anpassad plats:

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

### <a name="configuration-in-code-for-aspnet-applications"></a>Konfigurationen i koden för ASP.NET-program

Följande kod ställer in en 'ServerTelemetryChannel'-instans med `StorageFolder` inställd på en anpassad plats. Lägg till den här koden i början av programmet, vanligtvis i `Application_Start()` -metod i Global.aspx.cs.

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

### <a name="configuration-in-code-for-aspnet-core-applications"></a>Konfigurationen i koden för ASP.NET Core-program

Ändra den `ConfigureServices` -metoden för den `Startup.cs` klassen som visas här:

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
> Konfigurera kanalen med `TelemetryConfiguration.Active` rekommenderas inte för ASP.NET Core-program.

### <a name="configuration-in-code-for-netnet-core-console-applications"></a>Konfigurationen i koden för.NET/.NET Core-konsolprogram

För konsolappar är koden detsamma för både .NET och .NET Core:

```csharp
var serverTelemetryChannel = new ServerTelemetryChannel();
serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
```

## <a name="operational-details-of-servertelemetrychannel"></a>För ett ServerTelemetryChannel

`ServerTelemetryChannel` butiker som inkommer objekt i en InMemory-buffert. Objekten serialiseras, komprimeras och lagras i en `Transmission` instans en gång med 30 sekunders mellanrum, eller när buffras 500 objekt. En enda `Transmission` instans innehåller upp till 500 objekt och representerar en batch med telemetri som har skickats via ett enda HTTPS-anrop till Application Insights-tjänsten.

Som standard högst 10 `Transmission` instanser kan skickas parallellt. Om telemetri kommer priserna för snabbare, eller om nätverket eller Application Insights tillbaka slutet är långsam, `Transmission` instanser lagras i minnet. Standardkapaciteten på den här minnesinterna `Transmission` bufferten är 5 MB. När InMemory-kapacitet har överskridits, `Transmission` instanser som är lagrade på lokal disk upp till högst 50 MB. `Transmission` instanser lagras på den lokala disken även när det finns nätverksproblem. Bara de objekt som lagras på en lokal disk överleva en program-krascher. De skickas varje gång programmet startas igen.

## <a name="configurable-settings-in-channels"></a>Konfigurerbara inställningar i kanaler

En fullständig lista över konfigurerbara inställningar för varje kanal finns:

* [InMemoryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/src/Microsoft.ApplicationInsights/Channel/InMemoryChannel.cs)

* [ServerTelemetryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/src/ServerTelemetryChannel/ServerTelemetryChannel.cs)

Här är de vanligaste inställningarna för `ServerTelemetryChannel`:

1. `MaxTransmissionBufferCapacity`: Den maximala mängden minne i bytes som används av kanal till bufferten överföringar i minnet. När den här kapaciteten har uppnåtts kan lagras nya objekt direkt till lokal disk. Standardvärdet är 5 MB. Ange ett högre värde leder till mindre diskanvändning, men kom ihåg att objekt i minnet kommer att försvinna om programmet kraschar.

1. `MaxTransmissionSenderCapacity`: Det maximala antalet `Transmission` instanser som ska skickas till Application Insights på samma gång. Standardvärdet är 10. Den här inställningen kan konfigureras till ett större antal, vilket rekommenderas när en stor mängd telemetri som genereras. Hög volym normalt under belastningstest eller när sampling är avstängd.

1. `StorageFolder`: Mappen som används av kanalen för att lagra objekten till disken efter behov. I Windows används % LOCALAPPDATA % eller % TEMP % om inga andra sökväg anges uttryckligen. I andra miljöer än Windows, måste du ange en giltig plats eller telemetri inte lagras till lokal disk.

## <a name="which-channel-should-i-use"></a>Vilken kanal ska jag använda?

`ServerTelemetryChannel` rekommenderas för de flesta produktionsscenarier som involverar tidskrävande program. Den `Flush()` metoden implementeras av `ServerTelemetryChannel` inte synkron, och det inte garanterar också skicka alla väntande objekt från minne eller diskutrymme. Om du använder den här kanalen i scenarier där programmet är på väg att stänga av, rekommenderar vi att du lägger till en fördröjning när du anropar `Flush()`. Den exakta mängden fördröjning som du kan behöva är inte förutsägbart. Det beror på faktorer som hur många objekt eller `Transmission` instanser är i minnet, hur många finns på disken, hur många sänds till backend-server och om kanalen är mitt exponentiell backoff-scenarier.

Om du behöver göra en synkron tömning rekommenderar vi att du använder `InMemoryChannel`.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="does-the-application-insights-channel-guarantee-telemetry-delivery-if-not-what-are-the-scenarios-in-which-telemetry-can-be-lost"></a>Garanterar leverans av telemetri i Application Insights-kanalen? Om inte, vilka är de scenarier som telemetri kan gå förlorade?

Kort svaret är att ingen av de inbyggda kanalerna garanterar typ av telemetri leverans till backend-servern. `ServerTelemetryChannel` är mer avancerade jämfört med med `InMemoryChannel` för pålitlig leverans, men det gör också att bara ett bästa försök att skicka telemetri. Telemetri kan i flera fall, inklusive dessa vanliga scenarier fortfarande förlorade:

1. Objekt i minnet försvinner när programmet kraschar.

1. Telemetri förloras under en längre period av nätverksproblem. Telemetri lagras till lokal disk under avbrott i nätverket eller när det uppstår problem med Application Insights-serverdel. Dock ignoreras objekt som är äldre än 24 timmar.

1. Standardplatserna för disken för att lagra telemetri i Windows är % LOCALAPPDATA % eller % TEMP %. De här platserna är vanligtvis lokalt på datorn. Om programmet migreras fysiskt från en plats till ett annat, förloras alla telemetri som lagras i den ursprungliga platsen.

1. Standardplatsen för disk-lagring är D:\local\LocalAppData i Web Apps på Windows. Den här platsen är inte beständiga. Den rensar i appen startas om, skala detaljer och andra sådana åtgärder, vilket leder till förlust av någon telemetri lagras där. Du kan åsidosätta standardinställningen och ange lagring till en bestående plats som D:\home. Sådana beständiga platser betjänas av Fjärrlagring och därför kan ta lång tid.

### <a name="does-servertelemetrychannel-work-on-systems-other-than-windows"></a>Fungerar ServerTelemetryChannel på andra system än Windows?

Även om namnet på dess paketet och namnområde innehåller ”WindowsServer”, stöds den här kanalen på andra system än Windows, med följande undantag. Kanalen skapa inte en mapp för lokal lagring som standard på andra system än Windows. Du måste skapa en mapp för lokal lagring och konfigurera kanal för att använda den. När lokal lagring har konfigurerats, fungerar på samma sätt på alla datorer i kanalen.

### <a name="does-the-sdk-create-temporary-local-storage-is-the-data-encrypted-at-storage"></a>Skapar SDK tillfälligt lokal lagring? Krypteras data i lagring?

SDK: N lagrar telemetri-objekt i lokal lagring under nätverksproblem eller under begränsning. Dessa data är inte krypterad lokalt.

Windows-system använder SDK: N skapas automatiskt en temporär lokal mapp i % TEMP % eller % LOCALAPPDATA % directory och begränsar åtkomsten till administratörer och den aktuella användaren.

Någon lokal lagringsenhet skapas automatiskt av SDK för andra system än Windows, och därför inga data lagras lokalt som standard. Du kan skapa en Arkivkatalog själv och konfigurera kanal för att använda den. I det här fallet är du ansvarig för att säkerställa att katalogen är skyddad.
Läs mer om [dataskydd och sekretess](data-retention-privacy.md#does-the-sdk-create-temporary-local-storage).

## <a name="open-source-sdk"></a>Öppen källkod-SDK
Kanaler är öppen källkod som varje SDK för Application Insights. Läsa och bidra till kod eller rapportera problem vid [officiella GitHub-lagringsplatsen](https://github.com/Microsoft/ApplicationInsights-dotnet).

## <a name="next-steps"></a>Nästa steg

* [Sampling](../../azure-monitor/app/sampling.md)
* [Felsökning av SDK](../../azure-monitor/app/asp-net-troubleshoot-no-data.md)
