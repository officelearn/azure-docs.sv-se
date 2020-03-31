---
title: Distribuera moduler i stor skala med Visual Studio Code - Azure IoT Edge
description: Använd IoT-tillägget för Visual Studio-kod för att skapa automatiska distributioner för grupper av IoT Edge-enheter.
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 1/8/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 08299a589dc6e8f768cba7ef976e109ef1fb69d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774138"
---
# <a name="deploy-iot-edge-modules-at-scale-using-visual-studio-code"></a>Distribuera IoT Edge-moduler i stor skala med Visual Studio-kod

Du kan skapa en **automatisk distribution i IoT Edge** med Visual Studio-kod för att hantera pågående distributioner för många enheter samtidigt. Automatiska distributioner för IoT Edge är en del av den [automatiska enhetshanteringsfunktionen](/azure/iot-hub/iot-hub-automatic-device-management) i IoT Hub. Distributioner är dynamiska processer som gör att du kan distribuera flera moduler till flera enheter. Du kan också spåra status och hälsa för modulerna och göra ändringar vid behov.

Mer information finns i [Förstå IoT Edge automatiska distributioner för enskilda enheter eller i stor skala](module-deployment-monitoring.md).

I den här artikeln ställer du in Visual Studio Code och IoT-tillägget. Du lär dig sedan hur du distribuerar moduler till en uppsättning IoT Edge-enheter.

## <a name="prerequisites"></a>Krav

* En [IoT-hubb](../iot-hub/iot-hub-create-through-portal.md) i din Azure-prenumeration.
* En [IoT Edge-enhet](how-to-register-device.md#register-with-visual-studio-code) med IoT Edge-körningen installerad.
* [Visual Studio Code](https://code.visualstudio.com/).
* [Azure IoT-verktyg](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools#overview) för Visual Studio-kod.

## <a name="sign-in-to-access-your-iot-hub"></a>Logga in för att komma åt din IoT-hubb

Du kan använda Azure IoT-tilläggen för Visual Studio-kod för att utföra åtgärder med din Hub. För att dessa åtgärder ska fungera måste du logga in på ditt Azure-konto och välja den IoT-hubb som du arbetar med.

1. Öppna **Utforskarvyn** i Visual Studio-kod.

1. Expandera avsnittet **Azure IoT Hub** längst ned i Utforskaren.

1. Klicka på **...** i avsnittshuvudet för **Azure IoT Hub.** Om du inte ser ellipsen håller du muspekaren över huvudet.

1. Välj **Välj IoT Hub**.

1. Om du inte är inloggad på ditt Azure-konto följer du anvisningarna för att göra det.

1. Välj din Azure-prenumeration.

1. Välj din IoT-hubb.

## <a name="configure-a-deployment-manifest"></a>Konfigurera ett distributionsmanifest

Ett distributionsmanifest är ett JSON-dokument som beskriver vilka moduler som ska distribueras. Den beskriver också hur data flödar mellan modulerna och önskade egenskaper hos modultvillingarna. Mer information finns i [Lär dig hur du distribuerar moduler och upprättar vägar i IoT Edge](module-composition.md).

Om du vill distribuera moduler med Visual Studio-kod sparar du distributionsmanifestet lokalt som en . JSON-fil. Du måste ange dess plats när du kör kommandot för att tillämpa konfigurationen på enheten.

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

Om du behöver bestämma vilka IoT Edge-enheter du kan konfigurera kör du kommandot **IoT Edge: Get Device Info.**

## <a name="identify-devices-with-target-conditions"></a>Identifiera enheter med målvillkor

Om du vill identifiera de IoT Edge-enheter som ska ta emot distributionen måste du ange ett målvillkor. Ett målvillkor uppfylls när angivna villkor matchas av ett deviceId- eller taggvärde eller ett rapporterat egenskapsvärde.

Du konfigurerar taggar i enhetstvillingen. Här är ett exempel på en enhetstvilling som har taggar:

```json
"tags":{
  "location":{
    "building": "20",
    "floor": "2"
  },
  "roomtype": "conference",
  "environment": "prod"
}
```

Den här enheten får en distribution om målvillkoret för distributionen innehåller ett uttryck `tag.location.building = '20'`som matchar ett av taggens värden, till exempel .

Om du vill rikta in dig på en viss enhet `deviceId` oavsett taggar eller andra värden anger du bara för målvillkoret.

Här är några fler exempel:

* deviceId ='linuxprod1'
* deviceId = "linuxprod1" ELLER deviceId = 'linuxprod2' ELLER deviceId = 'linuxprod3'
* tags.environment ='prod'
* tags.environment = "prod" OCH tags.location = 'westus2'
* tags.environment = "prod" ELLER tags.location = 'westus2'
* tags.operator = "John" OCH tags.environment = "prod" OCH INTE deviceId = 'linuxprod1'

Se [målvillkor](module-deployment-monitoring.md#target-condition) för mer information. Mer information om enhetstvillingar och -taggar finns i [Förstå och använda enhetstvillingar i IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).

### <a name="edit-the-device-twin"></a>Redigera enhetstvillingen

Du kan redigera enhetstvillingen i Visual Studio-kod för att konfigurera taggarna. På **Visa-menyn** väljer du **Kommandopalett** och kör kommandot **IoT Edge: Edit Device Twin.** Välj din IoT Edge-enhet så visas enhetstvillingen.

I det här exemplet har inga taggar definierats. Ersätt det aktuella tomma avsnittet `"tags": {}` med din egen taggdefinition.

```json
{
    "deviceId": "myEdgeDevice",
    "etag": "AAAAAAAAAAE=",
    "deviceEtag": "NTgwMDg5MDAz",
    "status": "enabled",
    "statusUpdateTime": "0001-01-01T00:00:00Z",
    "connectionState": "Disconnected",
    "lastActivityTime": "0001-01-01T00:00:00Z",
    "cloudToDeviceMessageCount": 0,
    "authenticationType": "sas",
    "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
    },
    "version": 2,
    "properties": {
        "desired": {
            "$metadata": {
                "$lastUpdated": "2019-12-29T00:58:49.9315265Z"
            },
            "$version": 1
        },
        "reported": {
            "$metadata": {
                "$lastUpdated": "2019-12-29T00:58:49.9315265Z"
            },
            "$version": 1
        }
    },
    "capabilities": {
        "iotEdge": true
    },
    "deviceScope": "ms-azure-iot-edge://myEdgeDevice-637131779299315265",
    "tags": {}
}
```

När du har sparat den lokala filen kör du kommandot **IoT Edge: Update Device Twin.**

## <a name="create-deployment-at-scale"></a>Skapa distribution i stor skala

När du har konfigurerat distributionsmanifestet och konfigurerade taggar i enhetstvillingen är du redo att distribuera.

1. Välj **Kommandopalett** på **Visa-menyn** och välj kommandot **Azure IoT Edge: Create Deployment at Scale.**

1. Navigera till den JSON-fil för distributionsmanifest som du vill använda och klicka på **Välj edge-distributionsmanifest**.

1. Ange värden enligt uppmaning, med början i **distributions-ID**.

   ![Ange ett distributions-ID](./media/how-to-deploy-monitor-vscode/create-deployment-at-scale.png)

   Ange värden för dessa parametrar:

  | Parameter | Beskrivning |
  | --- | --- |
  | Distribution id | Namnet på distributionen som ska skapas i IoT-hubben. Ge distributionen ett unikt namn som är upp till 128 gemener. Undvik blanksteg och följande `& ^ [ ] { } \ | " < > /`ogiltiga tecken: . |
  | Målvillkor | Ange ett målvillkor för att avgöra vilka enheter som ska riktas med den här distributionen.Villkoret baseras på enhetstvillingtaggar eller enhetstvillingrapporterade egenskaper och bör matcha uttrycksformatet.Till exempel `tags.environment='test' and properties.reported.devicemodel='4000x'`. |
  | Prioritet |  Ett positivt heltal. Om två eller flera distributioner är inriktade på samma enhet gäller distributionen med det högsta numeriska värdet för Prioritet. |

  När du har angett prioriteten ska terminalen visa utdata som liknar följande avbildning:

   ```cmd
   [Edge] Start deployment with deployment id [{specified-value}] and target condition [{specified-value}]
   [Edge] Deployment with deployment id [{specified-value}] succeeded.
   ```

## <a name="monitoring-and-modifying-deployments"></a>Övervaka och ändra distributioner

Använd [Azure CLI](how-to-deploy-monitor-cli.md#monitor-a-deployment) eller [Azure-portalen](how-to-deploy-monitor.md#monitor-a-deployment) för att övervaka, ändra och ta bort distributioner. Båda ger mått på dina distributioner.

## <a name="next-steps"></a>Nästa steg

Läs mer om [att distribuera moduler till IoT Edge-enheter](module-deployment-monitoring.md).
