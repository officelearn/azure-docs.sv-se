---
title: Fjärr styrnings lösning Lägg till gräns enhet – Azure | Microsoft Docs
description: I den här artikeln beskrivs hur du lägger till en IoT Edge enhet i en lösning för fjärr styrnings lösningar
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/09/2018
ms.topic: conceptual
ms.openlocfilehash: 88d732cc3feaa2c9a528443d4a783227f3d8e641
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96446792"
---
# <a name="add-an-iot-edge-device-to-your-remote-monitoring-solution-accelerator"></a>Lägga till en IoT Edge enhet till din lösning för fjärr styrnings lösning

Om du vill lägga till en [IoT Edge](../iot-edge/about-iot-edge.md) enhet i Solution Accelerator utför du följande två steg:

1. Lägg till gräns enheten på sidan **Device Explorer** i webb gränssnittet för fjärr styrnings lösnings Accelerator.
1. Installera IoT Edge runtime på din Edge-enhet.

## <a name="add-the-iot-edge-device"></a>Lägg till IoT Edge enheten

Om du vill lägga till en IoT Edge-enhet till acceleratorn för fjärrövervakningslösningen navigerar du till sidan **Device Explorer** i webbgränssnittet och klickar på **+ Ny enhet**.

I panelen **ny enhet** väljer du **IoT Edge enhet**. Du behålla standardvärden för de andra inställningarna. Klicka sedan på **Använd**:

![Lägga till IoT Edge-enhet](media/iot-accelerators-remote-monitoring-add-edge-device/addedgedevice.png)

### <a name="alternative-ways-to-add-an-iot-edge-device"></a>Alternativa sätt att lägga till en IoT Edge-enhet

Det är också möjligt att registrera en IoT Edge-enhet direkt med IoT Hub-instansen i Solution Accelerator. Du måste känna till namnet på IoT-hubben i Solution Accelerator innan du följer någon av följande instruktions guider:

- [Registrera en ny Azure IoT Edge enhet från Azure Portal](../iot-edge/how-to-manual-provision-symmetric-key.md?tabs=azure-portal%2Cwindows)
- [Registrera en ny Azure IoT Edge enhet med Azure CLI](../iot-edge/how-to-manual-provision-symmetric-key.md?tabs=azure-cli%2Cwindows)
- [Registrera en ny Azure IoT Edge-enhet från Visual Studio Code](../iot-edge/how-to-manual-provision-symmetric-key.md?tabs=visual-studio-code%2Cwindows)

När du registrerar en enhet direkt med IoT Hub i lösnings acceleratorn för fjärrövervakning visas den på sidan **Device Explorer** i webb gränssnittet.

## <a name="install-the-iot-edge-runtime"></a>Installera IoT Edge-körningen

Innan du kan distribuera moduler till din gräns enhet måste du installera IoT Edge runtime på den riktiga enheten. Följande instruktions guider visar hur du installerar körningen på vanliga enhets plattformar:

- [Installera Azure IoT Edge runtime på Linux (x64)](../iot-edge/how-to-install-iot-edge.md)
- [Installera Azure IoT Edge runtime på Linux (ARM32v7/armhf)](../iot-edge/how-to-install-iot-edge.md)
- [Installera Azure IoT Edge runtime på Windows som ska användas med Windows-behållare](../iot-edge/how-to-install-iot-edge.md)
- [Installera Azure IoT Edge runtime på Windows som ska användas med Linux-behållare](../iot-edge/how-to-install-iot-edge.md)
- [Installera IoT Edge runtime på Windows IoT Core](../iot-edge/how-to-install-iot-edge.md)

## <a name="next-steps"></a>Nästa steg

Nu när du har för berett din IoT Edge-enhet är nästa steg att distribuera moduler till den. Se [Importera ett IoT Edge-paket till din lösning för fjärr styrnings lösning](iot-accelerators-remote-monitoring-import-edge-package.md)