---
title: Lösning för fjärrövervakning lägga till Edge-enhet – Azure | Microsoft-dokument
description: I den här artikeln beskrivs hur du lägger till en IoT Edge-enhet i en lösningsaccelerator för fjärrövervakning
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/09/2018
ms.topic: conceptual
ms.openlocfilehash: 0a42763ff47cccfa506acbbbd95d20d41eb0827f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72965376"
---
# <a name="add-an-iot-edge-device-to-your-remote-monitoring-solution-accelerator"></a>Lägga till en IoT Edge-enhet i lösningsacceleratorn för fjärrövervakning

Om du vill lägga till en [IoT Edge-enhet](../iot-edge/about-iot-edge.md) i lösningsacceleratorn gör du följande två steg:

1. Lägg till Edge-enheten på sidan **Enhetsutforskaren** i webbgränssnittet för fjärrövervakningslösningsaccelerator.
1. Installera IoT Edge-körningen på Edge-enheten.

## <a name="add-the-iot-edge-device"></a>Lägga till IoT Edge-enheten

Om du vill lägga till en IoT Edge-enhet till acceleratorn för fjärrövervakningslösningen navigerar du till sidan **Device Explorer** i webbgränssnittet och klickar på **+ Ny enhet**.

På panelen **Ny enhet** väljer du **IoT Edge-enhet**. Du behålla standardvärden för de andra inställningarna. Klicka sedan på **Använd**:

![Lägga till IoT Edge-enhet](media/iot-accelerators-remote-monitoring-add-edge-device/addedgedevice.png)

### <a name="alternative-ways-to-add-an-iot-edge-device"></a>Alternativa sätt att lägga till en IoT Edge-enhet

Det är också möjligt att registrera en IoT Edge-enhet direkt med IoT Hub-instansen i din lösningsaccelerator. Du måste veta namnet på IoT-hubben i lösningsacceleratorn innan du följer någon av dessa how-to-guider:

- [Registrera en ny Azure IoT Edge-enhet från Azure-portalen](../iot-edge/how-to-register-device.md#register-in-the-azure-portal)
- [Registrera en ny Azure IoT Edge-enhet med Azure CLI](../iot-edge/how-to-register-device.md#register-with-the-azure-cli)
- [Registrera en ny Azure IoT Edge-enhet från Visual Studio Code](../iot-edge/how-to-register-device.md#register-with-visual-studio-code)

När du registrerar en enhet direkt med IoT-hubben i lösningsacceleratorn för fjärrövervakning visas den på sidan **Enhetsutforskaren** i webbgränssnittet.

## <a name="install-the-iot-edge-runtime"></a>Installera IoT Edge-körningen

Innan du kan distribuera moduler till Edge-enheten måste du installera IoT Edge-körningen på den riktiga enheten. Följande programguider visar hur du installerar körningen på vanliga enhetsplattformar:

- [Installera Azure IoT Edge-körningen på Linux (x64)](../iot-edge/how-to-install-iot-edge-linux.md)
- [Installera Azure IoT Edge-körning på Linux (ARM32v7/armhf)](../iot-edge/how-to-install-iot-edge-linux-arm.md)
- [Installera Azure IoT Edge-körningen i Windows som ska användas med Windows-behållare](../iot-edge/how-to-install-iot-edge-windows-with-windows.md)
- [Installera Azure IoT Edge-körningen i Windows för användning med Linux-behållare](../iot-edge/how-to-install-iot-edge-windows-with-linux.md)
- [Installera IoT Edge-körningen på Windows IoT Core](../iot-edge/how-to-install-iot-core.md)

## <a name="next-steps"></a>Nästa steg

Nu när du har förberett din IoT Edge-enhet är nästa steg att distribuera moduler till den. Se [Importera ett IoT Edge-paket till din lösningsaccelerator för fjärrövervakning](iot-accelerators-remote-monitoring-import-edge-package.md)
