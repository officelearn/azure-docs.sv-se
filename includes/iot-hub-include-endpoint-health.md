---
title: inkludera fil
description: inkludera fil
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/28/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: a0d1de622eefad4ae5e55a427f8b0b1bf4360c0a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84792105"
---
Du kan använda REST API [Hämta slut punkts hälsa](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) för att få hälso status för slut punkterna. Vi rekommenderar att du använder [IoT Hub mått](../articles/iot-hub/iot-hub-metrics.md) som rör svars tiden för routning av meddelanden för att identifiera och felsöka fel när slut punktens hälso tillstånd är död eller ohälsosamt, eftersom fördröjningen blir högre när slut punkten är i något av dessa tillstånd.


|Hälsostatus|Description|
|---|---|
|felfri|Slut punkten accepterar meddelanden som förväntat.|
|fel tillstånd|Slut punkten tar inte emot meddelanden och IoT Hub försöker skicka meddelanden till den här slut punkten igen.|
|okänd|IoT Hub har inte försökt leverera meddelanden till den här slut punkten.|
|försämrad|Slut punkten tar emot meddelanden långsammare än förväntat eller återställer från ett ohälsosamt tillstånd.|
|meddelanden|IoT Hub inte längre leverera meddelanden till den här slut punkten. Försök att skicka meddelanden till den här slut punkten misslyckades.|
