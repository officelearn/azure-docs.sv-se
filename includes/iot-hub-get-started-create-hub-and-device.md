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
ms.openlocfilehash: f8cd78e63099f864c5fc54b6268f6e558d738626
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
ms.locfileid: "34371379"
---
## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-create-hub](iot-hub-create-hub.md)]

Nu när du har skapat en IoT-hubb kan hitta viktig information som du använder för att ansluta enheter och program till din IoT-hubb. 

Öppna i navigeringsmenyn din IoT-hubb **principer för delad åtkomst**.
Välj den **iothubowner** principen och kopiera sedan de **anslutningssträngen---primärnyckel** för din IoT-hubb. Mer information finns i [Control access to IoT Hub](../articles/iot-hub/iot-hub-devguide-security.md) (Kontrollera åtkomsten till IoT Hub).

   > [!NOTE] 
   > Du behöver inte den här iothubowner anslutningssträngen för den här självstudiekursen för installation. Men kan du behöva den för några av de självstudier eller olika IoT-scenarier när du har slutfört den här uppsättningen.

   ![Hämta IoT-hubbens anslutningssträng](./media/iot-hub-get-started-create-hub-and-device/create-iot-hub5.png)

## <a name="register-a-device-in-the-iot-hub-for-your-device"></a>Registrera en enhet i IoT-hubben för din enhet

1. Öppna i navigeringsmenyn din IoT-hubb **IoT-enheter**, klicka på **Lägg till** att registrera en enhet i din IoT-hubb.

   ![Lägga till en enhet i IoT-enheter för din IoT-hubb](./media/iot-hub-get-started-create-hub-and-device/create-identity-portal.png)

2. Ange en **enhets-ID** för den nya enheten. Enhets-ID är skiftlägeskänsliga.

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

4. Klicka på **Spara**.
5. När enheten har skapats kan du öppna enheten i listan i den **IoT-enheter** fönstret.
6. Kopiera den **anslutningssträngen---primärnyckel** för senare användning.

   ![Hämta enhetens anslutningssträng](./media/iot-hub-get-started-create-hub-and-device/device-connection-string.png)
