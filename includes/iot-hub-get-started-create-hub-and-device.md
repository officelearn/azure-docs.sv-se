---
title: ta med fil
description: ta med fil
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 05/17/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 9f7fee71fb2b80be93d978569791dbb57f137949
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2018
ms.locfileid: "39346842"
---
## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-create-hub](iot-hub-create-hub.md)]

Nu när du har skapat en IoT-hubb letar du upp viktig information som du använder för att ansluta enheter och program till IoT-hubben. 

Öppna i navigeringsmenyn din IoT-hubb **principer för delad åtkomst**. Välj den **iothubowner** principen och kopiera sedan den **anslutningssträngen---primärnyckel** för IoT-hubben. Mer information finns i [Control access to IoT Hub](../articles/iot-hub/iot-hub-devguide-security.md) (Kontrollera åtkomsten till IoT Hub).

   > [!NOTE] 
   > Du behöver inte iothubowner-anslutningssträngen i den här konfigurationskursen. Men kan du behöva den för några av självstudierna eller olika IoT-scenarier när du har slutfört den här konfigurationen.

   ![Hämta IoT-hubbens anslutningssträng](./media/iot-hub-get-started-create-hub-and-device/create-iot-hub5.png)

## <a name="register-your-device-in-the-iot-hub"></a>Registrera din enhet i IoT hub

1. Öppna i navigeringsmenyn din IoT-hubb **IoT-enheter**, klicka sedan på **Lägg till** att registrera en enhet i IoT hub.

   ![Lägg till en enhet i IoT-enheter för IoT-hubben](./media/iot-hub-get-started-create-hub-and-device/create-identity-portal.png)

2. Ange en **enhets-ID** för den nya enheten. Enhets-ID är skiftlägeskänsliga.

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

3. Klicka på **Spara**.

4. När enheten har skapats öppnar du enheten i listan i den **IoT-enheter** fönstret.

5. Kopiera den **anslutningssträngen---primärnyckel** för senare användning.

   ![Hämta enhetens anslutningssträng](./media/iot-hub-get-started-create-hub-and-device/device-connection-string.png)
