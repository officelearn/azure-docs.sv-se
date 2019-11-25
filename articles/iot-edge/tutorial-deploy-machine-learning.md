---
title: 'Tutorial: Deploy Azure Machine Learning to a device - Azure IoT Edge'
description: In this tutorial, you create an Azure Machine Learning model, then deploy it as a module to an edge device
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

Use Azure Notebooks to develop a machine learning module and deploy it to a Linux device running Azure IoT Edge. 

Du kan använda IoT Edge-moduler till att distribuera kod som implementerar din affärslogik direkt på dina IoT Edge-enheter. Den här självstudiekursen vägleder dig genom processen att distribuera en Azure Machine Learning-modul som förutsäger när en enhet slutar fungera baserat på simulerade data om datortemperaturen. For more information about Azure Machine Learning on IoT Edge, see [Azure Machine Learning documentation](../machine-learning/service/how-to-deploy-to-iot.md).

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


## <a name="prerequisites"></a>Krav

En Azure IoT Edge-enhet:

* You can use an Azure virtual machine as an IoT Edge device by following the steps in the quickstart for [Linux](quickstart-linux.md).
* The Azure Machine Learning module doesn't support Windows containers.
* The Azure Machine Learning module doesn't support ARM processors.

Molnresurser:

* En [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) på kostnadsfri nivå eller standardnivå i Azure.
* En Azure Machine Learning-arbetsyta. Follow the instructions in [Use the Azure portal to get started with Azure Machine Learning](../machine-learning/service/quickstart-get-started.md) to create one and learn how to use it.
   * Make a note of the workspace name, resource group, and subscription ID. These values are all available on the workspace overview in the Azure portal. You'll use these values later in the tutorial to connect an Azure notebook to your workspace resources. 


## <a name="create-and-deploy-azure-machine-learning-module"></a>Create and deploy Azure Machine Learning module

In this section, you convert trained machine learning model files and into an Azure Machine Learning container. Alla komponenter som krävs för dockeravbildningen finns i [AI Toolkit for Azure IoT Edge Git repo](https://github.com/Azure/ai-toolkit-iot-edge/tree/master/IoT%20Edge%20anomaly%20detection%20tutorial) (AI-verktyg för Azure IoT Edge Git-lagringsplats). Follow these steps to upload that repository into Microsoft Azure Notebooks to create the container and push it to Azure Container Registry.


1. Navigate to your Azure Notebooks projects. You can get there from your Azure Machine Learning workspace in the [Azure portal](https://portal.azure.com) or by signing in to [Microsoft Azure Notebooks](https://notebooks.azure.com/home/projects) with your Azure account.

2. Select **Upload GitHub Repo**.

3. Provide the following GitHub repository name: `Azure/ai-toolkit-iot-edge`. Uncheck the **Public** box if you want to keep your project private. Select **Import**. 

4. Once the import is finished, navigate into the new **ai-toolkit-iot-edge** project and open the **IoT Edge anomaly detection tutorial** folder. 

5. Verify that your project is running. If not, select **Run on Free Compute**.

   ![Run on free compute](./media/tutorial-deploy-machine-learning/run-on-free-compute.png)

6. Open the **aml_config/config.json** file.

7. Edit the config file to include the values for your Azure subscription ID, a resource group in your subscription, and your Azure Machine Learning workspace name. You can get all these values from the **Overview** section of your workspace in Azure. 

8. Save the config file.

9. Open the **00-anomaly-detection-tutorial.ipynb** file.

10. When prompted, select the **Python 3.6** kernel then select **Set Kernel**.

11. Edit the first cell in the notebook according to the instructions in the comments. Use the same resource group, subscription ID, and workspace name that you added to the config file.

12. Run the cells in the notebook by selecting them and selecting **Run** or pressing `Shift + Enter`.

    >[!TIP]
    >Some of the cells in the anomaly detection tutorial notebook are optional, because they create resources that some users may or may not have yet, like an IoT Hub. If you put your existing resource information in the first cell, you'll receive errors if you run the cells that create new resources because Azure won't create duplicate resources. This is fine, and you can ignore the errors or skip those optional sections entirely. 

By completing all the steps in the notebook, you trained an anomaly detection model, built it as a Docker container image, and pushed that image to Azure Container Registry. Then, you tested the model and finally deployed it to your IoT Edge device. 

## <a name="view-container-repository"></a>View container repository

Check that your container image was successfully created and stored in the Azure container registry associated with your machine learning environment. The notebook that you used in the previous section automatically provided the container image and the registry credentials to your IoT Edge device, but you should know where they're stored so that you can find the information yourself later. 

1. In the [Azure portal](https://portal.azure.com), navigate to your Machine Learning service workspace. 

2. The **Overview** section lists the workspace details as well its associated resources. Select the **Registry** value, which should be your workspace name followed by random numbers. 

3. In the container registry, select **Repositories**. You should see a repository called **tempanomalydetection** that was created by the notebook you ran in the earlier section. 

4. Select **tempanomalydetection**. You should see that the repository has one tag: **1**. 

   Now that you know the registry name, repository name, and tag, you know the full image path of the container. Image paths look like **\<registry_name\>.azurecr.io/tempanomalydetection:1**. You can use the image path to deploy this container to IoT Edge devices. 

5. In the container registry, select **Access keys**. You should see a number of access credentials, including **Login server** and the **Username**, and **Password** for an admin user.

   These credentials can be included in the deployment manifest to give your IoT Edge device access to pull container images from the registry. 

Now you know where the Machine Learning container image is stored. The next section walks through steps to view the container running as a module on your IoT Edge device. 

## <a name="view-generated-data"></a>Visa genererade data

Du kan visa meddelanden som skapas av varje IoT Edge-modul och du kan visa meddelanden som skickas till din IoT Hub.

### <a name="view-data-on-your-iot-edge-device"></a>Visa data på din IoT Edge-enhet

Du kan visa de meddelanden som skickas från varje enskild modul på din IoT Edge-enhet.

You may need to use `sudo` for elevated permissions to run `iotedge` commands. Signing out and signing back in to your device automatically updates your permissions.

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

4. Select **...** again then select **Start Monitoring Built-in Event Endpoint**.

5. Kontrollera meddelandena som kommer från tempSensor var femte sekund. The message body contains a property called **anomaly**, which the machinelearningmodule provides with a true or false value. Egenskapen **AzureMLResponse** innehåller värdet "OK" om modellen har körts med lyckat resultat.

   ![Azure Machine Learning response in message body](./media/tutorial-deploy-machine-learning/ml-output.png)

## <a name="clean-up-resources"></a>Rensa resurser

Om du tänker fortsätta till nästa rekommenderade artikel kan du behålla de resurser och konfigurationer du har skapat och använda dem igen. Du kan även fortsätta att använda samma IoT Edge-enhet som en testenhet.

Annars kan du ta bort de lokala konfigurationerna och de Azure-resurser som du har skapat i den här artikeln för att därigenom undvika kostnader.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen har du distribuerat en IoT Edge-modul som tillhandahålls av Azure Machine Learning. Du kan fortsätta med någon av följande självstudiekurser om du vill veta mer hur Azure IoT Edge kan förvandla dina data till affärsinsikter.

> [!div class="nextstepaction"]
> [Klassificera bilder med Custom Vision-tjänsten](tutorial-deploy-custom-vision.md)

