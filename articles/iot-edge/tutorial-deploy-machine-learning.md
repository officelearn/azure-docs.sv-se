---
title: Distribuera Azure Machine Learning till en enhet – Azure IoT Edge | Microsoft Docs
description: I den här självstudien får du skapa en Azure Machine Learning-modell och sedan distribuera den som en modul till en edge-enhet
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/07/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 8b1036128755a5218afc35648dfd16f09f559908
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60611720"
---
# <a name="tutorial-deploy-azure-machine-learning-as-an-iot-edge-module-preview"></a>Självstudier: Distribuera Azure Machine Learning som en IoT Edge-modul (förhandsversion)

Du kan använda IoT Edge-moduler till att distribuera kod som implementerar din affärslogik direkt på dina IoT Edge-enheter. Den här självstudiekursen vägleder dig genom processen att distribuera en Azure Machine Learning-modul som förutsäger när en enhet slutar fungera baserat på simulerade data om datortemperaturen. Mer information om Azure Machine Learning-tjänsten på IoT Edge finns i [Dokumentation om Azure Machine Learning](../machine-learning/service/how-to-deploy-to-iot.md).

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

* Du kan använda en Azure virtuell dator som en IoT Edge-enhet genom att följa stegen i snabbstarten för [Linux](quickstart-linux.md).
* Azure Machine Learning-modulen stöder inte Windows-behållare.
* Azure Machine Learning-modulen stöder inte ARM-processorer.

Molnresurser:

* En [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) på kostnadsfri nivå eller standardnivå i Azure.
* En Azure Machine Learning-arbetsyta. Följ instruktionerna i [använda Azure portal för att komma igång med Azure Machine Learning](../machine-learning/service/quickstart-get-started.md) skapa en och lär dig hur du använder den.
   * Anteckna den arbetsytans namn, resursgrupp och prenumerations-ID. Dessa värdena är den arbetsyta-översikt i Azure-portalen. Du kommer använda dessa värden senare under kursen för att ansluta en Azure-anteckningsbok till resurserna i arbetsytan. 


### <a name="disable-process-identification"></a>Inaktivera processidentifiering

>[!NOTE]
>
> I förhandsversionen stöder inte Azure Machine Learning säkerhetsfunktionen processidentifiering som är aktiverad som standard med IoT Edge.
> Här nedan följer stegen för att inaktivera den. Detta är dock inte lämpligt för användning i produktion. Dessa åtgärder krävs endast på Linux-enheter. 

Om du vill inaktivera identifiering av processen på din IoT Edge-enhet, måste du ange IP-adressen och porten för **workload_uri** och **management_uri** i den **ansluta** del av konfigurationen av IoT Edge-daemon.

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

Spara och Stäng konfigurationsfilen.

Skapa en miljövariabel IOTEDGE_HOST med management_uri-adress (om du vill ange permanent, lägger du till den i `/etc/environment`). Exempel:

```cmd/sh
export IOTEDGE_HOST="http://172.17.0.1:15580"
```

Starta om tjänsten IoT Edge för att ändringarna ska börja gälla.

```cmd/sh
sudo systemctl restart iotedge
```

## <a name="create-and-deploy-azure-machine-learning-module"></a>Skapa och distribuera Azure Machine Learning-modul

I det här avsnittet, konvertera trained modellfiler för maskininlärning och till en Azure Machine Learning-tjänsten behållare. Alla komponenter som krävs för dockeravbildningen finns i [AI Toolkit for Azure IoT Edge Git repo](https://github.com/Azure/ai-toolkit-iot-edge/tree/master/IoT%20Edge%20anomaly%20detection%20tutorial) (AI-verktyg för Azure IoT Edge Git-lagringsplats). Följ dessa steg om du vill överföra den lagringsplatsen till Microsoft Azure-anteckningsböcker för att skapa behållaren och push-överföra den till Azure Container Registry.


1. Navigera till dina anteckningsböcker i Azure-projekt. Du kan hämta det från din arbetsyta med Azure Machine Learning-tjänsten i den [Azure-portalen](https://portal.azure.com) eller genom att logga in till [Microsoft Azure-anteckningsböcker](https://notebooks.azure.com/home/projects) med ditt Azure-konto.

2. Välj **överför GitHub-lagringsplatsen**.

3. Ange följande GitHub-lagringsplatsnamn: `Azure/ai-toolkit-iot-edge`. Avmarkera den **offentliga** om du vill att hålla projektet privat. Välj **Import**. 

4. När importen är klar går du till den nya **ai-toolkit-iot-edge** projektet och öppna den **IoT Edge avvikelseidentifiering identifiering av självstudien** mapp. 

5. Kontrollera att projektet körs. Om inte, Välj **körs på kostnadsfria Compute**.

   ![Kör på kostnadsfri beräkning](./media/tutorial-deploy-machine-learning/run-on-free-compute.png)

6. Öppna den **aml_config/config.json** fil.

7. Redigera konfigurationsfilen för att inkludera värdena för ditt Azure-prenumerations-ID, en resursgrupp i din prenumeration och Azure Machine Learning-arbetsyta tjänstnamnet. Du kan hämta dessa värden från den **översikt** avsnitt i din arbetsyta i Azure. 

8. Spara konfigurationsfilen.

9. Öppna den **00-avvikelseidentifiering-identifiering – tutorial.ipynb** fil.

10. När du uppmanas, väljer den **Python 3.6** kernel därefter **ange Kernel**.

11. Redigera den första cellen i anteckningsboken enligt instruktionerna i kommentarerna. Använd samma resursgrupp, prenumerations-ID och namn på arbetsyta som du lade till konfigurationsfilen.

12. Kör cellerna i anteckningsboken genom att markera dem och välja **kör** eller trycka på `Shift + Enter`.

    >[!TIP]
    >Vissa av cellerna i avvikelseidentifiering identifiering av självstudiekursen anteckningsboken är valfria, eftersom de skapar resurser som vissa användare kanske eller kanske inte ännu, t.ex. en IoT-hubb. Om du placerar dina befintliga resursinformation på den första cellen får du fel om du kör cellerna som skapar nya resurser eftersom Azure inte kommer att skapa duplicerade resurser. Det här är bra, och du kan ignorera felen eller hoppa över dessa valfria avsnitt helt och hållet. 

När du har slutfört alla steg i anteckningsboken, har du tränats en avvikelseidentifiering identifiering av modellen som skapats som en Docker-behållaravbildning och push-överfört avbildningen till Azure Container Registry. Sedan testade modellen och slutligen distribuerat den till din IoT Edge-enhet. 

## <a name="view-container-repository"></a>Visa behållaren lagringsplats

Kontrollera att din behållaravbildning skapades och lagras i Azure container registry som är associerad med din machine learning-miljö. Anteckningsboken som du använde i föregående avsnitt automatiskt tillhandahålls behållaravbildningen och autentiseringsuppgifter för registret till din IoT Edge-enhet, men du bör känna till där de lagras så att du kan hitta informationen om dig själv senare. 

1. I den [Azure-portalen](https://portal.azure.com), navigera till din arbetsyta för Machine Learning-tjänsten. 

2. Den **översikt** avsnittet innehåller information om arbetsytan som och dess kopplade resurser. Välj den **registret** värde, som bör vara namnet på din arbetsyta följt av slumptal. 

3. Välj i behållarregistret **databaser**. Du bör se en databas som heter **tempanomalydetection** som har skapats med den bärbara datorn som du körde i det tidigare avsnittet. 

4. Välj **tempanomalydetection**. Du bör se att databasen har en tagg: **1**. 

   Nu när du vet registernamn, databasnamn och tagg kan känner du till hela avbildningen-sökvägen i behållaren. Sökvägar för bilder ut  **\<registry_name\>.azurecr.io/tempanomalydetection:1**. Du kan använda sökvägen till bild för att distribuera den här behållaren till IoT Edge-enheter. 

5. Välj i behållarregistret **åtkomstnycklar**. Du bör se ett antal autentiseringsuppgifter, inklusive **inloggningsserver** och **användarnamn**, och **lösenord** för en administratörsanvändare.

   Dessa autentiseringsuppgifter kan ingå i manifestet distribution att ge dina IoT-Edge Enhetsåtkomst till hämta behållaravbildningar från registret. 

Nu vet du var den Machine Learning-behållaravbildningen lagras. Nästa avsnitt går igenom stegen för att se hur det fungerar som en distribuerad modul på din IoT Edge-enhet. 

## <a name="view-generated-data"></a>Visa genererade data

Du kan visa meddelanden som skapas av varje IoT Edge-modul och du kan visa meddelanden som skickas till din IoT Hub.

### <a name="view-data-on-your-iot-edge-device"></a>Visa data på din IoT Edge-enhet

Du kan visa de meddelanden som skickas från varje enskild modul på din IoT Edge-enhet.

Du kan behöva använda `sudo` för förhöjd behörighet att köra `iotedge` kommandon. Logga ut och logga in igen till din enhet automatiskt uppdaterar dina behörigheter.

1. Visa alla moduler på din IoT Edge-enhet.

   ```cmd/sh
   iotedge list
   ```

2. Visa meddelanden som skickas från en specifik enhet. Använd modulnamnet från utdata från det föregående kommandot.

   ```cmd/sh
   iotedge logs <module_name> -f
   ```

### <a name="view-data-arriving-at-your-iot-hub"></a>Visa data som inkommer till din IoT Hub

Du kan visa meddelanden från enhet-till-moln som din IoT-hubb tar emot med hjälp av [Azure IoT Hub Toolkit-tillägget för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) (kallades tidigare Azure IoT Toolkit-tillägget).

Följande steg visar hur du ställer in Visual Studio Code för att övervaka meddelanden från enhet till moln som kommer till din IoT Hub.

1. Välj **IoT Hub-enheter** i Visual Studio Code.

2. Välj **...** och sedan alternativet för att **ställa in IoT Hub-anslutningssträngen** på menyn.

   ![Ange IoT-hubbens anslutningssträng](./media/tutorial-deploy-machine-learning/set-connection.png)

3. Ange anslutningssträngen iothubowner för din IoT Hub i textrutan som visas överst på sidan. Enhetens IoT Edge-enhet bör visas i listan över IoT Hub-enheter.

4. Välj **...** igen och välj sedan **alternativet för att börja övervaka D2C**.

5. Kontrollera meddelandena som kommer från tempSensor var femte sekund. Meddelandetexten innehåller en egenskap som kallas **avvikelseidentifiering**, där machinelearningmodule har värdet SANT eller FALSKT. Egenskapen **AzureMLResponse** innehåller värdet "OK" om modellen har körts med lyckat resultat.

   ![Azure Machine Learning Service-svar i meddelandets brödtext](./media/tutorial-deploy-machine-learning/ml-output.png)

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att fortsätta med nästa rekommenderade artikel kan du behålla de resurser och konfigurationer som du skapat och använda dem igen. Du kan även fortsätta att använda samma IoT Edge-enhet som en testenhet.

Annars kan du ta bort de lokala konfigurationerna och de Azure-resurser som du har skapat i den här artikeln för att därigenom undvika kostnader.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen har du distribuerat en IoT Edge-modul som tillhandahålls av Azure Machine Learning. Du kan fortsätta med någon av följande självstudiekurser om du vill veta mer hur Azure IoT Edge kan förvandla dina data till affärsinsikter.

> [!div class="nextstepaction"]
> [Klassificera bilder med Custom Vision-tjänsten](tutorial-deploy-custom-vision.md)

