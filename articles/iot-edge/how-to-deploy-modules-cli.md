---
title: Distribuera Azure IoT kant-moduler (CLI) | Microsoft Docs
description: Använd IoT-tillägget för Azure CLI 2.0 för att distribuera moduler till en IoT-enhet
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/08/2018
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 98a4be02188f7e0462979792a6061d535a64a18d
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37095984"
---
# <a name="deploy-azure-iot-edge-modules-with-azure-cli-20"></a>Distribuera Azure IoT kant moduler med Azure CLI 2.0

När du skapar IoT kant moduler med affärslogik som du vill distribuera dem till dina enheter fungerar med gränsen. Om du har flera moduler som arbetar tillsammans för att samla in och bearbeta data kan du distribuera dem på samma gång och deklarera routningsregler som ansluter dem. 

[Azure CLI 2.0](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) är ett kommandoradsverktyg med öppen källkod för flera plattformar, för hantering av Azure-resurser som IoT Edge. Det kan du hantera Azure IoT Hub resurser enheten etablering tjänstinstanser och länkade hubs direkt. Med det nya IoT-tillägget får Azure CLI 2.0 enhetshantering och fullständig IoT Edge-funktionalitet.

Den här artikeln visar hur du skapar en JSON-distributionsmanifestet och sedan använda den filen för att skicka distributionen till en IoT-enhet. Information om hur du skapar en distribution som riktar sig till flera enheter baserat på deras delade taggar finns [distribuera och övervaka IoT kant moduler i skala](how-to-deploy-monitor-cli.md)

## <a name="prerequisites"></a>Förutsättningar

* En [IoT-hubb](../iot-hub/iot-hub-create-using-cli.md) i din Azure-prenumeration. 
* En [IoT gränsenheten](how-to-register-device-cli.md) med den IoT kant som är installerad.
* [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) i din miljö. Din Azure CLI 2.0-version måste vara 2.0.24 eller senare. Validera med `az –-version`. Den här versionen har stöd för az-tilläggskommandon och introducerar kommandoramverket Knack. 
* Den [IoT-tillägg för Azure CLI 2.0](https://github.com/Azure/azure-iot-cli-extension).

## <a name="configure-a-deployment-manifest"></a>Konfigurera en distributionsmanifestet

En distributionsmanifestet är en JSON-dokument som beskriver vilka moduler för att distribuera, hur data flödar mellan moduler och önskade egenskaper för modulen twins. Mer information om hur distributionen visar fungerar och hur du skapar dem finns [förstå hur IoT kant moduler kan användas, konfigurerats och återanvändas](module-composition.md).

Om du vill distribuera moduler med Azure CLI 2.0, spara distributionsmanifestet lokalt som en txt-fil. Sökvägen till filen används i nästa avsnitt när du kör kommandot för att tillämpa konfigurationen på enheten. 

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

## <a name="deploy-to-your-device"></a>Distribuera till enheten

Du kan distribuera moduler till din enhet genom att använda distributionsmanifestet som du konfigurerade med informationen som modulen. 

Använd följande kommando för att tillämpa konfigurationen på en IoT-enhet:

   ```cli
   az iot hub apply-configuration --device-id [device id] --hub-name [hub name] --content [file path]
   ```

Enheten ID-parametern är skiftlägeskänslig. Innehåll parametern pekar på distributionen manifest-fil som du sparade. 

## <a name="view-modules-on-your-device"></a>Visa moduler på enheten

När du har distribuerat moduler till din enhet kan visa du dem med följande kommando: 

Visa modulerna på din IoT Edge-enhet:
    
   ```cli
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

Enheten ID-parametern är skiftlägeskänslig.

   ![Lista med moduler](./media/how-to-deploy-cli/list-modules.png)

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [distribuera och övervaka IoT kant moduler i skala](how-to-deploy-monitor.md)