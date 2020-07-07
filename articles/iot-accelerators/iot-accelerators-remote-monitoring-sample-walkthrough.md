---
title: Översikt över lösnings Accelerator för fjärr styrning – Azure | Microsoft Docs
description: Den här artikeln innehåller en översikt över några av de viktigaste elementen i lösningen för fjärrövervakning så att du kan förstå hur det fungerar.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: f501eb55f72811063ddf1d8e02a0ce2137d598f3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "80546316"
---
# <a name="remote-monitoring-solution-accelerator-overview"></a>Översikt över acceleratorn för fjärrövervakningslösning

[Lösnings acceleratorn](../iot-accelerators/about-iot-accelerators.md) för fjärrövervakning implementerar en övervaknings lösning från slut punkt till slut punkt för flera datorer på fjärranslutna platser. I lösningen kombineras viktiga Azure-tjänster till en allmän implementering av affärsscenariot. Du kan använda lösningen som startpunkt för en egen implementering och [anpassa](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md) den efter dina egna affärsbehov.

Den här artikeln vägleder dig igenom några av de viktigaste elementen i lösningen för fjärrövervakning så att du kan förstå hur det fungerar. Med den här kunskapen kan du sedan:

* Felsöka problem i lösningen.
* Planera hur lösningen kan anpassas för att uppfylla dina behov.
* Utforma en egen IoT-lösning som använder Azure-tjänster.

Accelerator-koden för Remote Monitoring Solution finns på GitHub:

* [.NET](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)

## <a name="logical-architecture"></a>Logisk arkitektur

Följande diagram beskriver de logiska komponenterna i den lösnings Accelerator för fjärrövervakning som står i IoT- [arkitekturen](../iot-fundamentals/iot-introduction.md):

![Logisk arkitektur](./media/iot-accelerators-remote-monitoring-sample-walkthrough/remote-monitoring-architecture.png)

## <a name="why-microservices"></a>Varför mikrotjänster?

Moln arkitekturen har utvecklats sedan Microsoft släppte de första lösnings acceleratorerna. [Mikrotjänster](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/) har uppvisats som en beprövad metod för att uppnå skalbarhet och flexibilitet utan att offra utvecklings hastigheten. Flera Microsoft-tjänster använder detta arkitektur mönster internt med bättre Tillförlitlighets-och skalbarhets resultat. De uppdaterade lösnings acceleratorerna sätter i övningen så att du kan dra nytta av dem.

> [!TIP]
> Läs mer om arkitekturer för mikrotjänster i [.NET Application Architecture](https://www.microsoft.com/net/learn/architecture) (.NET-programarkitektur) och [Microservices: An application revolution powered by the cloud](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/) (Mikrotjänster: En programrevolution som drivs av molnet).

## <a name="device-connectivity"></a>Enhetsanslutning

Lösningen innehåller följande komponenter i enhetens anslutnings del i den logiska arkitekturen:

### <a name="real-devices"></a>Verkliga enheter

Du kan ansluta riktiga enheter till lösningen. Du kan implementera beteendet för dina simulerade enheter med hjälp av Azure IoT-enhetens SDK: er.

Du kan etablera riktiga enheter från instrument panelen i lösnings portalen.

### <a name="device-simulation-microservice"></a>Mikrotjänst för enhets simulering

Lösningen innehåller [mikrotjänsten för enhets simulering](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/device-simulation) som gör att du kan hantera en pool med simulerade enheter från lösnings portalen för att testa flödet från slut punkt till slut punkt i lösningen. De simulerade enheterna:

* Skapa telemetri från enhet till moln.
* Svara på metod anrop från moln till enhet från IoT Hub.

Mikrotjänsten tillhandahåller en RESTful-slutpunkt där du kan skapa, starta och stoppa simuleringar. Varje simulering består av en uppsättning virtuella enheter av olika typer, som skickar telemetri och svarar på metod anrop.

Du kan etablera simulerade enheter från instrument panelen i lösnings portalen.

### <a name="iot-hub"></a>IoT Hub

[IoT Hub](../iot-hub/index.yml) matar in telemetri som skickas från både verkliga och simulerade enheter i molnet. IoT-hubben gör telemetri tillgängligt för tjänsterna i Server delen IoT-lösning för bearbetning.

IoT Hub ansvarar även för följande uppgifter i lösningen:

* Upprätthåller ett identitets register som lagrar ID: n och autentiseringsinställningarna för alla enheter som tillåts ansluta till portalen.
* Anropar metoder på dina enheter för lösnings acceleratorns räkning.
* Att underhålla enhetstvillingar för alla registrerade enheter. De egenskapsvärden som rapporteras av en enhet lagras i enhetstvillingen. De önskade egenskaper som anges i lösningsportalen och som enheten ska hämta vid nästa anslutning lagras också där.
* Att schemalägga jobb där egenskaper ska anges för flera enheter eller där metoder ska anropas på flera enheter.

## <a name="data-processing-and-analytics"></a>Databearbetning och analys

Lösningen innehåller följande komponenter i den logiska arkitekturen för data bearbetning och analys:

### <a name="iot-hub-manager-microservice"></a>Mikrotjänst för IoT Hub Manager

Lösningen innehåller [mikrotjänsten IoT Hub Manager](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/iothub-manager) som hanterar interaktioner med IoT-hubben, till exempel:

* Skapa och hantera IoT-enheter.
* Hantera enhets dubbla.
* Anropar metoder på enheter.
* Hantera IoT-autentiseringsuppgifter.

Den här tjänsten kör också IoT Hub frågor för att hämta enheter som tillhör användardefinierade grupper.

Mikrotjänsten tillhandahåller en RESTful-slutpunkt för att hantera enheter och enhets dubbla, anropa metoder och köra IoT Hub frågor.

### <a name="device-telemetry-microservice"></a>Mikrotjänst för enhets telemetri

[Mikrotjänsten Device telemetri](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/device-telemetry) tillhandahåller en RESTful-slutpunkt för Läs åtkomst till enhets telemetri som lagras i Time Series Insights. RESTful-slutpunkten aktiverar också CRUD-åtgärder för regler och Läs-/skriv åtkomst för larm definitioner från lagrings platsen.

### <a name="storage-adapter-microservice"></a>Mikrotjänsten Storage adapter

[Mikrotjänsten Storage adapter](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/storage-adapter) hanterar nyckel/värde-par, sammanfattar lagrings tjänstens semantik och presenterar ett enkelt gränssnitt för att lagra data i alla format med hjälp av Azure Cosmos dB.

Värdena är ordnade i samlingar. Du kan arbeta med enskilda värden eller hämta hela samlingar. Komplexa data strukturer serialiseras av klienterna och hanteras som en vanlig text nytto Last.

Tjänsten tillhandahåller en RESTful-slutpunkt för CRUD-åtgärder i nyckel/värde-par. values

### <a name="azure-cosmos-db"></a>Azure Cosmos DB

Distributioner av Solution Accelerator använder [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/) för att lagra regler, aviseringar, konfigurations inställningar och all annan kall lagring.

### <a name="azure-stream-analytics-manager-microservice"></a>Mikrotjänst för Azure Stream Analytics Manager

[Mikrotjänsten i Azure Stream Analytics Manager](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/asa-manager) hanterar Azure Stream Analytics (ASA)-jobb, inklusive inställning av deras konfiguration, start och stopp och övervakning av deras status.

ASA-jobbet stöds av två referens data uppsättningar. En data uppsättning definierar regler och en definierar enhets grupper. Regel referens data genereras från den information som hanteras av mikrotjänsten för Device telemetri. Mikrotjänsten i Azure Stream Analytics Manager omvandlar regler för telemetri till ström bearbetnings logiken.

Referens data för enhets grupper används för att identifiera vilken regel grupp som ska användas för ett inkommande telemetri. Enhets grupperna hanteras av konfigurations mikrotjänsten och använder sig av dubbla frågor i Azure IoT Hub Device.

ASA-jobben levererar Telemetrin från de anslutna enheterna till Time Series Insights för lagring och analys.

### <a name="azure-stream-analytics"></a>Azure Stream Analytics

[Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/) är en händelse bearbetnings motor som gör att du kan undersöka stora mängder data som strömmas från enheter.

### <a name="azure-time-series-insights"></a>Azure Time Series Insights

[Azure Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/) lagrar Telemetrin från de enheter som är anslutna till Solution Accelerator. Det möjliggör också visualisering och frågekörning av enhets telemetri i lösningens webb gränssnitt.

### <a name="configuration-microservice"></a>Konfigurations mikrotjänst

[Konfigurations-mikrotjänsten](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/config) innehåller en RESTful-slutpunkt för CRUD åtgärder på enhets grupper, lösnings inställningar och användar inställningar i Solution Accelerator. Det fungerar med Storage adapter-mikrotjänsten för att spara konfigurations data.

### <a name="authentication-and-authorization-microservice"></a>Autentisering och auktorisering av mikrotjänster

[Mikrotjänsten för autentisering och auktorisering](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/auth) hanterar de användare som har behörighet att komma åt Solution Accelerator. Användar hantering kan utföras med valfri identitets tjänst leverantör som stöder [OpenID Connect](https://openid.net/connect/).

### <a name="azure-active-directory"></a>Azure Active Directory

Vid distributioner av Solution Accelerator används [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/) som OpenID Connect-Provider. Azure Active Directory lagrar användar information och innehåller certifikat för att validera JWT-token-signaturer.

## <a name="presentation"></a>Presentation

Lösningen innehåller följande komponenter i presentations delen av den logiska arkitekturen:

[Webb användar gränssnittet är en Reakta JavaScript-applikation](https://github.com/Azure/pcs-remote-monitoring-webui). Programmet:

* Använder Java Script endast reagerar och körs helt i webbläsaren.
* Är formaterad med CSS.
* Interagerar med offentliga mikrotjänster via AJAX-anrop.

Användar gränssnittet visar alla funktioner i Solution Accelerator och samverkar med andra mikrotjänster, till exempel:

* Mikrotjänsten för autentisering och auktorisering för att skydda användar data.
* Mikrotjänsten i IoT Hub Manager för att visa och hantera IoT-enheter.

Användar gränssnittet integrerar Azure Time Series Insights Explorer för att aktivera frågor och analys av telemetri för enheter.

Konfigurations-mikrotjänsten gör det möjligt för användar gränssnittet att lagra och hämta konfigurations inställningar.

## <a name="next-steps"></a>Nästa steg

Om du vill utforska käll koden och dokumentationen för utvecklare börjar du med en av de två GitHub-databaserna:

* [Solution Accelerator för fjärrövervakning med Azure IoT (.net)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet).
* [Solution Accelerator för fjärrövervakning med Azure IoT (Java)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java).

Detaljerade diagram över lösnings arkitektur:
* [Lösnings Accelerator för arkitektur för fjärrövervakning](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Architecture).

Mer information om lösnings acceleratorn för fjärrövervakning finns i [Anpassa Solution Accelerator](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md).
