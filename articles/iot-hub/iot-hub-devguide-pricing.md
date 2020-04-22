---
title: Förstå Azure IoT Hub-priser | Microsoft-dokument
description: Utvecklarguide – information om hur mätning och prissättning fungerar med IoT Hub inklusive arbetade exempel.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/11/2019
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 4c7382f84522333b6aae0d79941aae8f2147a12f
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81729148"
---
# <a name="azure-iot-hub-pricing-information"></a>Prisinformation för Azure IoT Hub

[Azure IoT Hub prissättning](https://azure.microsoft.com/pricing/details/iot-hub) ger allmän information om olika SKU:er och priser för IoT Hub. Den här artikeln innehåller ytterligare information om hur de olika IoT Hub-funktionerna mäts som meddelanden av IoT Hub.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="charges-per-operation"></a>Avgifter per operation

| Åtgärd | Faktureringsinformation | 
| --------- | ------------------- |
| Identitetsregisteråtgärder <br/> (skapa, hämta, lista, uppdatera, ta bort) | Inte laddad. |
| Meddelanden från enheten till molnet | Skickade meddelanden debiteras i 4 KB-segment på ingress till IoT Hub. Ett meddelande på 6 kB debiteras till exempel två meddelanden. |
| Meddelanden från molnet till enheten | Skickade meddelanden debiteras i 4 KB-segment, till exempel debiteras ett 6 KB-meddelande 2 meddelanden. |
| Filuppladdningar | Filöverföring till Azure Storage mäts inte av IoT Hub. Initierings- och slutförandemeddelanden för filöverföring debiteras som meddelanden som mäts i steg om 4 KB. Överföring av en 10 MB-fil debiteras till exempel som två meddelanden utöver kostnaden för Azure Storage. |
| Direkta metoder | Lyckade metodbegäranden debiteras i 4 KB-segment och svar debiteras i 4 KB-segment som ytterligare meddelanden. Begäranden om frånkopplade enheter debiteras som meddelanden i 4 KB-segment. Till exempel debiteras en metod med en 4-KB-brödtext som resulterar i ett svar utan brödtext från enheten som två meddelanden. En metod med en 6 KB-brödtext som resulterar i ett 1 KB-svar från enheten debiteras som två meddelanden för begäran plus ett annat meddelande för svaret. |
| Enhets- och modultvillingläsningar | Twin läser från enheten eller modulen och från lösningens serverdel debiteras som meddelanden i 512-byte bitar. Om du till exempel läser en 6-KB-tvilling debiteras du som 12 meddelanden. |
| Dubbeluppdateringar för enheter och modul (taggar och egenskaper) | Dubbla uppdateringar från enheten eller modulen och från lösningens serverdel debiteras som meddelanden i 512 byte-segment. Om du till exempel läser en 6-KB-tvilling debiteras du som 12 meddelanden. |
| Dubbelfrågor för enhet och modul | Frågor debiteras som meddelanden beroende på resultatstorleken i 512 byte-segment. |
| Jobbåtgärder <br/> (skapa, uppdatera, visa, ta bort) | Inte laddad. |
| Jobb per enhet | Jobbåtgärder (till exempel dubbla uppdateringar och metoder) debiteras som vanligt. Ett jobb som resulterar i 1 000 metodanrop med 1 KB-begäranden och svar på tomma brödtext debiteras till exempel 1 000 meddelanden. |
| Keep-alive meddelanden | När du använder AMQP- eller MQTT-protokoll debiteras inte meddelanden som utbyts för att upprätta anslutningen och meddelanden som utbyts i förhandlingen. |

> [!NOTE]
> Alla storlekar beräknas med tanke på nyttolaststorleken i byte (protokollinramning ignoreras). För meddelanden, som har egenskaper och brödtext, beräknas storleken på ett protokollaberoende sätt. Mer information finns i [IoT Hub meddelandeformat](iot-hub-devguide-messages-construct.md).

## <a name="example-1"></a>Exempel #1

En enhet skickar ett 1 KB-meddelande från enhet till moln per minut till IoT Hub, som sedan läs av Azure Stream Analytics. Lösningens serverdel anropar en metod (med en nyttolast på 512 byte) på enheten var tionde minut för att utlösa en viss åtgärd. Enheten svarar på metoden med ett resultat av 200 byte.

Enheten förbrukar:

* Ett meddelande * 60 minuter * 24 timmar = 1440 meddelanden per dag för meddelanden från enhet till moln.
* Två begäran plus svar * 6 gånger per timme * 24 timmar = 288 meddelanden för metoderna.

Den här beräkningen ger totalt 1728 meddelanden per dag.

## <a name="example-2"></a>Exempel #2

En enhet skickar ett 100 KB-meddelande från enhet till moln varje timme. Den uppdaterar också sin enhet twin med 1-KB nyttolaster var fjärde timme. Lösningens serverdel, en gång per dag, läser 14 KB-enhetens tvilling och uppdaterar den med 512 byte nyttolaster för att ändra konfigurationer.

Enheten förbrukar:

* 25 (100 KB / 4 KB) meddelanden * 24 timmar för meddelanden från enhet till moln.
* Två meddelanden (1 KB / 0,5 KB) * sex gånger per dag för enhetstvillinguppdateringar.

Den här beräkningen ger totalt 612 meddelanden per dag.

Lösningen back end förbrukar 28 meddelanden (14 KB / 0,5 KB) för att läsa enheten twin, plus ett meddelande för att uppdatera den, för totalt 29 meddelanden.

Totalt förbrukar enheten och lösningens serverdel 641 meddelanden per dag.
