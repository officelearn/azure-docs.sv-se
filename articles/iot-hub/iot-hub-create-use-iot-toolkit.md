---
title: Skapa en Azure IoT Hub med hjälp av Azure IoT Hub Toolkit för VS Code | Microsoft Docs
description: Hur du använder Azure IoT Hub-verktyg för VS Code för att skapa en IoT hub.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: junhan
ms.openlocfilehash: cee71ddfbf1a20cc7417976d60b04bff6f0deac8
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2018
ms.locfileid: "53339033"
---
# <a name="create-an-iot-hub-using-the-azure-iot-hub-toolkit-for-visual-studio-code"></a>Skapa en IoT hub med Azure IoT Hub Toolkit för Visual Studio Code

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Den här artikeln visar hur du använder den [Azure IoT Hub Toolkit för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) (tidigare Azure IoT Toolkit) att skapa en Azure IoT hub. 

För att slutföra den här artikeln behöver du följande:

- En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

- [Visual Studio Code](https://code.visualstudio.com/)

- [Azure IoT Hub Toolkit](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

1. I Visual Studio Code, öppna den **Explorer** vy.

2. Längst ned i Utforskaren, expandera den **Azure IoT Hub-enheter** avsnittet. 

   ![Expandera Azure IoT Hub-enheter](./media/iot-hub-create-use-iot-toolkit/azure-iot-hub-devices.png)

3. Klicka på den **...**  i den **Azure IoT Hub-enheter** avsnittsrubrik. Om du inte ser de tre punkterna, hovrar du över rubriken. 

4. Välj **skapa IoT-hubb**.

5. Ett popup-fönster visas i det nedre högra hörnet så att du kan logga in på Azure för första gången.

6. Välj Azure-prenumeration. 

7. Välj resursgrupp.

8. Välj plats.

9. Välj prisnivå.

10. Ange ett globalt unikt namn för din IoT Hub.

11. Vänta några minuter tills IoT-hubben har skapats.

## <a name="next-steps"></a>Nästa steg

Nu har du distribuerat en IoT hub med Azure IoT Hub Toolkit för Visual Studio Code. Om du vill utforska ytterligare ta en titt i följande artiklar:

* [Använd Azure IoT Hub Toolkit-tillägget för Visual Studio Code för att skicka och ta emot meddelanden mellan enheten och en IoT-hubb](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

* [Använd Azure IoT Hub Toolkit-tillägget för Visual Studio Code för Azure IoT Hub-enhetshantering](iot-hub-device-management-iot-toolkit.md)

* [Finns i wiki-sida för Azure IoT Hub Toolkit](https://github.com/microsoft/vscode-azure-iot-toolkit/wiki).
