---
title: Distribuera moduler från Visual Studio Code-Azure IoT Edge
description: Använd Visual Studio Code med Azure IoT-verktyg för att skicka en IoT Edge modul från din IoT Hub till din IoT Edge enhet, enligt konfigurationen i ett distributions manifest.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/8/2019
ms.topic: conceptual
ms.reviewer: ''
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: d8c90c2a13d111d01a1e7a1bd9b63da180621ded
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75772099"
---
# <a name="deploy-azure-iot-edge-modules-from-visual-studio-code"></a>Distribuera Azure IoT Edge-moduler från Visual Studio Code

När du skapar IoT Edge moduler med din affärslogik som du vill distribuera dem till dina enheter att fungera på gränsen. Om du har flera moduler som arbetar tillsammans för att samla in och bearbeta data kan du distribuera dem på en gång och deklarera routningsregler som förenar dem.

Den här artikeln visar hur du skapar ett manifest för distribution av JSON och sedan använda den filen för att skicka distributionen till en IoT Edge-enhet. Information om hur du skapar en distribution som riktar sig till flera enheter baserat på deras delade taggar finns i [distribuera IoT Edge moduler i skala med Visual Studio Code](how-to-deploy-monitor-vscode.md).

## <a name="prerequisites"></a>Krav

* En [IoT-hubb](../iot-hub/iot-hub-create-through-portal.md) i Azure-prenumerationen.
* En [IoT Edge-enhet](how-to-register-device.md#register-with-visual-studio-code) med IoT Edge-körningen installerad.
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
             "SimulatedTemperatureSensor": {
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
               "route": "FROM /messages/* INTO $upstream"
           },
           "storeAndForwardConfiguration": {
             "timeToLiveSecs": 7200
           }
         }
       },
       "SimulatedTemperatureSensor": {
         "properties.desired": {}
       }
     }
   }
   ```

## <a name="sign-in-to-access-your-iot-hub"></a>Logga in att få åtkomst till din IoT-hubb

Du kan använda Azure IoT-tillägg för Visual Studio Code för att utföra åtgärder med IoT-hubben. För dessa åtgärder ska fungera måste du logga in på ditt Azure-konto och välj IoT-hubben som du arbetar med.

1. I Visual Studio Code, öppna den **Explorer** vy.

1. Expandera avsnittet **Azure IoT Hub** längst ned i Utforskaren.

   ![Avsnittet Expandera Azure IoT Hub](./media/how-to-deploy-modules-vscode/azure-iot-hub-devices.png)

1. Klicka på **...** i avsnitts rubriken för **Azure IoT Hub** . Om du inte ser de tre punkterna, hovrar du över rubriken.

1. Välj **Välj IoT Hub**.

1. Följ anvisningarna för att göra det om du inte har loggat in på kontot.

1. Välj din Azure-prenumeration.

1. Välj din IoT-hubb.

## <a name="deploy-to-your-device"></a>Distribuera till din nya enhet

Du distribuerar moduler på din enhet genom att använda manifestet distribution som du har konfigurerat med informationen som modulen.

1. I vyn Visual Studio Code Explorer expanderar du avsnittet **Azure IoT Hub** och expanderar sedan noden **enheter** .

1. Högerklicka på den IoT Edge enhet som du vill konfigurera med distributions manifestet.

    > [!TIP]
    > För att bekräfta att enheten du har valt är en IoT Edge enhet, väljer du den för att expandera listan över moduler och kontrollerar förekomsten av **$edgeHub** och **$edgeAgent**. Varje IoT Edge enhet innehåller dessa två moduler.

1. Välj **skapa distribution för enskild enhet**.

1. Navigera till den distribution-manifest JSON-fil som du vill använda och klicka på **Välj Edge-distribution Manifest**.

   ![Välj Edge-distribution Manifest](./media/how-to-deploy-modules-vscode/select-deployment-manifest.png)

Resultatet av distributionen skrivs ut i VS Code-utdata. Lyckade distributioner tillämpas inom några minuter om målenheten körs och är ansluten till internet.

## <a name="view-modules-on-your-device"></a>Visa moduler på enheten

När du har distribuerat moduler till din enhet kan du Visa alla i **Azure IoT Hub** -avsnittet. Välj pilen bredvid din IoT Edge-enhet så att det expanderas. Alla pågående moduler visas.

Om du nyligen har distribuerats nya moduler på en enhet, hovra över den **Azure IoT Hub-enheter** huvud och välj uppdateringsikonen för att uppdatera vyn.

Högerklicka på namnet på en modul för att visa och redigera modultvillingen.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [distribuerar och övervakar IoT Edge moduler i skala med Visual Studio Code](how-to-deploy-monitor.md)
