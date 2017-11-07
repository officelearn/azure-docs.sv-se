---
title: Ansluta en Raspberry Pi till Azure IoT Suite | Microsoft Docs
description: "Självstudier med Node.js eller C för att hjälpa dig att lära dig hur du använder Microsoft Azure IoT-startpaket för hallon Pi 3 och fjärranslutna övervakningslösning IoT Suite. Du kan välja en självstudiekurs som simulerar telemetri, som använder verkliga sensorer eller som gör att fjärranslutna firmware-uppdateringar."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: d9e737fcaaabd2088d2920b69fca96d6058f4b4a
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2017
---
# <a name="connect-your-microsoft-azure-iot-raspberry-pi-3-starter-kit-to-the-remote-monitoring-solution"></a>Ansluta din Microsoft Azure IoT hallon Pi 3 startpaket till fjärranslutna övervakningslösning

Självstudiekurser i det här avsnittet hjälpa dig att lära dig hur du ansluter en hallon Pi 3-enhet till fjärranslutna övervakningslösning. Välj kursen bör du önskade programmeringsspråk och om du har den sensor maskinvaran som är tillgängliga för användning med din hallon Pi.

## <a name="the-tutorials"></a>Självstudierna

| Självstudier | Anteckningar | Språk |
| -------- | ----- | --------- |
| Simulerade telemetri (grundläggande)| Simulerar sensordata. Använder en fristående hallon Pi. | [C][lnk-c-simulator], [Node.js][lnk-node-simulator] |
| Verklig temperatursensor (mellanliggande) | Använder data från en BME280 sensor som är ansluten till din hallon Pi. | [C][lnk-c-basic], [Node.js][lnk-node-basic] |
| Implementera firmware-uppdatering (Avancerat)| Använder data från en BME280 sensor som är ansluten till din hallon Pi. Aktiverar fjärråtkomst programvara på din hallon Pi. | [C][lnk-c-advanced], [Node.js][lnk-node-advanced] |

## <a name="next-steps"></a>Nästa steg

Besök den [Azure IoT Dev Center](https://azure.microsoft.com/develop/iot/) fler exempel och dokumentation om Azure IoT.

[lnk-node-simulator]: iot-suite-v1-raspberry-pi-kit-node-get-started-simulator.md
[lnk-node-basic]: iot-suite-v1-raspberry-pi-kit-node-get-started-basic.md
[lnk-node-advanced]: iot-suite-v1-raspberry-pi-kit-node-get-started-advanced.md
[lnk-c-simulator]: iot-suite-v1-raspberry-pi-kit-c-get-started-simulator.md
[lnk-c-basic]: iot-suite-v1-raspberry-pi-kit-c-get-started-basic.md
[lnk-c-advanced]: iot-suite-v1-raspberry-pi-kit-c-get-started-advanced.md