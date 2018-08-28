---
title: Distribuera Azure Machine Learning med Azure IoT Edge | Microsoft Docs
description: I den här självstudien distribuerar du Azure Machine Learning som en modul till en gränsenhet
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/25/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: c13cd495f7802f14937321134a975b7a00c791ab
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/11/2018
ms.locfileid: "41920373"
---
# <a name="tutorial-deploy-azure-machine-learning-as-an-iot-edge-module-preview"></a>Distribuera Azure Machine Learning som en IoT Edge-modul (förhandsversion)

Du kan använda IoT Edge-moduler till att distribuera kod som implementerar din affärslogik direkt på dina IoT Edge-enheter. Den här självstudiekursen vägleder dig genom processen att distribuera en Azure Machine Learning-modul som förutsäger när en enhet slutar fungera baserat på simulerade data om datortemperaturen. Mer information om Azure ML på IoT Edge finns i [Dokumentation om Azure Machine Learning](../machine-learning/desktop-workbench/use-azure-iot-edge-ai-toolkit.md).

Azure Machine Learning-modulen som du skapar i den här självstudiekursen läser av miljödata som genereras av enheten och märker meddelandena som avvikande eller normala.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en Azure Machine Learning-modul
> * Push-överför en modulbehållare till ett Azure-containerregister
> * Distribuera Azure Machine Learning-modul till din IoT Edge-enhet
> * Visa genererade data

>[!NOTE]
>Azure Machine Learning-moduler på Azure IoT Edge finns i en allmänt tillgänglig förhandsversion. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Nödvändiga komponenter

En Azure IoT Edge-enhet:

* Du kan använda utvecklingsdatorn eller en virtuell dator som en gränsenhet genom att följa stegen i snabbstarten för [Linux-](quickstart-linux.md) eller [Windows-enheter](quickstart.md).
* Azure Machine Learning-modulen stöder inte ARM-processorer.

Molnresurser:

* En [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) på standardnivå i Azure. 
* Ett Azure Machine Learning-konto. Följ anvisningarna för att [skapa Azure Machine Learning-konton och installera Azure Machine Learning Workbench](../machine-learning/service/quickstart-installation.md#create-azure-machine-learning-services-accounts). Du behöver inte installera Workbench för den här självstudiekursen. 

Utvecklingsresurser:

* Modellhantering för Azure ML. Om du vill konfigurera din miljö och skapa ett konto följer du anvisningarna för [konfigurering av modellhantering](../machine-learning/desktop-workbench/deployment-setup-configuration.md). Under installationen av distributionen rekommenderar vi att du väljer de lokala stegen i stället för klustret, där det är möjligt.

### <a name="disable-process-identification"></a>Inaktivera processidentifiering

>[!NOTE]
>
> I förhandsversionen stöder inte Azure Machine Learning säkerhetsfunktionen processidentifiering som är aktiverad som standard med IoT Edge. 
> Här nedan följer stegen för att inaktivera den. Detta är dock inte lämpligt för användning i produktion. Dessa åtgärder krävs endast på Linux eftersom du kommer att ha slutfört det här under installationen av Windows Edge-körning.

Om du vill inaktivera processidentifiering på din IoT Edge-enhet måste du ange IP-adressen och porten för **workload_uri** och **management_uri** i avsnittet **ansluta** i daemon-konfiguration för IoT Edge.

Hämta IP-adressen först. Ange `ifconfig` på kommandoraden och kopiera IP-adressen för gränssnittet **docker0**.

Redigera daemon-konfigurationsfilen för IoT Edge:

```cmd/sh
sudo nano /etc/iotedge/config.yaml
```

Uppdatera avsnittet **connect** i konfigurationen med din IP-adress. Exempel:
```yaml
connect:
  management_uri: "http://172.17.0.1:15580"
  workload_uri: "http://172.17.0.1:15581"
```

Ange samma adresser i avsnittet **lyssna** i konfigurationen. Exempel:

```yaml
listen:
  management_uri: "http://172.17.0.1:15580"
  workload_uri: "http://172.17.0.1:15581"
```

Skapa en IOTEDGE_HOST-miljövariabel med management_uri-adressen. (Om du vill ange den permanent lägger du till den i `/etc/environment`.) Exempel:

```cmd/sh
export IOTEDGE_HOST="http://172.17.0.1:15580"
```


## <a name="create-the-azure-ml-container"></a>Skapa en Azure ML-container
I det här avsnittet laddar du ned tränade modellfiler och konverterar dem till en Azure ML-container.

På datorn som kör Modellhantering för Azure ML laddar du ned och sparar [iot_score.py](https://github.com/Azure/ai-toolkit-iot-edge/blob/master/IoT%20Edge%20anomaly%20detection%20tutorial/iot_score.py) och [model.pkl](https://github.com/Azure/ai-toolkit-iot-edge/blob/master/IoT%20Edge%20anomaly%20detection%20tutorial/model.pkl) från Azure ML IoT Toolkit på GitHub. De här filerna definierar den tränade maskininlärningsmodell som du ska distribuera till din IoT Edge-enhet.

Använd den tränade modellen för att skapa en container som kan distribueras till IoT Edge-enheter. Använd följande kommando för att:

   * Registrera din modell.
   * Skapa ett manifest.
   * Skapa en Docker-containeravbildning med namnet *machinelearningmodule*.
   * Distribuera avbildningen till din Azure Kubernetes Service-kluster (AKS).

```cmd
az ml service create realtime --model-file model.pkl -f iot_score.py -n machinelearningmodule -r python
```

### <a name="view-the-container-repository"></a>Visa containerlagringsplatsen

Kontrollera att containeravbildningen skapades och lagras i Azure Container-registret som är associerad med maskininlärningsmiljön.

1. På [Azure Portal](https://portal.azure.com) går du till **Alla tjänster** och väljer **Containerregister**.
2. Välj ditt register. Namnet måste börja med **mlcr** och det hör till den resursgrupp, plats och prenumeration som du använde för att ställa in modulhanteringen.
3. Välj **Åtkomstnycklar**
4. Kopiera värdena för **inloggningsserver**, **användarnamn** och **lösenord**.  Du behöver dem för att komma åt registret från dina Edge-enheter.
5. Välj **Lagringsplatser**
6. Välj **machinelearningmodule**
7. Nu har du fullständig avbildningssökväg för containern. Anteckna avbildningssökvägen för nästa avsnitt. Den bör se ut så här: **<registernamn>.azurecr.io/machinelearningmodule:1**

## <a name="deploy-to-your-device"></a>Distribuera till din nya enhet

1. Gå till din IoT-hubb på [Azure Portal](https://portal.azure.com).

1. Gå till **IoT Edge** och välj din IoT Edge-enhet.

1. Välj **Ange moduler**.

1. I avsnittet **Registerinställningar** lägger du till de autentiseringsuppgifter som du kopierade från ditt Azure Container-register. 

   ![Lägg till autentiseringsuppgifter för registret](./media/tutorial-deploy-machine-learning/registry-settings.png)

1. Om du redan har distribuerat modulen tempSensor till din IoT Edge-enhet kanske den fylls i automatiskt. Lägg till den om den inte redan finns i listan över moduler.

    1. Klicka på **Lägg till** och välj **IoT Edge-modul**.
    2. I **Namn**-fältet skriver du `tempSensor`.
    3. I **URI för avbildning**-fältet skriver du `mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0`.
    4. Välj **Spara**.

1. Lägg till maskininlärningsmodulen som du skapade.

    1. Klicka på **Lägg till** och välj **IoT Edge-modul**.
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

1. Välj **Skicka** i steget **Granska distribution**.

1. Återgå till informationssidan om enheten och välj **Uppdatera**.  Du borde se den nya **maskininlärningsmodulen** som körs tillsammans med **tempSensor**-modulen och IoT Edge-körningsmodulerna.

## <a name="view-generated-data"></a>Visa genererade data

Du kan visa meddelanden som skapas av varje IoT Edge-modul och du kan visa meddelanden som skickas till din IoT Hub.

### <a name="view-data-on-your-iot-edge-device"></a>Visa data på din IoT Edge-enhet

Du kan visa de meddelanden som skickas från varje enskild modul på din IoT Edge-enhet. 

Om du utför dessa kommandon på en Linux-enhet kan du behöva använda `sudo` för att få förhöjda behörigheter.

1. Visa alla moduler på din IoT Edge-enhet.

   ```cmd/sh
   iotedge list
   ```

2. Visa meddelanden som skickas från en specifik enhet. Använd modulnamnet från utdata från det föregående kommandot.

   ```cmd/sh
   iotedge logs <module_name> -f
   ```

### <a name="view-data-arriving-at-your-iot-hub"></a>Visa data som inkommer till din IoT Hub

Du kan visa meddelanden från enhet-till-moln som din IoT-hubb tar emot med hjälp av [Azure IoT Toolkit-tillägget för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit).

Följande steg visar hur du ställer in Visual Studio Code för att övervaka meddelanden från enhet till moln som kommer till din IoT Hub. 

1. Välj **IoT Hub-enheter** i Visual Studio Code.

2. Välj **...** och sedan alternativet för att **ställa in IoT Hub-anslutningssträngen** på menyn.

   ![IoT Hub-enheter – fler menyer](./media/tutorial-deploy-machine-learning/set-connection.png)

3. Ange anslutningssträngen iothubowner för din IoT Hub i textrutan som visas överst på sidan. Enhetens IoT Edge-enhet bör visas i listan över IoT Hub-enheter.

4. Välj **...** igen och välj sedan **alternativet för att börja övervaka D2C**.

5. Kontrollera meddelandena som kommer från tempSensor var femte sekund. Meddelandetexten innehåller en egenskap som kallas **avvikelseidentifiering** där machinelearningmodule har värdet true eller false. Egenskapen **AzureMLResponse** innehåller värdet "OK" om modellen har körts med lyckat resultat.

   ![Azure ML-svar i meddelandetexten](./media/tutorial-deploy-machine-learning/ml-output.png)

## <a name="clean-up-resources"></a>Rensa resurser 

Om du planerar att fortsätta med nästa rekommenderade artikel kan du behålla de resurser och konfigurationer som du skapat och använda dem igen. Du kan även fortsätta att använda samma IoT Edge-enhet som en testenhet. 

Annars kan du ta bort de lokala konfigurationerna och de Azure-resurser som du har skapat i den här artikeln för att därigenom undvika kostnader. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]


## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen har du distribuerat en IoT Edge-modul som tillhandahålls av Azure Machine Learning. Du kan fortsätta med någon av följande självstudiekurser om du vill veta mer hur Azure IoT Edge kan förvandla dina data till affärsinsikter.

> [!div class="nextstepaction"]
> [Filtrera sensordata med hjälp av C#-kod](tutorial-csharp-module.md)

<!--Links-->
[lnk-tutorial1-win]: quickstart.md
[lnk-tutorial1-lin]: quickstart-linux.md
