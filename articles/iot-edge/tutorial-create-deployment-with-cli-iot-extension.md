---
title: Distribuera moduler till IoT-gränsenheterna med IoT-tillägg för Azure CLI 2.0 | Microsoft Docs
description: Distribuera moduler till en insticksenhet för IoT som använder IoT-tillägg för Azure CLI 2.0
services: iot-edge
keywords: ''
author: chrissie926
manager: timlt
ms.author: menchi
ms.date: 03/02/2018
ms.topic: article
ms.service: iot-edge
ms.custom: ''
ms.reviewer: kgremban
ms.openlocfilehash: 7bc0d0706385f2f3e101d06be3a2837341c331b9
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="deploy-modules-to-an-iot-edge-device-using-iot-extension-for-azure-cli-20"></a>Distribuera moduler till en insticksenhet för IoT som använder IoT-tillägg för Azure CLI 2.0

[Azure CLI 2.0](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) är ett kommandoradsverktyg med öppen källkod för flera plattformar, för hantering av Azure-resurser som IoT Edge. Azure CLI 2.0 finns för Windows, Linux och MacOS.

Azure CLI 2.0 kan du hantera Azure IoT Hub-resurser, tjänstinstanser för enhetsetablering samt länkade hubbar direkt. Det nya tillägget IoT förbättra Azure CLI 2.0 med funktioner som enhetshantering och kapaciteten för fullständig IoT kant.

I den här artikeln kan du ställa in Azure CLI 2.0 och IoT-tillägget. Sedan får du lära dig hur du distribuerar moduler till en gräns för IoT-enhet med hjälp av de tillgängliga CLI-kommandona.

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto. Om du inte har någon ännu, kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?v=17.39a) idag. 

* [Python 2.7 x eller Python 3.x](https://www.python.org/downloads/).

* [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) i din miljö. Din Azure CLI 2.0-version måste vara 2.0.24 eller senare. Validera med `az –-version`. Den här versionen har stöd för az-tilläggskommandon och introducerar kommandoramverket Knack. Ett enkelt sätt att installera i Windows är att hämta och installera [MSI](https://aka.ms/InstallAzureCliWindows).

* [IoT-tillägget för Azure CLI 2.0](https://github.com/Azure/azure-iot-cli-extension):
   1. Kör `az extension add --name azure-cli-iot-ext`. 
   2. Efter installationen kan använda `az extension list` att verifiera de installerade tilläggen eller `az extension show --name azure-cli-iot-ext` att se information om IoT-tillägget.
   3. Ta bort tillägget med `az extension remove --name azure-cli-iot-ext`.


## <a name="create-an-iot-edge-device"></a>Skapa en IoT-enhet
Den här artikeln innehåller instruktioner för att skapa en IoT-Edge-distribution. Exemplet visar hur du logga in på ditt Azure-konto, skapa en Azure-resursgrupp (en behållare som innehåller relaterade resurser för en Azure-lösning), skapar en IoT-hubb, skapa tre IoT kant enheter identitet, ställa in etiketter och sedan skapa en IoT-Edge-distribution som riktar sig till dessa enheter. 

Logga in på ditt Azure-konto. När du har angett kommandot inloggningen uppmanas du att använda en webbläsare för att logga in med en engångskod: 

   ```cli
   az login
   ```

Skapa en ny resursgrupp som kallas **IoTHubCLI** i östra USA: 

   ```cli
   az group create -l eastus -n IoTHubCLI
   ```

   ![Skapa resursgrupp][2]

Skapa en IoT-hubb som kallas **CLIDemoHub** i nyskapade resursgrupp:

   ```cli
   az iot hub create --name CLIDemoHub --resource-group IoTHubCLI --sku S1
   ```

   >[!TIP]
   >Varje prenumeration tilldelas en kostnadsfri IoT-hubb. Om du vill skapa ett kostnadsfritt nav med kommandot CLI, Ersätt SKU-värdet med `--sku F1`. Om du redan har en kostnadsfri hubb i din prenumeration får du ett felmeddelande när du försöker skapa en andra. 

Skapa en IoT-enhet:

   ```cli
   az iot hub device-identity create --device-id edge001 -hub-name CLIDemoHub --edge-enabled
   ```

   ![Skapa IoT-enhet][4]

## <a name="configure-the-iot-edge-device"></a>Konfigurera IoT gränsenheten

Skapa en JSON-mall för distribution och spara den lokalt som en txt-fil. Du måste sökvägen till filen när du kör kommandot tillämpa konfigurationen.

Mallar för distribution av JSON bör alltid innehålla två systemmoduler, edgeAgent och edgeHub. Du kan använda den här filen för att distribuera ytterligare moduler till IoT gränsenheten utöver de två. Använd följande exempel för att konfigurera IoT-enhet med en tempSensor-modul:

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

Gäller konfigurationen för din IoT-Edge-enhet:

   ```cli
   az iot hub apply-configuration --device-id edge001 --hub-name CLIDemoHub --content C:\<configuration.txt file path>
   ```

Visa moduler på din IoT-Edge-enhet:
    
   ```cli
   az iot hub module-identity list --device-id edge001 --hub-name CLIDemoHub
   ```

   ![Lista moduler][6]

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [använda en insticksenhet för IoT som en gateway](how-to-create-transparent-gateway.md)

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md

<!-- Images -->
[2]: ./media/tutorial-create-deployment-with-cli-iot-extension/create-resource-group.png
[4]: ./media/tutorial-create-deployment-with-cli-iot-extension/Create-edge-device.png
[6]: ./media/tutorial-create-deployment-with-cli-iot-extension/list-modules.png

