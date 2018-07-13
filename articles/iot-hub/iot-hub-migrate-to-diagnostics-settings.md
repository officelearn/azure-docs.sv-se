---
title: Azure IoT Hub migrera till diagnostikinställningar | Microsoft Docs
description: Så här uppdaterar Azure IoT Hub för att använda Azure diagnostics-inställningar i stället för åtgärdsövervakning om du vill övervaka statusen för åtgärder på IoT hub i realtid.
author: kgremban
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/10/2017
ms.author: kgremban
ms.openlocfilehash: 85bdb4b4802283c591e4d7a9e8b14ae74fa44e8d
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38666730"
---
# <a name="migrate-your-iot-hub-from-operations-monitoring-to-diagnostics-settings"></a>Migrera din IoT Hub från åtgärdsövervakning till diagnostikinställningar

Kunder som använder [åtgärdsövervakning] [ lnk-opsmon] att spåra statusen för åtgärder i IoT Hub kan migrera som arbetsflödet ska [Azure diagnostikinställningar] [ lnk-diagnostics-settings], en funktion i Azure Monitor. Diagnostikinställningar ange resursnivå diagnostisk information för många Azure-tjänster.

Åtgärder för övervakningsfunktioner för IoT Hub är inaktuell och kommer att tas bort i framtiden. Den här artikeln innehåller steg för att flytta dina arbetsbelastningar från åtgärdsövervakning till diagnostikinställningar. Läs mer om utfasning tidslinjen [övervaka dina Azure-IoT-lösningar med Azure Monitor och Azure Resource Health][lnk-blog-announcement].

## <a name="update-iot-hub"></a>Uppdatera IoT-hubb

Aktivera diagnostikinställningar för att uppdatera din IoT-hubb i Azure-portalen, och sedan inaktivera åtgärdsövervakning.  

[!INCLUDE [iot-hub-diagnostics-settings](../../includes/iot-hub-diagnostics-settings.md)]

### <a name="turn-off-operations-monitoring"></a>Inaktivera åtgärdsövervakning

När du har testat de nya diagnostikinställningarna för på ditt arbetsflöde kan stänga du av funktionen för åtgärdsövervakning. 

1. I din IoT Hub-menyn väljer **Åtgärdsövervakning**.
1. Under varje övervakning kategori väljer **ingen**.
1. Spara åtgärdsövervakning ändringar.

## <a name="update-applications-that-use-operations-monitoring"></a>Uppdatera program som använder åtgärdsövervakning

Scheman för åtgärdsövervakning och inställningarna för startdiagnostik kan skilja sig något. Det är viktigt att du uppdaterar de program som använder åtgärdsövervakning idag ska mappas till ett schema som används av diagnostikinställningar. 

Dessutom diagnostik inställningar erbjudanden spårning för fem nya kategorier. När du har uppdaterat program för det befintliga schemat, lägger du till de nya kategorierna:

- Moln till enhet twin åtgärder
- Enhet-till-moln-twin-åtgärder
- Twin frågor
- Jobbåtgärder
- Direkta metoder

Specifika schema-konstruktioner Se [förstå schemat för diagnostikinställningar][lnk-diagnostics-schema].

## <a name="next-steps"></a>Nästa steg

- [Övervaka hälsotillståndet för Azure IoT Hub och diagnostisera problem snabbt][lnk-monitor]

[lnk-opsmon]: iot-hub-operations-monitoring.md
[lnk-diagnostics-settings]: ../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md
[lnk-diagnostics-schema]: iot-hub-monitor-resource-health.md#understand-the-logs
[lnk-blog-announcement]: https://azure.microsoft.com/blog/monitor-your-azure-iot-solutions-with-azure-monitor-and-azure-resource-health/
[lnk-monitor]: iot-hub-monitor-resource-health.md
