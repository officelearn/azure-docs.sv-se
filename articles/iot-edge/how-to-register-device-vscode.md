---
title: Registrera en ny Azure IoT Edge-enhet (VS-kod) | Microsoft Docs
description: Använd Visual Studio-koden för att skapa en ny IoT Edge-enhet i Azure IoT-hubb
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/14/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 7902461f58df1b4fe0c3ed3b577f668fe8be4cc2
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37036531"
---
# <a name="register-a-new-azure-iot-edge-device-from-visual-studio-code"></a>Registrera en ny Azure IoT Edge-enhet från Visual Studio Code

Innan du kan använda din IoT-enheter med Azure IoT gräns, måste du registrera dem med din IoT-hubb. När du registrerar en enhet kan få du en anslutningssträng som kan användas för att konfigurera din enhet för Edge arbetsbelastningar. 

Den här artikeln visar hur du registrerar en ny IoT Edge-enhet med hjälp av Visual Studio-koden (VS). Det finns flera sätt att utföra de flesta åtgärder i VS-kod. Den här artikeln använder Utforskaren, men du kan också använda kommandot paletten för de flesta av steg. 

## <a name="prerequisites"></a>Förutsättningar

* En [IoT-hubb](../iot-hub/iot-hub-create-through-portal.md) i din Azure-prenumeration
* [Visual Studio Code](https://code.visualstudio.com/) 
* [Azure IoT kant-tillägget](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) för Visual Studio Code

## <a name="sign-in-to-access-your-iot-hub"></a>Logga in att få åtkomst till din IoT-hubb

Du kan använda Azure IoT-tillägg för Visual Studio-koden för att utföra åtgärder med IoT-hubben. För dessa åtgärder ska fungera måste du logga in på ditt Azure-konto och välj IoT-hubb som du arbetar med.

1. Öppna i Visual Studio-koden i **Explorer** vyn.

2. Längst ned i Utforskaren, expandera den **Azure IoT Hub-enheter** avsnitt. 

   ![Expandera Azure IoT Hub-enheter](./media/how-to-register-device-vscode/azure-iot-hub-devices.png)

3. Klicka på den **...**  i den **Azure IoT Hub-enheter** avsnittsrubriken. Om du inte ser de tre punkterna hovra över huvudet. 

4. Välj **Välj IoT-hubb**.

5. Följ anvisningarna för att göra det om du inte har loggat in på kontot. 

6. Välj din Azure-prenumeration. 

7. Välj IoT-hubb. 

## <a name="create-a-device"></a>Skapa en enhet

1. VS kod Explorer, expandera den **Azure IoT Hub-enheter** avsnitt. 

2. Klicka på den **...**  i den **Azure IoT Hub-enheter** avsnittsrubriken. Om du inte ser de tre punkterna hovra över huvudet. 

3. Välj **skapa IoT Gränsenheten**. 

4. I textrutan som öppnar ge enheten ett ID. 

På skärmen utdata visas resultatet av kommandot. Enhetsinformationen skrivs ut, som innehåller den **deviceId** som du angav och **connectionString** som du kan använda för att ansluta den fysiska enheten till din IoT-hubb. 

## <a name="view-all-devices"></a>Visa alla enheter

Alla enheter som ansluter till din IoT-hubb visas i den **Azure IoT Hub-enheter** avsnitt i Visual Studio Code Explorer. IoT-gränsenheterna är kan särskiljas från icke-enheter med olika ikoner och det faktum att de kan utökas för att visa de moduler som har distribuerats till varje enhet. 

   ![Visa enheter i VS-kod](./media/how-to-register-device-vscode/view-devices.png)

## <a name="retrieve-the-connection-string"></a>Hämta anslutningssträngen

När du är redo att konfigurera din enhet måste den anslutningssträng som länkar den fysiska enheten med sin identitet i IoT-hubben.

1. Högerklicka på ID för din enhet i den **Azure IoT Hub-enheter** avsnitt. 
2. Välj **kopiera enhet anslutningssträng**.

   Anslutningssträngen kopieras till Urklipp. 

Du kan också välja **hämta enhetsinformation** från snabbmenyn att se alla enhetsinformation, inklusive anslutningssträngen i utdatafönstret. 


## <a name="next-steps"></a>Nästa steg

Lär dig hur du [distribuerar moduler på en enhet med Visual Studio Code](how-to-deploy-modules-vscode.md).
