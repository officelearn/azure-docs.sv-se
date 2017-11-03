---
title: "Komma igång med att ansluta fysiska enheter till Azure IoT Hub | Microsoft Docs"
description: "Lär dig mer om att ansluta till Azure IoT Hub fysiska enheter och -kort. Enheterna kan skicka telemetri IoT-hubb och IoT-hubb kan övervaka och hantera dina enheter."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
keywords: "självstudiekurs för Azure iot-hubb"
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2017
ms.author: dobett
ms.openlocfilehash: f4128b6b049aa876e170c56dcf2e40720644dc3d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-iot-hub-get-started-with-physical-devices-tutorials"></a>Azure IoT-hubb Kom igång med fysiska enheter självstudier

Dessa självstudiekurser introduktion till Azure IoT Hub och SDK-enheten. Kursen beskriver vanliga IoT-scenarier för att demonstrera funktionerna i IoT-hubb. Självstudierna visar även hur du kombinerar IoT-hubb med andra Azure-tjänster och verktyg för att skapa mer kraftfulla IoT-lösningar. Självstudiekurser som anges i följande tabell visar hur du skapar fysiska IoT-enheter.

| IoT-enhet                       | Programmeringsspråk |
|---------------------------------|----------------------|
| Raspberry Pi                    | [Python][Pi_Py], [Node.js][Pi_Nd], [C][Pi_C]  |
| IoT DevKit                      | [Arduino i VSCode][DevKit]     |
| Intel Edison                    | [Node.js][Ed_Nd], [C][Ed_C]           |
| Adafruit ludd HUZZAH ESP8266 | [Arduino][Hu_Ard]              |
| Sparkfun ESP8266 sak Dev      | [Arduino][Th_Ard]              |
| Adafruit ludd M0             | [Arduino][M0_Ard]              |

Dessutom kan du använda en IoT-gräns-gatewayen att enheterna ansluter till din IoT-hubb.

| Gateway-enhet               | Programmeringsspråk | Plattform         |
|------------------------------|----------------------|------------------|
| Intel NUC (model DE3815TYKE) | C                    | [Vind floden Linux][NUC_Lnx] |

[!INCLUDE [iot-hub-get-started-extended](../../includes/iot-hub-get-started-extended.md)]


[Pi_Nd]: iot-hub-raspberry-pi-kit-node-get-started.md
[Pi_C]: iot-hub-raspberry-pi-kit-c-get-started.md
[Pi_Py]: iot-hub-raspberry-pi-kit-python-get-started.md
[DevKit]: iot-hub-arduino-iot-devkit-az3166-get-started.md
[Ed_Nd]: iot-hub-intel-edison-kit-node-get-started.md
[Ed_C]: iot-hub-intel-edison-kit-c-get-started.md
[Hu_Ard]: iot-hub-arduino-huzzah-esp8266-get-started.md
[Th_Ard]: iot-hub-sparkfun-esp8266-thing-dev-get-started.md
[M0_Ard]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-get-started.md
[NUC_Lnx]: iot-hub-gateway-kit-c-lesson1-set-up-nuc.md
