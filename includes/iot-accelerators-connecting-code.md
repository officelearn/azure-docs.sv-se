---
title: ta med fil
description: ta med fil
services: iot-suite
author: dominicbetts
ms.service: iot-suite
ms.topic: include
ms.date: 09/17/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: c79b6f854dc78670a7eb8a1275c3e2fc46fcdd99
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61450228"
---
### <a name="code-walkthrough"></a>Kodhandledning

Det här avsnittet beskrivs några av de viktigaste delarna av exempelkoden och hur de relaterar till lösningsacceleratorn för fjärrövervakning.

Följande kodfragment visar hur rapporterade egenskaper som beskriver funktionerna i enheten har definierats. De här egenskaperna är:

- Platsen för enheten för att aktivera lösningsaccelerator att lägga till enheten på kartan.
- Den aktuella versionen för inbyggd programvara.
- Lista över metoder som enheten har stöd för.
- Schemat för telemetrimeddelanden som skickas av enheten.



Exemplet innehåller en **serializeToJson** funktion som Serialiserar den här datastrukturen med Parson-biblioteket.

Exemplet innehåller flera återanropsfunktionerna som skriver ut information till konsolen när klienten interagerar med solution accelerator:

- **connection_status_callback**
- **send_confirm_callback**
- **reported_state_callback**
- **device_method_callback**

I följande fragment visas den **device_method_callback** funktion. Den här funktionen anger åtgärden som ska vidtas när ett metodanrop tas emot från solution accelerator. Funktionen tar emot en referens till den **kylaggregat** data struktur i den **userContextCallback** parametern. Värdet för **userContextCallback** anges när Återanropsfunktionen konfigureras i den **huvudsakliga** funktionen:



När solution accelerator anropar metoden för uppdatering av inbyggd programvara, exemplet deserializes JSON-nyttolasten och startar en bakgrundstråd för att slutföra uppdateringsprocessen. I följande fragment visas den **do_firmware_update** som körs på tråden:



Följande kodfragment visar hur klienten skickar en telemetrimeddelanden till solution accelerator. Meddelandeegenskaperna inkluderar meddelande schemat för att hjälpa lösningsaccelerator visa telemetri på instrumentpanelen:



Den **huvudsakliga** funktion i det här exemplet:

- Initierar och stänger av SDK-undersystemet.
- Initierar den **kylaggregat** datastruktur.
- Skickar rapporterade egenskaper till solution accelerator.
- Konfigurerar återanropsfunktion för enhet-metoden.
- Skickar simulerade telemetrivärden för solution accelerator.


