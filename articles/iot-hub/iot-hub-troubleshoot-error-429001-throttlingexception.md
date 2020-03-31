---
title: Felsökning av Azure IoT Hub-fel 429001 Begränsningsexception
description: Förstå hur du åtgärdar fel 429001 ThrottlingException
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 3095e398d7e5cfe59085144d5bb4e8dc33618064
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960703"
---
# <a name="429001-throttlingexception"></a>429001 ThrottlingException

I den här artikeln beskrivs orsakerna och lösningarna för **429001 ThrottlingException-fel.**

## <a name="symptoms"></a>Symtom

Dina begäranden till IoT Hub misslyckas med felet **429001 ThrottlingException**.

## <a name="cause"></a>Orsak

IoT Hub [begränsningsgränser](./iot-hub-devguide-quotas-throttling.md) har överskridits för den begärda åtgärden.

## <a name="solution"></a>Lösning

Kontrollera om du når begränsningsgränsen genom att jämföra *måttet Telemetrimeddelande skicka försök* mot de gränser som anges ovan. Du kan också kontrollera måttet *Antal begränsningsfel.* Mer information om dessa och andra mått som är tillgängliga för IoT Hub finns i [IoT Hub-mått och hur du använder dem](./iot-hub-metrics.md#iot-hub-metrics-and-how-to-use-them).

IoT Hub returnerar 429 BegränsningException först efter att gränsen har brutits under en för lång period. Detta görs så att dina meddelanden inte tas bort om din IoT-hubb får burst-trafik. Under tiden bearbetar IoT Hub meddelandena med begränsad hastighet, vilket kan gå långsamt om det finns för mycket kvarvarande trafik att hantera. Läs mer i [Trafikkvotering](./iot-hub-devguide-quotas-throttling.md#traffic-shaping).

## <a name="next-steps"></a>Nästa steg

Överväg [att skala upp din IoT Hub](./iot-hub-scaling.md) om du kör in i kvot- eller begränsningsgränser.