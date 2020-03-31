---
title: Distribuera moduler från Azure CLI-kommandoraden - Azure IoT Edge
description: Använd Azure CLI med Azure IoT-tillägget för att skicka en IoT Edge-modul från din IoT-hubb till din IoT Edge-enhet, som konfigurerats av ett distributionsmanifest.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/16/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 619ba7cb2d99e0137fd1834096dd5b66ffcd6ec9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240385"
---
# <a name="deploy-azure-iot-edge-modules-with-azure-cli"></a>Distribuera Azure IoT Edge-moduler med Azure CLI

När du har skapat IoT Edge-moduler med din affärslogik vill du distribuera dem till dina enheter för att fungera på gränsen. Om du har flera moduler som arbetar tillsammans för att samla in och bearbeta data kan du distribuera dem alla på en gång och deklarera routningsreglerna som ansluter dem.

[Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) är ett kommandoradsverktyg med flera plattformar med öppen källkod för hantering av Azure-resurser som IoT Edge. Det gör att du kan hantera Azure IoT Hub-resurser, enhetsetablering tjänstinstanser och länkade hubbar ur lådan. Det nya IoT-tillägget berikar Azure CLI med funktioner som enhetshantering och fullständig IoT Edge-kapacitet.

Den här artikeln visar hur du skapar ett JSON-distributionsmanifest och sedan använder filen för att skicka distributionen till en IoT Edge-enhet. Information om hur du skapar en distribution som riktar sig till flera enheter baserat på deras delade taggar finns i [Distribuera och övervaka IoT Edge-moduler i stor skala](how-to-deploy-monitor-cli.md)

## <a name="prerequisites"></a>Krav

* En [IoT-hubb](../iot-hub/iot-hub-create-using-cli.md) i din Azure-prenumeration.
* En [IoT Edge-enhet](how-to-register-device.md#register-with-the-azure-cli) med IoT Edge-körningen installerad.
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) i din miljö. Azure CLI-versionen måste vara minst 2.0.70 eller högre. Validera med `az --version`. Den här versionen har stöd för az-tilläggskommandon och introducerar kommandoramverket Knack.
* [IoT-tillägget för Azure CLI](https://github.com/Azure/azure-iot-cli-extension).

## <a name="configure-a-deployment-manifest"></a>Konfigurera ett distributionsmanifest

Ett distributionsmanifest är ett JSON-dokument som beskriver vilka moduler som ska distribueras, hur data flödar mellan modulerna och önskade egenskaper för modultvillingarna. Mer information om hur distributionsmanifest fungerar och hur du skapar dem finns i [Förstå hur IoT Edge-moduler kan användas, konfigureras och återanvändas](module-composition.md).

Om du vill distribuera moduler med Azure CLI sparar du distributionsmanifestet lokalt som en .json-fil. Du kommer att använda filsökvägen i nästa avsnitt när du kör kommandot för att tillämpa konfigurationen på enheten.

Här är ett grundläggande distributionsmanifest med en modul som exempel:

```json
{
  "content": {
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
                "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
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
            "upstream": "FROM /messages/* INTO $upstream"
          },
          "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
          }
        }
      },
      "SimulatedTemperatureSensor": {
        "properties.desired": {
          "SendData": true,
          "SendInterval": 5
        }
      }
    }
  }
}
```

## <a name="deploy-to-your-device"></a>Distribuera till din nya enhet

Du distribuerar moduler till enheten genom att använda distributionsmanifestet som du konfigurerade med modulinformationen.

Ändra kataloger till mappen där distributionsmanifestet sparas. Om du använde en av VS-kod-IoT `deployment.json` Edge-mallarna använder du filen i `deployment.template.json` **konfigurationsmappen** i lösningskatalogen och inte filen.

Använd följande kommando för att tillämpa konfigurationen på en IoT Edge-enhet:

   ```azurecli
   az iot edge set-modules --device-id [device id] --hub-name [hub name] --content [file path]
   ```

Enhets-ID-parametern är skiftlägeskänslig. Innehållsparametern pekar på distributionsmanifestfilen som du sparade.

   ![az iot kant set-moduler utgång](./media/how-to-deploy-cli/set-modules.png)

## <a name="view-modules-on-your-device"></a>Visa moduler på enheten

När du har distribuerat moduler till enheten kan du visa dem alla med följande kommando:

Visa modulerna på din IoT Edge-enhet:

   ```azurecli
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

Enhets-ID-parametern är skiftlägeskänslig.

   ![az iot hub module-identity list output az iot hub module-identity list output az iot hub module-identity list output az i](./media/how-to-deploy-cli/list-modules.png)

## <a name="next-steps"></a>Nästa steg

Lär dig hur [du distribuerar och övervakar IoT Edge-moduler i stor skala](how-to-deploy-monitor.md)
