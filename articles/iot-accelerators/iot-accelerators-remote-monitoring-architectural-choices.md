---
title: Arkitekturalternativ för fjärrövervakningslösning – Azure | Microsoft-dokument
description: I den här artikeln beskrivs de arkitektoniska och tekniska val som görs i fjärrövervakning
author: timlaverty
manager: camerons
ms.author: timlav
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/20/2018
ms.topic: conceptual
ms.openlocfilehash: 1bd08596a30db7322a72b4269fddfe0b9df19119
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "61447186"
---
# <a name="remote-monitoring-architectural-choices"></a>Arkitekturval för fjärrövervakning

Azure IoT Remote Monitoring-lösningsacceleratorn är enaccelerator med öppen källkod, MIT-licensierad, lösningsaccelerator. För att hjälpa dig att påskynda din IoT-utvecklingsprocess visas vanliga IoT-scenarier som:

- Enhetsanslutning
- Enhetshantering
- Strömbearbetning

Lösningen för fjärrövervakning följer den rekommenderade [Azure IoT-referensarkitekturen](https://aka.ms/iotrefarchitecture).

I den här artikeln beskrivs de viktigaste arkitektoniska och tekniska valen som görs i vart och ett av delsystemen Fjärrövervakning. De tekniska val som Microsoft gjorde i lösningen för fjärrövervakning är dock inte det enda sättet att implementera en IoT-lösning för fjärrövervakning. Du bör betrakta den tekniska implementeringen som en baslinje för att skapa ett lyckat program och du bör ändra den till:

- Anpassa de tillgängliga färdigheterna och erfarenheterna i din organisation.
- Uppfyller dina vertikala applikationsbehov.

## <a name="architectural-choices"></a>Val av arkitektur

Den arkitektur som Microsoft rekommenderar för ett IoT-program är inbyggt i molnet, mikrotjänst och serverlös. Du bör skapa de olika delsystemen i ett IoT-program som diskreta tjänster som du kan distribuera och skala oberoende av sig. Dessa attribut möjliggör större skala, större flexibilitet vid uppdatering av enskilda delsystem och ger flexibiliteten att välja en lämplig teknik för varje delsystem.

Du kan implementera mikrotjänster med mer än en teknik. Du kan till exempel välja något av följande alternativ för att implementera en mikrotjänst:

- Använd en behållarteknik som Docker med serverlös teknik som Azure Functions.
- Vara värd för dina mikrotjänster i PaaS-tjänster som Azure App Services.

## <a name="technology-choices"></a>Teknikval

I det här avsnittet beskrivs de teknikval som görs i lösningen för fjärrövervakning för vart och ett av de grundläggande delsystemen.

![Kärndiagram](./media/iot-accelerators-remote-monitoring-architectural-choices/subsystem.png)

### <a name="cloud-gateway"></a>Moln gateway

Azure IoT Hub används som molngateway för fjärrövervakningslösning. [IoT Hub](https://azure.microsoft.com/services/iot-hub/) erbjuder säker dubbelriktad kommunikation med enheter.

För IoT-enhetsanslutning kan du använda:

- [IoT Hub-enheten SDK:er](../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks) för att implementera ett inbyggt klientprogram för din enhet. SDK:erna erbjuder omslag runt IoT Hub REST API och hanterar scenarier som återförsök.
- Integreringen med Azure IoT Edge för att distribuera och hantera anpassade moduler som körs i behållare på dina enheter.
- Integreringen med automatisk enhetshantering i IoT Hub för att hantera anslutna enheter i bulk.

### <a name="stream-processing"></a>Strömbearbetning

För dataflödesbearbetning använder fjärrövervakningslösningen Azure Stream Analytics för komplex regelbearbetning. Om du vill använda enklare regler finns det en anpassad mikrotjänst med stöd för enkel regelbearbetning, även om den här uppsättningen inte ingår i den färdiga distributionen. Referensarkitekturen rekommenderar Azure Functions för enkel regelbearbetning och Azure Stream Analytics för komplex regelbearbetning.

### <a name="storage"></a>Lagring

För lagring använder lösningsacceleratorn för fjärrövervakning både Azure Time Series Insights och Azure Cosmos DB. Azure Time Series Insights lagrar meddelanden som kommer via IoT Hub från dina anslutna enheter. Lösningsacceleratorn använder Azure Cosmos DB för all annan lagring, till exempel kyllagring, regeldefinitioner, aviseringar och konfigurationsinställningar.

Azure Cosmos DB är den rekommenderade allmänna lösningen för varm lagring för IoT-program. Lösningar som Azure Time Series Insights och Azure Data Lake är dock lämpliga för många användningsfall. Med Azure Time Series Insights kan du få djupare insikter i dina tidsseriesensordata genom att upptäcka trender och avvikelser. Med den här funktionen kan du utföra rotorsaksanalyser och undvika kostsamma driftstopp.

> [!NOTE]
> Time Series Insights är för närvarande inte tillgängligt i Azure China-molnet. Nya snabbacceleratorer för fjärrövervakning i Azure China-molnet använder Cosmos DB för all lagring.

### <a name="business-integration"></a>Företagsintegrering

Affärsintegrering i lösningen för fjärrövervakning är begränsad till generering av aviseringar som placeras i varm lagring. Anslut lösningen med Azure Logic Apps för att implementera djupare scenarier för affärsintegrering.

### <a name="user-interface"></a>Användargränssnitt

Webbgränssnittet är byggt med JavaScript React. React erbjuder en vanlig industri webbgränssnitt ram och liknar andra populära ramar såsom Kantiga.

### <a name="runtime-and-orchestration"></a>Runtime och orkestrering

Remote Monitoring-lösningen använder Docker-behållare för att köra undersystemen med Kubernetes som orchestrator för vågrät skala. Den här arkitekturen möjliggör enskilda skalningsdefinitioner för varje delsystem. Den här arkitekturen medför dock DevOps-kostnader för att hålla de virtuella datorerna och behållarna uppdaterade och säkra.

Alternativ till Docker inkluderar värd för mikrotjänster i PaaS-tjänster som Azure App Service. Alternativ till Kubernetes inkluderar orchestrators som Service Fabric, DC/OS eller Swarm.

## <a name="next-steps"></a>Nästa steg

* Distribuera din lösning för fjärrövervakning [här](https://www.azureiotsolutions.com/).
* Utforska GitHub-kod i [C#](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) och [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java/).  
* Läs mer om IoT-referensarkitekturen [här](https://aka.ms/iotrefarchitecture).
