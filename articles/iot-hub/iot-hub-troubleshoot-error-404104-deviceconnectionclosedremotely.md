---
title: Felsökning av Azure IoT Hub-fel 404104 DeviceConnectionClosedRemotely
description: Förstå hur du åtgärdar fel 404104 DeviceConnectionClosedRemotely
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom: mqtt
ms.openlocfilehash: c8cb91aa0c7ce1610320d4107db282d3c34407ba
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758720"
---
# <a name="404104-deviceconnectionclosedremotely"></a>404104 DeviceConnectionClosedRemotely

I den här artikeln beskrivs orsaker och lösningar för **404104 DeviceConnectionClosedRemotely-fel.**

## <a name="symptoms"></a>Symtom

### <a name="symptom-1"></a>Symptom 1

Enheter kopplas från med jämna mellanrum (var 65:e minut till exempel) och du ser **404104 DeviceConnectionClosedRemotely** i IoT Hub-diagnostikloggar. Ibland ser du också **401003 IoTHubUnauthorized** och en lyckad enhetsanslutningshändelse mindre än en minut senare.

### <a name="symptom-2"></a>Symptom 2

Enheter kopplar från slumpmässigt och du ser **404104 DeviceConnectionClosedRemotely** i IoT Hub-diagnostikloggar.

### <a name="symptom-3"></a>Symptom 3

Många enheter kopplar från samtidigt, du ser ett dopp i [måttet för anslutna enheter](iot-hub-metrics.md)och det finns fler **404104 DeviceConnectionClosedRemotely** och [500xxx Interna fel](iot-hub-troubleshoot-error-500xxx-internal-errors.md) i diagnostikloggar än vanligt.

## <a name="causes"></a>Orsaker

### <a name="cause-1"></a>Orsak 1

[SAS-token som används för att ansluta till IoT Hub](iot-hub-devguide-security.md#security-tokens) har upphört att gälla, vilket gör att IoT Hub kopplar från enheten. Anslutningen återupprättas när token uppdateras av enheten. [SAS-token upphör](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-authentication)till exempel varje timme som standard för C SDK , vilket kan leda till regelbundna frånkopplingar.

Mer information finns i [401003 IoTHubUnauthorized orsak](iot-hub-troubleshoot-error-401003-iothubunauthorized.md#cause-1).

### <a name="cause-2"></a>Orsak 2

Några möjligheter är:

- Enheten förlorade underliggande nätverksanslutning längre än [MQTT keep-alive,](iot-hub-mqtt-support.md#default-keep-alive-timeout)vilket resulterar i en fjärr-inaktiv timeout. MQTT-inställningen för håll- och håll- kan vara olik per enhet.

- Enheten skickade en återställning på TCP/IP-nivå men skickade `MQTT DISCONNECT`inte en programnivå . I grund och botten stängde enheten plötsligt den underliggande socketanslutningen. Ibland orsakas det här problemet av fel i äldre versioner av Azure IoT SDK.

- Programmet för enhetssidan kraschade.

### <a name="cause-3"></a>Orsak 3

IoT Hub kan ha ett tillfälligt problem. Se [IoT Hub interna serverfel orsak](iot-hub-troubleshoot-error-500xxx-internal-errors.md#cause).

## <a name="solutions"></a>Lösningar

### <a name="solution-1"></a>Lösning 1

Se [401003 IoTHubUnaukukoriserad lösning 1](iot-hub-troubleshoot-error-401003-iothubunauthorized.md#solution-1)

### <a name="solution-2"></a>Lösning 2

- Kontrollera att enheten har god anslutning till IoT Hub genom att [testa anslutningen](tutorial-connectivity.md). Om nätverket är opålitligt eller intermittent rekommenderar vi inte att du ökar värdet för att hålla liv eftersom det kan resultera i identifiering (till exempel via Azure Monitor-aviseringar) som tar längre tid. 

- Använd de senaste versionerna av [IoT SDK:er](iot-hub-devguide-sdks.md).

### <a name="solution-3"></a>Lösning 3

Se [lösningar på interna serverfel i IoT Hub](iot-hub-troubleshoot-error-500xxx-internal-errors.md#solution).

## <a name="next-steps"></a>Nästa steg

Vi rekommenderar att du använder Azure IoT-enhet SDK:er för att hantera anslutningar på ett tillförlitligt sätt. Mer information finns i [Hantera anslutning och tillförlitliga meddelanden med hjälp av Azure IoT Hub-enhet SDK:er](iot-hub-reliability-features-in-sdks.md)