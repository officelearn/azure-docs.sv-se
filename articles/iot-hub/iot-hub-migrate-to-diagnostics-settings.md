---
title: Azure IoT Hub migrera till diagnostikinställningar | Microsoft-dokument
description: Så här uppdaterar du Azure IoT Hub för att använda Azure-diagnostikinställningar i stället för åtgärder som övervakar status för åtgärder på din IoT-hubb i realtid.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: kgremban
ms.openlocfilehash: ab07da38c01b052a4220274fb059683a22950a3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75750692"
---
# <a name="migrate-your-iot-hub-from-operations-monitoring-to-diagnostics-settings"></a>Migrera din IoT Hub från driftövervakning till diagnostikinställningar

Kunder som använder [verksamhetsövervakning](iot-hub-operations-monitoring.md) för att spåra status för åtgärder i IoT Hub kan migrera arbetsflödet till [Azure-diagnostikinställningar](../azure-monitor/platform/platform-logs-overview.md), en funktion i Azure Monitor. Diagnostikinställningar tillhandahåller diagnostikinformation på resursnivå för många Azure-tjänster.

**Funktionen för driftövervakning av IoT Hub är inaktuell**och har tagits bort från portalen. Den här artikeln innehåller steg för att flytta dina arbetsbelastningar från driftövervakning till diagnostikinställningar. Mer information om tidslinjen för utfasning finns i [Övervaka dina Azure IoT-lösningar med Azure Monitor och Azure Resource Health](https://azure.microsoft.com/blog/monitor-your-azure-iot-solutions-with-azure-monitor-and-azure-resource-health/).

## <a name="update-iot-hub"></a>Uppdatera IoT-hubb

Om du vill uppdatera din IoT-hubb i Azure-portalen aktiverar du först diagnostikinställningarna och inaktiverar sedan övervakning av åtgärder.  

[!INCLUDE [iot-hub-diagnostics-settings](../../includes/iot-hub-diagnostics-settings.md)]

### <a name="turn-off-operations-monitoring"></a>Inaktivera övervakning av operationer

> [!NOTE]
> Från och med den 11 mars 2019 tas driftövervakningsfunktionen bort från IoT Hubs Azure-portalgränssnitt. Stegen nedan gäller inte längre. Om du vill migrera kontrollerar du att rätt kategorier är aktiverade i diagnostikinställningarna för Azure Monitor ovan.

När du har testat de nya diagnostikinställningarna i arbetsflödet kan du inaktivera funktionen för övervakning av åtgärder. 

1. Välj **Operationsövervakning**på IoT Hub-menyn .

2. Under varje övervakningskategori väljer du **Ingen**.

3. Spara ändringarna för övervakning av åtgärder.

## <a name="update-applications-that-use-operations-monitoring"></a>Uppdatera program som använder verksamhetsövervakning

Scheman för inställningar för driftövervakning och diagnostik varierar något. Det är viktigt att du uppdaterar de program som använder åtgärder övervakning idag för att mappa till det schema som används av diagnostikinställningar. 

Diagnostikinställningarna erbjuder också fem nya kategorier för spårning. När du har uppdaterat program för det befintliga schemat lägger du också till de nya kategorierna:

* Dubbla operationer mellan molnet och enheten
* Dubbla åtgärder mellan enheter och moln
* Dubbla frågor
* Jobbåtgärder
* Direkta metoder

För de specifika schemastrukturerna finns [i Förstå schemat för diagnostikinställningar](iot-hub-monitor-resource-health.md#understand-the-logs).

## <a name="monitoring-device-connect-and-disconnect-events-with-low-latency"></a>Övervaka enhetsanslutning och koppla från händelser med låg latens

Om du vill övervaka enhetsanslutning och koppla från händelser i produktionen rekommenderar vi att du prenumererar på den [ **frånkopplade** händelsen](iot-hub-event-grid.md#event-types) från enheten i Händelseutnätet för att få aviseringar och övervaka enhetens anslutningstillstånd. Använd den här [självstudien](iot-hub-how-to-order-connection-state-events.md) om du vill lära dig hur du integrerar händelser som är anslutna till enheten och frånkopplad enhet från IoT-hubben i IoT-lösningen.

## <a name="next-steps"></a>Nästa steg

[Övervaka hälsotillståndet för Azure IoT Hub och diagnostisera problem snabbt](iot-hub-monitor-resource-health.md)
