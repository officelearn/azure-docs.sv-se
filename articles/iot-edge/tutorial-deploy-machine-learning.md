---
title: Distribuera Azure Machine Learning med Azure IoT-Edge | Microsoft Docs
description: Distribuera Azure Machine Learning som en modul till en insticksenhet
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 03/12/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: e4753cf0ffdedcc2ddc694fba67c560363789e3a
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="deploy-azure-machine-learning-as-an-iot-edge-module---preview"></a>Distribuera Azure Machine Learning som en gräns för IoT-modul - förhandsgranskning

Du kan använda IoT kant-moduler för att distribuera kod som implementerar affärslogiken direkt till IoT Edge-enheter. Den här självstudiekursen vägleder dig genom att distribuera en Azure Machine Learning-modul som beräknar när en enhet inte baserat på sensordata på simulerade IoT gränsenheten som du skapade i den [distribuera Azure IoT kanten på en simulerad enhet i Windows] [ lnk-tutorial1-win] eller [Linux] [ lnk-tutorial1-lin] självstudier.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en Azure Machine Learning-modul
> * Push-modulen behållare till en Azure-behållaren registret
> * Distribuera en Azure Machine Learning-modul till din IoT-Edge-enhet
> * Visa genererade data

Azure Machine Learning-modul som du skapar i den här kursen läser miljön data som genereras av enheten och etiketter meddelanden som avvikande eller inte.

## <a name="prerequisites"></a>Förutsättningar

* Azure IoT gränsenheten som du skapade i Snabbstart eller första självstudierna.
* IoT-hubb anslutningssträngen för IoT-hubb som din IoT insticksenhet ansluter till.
* Ett Azure Machine Learning-konto. Om du vill skapa ett konto, följ instruktionerna i [skapa Azure Machine Learning-konton och installera Azure Machine Learning arbetsstationen](../machine-learning/service/quickstart-installation.md#create-azure-machine-learning-services-accounts). Du behöver inte installera programmet arbetsstationen för den här självstudiekursen.
* Hantering av modulen för Azure ML på din dator. Om du vill konfigurera din miljö och skapa ett konto, följ instruktionerna i [installation av hantering av modellen](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/deployment-setup-configuration).

Azure Machine Learning-modulen stöder inte ARM-processorer.

## <a name="create-the-azure-ml-container"></a>Skapa Azure ML-behållare
I det här avsnittet hämta trained modellfilerna och konvertera dem till en Azure ML-behållare.

På datorn som kör Management-modulen för Azure ML, hämta och spara [iot_score.py](https://github.com/Azure/ai-toolkit-iot-edge/blob/master/IoT%20Edge%20anomaly%20detection%20tutorial/iot_score.py) och [model.pkl](https://github.com/Azure/ai-toolkit-iot-edge/blob/master/IoT%20Edge%20anomaly%20detection%20tutorial/model.pkl) från Azure ML IoT Toolkit på GitHub. De här filerna definiera tränad modell som du ska distribuera till enheten Iot kant för maskininlärning.

Använd den tränade modellen för att skapa en behållare som kan distribueras till enheter som IoT. Använd följande kommando för att:

   * Registrera din modell.
   * Skapa ett manifest.
   * Skapa en avbildning av Docker behållare med namnet *machinelearningmodule*.
   * Distribuera avbildningen till Azure Kubernetes Service (AKS) klustret.

```cmd
az ml service create realtime --model-file model.pkl -f iot_score.py -n machinelearningmodule -r python
```

### <a name="view-the-container-repository"></a>Visa behållaren databasen

Kontrollera att behållaren avbildningen skapades och lagras i Azure-behållaren databasen som är associerad med machine learning miljön.

1. På den [Azure-portalen](https://portal.azure.com), gå till **alla tjänster** och välj **behållare register**.
2. Välj registret. Namnet måste börja med **mlcr** och det hör till resursgruppens namn, plats och prenumeration som du använde för att ställa in hantering av modulen.
3. Välj **åtkomstnycklar**
4. Kopiera den **inloggningsserver**, **användarnamn**, och **lösenord**.  Du behöver dem för att komma åt registret från dina enheter.
5. Välj **databaser**
6. Välj **machinelearningmodule**
7. Nu har du fullständig avbildning på behållaren. Anteckna denna avbildningssökväg för nästa avsnitt. Det bör se ut så här: **< registry_name >.azureacr.io/machinelearningmodule:1**

## <a name="add-registry-credentials-to-your-edge-device"></a>Lägg till autentiseringsuppgifter för registret i din Edge-enhet

Lägg till autentiseringsuppgifterna för ditt register i Edge-runtimen på den dator där du kör din Edge-enhet. Det här kommandot ger runtime-åtkomst till pull-behållaren.

Linux:
   ```cmd
   sudo iotedgectl login --address <registry-login-server> --username <registry-username> --password <registry-password>
   ```

Windows:
   ```cmd
   iotedgectl login --address <registry-login-server> --username <registry-username> --password <registry-password>
   ```

## <a name="run-the-solution"></a>Kör lösningen

1. På den [Azure-portalen](https://portal.azure.com), navigera till din IoT-hubb.
1. Gå till **IoT Edge (förhandsversion)** och välj IoT Edge-enhet.
1. Välj **Ange moduler**.
1. Om du har redan distribuerats modulen tempSensor till din IoT insticksenhet, får den autopopulate. Om det inte redan finns i listan över moduler, kan du lägga till den.
    1. Välj **Lägg till IoT Edge-modul**.
    2. I **Namn**-fältet skriver du `tempSensor`.
    3. I **URI för avbildning**-fältet skriver du `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
    4. Välj **Spara**.
1. Lägg till datorn lärmodulen som du skapade.
    1. Välj **Lägg till IoT Edge-modul**.
    1. I den **namn** anger `machinelearningmodule`
    1. I den **bild** , ange din adress bild, till exempel `<registry_name>.azurecr.io/machinelearningmodule:1`.
    1. Välj **Spara**.
1. I steget **Lägg till moduler** väljer du **Nästa**.
1. I steget **Ange vägar** kopierar du den JSON nedanför till textrutan. Första vägen transporter meddelanden från temperatursensor till datorn lärmodulen via slutpunkten ”amlInput”, vilket är den slutpunkt som använder alla moduler i Azure Machine Learning. Andra vägen transporter meddelanden från machine learning-modulen till IoT-hubb. I den här vägen '' amlOutput'' slutpunkten med alla Azure Machine Learning-moduler som utdata och '' uppströms$ '' anger IoT-hubb.

    ```json
    {
        "routes": {
            "sensorToMachineLearning":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/machinelearningmodule/inputs/amlInput\")",
            "machineLearningToIoTHub": "FROM /messages/modules/machinelearningmodule/outputs/amlOutput INTO $upstream"
        }
    }
    ```

1. Välj **Nästa**.
1. I steget för att **granska mallen** väljer du **Skicka**.
1. Återgå till informationssidan om enheten och välj **Uppdatera**.  Du bör se den nya **machinelearningmodule** körs tillsammans med den **tempSensor** modulen och moduler för IoT-Edge-körning.

## <a name="view-generated-data"></a>Visa genererade data

Du kan visa meddelanden enhet till moln som din IoT insticksenhet skickar med hjälp av den [IoT-hubb explorer](https://github.com/azure/iothub-explorer) eller Azure IoT Toolkit-tillägget för Visual Studio-koden.

1. Välj i Visual Studio Code **IoT Hub-enheter**.
2. Välj **...**  Välj **ange anslutningssträngen för IoT-hubb** på menyn.

   ![IoT Hub-enheter menyn mer](./media/tutorial-deploy-machine-learning/set-connection.png)

3. Ange anslutningssträngen iothubowner för din IoT-hubb i textrutan som visas överst på sidan. Enheten IoT kant ska visas i listan över IoT Hub-enheter.
4. Välj **...**  igen Välj **börja övervaka D2C meddelandet**.
5. Se de meddelanden som kommer från tempSensor var femte sekund. Meddelandetexten innehåller en egenskap som kallas **avvikelseidentifiering** där machinelearningmodule har värdet true eller false. Den **AzureMLResponse** egenskap innehåller värdet ”OK” om modellen har körts.

   ![Azure ML-svar i meddelandetexten](./media/tutorial-deploy-machine-learning/ml-output.png)

## <a name="next-steps"></a>Nästa steg

Du har distribuerat en IoT-Edge-modul som tillhandahålls av Azure Machine Learning i den här självstudiekursen. Du kan fortsätta in på någon av följande självstudiekurser vill veta mer om andra sätt att Azure IoT gräns kan du göra data till affärsinsikter kant.

> [!div class="nextstepaction"]
> [Distribuera en Azure-funktion som en modul](tutorial-deploy-function.md)

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
