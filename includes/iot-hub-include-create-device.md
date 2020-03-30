---
title: ta med fil
description: ta med fil
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 11/06/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 7b022f71e197c5695876f2049ee376c3616afc6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "70048986"
---
<!-- put the ## header in the file that includes this file -->

I det här avsnittet skapar du en enhetsidentitet i identitetsregistret i IoT-hubben. En enhet kan inte ansluta till ett nav om den inte har en post i identitetsregistret. Mer information finns i [utvecklarhandboken för IoT Hub](../articles/iot-hub/iot-hub-devguide-identity-registry.md#identity-registry-operations).

1. Öppna **IoT-enheter**på navigeringsmenyn för IoT-hubb och välj sedan **Ny** om du vill lägga till en enhet i IoT-hubben.

    ![Skapa enhetsidentitet i portalen](./media/iot-hub-include-create-device/create-identity-portal-vs2019.png)

1. Ange ett namn på den nya enheten, till exempel **myDeviceId**i **Skapa en enhet,** och välj **Spara**. Den här åtgärden skapar en enhetsidentitet för din IoT-hubb.

   ![Lägga till en ny enhet](./media/iot-hub-include-create-device/create-a-device-vs2019.png)

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

1. När enheten har skapats öppnar du enheten från listan i fönsterrutan **IoT-enheter**. Kopiera den **primära anslutningssträngen** som ska användas senare.

    ![Anslutningssträng för enhet](./media/iot-hub-include-create-device/device-details-vs2019.png)

> [!NOTE]
> IoT Hub-identitetsregistret lagrar bara enhetsidentiteter för att skydda åtkomsten till IoT Hub. Registret lagrar enhets-ID:n och enhetsnycklar som ska användas som säkerhetsreferenser och en aktiverad/inaktiverad-flagga som du kan använda för att inaktivera åtkomst för en enskild enhet. Om ditt program behöver lagra andra enhetsspecifika metadata bör det använda ett programspecifikt datalager. Mer information finns i [utvecklarhandboken för IoT Hub](../articles/iot-hub/iot-hub-devguide-identity-registry.md).
