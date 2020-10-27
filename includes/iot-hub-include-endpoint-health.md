---
title: ta med fil
description: ta med fil
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/28/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 36c05badb3b2292a29b8227c7f03b841474c97ad
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92548156"
---
Du kan använda REST API [Hämta slut punkts hälsa](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) för att få hälso status för slut punkterna. Vi rekommenderar att du använder [IoT Hub routnings mått](../articles/iot-hub/monitor-iot-hub-reference.md#routing-metrics) som är relaterade till routnings meddelande svars tid för att identifiera och felsöka fel när slut punktens hälso tillstånd är avstängt eller dåligt Mer information om hur du använder IoT Hub mått finns i [övervaka IoT Hub](../articles/iot-hub/monitor-iot-hub.md).

|Hälsostatus|Beskrivning|
|---|---|
|felfri|Slut punkten accepterar meddelanden som förväntat.|
|fel tillstånd|Slut punkten tar inte emot meddelanden och IoT Hub försöker skicka meddelanden till den här slut punkten igen.|
|okänd|IoT Hub har inte försökt leverera meddelanden till den här slut punkten.|
|försämrad|Slut punkten tar emot meddelanden långsammare än förväntat eller återställer från ett ohälsosamt tillstånd.|
|meddelanden|IoT Hub inte längre leverera meddelanden till den här slut punkten. Försök att skicka meddelanden till den här slut punkten misslyckades.|
