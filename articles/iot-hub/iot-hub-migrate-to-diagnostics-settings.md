---
title: Azure IoT Hub migrera till diagnostikinställningar | Microsoft Docs
description: Så här uppdaterar Azure IoT Hub för att använda Azure diagnostics-inställningar i stället för åtgärdsövervakning om du vill övervaka statusen för åtgärder på IoT hub i realtid.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: kgremban
ms.openlocfilehash: d54b109cd5ef41665e595e7b878c077732536297
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/22/2019
ms.locfileid: "56674250"
---
# <a name="migrate-your-iot-hub-from-operations-monitoring-to-diagnostics-settings"></a>Migrera din IoT Hub från åtgärdsövervakning till diagnostikinställningar

Kunder som använder [åtgärdsövervakning](iot-hub-operations-monitoring.md) att spåra statusen för åtgärder i IoT Hub kan migrera som arbetsflödet ska [Azure diagnostikinställningar](../azure-monitor/platform/diagnostic-logs-overview.md), en funktion i Azure Monitor. Diagnostikinställningar ange resursnivå diagnostisk information för många Azure-tjänster.

Åtgärder för övervakningsfunktioner för IoT Hub är inaktuell och kommer att tas bort i framtiden. Den här artikeln innehåller steg för att flytta dina arbetsbelastningar från åtgärdsövervakning till diagnostikinställningar. Läs mer om utfasning tidslinjen [övervaka dina Azure-IoT-lösningar med Azure Monitor och Azure Resource Health](https://azure.microsoft.com/blog/monitor-your-azure-iot-solutions-with-azure-monitor-and-azure-resource-health/).

## <a name="update-iot-hub"></a>Uppdatera IoT-hubb

Aktivera diagnostikinställningar för att uppdatera din IoT-hubb i Azure-portalen, och sedan inaktivera åtgärdsövervakning.  

[!INCLUDE [iot-hub-diagnostics-settings](../../includes/iot-hub-diagnostics-settings.md)]

### <a name="turn-off-operations-monitoring"></a>Inaktivera åtgärdsövervakning

När du har testat de nya diagnostikinställningarna för i arbetsflödet kan stänga du av funktionen för åtgärdsövervakning. 

1. I din IoT Hub-menyn väljer **Åtgärdsövervakning**.

2. Under varje övervakning kategori väljer **ingen**.

3. Spara åtgärdsövervakning ändringar.

## <a name="update-applications-that-use-operations-monitoring"></a>Uppdatera program som använder åtgärdsövervakning

Scheman för åtgärdsövervakning och inställningarna för startdiagnostik kan skilja sig något. Det är viktigt att du uppdaterar de program som använder åtgärdsövervakning idag ska mappas till ett schema som används av diagnostikinställningar. 

Dessutom erbjuder diagnostikinställningar fem nya kategorier för spårning. När du har uppdaterat program för det befintliga schemat, lägger du till de nya kategorierna:

* Moln till enhet twin åtgärder
* Enhet-till-moln-twin-åtgärder
* Twin frågor
* Jobbåtgärder
* Direkta metoder

Specifika schema-konstruktioner Se [förstå schemat för diagnostikinställningar](iot-hub-monitor-resource-health.md#understand-the-logs).

## <a name="monitoring-device-connect-and-disconnect-events-with-low-latency"></a>Övervakning av enheten ansluta och koppla från händelser med kort svarstid

Övervaka enheter ansluta och koppla från händelser, rekommenderar vi prenumerera på den [ **enheten är frånkopplad** händelse](iot-hub-event-grid.md#event-types) på Event Grid för att få aviseringar och övervaka enhetens anslutning tillstånd. Använd det här [självstudien](iot-hub-how-to-order-connection-state-events.md) att lära dig hur du integrerar enheten ansluten och enheten frånkopplad händelser från IoT Hub i din IoT-lösning.

## <a name="next-steps"></a>Nästa steg

[Övervaka hälsotillståndet för Azure IoT Hub och diagnostisera problem snabbt](iot-hub-monitor-resource-health.md)
