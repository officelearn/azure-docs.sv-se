---
title: Arkitektur alternativ för fjärran sluten övervakning – Azure | Microsoft Docs
description: I den här artikeln beskrivs arkitektur och tekniska val som görs i fjärrövervakning
author: timlaverty
manager: camerons
ms.author: timlav
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/20/2018
ms.topic: conceptual
ms.openlocfilehash: 27a65d8a499f6eba130dc9537de3cb2b3dc3abe8
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96446772"
---
# <a name="remote-monitoring-architectural-choices"></a>Arkitekturval för fjärrövervakning

Azure IoT Remote Monitoring Solution Accelerator är en lösning med öppen källkod och MIT-lösning. För att hjälpa dig att påskynda din IoT-utvecklings process visar den vanliga IoT-scenarier som:

- Enhetsanslutning
- Enhetshantering
- Strömbearbetning

Lösningen för fjärrövervakning följer den rekommenderade [Azure IoT-referens arkitekturen](/azure/architecture/reference-architectures/iot).

Den här artikeln beskriver nyckel arkitektur och tekniska val som görs i varje under system för fjärrövervakning. De tekniska alternativen som Microsoft har gjort i fjärr styrnings lösningen är dock inte det enda sättet att implementera en IoT-lösning för fjärrövervakning. Du bör beakta den tekniska implementeringen som en bas linje för att skapa ett lyckat program och du bör ändra den till:

- Anpassa din organisations tillgängliga kunskaper och erfarenhet.
- Möta dina vertikala program behov.

## <a name="architectural-choices"></a>Val av arkitektur

Arkitekturen som Microsoft rekommenderar för ett IoT-program är inbyggd i molnet, mikrotjänster och Server lös. Du bör bygga de olika del systemen i ett IoT-program som diskreta tjänster som du kan distribuera och skala oberoende av varandra. Dessa attribut ger större skalbarhet, mer flexibilitet vid uppdatering av enskilda del system och ger flexibiliteten att välja en lämplig teknik för varje under system.

Du kan implementera mikrotjänster med mer än en teknik. Du kan till exempel välja något av följande alternativ för att implementera en mikrotjänst:

- Använd en behållar teknik som Docker med Server lös teknik, till exempel Azure Functions.
- Vara värd för mikrotjänster i PaaS-tjänster som Azure App Services.

## <a name="technology-choices"></a>Teknikval

I det här avsnittet beskrivs de teknik alternativ som har gjorts i lösningen för fjärrövervakning för var och en av kärn under systemen.

![Kärn diagram](./media/iot-accelerators-remote-monitoring-architectural-choices/subsystem.png)

### <a name="cloud-gateway"></a>Cloud Gateway

Azure IoT Hub används som moln-Gateway för fjärr styrnings lösningen. [IoT Hub](https://azure.microsoft.com/services/iot-hub/) erbjuder säker dubbelriktad kommunikation med enheter.

För IoT-enhets anslutning kan du använda:

- [IoT Hub enhets-SDK](../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks) : er för att implementera ett internt klient program för enheten. SDK: erna erbjuder omslutningar kring IoT Hub REST API och hanterar scenarier som försök.
- Integrationen med Azure IoT Edge för att distribuera och hantera anpassade moduler som körs i behållare på dina enheter.
- Integrationen med automatisk enhets hantering i IoT Hub för att hantera anslutna enheter i grupp.

### <a name="stream-processing"></a>Strömbearbetning

För strömnings bearbetning använder lösningen för fjärrövervakning Azure Stream Analytics för komplex regel bearbetning. Om du vill använda enklare regler finns det en anpassad mikrotjänst med stöd för enkel regel bearbetning, även om den här inställningen inte är en del av den färdiga distributionen. Referens arkitekturen rekommenderar Azure Functions för enkel regel bearbetning och Azure Stream Analytics för komplex regel bearbetning.

### <a name="storage"></a>Storage

För lagring används både Azure Time Series Insights och Azure Cosmos DB för lösningen för fjärrövervakning. Azure Time Series Insights lagrar de meddelanden som kommer genom IoT Hub från dina anslutna enheter. Solution Accelerator använder Azure Cosmos DB för all annan lagring, till exempel kall lagring, regler definitioner, aviseringar och konfigurations inställningar.

Azure Cosmos DB är den rekommenderade lösningen för varm lagring för IoT-program som är allmänt utformad. Lösningar som Azure Time Series Insights och Azure Data Lake är dock lämpliga för många användnings fall. Med Azure Time Series Insights kan du få djupare insikter om dina data i Time Series-sensorn genom upptäcka trender och avvikelser. Med den här funktionen kan du utföra rotor Saks analyser och undvika kostsamma drift stopp.

> [!NOTE]
> Time Series Insights är för närvarande inte tillgängligt i Azure Kina-molnet. Nya distributioner av Solution Accelerator för fjärrövervakning i molnet i Azure Kina använder Cosmos DB för all lagring.

### <a name="business-integration"></a>Företags integrering

Företags integrering i den fjärranslutna övervaknings lösningen är begränsad till genereringen av aviseringar som placeras i varmt lagrings utrymme. Anslut lösningen med Azure Logic Apps för att implementera djupare affärs integrerings scenarier.

### <a name="user-interface"></a>Användargränssnitt

Webb gränssnittet skapas med JavaScript-reagera. Reakta erbjuder ett vanligt ramverk för bransch webb gränssnitt och liknar andra populära ramverk, till exempel vinkel.

### <a name="runtime-and-orchestration"></a>Körning och dirigering

Lösningen för fjärrövervakning använder Docker-behållare för att köra under system med Kubernetes som Orchestrator för horisontell skalning. Den här arkitekturen möjliggör enskilda skalnings definitioner för varje under system. Den här arkitekturen medför dock DevOps kostnader för att hålla de virtuella datorerna och behållarna uppdaterade och säkra.

Alternativ till Docker är värd för mikrotjänster i PaaS-tjänster som Azure App Service. Alternativ till Kubernetes innehåller Dirigerare som Service Fabric, DC/OS eller Swarm.

## <a name="next-steps"></a>Nästa steg

* Distribuera din lösning för fjärr övervakning [här](https://www.azureiotsolutions.com/).
* Utforska GitHub-kod i [C#](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) och [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java/).  
* Lär dig mer om IoT Reference Architecture [här](/azure/architecture/reference-architectures/iot).