---
title: 'Självstudie: Distribuera Azure Machine Learning till en enhet – Azure IoT Edge'
description: I den här självstudien skapar du en Azure Machine Learning modell och distribuerar den sedan som en modul till en gräns enhet
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 3f1bd4ce5b701652318679f3277bc7c9109fa529
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457636"
---
# <a name="tutorial-deploy-azure-machine-learning-as-an-iot-edge-module-preview"></a>Distribuera Azure Machine Learning som en IoT Edge-modul (förhandsversion)

Använd Azure Notebooks för att utveckla en Machine Learning-modul och distribuera den till en Linux-enhet som kör Azure IoT Edge. 

Du kan använda IoT Edge-moduler för att distribuera kod som implementerar din affärslogik direkt till dina IoT Edge-enheter. Den här självstudiekursen vägleder dig genom processen att distribuera en Azure Machine Learning-modul som förutsäger när en enhet slutar fungera baserat på simulerade data om datortemperaturen. Mer information om Azure Machine Learning på IoT Edge finns i [Azure Machine Learning dokumentation](../machine-learning/service/how-to-deploy-to-iot.md).

Azure Machine Learning-modulen som du skapar i den här självstudiekursen läser av miljödata som genereras av enheten och märker meddelandena som avvikande eller normala.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa en Azure Machine Learning-modul
> * Push-överför en modulbehållare till ett Azure-containerregister
> * Distribuera Azure Machine Learning-modul till din IoT Edge-enhet
> * Visa genererade data

>[!NOTE]
>Azure Machine Learning-moduler på Azure IoT Edge finns i en allmänt tillgänglig förhandsversion.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Krav

En Azure IoT Edge-enhet:

* Du kan använda en virtuell Azure-dator som en IoT Edge enhet genom att följa stegen i snabb starten för [Linux](quickstart-linux.md).
* Azure Machine Learning-modulen stöder inte Windows-behållare.
* Azure Machine Learning modulen stöder inte ARM-processorer.

Molnresurser:

* En [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) på kostnadsfri nivå eller standardnivå i Azure.
* En Azure Machine Learning-arbetsyta. Följ anvisningarna i [använda Azure Portal för att komma igång med Azure Machine Learning](../machine-learning/service/quickstart-get-started.md) för att skapa en och lär dig hur du använder den.
   * Anteckna arbets ytans namn, resurs grupp och prenumerations-ID. Dessa värden är alla tillgängliga i Översikt över arbets ytan i Azure Portal. Du kommer att använda dessa värden senare i självstudien för att ansluta en Azure-anteckningsbok till arbets ytans resurser. 


## <a name="create-and-deploy-azure-machine-learning-module"></a>Skapa och Distribuera Azure Machine Learning modul

I det här avsnittet konverterar du tränade Machine Learning-modeller och till en Azure Machine Learning behållare. Alla komponenter som krävs för dockeravbildningen finns i [AI Toolkit for Azure IoT Edge Git repo](https://github.com/Azure/ai-toolkit-iot-edge/tree/master/IoT%20Edge%20anomaly%20detection%20tutorial) (AI-verktyg för Azure IoT Edge Git-lagringsplats). Följ de här stegen för att ladda upp lagrings platsen till Microsoft Azure Notebooks för att skapa behållaren och skicka den till Azure Container Registry.


1. Navigera till dina Azure Notebooks-projekt. Du kan komma dit från din Azure Machine Learning arbets yta i [Azure Portal](https://portal.azure.com) eller genom att logga in på [Microsoft Azure Notebooks](https://notebooks.azure.com/home/projects) med ditt Azure-konto.

2. Välj **Ladda upp GitHub lagrings platsen**.

3. Ange följande namn på GitHub-databasen: `Azure/ai-toolkit-iot-edge`. Avmarkera den **offentliga** rutan om du vill hålla ditt projekt privat. Välj **Importera**. 

4. När importen är färdig navigerar du till det nya **AI-Toolkit-IoT-Edge** -projektet och öppnar mappen **IoT Edge självstudie för avvikelse identifiering** . 

5. Kontrol lera att projektet körs. Annars väljer du **kör på kostnads fri beräkning**.

   ![Kör vid kostnads fri beräkning](./media/tutorial-deploy-machine-learning/run-on-free-compute.png)

6. Öppna filen **aml_config/config.JSON** .

7. Redigera konfigurations filen så att den innehåller värdena för ditt Azure-prenumerations-ID, en resurs grupp i din prenumeration och din Azure Machine Learning arbets ytans namn. Du kan hämta alla dessa värden från **översikts** avsnittet på din arbets yta i Azure. 

8. Spara konfigurations filen.

9. Öppna filen **00-#-Detection-självstudie-självstudie. ipynb** .

10. När du uppmanas väljer du den **Python 3,6** -kernel som du väljer **Ange kernel**.

11. Redigera den första cellen i antecknings boken enligt anvisningarna i kommentarerna. Använd samma resurs grupp, prenumerations-ID och namn på arbets ytan som du har lagt till i konfigurations filen.

12. Kör cellerna i antecknings boken genom att markera dem och välja **Kör** eller tryck på `Shift + Enter`.

    >[!TIP]
    >Några av cellerna i själv studie kursen om avvikelse identifiering är valfria, eftersom de skapar resurser som vissa användare kan eller ännu inte har, till exempel en IoT Hub. Om du infogar din befintliga resursinformation i den första cellen får du fel meddelanden om du kör cellerna som skapar nya resurser eftersom Azure inte skapar några dubbla resurser. Detta är bra och du kan ignorera felen eller hoppa över dessa valfria avsnitt helt och hållet. 

Genom att slutföra alla steg i antecknings boken har du tränat en avvikelse identifierings modell, skapat den som en Docker-behållar avbildning och publicerade avbildningen till Azure Container Registry. Sedan testade du modellen och distribuerar den slutligen till din IoT Edge-enhet. 

## <a name="view-container-repository"></a>Visa behållarens lagrings plats

Kontrol lera att behållar avbildningen har skapats och lagrats i Azure Container Registry som är associerat med din Machine Learning-miljö. Den antecknings bok som du använde i föregående avsnitt angav automatiskt behållar avbildningen och autentiseringsuppgifterna för din IoT Edge enhet, men du bör veta var de lagras så att du kan hitta informationen igen senare. 

1. I [Azure Portal](https://portal.azure.com)navigerar du till Machine Learning service-arbetsytan. 

2. I **översikts** avsnittet visas information om arbets ytan och dess tillhör ande resurser. Välj **registervärdet** , som ska vara namnet på din arbets yta följt av slumpmässiga tal. 

3. I behållar registret väljer du **databaser**. Du bör se en databas med namnet **tempanomalydetection** som har skapats av den antecknings bok som du körde i föregående avsnitt. 

4. Välj **tempanomalydetection**. Du bör se att lagrings platsen har en tagg: **1**. 

   Nu när du känner till register namnet, databas namnet och taggen, känner du till den fullständiga avbildnings Sök vägen för behållaren. Bild Sök vägar ser ut som **\<registry_name\>. azurecr.io/tempanomalydetection:1**. Du kan använda avbildnings Sök vägen för att distribuera behållaren till IoT Edge enheter. 

5. I behållar registret väljer du **åtkomst nycklar**. Du bör se ett antal autentiseringsuppgifter, till exempel **inloggnings Server** och **användar namn**och **lösen ord** för en administratörs användare.

   Dessa autentiseringsuppgifter kan inkluderas i distributions manifestet för att ge din IoT Edge enhet åtkomst till att hämta behållar avbildningar från registret. 

Nu vet du var Machine Learning behållar avbildningen lagras. Nästa avsnitt går igenom stegen för att Visa behållaren som körs som en modul på din IoT Edge-enhet. 

## <a name="view-generated-data"></a>Visa genererade data

Du kan visa meddelanden som skapas av varje IoT Edge-modul och du kan visa meddelanden som skickas till din IoT Hub.

### <a name="view-data-on-your-iot-edge-device"></a>Visa data på din IoT Edge-enhet

Du kan visa de meddelanden som skickas från varje enskild modul på din IoT Edge-enhet.

Du kan behöva använda `sudo` för att få utökade behörigheter för att köra `iotedge`-kommandon. Om du loggar ut och loggar in igen på enheten uppdateras dina behörigheter automatiskt.

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

4. Välj **...** igen och välj **starta övervakning inbyggd händelse slut punkt**.

5. Kontrollera meddelandena som kommer från tempSensor var femte sekund. Meddelande texten innehåller en egenskap som kallas **avvikelse**, som machinelearningmodule ger med värdet true eller false. Egenskapen **AzureMLResponse** innehåller värdet "OK" om modellen har körts med lyckat resultat.

   ![Azure Machine Learning svar i meddelande texten](./media/tutorial-deploy-machine-learning/ml-output.png)

## <a name="clean-up-resources"></a>Rensa resurser

Om du tänker fortsätta till nästa rekommenderade artikel kan du behålla de resurser och konfigurationer du har skapat och använda dem igen. Du kan även fortsätta att använda samma IoT Edge-enhet som en testenhet.

Annars kan du ta bort de lokala konfigurationerna och de Azure-resurser som du har skapat i den här artikeln för att därigenom undvika kostnader.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen har du distribuerat en IoT Edge-modul som tillhandahålls av Azure Machine Learning. Du kan fortsätta med någon av följande självstudiekurser om du vill veta mer hur Azure IoT Edge kan förvandla dina data till affärsinsikter.

> [!div class="nextstepaction"]
> [Klassificera bilder med Custom Vision-tjänsten](tutorial-deploy-custom-vision.md)

