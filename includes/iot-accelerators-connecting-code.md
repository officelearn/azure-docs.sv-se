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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67187778"
---
### <a name="code-walkthrough"></a>Steg-för-steg-beskrivning av kod

I det här avsnittet beskrivs några av de viktigaste delarna av exempelkoden och hur de relaterar till lösningsacceleratorn för fjärrövervakning.

Följande kodavsnitt visar hur de rapporterade egenskaperna som beskriver enhetens funktioner definieras. Dessa egenskaper inkluderar:

- Enhetens placering för att göra det möjligt för lösningsacceleratorn att lägga till enheten på kartan.
- Den aktuella firmware-versionen.
- Listan över metoder som enheten stöder.
- Schemat för telemetrimeddelanden som skickas av enheten.

[!code-cpp[Define data structures for Chiller](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=datadefinition "Define data structures for Chiller")]

Exemplet innehåller en **serializeToJson-funktion** som serialiserar den här datastrukturen med hjälp av Parson-biblioteket.

Exemplet innehåller flera motringningsfunktioner som skriver ut information till konsolen när klienten interagerar med lösningsacceleratorn:

- **connection_status_callback**
- **send_confirm_callback**
- **reported_state_callback**
- **device_method_callback**

Följande kodavsnitt visar **funktionen device_method_callback.** Den här funktionen bestämmer vilken åtgärd som ska vidtas när ett metodanrop tas emot från lösningsacceleratorn. Funktionen får en referens till **Chiller-datastrukturen** i parametern **userContextCallback.** Värdet **för userContextCallback** ställs in när motringningsfunktionen konfigureras i **huvudfunktionen:**

[!code-cpp[Device method callback](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=devicemethodcallback "Device method callback")]

När lösningsacceleratorn anropar uppdateringsmetoden för inbyggd programvara avsererialiserar exemplet JSON-nyttolasten och startar en bakgrundstråd för att slutföra uppdateringsprocessen. Följande kodavsnitt visar **do_firmware_update** som körs på tråden:

[!code-cpp[Firmware update thread](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=firmwareupdate "Firmware update thread")]

Följande kodavsnitt visar hur klienten skickar ett telemetrimeddelande till lösningsacceleratorn. Meddelandeegenskaperna inkluderar meddelandeschemat som hjälper lösningsacceleratorn att visa telemetrin på instrumentpanelen:

[!code-cpp[Send telemetry](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=sendmessage "Send telemetry")]

**Huvudfunktionen** i provet:

- Initierar och stänger av SDK-undersystemet.
- Initierar **kylmaskinsdatastrukturen.**
- Skickar de rapporterade egenskaperna till lösningsacceleratorn.
- Konfigurerar motringningsfunktionen för enhetsmetoden.
- Skickar simulerade telemetrivärden till lösningsacceleratorn.

[!code-cpp[Main](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=main "Main")]
