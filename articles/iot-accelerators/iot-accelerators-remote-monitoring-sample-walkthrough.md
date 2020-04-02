---
title: Översikt över snabblösningsaccelerator för fjärrövervakning – Azure | Microsoft-dokument
description: Den här artikeln innehåller en översikt över några av de viktigaste delarna i lösningen för fjärrövervakning så att du kan förstå hur det fungerar.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: f501eb55f72811063ddf1d8e02a0ce2137d598f3
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546316"
---
# <a name="remote-monitoring-solution-accelerator-overview"></a>Översikt över acceleratorn för fjärrövervakningslösning

[Remote Monitoring-lösningsacceleratorn](../iot-accelerators/about-iot-accelerators.md) implementerar en heltäckande övervakningslösning för flera datorer på fjärrplatser. I lösningen kombineras viktiga Azure-tjänster till en allmän implementering av affärsscenariot. Du kan använda lösningen som startpunkt för en egen implementering och [anpassa](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md) den efter dina egna affärsbehov.

I den här artikeln får du hjälp med några av de viktigaste delarna i lösningen för fjärrövervakning så att du kan förstå hur det fungerar. Med den här kunskapen kan du sedan:

* Felsöka problem i lösningen.
* Planera hur lösningen kan anpassas för att uppfylla dina behov.
* Utforma en egen IoT-lösning som använder Azure-tjänster.

Acceleratorkoden för fjärrövervakningslösningen är tillgänglig på GitHub:

* [.NET](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)

## <a name="logical-architecture"></a>Logisk arkitektur

I följande diagram beskrivs de logiska komponenterna i lösningsacceleratorn för fjärrövervakning överlagrad på [IoT-arkitekturen:](../iot-fundamentals/iot-introduction.md)

![Logisk arkitektur](./media/iot-accelerators-remote-monitoring-sample-walkthrough/remote-monitoring-architecture.png)

## <a name="why-microservices"></a>Varför mikrotjänster?

Molnarkitekturen har utvecklats sedan Microsoft släppte de första lösningsacceleratorerna. [Mikrotjänster](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/) har vuxit fram som en beprövad praxis för att uppnå skala och flexibilitet utan att offra utvecklingshastighet. Flera Microsoft-tjänster använder det här arkitektoniska mönstret internt med stor tillförlitlighet och skalbarhetsresultat. De uppdaterade lösningsacceleratorerna omsätter dessa lärdomar i praktiken så att du också kan dra nytta av dem.

> [!TIP]
> Läs mer om arkitekturer för mikrotjänster i [.NET Application Architecture](https://www.microsoft.com/net/learn/architecture) (.NET-programarkitektur) och [Microservices: An application revolution powered by the cloud](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/) (Mikrotjänster: En programrevolution som drivs av molnet).

## <a name="device-connectivity"></a>Enhetsanslutning

Lösningen innehåller följande komponenter i enhetsanslutningsdelen av den logiska arkitekturen:

### <a name="real-devices"></a>Verkliga enheter

Du kan ansluta riktiga enheter till lösningen. Du kan implementera beteendet hos dina simulerade enheter med hjälp av Azure IoT-enhetenSDK:er.

Du kan etablera riktiga enheter från instrumentpanelen i lösningsportalen.

### <a name="device-simulation-microservice"></a>Mikrotjänst för enhetssimulering

Lösningen innehåller [mikrotjänsten för enhetssimulering](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/device-simulation) som gör att du kan hantera en pool med simulerade enheter från lösningsportalen för att testa lösningens slut-till-slut-flöde. De simulerade enheterna:

* Generera telemetri från enhet till moln.
* Svara på metodanrop från molnet till enheten från IoT Hub.

Mikrotjänsten ger en RESTful-slutpunkt där du kan skapa, starta och stoppa simuleringar. Varje simulering består av en uppsättning virtuella enheter av olika typer, som skickar telemetri och svarar på metodanrop.

Du kan etablera simulerade enheter från instrumentpanelen i lösningsportalen.

### <a name="iot-hub"></a>IoT Hub

[IoT-hubben](../iot-hub/index.yml) intar telemetri som skickas från både de verkliga och simulerade enheterna i molnet. IoT-hubben gör telemetrin tillgänglig för tjänsterna i IoT-lösningens backend för bearbetning.

IoT Hub ansvarar även för följande uppgifter i lösningen:

* Underhåller ett identitetsregister som lagrar ID:n och autentiseringsnycklarna för alla enheter som tillåts ansluta till portalen.
* Anropar metoder för dina enheter för lösningsacceleratorns räkning.
* Att underhålla enhetstvillingar för alla registrerade enheter. De egenskapsvärden som rapporteras av en enhet lagras i enhetstvillingen. De önskade egenskaper som anges i lösningsportalen och som enheten ska hämta vid nästa anslutning lagras också där.
* Att schemalägga jobb där egenskaper ska anges för flera enheter eller där metoder ska anropas på flera enheter.

## <a name="data-processing-and-analytics"></a>Databearbetning och analys

Lösningen innehåller följande komponenter i databehandlings- och analysdelen av den logiska arkitekturen:

### <a name="iot-hub-manager-microservice"></a>Mikrotjänst för IoT Hub-hanterare

Lösningen innehåller [mikrotjänsten IoT Hub manager](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/iothub-manager) för att hantera interaktioner med din IoT-hubb, till exempel:

* Skapa och hantera IoT-enheter.
* Hantera enhetstvillingar.
* Anropa metoder på enheter.
* Hantera IoT-autentiseringsuppgifter.

Den här tjänsten kör också IoT Hub-frågor för att hämta enheter som tillhör användardefinierade grupper.

Mikrotjänsten tillhandahåller en RESTful-slutpunkt för att hantera enheter och enhetstvillingar, anropa metoder och köra IoT Hub-frågor.

### <a name="device-telemetry-microservice"></a>Mikrotjänst för enhetstelemetri

[Enhetens telemetrimikrotjänst](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/device-telemetry) ger en RESTful-slutpunkt för läsåtkomst till enhetstelemetri som lagras i Time Series Insights. SLUTPUNKTEN RESTful möjliggör också CRUD-åtgärder för regler och läs-/skrivåtkomst för larmdefinitioner från lagring.

### <a name="storage-adapter-microservice"></a>Mikrotjänst för lagringsadapter

[Mikrotjänsten för lagringskortet](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/storage-adapter) hanterar nyckelvärdespar, abstraherar lagringstjänstens semantik och presenterar ett enkelt gränssnitt för att lagra data i alla format med Azure Cosmos DB.

Värden ordnas i samlingar. Du kan arbeta med enskilda värden eller hämta hela samlingar. Komplexa datastrukturer serialiseras av klienterna och hanteras som enkel textnyttolast.

Tjänsten tillhandahåller en RESTful-slutpunkt för CRUD-åtgärder på nyckelvärdespar. values

### <a name="azure-cosmos-db"></a>Azure Cosmos DB

Lösningsacceleratorer använder [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/) för att lagra regler, aviseringar, konfigurationsinställningar och all annan kyllagring.

### <a name="azure-stream-analytics-manager-microservice"></a>Mikrotjänsten Azure Stream Analytics-chef

[Azure Stream Analytics manager-mikrotjänsten](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/asa-manager) hanterar Azure Stream Analytics (ASA) jobb, inklusive att ange deras konfiguration, starta och stoppa dem och övervaka deras status.

ASA-jobbet stöds av två referensdatauppsättningar. En datauppsättning definierar regler och en definierar enhetsgrupper. Regelreferensdata genereras från den information som hanteras av enhetens telemetrimikrotjänst. Azure Stream Analytics manager-mikrotjänsten omvandlar telemetriregler till dataflödesbearbetningslogik.

Enhetsgruppernas referensdata används för att identifiera vilken grupp av regler som ska tillämpas på ett inkommande telemetrimeddelande. Enhetsgrupperna hanteras av konfigurationsmikrotjänsten och använder azure IoT Hub-enhets dubbla frågor.

ASA-jobben levererar telemetrin från anslutna enheter till Time Series Insights för lagring och analys.

### <a name="azure-stream-analytics"></a>Azure Stream Analytics

[Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/) är en händelsebearbetningsmotor som gör att du kan undersöka stora volymer dataströmning från enheter.

### <a name="azure-time-series-insights"></a>Azure Time Series Insights

[Azure Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/) lagrar telemetrin från de enheter som är anslutna till lösningsacceleratorn. Det gör det också möjligt att visualisera och fråga enhetstelemetri i lösningswebbgränssnittet.

### <a name="configuration-microservice"></a>Mikrotjänst för konfiguration

[Konfigurationsmikrotjänsten](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/config) tillhandahåller en RESTful-slutpunkt för CRUD-åtgärder på enhetsgrupper, lösningsinställningar och användarinställningar i lösningsacceleratorn. Det fungerar med lagringskortet mikrotjänst för att bevara konfigurationsdata.

### <a name="authentication-and-authorization-microservice"></a>Mikrotjänst för autentisering och auktorisering

[Mikrotjänsten för autentisering och auktorisering](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/auth) hanterar de användare som har behörighet att komma åt lösningsacceleratorn. Användarhantering kan göras med hjälp av alla identitetstjänstleverantörer som stöder [OpenId Connect](https://openid.net/connect/).

### <a name="azure-active-directory"></a>Azure Active Directory

Lösningsacceleratorer använder [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/) som OpenID Connect-provider. Azure Active Directory lagrar användarinformation och tillhandahåller certifikat för att validera JWT-tokensignaturer.

## <a name="presentation"></a>Presentation

Lösningen innehåller följande komponenter i presentationsdelen av den logiska arkitekturen:

[Webbanvändargränssnittet är ett React Javascript-program](https://github.com/Azure/pcs-remote-monitoring-webui). Ansökan:

* Använder Javascript React endast och körs helt i webbläsaren.
* Är stylad med CSS.
* Interagerar med offentliga mikrotjänster via AJAX-samtal.

Användargränssnittet presenterar alla funktioner för lösningsaccelerator och interagerar med andra mikrotjänster, till exempel:

* Mikrotjänsten för autentisering och auktorisering för att skydda användardata.
* Mikrotjänsten IoT Hub hanterar för att lista och hantera IoT-enheter.

Användargränssnittet integrerar Utforskaren för Azure Time Series Insights för att aktivera frågor och analys av enhetstelemetri.

Konfigurationsmikrotjänsten gör det möjligt för användargränssnittet att lagra och hämta konfigurationsinställningar.

## <a name="next-steps"></a>Nästa steg

Om du vill utforska källkoden och utvecklardokumentationen börjar du med något av de två GitHub-databaserna:

* [Lösningsaccelerator för fjärrövervakning med Azure IoT (.NET)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet).
* [Lösningsaccelerator för fjärrövervakning med Azure IoT (Java)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java).

Detaljerade lösningar arkitektur diagram:
* [Lösningsaccelerator för fjärrövervakningsarkitektur](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Architecture).

Mer begreppsmässig information om lösningsacceleratorn för fjärrövervakning finns i [Anpassa lösningsacceleratorn](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md).
