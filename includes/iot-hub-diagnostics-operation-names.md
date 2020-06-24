---
title: inkludera fil
description: inkludera fil
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 04/28/2020
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 362c13771e7382ead1ba5aebd99a69fd86cd718c
ms.sourcegitcommit: 6571e34e609785e82751f0b34f6237686470c1f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/15/2020
ms.locfileid: "84793319"
---
<!-- operation names for the diag logs for IoT Hub -->

|Åtgärds namn|Nivå|Beskrivning|
|------------- |-----|-----------|
|UndefinedRouteEvaluation|Information|Det går inte att utvärdera meddelandet med ett ge villkor. Till exempel om en egenskap i Route-villkoret saknas i meddelandet. Läs mer om [frågesyntax för routning](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax).|
|RouteEvaluationError|Fel|Ett fel inträffade vid utvärdering av meddelandet på grund av ett problem med meddelande formatet. Det här felet loggas till exempel om innehålls kodningen inte har angetts eller innehålls typen inte är giltig i meddelandet. De måste anges i [System egenskaperna](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#system-properties).|
|DroppedMessage|Fel|Meddelandet ignorerades och dirigerades inte. Detta kan bero på att meddelandet inte matchade någon cirkulations fråga eller slut punkt var dött och att meddelandet inte kunde levereras efter flera återförsök. Vi rekommenderar att du får mer information om slut punkten genom att använda REST API [Hämta slut punkts hälsa](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth).|
|EndpointUnhealthy|Fel|Slut punkten har inte accepterat meddelanden från IoT Hub och IoT Hub försöker skicka meddelanden igen. Vi rekommenderar att du observerar det senaste kända felet via REST API [Hämta slut punkts hälsa](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth).|
|EndpointDead|Fel|Slut punkten har inte accepterat meddelanden från IoT Hub i mer än en timme. Vi rekommenderar att du observerar det senaste kända felet via REST API [Hämta slut punkts hälsa](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth).|
|EndpointHealthy|Information|Slut punkten är felfri och tar emot meddelanden från IoT Hub. Det här meddelandet loggas inte kontinuerligt, men loggas bara när slut punkten skadas igen. Det här meddelandet innebär att IoT Hub inte kunde skicka meddelanden till slut punkten, men slut punkten är nu felfri.|
|OrphanedMessage|Information|Meddelandet matchar inte någon väg.|
|InvalidMessage|Fel|Meddelandet är ogiltigt på grund av inkompatibilitet med slut punkten. Vi rekommenderar att du kontrollerar konfigurationen av slut punkten.|


Åtgärderna *UndefinedRouteEvaluation*, *RouteEvaluationError* och *OrphanedMessage* är begränsade och loggas inte mer än en gång i minuten per IoT Hub.