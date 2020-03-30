---
title: Så här distribuerar du OPC Twin-modul för Azure från grunden | Microsoft-dokument
description: I den här artikeln beskrivs hur du distribuerar OPC Twin från grunden med hjälp av Azure-portalens IoT Edge-blad och även med AZ CLI.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 6c8ceeaf49d8ebfa15a83118e8b518190f6ff85e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241066"
---
# <a name="deploy-opc-twin-module-and-dependencies-from-scratch"></a>Distribuera OPC Twin-modul och beroenden från grunden

OPC Twin-modulen körs på IoT Edge och tillhandahåller flera edge-tjänster till OPC-enhetens tvilling- och registertjänster. 

Det finns flera alternativ för att distribuera moduler till din [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) Gateway, bland annat

- [Distribuera från Azure-portalens IoT Edge-blad](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-portal)
- [Distribuera med AZ CLI](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-monitor-cli)

> [!NOTE]
> Mer information om distributionsinformation och instruktioner finns i [GitHub-databasen](https://github.com/Azure/azure-iiot-components).

## <a name="deployment-manifest"></a>Distributionsmanifest

Alla moduler distribueras med hjälp av ett distributionsmanifest.  Ett exempelmanifest för distribution av både [OPC Publisher](https://github.com/Azure/iot-edge-opc-publisher) och [OPC Twin](https://github.com/Azure/azure-iiot-opc-twin-module) visas nedan.

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
                "createOptions": ""
              }
            },
            "edgeHub": {
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
                "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}], \"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
              }
            }
          },
          "modules": {
            "opctwin": {
              "version": "1.0",
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/iotedge/opc-twin:latest",
                "createOptions": "{\"NetworkingConfig\": {\"EndpointsConfig\": {\"host\": {}}}, \"HostConfig\": {\"NetworkMode\": \"host\" }}"
              }
            },
            "opcpublisher": {
              "version": "2.0",
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/iotedge/opc-publisher:latest",
                "createOptions": "{\"Hostname\":\"publisher\",\"Cmd\":[\"publisher\",\"--pf=./pn.json\",\"--di=60\",\"--to\",\"--aa\",\"--si=0\",\"--ms=0\"],\"ExposedPorts\":{\"62222/tcp\":{}},\"NetworkingConfig\":{\"EndpointsConfig\":{\"host\":{}}},\"HostConfig\":{\"NetworkMode\":\"host\",\"PortBindings\":{\"62222/tcp\":[{\"HostPort\":\"62222\"}]}}}"
              }
            }
          }
        }
      },
      "$edgeHub": {
        "properties.desired": {
          "schemaVersion": "1.0",
          "routes": {
            "opctwinToIoTHub": "FROM /messages/modules/opctwin/* INTO $upstream",
            "opcpublisherToIoTHub": "FROM /messages/modules/opcpublisher/* INTO $upstream"
          },
          "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
          }
        }
      }
    }
  }
}
```

## <a name="deploying-from-azure-portal"></a>Distribuera från Azure Portal

Det enklaste sättet att distribuera modulerna till en Azure IoT Edge gateway-enhet är via Azure-portalen.  

### <a name="prerequisites"></a>Krav

1. Distribuera OPC [Twin-beroenden](howto-opc-twin-deploy-dependencies.md) och `.env` hämta den resulterande filen. Observera den `hub name` distribuerade `PCS_IOTHUBREACT_HUB_NAME` variabeln `.env` i den resulterande filen.

2. Registrera och starta en [Linux-](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) eller [Windows](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-windows) `device id`IoT Edge-gateway och notera dess .

### <a name="deploy-to-an-edge-device"></a>Distribuera till en kantenhet

1. Logga in på [Azure-portalen](https://portal.azure.com/) och navigera till din IoT-hubb.

2. Välj **IoT Edge** på menyn till vänster.

3. Klicka på målenhetens ID från listan över enheter.

4. Välj **Ange moduler**.

5. I avsnittet **Distributionsmoduler** på sidan väljer du **Lägg till** och **IoT Edge Module.**

6. I dialogrutan **IoT Edge** `opctwin` Custom Module används som namn för modulen och ange sedan behållaren *Image URI* som

   ```bash
   mcr.microsoft.com/iotedge/opc-twin:latest
   ```

   Använd följande JSON som *alternativ för att skapa behållare:*

   ```json
   {"NetworkingConfig": {"EndpointsConfig": {"host": {}}}, "HostConfig": {"NetworkMode": "host" }}
   ```

   Fyll i de valfria fälten om det behövs. Mer information om alternativ för att skapa behållare, starta om principen och önskad status finns i [Önskade egenskaper för EdgeAgent](https://docs.microsoft.com/azure/iot-edge/module-edgeagent-edgehub#edgeagent-desired-properties). Mer information om modultvillingen finns [i Definiera eller uppdatera önskade egenskaper](https://docs.microsoft.com/azure/iot-edge/module-composition#define-or-update-desired-properties).

7. Välj **Spara** och upprepa steg **5**.  

8. I dialogrutan IoT Edge Custom `opcpublisher` Module använder du som namn för modulen och behållaravbildningen *URI* som 

   ```bash
   mcr.microsoft.com/iotedge/opc-publisher:latest
   ```

   Använd följande JSON som *alternativ för att skapa behållare:*

   ```json
   {"Hostname":"publisher","Cmd":["publisher","--pf=./pn.json","--di=60","--to","--aa","--si=0","--ms=0"],"ExposedPorts":{"62222/tcp":{}},"HostConfig":{"PortBindings":{"62222/tcp":[{"HostPort":"62222"}] }}}
   ```

9. Välj **Spara** och sedan **Nästa** om du vill fortsätta till flödesavsnittet.

10. Klistra in följande på fliken rutter 

    ```json
    {
      "routes": {
        "opctwinToIoTHub": "FROM /messages/modules/opctwin/* INTO $upstream",
        "opcpublisherToIoTHub": "FROM /messages/modules/opcpublisher/* INTO $upstream"
      }
    }
    ```

    och välj **Nästa**

11. Granska distributionsinformationen och manifestet.  Det bör se ut som ovanstående distributionsmanifest.  Välj **Skicka**.

12. När du har distribuerat moduler till enheten kan du visa dem alla på sidan **Enhetsinformation** på portalen. På den här sidan visas namnet på varje distribuerad modul samt användbar information som distributionsstatus och avslutningskod.

## <a name="deploying-using-azure-cli"></a>Distribuera med Azure CLI

### <a name="prerequisites"></a>Krav

1. Installera den senaste versionen av [Azure-kommandoradsgränssnittet (AZ)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) [härifrån](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

### <a name="quickstart"></a>Snabbstart

1. Spara distributionsmanifestet `deployment.json` ovan i en fil.  

2. Använd följande kommando för att tillämpa konfigurationen på en IoT Edge-enhet:

   ```azurecli
   az iot edge set-modules --device-id [device id] --hub-name [hub name] --content ./deployment.json
   ```

   Parametern `device id` är skiftlägeskänslig. Innehållsparametern pekar på distributionsmanifestfilen som du sparade. 
    ![az IoT Edge-setmoduler utdata](https://docs.microsoft.com/azure/iot-edge/media/how-to-deploy-cli/set-modules.png)

3. När du har distribuerat moduler till enheten kan du visa dem alla med följande kommando:

   ```azurecli
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

   Enhets-ID-parametern är skiftlägeskänslig. ![az iot hub module-identity list output az iot hub module-identity list output az iot hub module-identity list output az i](https://docs.microsoft.com/azure/iot-edge/media/how-to-deploy-cli/list-modules.png)

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig att distribuera OPC Twin från grunden, här är det föreslagna nästa steg:

> [!div class="nextstepaction"]
> [Distribuera OPC Twin till ett befintligt projekt](howto-opc-twin-deploy-existing.md)
