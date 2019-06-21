---
title: Så här distribuerar du OPC-Twin-modulen för Azure från grunden | Microsoft Docs
description: Så här distribuerar OPC-Twin från grunden.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 41d544fd23d258393cc83ea09371332655223581
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67203934"
---
# <a name="deploy-opc-twin-module-and-dependencies-from-scratch"></a>Distribuera OPC-Twin-modulen och beroenden från grunden

OPC-Twin-modulen körs på IoT Edge och tillhandahåller flera edge OPC enhetstvillingen och registret tjänsterna. 

Det finns flera alternativ för att distribuera moduler till ditt [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) Gateway mellan dem.

- [Distribuera från Azure portal IoT Edge-bladet](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-portal)
- [Distribuera med hjälp av AZ CLI](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-monitor-cli)

> [!NOTE]
> Mer information om distributionsinformation och instruktioner finns i GitHub [databasen](https://github.com/Azure/azure-iiot-components).

## <a name="deployment-manifest"></a>Distributionsmanifest

Alla moduler som distribueras med hjälp av ett manifest för distribution.  Ett exempel manifest för att distribuera både [OPC Publisher](https://github.com/Azure/iot-edge-opc-publisher) och [OPC-Twin](https://github.com/Azure/azure-iiot-opc-twin-module) visas nedan.

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
                "createOptions": "{\"NetworkingConfig\":{\"EndpointsConfig\":{\"host\":{}}},\"HostConfig\":{\"NetworkMode\":\"host\",\"CapAdd\":[\"NET_ADMIN\"]}}"
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

## <a name="deploying-from-azure-portal"></a>Distribuera från Azure-portalen

Det enklaste sättet att distribuera modulerna till en Azure IoT Edge-gatewayenhet är via Azure portal.  

### <a name="prerequisites"></a>Nödvändiga komponenter

1. Distribuera OPC-Twin [beroenden](howto-opc-twin-deploy-dependencies.md) och fick det resulterande `.env` fil. Observera den distribuerade `hub name` av den `PCS_IOTHUBREACT_HUB_NAME` variabel i den resulterande `.env` fil.

2. Registrera dig och börja en [Linux](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) eller [Windows](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-windows) IoT Edge-gateway och anteckna dess `device id`.

### <a name="deploy-to-an-edge-device"></a>Distribuera till en edge-enhet

1. Logga in på den [Azure-portalen](https://portal.azure.com/) och navigera till din IoT-hubb.

2. Välj **IoT Edge** på den vänstra menyn.

3. Klicka på ID för målenhet i listan över enheter.

4. Välj **Ange moduler**.

5. I den **distribution moduler** på sidan, väljer **Lägg till** och **IoT Edge-modul.**

6. I den **IoT Edge-modul för anpassad** dialogrutan Använd `opctwin` som namn för modulen, ange sedan behållaren *bild-URI: N* som

   ```bash
   mcr.microsoft.com/iotedge/opc-twin:latest
   ```

   Som *alternativ för att skapa* använda följande JSON:

   ```json
   {"HostConfig":{"NetworkMode":"host","CapAdd":["NET_ADMIN"]}}
   ```

   Fyll i valfritt fält om det behövs. För mer information om behållare alternativ, omstartsprincip, för att skapa och önskad status finns i [EdgeAgent önskade egenskaper](https://docs.microsoft.com/azure/iot-edge/module-edgeagent-edgehub#edgeagent-desired-properties). Läs mer om modultvillingen [definiera eller uppdatera önskade egenskaper](https://docs.microsoft.com/azure/iot-edge/module-composition#define-or-update-desired-properties).

7. Välj **spara** och upprepar du steg **5**.  

8. I dialogrutan Anpassad IoT Edge-modulen använder `opcpublisher` som namn för modulen och behållaren *bild-URI: N* som 

   ```bash
   mcr.microsoft.com/iotedge/opc-publisher:latest
   ```

   Som *alternativ för att skapa* använda följande JSON:

   ```json
   {"Hostname":"publisher","Cmd":["publisher","--pf=./pn.json","--di=60","--to","--aa","--si=0","--ms=0"],"ExposedPorts":{"62222/tcp":{}},"HostConfig":{"PortBindings":{"62222/tcp":[{"HostPort":"62222"}] }}}
   ```

9. Välj **spara** och sedan **nästa** att fortsätta till avsnittet vägar.

10. Klistra in följande på fliken vägar 

    ```json
    {
      "routes": {
        "opctwinToIoTHub": "FROM /messages/modules/opctwin/outputs/* INTO $upstream",
        "opcpublisherToIoTHub": "FROM /messages/modules/opcpublisher/outputs/* INTO $upstream"
      }
    }
    ```

    Välj **nästa**

11. Granska distributionsinformationen om och manifest.  Det bör se ut som ovan distribution manifestet.  Välj **skicka**.

12. När du har distribuerat modulerna till din enhet kan du visa dem i alla de **enhetsinformation** i portalen. Den här sidan visar namnet på varje distribuerade modulen, samt användbar information som distribution status och avsluta kod.

## <a name="deploying-using-azure-cli"></a>Distribuera med hjälp av Azure CLI

### <a name="prerequisites"></a>Nödvändiga komponenter

1. Installera den senaste versionen av den [Azure kommandoradsgränssnitt (AZ)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) från [här](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

### <a name="quickstart"></a>Snabbstart

1. Spara ovan distribution manifestet i en `deployment.json` fil.  

2. Använd följande kommando för att tillämpa konfigurationen till en IoT Edge-enhet:

   ```bash
   az iot edge set-modules --device-id [device id] --hub-name [hub name] --content ./deployment.json
   ```

   Den `device id` parametern är skiftlägeskänsligt. Innehåll parametern pekar på distributionen manifestfil som du sparade. 
    ![utdata för AZ IoT Edge-set-moduler](https://docs.microsoft.com/azure/iot-edge/media/how-to-deploy-cli/set-modules.png)

3. När du har distribuerat modulerna till din enhet kan visa du alla med följande kommando:

   ```bash
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

   Enhetens ID-parametern är skiftlägeskänsligt. ![AZ iot hub-modul-identity utdata från lista](https://docs.microsoft.com/azure/iot-edge/media/how-to-deploy-cli/list-modules.png)

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du distribuerar OPC-Twin från grunden, är här nästa föreslagna steg:

> [!div class="nextstepaction"]
> [Distribuera OPC-Twin till ett befintligt projekt](howto-opc-twin-deploy-existing.md)