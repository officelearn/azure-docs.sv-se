---
title: Distribuera moduler från kommando raden i Azure CLI – Azure IoT Edge
description: Använd Azure CLI med Azure IoT-tillägget för att skicka en IoT Edge modul från din IoT Hub till din IoT Edge enhet, enligt konfigurationen i ett distributions manifest.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/16/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c63180e77a15c6fc7cbee06ad2eb344b50b97ab7
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75434332"
---
# <a name="deploy-azure-iot-edge-modules-with-azure-cli"></a>Distribuera Azure IoT Edge-moduler med Azure CLI

När du skapar IoT Edge moduler med din affärslogik som du vill distribuera dem till dina enheter att fungera på gränsen. Om du har flera moduler som arbetar tillsammans för att samla in och bearbeta data kan du distribuera dem på en gång och deklarera routningsregler som förenar dem.

[Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) är en öppen källkod för flera plattformar kommandoradsverktyget för att hantera Azure-resurser som IoT Edge. Det gör det möjligt för dig att hantera Azure IoT Hub-resurser, tjänstinstanser för enhetsetablering och länkade hubbar rutan. Det nya IoT-tillägget får Azure CLI med funktioner som enhetshantering och full IoT Edge-funktion.

Den här artikeln visar hur du skapar ett manifest för distribution av JSON och sedan använda den filen för att skicka distributionen till en IoT Edge-enhet. Information om hur du skapar en distribution som är inriktad på flera enheter baserat på deras delade taggar finns i [distribuera och övervaka IoT Edge-moduler i stor skala](how-to-deploy-monitor-cli.md)

## <a name="prerequisites"></a>Krav

* En [IoT-hubb](../iot-hub/iot-hub-create-using-cli.md) i Azure-prenumerationen.
* En [IoT Edge-enhet](how-to-register-device.md#register-with-the-azure-cli) med IoT Edge-körningen installerad.
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) i din miljö. Azure CLI-version måste minst vara 2.0.24 eller senare. Validera med `az --version`. Den här versionen har stöd för az-tilläggskommandon och introducerar kommandoramverket Knack.
* Den [IoT-tillägget för Azure CLI](https://github.com/Azure/azure-iot-cli-extension).

## <a name="configure-a-deployment-manifest"></a>Konfigurera ett manifest för distribution

Ett manifest för distribution är ett JSON-dokument som beskriver vilka moduler för att distribuera, hur data flödar mellan moduler och önskade egenskaper för modultvillingar. Mer information om hur distribution manifest fungerar och hur du skapar dem finns i [förstå hur IoT Edge-moduler kan användas, konfigurerats och återanvändas](module-composition.md).

Om du vill distribuera moduler med Azure CLI, spara manifestet distribution lokalt som en JSON-fil. Du använder sökvägen till filen i nästa avsnitt när du kör kommandot för att tillämpa konfigurationen på din enhet.

Här är ett manifest för grundläggande distribution med en modul som exempel:

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

Du distribuerar moduler på din enhet genom att använda manifestet distribution som du har konfigurerat med informationen som modulen.

Ändra sökvägen till mappen där ditt manifest för distribution har sparats. Om du använde en av VS Code IoT Edge-mallarna använder du `deployment.json`-filen i mappen **config** i din lösnings katalog och inte `deployment.template.json`s filen.

Använd följande kommando för att tillämpa konfigurationen till en IoT Edge-enhet:

   ```cli
   az iot edge set-modules --device-id [device id] --hub-name [hub name] --content [file path]
   ```

Parametern för enhets-ID är Skift läges känslig. Innehåll parametern pekar på distributionen manifestfil som du sparade.

   ![AZ iot edge set-moduler utdata](./media/how-to-deploy-cli/set-modules.png)

## <a name="view-modules-on-your-device"></a>Visa moduler på enheten

När du har distribuerat modulerna till din enhet kan visa du alla med följande kommando:

Visa modulerna på din IoT Edge-enhet:

   ```cli
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

Parametern för enhets-ID är Skift läges känslig.

   ![AZ iot hub-modul-identity utdata från lista](./media/how-to-deploy-cli/list-modules.png)

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [distribuera och övervaka IoT Edge-moduler i stor skala](how-to-deploy-monitor.md)
