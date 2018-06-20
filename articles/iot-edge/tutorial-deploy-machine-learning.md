---
title: Distribuera Azure Machine Learning med Azure IoT Edge | Microsoft Docs
description: Distribuera Azure Machine Learning som en modul till en Edge-enhet
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 03/12/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 248bc97c214c013d10f1839201ce2f572cb854ed
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34631181"
---
# <a name="deploy-azure-machine-learning-as-an-iot-edge-module---preview"></a>Distribuera Azure Machine Learning som en IoT Edge-modul – förhandsgranskning

Du kan använda IoT Edge-moduler för att distribuera kod som implementerar din affärslogik direkt till dina IoT Edge-enheter. Den här självstudiekursen visar hur du distribuerar en Azure Machine Learning-modul som förutsäger när en enhet kommer att misslyckas utifrån sensordata på den simulerade IoT Edge-enheten du skapade i självstudiekurserna [Distribuera Azure IoT Edge på en simulerad enhet i Windows][lnk-tutorial1-win] eller [Linux][lnk-tutorial1-lin].

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Skapa en Azure Machine Learning-modul
> * Push-överför en modulbehållare till ett Azure-containerregister
> * Distribuera Azure Machine Learning-modul till din IoT Edge-enhet
> * Visa genererade data

Azure Machine Learning-modulen som du skapar i den här självstudiekursen läser av miljödata som genereras av enheten och märker meddelandena som avvikande eller normala.

## <a name="prerequisites"></a>Nödvändiga komponenter

* Azure IoT Edge-enheten som du skapade i snabbstarten eller tidigare självstudiekurser.
* IoT Hub-anslutningssträngen för den IoT Hub som din IoT Edge-enhet ansluter till.
* Ett Azure Machine Learning-konto. Om du vill skapa ett konto följer du anvisningarna för att [skapa Azure Machine Learning-konton och installera Azure Machine Learning Workbench](../machine-learning/service/quickstart-installation.md#create-azure-machine-learning-services-accounts). Du behöver inte installera Workbench för den här självstudiekursen. 
* Hantering av modulen för Azure ML på din dator. Om du vill konfigurera din miljö och skapa ett konto följer du anvisningarna för [konfigurering av modellhantering](../machine-learning/desktop-workbench/deployment-setup-configuration.md).

Azure Machine Learning-modulen stöder inte ARM-processorer.

## <a name="create-the-azure-ml-container"></a>Skapa en Azure ML-behållare
I det här avsnittet laddar du ned tränade modellfiler och konverterar dem till en Azure ML-behållare.

På datorn som kör modulhanteringen för Azure ML laddar du ned och sparar [iot_score.py](https://github.com/Azure/ai-toolkit-iot-edge/blob/master/IoT%20Edge%20anomaly%20detection%20tutorial/iot_score.py) och [model.pkl](https://github.com/Azure/ai-toolkit-iot-edge/blob/master/IoT%20Edge%20anomaly%20detection%20tutorial/model.pkl) från Azure ML IoT Toolkit på GitHub. De här filerna definierar den tränade maskininlärningsmodell som du ska distribuera till din IoT Edge-enhet.

Använd den tränade modellen för att skapa en behållare som kan distribueras till IoT Edge-enheter. Använd följande kommando för att:

   * Registrera din modell.
   * Skapa ett manifest.
   * Skapa en Docker-containeravbildning med namnet *machinelearningmodule*.
   * Distribuera avbildningen till din Azure Kubernetes Service-kluster (AKS).

```cmd
az ml service create realtime --model-file model.pkl -f iot_score.py -n machinelearningmodule -r python
```

### <a name="view-the-container-repository"></a>Visa behållarlagringsplatsen

Kontrollera att containeravbildningen skapades och lagras i Azure-behållaren som är associerad med maskininlärningsmiljön.

1. På [Azure Portal](https://portal.azure.com) går du till **Alla tjänster** och väljer **Behållarregister**.
2. Välj ditt register. Namnet måste börja med **mlcr** och det hör till den resursgrupp, plats och prenumeration som du använde för att ställa in modulhanteringen.
3. Välj **Åtkomstnycklar**
4. Kopiera värdena för **inloggningsserver**, **användarnamn** och **lösenord**.  Du behöver dem för att komma åt registret från dina Edge-enheter.
5. Välj **Lagringsplatser**
6. Välj **machinelearningmodule**
7. Nu har du fullständig avbildningssökväg för behållaren. Anteckna avbildningssökvägen för nästa avsnitt. Det bör se ut så här: **<registry_name>.azureacr.io/machinelearningmodule:1**

## <a name="add-registry-credentials-to-your-edge-device"></a>Lägg till autentiseringsuppgifter för registret i din Edge-enhet

Lägg till autentiseringsuppgifterna för ditt register i Edge-runtimen på den dator där du kör din Edge-enhet. Detta kommando ger körningsåtkomst för att hämta behållaren.

Linux:
   ```cmd
   sudo iotedgectl login --address <registry-login-server> --username <registry-username> --password <registry-password>
   ```

Windows:
   ```cmd
   iotedgectl login --address <registry-login-server> --username <registry-username> --password <registry-password>
   ```

## <a name="run-the-solution"></a>Kör lösningen

1. Gå till din IoT-hubb på [Azure Portal](https://portal.azure.com).
1. Gå till **IoT Edge (förhandsversion)** och välj IoT Edge-enhet.
1. Välj **Ange moduler**.
1. Om du redan har distribuerat modulen tempSensor till din IoT Edge-enhet kanske den fylls i automatiskt. Lägg till den om den inte redan finns i listan över moduler.
    1. Välj **Lägg till IoT Edge-modul**.
    2. I **Namn**-fältet skriver du `tempSensor`.
    3. I **URI för avbildning**-fältet skriver du `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
    4. Välj **Spara**.
1. Lägg till maskininlärningsmodulen som du skapade.
    1. Välj **Lägg till IoT Edge-modul**.
    1. I fältet **Namn** skriver du `machinelearningmodule`
    1. I fältet **Avbildning** anger du adressen till din avbildning, t.ex. `<registry_name>.azurecr.io/machinelearningmodule:1`.
    1. Välj **Spara**.
1. I steget **Lägg till moduler** väljer du **Nästa**.
1. I steget **Ange vägar** kopierar du den JSON nedanför till textrutan. Första vägen transporterar meddelanden från temperatursensorn till datorns maskininlärningsmodul via slutpunkten "amlInput", som är den slutpunkt som alla Azure Machine Learning-moduler använder. Den andra vägen transporterar meddelanden från maskininlärningen till IoT Hub. I den här vägen är ''amlOutput'' slutpunkten som alla Azure Machine Learning-moduler använder för utdata och ''$upstream'' anger IoT Hub.

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
1. Återgå till informationssidan om enheten och välj **Uppdatera**.  Du borde se den nya **maskininlärningsmodulen** som körs tillsammans med **tempSensor**-modulen och IoT Edge-körningsmodulerna.

## <a name="view-generated-data"></a>Visa genererade data

Du kan visa meddelanden från enhet-till-moln som din IoT Edge-enhet skickar med hjälp av [IoT Hub Explorer](https://github.com/azure/iothub-explorer) eller Azure IoT Toolkit-tillägget för Visual Studio Code.

1. Välj **IoT Hub-enheter** i Visual Studio Code.
2. Välj **...** och sedan alternativet för att **ställa in IoT Hub-anslutningssträngen** på menyn.

   ![IoT Hub-enheter – fler menyer](./media/tutorial-deploy-machine-learning/set-connection.png)

3. Ange anslutningssträngen iothubowner för din IoT Hub i textrutan som visas överst på sidan. Enhetens IoT Edge-enhet bör visas i listan över IoT Hub-enheter.
4. Välj **...** igen och välj sedan **alternativet för att börja övervaka D2C**.
5. Kontrollera meddelandena som kommer från tempSensor var femte sekund. Meddelandetexten innehåller en egenskap som kallas **avvikelseidentifiering** där machinelearningmodule har värdet true eller false. Egenskapen **AzureMLResponse** innehåller värdet "OK" om modellen har körts med lyckat resultat.

   ![Azure ML-svar i meddelandetexten](./media/tutorial-deploy-machine-learning/ml-output.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen har du distribuerat en IoT Edge-modul som tillhandahålls av Azure Machine Learning. Du kan fortsätta med någon av följande självstudiekurser om du vill veta mer hur Azure IoT Edge kan förvandla dina data till affärsinsikter.

> [!div class="nextstepaction"]
> [Distribuera en Azure-funktion som en modul](tutorial-deploy-function.md)

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
