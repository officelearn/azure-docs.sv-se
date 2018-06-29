---
title: Distribuera Azure IoT kant-moduler (VS-kod) | Microsoft Docs
description: Använd Visual Studio-koden för att distribuera moduler till en IoT-enhet
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/26/2018
ms.topic: conceptual
ms.reviewer: ''
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 7e75f2ff5e2df3189683d084a315ad6c8730be84
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37036357"
---
# <a name="deploy-azure-iot-edge-modules-from-visual-studio-code"></a>Distribuera Azure IoT kant moduler från Visual Studio Code

När du skapar IoT kant moduler med affärslogik som du vill distribuera dem till dina enheter fungerar med gränsen. Om du har flera moduler som arbetar tillsammans för att samla in och bearbeta data kan du distribuera dem på samma gång och deklarera routningsregler som ansluter dem. 

Den här artikeln visar hur du skapar en JSON-distributionsmanifestet och sedan använda den filen för att skicka distributionen till en IoT-enhet. Information om hur du skapar en distribution som riktar sig till flera enheter baserat på deras delade taggar finns [distribuera och övervaka IoT kant moduler i skala](how-to-deploy-monitor.md)

## <a name="prerequisites"></a>Förutsättningar

* En [IoT-hubb](../iot-hub/iot-hub-create-through-portal.md) i din Azure-prenumeration. 
* En [IoT gränsenheten](how-to-register-device-portal.md) med den IoT kant som är installerad. 
* [Visual Studio Code](https://code.visualstudio.com/).
* [Azure IoT kant-tillägget](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) för Visual Studio-koden. 

## <a name="configure-a-deployment-manifest"></a>Konfigurera en distributionsmanifestet

En distributionsmanifestet är en JSON-dokument som beskriver vilka moduler för att distribuera, hur data flödar mellan moduler och önskade egenskaper för modulen twins. Mer information om hur distributionen visar fungerar och hur du skapar dem finns [förstå hur IoT kant moduler kan användas, konfigurerats och återanvändas](module-composition.md).

Om du vill distribuera moduler med Visual Studio Code, spara distributionsmanifestet lokalt som en. JSON-fil. Sökvägen till filen används i nästa avsnitt när du kör kommandot för att tillämpa konfigurationen på enheten.

Här är ett manifest för grundläggande distribution med en modul som exempel:

   ```json
   {
     "moduleContent": {
       "$edgeAgent": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "runtime": {
             "type": "docker",
             "settings": {
               "minDockerVersion": "v1.25",
               "loggingOptions": "",
               "registryCredentials": {
                 "registryName": {
                   "username": "",
                   "password": "",
                   "address": ""
                 }
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
                 "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
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

Du kan använda Azure IoT-tillägg för Visual Studio-koden för att utföra åtgärder med IoT-hubben. För dessa åtgärder ska fungera måste du logga in på ditt Azure-konto och välj IoT-hubb som du arbetar med.

1. Öppna i Visual Studio-koden i **Explorer** vyn.

2. Längst ned i Utforskaren, expandera den **Azure IoT Hub-enheter** avsnitt. 

   ![Expandera Azure IoT Hub-enheter](./media/how-to-deploy-modules-vscode/azure-iot-hub-devices.png)

3. Klicka på den **...**  i den **Azure IoT Hub-enheter** avsnittsrubriken. Om du inte ser de tre punkterna hovra över huvudet. 

4. Välj **Välj IoT-hubb**.

5. Följ anvisningarna för att göra det om du inte har loggat in på kontot. 

6. Välj din Azure-prenumeration. 

7. Välj IoT-hubb. 


## <a name="deploy-to-your-device"></a>Distribuera till enheten

Du kan distribuera moduler till din enhet genom att använda distributionsmanifestet som du konfigurerade med informationen som modulen. 

1. I vyn Visual Studio Code explorer expanderar den **Azure IoT Hub-enheter** avsnitt. 

2. Högerklicka på den enhet som du vill konfigurera med distributionsmanifestet. 

3. Välj **skapa distribution för IoT-enhet**. 

4. Navigera till distribution JSON-manifestfilen som du vill använda och klicka på **Välj kant distribution Manifest**. 

   ![Välj kant Distributionsmanifestet](./media/how-to-deploy-modules-vscode/select-deployment-manifest.png)


Resultatet av distributionen skrivs ut i utdata VS-kod. Lyckad distribution tillämpas inom några minuter om målenheten körs och ansluten till internet. 

## <a name="view-modules-on-your-device"></a>Visa moduler på enheten

När du har distribuerat moduler till din enhet kan du visa dem i den **Azure IoT Hub-enheter** avsnitt. Välj på pilen bredvid enheten IoT kant för att expandera den. Alla moduler som körs för närvarande visas. 

Om du nyligen har distribuerat nya moduler till en enhet hovra över den **Azure IoT Hub-enheter** sidhuvud och välj den här ikonen för att uppdatera vyn. 

Högerklicka på en modul för att visa och redigera modulen dubbla namn. 

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [distribuera och övervaka IoT kant moduler i skala](how-to-deploy-monitor.md)