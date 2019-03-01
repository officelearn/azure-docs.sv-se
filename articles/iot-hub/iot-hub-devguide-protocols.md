---
title: Azure IoT Hub kommunikationsprotokoll och portar | Microsoft Docs
description: Utvecklarguide – beskriver stöds kommunikationsprotokoll enhet till moln och från moln till enhet informations-och de portnummer som måste vara öppna.
author: robinsh
manager: philmea
ms.author: robin.shahan
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.openlocfilehash: 40297feca46910a3d5106134f8c2f45270d3f6df
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2019
ms.locfileid: "57011236"
---
# <a name="reference---choose-a-communication-protocol"></a>Referera till – Välj ett kommunikationsprotokoll

IoT Hub kan använda följande protokoll för enhetssidan kommunikation-enheter:

* [MQTT](http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/mqtt-v3.1.1.pdf)
* MQTT över WebSockets
* [AMQP](http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf)
* AMQP över WebSockets
* HTTPS

Information om hur dessa protokoll stöder specifika IoT Hub-funktioner finns i [enhet till molnet kommunikation vägledning](iot-hub-devguide-d2c-guidance.md) och [moln till enhet kommunikation vägledning](iot-hub-devguide-c2d-guidance.md).

Följande tabell innehåller övergripande rekommendationer för ditt val av protokollet:

| Protokoll | När du ska välja det här protokollet |
| --- | --- |
| MQTT <br> MQTT via WebSocket |Använd på alla enheter som inte kräver för att ansluta flera enheter (var och en med sina egna autentiseringsuppgifter per enhet) via samma TLS-anslutning. |
| AMQP <br> AMQP via WebSocket |Använd på fältet och cloud-gatewayer för att dra nytta av anslutningen multiplexing mellan enheter. |
| HTTPS |Använd detta för enheter som inte stöder andra protokoll. |

Tänk på följande när du väljer ditt protokoll för enhetssidan kommunikation:

* **Mönster för moln till enhet**. HTTPS har inte ett effektivt sätt att implementera server-push. Det innebär när du använder HTTPS avsöka enheter IoT Hub för meddelanden från molnet till enheten. Den här metoden är ineffektiv för både enheten och IoT Hub. Under den aktuella HTTPS riktlinjerna ska varje enhet söka efter meddelanden varje 25 minuter eller mer. MQTT- och AMQP stöd för server-push när du tar emot meddelanden från molnet till enheten. De gör omedelbar push-meddelanden av meddelanden från IoT Hub till enheten. Om leverans svarstiden är något problem, är MQTT eller AMQP de bästa protokoll som ska användas. För data som sällan anslutna enheter fungerar HTTPS också.

* **Fältet gatewayer**. När du använder MQTT- och HTTPS kan du inte kan ansluta flera enheter (var och en med sina egna autentiseringsuppgifter per enhet) med hjälp av samma TLS-anslutning. För [fältet gatewayscenarier](iot-hub-devguide-endpoints.md#field-gateways) som kräver en TLS-anslutning mellan fält-gateway och IoT Hub för varje ansluten enhet, dessa protokoll är icke-optimal.

* **Låg resource enheter**. MQTT- och HTTPS-bibliotek har mindre avtryck än AMQP-bibliotek. Om enheten har begränsade resurser (till exempel mindre än 1 MB RAM-minne), vara dessa protokoll därför den enda protokollimplementering som är tillgängliga.

* **Network edge traversal**. Standard AMQP-protokollet använder port 5671 och MQTT lyssnar på port 8883. Användning av dessa portar kan orsaka problem i nätverk som är stängda för icke-HTTPS-protokoll. Använda MQTT via WebSockets, AMQP via WebSockets eller HTTPS i det här scenariot.

* **Nyttolast**. MQTT- och AMQP är binär protokoll, vilket resulterar i mer kompakt nyttolaster än HTTPS.

> [!WARNING]
> När du använder HTTPS måste ska varje enhet söka efter meddelanden från moln till enhet var 25 minuter eller mer. Under utveckling är det dock att avsöka oftare än var 25: e minut.

## <a name="port-numbers"></a>Portnummer

Enheter kan kommunicera med IoT Hub i Azure med olika protokoll. Normalt drivs valet av protokoll av de specifika kraven för lösningen. I följande tabell visas de utgående portar som måste vara öppna för en enhet för att kunna använda ett visst protokoll:

| Protokoll | Port |
| --- | --- |
| MQTT |8883 |
| MQTT över WebSockets |443 |
| AMQP |5671 |
| AMQP över WebSockets |443 |
| HTTPS |443 |

När du har skapat en IoT-hubb i en Azure-region, IoT-hubben från samma IP-adress för livslängden för den IoT-hubben. Men om Microsoft flyttar IoT-hubben till en annan skalningsenhet att upprätthålla tjänstkvalitet, tilldelas den en ny IP-adress.

## <a name="next-steps"></a>Nästa steg

Läs mer om hur IoT Hub implementerar MQTT-protokollet i [kommunicera med IoT-hubben med hjälp av MQTT-protokollet](iot-hub-mqtt-support.md).
