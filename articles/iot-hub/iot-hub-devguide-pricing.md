---
title: "Förstå Azure IoT Hub-priser | Microsoft Docs"
description: "Utvecklarhandbok - information om hur fungerade avläsning och prissättning fungerar med IoT-hubben inklusive exempel."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 1ac90923-1edf-4134-bbd4-77fee9b68d24
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: dbceca3daed53da2a383fd8a6b7a0bd1f225cf77
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2018
---
# <a name="azure-iot-hub-pricing-information"></a>Information om priser för Azure IoT-hubb

[Azure IoT-hubb priser] [ lnk-pricing] ger allmän information om olika SKU: er och priser för IoT-hubb. Den här artikeln innehåller ytterligare information om hur olika funktioner i IoT-hubben är avgiftsbelagda som meddelanden i IoT Hub.

## <a name="charges-per-operation"></a>Avgifter per åtgärd

| Åtgärd | Faktureringsinformation | 
| --------- | ------------------- |
| Identitetsregisteråtgärder <br/> (skapa, hämta, visa, uppdatera, ta bort) | Inte debiteras. |
| Meddelanden från enheten till molnet | Skickade meddelanden debiteras i 4 KB-block på ingång i IoT-hubb. Till exempel debiteras ett meddelande på 6 KB 2 meddelanden. |
| Meddelanden moln till enhet | Skickade meddelanden debiteras i 4 KB-block, till exempel ett meddelande på 6 KB debiteras 2 meddelanden. |
| Filöverföringar | Filöverföring till Azure Storage mäts inte i IoT Hub. Filen överföring stöd för start och slutförande meddelanden debiteras som postmeddelandet förbrukade i steg 4 KB. Till exempel debiteras överföra en fil på 10 MB två meddelanden förutom kostnaden för Azure Storage. |
| Direkta metoder | Lyckad metodbegäranden debiteras i 4 KB-block, debiteras svar med icke-tom organ i 4 KB-block som ytterligare meddelanden. Begäranden till frånkopplade enheter debiteras som meddelanden i 4 KB-block. Till exempel debiteras en metod med 6 KB brödtext som resulterar i ett svar med ingen brödtext från enheten, som två meddelanden. En metod med 6 KB brödtext som resulterar i ett 1-KB-svar från enheten debiteras som två meddelanden för begäran plus ett annat meddelande för svaret. |
| Läsoperationer för enhetstvilling | Enheten dubbla läser från enheten och lösningen tillbaka slutet debiteras som meddelanden i 512 byte-segment. Till exempel debiteras läsa en 6 KB enheten dubbla som 12 meddelanden. |
| Dubbla uppdateringar (taggar och egenskaper) | Dubbla uppdateringar från enheten och lösningens serverdel debiteras som meddelanden i 512 byte-segment. Till exempel debiteras läsa en 6 KB enheten dubbla som 12 meddelanden. |
| Enheten dubbla frågor | Frågor debiteras som meddelanden beroende på storleken på resultatet i 512 byte-segment. |
| Jobbåtgärder <br/> (skapa, uppdatera, visa, ta bort) | Inte debiteras. |
| Åtgärder för jobb per enhet | Jobb-åtgärder (till exempel dubbla uppdateringar och metoder) debiteras som vanligt. Till exempel debiteras ett jobb som ledde till 1000 metodanrop med 1 KB och -svar för tom brödtext 1000 meddelanden. |

> [!NOTE]
> Alla storlekar beräknas överväger nyttolastens storlek i byte (protokollet synkroniseringstecken ignoreras). För meddelanden som har egenskaper och brödtext beräknas storleken på protocol-oberoende sätt. Mer information finns i [IoT-hubb messaging Utvecklingsguide][lnk-message-size].

## <a name="example-1"></a>Exempel #1

En enhet skickar ett 1 KB enhet till moln meddelande per minut till IoT-hubb som läses sedan av Azure Stream Analytics. Lösningens serverdel anropar en metod (med 512 byte-nyttolast) på enheten var tionde minut för att utlösa en specifik åtgärd. Enheten svarar metoden med ett resultat av 200 byte.

Enheten förbrukar:

* Ett meddelande * 60 minuter * 24 timmar = 1440 meddelanden per dag för meddelanden enhet till moln.
* Två begära plus svar * 6 gånger i timmen * 24 timmar = 288 meddelanden för metoder.

Den här beräkningen ger totalt 1728 meddelanden per dag.

## <a name="example-2"></a>Exempel #2

En enhet skickar en 100 KB enhet till moln meddelande varje timme. Uppdaterar den även dess enhet dubbla med 1 KB nyttolaster var fjärde timme. Lösningen tillbaka avslutas, en gång per dag, läser 14 KB enheten dubbla och uppdaterar med 512 byte-nyttolaster ändra konfigurationer.

Enheten förbrukar:

* 25 (100 KB/4 KB) meddelanden * 24 timmar för meddelanden från enhet till moln.
* Två meddelanden (1 KB/0,5 KB) * sex gånger per dag för dubbla uppdateringar.

Den här beräkningen ger totalt 612 meddelanden per dag.

Lösningens serverdel förbrukar 28 meddelanden (14 KB/0,5 KB) för att läsa enhet dubbla plus ett meddelande att uppdatera det, totalt 29 meddelanden.

Sammanlagt enheten och lösningens serverdel kan du använda 641 meddelanden per dag.


[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[lnk-message-size]: iot-hub-devguide-messages-construct.md
