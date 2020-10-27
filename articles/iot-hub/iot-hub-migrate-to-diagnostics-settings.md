---
title: Azure IoT Hub migrera till diagnostikinställningar | Microsoft Docs
description: Så här uppdaterar du Azure-IoT Hub för att använda Azure Diagnostics-inställningar i stället för drift övervakning för att övervaka status för åtgärder i IoT Hub i real tid.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: kgremban
ms.openlocfilehash: 40c90142330b0530f1127beae1624ff27d7eb6ca
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92541493"
---
# <a name="migrate-your-iot-hub-from-operations-monitoring-to-diagnostics-settings"></a>Migrera IoT Hub från Operations Monitor till diagnostikinställningar

Kunder som använder [drift övervakning](iot-hub-operations-monitoring.md) för att spåra statusen för åtgärder i IoT Hub kan migrera det till [Azure Diagnostics-inställningar](../azure-monitor/platform/platform-logs-overview.md), en funktion i Azure Monitor. Diagnostikinställningar tillhandahåller diagnostisk information på resurs nivå för många Azure-tjänster.

**Funktionerna för drift övervakning av IoT Hub är inaktuella** och har tagits bort från portalen. Den här artikeln innehåller steg för steg hur du flyttar dina arbets belastningar från drift övervakning till diagnostikinställningar. Mer information om tids linjen för tids linjen finns i [övervaka dina Azure IoT-lösningar med Azure Monitor och Azure Resource Health](https://azure.microsoft.com/blog/monitor-your-azure-iot-solutions-with-azure-monitor-and-azure-resource-health/).

## <a name="update-iot-hub"></a>Uppdatera IoT Hub

Om du vill uppdatera din IoT Hub i Azure Portal aktiverar du först diagnostikinställningar och stänger sedan av drift övervakning.  

[!INCLUDE [iot-hub-diagnostics-settings](../../includes/iot-hub-diagnostics-settings.md)]

### <a name="turn-off-operations-monitoring"></a>Inaktivera drift övervakning

> [!NOTE]
> Från och med den 11 mars 2019 tas funktionen för Operations övervakning bort från IoT Hub Azure Portal-gränssnittet. Stegen nedan gäller inte längre. För att migrera, se till att rätt kategorier är aktiverade i Azure Monitor diagnostikinställningar ovan.

När du har testat de nya diagnostikinställningar i ditt arbets flöde kan du inaktivera funktionen för drifts övervakning. 

1. I IoT Hub-menyn väljer du **Åtgärds övervakning** .

2. Under varje övervaknings kategori väljer du **ingen** .

3. Spara ändringarna i Operations Monitoring.

## <a name="update-applications-that-use-operations-monitoring"></a>Uppdatera program som använder Operations Monitoring

Scheman för åtgärder som övervakar och diagnostikinställningar skiljer sig något. Det är viktigt att du uppdaterar de program som använder drift övervakning idag för att mappa till det schema som används av diagnostikinställningar. 

Dessutom erbjuder diagnostikinställningar fem nya kategorier för spårning. När du har uppdaterat program för det befintliga schemat lägger du även till de nya kategorierna:

* Dubbla åtgärder från moln till enhet
* Dubbla åtgärder från enhet till moln
* Dubbla frågor
* Jobbåtgärder
* Direkta metoder

Information om de olika schema strukturerna finns i [resurs loggar](monitor-iot-hub-reference.md#resource-logs).

## <a name="monitoring-device-connect-and-disconnect-events-with-low-latency"></a>Övervaka enheten Anslut och koppla från händelser med låg latens

För att övervaka enhets anslutning och från koppling av händelser i produktion, rekommenderar vi att du prenumererar på [händelsen **frånkopplad av enheten**](iot-hub-event-grid.md#event-types) på Event Grid för att få aviseringar och övervaka enhetens anslutnings tillstånd. I den här [självstudien](iot-hub-how-to-order-connection-state-events.md) får du lära dig hur du integrerar enhet anslutna och avkopplade händelser från IoT Hub i din IoT-lösning.

## <a name="next-steps"></a>Nästa steg

[Övervaka IoT Hub](monitor-iot-hub.md)
