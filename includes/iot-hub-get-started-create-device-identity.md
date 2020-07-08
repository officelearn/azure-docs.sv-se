---
title: inkludera fil
description: inkludera fil
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 09/07/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 68260bf8aafbbe5afd46ec7dfb763eb88ee2123e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "78893024"
---
I det här avsnittet använder du Azure CLI för att skapa en enhets identitet för den här artikeln. Enhets-ID är skiftlägeskänsliga.

1. Öppna [Azure Cloud Shell](https://shell.azure.com/).

1. I Azure Cloud Shell kör du följande kommando för att installera Microsoft Azure IoT-tillägget för Azure CLI:

    ```azurecli-interactive
    az extension add --name azure-iot
    ```

2. Skapa en ny enhets identitet `myDeviceId` med namnet och hämta anslutnings strängen för enheten med följande kommandon:

    ```azurecli-interactive
    az iot hub device-identity create --device-id myDeviceId --hub-name {Your IoT Hub name}
    az iot hub device-identity show-connection-string --device-id myDeviceId --hub-name {Your IoT Hub name} -o table
    ```

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

Anteckna enhetens anslutnings sträng från resultatet. Den här enhets anslutnings strängen används av Device-appen för att ansluta till din IoT Hub som en enhet.

<!-- images and links -->
