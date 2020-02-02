---
title: Fel sökning av Azure IoT Hub-fel 429001 ThrottlingException
description: Förstå hur du åtgärdar fel 429001 ThrottlingException
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 3095e398d7e5cfe59085144d5bb4e8dc33618064
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2020
ms.locfileid: "76960703"
---
# <a name="429001-throttlingexception"></a>429001 ThrottlingException

I den här artikeln beskrivs orsaker och lösningar för **429001 ThrottlingException** -fel.

## <a name="symptoms"></a>Symtom

Dina begär anden till IoT Hub fungerar inte med fel **429001-ThrottlingException**.

## <a name="cause"></a>Orsak

IoT Hub [begränsnings gränser](./iot-hub-devguide-quotas-throttling.md) har överskridits för den begärda åtgärden.

## <a name="solution"></a>Lösning

Kontrol lera att du når begränsnings gränsen genom att jämföra dina *telemetri-meddelanden* med de gränser som anges ovan. Du kan också kontrol lera *antalet begränsnings fel* . Mer information om dessa och andra mått som är tillgängliga för IoT Hub finns [IoT Hub mått och hur du använder dem](./iot-hub-metrics.md#iot-hub-metrics-and-how-to-use-them).

IoT Hub returnerar 429 ThrottlingException endast efter att gränsen har överskridits för för lång tid. Detta görs så att dina meddelanden inte försvinner om din IoT Hub får burst-trafik. Under tiden bearbetar IoT Hub meddelandena med begränsad hastighet, vilket kan gå långsamt om det finns för mycket kvarvarande trafik att hantera. Läs mer i [Trafikkvotering](./iot-hub-devguide-quotas-throttling.md#traffic-shaping).

## <a name="next-steps"></a>Nästa steg

Överväg att [skala upp IoT Hub](./iot-hub-scaling.md) om du kör i kvot-eller begränsnings gränser.