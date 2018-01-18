---
title: "Översikt över förkonfigurerade lösningar i Azure IoT Suite | Microsoft Docs"
description: "En beskrivning av de förkonfigurerade lösningarna i Azure IoT Suite och deras arkitektur med länkar till ytterligare resurser."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 59009f37-9ba0-4e17-a189-7ea354a858a2
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 17/01/2018
ms.author: dobett
ms.openlocfilehash: 0ac6226b1e5773b1212b70172e2c13a0a5c925a8
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/17/2018
---
# <a name="what-is-azure-iot-suite"></a>Vad är Azure IoT Suite?

Azure IoT Suite är en uppsättning *förkonfigurerade lösningar* som:

* Distribuerar på några minuter
* Hjälper dig att komma igång snabbt
* Du kan anpassa för att uppfylla dina specifika krav

De förkonfigurerade *IoT Suite*-lösningarna är utformade enligt samma principer och mål.

I följade video visas en översikt över den förkonfigurerade lösningen för fjärrövervakning:

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Meet-the-new-Remote-Monitoring-accelerator-for-Azure-IoT/Player]

## <a name="preconfigured-solutions-overview"></a>Översikt över förkonfigurerade lösningar

En förkonfigurerad lösning är en implementering med öppen källkod av ett vanligt IoT-lösningsmönster som du kan distribuera till Azure via din prenumeration. Varje förkonfigurerad lösning kombinerar anpassad kod och Azure-tjänster för att implementera ett specifikt IoT-scenario eller scenarier. Du kan anpassa alla dessa scenarion för att uppfylla dina specifika krav. Några vanliga scenarier:

* Visualisera data på en omfattande instrumentpanel för djupa insikter och lösningsstatus.
* Konfigurera regler och larm för live-telemetri från IoT-enheter.
* Schemalägga enhetshanteringsjobb, t.ex uppdateringar av programvara och konfigurationer.
* Etablera egna anpassade fysiska eller simulerade enheter.
* Felsök och åtgärda problem i dina IoT-enhetsgrupper.

Varje förkonfigurerad lösning är en fullständig implementering från slutpunkt till slutpunkt som kan använda simulerade eller fysiska enheter för att generera telemetri. Du kan använda de förkonfigurerade lösningarna för att lösa problem snabbare för att:

* Tillhandahålla en startpunkt för dina egna IoT-lösningar.
* Lära dig mer om vanliga mönster i utformningen och utvecklingen av IoT-lösningar.

Tre förkonfigurerade lösningar är tillgängliga idag:

* [Fjärrövervakning](iot-suite-remote-monitoring-explore.md)
* [Förebyggande underhåll](iot-suite-predictive-overview.md)
* [Ansluten fabrik](iot-suite-connected-factory-overview.md)

Följande tabell visar hur lösningarna mappar till specifika IoT-funktioner:

| Lösning | Datainhämtning | Enhetsidentitet | Enhetshantering | Edge-bearbetning | Kommando och kontroll | Regler och åtgärder | Förutsägelseanalys |
| ------------------------------------------------------------ | -- | -- | -- | -- | -- | -- | -- |
| [Fjärrövervakning](iot-suite-remote-monitoring-explore.md)  |Ja |Ja |Ja |-   |Ja |Ja |-   |
| [Förebyggande underhåll](iot-suite-predictive-overview.md)   |Ja |Ja |-   |-   |Ja |Ja |Ja |
| [Ansluten fabrik](iot-suite-connected-factory-overview.md) |Ja |- |- |Ja |Ja |Ja |-   |

* *Datainhämtning*: Inhämtning av data till molnet i hög skala.
* *Enhetsidentitet*: Hantera unika enhetsidentiteter och kontrollera enhetsåtkomst till lösningen.
* *Enhetshantering*: Hantera enhetsmetadata och utför åtgärder som omstarter av enheten och uppgraderingar av den inbyggda programvaran.
* *Kommando och kontroll*: Skicka meddelanden till en enhet från molnet för att utlösa en åtgärd på enheten.
* *Regler och åtgärder*: Lösningens serverdel använder regler för att agera på specifika data från enheten till molnet.
* *Förutsägelseanalys*: Lösningens backend-server analyserar data från enheten till molnet för att förutsäga när specifika åtgärder ska äga rum. Lösningen kan till exempel analysera telemetri från en flygplansmotor för att fastställa när motorunderhåll krävs.

> [!NOTE]
> Besök [Microsoft Azure IoT Suite](https://www.azureiotsuite.com/) om du vill distribuera en förkonfigurerad lösning och lära dig mer om hur du anpassar den.

## <a name="azure-services"></a>Azure-tjänster

När du distribuerar en förkonfigurerad lösning konfigurerar etableringsprocessen ett antal Azure-tjänster. I följande tabell visas de tjänster som används i förkonfigurerade lösningar:

|                      | Fjärrövervakning  | Förebyggande underhåll | Ansluten fabrik |
| -------------------- | ------------------ | ---------------------- | ----------------- |
| IoT Hub              | Ja                |                        | Ja               |
| Händelsehubbar           |                    | Ja                    |                   |
| Time Series Insights |                    |                        | Ja               |
| Container Services   | Ja                |                        |                   |
| Stream Analytics     |                    | Ja                    |                   |
| Web Apps             | Ja                | Ja                    | Ja               |
| Cosmos DB            | Ja                | Ja                    |                    |
| Azure Storage         |                    | Ja                    | Ja               |

> [!NOTE]
> Mer information om resurser som har distribuerats i den förkonfigurerade lösningen för fjärrövervakning finns i den här [artikeln](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/blob/master/README.md#basic-vs-standard-deployments) på GitHub.

* [Azure IoT Hub](../iot-hub/index.md). Den här tjänsten tillhandahåller funktioner för meddelandehantering från ”enhet till moln” och från ”moln till enhet” och fungerar som en gateway till molnet och andra viktiga IoT Suite-tjänster. Tjänsten gör att du kan ta emot meddelanden från dina enheter i hög skala och skicka kommandon till dina enheter. Med tjänsten kan du även [hantera dina enheter](../iot-hub/iot-hub-device-management-overview.md). Du kan till exempel konfigurera, starta om eller utföra en fabriksåterställning på en eller flera enheter som är anslutna till hubben.
* [Azure Event Hubs](../event-hubs/index.md). Tjänst för inmatning av stora händelsevolymer. Se [Comparison of Azure IoT Hub and Azure Event Hubs](../iot-hub/iot-hub-compare-event-hubs.md) (Jämförelse mellan Azure IoT Hub och Azure Event Hubs).
* [Azure Time Series Insights](../time-series-insights/index.yml). De förkonfigurerade lösningarna använder den här tjänsten för att analysera och visa telemetridata från dina enheter.
* [Azure Container Service](../container-service/index.yml). Den här tjänsten är värd för och hanterar mikrotjänster i förkonfigurerade lösningar.
* [Azure Cosmos DB](../cosmos-db/index.yml) och [Azure Storage](../storage/index.yml) för datalagring.
* [Azure Stream Analytics](../stream-analytics/index.md). Den förkonfigurerade lösningen Förutsägande underhåll använder den här tjänsten för att bearbeta inkommande telemetri, utföra sammansättningar och identifiera händelser. Den här förkonfigurerade lösningen använder också dataströmsanalys för att bearbeta informationsmeddelanden som innehåller data, till exempel metadata eller kommandosvar från enheter.
* [Azure Web Apps](../app-service/index.yml) som värd för den anpassade programkoden i de förkonfigurerade lösningarna.

En översikt över arkitekturen i en typisk IoT-lösning finns i [Microsoft Azure och Sakernas Internet (IoT)](iot-suite-what-is-azure-iot.md).

## <a name="whats-new-in-preconfigured-solutions"></a>Vad är nytt i de förkonfigurerade lösningarna?

Microsoft uppdaterar de förkonfigurerade lösningarna till en ny arkitektur baserad på mikrotjänster. I följande tabell visas den nuvarande statusen för de förkonfigurerade lösningarna:

| Förkonfigurerad lösning | Arkitektur  | Språk     |
| ---------------------- | ------------- | ------------- |
| Fjärrövervakning      | Mikrotjänster | Java och .NET |
| Förebyggande underhåll | MVC           | .NET          |
| Ansluten fabrik      | MVC           | .NET          |

Följande avsnitt beskriver vad som är nytt i de förkonfigurerade lösningarna som är baserade på mikrotjänster:

### <a name="microservices"></a>Mikrotjänster

Den nya versionen av den förkonfigurerade lösningen för fjärrövervakning använder en arkitektur baserad på mikrotjänster. Den här förkonfigurerade lösningen består av flera mikrotjänster som en *IoT Hub-hanterare* och en *Lagringshanterare*. Både Java- och .NET-versioner av varje mikrotjänst är tillgänglig för nedladdning tillsammans med relaterad utvecklardokumentation. Läs mer om mikrotjänsterna i [Remote monitoring architecture](iot-suite-remote-monitoring-sample-walkthrough.md) (Arkitektur för fjärrövervakning).

Den här arkitekturen för mikrotjänster är ett beprövat mönster för molnlösningar som:

* Är skalbar.
* Aktiverar utökningsbarhet.
* Är lätt att förstå.
* Gör det möjligt att byta ut enskilda tjänster mot alternativa tjänster.

> [!TIP]
> Läs mer om arkitekturer för mikrotjänster i [.NET Application Architecture](https://www.microsoft.com/net/learn/architecture) (.NET-programarkitektur) och [Microservices: An application revolution powered by the cloud](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/) (Mikrotjänster: En programrevolution som drivs av molnet).

När du distribuerar den nya versionen av fjärrövervakningen så måste du välja något av följande distributionsalternativ:

* **Basic:** Version till reducerad kostnad för en demonstration eller för att testa en distribution. Alla mikrotjänster distribueras till en enda virtuell Azure-dator.
* **Standard:** Expanderad distribution av infrastruktur för att utveckla en produktionsdistribution. Azure Container Service distribuerar mikrotjänsterna till flera virtuella Azure-datorer. Kubernetes orkestrerar Docker-behållare som värdar för enskilda mikrotjänster.

### <a name="language-choices-java-and-net"></a>Språkval: Java och .NET

Implementeringar av var och en av mikrotjänsterna är tillgängliga i både Java- och .NET. Precis som .NET-koden är Java-källkoden öppen källkod och finns tillgänglig för dig så du kan anpassa den efter dina specifika krav:

* [Remote monitoring .NET GitHub repository](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet) (.NET GitHub-lagringsplatsen för fjärrövervakning)
* [Remote monitoring Java GitHub repository](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java) (Java GitHub-lagringsplatsen för fjärrövervakning)

Om du vill se andra språkimplementeringar lägger du till en begäran om det i [Azure IoT UserVoice](https://feedback.azure.com/forums/321918-azure-iot).

### <a name="react-user-interface-framework"></a>Ramverk för användargränssnittet React

Användargränssnittet skapas med javascript-biblioteket [React](https://facebook.github.io/react/). Källkoden är öppen källkod och finns tillgänglig för dig att ladda ned och anpassa.

## <a name="next-steps"></a>Nästa steg

Nu när du har en översikt över de förkonfigurerade IoT Suite-lösningar föreslår vi följande nästa steg för varje förkonfigurerad lösning:

* [Utforska Resource Manager-distributionsmodellen för Azure IoT Suite fjärrövervakningslösning](iot-suite-remote-monitoring-explore.md).
* [Översikt över förkonfigurerad lösning för förebyggande underhåll](iot-suite-predictive-overview.md).
* [Kom igång med den förkonfigurerade lösningen Ansluten fabrik](iot-suite-connected-factory-overview.md).

Mer information om IoT-lösningsarkitekturer finns i [Microsoft Azure IoT-tjänster: referensarkitektur](http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf).
