---
title: Arkitektur IoT Smart Lagerhantering | Microsoft-dokument
description: En arkitektur för IoT Smart Inventory Management-mall för IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 10/20/2019
ms.custom: mqtt
ms.openlocfilehash: 9e0b4a3c22c1e6008bdce7fecc1d122fe317dd58
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686211"
---
# <a name="architecture-of-iot-central-smart-inventory-management-application-template"></a>Arkitektur för IoT Central programmall för smart lagerhantering

Partners och kunder kan använda appmallen och följa vägledning för att utveckla lösningar för hantering **av smarta lager.**

> [!div class="mx-imgBorder"]
> ![smart lagerhantering](./media/concept-smart-inventory-mgmt-architecture/smart-inventory-management-architecture.png)

1. Uppsättning IoT-sensorer som skickar telemetridata till en gateway-enhet
2. Gateway-enheter som skickar telemetri och aggregerade insikter till IoT Central
3. Data dirigeras till önskad Azure-tjänst för manipulering
4. Azure-tjänster som ASA eller Azure Functions kan användas för att formatera om dataströmmar och skicka till önskade lagringskonton 
5. Bearbetade data lagras i frekvent lagring för åtgärder i nära realtid eller kalllagring för ytterligare insiktsförbättringar som baseras på ML- eller batchanalys. 
6. Logic Apps kan användas för att driva olika affärsarbetsflöden i slutanvändarens affärsprogram

## <a name="details"></a>Information
I följande avsnitt beskrivs varje del av den konceptuella arkitekturen Telemetriinmatning från RFID-taggar (Radio-frequency Identification), BLE-taggar (Bluetooth low energy)

## <a name="rfid-tags"></a>RFID-taggar
RFID-taggar överför data om ett objekt via radiovågor. RFID-taggar har vanligtvis inget batteri om inget annat anges. Taggar får energi från radiovågor som genereras av läsaren och överföra en signal tillbaka mot RFID-läsaren.

## <a name="ble-tags"></a>BLE-taggar
Energifyr sänder datapaket med jämna mellanrum. Beacon-data identifieras av BLE-läsare eller installerade tjänster på smartphones och sedan överföra det till molnet.

## <a name="rfid--ble-readers"></a>RFID & BLE läsare
RFID-läsare konverterar radiovågorna till en mer användbar form av data. Information som samlas in från taggarna lagras sedan i lokal kantserver eller skickas till molnet med JSON-RPC 2.0 över MQTT.
BLE-läsare som också kallas Access Points (AP) liknar RFID-läsare. Det används för att identifiera närliggande Bluetooth-signaler och vidarebefordra sitt meddelande till lokala Azure IoT Edge eller moln med JSON-RPC 2.0 över MQTT.
Många läsare kan läsa RFID och beacon signaler, och ger ytterligare sensor kapacitet i samband med temperatur, luftfuktighet, accelerometer och gyroskop.

## <a name="azure-iot-edge-gateway"></a>Azure IoT Edge-gateway
Azure IoT Edge-server är en plats där data kan förbehandlas lokalt innan den skickas vidare till molnet. Vi kan också distribuera molnarbetsbelastningar artificiell intelligens, Azure och tjänster från tredje part, affärslogik med hjälp av standardbehållare.

## <a name="device-management-with-iot-central"></a>Enhetshantering med IoT Central 
Azure IoT Central är en lösningsutvecklingsplattform som förenklar IoT-enhetsanslutning, konfiguration och hantering. Plattformen minskar avsevärt bördan och kostnaderna för IoT-enhetshantering, drift och relaterad utveckling. Kunder & partner kan skapa en end to end-lösningar för att uppnå en digital återkoppling i lagerhantering.

## <a name="business-insights--actions-using-data-egress"></a>Affärsinsikter & åtgärder med hjälp av datarånge 
IoT Central-plattformen ger omfattande utökningsmöjligheter via CDE (Continuous Data Export) och API:er. Affärsinsikter baserade på telemetridatabearbetning eller råtelemetri exporteras vanligtvis till ett önskat affärsprogram. Det kan uppnås med hjälp av webhook, servicebuss, händelsenav eller bloblagring för att skapa, träna och distribuera maskininlärningsmodeller & ytterligare berika insikter.

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du distribuerar [smart lagerhanteringsmall](./tutorial-iot-central-smart-inventory-management.md)
* Läs mer om [IoT Central-butiksmallar](./overview-iot-central-retail.md)
* Läs mer om IoT Central se [IoT Central översikt](../core/overview-iot-central.md)
