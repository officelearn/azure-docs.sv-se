---
title: Distribuera moduler från Visual Studio Code - Azure IoT Edge | Microsoft Docs
description: Använd Visual Studio Code för att distribuera moduler till en IoT Edge-enhet
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 01/09/2019
ms.topic: conceptual
ms.reviewer: ''
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 91a074cf98291b105864a69730314efff3482254
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62126417"
---
# <a name="deploy-azure-iot-edge-modules-from-visual-studio-code"></a>Distribuera Azure IoT Edge-moduler från Visual Studio Code

När du skapar IoT Edge moduler med din affärslogik som du vill distribuera dem till dina enheter att fungera på gränsen. Om du har flera moduler som arbetar tillsammans för att samla in och bearbeta data kan du distribuera dem på en gång och deklarera routningsregler som förenar dem. 

Den här artikeln visar hur du skapar ett manifest för distribution av JSON och sedan använda den filen för att skicka distributionen till en IoT Edge-enhet. Information om hur du skapar en distribution som är inriktad på flera enheter baserat på deras delade taggar finns i [distribuera och övervaka IoT Edge-moduler i stor skala](how-to-deploy-monitor.md)

## <a name="prerequisites"></a>Förutsättningar

* En [IoT-hubb](../iot-hub/iot-hub-create-through-portal.md) i Azure-prenumerationen. 
* En [IoT Edge-enhet](how-to-register-device-portal.md) med IoT Edge-körningen installerad. 
* [Visual Studio Code](https://code.visualstudio.com/).
* [Azure IoT-verktyg](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools#overview) för Visual Studio Code.

## <a name="configure-a-deployment-manifest"></a>Konfigurera ett manifest för distribution

Ett manifest för distribution är ett JSON-dokument som beskriver vilka moduler för att distribuera, hur data flödar mellan moduler och önskade egenskaper för modultvillingar. Mer information om hur distribution manifest fungerar och hur du skapar dem finns i [förstå hur IoT Edge-moduler kan användas, konfigurerats och återanvändas](module-composition.md).

Om du vill distribuera moduler med Visual Studio Code, spara distribution manifestet arbeta lokalt som en. JSON-fil. Du använder sökvägen till filen i nästa avsnitt när du kör kommandot för att tillämpa konfigurationen på din enhet.

Här är ett manifest för grundläggande distribution med en modul som exempel:

   ```json
   {
     "modulesContent": {
       "$edgeAgent": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "runtime": {
             "type": "docker",
             "settings": {
               "minDockerVersion": "v1.25",
               "loggingOptions": "",
               "registryCredentials": {}
             }
           },
           "systemModules": {
             "edgeAgent": {
               "type": "docker",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
                 "createOptions": "{}"
               }
             },
             "edgeHub": {
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
                 "createOptions": "{}"
               }
             }
           },
           "modules": {
             "tempSensor": {
               "version": "1.0",
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
                 "createOptions": "{}"
               }
             }
           }
         }
       },
       "$edgeHub": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "routes": {
               "route": "FROM /* INTO $upstream"
           },
           "storeAndForwardConfiguration": {
             "timeToLiveSecs": 7200
           }
         }
       },
       "tempSensor": {
         "properties.desired": {}
       }
     }
   }
   ```
   
## <a name="sign-in-to-access-your-iot-hub"></a>Logga in att få åtkomst till din IoT-hubb

Du kan använda Azure IoT-tillägg för Visual Studio Code för att utföra åtgärder med IoT-hubben. För dessa åtgärder ska fungera måste du logga in på ditt Azure-konto och välj IoT-hubben som du arbetar med.

1. I Visual Studio Code, öppna den **Explorer** vy.

2. Längst ned i Utforskaren, expandera den **Azure IoT Hub-enheter** avsnittet. 

   ![Expandera avsnittet Azure IoT Hub-enheter](./media/how-to-deploy-modules-vscode/azure-iot-hub-devices.png)

3. Klicka på den **...**  i den **Azure IoT Hub-enheter** avsnittsrubrik. Om du inte ser de tre punkterna, hovrar du över rubriken. 

4. Välj **Välj IoT Hub**.

5. Följ anvisningarna för att göra det om du inte har loggat in på kontot. 

6. Välj din Azure-prenumeration. 

7. Välj din IoT-hubb. 


## <a name="deploy-to-your-device"></a>Distribuera till din nya enhet

Du distribuerar moduler på din enhet genom att använda manifestet distribution som du har konfigurerat med informationen som modulen. 

1. I vyn Visual Studio Code-Utforskaren expanderar den **Azure IoT Hub-enheter** avsnittet. 

1. Högerklicka på IoT Edge-enhet som du vill konfigurera med distribution manifestet.

    > [!TIP]
    > För att bekräfta att den enhet som du har valt är en IoT Edge-enhet, väljer du den för att expandera listan över moduler och kontrollera förekomst av **$edgeHub** och **$edgeAgent**. Varje IoT Edge-enhet omfattar dessa två moduler.

1. Välj **skapa distribution för enskild enhet**.

4. Navigera till den distribution-manifest JSON-fil som du vill använda och klicka på **Välj Edge-distribution Manifest**. 

   ![Välj Edge-distribution Manifest](./media/how-to-deploy-modules-vscode/select-deployment-manifest.png)


Resultatet av distributionen skrivs ut i VS Code-utdata. Lyckade distributioner tillämpas inom några minuter om målenheten körs och är ansluten till internet. 

## <a name="view-modules-on-your-device"></a>Visa moduler på enheten

När du har distribuerat modulerna till din enhet kan du visa dem i alla de **Azure IoT Hub-enheter** avsnittet. Välj pilen bredvid din IoT Edge-enhet så att det expanderas. Alla pågående moduler visas. 

Om du nyligen har distribuerats nya moduler på en enhet, hovra över den **Azure IoT Hub-enheter** huvud och välj uppdateringsikonen för att uppdatera vyn. 

Högerklicka på namnet på en modul för att visa och redigera modultvillingen. 

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [distribuera och övervaka IoT Edge-moduler i stor skala](how-to-deploy-monitor.md)