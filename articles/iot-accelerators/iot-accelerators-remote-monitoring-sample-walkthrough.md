---
title: Fjärråtkomst övervakning solution accelerator översikt – Azure | Microsoft Docs
description: En översikt över lösningsacceleratorn för fjärrövervakning.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 11/10/2017
ms.author: dobett
ms.openlocfilehash: dfe584532efeab1dbc0d2928b7afb0a6695a21ee
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2018
ms.locfileid: "39184953"
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

### <a name="simulated-devices"></a>Simulerade enheter

Lösningen innehåller en mikrotjänst som hjälper dig att hantera en pool med simulerade enheter att testa flödet slutpunkt till slutpunkt i lösningen. De simulerade enheterna:

* Generera enhet-till-moln-telemetri.
* Svara på metodanrop för moln-till-enhet från IoT Hub.

Mikrotjänst innehåller en RESTful-slutpunkt som du kan skapa, starta och stoppa simuleringar. Varje simulering består av en uppsättning virtuella enheter av olika typer, som skickar telemetri och svarar på metodanrop.

Du kan etablera simulerade enheter från instrumentpanelen på lösningsportalen.

### <a name="physical-devices"></a>Fysiska enheter

Du kan ansluta fysiska enheter till lösningen. Du kan implementera beteendet för dina simulerade enheter med hjälp av SDK: er för Azure IoT-enheter.

Du kan etablera fysiska enheter från instrumentpanelen på lösningsportalen.

### <a name="iot-hub-and-the-iot-manager-microservice"></a>IoT Hub och IoT manager mikrotjänst

Den [IoT-hubb](../iot-hub/index.yml) matar in data som skickas från enheterna till molnet och gör dem tillgängliga för den `telemetry-agent` mikrotjänst.

IoT Hub ansvarar även för följande uppgifter i lösningen:

* Underhåller ett identitetsregister som lagrar ID: N och autentiseringsnycklar för alla enheter som har behörighet att ansluta till portalen. Du kan aktivera och inaktivera enheter via ID-registret.
* Att anropa metoder på dina enheter för lösningsportalens räkning.
* Att underhålla enhetstvillingar för alla registrerade enheter. De egenskapsvärden som rapporteras av en enhet lagras i enhetstvillingen. De önskade egenskaper som anges i lösningsportalen och som enheten ska hämta vid nästa anslutning lagras också där.
* Att schemalägga jobb där egenskaper ska anges för flera enheter eller där metoder ska anropas på flera enheter.

Lösningen innehåller den `iot-manager` mikrotjänst kan hantera interaktioner med IoT-hubben som:

* Skapa och hantera IoT-enheter.
* Hantera enhetstvillingar.
* Anropa metoder på enheter.
* Hantera IoT-autentiseringsuppgifter.

Den här tjänsten körs även IoT-hubb frågor för att hämta enheter som hör till användardefinierade grupper.

Mikrotjänst innehåller en RESTful-slutpunkt för att hantera enheter och enhetstvillingar, anropa metoder och köra frågor för IoT Hub.

## <a name="data-processing-and-analytics"></a>Databearbetning och analys

Lösningen innehåller följande komponenter i databearbetning och analys som en del av logisk arkitektur:

### <a name="device-telemetry"></a>Enhetstelemetri

Lösningen innehåller två mikrotjänster för att hantera enhetstelemetri.

Den [telemetri-agent](https://github.com/Azure/telemetry-agent-dotnet) mikrotjänster:

* Lagrar telemetri i Azure Cosmos DB.
* Analyserar telemetriströmmen från enheterna.
* Genererar larm enligt definierade regler.

Larmen lagras i Azure Cosmos DB.

Den [telemetri-agent](https://github.com/Azure/telemetry-agent-dotnet) mikrotjänst gör det möjligt för lösningsportalen att läsa telemetri som skickas från enheterna. Lösningsportalen använder den här tjänsten för att:

* Definiera regler för övervakning, till exempel de tröskelvärden som utlöser larm
* Hämta listan över senaste larm.

Använda RESTful-slutpunkten från den här mikrotjänster för att hantera telemetri, regler och larm.

### <a name="storage"></a>Storage

Den [lagringsadapter](https://github.com/Azure/pcs-storage-adapter-dotnet) mikrotjänst är ett kort framför den huvudsakliga storage-tjänst som används för solution accelerator. Det ger enkel insamling och nyckel / värde-lagring.

Standarddistribution för solution accelerator använder Azure Cosmos DB som dess huvudsakliga lagringstjänst.

Azure Cosmos DB-databasen lagrar data i solution accelerator. Den **lagringsadapter** mikrotjänst fungerar som en adapter för mikrotjänster i lösningen till åtkomst till lagringstjänster.

## <a name="presentation"></a>Presentation

Lösningen innehåller följande komponenter i presentation-delen av logisk arkitektur:

Den [webbaserat användargränssnitt är ett reagera Javascript-program](https://github.com/Azure/pcs-remote-monitoring-webui). Program:

* Använder Javascript reagera endast och kör helt i webbläsaren.
* Är formaterad med CSS.
* Samverkar med offentliga internetuppkopplade mikrotjänster via AJAX-anrop.

Användargränssnittet visar alla funktioner för lösningen-accelerator och samverkar med andra tjänster som:

* Den [autentisering](https://github.com/Azure/pcs-auth-dotnet) mikrotjänst att skydda användardata.
* Den [iothub-manager](https://github.com/Azure/iothub-manager-dotnet) mikrotjänst att visa och hantera IoT-enheter.

Den [ui-config](https://github.com/Azure/pcs-config-dotnet) mikrotjänst gör det möjligt för användargränssnittet för att lagra och hämta konfigurationsinställningar.

## <a name="next-steps"></a>Nästa steg

Om du vill utforska dokumentationen för källa kod och utvecklare kan börja med en två huvudsakliga GitHub-databaser:

* [Solution accelerator för fjärrövervakning med Azure IoT (.NET)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/).
* [Solution accelerator för fjärrövervakning med Azure IoT (Java)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java).

Arkitekturdiagram för detaljerad lösningen:
* [Solution accelerator för fjärrövervakning arkitektur](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Architecture).

Mer information om lösningsacceleratorn för fjärrövervakning finns [anpassa solution accelerator](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md).
