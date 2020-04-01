---
title: Självstudiekurs - Distribuera Azure Machine Learning till en enhet med Azure IoT Edge
description: I den här självstudien skapar du en Azure Machine Learning-modell och distribuerar den sedan som en modul till en kantenhet
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 5bfbf4a432f720b683ded4c85530135d86b24eba
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "76773008"
---
# <a name="tutorial-deploy-azure-machine-learning-as-an-iot-edge-module-preview"></a>Distribuera Azure Machine Learning som en IoT Edge-modul (förhandsversion)

Använd Azure Notebooks för att utveckla en maskininlärningsmodul och distribuera den till en Linux-enhet som kör Azure IoT Edge.
Du kan använda IoT Edge-moduler för att distribuera kod som implementerar din affärslogik direkt till dina IoT Edge-enheter. Den här självstudiekursen vägleder dig genom processen att distribuera en Azure Machine Learning-modul som förutsäger när en enhet slutar fungera baserat på simulerade data om datortemperaturen. Mer information om Azure Machine Learning på IoT Edge finns i [Azure Machine Learning-dokumentation](../machine-learning/how-to-deploy-and-where.md).

>[!NOTE]
>Azure Machine Learning-moduler på Azure IoT Edge finns i en allmänt tillgänglig förhandsversion.

Azure Machine Learning-modulen som du skapar i den här självstudiekursen läser av miljödata som genereras av enheten och märker meddelandena som avvikande eller normala.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
>
> * Skapa en Azure Machine Learning-modul
> * Push-överför en modulbehållare till ett Azure-containerregister
> * Distribuera Azure Machine Learning-modul till din IoT Edge-enhet
> * Visa genererade data

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Krav

En Azure IoT Edge-enhet:

* Du kan använda en virtuell Azure-dator som en IoT Edge-enhet genom att följa stegen i snabbstarten för [Linux](quickstart-linux.md).
* Azure Machine Learning-modulen stöder inte Windows-behållare.
* Azure Machine Learning-modulen stöder inte ARM-processorer.

Molnresurser:

* En [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) på kostnadsfri nivå eller standardnivå i Azure.
* En Azure Machine Learning-arbetsyta. Följ instruktionerna i [Använd Azure-portalen för att komma igång med Azure Machine Learning](../machine-learning/tutorial-1st-experiment-sdk-setup.md) för att skapa en och lära dig hur du använder den.
  * Anteckna arbetsytans namn, resursgrupp och prenumerations-ID. Dessa värden är alla tillgängliga på arbetsytans översikt i Azure-portalen. Du använder dessa värden senare i självstudien för att ansluta en Azure-anteckningsbok till dina arbetsyteresurser.

## <a name="create-and-deploy-azure-machine-learning-module"></a>Skapa och distribuera Azure Machine Learning-modul

I det här avsnittet konverterar du utbildade maskininlärningsmodellfiler och till en Azure Machine Learning-behållare. Alla komponenter som krävs för dockeravbildningen finns i [AI Toolkit for Azure IoT Edge Git repo](https://github.com/Azure/ai-toolkit-iot-edge/tree/master/IoT%20Edge%20anomaly%20detection%20tutorial) (AI-verktyg för Azure IoT Edge Git-lagringsplats). Följ dessa steg för att överföra databasen till Microsoft Azure-anteckningsböcker för att skapa behållaren och skicka den till Azure Container Registry.

1. Navigera till dina Azure Notebooks-projekt. Du kan komma dit från din Azure Machine Learning-arbetsyta i [Azure-portalen](https://portal.azure.com) eller genom att logga in på [Microsoft Azure-anteckningsböcker](https://notebooks.azure.com/home/projects) med ditt Azure-konto.

2. Välj **Ladda upp GitHub Repo**.

3. Ange följande GitHub-databasnamn: `Azure/ai-toolkit-iot-edge`. Avmarkera rutan **Offentlig** om du vill hålla projektet privat. Välj **Importera**.

4. När importen är klar navigerar du till det nya **ai-toolkit-iot-edge-projektet** och öppnar **självstudiemappen för identifiering av IoT Edge-avvikelse.**

5. Kontrollera att projektet körs. Om inte väljer du **Kör på fri beräkning**.

   ![Kör på gratis beräkning](./media/tutorial-deploy-machine-learning/run-on-free-compute.png)

6. Öppna filen **aml_config/config.json.**

7. Redigera konfigurationsfilen för att inkludera värdena för ditt Azure-prenumerations-ID, en resursgrupp i din prenumeration och ditt Azure Machine Learning-arbetsytenamn. Du kan hämta alla dessa värden från avsnittet **Översikt på** din arbetsyta i Azure.

8. Spara konfigurationsfilen.

9. Öppna filen **00-anomaly-detection-tutorial.ipynb.**

10. När du uppmanas till det väljer du **Python 3.6-kärnan** och väljer sedan **Ange kernel**.

11. Redigera den första cellen i anteckningsboken enligt instruktionerna i kommentarerna. Använd samma resursgrupp, prenumerations-ID och arbetsytenamn som du har lagt till i konfigurationsfilen.

12. Kör cellerna i anteckningsboken genom att **Run** markera `Shift + Enter`dem och välja Kör eller trycka på .

    >[!TIP]
    >Vissa av cellerna i den hjälpbara självstudien är valfria, eftersom de skapar resurser som vissa användare kanske har eller kanske inte har ännu, till exempel en IoT Hub. Om du placerar din befintliga resursinformation i den första cellen visas fel om du kör cellerna som skapar nya resurser eftersom Azure inte skapar dubblettresurser. Detta är bra, och du kan ignorera fel eller hoppa över dessa valfria avsnitt helt.

Genom att slutföra alla steg i anteckningsboken har du tränat en avvikelseidentifieringsmodell, skapat den som en Docker-behållaravbildning och skicka den avbildningen till Azure Container Registry. Sedan har du testat modellen och slutligen distribuerat den till din IoT Edge-enhet.

## <a name="view-container-repository"></a>Visa behållardatabas

Kontrollera att behållaravbildningen har skapats och lagrats i Azure-behållarregistret som är associerat med din maskininlärningsmiljö. Anteckningsboken som du använde i föregående avsnitt gav automatiskt behållaravbildningen och registerautentiseringsuppgifterna till IoT Edge-enheten, men du bör veta var de lagras så att du kan hitta informationen själv senare.

1. Navigera till din Machine Learning-tjänstarbetsyta i [Azure-portalen.](https://portal.azure.com)

2. I avsnittet **Översikt** visas arbetsyteinformationen och dess associerade resurser. Välj **registervärdet,** som ska vara ditt arbetsområdesnamn följt av slumptal.

3. Välj **Databaser**i behållarregistret . Du bör se en databas som kallas **tempanomalydetection** som skapades av anteckningsboken du körde i det tidigare avsnittet.

4. Välj **tempanomalydektection**. Du bör se att databasen har en tagg: **1**.

   Nu när du känner till registrets namn, databasnamn och tagg vet du behållarens fullständiga avbildningssökväg. Bildbanor ser ut som ** \<\>registry_name .azurecr.io/tempanomalydetection:1**. Du kan använda avbildningssökvägen för att distribuera containern till IoT Edge-enheter.

5. Välj **Access-nycklar**i behållarregistret . Du bör se ett antal åtkomstuppgifter, inklusive **inloggningsservern** och **användarnamnet**och **lösenordet** för en administratörsanvändare.

   Dessa autentiseringsuppgifter kan ingå i distributionsmanifestet så att IoT Edge-enheten kan hämta containeravbildningar från registret.

Nu vet du var machine learning-behållaravbildningen lagras. I nästa avsnitt går du igenom steg för att visa behållaren som körs som en modul på din IoT Edge-enhet.

## <a name="view-the-generated-data"></a>Visa genererade data

Du kan visa meddelanden som skapas av varje IoT Edge-modul och du kan visa meddelanden som skickas till din IoT Hub.

### <a name="view-data-on-your-iot-edge-device"></a>Visa data på din IoT Edge-enhet

Du kan visa de meddelanden som skickas från varje enskild modul på din IoT Edge-enhet.

Du kan behöva `sudo` använda för förhöjda behörigheter för att köra `iotedge` kommandon. När du loggar ut och loggar in igen på enheten uppdateras dina behörigheter automatiskt.

1. Visa alla moduler på din IoT Edge-enhet.

   ```cmd/sh
   iotedge list
   ```

2. Visa meddelanden som skickas från en specifik enhet. Använd modulnamnet från utdata från det föregående kommandot.

   ```cmd/sh
   iotedge logs <module_name> -f
   ```

### <a name="view-data-arriving-at-your-iot-hub"></a>Visa data som inkommer till din IoT Hub

Du kan visa de meddelanden som enheten till molnet tar emot med hjälp av [Azure IoT Hub-tillägget för Visual Studio-kod](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit).

Följande steg visar hur du ställer in Visual Studio Code för att övervaka meddelanden från enhet till moln som kommer till din IoT Hub.

1. Välj **IoT Hub-enheter** i Visual Studio Code.

2. Välj **...** och sedan alternativet för att **ställa in IoT Hub-anslutningssträngen** på menyn.

   ![Ange IoT-hubbens anslutningssträng](./media/tutorial-deploy-machine-learning/set-connection.png)

3. Ange anslutningssträngen iothubowner för din IoT Hub i textrutan som visas överst på sidan. Enhetens IoT Edge-enhet bör visas i listan över IoT Hub-enheter.

4. Välj **...** igen och välj sedan **Starta övervakning inbyggd händelseslutpunkt**.

5. Kontrollera meddelandena som kommer från tempSensor var femte sekund. Meddelandetexten innehåller en egenskap som kallas **anomali**, som machinelearningmodulen ger ett sant eller falskt värde. Egenskapen **AzureMLResponse** innehåller värdet "OK" om modellen har körts med lyckat resultat.

   ![Azure Machine Learning-svar i meddelandetext](./media/tutorial-deploy-machine-learning/ml-output.png)

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att fortsätta med nästa rekommenderade artikel kan du behålla de resurser och konfigurationer som du skapat och använda dem igen. Du kan även fortsätta att använda samma IoT Edge-enhet som en testenhet.

Annars kan du ta bort de lokala konfigurationerna och de Azure-resurser som du har skapat i den här artikeln för att därigenom undvika kostnader.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen har du distribuerat en IoT Edge-modul som tillhandahålls av Azure Machine Learning. Du kan fortsätta med någon av följande självstudiekurser om du vill veta mer hur Azure IoT Edge kan förvandla dina data till affärsinsikter.

> [!div class="nextstepaction"]
> [Klassificera bilder med Custom Vision-tjänsten](tutorial-deploy-custom-vision.md)
