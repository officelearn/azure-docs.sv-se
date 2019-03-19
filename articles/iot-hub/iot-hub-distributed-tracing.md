---
title: 'Lägg till Korrelations-ID: N i IoT-meddelanden med distribuerad spårning (förhandsversion)'
description: ''
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: jlian
ms.openlocfilehash: 0553bd904cfaabaefce4e6ab3f7fbf5d356922d3
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58100368"
---
# <a name="trace-azure-iot-device-to-cloud-messages-with-distributed-tracing-preview"></a>Spårningsmeddelanden Azure IoT-enhet till molnet med distribuerad spårning (förhandsversion)

Microsoft Azure IoT Hub stöder för närvarande distribuerad spårning som en [förhandsgranskningsfunktion](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

IoT Hub är en av de första Azure-tjänsterna för distribuerad spårning. Eftersom flera Azure-tjänster stöder distribuerad spårning, kommer du att kunna IoT spårningsmeddelanden i hela Azure-tjänster som ingår i din lösning. En bakgrundsinformation distribuerad spårning finns [distribuerad spårning](../azure-monitor/app/distributed-tracing.md).

Aktivera distribuerad spårning för IoT Hub ger dig möjlighet att:

- Exakt övervaka flödet av varje meddelande via IoT Hub med [trace kontext](https://github.com/w3c/trace-context). Den här spårningen kontexten innehåller Korrelations-ID: N som gör det möjligt att korrelera händelser från en komponent med händelser från en annan komponent. Det kan användas för en delmängd eller alla meddelanden från IoT enheten med hjälp av [enhetstvillingen](iot-hub-devguide-device-twins.md).
- Logga automatiskt att spåra kontexten [diagnostikloggar för Azure Monitor](iot-hub-monitor-resource-health.md).
- Mäta och förstå meddelande flödet och svarstid från enheter till IoT Hub och routning slutpunkter.
- Starta överväger hur du vill implementera distribuerad spårning för icke-Azure-tjänster i din IoT-lösning.

I den här artikeln använder du den [Azure IoT-enhetens SDK för C](./iot-hub-device-sdk-c-intro.md) med distribuerad spårning. Stöd för distribuerad spårning pågår fortfarande för de andra SDK: er.

## <a name="prerequisites"></a>Förutsättningar

- Förhandsgranskning av distribuerad spårning är för närvarande stöds endast för IoT-hubbar som har skapats i följande regioner:

  - **Nordeuropa**
  - **Sydostasien**
  - **USA, västra 2**

- Den här artikeln förutsätter att du är bekant med att skicka telemetrimeddelanden till IoT hub. Kontrollera att du har slutfört den [skicka telemetri C Snabbstart](./quickstart-send-telemetry-c.md).

- Registrera en enhet med IoT hub (steg som är tillgängliga i varje snabbstarten) och anteckna anslutningssträngen.

- Installera den senaste versionen av [Git](https://git-scm.com/download/).

## <a name="configure-iot-hub"></a>Konfigurera IoT Hub

I det här avsnittet konfigurerar du en IoT-hubb för att logga distribuerad spårning attribut (Korrelations-ID: N och tidsstämplar).

1. Gå till din IoT hub i den [Azure-portalen](https://portal.azure.com/).

1. I den vänstra rutan för din IoT-hubb, rulla ned till den **övervakning** och klicka **diagnostikinställningar**.

1. Om diagnostikinställningar inte är redan aktiverat, klickar du på **slå på diagnostik**. Om du redan har aktiverat diagnostikinställningar klickar du på **Lägg till diagnostikinställning**.

1. I den **namn** fältet, anger du ett namn för en ny diagnostikinställning. Till exempel **DistributedTracingSettings**.

1. Välj en eller flera av följande alternativ som bestämmer var loggning ska skickas:

    - **Arkivera till ett lagringskonto**: Konfigurera ett lagringskonto så att den innehåller loggningsinformation.
    - **Stream till en händelsehubb**: Skapa en event hub så att den innehåller loggningsinformation.
    - **Skicka till Log Analytics**: Konfigurera en log analytics-arbetsytan så att den innehåller loggningsinformation.

1. I den **Log** väljer du de åtgärder som du vill logga information för.

    Se till att inkludera **DistributedTracing**, och konfigurera en **kvarhållning** för hur många dagar som du vill att loggning bevaras. Kvarhållning av logg påverkar kostnader för lagring.

    ![Skärmbild som visar där kategorin DistributedTracing är för IoT diagnostikinställningar](./media/iot-hub-distributed-tracing/diag-logs.png)

1. Klicka på **spara** för den nya inställningen.

1. (Valfritt) Se meddelandena som flödar till olika platser, konfigurera [routningsregler och minst två olika slutpunkter](iot-hub-devguide-messages-d2c.md).

När loggning är aktiverat kan registrerar en logg i IoT Hub när ett meddelande som innehåller egenskaper för giltiga spår har påträffats i någon av följande situationer:

- Meddelanden anländer till IoT Hub gateway.
- Meddelandet har bearbetats av IoT Hub.
- Meddelandet dirigeras till anpassade slutpunkter. Routning måste aktiveras.

Läs mer om de här loggarna och deras scheman i [distribuerad spårning i IoT Hub diagnostikloggar](iot-hub-monitor-resource-health.md#distributed-tracing-preview).

## <a name="set-up-device"></a>Konfigurera enhet

I det här avsnittet, förbereder du en utvecklingsmiljö för användning med den [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c). Sedan kan ändra du något av exemplen för att aktivera distribuerad spårning på din enhet telemetrimeddelanden.

Dessa instruktioner är för att skapa exemplet på Windows. Andra miljöer, se [kompilera C SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#compile) eller [färdigförpackade C SDK för specifika plattformsutveckling](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#prepackaged-c-sdk-for-platform-specific-development).

### <a name="clone-the-source-code-and-initialize"></a>Klona källkoden och initiera

1. Installera [”skrivbordsutveckling med C++” arbetsbelastning](https://docs.microsoft.com/cpp/build/vscpp-step-0-installation?view=vs-2017) för Visual Studio 2015 eller 2017.

1. Installera [CMake](https://cmake.org/). Kontrollera att det finns i din `PATH` genom att skriva `cmake -version` från en kommandotolk.

1. Öppna en kommandotolk eller Git Bash-gränssnittet. Kör följande kommando för att klona [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub-lagringsplatsen:

    ```cmd
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive -b public-preview
    ```

    Storleken på den här lagringsplatsen är för närvarande cirka 220 MB. Den här åtgärden kan förväntas ta flera minuter att slutföra.

1. Skapa en `cmake`-underkatalog i rotkatalogen på git-lagringsplatsen och navigera till den mappen.

    ```cmd
    cd azure-iot-sdk-c    
    mkdir cmake
    cd cmake
    cmake ..
    ```

    Om `cmake` inte hittar din C++-kompilerare kan du få kompileringsfel när du kör kommandot ovan. Om det händer ska du försöka köra det här kommandot i [kommandotolken i Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs). 

    När bygget är klart ser de sista utdataraderna ut ungefär som följande utdata:

    ```cmd
    $ cmake ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```

### <a name="edit-the-send-telemetry-sample-to-enable-distributed-tracing"></a>Redigera telemetriexempel skicka om du vill aktivera distribuerad spårning

1. Använd en textredigerare för att öppna den `azure-iot-sdk-c/iothub_client/samples/iothub_ll_telemetry_sample/iothub_ll_telemetry_sample.c` källfilen.

1. Hitta deklarationen för konstanten `connectionString`:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_config&highlight=2)]

    Ersätt värdet för den `connectionString` konstant med enhetens anslutningssträng du antecknade i den [registrera en enhet](./quickstart-send-telemetry-c.md#register-a-device) delen av den [skicka telemetri C Snabbstart](./quickstart-send-telemetry-c.md).

1. Ändra den `MESSAGE_COUNT` definiera till `5000`:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_config&highlight=3)]

1. Hitta raden med kod som anropar `IoTHubDeviceClient_LL_SetConnectionStatusCallback` att registrera en anslutning status återanropsfunktion innan du skickar meddelandeloopen. Lägg till kod under den raden som visas nedan för att anropa `IoTHubDeviceClient_LL_EnablePolicyConfiguration` aktiverar distribuerad spårning för enheten:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_tracing&highlight=5)]

    Den `IoTHubDeviceClient_LL_EnablePolicyConfiguration` funktionen gör det möjligt för principer för specifika IoTHub-funktioner som är konfigurerade via [enhetstvillingar](./iot-hub-devguide-device-twins.md). En gång `POLICY_CONFIGURATION_DISTRIBUTED_TRACING` aktiveras med raden i koden ovan, spårning av beteendet för enheten visas distribuerad spårning av ändringar som gjorts i enhetstvillingen.

1. Lägg till en en sekunds fördröjning i slutet av loopen för Skicka meddelande för att hålla den exempelapp som körs utan att använda upp din kvot:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_sleep&highlight=8)]

### <a name="compile-and-run"></a>Kompilera och kör

1. Navigera till den *iothub_ll_telemetry_sample* projektkatalogen från katalogen CMake (`azure-iot-sdk-c/cmake`) du skapade tidigare och kompilera exemplet:

    ```cmd
    cd iothub_client/samples/iothub_ll_telemetry_sample
    cmake --build . --target iothub_ll_telemetry_sample --config Debug
    ```

1. Kör appen. Enheten skickar telemetri som stöd för distribuerad spårning.

    ```cmd
    Debug/iothub_ll_telemetry_sample.exe
    ```

1. Fortsätt att köra appen. Du kan också se meddelandet som skickas till IoT Hub genom att titta på konsolfönstret.

<!-- For a client app that can receive sampling decisions from the cloud, check out [this sample](https://aka.ms/iottracingCsample).  -->

### <a name="using-third-party-clients"></a>Med hjälp av klienter från tredje part

Om du inte använder C SDK och ändå vill förhandsgranska distribuerad spårning för IoT-hubb, skapar du meddelanden som ska innehålla en `tracestate` programegenskap med tiden för skapandet av meddelande i formatet för unix-tidsstämpel. Till exempel `tracestate=timestamp=1539243209`. Om du vill styra hur stor andel av meddelanden som innehåller den här egenskapen, implementera logik för att lyssna på molnet-initierad händelser, t.ex uppdateringar för enhetstvilling.

## <a name="update-sampling-options"></a>Uppdatera alternativ för datainsamling 

Om du vill ändra procentandelen av meddelanden från molnet, måste du uppdatera enhetstvillingen. Du kan göra detta på flera sätt, inklusive JSON-redigeraren i portalen och SDK för IoT Hub-tjänsten. Följande avsnitt innehåller exempel.

### <a name="update-using-the-portal"></a>Uppdatera med hjälp av portalen

1. Gå till din IoT-hubb i [Azure-portalen](https://portal.azure.com/), klicka sedan på **IoT-enheter**.

1. Klicka på din enhet.

1. Leta efter **Aktivera distribuerad spårning (förhandsversion)** och välj sedan **aktivera**.

    ![Aktivera distribuerad spårning i Azure-portalen](./media/iot-hub-distributed-tracing/azure-portal.png)

1. Välj en **samplingsfrekvensen** mellan 0 och 100%.

1. Klicka på **Spara**.

1. Vänta några sekunder och tryck på **uppdatera**, om du har godkänt av enheten kan visas en synkroniseringsikon med en bock.

1. Gå tillbaka till konsolfönstret för telemetri meddelande appen. Visas meddelanden som skickas med `tracestate` i egenskaperna för programmet.

    ![Spåra status](./media/iot-hub-distributed-tracing/MicrosoftTeams-image.png)

1. (Valfritt) Ändra samplingsfrekvensen till ett annat värde och se ändringen i frekvens som meddelanden inkluderar `tracestate` i egenskaperna för programmet.

### <a name="update-using-azure-iot-hub-toolkit-for-vs-code"></a>Uppdatering med hjälp av Azure IoT Hub Toolkit för VS Code

1. Installera VS Code, och sedan installera den senaste versionen av Azure IoT Hub Toolkit för VS Code från [här](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).

1. Öppna VS Code och [konfigurera IoT Hub-anslutningssträngen](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit#user-content-prerequisites).

1. Expandera enheten och leta efter **distribuerad spårning av inställningen (förhandsversion)**. Under det klickar du på **Update distribuerad spårning-inställningen (förhandsversion)** för sub-nod.

    ![Aktivera distribuerad spårning i Azure IoT Hub Toolkit](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-1.png)

1. I popup-fönstret väljer **aktivera**, tryck sedan på RETUR för att bekräfta 100 som samplingsfrekvensen.

    ![Uppdatera urvalsläge](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-2.png)

    ![Uppdatera samplingsfrekvensen](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-3.png)

### <a name="bulk-update-for-multiple-devices"></a>Massuppdatering för flera enheter

Uppdatera konfigurationen av distribuerad spårning sampling för flera enheter med [automatisk enhetskonfiguration](iot-hub-auto-device-config.md). Kontrollera att du följer den här twin schema:

```json
{
    "properties": {
        "desired": {
            "azureiot*com^dtracing^1": {
                "sampling_mode": 1,
                "sampling_rate": 100
            }
        }
    }
}
```

| Elementnamn | Krävs | Typ | Beskrivning |
|-----------------|----------|---------|-----------------------------------------------------|
| `sampling_mode` | Ja | Integer | Två lägesvärden stöds för närvarande för att aktivera eller inaktivera sampling. `1` är på och, `2` är inaktiverad. |
| `sampling_rate` | Ja | Integer | Det här värdet är en procentandel. Endast värden från `0` till `100` (inklusivt) tillåts.  |

## <a name="query-and-visualize"></a>Fråga och visualisera

Om du vill se alla spårningar som loggats av en IoT-hubb, fråga log-butiken som du valde i diagnostikinställningar. Det här avsnittet beskriver några olika alternativ.

### <a name="query-using-log-analytics"></a>Ställa frågor med Log Analytics

Om du har konfigurerat [Log Analytics med diagnostikloggar](../azure-monitor/platform/diagnostic-logs-stream-log-store.md), fråga genom att söka efter loggar i den `DistributedTracing` kategori. Den här frågan visar till exempel alla spårningar som loggats:

```Kusto
// All distributed traces 
AzureDiagnostics 
| where Category == "DistributedTracing" 
| project TimeGenerated, Category, OperationName, Level, CorrelationId, DurationMs, properties_s 
| order by TimeGenerated asc  
```

Exemplet loggar som visas av Log Analytics:

| TimeGenerated | OperationName | Kategori | Nivå | CorrelationId | . durationMs | Egenskaper |
|--------------------------|---------------|--------------------|---------------|---------------------------------------------------------|------------|------------------------------------------------------------------------------------------------------------------------------------------|
| 2018-02-22T03:28:28.633Z | DiagnosticIoTHubD2C | DistributedTracing | Information | 00-8cd869a412459a25f5b4f31311223344-0144d2590aacd909-01 |  | {"deviceId":"AZ3166","messageSize":"96","callerLocalTimeUtc":"2018-02-22T03:27:28.633Z","calleeLocalTimeUtc":"2018-02-22T03:27:28.687Z"} |
| 2018-02-22T03:28:38.633Z | DiagnosticIoTHubIngress | DistributedTracing | Information | 00-8cd869a412459a25f5b4f31311223344-349810a9bbd28730-01 | 20 | {"isRoutingEnabled":"false","parentSpanId":"0144d2590aacd909"} |
| 2018-02-22T03:28:48.633Z | DiagnosticIoTHubEgress | DistributedTracing | Information | 00-8cd869a412459a25f5b4f31311223344-349810a9bbd28730-01 | 23 | {"endpointType":"EventHub","endpointName":"myEventHub", "parentSpanId":"0144d2590aacd909"} |

Information om de olika typerna av loggar finns i [diagnostikloggar för Azure IoT Hub](iot-hub-monitor-resource-health.md#distributed-tracing-preview).

### <a name="application-map"></a>Programkarta

Om du vill visualisera flödet av IoT-meddelanden, ställa in exempelappen Programkartan. Exempelappen skickar distribuerad spårning-loggar till [Programkartan](../application-insights/app-insights-app-map.md) med hjälp av en Azure-funktion och en Event Hub.

> [!div class="button"]
> <a href="https://github.com/Azure-Samples/e2e-diagnostic-provision-cli" target="_blank">Hämta exemplet på Github</a>

Den här bilden nedan visar distribuerad spårning i App-karta med tre routning slutpunkter:

![IoT distribuerad spårning i App-karta](./media/iot-hub-distributed-tracing/app-map.png)

## <a name="understand-azure-iot-distributed-tracing"></a>Förstå Azure IoT distribuerad spårning

### <a name="context"></a>Kontext

Många IoT-lösningar, inklusive vår egen [referensarkitekturen](https://aka.ms/iotrefarchitecture) (endast engelska), vanligtvis följer en variant av den [mikrotjänstarkitektur](https://docs.microsoft.com/azure/architecture/microservices/). När en IoT-lösning växer mer komplexa kan få du med hjälp av ett dussin eller flera mikrotjänster. Dessa mikrotjänster kanske eller kanske inte är från Azure. Lokalisera där IoT meddelanden släppa eller långsammare kan det bli svårt. Exempelvis kan ha du en IoT-lösning som använder 5 olika Azure-tjänster och 1500 aktiva enheter. Varje enhet skickar 10 enhet till moln-meddelanden per sekund (för sammanlagt 15 000 meddelanden per sekund), men du upptäcker att webbappen ser endast 10 000 meddelanden per sekund. Var finns problemet? Hur tar du reda på orsaken?

### <a name="distributed-tracing-pattern-in-microservice-architecture"></a>Mönster för distribuerad spårning i mikrotjänstarkitektur

Om du vill återskapa flödet för en IoT-meddelande för olika tjänster, varje tjänst ska spridas en *Korrelations-ID* som unikt identifierar meddelandet. När samlats in i ett centraliserad system, kan du se Meddelandeflöde Korrelations-ID: N. Den här metoden anropas den [distribuerad spårning mönstret](https://docs.microsoft.com/azure/architecture/microservices/logging-monitoring#distributed-tracing).

För att stödja större utnyttjande för distribuerad spårning, Microsoft bidrar till [W3C standard förslag för distribuerad spårning](https://w3c.github.io/trace-context/).

### <a name="iot-hub-support"></a>Stöd för IoT Hub

När du har aktiverat, följer distribuerad spårning stöd för IoT Hub detta flöde:

1. Ett meddelande genereras på IoT-enheter.
1. IoT-enheter bestämmer sig (med hjälp av molnet) för att det här meddelandet ska tilldelas med en trace-kontext.
1. SDK: N lägger till en `tracestate` till programegenskap meddelandet som innehåller tidsstämpel för meddelande skapas.
1. IoT-enhet skickar meddelandet till IoT Hub.
1. Meddelandet anländer till IoT hub gateway.
1. IoT Hub söker efter den `tracestate` i meddelandeegenskaper för program och kontroller för att se om det är i rätt format.
1. Om det IoT Hub genererar och loggar den `trace-id` och `span-id` till diagnostikloggar för Azure Monitor under kategorin `DiagnosticIoTHubD2C`.
1. När meddelandet bearbetningen är klar, IoT Hub genererar en annan `span-id` och loggar tillsammans med den befintliga `trace-id` under kategorin `DiagnosticIoTHubIngress`.
1. Om routning är aktiverat för meddelandet, IoT Hub skriver den till anpassad slutpunkt och loggar en annan `span-id` med samma `trace-id` under kategorin `DiagnosticIoTHubEgress`.
1. Stegen ovan upprepas för varje meddelande som genereras.

## <a name="public-preview-limits-and-considerations"></a>Offentlig förhandsversion begränsningar och överväganden

- Förslag för W3C Trace sammanhanget standard är för närvarande en utkastet.
- Den enda programmeringsspråk som stöds av klient-SDK är för närvarande C.
- Kapaciteten för moln till enhet twin är inte tillgängligt för [IoT Hub basic-nivån](iot-hub-scaling.md#basic-and-standard-tiers). IoT Hub kommer dock fortfarande logga till Azure Monitor om den får en korrekt består trace kontext rubrik.
- För att säkerställa effektiv drift, medför IoT Hub en begränsning av loggning som kan uppstå som en del av distribuerad spårning.

## <a name="next-steps"></a>Nästa steg

- Läs mer om allmänna distribuerad spårning mönstret i mikrotjänster i [Mikrotjänst-arkitekturmönster: distribuerad spårning](https://microservices.io/patterns/observability/distributed-tracing.html).
- Om du vill konfigurera konfiguration för att tillämpa inställningar för distribuerad spårning på ett stort antal enheter, se [konfigurera och övervaka IoT-enheter skalenligt](iot-hub-auto-device-config.md).
- Läs mer om Azure Monitor i [vad är Azure Monitor?](../azure-monitor/overview.md).
