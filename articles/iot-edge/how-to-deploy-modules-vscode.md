---
title: Distribuera moduler från Visual Studio Code - Azure IoT Edge
description: Använd Visual Studio-kod med Azure IoT-verktyg för att skicka en IoT Edge-modul från din IoT-hubb till din IoT Edge-enhet, som konfigurerats av ett distributionsmanifest.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/8/2019
ms.topic: conceptual
ms.reviewer: ''
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: e1b2e2a80670cf0409f8f8477563b9a209cc8706
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77209213"
---
# <a name="deploy-azure-iot-edge-modules-from-visual-studio-code"></a>Distribuera Azure IoT Edge-moduler från Visual Studio-kod

När du har skapat IoT Edge-moduler med din affärslogik vill du distribuera dem till dina enheter för att fungera på gränsen. Om du har flera moduler som arbetar tillsammans för att samla in och bearbeta data kan du distribuera dem alla på en gång och deklarera routningsreglerna som ansluter dem.

Den här artikeln visar hur du skapar ett JSON-distributionsmanifest och sedan använder filen för att skicka distributionen till en IoT Edge-enhet. Information om hur du skapar en distribution som riktar sig till flera enheter baserat på deras delade taggar finns i [Distribuera IoT Edge-moduler i stor skala med Visual Studio Code](how-to-deploy-monitor-vscode.md).

## <a name="prerequisites"></a>Krav

* En [IoT-hubb](../iot-hub/iot-hub-create-through-portal.md) i din Azure-prenumeration.
* En [IoT Edge-enhet](how-to-register-device.md#register-with-visual-studio-code) med IoT Edge-körningen installerad.
* [Visual Studio Code](https://code.visualstudio.com/).
* [Azure IoT-verktyg](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools#overview) för Visual Studio-kod.

## <a name="configure-a-deployment-manifest"></a>Konfigurera ett distributionsmanifest

Ett distributionsmanifest är ett JSON-dokument som beskriver vilka moduler som ska distribueras, hur data flödar mellan modulerna och önskade egenskaper för modultvillingarna. Mer information om hur distributionsmanifest fungerar och hur du skapar dem finns i [Förstå hur IoT Edge-moduler kan användas, konfigureras och återanvändas](module-composition.md).

Om du vill distribuera moduler med Visual Studio-kod sparar du distributionsmanifestet lokalt som en . JSON-fil. Du kommer att använda filsökvägen i nästa avsnitt när du kör kommandot för att tillämpa konfigurationen på enheten.

Här är ett grundläggande distributionsmanifest med en modul som exempel:

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
                 "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
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

## <a name="sign-in-to-access-your-iot-hub"></a>Logga in för att komma åt din IoT-hubb

Du kan använda Azure IoT-tilläggen för Visual Studio-kod för att utföra åtgärder med din IoT-hubb. För att dessa åtgärder ska fungera måste du logga in på ditt Azure-konto och välja den IoT-hubb som du arbetar med.

1. Öppna **Utforskarvyn** i Visual Studio-kod.

1. Expandera avsnittet **Azure IoT Hub** längst ned i Utforskaren.

   ![Expandera avsnittet Azure IoT Hub](./media/how-to-deploy-modules-vscode/azure-iot-hub-devices.png)

1. Klicka på **...** i avsnittshuvudet för **Azure IoT Hub.** Om du inte ser ellipsen håller du muspekaren över huvudet.

1. Välj **Välj IoT Hub**.

1. Om du inte är inloggad på ditt Azure-konto följer du anvisningarna för att göra det.

1. Välj din Azure-prenumeration.

1. Välj din IoT-hubb.

## <a name="deploy-to-your-device"></a>Distribuera till din nya enhet

Du distribuerar moduler till enheten genom att använda distributionsmanifestet som du konfigurerade med modulinformationen.

1. Expandera avsnittet **Azure IoT Hub** i utforskaren i Utforskaren i Visual Studio-kod och expandera sedan noden **Enheter.**

1. Högerklicka på IoT Edge-enheten som du vill konfigurera med distributionsmanifestet.

    > [!TIP]
    > Om du vill bekräfta att enheten du har valt är en IoT Edge-enhet markerar du den för att expandera listan över moduler och verifiera förekomsten av **$edgeHub** och **$edgeAgent**. Varje IoT Edge-enhet innehåller dessa två moduler.

1. Välj **Skapa distribution för en enhet**.

1. Navigera till den JSON-fil för distributionsmanifest som du vill använda och klicka på **Välj edge-distributionsmanifest**.

   ![Välj edge-distributionsmanifest](./media/how-to-deploy-modules-vscode/select-deployment-manifest.png)

Resultatet av distributionen skrivs ut i VS-kodutdata. Lyckade distributioner tillämpas inom några minuter om målenheten körs och är ansluten till internet.

## <a name="view-modules-on-your-device"></a>Visa moduler på enheten

När du har distribuerat moduler till din enhet kan du visa dem alla i avsnittet **Azure IoT Hub.** Markera pilen bredvid IoT Edge-enheten för att expandera den. Alla moduler som körs för närvarande visas.

Om du nyligen har distribuerat nya moduler till en enhet håller du muspekaren över avsnittshuvudet för **Azure IoT Hub Devices** och väljer uppdateringsikonen för att uppdatera vyn.

Högerklicka på namnet på en modul för att visa och redigera modultvillingen.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [distribuerar och övervakar IoT Edge-moduler i stor skala med Visual Studio-kod](how-to-deploy-monitor.md)
