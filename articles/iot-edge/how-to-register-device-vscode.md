---
title: Registrera en ny enhet från Visual Studio Code - Azure IoT Edge | Microsoft Docs
description: Använda Visual Studio Code för att skapa en ny IoT Edge-enhet i Azure IoT hub och hämta anslutningssträngen
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/14/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: cc812cc7f628f533cbf342edee66e01ce703678f
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53099796"
---
# <a name="register-a-new-azure-iot-edge-device-from-visual-studio-code"></a>Registrera en ny Azure IoT Edge-enhet från Visual Studio Code

Innan du kan använda dina IoT-enheter med Azure IoT Edge, måste du registrera dem med IoT-hubben. När du registrerar en enhet får du en anslutningssträng som kan användas för att konfigurera din enhet för Edge-arbetsbelastningar. 

Den här artikeln visar hur du registrerar en ny IoT Edge-enhet med hjälp av Visual Studio Code (VS Code). Det finns flera sätt att utföra de flesta åtgärder i VS Code. Den här artikeln använder du Utforskaren, men du kan också använda Kommandopaletten för att köra de flesta stegen. 

## <a name="prerequisites"></a>Förutsättningar

* En [IoT-hubb](../iot-hub/iot-hub-create-through-portal.md) i Azure-prenumerationen
* [Visual Studio Code](https://code.visualstudio.com/) 
* [Azure IoT Edge-tillägget](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) för Visual Studio Code

## <a name="sign-in-to-access-your-iot-hub"></a>Logga in att få åtkomst till din IoT-hubb

Du kan använda Azure IoT-tillägg för Visual Studio Code för att utföra åtgärder med IoT-hubben. För dessa åtgärder ska fungera måste du logga in på ditt Azure-konto och välj IoT-hubben som du arbetar med.

1. I Visual Studio Code, öppna den **Explorer** vy.

2. Längst ned i Utforskaren, expandera den **Azure IoT Hub-enheter** avsnittet. 

   ![Expandera avsnittet Azure IoT Hub-enheter](./media/how-to-register-device-vscode/azure-iot-hub-devices.png)

3. Klicka på den **...**  i den **Azure IoT Hub-enheter** avsnittsrubrik. Om du inte ser de tre punkterna, klicka på eller hovra över rubriken. 

4. Välj **Välj IoT Hub**.

5. Följ anvisningarna för att göra det om du inte har loggat in på kontot. 

6. Välj din Azure-prenumeration. 

7. Välj din IoT-hubb. 

## <a name="create-a-device"></a>Skapa en enhet

1. I VS Code-Utforskaren expanderar den **Azure IoT Hub-enheter** avsnittet. 

2. Klicka på den **...**  i den **Azure IoT Hub-enheter** avsnittsrubrik. Om du inte ser de tre punkterna, klicka på eller hovra över rubriken. 

3. Välj **skapa IoT Edge-enhet**. 

4. I textrutan som öppnas, ge enheten ett ID. 

På skärmen utdata visas resultatet av kommandot. Enhetsinformation skrivs ut, som innehåller den **deviceId** som du angav och **connectionString** att du kan använda för att ansluta den fysiska enheten till IoT hub. 

## <a name="view-all-devices"></a>Visa alla enheter

Alla enheter som ansluter till IoT-hubben visas i den **Azure IoT Hub-enheter** avsnittet i Visual Studio Code-Utforskaren. IoT Edge-enheter är olika från icke-Edge-enheter med en annan ikon, och det faktum att de kan utökas för att visa modulerna som distribueras till varje enhet. 

   ![Visa alla IoT Edge-enheter i IoT hub](./media/how-to-register-device-vscode/view-devices.png)

## <a name="retrieve-the-connection-string"></a>Hämta anslutningssträngen

När du är redo att konfigurera din enhet, måste den anslutningssträng som länkar den fysiska enheten med sin identitet i IoT hub.

1. Högerklicka på ID för din enhet i den **Azure IoT Hub-enheter** avsnittet. 
2. Välj **kopiera enhetens anslutningssträng**.

   Anslutningssträngen kopieras till Urklipp. 

Du kan också välja **hämta enhetsinformation** från snabbmenyn att se alla enhetsinformation, inklusive anslutningssträngen i utdatafönstret. 


## <a name="next-steps"></a>Nästa steg

Lär dig hur du [distribuerar moduler på en enhet med Visual Studio Code](how-to-deploy-modules-vscode.md).
