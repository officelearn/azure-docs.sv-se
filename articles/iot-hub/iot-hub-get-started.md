---
title: Azure IoT-hubb - komma igång ansluta IoT-enheter till molnet | Microsoft Docs
description: Lär dig hur du ansluter din IoT-kort och startpaket till Azure IoT Hub. Enheterna kan skicka telemetri IoT-hubb och IoT-hubb kan övervaka och hantera dina enheter.
author: dominicbetts
manager: timlt
keywords: självstudiekurs för Azure iot-hubb
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: 76f427204f0ad31196ce8b995b9e4ed9676209ca
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34634710"
---
# <a name="azure-iot-hub-get-started-with-real-devices"></a>Azure IoT-hubb Kom igång med verkliga enheter

Du kan använda Azure IoT Hub och SDK för Azure IoT-enhet för att skapa lösningar för Sakernas Internet (IoT):

* Azure IoT Hub är en helt hanterad tjänst i molnet som ansluter, övervakar och hanterar IoT-enheter på ett säkert sätt. Använd SDK: er för Azure IoT-enhet för att implementera din IoT-enheter.
* Använd en IoT-gateway i mer komplexa IoT-scenarier. Till exempel när du behöver Tänk till exempel äldre enheter, kostnader för bandbredd, principer för säkerhet och sekretess eller edge databearbetning. I dessa fall kan använda [Azure IoT kant](https://docs.microsoft.com/azure/iot-edge/) att implementera en gateway som ansluter enheter till din IoT-hubb.

## <a name="what-the-how-to-articles-cover"></a>Instruktionsartiklar omfattar

Dessa artiklar introduktion till Azure IoT Hub och SDK-enheten. Artiklarna beskriver vanliga IoT-scenarier för att demonstrera funktionerna i IoT-hubb. Artiklarna visar även hur du kombinerar IoT-hubb med andra Azure-tjänster och verktyg för att skapa mer kraftfulla IoT-lösningar. I artiklar använder du verkliga IoT-enheter.

## <a name="set-up-your-device"></a>Konfigurera enheten

Anslut en IoT-enhet eller gateway för Azure IoT-hubb:

| IoT-enhet                       | Programmeringsspråk |
|----------------------------------|----------------------|
| Raspberry Pi                     | [Python][Pi_Py], [Node.js][Pi_Nd], [C][Pi_C]  |
| IoT DevKit                       | [Arduino i VSCode][DevKit]     |
| Intel Edison                     | [Node.js][Ed_Nd], [C][Ed_C]    |
| Adafruit ludd HUZZAH ESP8266  | [Arduino][Hu_Ard]              |
| Sparkfun ESP8266 sak Dev       | [Arduino][Th_Ard]              |
| Adafruit ludd M0              | [Arduino][M0_Ard]              |
| Online enheten simulatorn         | [Raspberry Pi (Node.js)][Ol_Sim] |

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
[Ol_Sim]: iot-hub-raspberry-pi-web-simulator-get-started.md
