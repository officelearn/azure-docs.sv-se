---
title: Distribuera moduler till IoT Edge-enheter med IoT-tillägget för Azure CLI 2.0 | Microsoft Docs
description: Distribuera moduler till en IoT Edge-enhet med IoT-tillägget för Azure CLI 2.0
author: chrissie926
manager: ''
ms.author: menchi
ms.date: 03/02/2018
ms.topic: tutorial
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
md.custom: mvc
ms.openlocfilehash: deee54fe5d11d6d1cf5485357f853b1cb078f96d
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34631589"
---
# <a name="deploy-modules-to-an-iot-edge-device-using-iot-extension-for-azure-cli-20"></a>Distribuera moduler till en IoT Edge-enhet med IoT-tillägget för Azure CLI 2.0

[Azure CLI 2.0](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) är ett kommandoradsverktyg med öppen källkod för flera plattformar, för hantering av Azure-resurser som IoT Edge. Azure CLI 2.0 finns för Windows, Linux och MacOS.

Azure CLI 2.0 kan du hantera Azure IoT Hub-resurser, tjänstinstanser för enhetsetablering samt länkade hubbar direkt. Med det nya IoT-tillägget får Azure CLI 2.0 enhetshantering och fullständig IoT Edge-funktionalitet.

I den här artikeln konfigurerar du Azure CLI 2.0 och IoT-tillägget. Sedan får du lära dig hur du distribuerar moduler till en IoT Edge-enhet med hjälp av tillgängliga CLI-kommandon.

## <a name="prerequisites"></a>Nödvändiga komponenter

* Ett Azure-konto. Om du inte redan har ett konto kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?v=17.39a) i dag. 

* [Python 2.7x eller Python 3.x](https://www.python.org/downloads/).

* [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) i din miljö. Din Azure CLI 2.0-version måste vara 2.0.24 eller senare. Validera med `az –-version`. Den här versionen har stöd för az-tilläggskommandon och introducerar kommandoramverket Knack. Ett enkelt sätt att installera i Windows är att hämta och installera [MSI](https://aka.ms/InstallAzureCliWindows).

* [IoT-tillägget för Azure CLI 2.0](https://github.com/Azure/azure-iot-cli-extension):
   1. Kör `az extension add --name azure-cli-iot-ext`. 
   2. Efter installationen använder du `az extension list` för att verifiera de tillägg som finns installerade för närvarande, eller `az extension show --name azure-cli-iot-ext` för att se information om IoT-tillägget.
   3. Om du vill ta bort tillägget kan du använda `az extension remove --name azure-cli-iot-ext`.


## <a name="create-an-iot-edge-device"></a>Skapa en IoT Edge-enhet
Den här artikeln innehåller anvisningar för att skapa en IoT-Edge-distribution. Exemplet visar hur du loggar in på ditt Azure-konto, skapar en Azure-resursgrupp (en behållare som innehåller relaterade resurser för en Azure-lösning), skapar en IoT Hub, skapar tre IoT Edge-enhetsidentiteter, anger taggar och sedan skapar en IoT Edge-distribution med dessa enheter som mål. 

Logga in på ditt Azure-konto. När du har angett följande inloggningskommando uppmanas du att använda en webbläsare för att logga in med en engångskod: 

   ```cli
   az login
   ```

Skapa en ny resursgrupp som kallas **IoTHubCLI** i regionen USA, östra: 

   ```cli
   az group create -l eastus -n IoTHubCLI
   ```

   ![Skapa resursgrupp][2]

Skapa en IoT-hubb som kallas **CLIDemoHub** i den resursgrupp du skapade nyss:

   ```cli
   az iot hub create --name CLIDemoHub --resource-group IoTHubCLI --sku S1
   ```

   >[!TIP]
   >Varje prenumeration har en tilldelad kostnadsfri IoT-hubb. Om du vill skapa en kostnadsfri hubb med CLI-kommandot, ersätter du SKU-värdet med `--sku F1`. Om du redan har en kostnadsfri hubb i din prenumeration får du ett felmeddelande när du försöker skapa en till. 

Skapa en IoT Edge-enhet:

   ```cli
   az iot hub device-identity create --device-id edge001 --hub-name CLIDemoHub --edge-enabled
   ```

   ![Skapa IoT Edge-enhet][4]

## <a name="configure-the-iot-edge-device"></a>Konfigurera IoT Edge-enheten

Skapa en JSON-mall för distribution och spara den lokalt som en txt-fil. Du behöver ha sökvägen till filen när du kör kommandot för att tillämpa konfigurationen.

JSON-mallar för distribution ska alltid innehålla två systemmoduler, edgeAgent och edgeHub. Förutom de två modulerna kan du använda den här filen till att distribuera fler moduler till IoT Edge-enheten. Använd följande exempel för att konfigurera IoT Edge-enheten med en tempSensor-modul:

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
                 "image": "microsoft/azureiotedge-agent:1.0-preview",
                 "createOptions": "{}"
               }
             },
             "edgeHub": {
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "microsoft/azureiotedge-hub:1.0-preview",
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
                 "image": "microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview",
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

Tillämpa konfigurationen på din IoT Edge-enhet:

   ```cli
   az iot hub apply-configuration --device-id edge001 --hub-name CLIDemoHub --content C:\<configuration.txt file path>
   ```

Visa modulerna på din IoT Edge-enhet:
    
   ```cli
   az iot hub module-identity list --device-id edge001 --hub-name CLIDemoHub
   ```

   ![Lista med moduler][6]

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [använder en IoT Edge-enhet som en gateway](how-to-create-transparent-gateway.md)

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md

<!-- Images -->
[2]: ./media/tutorial-create-deployment-with-cli-iot-extension/create-resource-group.png
[4]: ./media/tutorial-create-deployment-with-cli-iot-extension/Create-edge-device.png
[6]: ./media/tutorial-create-deployment-with-cli-iot-extension/list-modules.png

