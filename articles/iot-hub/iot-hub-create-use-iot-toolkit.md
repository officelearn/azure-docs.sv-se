---
title: Skapa en Azure IoT Hub med hjälp av Azure IoT Toolkit för VS Code | Microsoft Docs
description: Hur du använder Azure IoT Toolkit för VS Code för att skapa en IoT hub.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: junhan
ms.openlocfilehash: af31f9375d6a41e13a9122e9730ba9532d3d52c5
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2018
ms.locfileid: "40003077"
---
# <a name="create-an-iot-hub-using-the-azure-iot-toolkit-for-visual-studio-code"></a>Skapa en IoT hub med Azure IoT Toolkit för Visual Studio Code

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Du kan använda [Azure IoT Toolkit för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) att skapa Azure IoT-hubbar. Den här artikeln visar hur du skapar en IoT hub med Azure IoT Toolkit.

För att slutföra den här artikeln behöver du följande:

* Ett aktivt Azure-konto.
- [Visual Studio Code](https://code.visualstudio.com/)
- [Azure IoT Toolkit](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

1. I Visual Studio Code, öppna den **Explorer** vy.

2. Längst ned i Utforskaren, expandera den **Azure IoT Hub-enheter** avsnittet. 

   ![Expandera Azure IoT Hub-enheter](./media/iot-hub-create-use-iot-toolkit/azure-iot-hub-devices.png)

3. Klicka på den **... ** i den **Azure IoT Hub-enheter** avsnittsrubrik. Om du inte ser de tre punkterna, hovrar du över rubriken. 

4. Välj **skapa IoT-hubb**.

5. Ett popup-fönster visas i det nedre högra hörnet så att du kan logga in på Azure för första gången.

6. Välj Azure-prenumeration. 

7. Välj resursgrupp.

8. Välj plats.

9. Välj prisnivå.

10. Ange ett globalt unikt namn för din IoT Hub.

11. Vänta några minuter tills IoT-hubben har skapats.

## <a name="next-steps"></a>Nästa steg

Nu du har distribuerat en IoT hub med Azure IoT Toolkit för Visual Studio Code, kanske du vill utforska ytterligare:

* [Använd Azure IoT Toolkit-tillägget för Visual Studio Code för att skicka och ta emot meddelanden mellan enheten och IoT Hub](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).
* [Använd Azure IoT Toolkit-tillägget för Visual Studio Code för Azure IoT Hub-enhetshantering](iot-hub-device-management-iot-toolkit.md)
* [Wiki-sida](https://github.com/microsoft/vscode-azure-iot-toolkit/wiki) för Azure IoT Toolkit.
