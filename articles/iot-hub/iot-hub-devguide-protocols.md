---
title: Azure IoT Hub kommunikationsprotokoll och portar | Microsoft-dokument
description: Utvecklarguide – beskriver de kommunikationsprotokoll som stöds för kommunikation mellan enheter och kommunikation mellan molnet och från molnet till enheten och portnumren som måste vara öppna.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 3e7f31371a0582a6f4941efbfa0087119278d2d1
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81729120"
---
# <a name="reference---choose-a-communication-protocol"></a>Referens - välj ett kommunikationsprotokoll

Med IoT Hub kan enheter använda följande protokoll för kommunikation på enhetssidan:

* [MQTT](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/mqtt-v3.1.1.pdf)
* MQTT över WebSockets
* [AMQP](https://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf)
* AMQP över WebSockets
* HTTPS

Information om hur dessa protokoll stöder specifika IoT [Hub-funktioner](iot-hub-devguide-d2c-guidance.md) finns i riktlinjer för kommunikation mellan enheter och [kommunikation från molnet till enhet](iot-hub-devguide-c2d-guidance.md).

Följande tabell innehåller rekommendationer på hög nivå för ditt val av protokoll:

| Protokoll | När du ska välja det här protokollet |
| --- | --- |
| MQTT <br> MQTT över WebSocket |Använd på alla enheter som inte kräver att ansluta flera enheter (var och en med sina egna autentiseringsuppgifter per enhet) via samma TLS-anslutning. |
| AMQP <br> AMQP över WebSocket |Använd på fält- och molngateways för att dra nytta av anslutningsmultspling på olika enheter. |
| HTTPS |Används för enheter som inte stöder andra protokoll. |

Tänk på följande när du väljer ditt protokoll för kommunikation på enhetssidan:

* **Moln-till-enhet mönster**. HTTPS har inget effektivt sätt att implementera servertryck. När du använder HTTPS avsöker enheter därför IoT Hub för meddelanden från molnet till enheten. Den här metoden är ineffektiv för både enheten och IoT Hub. Enligt gällande HTTPS-riktlinjer bör varje enhet söka efter meddelanden var 25:e minut eller mer. MQTT och AMQP stöder server push när du tar emot meddelanden från molnet till enheten. De möjliggör omedelbara pushes av meddelanden från IoT Hub till enheten. Om leveransfördröjning är ett problem är MQTT eller AMQP de bästa protokollen att använda. För sällan anslutna enheter fungerar HTTPS också.

* **Fältgateways**. MQTT och HTTPS stöder endast en enhetsidentitet (enhets-ID plus autentiseringsuppgifter) per TLS-anslutning. Därför stöds inte dessa protokoll för [fältgatewayscenarier](iot-hub-devguide-endpoints.md#field-gateways) som kräver multiplexeringsmeddelanden med flera enhetsidentiteter över en enda eller en pool med uppströmsanslutningar till IoT Hub. Sådana gateways kan använda ett protokoll som stöder flera enhetsidentiteter per anslutning, till exempel AMQP, för sin uppströmstrafik.

* **Enheter med låg resurs**. MQTT- och HTTPS-biblioteken har ett mindre fotavtryck än AMQP-biblioteken. Om enheten har begränsade resurser (till exempel mindre än 1 MB RAM) kan dessa protokoll vara den enda tillgängliga protokollimplementeringen.

* **Nätverk traversal**. Standard-AMQP-protokollet använder port 5671 och MQTT lyssnar på port 8883. USe av dessa portar kan orsaka problem i nätverk som är stängda för icke-HTTPS-protokoll. Använd MQTT över WebSockets, AMQP över WebSockets eller HTTPS i det här scenariot.

* **Nyttolaststorlek**. MQTT och AMQP är binära protokoll, vilket resulterar i mer kompakta nyttolaster än HTTPS.

> [!WARNING]
> När du använder HTTPS bör varje enhet söka efter meddelanden från molnet till enheten högst en gång var 25:e minut. Under utveckling kan varje enhet avsöka oftare, om så önskas.

## <a name="port-numbers"></a>Portnummer

Enheter kan kommunicera med IoT Hub i Azure med hjälp av olika protokoll. Vanligtvis drivs valet av protokoll av lösningens specifika krav. I följande tabell visas de utgående portar som måste vara öppna för att en enhet ska kunna använda ett visst protokoll:

| Protokoll | Port |
| --- | --- |
| MQTT |8883 |
| MQTT över WebSockets |443 |
| AMQP |5671 |
| AMQP över WebSockets |443 |
| HTTPS |443 |

När du har skapat en IoT-hubb i en Azure-region behåller IoT-hubben samma IP-adress under den livslängden för IoT-hubben. Men om Microsoft flyttar IoT-hubben till en annan skalningsenhet för att upprätthålla kvaliteten på tjänsten, tilldelas den en ny IP-adress.

## <a name="next-steps"></a>Nästa steg

Mer information om hur IoT Hub implementerar MQTT-protokollet finns i [Kommunicera med din IoT-hubb med MQTT-protokollet](iot-hub-mqtt-support.md).
