---
title: Fjärråtkomst övervakning solution accelerator översikt – Azure | Microsoft Docs
description: En översikt över lösningsacceleratorn för fjärrövervakning.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: dobett
ms.openlocfilehash: f0f43826c50679cb3de88aef466795cbb9e9e76f
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2018
ms.locfileid: "50139500"
---
# <a name="remote-monitoring-solution-accelerator-overview"></a>Remote Monitoring solution accelerator översikt

Den fjärrövervakning [lösningsaccelerator](../iot-accelerators/about-iot-accelerators.md) implementerar en lösning för slutpunkt till slutpunkt-övervakning för flera datorer på fjärrplatser. I lösningen kombineras viktiga Azure-tjänster till en allmän implementering av affärsscenariot. Du kan använda lösningen som utgångspunkt för en egen implementering och [anpassa](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md) dem efter dina specifika affärskrav.

Den här artikeln vägleder dig igenom några av de viktigaste elementen i lösningen för fjärrövervakning så att du förstår hur den fungerar. Med den här kunskapen kan du sedan:

* Felsöka problem i lösningen.
* Planera hur lösningen kan anpassas för att uppfylla dina behov.
* Utforma en egen IoT-lösning som använder Azure-tjänster.

## <a name="logical-architecture"></a>Logisk arkitektur

Följande diagram illustrerar de logiska komponenterna i den lösningsacceleratorn för fjärrövervakning finns på den [IoT-arkitekturen](../iot-fundamentals/iot-introduction.md):

![Logisk arkitektur](./media/iot-accelerators-remote-monitoring-sample-walkthrough/remote-monitoring-architecture.png)

## <a name="why-microservices"></a>Varför mikrotjänster?

Molnarkitektur har utvecklats eftersom publicerades första Lösningsacceleratorer. [Mikrotjänster](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/) har utsågs till en beprövad metod att uppnå skalbarhet och flexibilitet utan att offra utvecklingshastigheten. Flera Microsoft-tjänster använder den här arkitekturmönster internt med bra tillförlitlighet och skalbarhet resultat. Uppdaterade Lösningsacceleratorer placerar dessa erfarenheter i praktiken så att du kan också dra nytta av dem.

> [!TIP]
> Läs mer om arkitekturer för mikrotjänster i [.NET Application Architecture](https://www.microsoft.com/net/learn/architecture) (.NET-programarkitektur) och [Microservices: An application revolution powered by the cloud](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/) (Mikrotjänster: En programrevolution som drivs av molnet).

## <a name="device-connectivity"></a>Enhetsanslutning

Lösningen innehåller följande komponenter i enheten anslutning delen av logisk arkitektur:

### <a name="physical-devices"></a>Fysiska enheter

Du kan ansluta fysiska enheter till lösningen. Du kan implementera beteendet för dina simulerade enheter med hjälp av SDK: er för Azure IoT-enheter.

Du kan etablera fysiska enheter från instrumentpanelen på lösningsportalen.

### <a name="device-simulation-microservice"></a>Enheten simulering mikrotjänst

Lösningen innehåller den [enheten simulering mikrotjänst](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/device-simulation) som hjälper dig att hantera en pool med simulerade enheter från lösningsportalen att testa flödet slutpunkt till slutpunkt i lösningen. De simulerade enheterna:

* Generera enhet-till-moln-telemetri.
* Svara på metodanrop för moln-till-enhet från IoT Hub.

Mikrotjänst innehåller en RESTful-slutpunkt som du kan skapa, starta och stoppa simuleringar. Varje simulering består av en uppsättning virtuella enheter av olika typer, som skickar telemetri och svarar på metodanrop.

Du kan etablera simulerade enheter från instrumentpanelen på lösningsportalen.

### <a name="iot-hub"></a>IoT Hub

Den [IoT-hubb](../iot-hub/index.yml) matar in telemetri som skickas från både fysiska och simulerade enheter till molnet. IoT-hubben tillgängliggör telemetri till tjänsterna i serverdelen för IoT-lösning för bearbetning.

IoT Hub ansvarar även för följande uppgifter i lösningen:

* Underhåller ett identitetsregister som lagrar ID: N och autentiseringsnycklar för alla enheter som har behörighet att ansluta till portalen.
* Anropar metoder på enheterna för solution accelerator.
* Att underhålla enhetstvillingar för alla registrerade enheter. De egenskapsvärden som rapporteras av en enhet lagras i enhetstvillingen. De önskade egenskaper som anges i lösningsportalen och som enheten ska hämta vid nästa anslutning lagras också där.
* Att schemalägga jobb där egenskaper ska anges för flera enheter eller där metoder ska anropas på flera enheter.

## <a name="data-processing-and-analytics"></a>Databearbetning och analys

Lösningen innehåller följande komponenter i databearbetning och analys som en del av logisk arkitektur:

### <a name="iot-hub-manager-microservice"></a>IoT Hub manager mikrotjänst

Lösningen innehåller den [IoT Hub manager mikrotjänst](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/iothub-manager) att hantera interaktioner med IoT-hubben som:

* Skapa och hantera IoT-enheter.
* Hantera enhetstvillingar.
* Anropa metoder på enheter.
* Hantera IoT-autentiseringsuppgifter.

Den här tjänsten körs även IoT-hubb frågor för att hämta enheter som hör till användardefinierade grupper.

Mikrotjänst innehåller en RESTful-slutpunkt för att hantera enheter och enhetstvillingar, anropa metoder och köra frågor för IoT Hub.

### <a name="device-telemetry-microservice"></a>Enheten telemetri mikrotjänst

Den [enheten telemetri mikrotjänst](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/device-telemetry) innehåller en RESTful-slutpunkt för läsåtkomst till enhetstelemetri som lagras i Time Series Insights. RESTful-slutpunkten kan också CRUD-åtgärder på regler och läs-/ skrivbehörighet för larm definitionerna från lagring.

### <a name="storage-adapter-microservice"></a>Storage nätverkskort mikrotjänst

Den [storage nätverkskort mikrotjänst](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/storage-adapter) hanterar nyckel / värde-par, abstrahera storage service-semantik och presentera ett enkelt gränssnitt för att lagra data i alla format med hjälp av Azure Cosmos DB.

Värden är ordnade i samlingar. Du kan arbeta med enskilda värden eller hämta hela samlingar. Komplexa datastrukturer serialiseras av klienter och hanteras som nyttolasten för enkel text.

Tjänsten tillhandahåller en RESTful-slutpunkt för CRUD-åtgärder på nyckel / värde-par. Värden

### <a name="azure-cosmos-db"></a>Azure Cosmos DB

Solution accelerator distributioner använder [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/) att lagra regler, larm, konfigurationsinställningar och alla andra kall lagring.

### <a name="azure-stream-analytics-manager-microservice"></a>Azure Stream Analytics manager mikrotjänst

Den [Azure Stream Analytics manager mikrotjänst](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/asa-manager) hanterar Azure Stream Analytics (ASA) jobb, inklusive att ställa in deras konfiguration, starta och stoppa dem och övervaka deras status.

ASA-jobbet stöds av två referensdatauppsättningar. En datauppsättning som definierar regler och ett definierar enhetsgrupper. Referensdata regler skapas från den information som hanteras av enheten telemetri mikrotjänst. Azure Stream Analytics manager mikrotjänst omvandlar telemetri regler till logik för strömbearbetning.

Referensdata för enheten grupper används för att identifiera vilken grupp av regler som avser ett inkommande telemetri-meddelande. Enhetsgrupper som hanteras av configuration mikrotjänster och Använd enhetstvillingfrågor för Azure IoT Hub.

ASA-jobben leverera telemetri från anslutna enheter till Time Series Insights för lagring och analys.

### <a name="azure-stream-analytics"></a>Azure Stream Analytics

[Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/) är en händelsebearbetningsmotor som hjälper dig att undersöka stora mängder data som strömmas från enheter.

### <a name="azure-time-series-insights"></a>Azure Time Series Insights

[Azure Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/) butiker telemetri från enheterna är anslutna till lösningsaccelerator. Dessutom kan visualisera och fråga enhetstelemetri i webbgränssnittet för lösningen.

> [!NOTE]
> Time Series Insights är inte tillgänglig för tillfället i Azure i Kina-molnet. Nya fjärrövervakning solution accelerator distributioner i Azure i Kina-molnet använder Cosmos DB för lagring av alla.

### <a name="configuration-microservice"></a>Konfiguration av mikrotjänster

Den [configuration mikrotjänst](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/config) innehåller en RESTful-slutpunkt för CRUD-åtgärder på enhetsgrupper och lösningsinställningar användarinställningar i solution accelerator. Det fungerar med mikrotjänster för storage-nätverkskort att spara konfigurationsdata.

### <a name="authentication-and-authorization-microservice"></a>Autentisering och auktorisering mikrotjänst

Den [autentisering och auktorisering mikrotjänst](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/auth) hanterar de användare som har behörighet att komma åt solution accelerator. Hantering av användare kan göras med hjälp av någon identity-tjänstleverantör som stöder [OpenId Connect](http://openid.net/connect/).

### <a name="azure-active-directory"></a>Azure Active Directory

Solution accelerator distributioner använder [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/) som en OpenID Connect-provider. Azure Active Directory lagrar information om användare och tillhandahåller certifikat för att verifiera JWT-token signaturer.

## <a name="presentation"></a>Presentation

Lösningen innehåller följande komponenter i presentation-delen av logisk arkitektur:

Den [webbaserat användargränssnitt är ett reagera Javascript-program](https://github.com/Azure/pcs-remote-monitoring-webui). Program:

* Använder Javascript reagera endast och kör helt i webbläsaren.
* Är formaterad med CSS.
* Samverkar med offentliga internetuppkopplade mikrotjänster via AJAX-anrop.

Användargränssnittet visar alla funktioner för lösningen-accelerator och samverkar med andra mikrotjänster:

* Autentisering och auktorisering mikrotjänst att skydda användardata.
* IoT Hub manager mikrotjänst att visa och hantera IoT-enheter.

Användargränssnittet integreras i Azure Time Series Insights explorer om du vill aktivera frågor och analys av enhetstelemetri.

Konfiguration av mikrotjänster gör det möjligt för användargränssnittet för att lagra och hämta konfigurationsinställningar.

## <a name="next-steps"></a>Nästa steg

Om du vill utforska dokumentationen för källa code och utvecklarverktyg, börja med någon av de två GitHub-databaserna:

* [Solution accelerator för fjärrövervakning med Azure IoT (.NET)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet).
* [Solution accelerator för fjärrövervakning med Azure IoT (Java)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java).

Arkitekturdiagram för detaljerad lösningen:
* [Solution accelerator för fjärrövervakning arkitektur](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Architecture).

Mer information om lösningsacceleratorn för fjärrövervakning finns [anpassa solution accelerator](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md).
