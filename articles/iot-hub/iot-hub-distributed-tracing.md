---
title: Lägga till korrelations-ID:er i IoT-meddelanden med distribuerad spårning (pre)
description: Lär dig hur du använder den distribuerade spårningsmöjligheten för att spåra IoT-meddelanden i de Azure-tjänster som används av din lösning.
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: jlian
ms.openlocfilehash: efee34ddfb2b2f6090d5dc8c43647c7ee1c53ce2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77562436"
---
# <a name="trace-azure-iot-device-to-cloud-messages-with-distributed-tracing-preview"></a>Spåra Azure IoT-meddelanden från enhet till moln med distribuerad spårning (förhandsversion)

Microsoft Azure IoT Hub stöder för närvarande distribuerad spårning som en [förhandsgranskningsfunktion](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

IoT Hub är en av de första Azure-tjänsterna som stöder distribuerad spårning. När fler Azure-tjänster stöder distribuerad spårning kan du spåra IoT-meddelanden i hela Azure-tjänsterna som ingår i din lösning. En bakgrund om distribuerad spårning finns i [Distribuerad spårning](../azure-monitor/app/distributed-tracing.md).

Om du aktiverar distribuerad spårning för IoT Hub kan du:

- Övervaka flödet för varje meddelande exakt via IoT Hub med hjälp av [spårningskontext](https://github.com/w3c/trace-context). Den här spårningskontexten innehåller korrelations-ID:er som gör att du kan korrelera händelser från en komponent med händelser från en annan komponent. Den kan användas för en delmängd eller alla IoT-enhetsmeddelanden med hjälp av [enhetstvilling](iot-hub-devguide-device-twins.md).
- Logga spårningskontexten automatiskt till [diagnostikloggar för Azure Monitor](iot-hub-monitor-resource-health.md).
- Mät och förstå meddelandeflöde och svarstid från enheter till IoT Hub och routningslutpunkter.
- Börja med att överväga hur du vill implementera distribuerad spårning för icke-Azure-tjänster i din IoT-lösning.

I den här artikeln använder du [Azure IoT-enheten SDK för C](iot-hub-device-sdk-c-intro.md) med distribuerad spårning. Stöd för distribuerad spårning pågår fortfarande för de andra SDK:erna.

## <a name="prerequisites"></a>Krav

- Förhandsversionen av distribuerad spårning stöds för närvarande endast för IoT Hubs som skapats i följande regioner:

  - **Europa, norra**
  - **Sydostasien**
  - **USA, västra 2**

- Den här artikeln förutsätter att du är bekant med att skicka telemetrimeddelanden till din IoT-hubb. Kontrollera att du har slutfört [snabbstarten Skicka telemetri C](quickstart-send-telemetry-c.md).

- Registrera en enhet med din IoT-hubb (steg som är tillgängliga i varje snabbstart) och anteckna anslutningssträngen.

- Installera den senaste versionen av [Git](https://git-scm.com/download/).

## <a name="configure-iot-hub"></a>Konfigurera IoT Hub

I det här avsnittet konfigurerar du en IoT-hubb för att logga distribuerade spårningsattribut (korrelations-ID och tidsstämplar).

1. Navigera till din IoT-hubb i [Azure-portalen](https://portal.azure.com/).

1. Bläddra ned till avsnittet **Övervakning** i den vänstra rutan för IoT-hubben och klicka på **Diagnostikinställningar**.

1. Om diagnostikinställningarna inte redan är aktiverade klickar du **på Aktivera diagnostik**. Om du redan har aktiverat diagnostikinställningar klickar du på **Lägg till diagnostikinställning**.

1. Ange ett namn för en ny diagnostikinställning i fältet **Namn.** Till exempel **DistributedTracingSettings**.

1. Välj ett eller flera av följande alternativ som avgör var loggningen ska skickas:

    - **Arkiv till ett lagringskonto**: Konfigurera ett lagringskonto så att det innehåller loggningsinformationen.
    - **Strömma till en händelsehubb**: Konfigurera en händelsehubb så att den innehåller loggningsinformationen.
    - **Skicka till Logganalys:** Konfigurera en logganalysarbetsyta så att den innehåller loggningsinformationen.

1. Välj de åtgärder som du vill logga information för i avsnittet **Logg.**

    Se till att inkludera **DistributedTracing**och konfigurera en **kvarhållning** för hur många dagar du vill att loggningen ska behållas. Loggkvarhållning påverkar lagringskostnaderna.

    ![Skärmbild som visar var kategorin DistributedTracing är för IoT-diagnostikinställningar](./media/iot-hub-distributed-tracing/diag-logs.png)

1. Klicka på **Spara** för den nya inställningen.

1. (Valfritt) Om du vill se meddelandena flöda till olika platser ställer du in [routningsregler på minst två olika slutpunkter](iot-hub-devguide-messages-d2c.md).

När loggningen är aktiverad registrerar IoT Hub en logg när ett meddelande som innehåller giltiga spårningsegenskaper påträffas i någon av följande situationer:

- Meddelandena anländer till IoT Hub's gateway.
- Meddelandet bearbetas av IoT Hub.
- Meddelandet dirigeras till anpassade slutpunkter. Routning måste vara aktiverat.

Mer information om dessa loggar och deras scheman finns [i Distribuerad spårning i diagnostikloggar för IoT Hub](iot-hub-monitor-resource-health.md#distributed-tracing-preview).

## <a name="set-up-device"></a>Konfigurera enhet

I det här avsnittet förbereder du en utvecklingsmiljö för användning med [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c). Sedan ändrar du ett av exemplen för att aktivera distribuerad spårning på enhetens telemetrimeddelanden.

De här anvisningarna är för att skapa exemplet i Windows. För andra miljöer finns [i Kompilera C SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#compile) eller [Färdigförpackade C SDK för plattformsspecifik utveckling](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#prepackaged-c-sdk-for-platform-specific-development).

### <a name="clone-the-source-code-and-initialize"></a>Klona källkoden och initiera

1. Installera ["Skrivbordsutveckling med C++" arbetsbelastning](https://docs.microsoft.com/cpp/build/vscpp-step-0-installation?view=vs-2019) för Visual Studio 2019. Visual Studio 2017 och 2015 stöds också.

1. Installera [CMake](https://cmake.org/). Kontrollera att den `PATH` finns i `cmake -version` din genom att skriva från en kommandotolk.

1. Öppna en kommandotolk eller Git Bash-gränssnittet. Kör följande kommandon för att klona den senaste versionen av [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub-databasen:

    ```cmd
    git clone -b public-preview https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Den här åtgärden kan förväntas ta flera minuter att slutföra.

1. Skapa en `cmake`-underkatalog i rotkatalogen på git-lagringsplatsen och navigera till den mappen. Kör följande kommandon från `azure-iot-sdk-c` katalogen:

    ```cmd
    mkdir cmake
    cd cmake
    cmake ..
    ```

    Om `cmake` du inte hittar c++-kompilatorn kan du få byggfel när du kör kommandot ovan. Om det händer ska du försöka köra det här kommandot i [kommandotolken i Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs). 

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

### <a name="edit-the-send-telemetry-sample-to-enable-distributed-tracing"></a>Redigera skicka telemetriexemplet för att aktivera distribuerad spårning

> [!div class="button"]
> <a href="https://github.com/Azure-Samples/azure-iot-distributed-tracing-sample/blob/master/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c" target="_blank">Hämta exemplet på GitHub</a>

1. Använd en redigerare `azure-iot-sdk-c/iothub_client/samples/iothub_ll_telemetry_sample/iothub_ll_telemetry_sample.c` för att öppna källfilen.

1. Hitta deklarationen för konstanten `connectionString`:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_config&highlight=2)]

    Ersätt värdet för `connectionString` konstanten med den enhetsanslutningssträng som du har antecknat i [registret en enhetsdel](./quickstart-send-telemetry-c.md#register-a-device) i [snabbstarten Skicka telemetri C](./quickstart-send-telemetry-c.md).

1. Ändra `MESSAGE_COUNT` definiera `5000`till:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_config&highlight=3)]

1. Leta reda på den `IoTHubDeviceClient_LL_SetConnectionStatusCallback` kodrad som anropar för att registrera en motringningsfunktion för anslutningsstatus innan skicka meddelandeloopen. Lägg till kod under den `IoTHubDeviceClient_LL_EnablePolicyConfiguration` raden enligt nedan för att anropa aktivering av distribuerad spårning för enheten:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_tracing&highlight=5)]

    Funktionen `IoTHubDeviceClient_LL_EnablePolicyConfiguration` aktiverar principer för specifika IoTHub-funktioner som konfigureras via [enhetstvillingar](./iot-hub-devguide-device-twins.md). När `POLICY_CONFIGURATION_DISTRIBUTED_TRACING` är aktiverat med kodraden ovan, kommer spårningsbeteendet för enheten att återspegla distribuerade spårningsändringar som gjorts på enhetstvillingen.

1. Om du vill att exempelappen ska fungera utan att använda kvoten lägger du till en fördröjning på en sekund i slutet av meddelandeloopen:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_sleep&highlight=8)]

### <a name="compile-and-run"></a>Kompilera och kör

1. Navigera till *iothub_ll_telemetry_sample* projektkatalogen från CMake-katalogen (`azure-iot-sdk-c/cmake`) som du skapade tidigare och kompilera exemplet:

    ```cmd
    cd iothub_client/samples/iothub_ll_telemetry_sample
    cmake --build . --target iothub_ll_telemetry_sample --config Debug
    ```

1. Kör appen. Enheten skickar telemetri som stöder distribuerad spårning.

    ```cmd
    Debug/iothub_ll_telemetry_sample.exe
    ```

1. Håll appen igång. Du kan också observera meddelandet som skickas till IoT Hub genom att titta på konsolfönstret.

<!-- For a client app that can receive sampling decisions from the cloud, check out [this sample](https://aka.ms/iottracingCsample).  -->

### <a name="workaround-for-third-party-clients"></a>Lösning för klienter från tredje part

Det är **inte trivialt** att förhandsgranska funktionen för distribuerad spårning utan att använda C SDK. Detta tillvägagångssätt rekommenderas därför inte.

Först måste du implementera alla IoT Hub-protokollprimitiver i dina meddelanden genom att följa utvecklingsguiden [Skapa och läsa IoT Hub-meddelanden](iot-hub-devguide-messages-construct.md). Redigera sedan protokollegenskaperna i MQTT/AMQP-meddelandena som ska läggas `tracestate` till som **systemegenskap**. Mer specifikt:

* För MQTT `%24.tracestate=timestamp%3d1539243209` lägger du till `1539243209` meddelandeämnet, där bör ersättas med meddelandets skapandetid i tidsstämpelformatet unix. Som ett exempel hänvisas till genomförandet [i C SDK](https://github.com/Azure/azure-iot-sdk-c/blob/6633c5b18710febf1af7713cf1a336fd38f623ed/iothub_client/src/iothubtransport_mqtt_common.c#L761)
* För AMQP, `key("tracestate")` `value("timestamp=1539243209")` lägg till och som meddelande anteckning. En referensimplementering finns [här](https://github.com/Azure/azure-iot-sdk-c/blob/6633c5b18710febf1af7713cf1a336fd38f623ed/iothub_client/src/uamqp_messaging.c#L527).

Om du vill styra procentandelen meddelanden som innehåller den här egenskapen implementerar du logik för att lyssna på molninitierade händelser, till exempel dubbla uppdateringar.

## <a name="update-sampling-options"></a>Uppdatera samplingsalternativ 

Om du vill ändra procentandelen meddelanden som ska spåras från molnet måste du uppdatera enhetstvillingen. Du kan utföra detta på flera sätt, inklusive JSON-redigeraren i portalen och IoT Hub-tjänsten SDK. Följande underavsnitt ger exempel.

### <a name="update-using-the-portal"></a>Uppdatera med hjälp av portalen

1. Navigera till din IoT-hubb i [Azure Portal](https://portal.azure.com/)och klicka sedan på **IoT-enheter**.

1. Klicka på enheten.

1. Leta efter **Aktivera distribuerad kalkering (förhandsgranskning)** och välj sedan **Aktivera**.

    ![Aktivera distribuerad spårning i Azure-portalen](./media/iot-hub-distributed-tracing/azure-portal.png)

1. Välj en **samplingsfrekvens** mellan 0 % och 100 %.

1. Klicka på **Spara**.

1. Vänta några sekunder och tryck på **Uppdatera**, sedan om det har bekräftats av enheten, visas en synkroniseringsikon med en bock.

1. Gå tillbaka till konsolfönstret för telemetrimeddelandeappen. Du kommer att se `tracestate` meddelanden som skickas med i programegenskaperna.

    ![Spårtillstånd](./media/iot-hub-distributed-tracing/MicrosoftTeams-image.png)

1. (Valfritt) Ändra samplingsfrekvensen till ett annat värde och observera den `tracestate` frekvensförändring som meddelandena innehåller i programegenskaperna.

### <a name="update-using-azure-iot-hub-for-vs-code"></a>Uppdatera med Azure IoT Hub för VS-kod

1. Installera VS-kod och installera sedan den senaste versionen av Azure IoT Hub för VS-kod [härifrån](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).

1. Öppna VS-kod och [konfigurera IoT Hub-anslutningssträng](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit#user-content-prerequisites).

1. Expandera enheten och leta efter inställningar för **distribuerad spårning (förhandsgranskning)**. Klicka på **Uppdatera inställning för distribuerad spårning (förhandsgranskning)** av undernoden under den.

    ![Aktivera distribuerad spårning i Azure IoT Hub-tillägget](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-1.png)

1. Välj **Aktivera**i popup-fönstret och tryck sedan på Retur för att bekräfta 100 som samplingsfrekvens.

    ![Uppdatera samplingsläge](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-2.png)

    ![Uppdatera samplingsfrekvens](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-3.png)

### <a name="bulk-update-for-multiple-devices"></a>Massuppdatering för flera enheter

Om du vill uppdatera konfigurationen för distribuerad spårningsprovtagning för flera enheter använder du [automatisk enhetskonfiguration](iot-hub-auto-device-config.md). Se till att du följer det här dubbla schemat:

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
| `sampling_mode` | Ja | Integer | Två lägesvärden stöds för närvarande för att aktivera och inaktivera sampling. `1`är På `2` och är Av. |
| `sampling_rate` | Ja | Integer | Det här värdet är en procentsats. Endast värden `0` `100` från till (inklusive) är tillåtna.  |

## <a name="query-and-visualize"></a>Fråga och visualisera

Om du vill visa alla spårningar som loggats av en IoT Hub frågar du loggarkivet som du har valt i diagnostikinställningar. Det här avsnittet går igenom ett par olika alternativ.

### <a name="query-using-log-analytics"></a>Fråga med hjälp av Logganalys

Om du har konfigurerat [Log Analytics med diagnostikloggar](../azure-monitor/platform/resource-logs-collect-storage.md)frågar `DistributedTracing` du efter loggar i kategorin. Den här frågan visar till exempel alla de spår som loggats:

```Kusto
// All distributed traces 
AzureDiagnostics 
| where Category == "DistributedTracing" 
| project TimeGenerated, Category, OperationName, Level, CorrelationId, DurationMs, properties_s 
| order by TimeGenerated asc  
```

Exempelloggar som visas av Log Analytics:

| TimeGenerated | OperationName | Kategori | Nivå | CorrelationId | VaraktighetMämmer | Egenskaper |
|--------------------------|---------------|--------------------|---------------|---------------------------------------------------------|------------|------------------------------------------------------------------------------------------------------------------------------------------|
| 2018-02-22T03:28:28.633Z | DiagnosticIoTHubD2C | DistribuerasTracing | Information | 00-8cd869a412459a25f5b4f31311223344-0144d2590aacd909-01 |  | {"deviceId":"AZ3166","messageSize":"96","callerLocalTimeUtc":"2018-02-22T03:27:28.633Z","calleeLocalTimeUtc":"2018-02-22T03:27:28.687Z"} |
| 2018-02-22T03:28:38.633Z | DiagnosticIoTHubIngress | DistribuerasTracing | Information | 00-8cd869a412459a25f5b4f31311223344-349810a9bbd28730-01 | 20 | {"isRoutingEnabled":"false","parentSpanId":"0144d2590aacd909"} |
| 2018-02-22T03:28:48.633Z | DiagnosticIoTHubEgress | DistribuerasTracing | Information | 00-8cd869a412459a25f5b4f31311223344-349810a9bbd28730-01 | 23 | {"endpointType":"EventHub","endpointName":"myEventHub", "parentSpanId":"0144d2590aacd909"} |

Information om hur du förstår de olika typerna av loggar finns i [Diagnostikloggar för Azure IoT Hub](iot-hub-monitor-resource-health.md#distributed-tracing-preview).

### <a name="application-map"></a>Programkarta

Om du vill visualisera flödet av IoT-meddelanden konfigurerar du exempelappen Programmappning. Exempelappen skickar de distribuerade spårningsloggarna till [programmappningen](../application-insights/app-insights-app-map.md) med hjälp av en Azure-funktion och en eventhubb.

> [!div class="button"]
> <a href="https://github.com/Azure-Samples/e2e-diagnostic-provision-cli" target="_blank">Hämta exemplet på GitHub</a>

Den här bilden nedan visar distribuerad spårning i App Map med tre routningslutpunkter:

![Distribuerad IoT-spårning i appkarta](./media/iot-hub-distributed-tracing/app-map.png)

## <a name="understand-azure-iot-distributed-tracing"></a>Förstå Azure IoT distribuerad spårning

### <a name="context"></a>Kontext

Många IoT-lösningar, inklusive vår egen [referensarkitektur](https://aka.ms/iotrefarchitecture) (endast på engelska), följer i allmänhet en variant av [mikrotjänstarkitekturen.](https://docs.microsoft.com/azure/architecture/microservices/) Som en IoT-lösning blir mer komplex, du sluta med ett dussin eller fler mikrotjänster. Dessa mikrotjänster kan komma från Azure eller inte. Att hitta var IoT-meddelanden sjunker eller saktar ner kan bli en utmaning. Du har till exempel en IoT-lösning som använder 5 olika Azure-tjänster och 1500 aktiva enheter. Varje enhet skickar 10 meddelanden från enhet till moln/sekund (för totalt 15 000 meddelanden/sekund), men du märker att webbappen bara ser 10 000 meddelanden/sekund. Var är problemet? Hur hittar du den skyldige?

### <a name="distributed-tracing-pattern-in-microservice-architecture"></a>Distribuerat spårningsmönster i mikrotjänstarkitektur

Om du vill rekonstruera flödet för ett IoT-meddelande över olika tjänster bör varje tjänst sprida ett *korrelations-ID* som unikt identifierar meddelandet. När de har samlats in i ett centraliserat system kan du visa meddelandeflöde. Den här metoden kallas [det distribuerade spårningsmönstret](https://docs.microsoft.com/azure/architecture/microservices/logging-monitoring#distributed-tracing).

För att stödja ett bredare införande för distribuerad spårning bidrar Microsoft till [W3C-standardförslag för distribuerad spårning](https://w3c.github.io/trace-context/).

### <a name="iot-hub-support"></a>Support för IoT Hub

När det har aktiverats följer stöd för distribuerad spårning för IoT Hub detta flöde:

1. Ett meddelande genereras på IoT-enheten.
1. IoT-enheten bestämmer (med hjälp av molnet) att det här meddelandet ska tilldelas med en spårningskontext.
1. SDK lägger `tracestate` till en egenskap för meddelandeprogram som innehåller tidsstämpeln för att skapa meddelanden.
1. IoT-enheten skickar meddelandet till IoT Hub.
1. Meddelandet anländer till IoT-hubbgatewayen.
1. IoT Hub söker `tracestate` efter egenskaperna i meddelandeprogrammet och kontrollerar om den är i rätt format.
1. Om så är fallet genererar IoT `trace-id` Hub en `span-id` globalt unik för meddelandet, a för "hop", och `DiagnosticIoTHubD2C`loggar dem till Azure Monitor diagnostikloggar under åtgärden .
1. När meddelandebearbetningen är klar genererar IoT Hub en annan `span-id` `trace-id` och loggar `DiagnosticIoTHubIngress`den tillsammans med den befintliga under åtgärden .
1. Om routning är aktiverat för meddelandet skriver IoT Hub det till den `span-id` anpassade slutpunkten och loggar en annan med samma `trace-id` under kategorin `DiagnosticIoTHubEgress`.
1. Stegen ovan upprepas för varje meddelande som genereras.

## <a name="public-preview-limits-and-considerations"></a>Begränsningar och överväganden för offentlig förhandsversion

- Förslag till W3C Trace Context-standard är för närvarande ett arbetsutkast.
- För närvarande är det enda utvecklingsspråk som stöds av klienten SDK C.
- Två funktioner mellan molnet till enheten är inte tillgängliga för [IoT Hub-grundläggande nivå](iot-hub-scaling.md#basic-and-standard-tiers). IoT Hub loggar dock fortfarande till Azure Monitor om den ser ett korrekt sammansatt spårningskontexthuvud.
- För att säkerställa en effektiv drift kommer IoT Hub att införa ett begränsningsreglage för loggningshastigheten som kan uppstå som en del av distribuerad spårning.

## <a name="next-steps"></a>Nästa steg

- Mer information om det allmänna distribuerade spårningsmönstret i mikrotjänster finns i [Arkitekturmönstret för Mikrotjänst: distribuerad spårning](https://microservices.io/patterns/observability/distributed-tracing.html).
- Mer om du vill ställa in konfiguration för att tillämpa distribuerade spårningsinställningar på ett stort antal enheter finns i [Konfigurera och övervaka IoT-enheter i stor skala](iot-hub-auto-device-config.md).
- Mer information om Azure Monitor finns i [Vad är Azure Monitor?](../azure-monitor/overview.md).
