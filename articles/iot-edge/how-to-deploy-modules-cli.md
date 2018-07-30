---
title: Distribuera Azure IoT Edge-moduler (CLI) | Microsoft Docs
description: Använd IoT-tillägget för Azure CLI 2.0 för att distribuera moduler till en IoT Edge-enhet
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 07/27/2018
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 29c11139a2c773db2d26bf44984ad4dc72f2d870
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/27/2018
ms.locfileid: "39324613"
---
# <a name="deploy-azure-iot-edge-modules-with-azure-cli-20"></a>Distribuera Azure IoT Edge-moduler med Azure CLI 2.0

När du skapar IoT Edge moduler med din affärslogik som du vill distribuera dem till dina enheter att fungera på gränsen. Om du har flera moduler som arbetar tillsammans för att samla in och bearbeta data kan du distribuera dem på en gång och deklarera routningsregler som förenar dem. 

[Azure CLI 2.0](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) är ett kommandoradsverktyg med öppen källkod för flera plattformar, för hantering av Azure-resurser som IoT Edge. Det gör det möjligt för dig att hantera Azure IoT Hub-resurser, tjänstinstanser för enhetsetablering och länkade hubbar rutan. Med det nya IoT-tillägget får Azure CLI 2.0 enhetshantering och fullständig IoT Edge-funktionalitet.

Den här artikeln visar hur du skapar ett manifest för distribution av JSON och sedan använda den filen för att skicka distributionen till en IoT Edge-enhet. Information om hur du skapar en distribution som är inriktad på flera enheter baserat på deras delade taggar finns i [distribuera och övervaka IoT Edge-moduler i stor skala](how-to-deploy-monitor-cli.md)

## <a name="prerequisites"></a>Förutsättningar

* En [IoT-hubb](../iot-hub/iot-hub-create-using-cli.md) i Azure-prenumerationen. 
* En [IoT Edge-enhet](how-to-register-device-cli.md) med IoT Edge-körningen installerad.
* [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) i din miljö. Din Azure CLI 2.0-version måste vara 2.0.24 eller senare. Validera med `az –-version`. Den här versionen har stöd för az-tilläggskommandon och introducerar kommandoramverket Knack. 
* Den [IoT-tillägget för Azure CLI 2.0](https://github.com/Azure/azure-iot-cli-extension).

## <a name="configure-a-deployment-manifest"></a>Konfigurera ett manifest för distribution

Ett manifest för distribution är ett JSON-dokument som beskriver vilka moduler för att distribuera, hur data flödar mellan moduler och önskade egenskaper för modultvillingar. Mer information om hur distribution manifest fungerar och hur du skapar dem finns i [förstå hur IoT Edge-moduler kan användas, konfigurerats och återanvändas](module-composition.md).

Om du vill distribuera moduler med Azure CLI 2.0, spara manifestet distribution lokalt som en JSON-fil. Du använder sökvägen till filen i nästa avsnitt när du kör kommandot för att tillämpa konfigurationen på din enhet. 

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

## <a name="deploy-to-your-device"></a>Distribuera till din nya enhet

Du distribuerar moduler på din enhet genom att använda manifestet distribution som du har konfigurerat med informationen som modulen. 

Ändra sökvägen till mappen där ditt manifest för distribution har sparats. Om du använde en av VS Code IoT Edge-mallar, använder den `deployment.json` fil i den **config** mapp på din katalog för lösningen. Använd inte den `deployment.template.json` filen. 

Använd följande kommando för att tillämpa konfigurationen till en IoT Edge-enhet:

   ```cli
   az iot hub apply-configuration --device-id [device id] --hub-name [hub name] --content [file path]
   ```

Enhetens ID-parametern är skiftlägeskänsligt. Innehåll parametern pekar på distributionen manifestfil som du sparade. 

   ![Ange moduler](./media/how-to-deploy-cli/set-modules.png)

## <a name="view-modules-on-your-device"></a>Visa moduler på enheten

När du har distribuerat modulerna till din enhet kan visa du alla med följande kommando: 

Visa modulerna på din IoT Edge-enhet:
    
   ```cli
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

Enhetens ID-parametern är skiftlägeskänsligt.

   ![Lista med moduler](./media/how-to-deploy-cli/list-modules.png)

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [distribuera och övervaka IoT Edge-moduler i stor skala](how-to-deploy-monitor.md)