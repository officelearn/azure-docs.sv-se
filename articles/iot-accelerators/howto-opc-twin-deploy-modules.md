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
ms.openlocfilehash: f470beb79e69b5a4a3febeb6a433c48490b96cf7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61451080"
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
            "restartPolicy": "never",
            "settings": {
              "image": "mcr.microsoft.com/iotedge/opc-twin:latest",
              "createOptions": "{\"HostConfig\": { \"NetworkMode\": \"host\", \"CapAdd\": [\"NET_ADMIN\"] } }"
            }
          },
          "opcpublisher": {
            "version": "2.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "never",
            "settings": {
              "image": "mcr.microsoft.com/iotedge/opc-publisher:latest",
              "createOptions": "{\"Hostname\": \"publisher\", \"Cmd\": [ \"publisher\", \"--pf=./pn.json\", \"--di=60\", \"--to\", \"--aa\", \"--si=0\", \"--ms=0\" ], \"ExposedPorts\": { \"62222/tcp\": {} }, \"HostConfig\": { \"PortBindings\": { \"62222/tcp\": [{ \"HostPort\": \"62222\" }] } } }"
            }
          }
        }
      }
    },
    "$edgeHub": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {
          "opctwinToIoTHub": "FROM /messages/modules/opctwin/outputs/* INTO $upstream",
          "opcpublisherToIoTHub": "FROM /messages/modules/opcpublisher/outputs/* INTO $upstream"
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 7200
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

11. Granska distributionsinformationen om och manifest.  Det bör se ut som ovan distribution manifestet.  Välj **Skicka**.

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

## <a name="run-and-debug-locally"></a>Köra och felsöka lokalt

För problem med att ta och felsökning är användbar för att köra Edge-moduler lokalt med hjälp av den [IoT Edge utveckling Simulator](https://github.com/Azure/iotedgehubdev).  Det ger en lokal utvecklingsmiljö en simulator för att skapa, utveckla, testa, kör och felsöka Azure IoT Edge-moduler och lösningar med hjälp av samma bits/kod som används i produktion.

### <a name="prerequisites"></a>Nödvändiga komponenter

1. Distribuera OPC-Twin [beroenden](howto-opc-twin-deploy-dependencies.md).

2. Installera [Docker CE (18.02.0+)](https://www.docker.com/community-edition) på [Windows](https://docs.docker.com/docker-for-windows/install/), [macOS](https://docs.docker.com/docker-for-mac/install/) eller [Linux](https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-docker-ce).

3. Installera [Docker Compose (1.20.0+)](https://docs.docker.com/compose/install/#install-compose) (krävs endast för **Linux**. Compose redan har inkluderats i Windows/Mac OS Docker CE-installation)

4. Installera [Python (2.7 / 3.5+) och Pip](https://www.python.org/)

5. Installera iotedgehubdev genom att köra nedanstående kommando i terminalen

   ```bash
   pip install --upgrade iotedgehubdev
   ```

> [!NOTE]
> Installera `iotedgehubdev` till **rot** i Linux/Mac OS (*Använd inte ”--användare” i ovanstående installationskommando pip-*).
> Kontrollera att det finns inga Azure IoT Edge-körningen körs på samma dator med iotedgehubdev eftersom de kräver samma portar.

### <a name="quickstart"></a>Snabbstart

1. Följ anvisningarna för att [skapa en Edge-enhet i Azure-portalen](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-portal).  Kopiera anslutningssträngen för edge-enhet.

2. Ställ in simulatorn med edge-anslutningssträng.

    ```bash
    iotedgehubdev setup -c <edge-device-connection-string>
    ```

3. Kopiera ovan manifestet i en `deployment.json` fil i samma mapp.  Starta distributionen i med simulator

    ```bash
    iotedgehubdev start -d deployment.json
    ```

4. Stoppa en simulator med hjälp av

   ```bash
   iotedgehubdev stop
   ```

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du distribuerar OPC-Twin från grunden, är här nästa föreslagna steg:

> [!div class="nextstepaction"]
> [Distribuera OPC-Twin till ett befintligt projekt](howto-opc-twin-deploy-existing.md)