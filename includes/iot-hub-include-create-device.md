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
ms.openlocfilehash: d70544866b9e321d98acd3978da145276e036025
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60354968"
---
<!-- put the ## header in the file that includes this file -->

I det här avsnittet ska skapa du en enhetsidentitet i identitetsregistret i IoT hub. En enhet kan inte ansluta till IoT Hub om den inte har en post i identitetsregistret. Mer information finns i avsnittet ”identitetsregistret” i den [utvecklarhandboken för IoT Hub](../articles/iot-hub/iot-hub-devguide-identity-registry.md) 

1. Öppna i navigeringsmenyn din IoT-hubb **IoT-enheter**och välj sedan **Lägg till** att registrera en ny enhet i IoT hub.

    ![Skapa en enhetsidentitet i portalen](./media/iot-hub-include-create-device/create-identity-portal.png)

1. Ange ett namn för den nya enheten, till exempel **myDeviceId**, och välj **spara**. Den här åtgärden skapar en ny enhetsidentitet för IoT-hubben.

   ![Lägg till en ny enhet](./media/iot-hub-include-create-device/create-a-device.png)

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]


1. När enheten har skapats öppnar du enheten i listan i den **IoT-enheter** fönstret. Kopiera den **anslutningssträngen---primärnyckel** för senare användning.

    ![Enhetens anslutningssträng](./media/iot-hub-include-create-device/device-details.png)

> [!NOTE]
> IoT Hub-identitetsregistret lagrar bara enhetsidentiteter för att skydda åtkomsten till IoT Hub. Registret lagrar enhets-ID:n och enhetsnycklar som ska användas som säkerhetsreferenser och en aktiverad/inaktiverad-flagga som du kan använda för att inaktivera åtkomst för en enskild enhet. Om ditt program behöver lagra andra enhetsspecifika metadata bör det använda ett programspecifikt datalager. Mer information finns i [utvecklarhandboken för IoT Hub](../articles/iot-hub/iot-hub-devguide-identity-registry.md).
