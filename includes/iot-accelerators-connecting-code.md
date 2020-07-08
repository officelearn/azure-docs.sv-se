---
title: inkludera fil
description: inkludera fil
services: iot-suite
author: dominicbetts
ms.service: iot-suite
ms.topic: include
ms.date: 09/17/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: c79b6f854dc78670a7eb8a1275c3e2fc46fcdd99
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "67187778"
---
### <a name="code-walkthrough"></a>Steg-för-steg-beskrivning av kod

I det här avsnittet beskrivs några av de viktigaste delarna i exempel koden och hur de relaterar till lösnings acceleratorn för fjärrövervakning.

Följande kodfragment visar hur de rapporterade egenskaperna som beskriver enhetens funktioner definieras. Bland dessa egenskaper finns:

- Platsen för enheten för att aktivera Solution Accelerator för att lägga till enheten på kartan.
- Aktuell version av inbyggd program vara.
- Listan med metoder som enheten stöder.
- Schemat för telemetri-meddelanden som skickas av enheten.

[!code-cpp[Define data structures for Chiller](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=datadefinition "Define data structures for Chiller")]

Exemplet innehåller en **serializeToJson** -funktion som serialiserar den här data strukturen med hjälp av Parson-biblioteket.

Exemplet innehåller flera återanrops funktioner som skriver ut information till-konsolen när klienten interagerar med Solution Accelerator:

- **connection_status_callback**
- **send_confirm_callback**
- **reported_state_callback**
- **device_method_callback**

Följande kodfragment visar **device_method_callback** -funktionen. Den här funktionen avgör vilken åtgärd som ska vidtas när ett metod anrop tas emot från Solution Accelerator. Funktionen tar emot en referens till **kyl** data strukturen i **userContextCallback** -parametern. Värdet för **userContextCallback** anges när motringningsfunktionen har kon figurer ATS i **huvud** funktionen:

[!code-cpp[Device method callback](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=devicemethodcallback "Device method callback")]

När Solution Accelerator anropar uppdaterings metoden för inbyggd program vara deserialiserar exemplet JSON-nyttolasten och startar en bakgrunds tråd för att slutföra uppdaterings processen. Följande fragment visar **do_firmware_update** som körs på tråden:

[!code-cpp[Firmware update thread](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=firmwareupdate "Firmware update thread")]

Följande kodfragment visar hur klienten skickar ett telemetri-meddelande till Solution Accelerator. Meddelande egenskaperna innehåller meddelande scheman för att hjälpa Solution Accelerator att Visa telemetri på instrument panelen:

[!code-cpp[Send telemetry](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=sendmessage "Send telemetry")]

**Huvud** funktionen i exemplet:

- Initierar och stänger av under systemet SDK.
- Initierar **kyl** data strukturen.
- Skickar de rapporterade egenskaperna till Solution Accelerator.
- Konfigurerar återanrops funktionen för enhets metoden.
- Skickar simulerade telemetri värden till Solution Accelerator.

[!code-cpp[Main](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=main "Main")]
