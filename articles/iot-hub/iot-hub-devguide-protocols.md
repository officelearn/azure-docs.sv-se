---
title: Azure IoT-hubb kommunikationsprotokoll och portar | Microsoft Docs
description: Utvecklarhandbok - beskriver stöds kommunikationsprotokoll enhet till moln och moln till enhet informations-och de portnummer som måste öppnas.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: 0fe3dd719877dac23410ff1ca00d559636a5ed60
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34633010"
---
# <a name="reference---choose-a-communication-protocol"></a>Referera - Välj ett kommunikationsprotokoll

IoT-hubb kan enheter som ska användas i följande protokoll för enheten på klientsidan kommunikation:

* [MQTT][lnk-mqtt]
* MQTT över WebSockets
* [AMQP][lnk-amqp]
* AMQP över WebSockets
* HTTPS

Information om hur dessa protokoll stöder specifika IoT-hubb-funktioner finns i [enhet till moln kommunikation vägledning] [ lnk-d2c-guidance] och [moln till enhet kommunikation vägledning][lnk-c2d-guidance].

Följande tabell innehåller övergripande rekommendationer för ditt val av protokollet:

| Protokoll | När du ska välja det här protokollet |
| --- | --- |
| MQTT <br> MQTT över WebSocket |Använd på alla enheter som inte kräver för att ansluta flera enheter (var och en med sina egna autentiseringsuppgifter per enhet) via samma TLS-anslutning. |
| AMQP <br> AMQP över WebSocket |Använda på fältet och molnet gateways för att dra nytta av anslutningen multiplexering mellan enheter. |
| HTTPS |Används för enheter som inte stöder andra protokoll. |

Tänk på följande när du väljer att protokollet för enheten på klientsidan kommunikation:

* **Moln till enhet mönster**. HTTPS har inte ett effektivt sätt att implementera server push. Därför när du använder HTTPS avsöker enheter IoT-hubb för moln till enhet meddelanden. Den här metoden är ineffektiv för både enheten och IoT-hubb. Under den aktuella HTTPS riktlinjerna ska varje enhet avsöka för meddelanden var 25: e minut eller mer. MQTT och AMQP stöder server push när du tar emot meddelanden moln till enhet. De ger omedelbar push-meddelanden av meddelanden från IoT-hubb till enheten. Om leverans svarstiden är ett bekymmer, är MQTT eller AMQP de bästa protokoll som ska användas. För sällan anslutna enheter fungerar HTTPS samt.
* **Fältet gateways**. När du använder MQTT och HTTPS kan du inte kan ansluta flera enheter (var och en med sina egna autentiseringsuppgifter per enhet) med samma TLS-anslutning. För [fältet gateway-scenarier] [ lnk-azure-gateway-guidance] som kräver en TLS-anslutning mellan fältet gateway- och IoT-hubb för varje ansluten enhet, dessa protokoll är något sämre.
* **Lite resurs enheter**. MQTT och HTTPS-bibliotek har en mindre utrymme än AMQP-bibliotek. Om enheten har begränsat resurser (till exempel mindre än 1 MB RAM-minne), som sådana vara dessa protokoll den enda protokollimplementering.
* **Nätverk traversal**. AMQP standardprotokollet använder port 5671 och MQTT lyssnar på port 8883. Användning av dessa portar kan orsaka problem i nätverk som har stängts till icke-HTTPS-protokoll. Använd MQTT över WebSockets, AMQP över WebSockets eller HTTPS i det här scenariot.
* **Nyttolastens storlek**. MQTT och AMQP är binär protokoll, vilket resulterar i mer komprimerade nyttolaster än HTTPS.

> [!WARNING]
> När du använder HTTPS måste ska varje enhet avsökas moln till enhet meddelanden var 25: e minut eller flera. Dock under utveckling är det acceptabelt att avsöka oftare än var 25: e minut.

## <a name="port-numbers"></a>Portnummer

Enheter kan kommunicera med IoT-hubben i Azure med olika protokoll. Val av protokoll är normalt styrs av de särskilda kraven i lösningen. I följande tabell visas de utgående portar som måste vara öppna för en enhet för att kunna använda ett visst protokoll:

| Protokoll | Port |
| --- | --- |
| MQTT |8883 |
| MQTT över WebSockets |443 |
| AMQP |5671 |
| AMQP över WebSockets |443 |
| HTTPS |443 |

När du har skapat en IoT-hubb i en Azure-region, har IoT-hubben samma IP-adress för livslängden för den IoT-hubben. Men om Microsoft flyttar IoT-hubben till en annan skalningsenhet att underhålla tjänstkvalitet, tilldelas den en ny IP-adress.


## <a name="next-steps"></a>Nästa steg

Mer information om hur IoT-hubb implementerar protokollet MQTT finns [kommunicera med din IoT-hubb med protokollet MQTT][lnk-mqtt-support].

[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-mqtt-support]: iot-hub-mqtt-support.md
[lnk-amqp]: http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf
[lnk-mqtt]: http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/mqtt-v3.1.1.pdf
[lnk-azure-gateway-guidance]: iot-hub-devguide-endpoints.md#field-gateways
