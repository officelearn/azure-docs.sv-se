---
title: Förstå prissättningen för Azure IoT Hub | Microsoft Docs
description: Utvecklarguide – information om hur Avläsning av programvara och priser fungerar med IoT-hubben som fungerade exempel.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: ac25fa1bcca9a49054f37d8799511fbc7d95645b
ms.sourcegitcommit: 5843352f71f756458ba84c31f4b66b6a082e53df
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/01/2018
ms.locfileid: "47584106"
---
# <a name="azure-iot-hub-pricing-information"></a>Azure IoT Hub information om priser

[Azure IoT Hub priser](https://azure.microsoft.com/pricing/details/iot-hub) innehåller allmän information om olika SKU: er och priser för IoT Hub. Den här artikeln innehåller ytterligare information om hur de olika funktionerna för IoT Hub mäts som meddelanden som IoT Hub.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="charges-per-operation"></a>Avgifter per åtgärd

| Åtgärd | Faktureringsinformation | 
| --------- | ------------------- |
| Identitetsregisteråtgärder <br/> (skapa, hämta, lista, uppdatera och ta bort) | Inte debiteras. |
| Meddelanden från enheten till molnet | Skickade meddelanden debiteras i segment om 4 KB på inkommande till IoT Hub. Ett meddelande på 6 KB debiteras till exempel 2 meddelanden. |
| Meddelanden från moln till enhet | Skickade meddelanden debiteras i segment om 4 KB, till exempel ett meddelande på 6 KB debiteras 2 meddelanden. |
| Filöverföringar | Filöverföring till Azure Storage mäts inte av IoT Hub. File transfer initiation och slutförande meddelanden debiteras som postmeddelandet förbrukade i steg om 4 KB. Till exempel debiteras överföra en fil på 10 MB två meddelanden förutom kostnaden för Azure Storage. |
| Direkta metoder | Lyckad metodbegäranden debiteras i segment om 4 KB, debiteras svar med icke-tomma organ i segment om 4 KB som ytterligare meddelanden. Begäranden till frånkopplade enheter debiteras som meddelanden i segment om 4 KB. En metod med en 6-KB text som resulterar i ett svar med ingen text från en enhet är till exempel debiteras som två meddelanden. En metod med en 6-KB text som resulterar i ett 1 KB-svar från enheten debiteras som två meddelanden för begäran plus ett annat meddelande för svaret. |
| Enheten och modulen twin läser | Twin läser från enheten eller modulen och från lösningens serverdel slut debiteras som meddelanden i 512 byte-segment. Till exempel debiteras läsning av en enhetstvilling på 6 KB som 12 meddelanden. |
| Enheten och modulen uppdateringar för enhetstvilling (taggar och egenskaper) | Uppdateringar för enhetstvilling från enheten eller modulen och lösningens backend-server debiteras som meddelanden i 512 byte-segment. Till exempel debiteras läsning av en enhetstvilling på 6 KB som 12 meddelanden. |
| Enheten och modulen twin frågor | Frågor debiteras som meddelanden beroende på resultatstorlek i 512 byte-segment. |
| Jobbåtgärder <br/> (skapa, uppdatera, visa, ta bort) | Inte debiteras. |
| Jobbåtgärder per enhet | Jobbåtgärder (t.ex uppdateringar för enhetstvilling och metoder) debiteras som vanligt. Ett jobb som lett till 1000 metodanrop med 1 KB begäranden och svar med tom brödtext debiteras till exempel 1000 meddelanden. |

> [!NOTE]
> Alla storlekar beräknas överväger Nyttolaststorlek i byte (protokollet synkroniseringstecken ignoreras). För meddelanden som har egenskaper och brödtext, beräknas storleken på ett protokoll-oberoende sätt. Mer information finns i [IoT Hub-meddelandeformat](iot-hub-devguide-messages-construct.md).

## <a name="example-1"></a>Exempel #1

En enhet skickar ett 1 KB enhet-till-moln-meddelande per minut till IoT Hub, som sedan läses av Azure Stream Analytics. Lösningens serverdel anropar en metod (med 512 byte-nyttolast) på enheten var tionde minut för att utlösa en viss åtgärd. Enheten svarar på metoden med ett resultat av 200 byte.

Enheten använder:

* Ett meddelande om att * 60 minuter * 24 timmar = 1440 meddelanden per dag för enhet-till-moln-meddelanden.
* Två begär plus svar * 6 gånger per timme * 24 timmar = 288 meddelanden för metoder.

Den här beräkningen ger totalt 1728 meddelanden per dag.

## <a name="example-2"></a>Exempel #2

En enhet skickar ett meddelande om enheten till molnet att 100 KB varje timme. Uppdaterar den även dess enhetstvilling med 1 KB nyttolaster var fjärde timme. Lösningens serverdel sluta, en gång per dag, läser 14 KB enhetstvillingen och uppdaterar med 512 byte-nyttolaster ändra konfigurationer.

Enheten använder:

* 25 (100 KB/4 KB) meddelanden * 24 timmar för meddelanden från enheten till molnet.
* Två meddelanden (1 KB/0,5 KB) * sex gånger per dag för uppdateringar för enhetstvilling.

Den här beräkningen ger totalt 612 meddelanden per dag.

Lösningens serverdel förbrukar 28 meddelanden (14 KB/0,5 KB) för att läsa enhetstvillingen, plus ett meddelande om att uppdatera det, 29 meddelanden totalt.

Totalt, enheten och lösningens backend-server kan du använda 641 meddelanden per dag.