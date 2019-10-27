---
title: Distribuera moduler från kommando raden – Azure IoT Edge | Microsoft Docs
description: Använda IoT-tillägget för Azure CLI för att distribuera moduler till en IoT Edge enhet
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/16/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: c8cd6e2d13e252f9a7560b55eca58341e791db5a
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/27/2019
ms.locfileid: "72964926"
---
# <a name="deploy-azure-iot-edge-modules-with-azure-cli"></a>Distribuera Azure IoT Edge-moduler med Azure CLI

När du skapar IoT Edge-moduler med din affärs logik vill du distribuera dem till dina enheter så att de körs på gränsen. Om du har flera moduler som arbetar tillsammans för att samla in och bearbeta data kan du distribuera dem samtidigt och deklarera de routningsregler som ansluter dem.

[Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) är ett kommando rads verktyg med öppen källkod, plattforms oberoende för hantering av Azure-resurser som IoT Edge. Det gör att du kan hantera Azure IoT Hub-resurser, enhets etablerings tjänst instanser och länkade hubbar direkt. Det nya IoT-tillägget förrikar Azure CLI med funktioner som enhets hantering och fullständig IoT Edge funktion.

Den här artikeln visar hur du skapar ett JSON-distributions manifest och använder sedan filen för att skicka distributionen till en IoT Edge enhet. Information om hur du skapar en distribution som riktar sig till flera enheter baserat på deras delade taggar finns i [distribuera och övervaka IoT Edge moduler i skala](how-to-deploy-monitor-cli.md)

## <a name="prerequisites"></a>Krav

* En [IoT-hubb](../iot-hub/iot-hub-create-using-cli.md) i din Azure-prenumeration.
* En [IoT Edge-enhet](how-to-register-device.md#register-with-the-azure-cli) med IoT Edge runtime installerad.
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) i din miljö. Som minst måste din Azure CLI-version vara 2.0.24 eller högre. Validera med `az --version`. Den här versionen har stöd för az-tilläggskommandon och introducerar kommandoramverket Knack.
* [IoT-tillägget för Azure CLI](https://github.com/Azure/azure-iot-cli-extension).

## <a name="configure-a-deployment-manifest"></a>Konfigurera ett distributions manifest

Ett distributions manifest är ett JSON-dokument som beskriver vilka moduler som ska distribueras, hur data flödar mellan moduler och önskade egenskaper för modulen. Mer information om hur distributions manifest fungerar och hur du skapar dem finns i [förstå hur IoT Edge moduler kan användas, konfigureras och återanvändas](module-composition.md).

Om du vill distribuera moduler med Azure CLI sparar du distributions manifestet lokalt som en JSON-fil. Du kommer att använda fil Sök vägen i nästa avsnitt när du kör kommandot för att tillämpa konfigurationen på enheten.

Här är ett grundläggande distributions manifest med en modul som exempel:

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
               "route": "FROM /* INTO $upstream"
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

## <a name="deploy-to-your-device"></a>Distribuera till din nya enhet

Du distribuerar moduler till din enhet genom att använda distributions manifestet som du konfigurerade med informationen i modulen.

Ändra kataloger till den mapp där distributions manifestet sparas. Om du använde en av VS Code IoT Edge-mallarna använder du `deployment.json`-filen i mappen **config** i din lösnings katalog och inte `deployment.template.json`s filen.

Använd följande kommando för att tillämpa konfigurationen på en IoT Edge enhet:

   ```cli
   az iot edge set-modules --device-id [device id] --hub-name [hub name] --content [file path]
   ```

Parametern för enhets-ID är Skift läges känslig. Innehålls parametern pekar på den distributions manifest fil som du sparade.

   ![AZ IoT Edge set-moduler utdata](./media/how-to-deploy-cli/set-modules.png)

## <a name="view-modules-on-your-device"></a>Visa moduler på enheten

När du har distribuerat moduler till din enhet kan du Visa alla med följande kommando:

Visa modulerna på din IoT Edge-enhet:

   ```cli
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

Parametern för enhets-ID är Skift läges känslig.

   ![AZ IoT Hub-modul – utdata för identitets lista](./media/how-to-deploy-cli/list-modules.png)

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [distribuerar och övervakar IoT Edge moduler i stor skala](how-to-deploy-monitor.md)
