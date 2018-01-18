---
title: "Distribuera moduler till IoT-gränsenheterna med IoT-tillägg för Azure CLI 2.0 | Microsoft Docs"
description: "Distribuera moduler till en insticksenhet för IoT som använder IoT-tillägg för Azure CLI 2.0"
services: iot-edge
keywords: 
author: chrissie926
manager: timlt
ms.author: menchi
ms.date: 01/11/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 26067187864f9a2a4c85c953ae8aca888458d245
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/18/2018
---
# <a name="deploy-modules-to-an-iot-edge-device-using-iot-extension-for-azure-cli-20"></a>Distribuera moduler till en insticksenhet för IoT som använder IoT-tillägg för Azure CLI 2.0

[Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/overview?view=azure-cli-latest) är en öppen källkod mellan plattform kommandoradsverktyg för att hantera Azure-resurser som IoT kant. Azure CLI 2.0 är tillgänglig på Windows, Linux och MacOS.

Azure CLI 2.0 kan du hantera Azure IoT Hub resurser enheten etablering tjänstinstanser och länkade hubs direkt. Det nya tillägget IoT förbättra Azure CLI 2.0 med funktioner som enhetshantering och kapaciteten för fullständig IoT kant.

I kursen får slutföra du först stegen för att konfigurera Azure CLI 2.0- och IoT-tillägget. Sedan får du lära dig hur du distribuerar moduler till en gräns för IoT-enhet med hjälp av de tillgängliga CLI-kommandona.

## <a name="installation"></a>Installation 

### <a name="step-1---install-python"></a>Steg 1 – Installera Python

[Python 2.7 x eller Python 3.x](https://www.python.org/downloads/) krävs.

### <a name="step-2---install-azure-cli-20"></a>Steg 2 – installera Azure CLI 2.0

Följ den [installationsinstruktion](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) att konfigurera Azure CLI 2.0 i din miljö. Minst Azure CLI 2.0 måste du ha 2.0.24 eller senare. Använd `az –version` att validera. Den här versionen stöder az tillägget kommandon och introducerar Knack kommandot ramen. Ett enkelt sätt att installera på Windows är att hämta och installera den [MSI](https://aka.ms/InstallAzureCliWindows).

### <a name="step-3---install-iot-extension"></a>Steg 3 – installation IoT-tillägg

[IoT-tillägget viktigt](https://github.com/Azure/azure-iot-cli-extension) beskrivs flera olika sätt att installera tillägget. Det enklaste sättet är att köra `az extension add --name azure-cli-iot-ext`. Efter installationen kan du använda `az extension list` att verifiera de installerade tilläggen eller `az extension show --name azure-cli-iot-ext` att se information om IoT-tillägget. Du kan använda för att ta bort tillägget `az extension remove --name azure-cli-iot-ext`.


## <a name="deploy-modules-to-an-iot-edge-device"></a>Distribuera moduler till en IoT-enhet
I kursen får du lära dig hur du skapar en IoT-Edge-distribution. Exemplet visar hur du logga in på ditt Azure-konto, skapa en Azure-resursgrupp (en behållare som innehåller relaterade resurser för en Azure-lösning), skapar en IoT-hubb, skapa tre IoT kant enheter identitet, ställa in etiketter och sedan skapa en IoT-Edge-distribution som riktar sig till dessa enheter. Slutför installationssteg som beskrivs ovan innan du börjar. Om du inte har ett Azure-konto ännu, kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?v=17.39a) idag. 


### <a name="1-login-to-the-azure-account"></a>1. Logga in på Azure-konto
  
    az login

![inloggning][1]

### <a name="2-create-a-resource-group-iothubblogdemo-in-eastus"></a>2. Skapa en resursgrupp IoTHubBlogDemo i eastus

    az group create -l eastus -n IoTHubBlogDemo

![Skapa resursgrupp][2]


### <a name="3-create-an-iot-hub-blogdemohub-under-the-newly-created-resource-group"></a>3. Skapa en IoT-hubb blogDemoHub under den nyligen skapade resursgruppen

    az iot hub create --name blogDemoHub --resource-group IoTHubBlogDemo

![Skapa IoT-hubb][3]


### <a name="4-create-an-iot-edge-device"></a>4. Skapa en IoT-enhet

    az iot hub device-identity create -d edge001 -n blogDemoHub --edge-enabled

![Skapa IoT-enhet][4]

### <a name="5-apply-configuration-to-the-iot-edge-device"></a>5. Tillämpa konfigurationen på den IoT-enheten

Spara mallen för distribution av JSON lokalt som en txt-fil. Du måste sökvägen till filen när du kör kommandot tillämpa konfigurationen.

Nedan visas ett exempel JSON Distributionsmall som innehåller en tempSensor modul:

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
            "loggingOptions": ""
          }
        },
        "systemModules": {
          "edgeAgent": {
            "type": "docker",
            "settings": {
              "image": "edgepreview.azurecr.io/azureiotedge/edge-agent:1.0-preview",
              "createOptions": "{}"
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "edgepreview.azurecr.io/azureiotedge/edge-hub:1.0-preview",
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
              "image": "edgepreview.azurecr.io/azureiotedge/simulated-temperature-sensor:1.0-preview",
              "createOptions": "{}"
            }
          }
        }
      }
    },
    "$edgeHub": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {},
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

    az iot hub apply-configuration --device-id edge001 --hub-name blogDemoHub --content C:\<yourLocation>\edgeconfig.txt

![Använd konfiguration][5]

### <a name="6-list-modules"></a>6. Lista moduler
    
    az iot hub module-identity list --device-id edge001 --hub-name blogDemoHub

![Lista moduler][6]

## <a name="next-steps"></a>Nästa steg

I kursen får skapat du en Azure-funktion som innehåller koden för att filtrera rådata som genereras av enheten IoT kant. Om du vill behålla utforska Azure IoT kant, lär du dig hur du använder en IoT-enhet som en gateway. 

> [!div class="nextstepaction"]
> [Skapa en IoT-Edge-gateway](how-to-create-transparent-gateway.md)

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md

<!-- Images -->
[1]: ./media/tutorial-create-deployment-with-cli-iot-extension/login.jpg
[2]: ./media/tutorial-create-deployment-with-cli-iot-extension/create-resource-group.jpg
[3]: ./media/tutorial-create-deployment-with-cli-iot-extension/create-hub.jpg
[4]: ./media/tutorial-create-deployment-with-cli-iot-extension/Create-edge-device.png
[5]: ./media/tutorial-create-deployment-with-cli-iot-extension/apply-configuration.PNG
[6]: ./media/tutorial-create-deployment-with-cli-iot-extension/list-modules.PNG

