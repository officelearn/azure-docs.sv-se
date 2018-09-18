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
ms.openlocfilehash: d087a3d5746396d81ef4ea44d90e917f25ebf19d
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2018
ms.locfileid: "45739189"
---
### <a name="code-walkthrough"></a>Kodhandledning

Det här avsnittet beskrivs några av de viktigaste delarna av exempelkoden och hur de relaterar till lösningsacceleratorn för fjärrövervakning.

Följande kodfragment visar hur rapporterade egenskaper som beskriver funktionerna i enheten har definierats. De här egenskaperna är:

- Platsen för enheten för att aktivera lösningsaccelerator att lägga till enheten på kartan.
- Den aktuella versionen för inbyggd programvara.
- Lista över metoder som enheten har stöd för.
- Schemat för telemetrimeddelanden som skickas av enheten.

[!code-cpp[Define data structures for Chiller](~/iot-samples-c/samples/solutions/remote_monitoring/remote_monitoring.c?name=datadefinition "Define data structures for Chiller")]

Exemplet innehåller en **serializeToJson** funktion som Serialiserar den här datastrukturen med Parson-biblioteket.

Exemplet innehåller flera återanropsfunktionerna som skriver ut information till konsolen när klienten interagerar med solution accelerator:

- **connection_status_callback**
- **send_confirm_callback**
- **reported_state_callback**
- **device_method_callback**

I följande fragment visas den **device_method_callback** funktion. Den här funktionen anger åtgärden som ska vidtas när ett metodanrop tas emot från solution accelerator. Funktionen tar emot en referens till den **kylaggregat** data struktur i den **userContextCallback** parametern. Värdet för **userContextCallback** anges när Återanropsfunktionen konfigureras i den **huvudsakliga** funktionen:

[!code-cpp[Device method callback](~/iot-samples-c/samples/solutions/remote_monitoring/remote_monitoring.c?name=devicemethodcallback "Device method callback")]

När solution accelerator anropar metoden för uppdatering av inbyggd programvara, exemplet deserializes JSON-nyttolasten och startar en bakgrundstråd för att slutföra uppdateringsprocessen. I följande fragment visas den **do_firmware_update** som körs på tråden:

[!code-cpp[Firmware update thread](~/iot-samples-c/samples/solutions/remote_monitoring/remote_monitoring.c?name=firmwareupdate "Firmware update thread")]

Följande kodfragment visar hur klienten skickar en telemetrimeddelanden till solution accelerator. Meddelandeegenskaperna inkluderar meddelande schemat för att hjälpa lösningsaccelerator visa telemetri på instrumentpanelen:

[!code-cpp[Send telemetry](~/iot-samples-c/samples/solutions/remote_monitoring/remote_monitoring.c?name=sendmessage "Send telemetry")]

Den **huvudsakliga** funktion i det här exemplet:

- Initierar och stänger av SDK-undersystemet.
- Initierar den **kylaggregat** datastruktur.
- Skickar rapporterade egenskaper till solution accelerator.
- Konfigurerar återanropsfunktion för enhet-metoden.
- Skickar simulerade telemetrivärden för solution accelerator.

[!code-cpp[Main](~/iot-samples-c/samples/solutions/remote_monitoring/remote_monitoring.c?name=main "Main")]
