---
title: Azure IoT-hubb migrera till diagnostikinställningar | Microsoft Docs
description: Så här uppdaterar Azure IoT-hubb för att använda inställningar för Azure-diagnostik i stället för åtgärder som övervakning för att övervaka status för åtgärder för din IoT-hubb i realtid.
services: iot-hub
documentationcenter: ''
author: kgremban
manager: timlt
editor: ''
ms.assetid: ''
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2017
ms.author: kgremban
ms.openlocfilehash: a46f6798a71c93ed769ae68877e72801d45b74a4
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2018
---
# <a name="migrate-your-iot-hub-from-operations-monitoring-to-diagnostics-settings"></a>Migrera din IoT-hubb från operations övervakning i diagnostikinställningar

Kunder som använder [operations övervakning] [ lnk-opsmon] spåra statusen för åtgärder i IoT-hubb kan migrera som arbetsflödet ska [Azure diagnostikinställningar] [ lnk-diagnostics-settings], en funktion i Azure-Monitor. Diagnostikinställningar ange resursnivå diagnostisk information för många Azure-tjänster.

Åtgärder övervakningsfunktioner för IoT-hubben är föråldrad och kommer att tas bort i framtiden. Den här artikeln innehåller steg för att flytta arbetsbelastningar från operations övervakning i inställningarna för webbprogramdiagnostik. Läs mer om utfasning tidslinjen [övervaka dina Azure IoT-lösningar med Azure-Monitor och Azure Resource Health][lnk-blog-announcement].

## <a name="update-iot-hub"></a>Uppdatera IoT-hubb

Först aktiverar diagnostikinställningar för att uppdatera din IoT-hubb i Azure-portalen, och sedan inaktivera operations övervakning.  

[!INCLUDE [iot-hub-diagnostics-settings](../../includes/iot-hub-diagnostics-settings.md)]

### <a name="turn-off-operations-monitoring"></a>Inaktivera operations övervakning

När du har testat de nya diagnostikinställningarna för på arbetsflödet kan inaktivera du de åtgärder som funktionen som övervakar. 

1. Välj din IoT-hubb-menyn **Operations övervakning**.
1. Under varje övervakning, Välj **ingen**.
1. Spara de åtgärder som övervakar ändringar.

## <a name="update-applications-that-use-operations-monitoring"></a>Uppdatera program som använder operations övervakning

Scheman för åtgärder som övervakning och diagnostikinställningar variera något. Det är viktigt att du uppdaterar de program som använder operations övervakning dag för att mappas till schemat som används av inställningarna för webbprogramdiagnostik. 

Dessutom diagnostik inställningar erbjudanden spårning för fem nya kategorier. När du har uppdaterat program för det befintliga schemat lägger du till de nya kategorierna:

- Moln till enhet dubbla åtgärder
- Enhet till moln dubbla åtgärder
- Dubbla frågor
- Jobbåtgärder
- Direkt-metoder

Specifika schema-konstruktioner finns [förstå schemat för diagnostikinställningar][lnk-diagnostics-schema].

## <a name="next-steps"></a>Nästa steg

- [Övervaka hälsotillståndet för Azure IoT Hub och diagnostisera problem snabbt][lnk-monitor]

[lnk-opsmon]: iot-hub-operations-monitoring.md
[lnk-diagnostics-settings]: ../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md
[lnk-diagnostics-schema]: iot-hub-monitor-resource-health.md#understand-the-logs
[lnk-blog-announcement]: https://azure.microsoft.com/blog/monitor-your-azure-iot-accelerators-with-azure-monitor-and-azure-resource-health
[lnk-monitor]: iot-hub-monitor-resource-health.md
