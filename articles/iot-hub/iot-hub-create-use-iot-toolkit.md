---
title: Skapa en Azure IoT-hubb med Azure IoT Tools for VS Code | Microsoft-dokument
description: Lär dig hur du använder Azure IoT-verktygen för Visual Studio-kod för att skapa en Azure IoT-hubb i en resursgrupp.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: junhan
ms.openlocfilehash: 668087ae596688e86b7b84a16bc5c0fd0f9fcef6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75912258"
---
# <a name="create-an-iot-hub-using-the-azure-iot-tools-for-visual-studio-code"></a>Skapa en IoT-hubb med Azure IoT Tools for Visual Studio-kod

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Den här artikeln visar hur du använder [Azure IoT Tools för Visual Studio-kod](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) för att skapa en Azure IoT-hubb. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

För att slutföra den här artikeln behöver du följande:

- En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

- [Visual Studio-kod](https://code.visualstudio.com/)

- [Azure IoT-verktyg](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) för Visual Studio-kod.

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

1. Öppna **Utforskarvyn** i Visual Studio-kod.

2. Expandera avsnittet **Azure IoT Hub Devices** längst ned i Utforskaren. 

   ![Expandera Azure IoT Hub-enheter](./media/iot-hub-create-use-iot-toolkit/azure-iot-hub-devices.png)

3. Klicka på avsnittshuvudet **...** i avsnittet **Azure IoT Hub Devices.** Om du inte ser ellipsen håller du muspekaren över huvudet. 

4. Välj **Skapa IoT Hub**.

5. Ett popup-fönster visas i det nedre högra hörnet så att du kan logga in på Azure för första gången.

6. Välj Azure-prenumeration. 

7. Välj resursgrupp.

8. Välj plats.

9. Välj prisnivå.

10. Ange ett globalt unikt namn för din IoT Hub.

11. Vänta några minuter tills IoT Hub skapas.

## <a name="next-steps"></a>Nästa steg

Nu har du distribuerat en IoT-hubb med Azure IoT Tools for Visual Studio Code. För att utforska ytterligare, kolla in följande artiklar:

* [Använd Azure IoT Tools for Visual Studio Code för att skicka och ta emot meddelanden mellan din enhet och en IoT Hub](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

* [Använda Azure IoT Tools för Visual Studio-kod för Azure IoT Hub-enhetshantering](iot-hub-device-management-iot-toolkit.md)

* [Se wiki-sidan Azure IoT Hub for VS Code](https://github.com/microsoft/vscode-azure-iot-toolkit/wiki).
