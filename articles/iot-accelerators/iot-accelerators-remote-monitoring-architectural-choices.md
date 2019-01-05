---
title: Remote Monitoring solution arkitektoniska val – Azure | Microsoft Docs
description: Den här artikeln beskriver fram arkitekturrelaterad eller teknisk valen som gjorts i fjärrövervakning
author: timlaverty
manager: camerons
ms.author: timlav
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/20/2018
ms.topic: conceptual
ms.openlocfilehash: 9140739e1c9610cb4cbefb611546fe9588512d06
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54050599"
---
# <a name="remote-monitoring-architectural-choices"></a>Fjärråtkomst övervakning arkitektoniska val

Azure IoT Remote Monitoring solution accelerator är en öppen källkod, MIT-licens, lösningsaccelerator. För att du snabbt utvecklingsprocessen IoT, den visar vanliga IoT-scenarier som:

- Enhetsanslutning
- Enhetshantering
- Strömbearbetning

Lösningen för fjärrövervakning följer den rekommenderade [Azure IoT-Referensarkitektur](https://aka.ms/iotrefarchitecture).

Den här artikeln beskriver viktiga fram arkitekturrelaterad eller teknisk valen som gjorts i varje delsystem fjärrövervakning. De tekniska alternativen Microsoft som gjorts i lösningen för fjärrövervakning är dock inte det enda sättet att implementera en IoT lösning för fjärrövervakning. Du bör se teknisk implementering som utgångspunkt för att skapa en lyckade tillämpning och du bör ändra den till:

- Anpassa färdigheter som är tillgängliga och i din organisation.
- Alla dina lodrät programbehov.

## <a name="architectural-choices"></a>Val av arkitektur

Arkitektur som Microsoft rekommenderar för en IoT-program är molnet native, mikrotjänster, och utan Server baserad. Du bör skapa olika delsystem av ett IoT-program som diskreta tjänster som du kan distribuera och skala oberoende av varandra. Dessa attribut aktivera större skala, mer flexibilitet vid uppdatering av enskilda undersystem och ger flexibilitet att välja en lämplig teknik för varje undersystem.

Du kan implementera mikrotjänster som använder mer än en teknik. Exempelvis kan du välja något av följande alternativ för att implementera en mikrotjänst:

- Använd en teknik för behållare, till exempel Docker med serverlös teknik som Azure Functions.
- Vara värd för dina mikrotjänster på PaaS-tjänster, till exempel Azure App Services.

## <a name="technology-choices"></a>Teknikval

Det här avsnittet beskriver de tekniska alternativen som görs i lösningen för fjärrövervakning för varje kärna delsystem.

![Core-Diagram](./media/iot-accelerators-remote-monitoring-architectural-choices/subsystem.png)

### <a name="cloud-gateway"></a>Molngatewayen

Azure IoT Hub används som molgatewayen fjärrövervakning lösning. [IoT Hub](https://azure.microsoft.com/services/iot-hub/) erbjuder säker, dubbelriktad kommunikation med enheter.

För IoT-enhetsanslutning, kan du använda:

- Den [IoT Hub device SDK: er](../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks) att implementera ett internt klientprogram för din enhet. SDK: erna erbjuder omslutningar kring IoT Hub REST API och hanterar scenarier, t.ex nya försök.
- Integrering med Azure IoT Edge för att distribuera och hantera anpassade moduler som körs i behållare på dina enheter.
- Integrering med automatisk enhetshantering i IoT Hub för att hantera anslutna enheter i grupp.

### <a name="stream-processing"></a>Strömbearbetning

För bearbetning av dataströmmen använder av lösningen för fjärrövervakning Azure Stream Analytics för Regelbearbetning av komplexa. Om du vill använda enklare regler finns en anpassad mikrotjänst med stöd för enkel Regelbearbetning, även om den här konfigurationen är inte en del av out-of the box-distributionen. Referensarkitekturen rekommenderar Azure Functions för bearbetning med enkel regel och Azure Stream Analytics för bearbetning av komplexa regeln.

### <a name="storage"></a>Storage

För lagring använder lösningsacceleratorn för fjärrövervakning både Azure Time Series Insights och Azure Cosmos DB. Azure Time Series Insights lagrar meddelanden som skickas via IoT Hub från dina anslutna enheter. Solution accelerator använder Azure Cosmos DB för alla andra lagringsenheter, till exempel kall lagring, definitioner av regler, larm och konfigurationsinställningar.

Azure Cosmos DB är den rekommendera Allmänt varma storage-lösningen för IoT-program. Lösningar som Azure Time Series Insights och Azure Data Lake är lämpliga för många användningsfall. Med Azure Time Series Insights kan du få djupare insikter om dina time series-sensordata genom att identifiera trender och avvikelser. Den här funktionen kan du utföra rotorsaksanalyser och undvika kostsamma avbrott.

> [!NOTE]
> Time Series Insights är inte tillgänglig för tillfället i Azure i Kina-molnet. Nya fjärrövervakning solution accelerator distributioner i Azure i Kina-molnet använder Cosmos DB för lagring av alla.

### <a name="business-integration"></a>Business-integrering

Business-integrering i lösningen för fjärrövervakning är begränsad till generering av larm som är placerade i varma lagring. Anslut lösningen med Azure Logic Apps och implementerar djupare integrering företagsscenarier.

### <a name="user-interface"></a>Användargränssnitt

Webb Användargränssnittet skapas med JavaScript reagera. React erbjuder ett vanligt bransch web UI-ramverk och liknar andra populära ramverk som Angular.

### <a name="runtime-and-orchestration"></a>Runtime och dirigering

Lösningen för fjärrövervakning använder Docker-behållare för att köra undersystem med Kubernetes som orchestrator för horisontell skalning. Den här arkitekturen möjliggör enskilda skala definitioner för varje undersystem. Dock medför den här arkitekturen DevOps kostnader för att hålla virtuella datorer och behållare uppdaterade och säkra.

Alternativen på Docker är värd för mikrotjänster i PaaS-tjänster, till exempel Azure App Service. Alternativ till Kubernetes är initierare som Service Fabric, DC/OS eller Swarm.

## <a name="next-steps"></a>Nästa steg

* Distribuera lösningen för fjärrövervakning [här](https://www.azureiotsolutions.com/).
* Utforska GitHub-koden i [C#](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) och [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java/).  
* Läs mer om IoT-Referensarkitektur [här](https://aka.ms/iotrefarchitecture).
