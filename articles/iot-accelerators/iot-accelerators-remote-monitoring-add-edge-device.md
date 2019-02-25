---
title: Remote Monitoring-lösningen lägga till Edge-enhet – Azure | Microsoft Docs
description: Den här artikeln beskrivs hur du lägger till en IoT Edge-enhet till en lösningsacceleratorn för fjärrövervakning
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/09/2018
ms.topic: conceptual
ms.openlocfilehash: d34ac159a216c5c77214b4c8b799a233c3671235
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/24/2019
ms.locfileid: "56749553"
---
# <a name="add-an-iot-edge-device-to-your-remote-monitoring-solution-accelerator"></a>Lägga till en IoT Edge-enhet i lösningsacceleratorn för fjärrövervakning

Att lägga till en [IoT Edge](../iot-edge/about-iot-edge.md) enheten utvecklingsacceleratorn, utför följande två steg:

1. Lägg till Edge-enhet på den **Device Explorer** sidan fjärrövervakning solution accelerator webbläsaren.
1. Installera IoT Edge-körningen på din Edge-enhet.

## <a name="add-the-iot-edge-device"></a>Lägg till IoT Edge-enhet

Om du vill lägga till en IoT Edge-enhet att lösningsacceleratorn för fjärrövervakning, navigera till den **Device Explorer** i webbläsaren och klicka på **+ ny enhet**.

I den **ny enhet** panelen, väljer **IoT Edge-enhet**. Du kan behålla standardvärden för de andra inställningarna. Klicka sedan på **Använd**:

![Lägg till IoT Edge-enhet](media/iot-accelerators-remote-monitoring-add-edge-device/addedgedevice.png)

### <a name="alternative-ways-to-add-an-iot-edge-device"></a>Olika sätt att lägga till en IoT Edge-enhet

Det är också möjligt att registrera en IoT Edge-enhet direkt med IoT Hub-instansen i utvecklingsacceleratorn. Du behöver veta namnet på IoT-hubb i utvecklingsacceleratorn innan du följer någon av dessa instruktionsguider:

- [Registrera en ny Azure IoT Edge-enhet från Azure portal](../iot-edge/how-to-register-device-portal.md)
- [Registrera en ny Azure IoT Edge-enhet med Azure CLI](../iot-edge/how-to-register-device-cli.md)
- [Registrera en ny Azure IoT Edge-enhet från Visual Studio Code](../iot-edge/how-to-register-device-vscode.md)

När du registrerar en enhet direkt med IoT hub i lösningsacceleratorn för fjärrövervakning det visas på den **Device Explorer** sida i webbläsaren.

## <a name="install-the-iot-edge-runtime"></a>Installera IoT Edge-körningen

Innan du kan distribuera moduler till din Edge-enhet, måste du installera IoT Edge-körningen på den faktiska enheten. Följande instruktionsguider visar hur du installera runtime på vanliga enhetsplattformar:

- [Installera Azure IoT Edge-körningen på Linux (x64)](../iot-edge/how-to-install-iot-edge-linux.md)
- [Installera Azure IoT Edge-körningen på Linux (ARM32v7/armhf)](../iot-edge/how-to-install-iot-edge-linux-arm.md)
- [Installera Azure IoT Edge-körningen på Windows för användning med Windows-behållare](../iot-edge/how-to-install-iot-edge-windows-with-windows.md)
- [Installera Azure IoT Edge-körningen på Windows för användning med Linux-behållare](../iot-edge/how-to-install-iot-edge-windows-with-linux.md)
- [Installera IoT Edge-körningen på Windows IoT Core](../iot-edge/how-to-install-iot-core.md)

## <a name="next-steps"></a>Nästa steg

Nu när du har förberett din IoT Edge-enhet, är nästa steg att distribuera moduler till den. Se [importera en IoT Edge-paketet till lösningsacceleratorn för fjärrövervakning](iot-accelerators-remote-monitoring-import-edge-package.md)
