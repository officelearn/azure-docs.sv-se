---
title: "Azure IoT-hubb - komma igång ansluta IoT-enheter till molnet | Microsoft Docs"
description: "Lär dig hur du ansluter din IoT-kort och startpaket till Azure IoT Hub. Enheterna kan skicka telemetri IoT-hubb och IoT-hubb kan övervaka och hantera dina enheter."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
keywords: "självstudiekurs för Azure iot-hubb"
ms.assetid: 24376318-5344-4a81-a1e6-0003ed587d53
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: 34742208e9189eb31310b58770ee4a22e33f56d5
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2018
---
# <a name="azure-iot-hub-get-started-tutorials"></a>Azure IoT-hubb komma igång Självstudier

Du kan använda Azure IoT Hub och SDK för Azure IoT-enhet för att skapa lösningar för Sakernas Internet (IoT):

* Azure IoT Hub är en helt hanterad tjänst i molnet som ansluter, övervakar och hanterar IoT-enheter på ett säkert sätt. Använd SDK: er för Azure IoT-enhet för att implementera din IoT-enheter.
* Använd en IoT-gateway i mer komplexa IoT-scenarier. Till exempel när du behöver Tänk till exempel äldre enheter, kostnader för bandbredd, principer för säkerhet och sekretess eller edge databearbetning. I dessa fall kan använda [Azure IoT kant](https://docs.microsoft.com/azure/iot-edge/) att implementera en gateway som ansluter enheter till din IoT-hubb.

## <a name="what-the-tutorials-cover"></a>Självstudierna omfattar

Dessa självstudiekurser introduktion till Azure IoT Hub och SDK-enheten. Kursen beskriver vanliga IoT-scenarier för att demonstrera funktionerna i IoT-hubb. Självstudierna visar även hur du kombinerar IoT-hubb med andra Azure-tjänster och verktyg för att skapa mer kraftfulla IoT-lösningar. I självstudierna, kan du välja att använda simulerad eller verkliga IoT-enheter. Dessutom kan du lära dig hur du använder en gateway för att aktivera enheter ansluter till din IoT-hubb.

## <a name="set-up-your-device"></a>Konfigurera enheten

Ansluta en IoT-enhet eller gateway för Azure IoT Hub. Du kan välja en fysisk eller simulerade enhet för att komma igång:

| IoT-enhet                       | Programmeringsspråk |
|----------------------------------|----------------------|
| Raspberry Pi                     | [Python][Pi_Py], [Node.js][Pi_Nd], [C][Pi_C]  |
| IoT DevKit                       | [Arduino i VSCode][DevKit]     |
| Intel Edison                     | [Node.js][Ed_Nd], [C][Ed_C]    |
| Adafruit Feather HUZZAH ESP8266  | [Arduino][Hu_Ard]              |
| Sparkfun ESP8266 sak Dev       | [Arduino][Th_Ard]              |
| Adafruit Feather M0              | [Arduino][M0_Ard]              |
| Simulerade enhet på datorn           | [.NET][Sim_NET], [Java][Sim_Jav], [Node.js][Sim_Nd], [Python][Sim_Pyth] |
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
[Sim_NET]: iot-hub-csharp-csharp-getstarted.md
[Sim_Jav]: iot-hub-java-java-getstarted.md
[Sim_Nd]: iot-hub-node-node-getstarted.md
[Sim_Pyth]: iot-hub-python-getstarted.md
[NUC_Lnx]: iot-hub-gateway-kit-c-lesson1-set-up-nuc.md
[Sim_Lnx]: iot-hub-linux-iot-edge-get-started.md
[Sim_Win]: iot-hub-windows-iot-edge-get-started.md
[Ol_Sim]: iot-hub-raspberry-pi-web-simulator-get-started.md
