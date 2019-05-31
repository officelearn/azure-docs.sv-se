---
title: Telemetri kanaler i Azure Application Insights | Microsoft Docs
description: 'Hur du anpassar telemetri kanaler i Azure Application Insights SDK: er för.NET/.NET Core.'
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
ms.openlocfilehash: fa99e29e9ec6c2bef7296a50dd381ee5fc60a1cb
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66255810"
---
# <a name="telemetrychannel-in-application-insights"></a>TelemetryChannel i Application Insights

TelemetryChannel är en del av [Azure Application Insights SDK: er](../../azure-monitor/app/app-insights-overview.md). Den hanterar buffring och överföring av telemetri till Application Insights-tjänsten. .NET och .NET Core-versioner av SDK: erna har två inbyggda TelemetryChannels - `InMemoryChannel` och `ServerTelemetryChannel`. Den här artikeln beskrivs olika kanaler i detalj, inklusive hur användare kan anpassa kanal beteende.

## <a name="what-is-a-telemetrychannel"></a>Vad är en TelemetryChannel?

`TelemetryChannel` ansvarar för buffring och skicka telemetri objekt till Application Insights-tjänsten var den lagras för frågor och analys. Det är en klass som implementerar gränssnittet [`Microsoft.ApplicationInsights.ITelemetryChannel`](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.channel.itelemetrychannel?view=azure-dotnet)

Den `Send(ITelemetry item)` metoden för TelemetryChannel anropas när alla `TelemetryInitializer`s och `TelemetryProcessor`s anropas. Detta innebär att alla objekt som utelämnats av `TelemetryProcessor` inte nå kanalen. `Send()` Vanligtvis skickar inte objekten direkt till serverdelen. De är vanligtvis buffrade i minnet och skickas i batchar för en effektiv överföring.

[LiveMetrics](live-stream.md) har också en anpassad kanal som driver liveuppspelning av telemetri. Den här kanalen är oberoende av regelbundna telemetri-kanalen och det här dokumentet gäller inte för den kanal som används av `LiveMetrics`.

## <a name="built-in-telemetrychannels"></a>Inbyggda TelemetryChannels

Application Insights.NET/.NET Core SDK levereras med två inbyggda kanaler:

* **InMemoryChannel** 
 `InMemoryChannel` är en lätt kanal, vilket buffrar objekt i minnet tills det har skickats. Objekt som buffras i minnet och rensade var 30: e sekund eller varje gång 500 objekt har buffras. Den här kanalen ger minimal tillförlitlighet garantier när det inte försök skicka telemetri vid fel. Den här kanalen Behåll inte objekten på disk, så att alla meddelanden som inte går förlorade permanent vid avstängning av program (utan problem eller inte). Det finns en `Flush()` metod som implementeras av den här kanalen som kan användas för att tvinga-tömning alla objekt i minnet telemetri synkront. Detta är bra för kortvarig program där en synkron tömning är idealiskt.

    Den här kanalen levereras som en del av den `Microsoft.ApplicationInsights` nuget paketera själva och är standardkanal SDK använder när inget annat har konfigurerats.

* **ServerTelemetryChannel** 
 `ServerTelemetryChannel` är en mer avancerad kanal som innehåller principer för återförsök och möjlighet att lagra data på den lokala disken. Den här kanalen försöker skicka telemetri om tillfälliga fel inträffar. Den här kanalen använder också lokalt diskutrymme för att spara objekt på disk under nätverks- eller hög telemetri volymer. På grund av dessa metoder för återförsök och lokalt diskutrymme kommer den här kanalen anses mer tillförlitlig och rekommenderas för alla produktionsscenarier. Den här kanalen är standard för [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) och [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) program, som är konfigurerade enligt länkade officiella dokumenten. Den här kanalen är optimerat för scenarier av tidskrävande processer. Den [ `Flush()` ](#which-channel-should-i-use) metod som implementeras av den här kanalen är inte synkron.

    Den här kanalen levereras som NuGet-paketet `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel`, och är online automatiskt när du använder något av NuGet-paketen `Microsoft.ApplicationInsights.Web` eller `Microsoft.ApplicationInsights.AspNetCore`.

## <a name="configuring-telemetrychannel"></a>Konfigurera TelemetryChannel

Telemetri kanal kan konfigureras genom att ange den önskade `TelemetryChannel` om aktivt `TelemetryConfiguration`. För Asp.Net-program, konfigurationen omfattar inställningen `TelemetryChannel` på `TelemetryConfiguration.Active`, eller ändra `ApplicationInsights.config`. För ASP.NET Core-program måste konfigurationen du lägga till önskade kanalen till behållaren beroende inmatning.

Följande visar ett exempel där användaren konfigurerar det `StorageFolder` för kanalen. `StorageFolder` är bara en av de konfigurerbara inställningarna. En fullständig lista över inställningar beskrivs [i avsnittet inställningar](telemetry-channels.md#configurable-settings-in-channel).

## <a name="configuration-using-applicationinsightsconfig-for-aspnet-applications"></a>Konfiguration med hjälp av ApplicationInsights.Config för ASP.NET-program

Följande avsnitt från [ApplicationInsights.config](configuration-with-applicationinsights-config.md) visar ServerTelemetryChannel som konfigurerats med `StorageFolder` inställd på en anpassad plats.

```xml
    <TelemetrySinks>
        <Add Name="default">
            <TelemetryProcessors>
                <!--TelemetryProcessors omitted for brevity  -->
            </TelemetryProcessors>
            <TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel, Microsoft.AI.ServerTelemetryChannel">
                <StorageFolder>d:\temp\applicationinsights</StorageFolder>
            </TelemetryChannel>
        </Add>
    </TelemetrySinks>
```

## <a name="configuration-in-code-for-aspnet-applications"></a>Konfigurationen i koden för ASP.NET-program

Följande kod konfigurerar ServerTelemetryChannel med `StorageFolder` inställd på en anpassad plats. Den här koden ska läggas till i början av program, vanligtvis i Application_Start()-metod i `Global.aspx.cs`

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

## <a name="configuration-in-code-for-aspnet-core-applications"></a>Konfigurationen i koden för ASP.NET Core-program

Ändra `ConfigureServices` -metoden för `Startup.cs` klassen som visas nedan.

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

> [!NOTE]
> Det är viktigt att Observera att konfigurera en kanal med hjälp av `TelemetryConfiguration.Active` rekommenderas inte för ASP.NET Core-program.

## <a name="configuring-telemetrychannel-in-code-for-netnet-core-console-applications"></a>Konfigurera TelemetryChannel i koden för.NET/.NET Core-konsolprogram

Koden för konsolappar är samma för .NET och .NET Core och visas nedan.

```csharp
var serverTelemetryChannel = new ServerTelemetryChannel();
serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
```

## <a name="operational-details-of-servertelemetrychannel"></a>För ett ServerTelemetryChannel

Den `ServerTelemetryChannel` buffrar inkommande objekt i en InMemory-buffert. Den serialiserade, komprimeras och lagras i `Transmission` instans när varje 30 sekunder eller när 500 objekt är buffrade. En enda `Transmission` instans innehåller upp till 500 objekt och representerar en batch med telemetri som skickas via ett enda https-anrop till Application Insights-tjänsten. Som standard, det kan finnas högst 10 `Transmission`s skickas parallellt. Om telemetri kommer priserna för snabbare eller om serverdelen för nätverk/Application Insights är långsam, `Transmission`s lagras i minnet. Standardkapaciteten på InMemory-överföringsbufferten är 5 MB. När i minneskapacitet överskrider `Transmission`s lagras på den lokala disken för upp till 50 MB. `Transmission`s lagras på den lokala disken när samt nätverksproblem. Endast de objekt som lagras i den lokala disken överleva en program-krascher som skickas när programmet startas igen.

## <a name="configurable-settings-in-channel"></a>Konfigurerbara inställningar i kanal

En fullständig lista över konfigurerbara inställningar för varje kanal finns här:

[InMemoryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/src/Microsoft.ApplicationInsights/Channel/InMemoryChannel.cs)

[ServerTelemetryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/src/ServerTelemetryChannel/ServerTelemetryChannel.cs)

De vanligaste inställningarna för `ServerTelemetryChannel` finns nedan:

1. `MaxTransmissionBufferCapacity` – Maximal mängd minne i byte som används av kanal till bufferten överföringar i minnet. Nya objekt kommer att lagras direkt till lokala diskar när den här kapaciteten har nåtts. Standardvärdet är 5 MB. Ange ett högre värde leads till mindre diskanvändning, men det är viktigt att notera att objekt i minnet går förlorade om programmet kraschar.

2. `MaxTransmissionSenderCapacity` – Maximal mängd `Transmission`s som ska skickas till Application Insights på samma gång. Standardvärdet är 10, men den kan konfigureras för att en hög siffra. Detta rekommenderas när stora volymer av telemetri skapas, vanligtvis vid belastningstest och/eller när sampling är avstängd.

3. `StorageFolder` -Mappen som används av kanalen för att lagra objekten till disken efter behov. I Windows används % LocalAppData % eller % Temp % om inget uttryckligen har konfigurerats. I miljöer med icke-Windows användaren **måste** konfigurera en giltig plats, utan där telemetri inte lagras till lokal disk.

## <a name="which-channel-should-i-use"></a>Vilken kanal ska jag använda?

`ServerTelemetryChannel` rekommenderas för de flesta produktionsscenarier av program som körs för länge. Den `Flush()` metodimplementering av `ServerTelemetryChannel` är inte synkron, och `Flush()` garanterar inte skicka alla väntande objekt från minne/disk. Om den här kanalen används i scenarier där programmet är håller på att stänga av, så rekommenderar vi att du gör ett tag sedan anropa `Flush()` i den här kanalen. Den exakta mängden fördröjning som krävs är inte förutsägbart, eftersom det beror på faktorer som hur många objekt eller `Transmissions` är i minnet och hur många finns i disk, hur många som överförs till säkerhetskopieras, och om kanalen är mitt exponentiell backoff-scenarier. Om det finns ett behov av att göra synkron tömning sedan `InMemoryChannel` rekommenderas.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="does-applicationinsights-channel-offer-guaranteed-telemetry-delivery-or-what-are-the-scenarios-where-telemetry-can-be-lost"></a>*Erbjuder ApplicationInsights kanal telemetri garanterad leverans eller vilka är de scenarier där telemetri kan gå förlorade?*

* Kort svaret är ingen av de inbyggda kanalerna erbjuder transaktion typ garanti om leverans av telemetri till serverdelen. Medan `ServerTelemetryChannel` är mer avancerade jämfört med `InMemoryChannel` för leverans av tillförlitliga telemetri, gör det också ett bästa försök att skicka telemetri och telemetri fortfarande kan gå förlorade i flera scenarier. Några vanliga scenarier där telemetri förloras är:

1. Objekt i minnet försvinner när programmet kraschar.
1. Telemetri sparas till lokal disk under avbrott i nätverket eller problem med Application Insights-serverdelen. Dock ignoreras objekt som är äldre än 24 timmar. Så förloras telemetri under en längre period av nätverksproblem.
1. Standardplatserna för disken för att lagra telemetri i Windows är % LocalAppData % eller % Temp %. De här platserna är vanligtvis lokalt på datorn. Om programmet migreras fysiskt från en plats till ett annat, förloras alla telemetri som lagras i den här platsen.
1. I Azure Web Apps (Windows) är standardplatsen för disk ”D:\local\LocalAppData”. Den här platsen är inte beständiga och rensar appen startas om, skala detaljer och så vidare, vilket leder till förlust av telemetri som lagras på dessa platser. Användare kan åsidosätta lagring till en bestående plats som ”D:\home”, men de här beständiga platserna nedanför betjänas av Fjärrlagring och kan vara långsam.

### <a name="does-servertelemetrychannel-work-in-non-windows-systems"></a>*Fungerar ServerTelemetryChannel i icke-Windows-System?*

* Trots namnet på paketet/namnområdet som Windows Server, stöds den här kanalen i icke-Windows-system med följande undantag. I icke-Windows skapar inte kanalen en lokal mapp som standard. Användarna måste skapa en mapp för lokal lagring och konfigurera kanal för att använda den. När du har konfigurerat lokal lagring fungerar kanalen samma i Windows och icke-Windows-System.

### <a name="does-the-sdk-create-temporary-local-storage-is-the-data-encrypted-at-storage"></a>*Skapar SDK tillfälligt lokal lagring? Krypteras data i lagring?*

* SDK lagrar telemetri-objekt i lokal lagring under nätverksproblem eller under begränsning. Dessa data krypteras inte lokalt.
Windows-system använder SDK: N skapas automatiskt en temporär lokal mapp i TEMP eller APPDATA och begränsar åtkomsten till administratörer och den aktuella användaren.
Någon lokal lagringsenhet skapas automatiskt av SDK för icke-Windows, och därför inga data lagras lokalt som standard. Användare kan skapa en Arkivkatalog sig själva och konfigurera kanal för att använda den. I det här fallet ansvarar användaren för att säkerställa att den här katalogen är skyddad.
Läs mer om [dataskydd och sekretess](data-retention-privacy.md#does-the-sdk-create-temporary-local-storage).

## <a name="open-source-sdk"></a>Öppen källkod-SDK
Kanaler är även öppen källkod som varje Application Insights SDK. Läsa och bidra till koden eller rapportera problem vid [officiella GitHub-lagringsplatsen](https://github.com/Microsoft/ApplicationInsights-dotnet).

## <a name="next-steps"></a>Nästa steg

* [Sampling](../../azure-monitor/app/sampling.md)
* [Felsökning av SDK](../../azure-monitor/app/asp-net-troubleshoot-no-data.md)
