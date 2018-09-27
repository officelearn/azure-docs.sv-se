---
title: Distribuera modeller till IoT Edge-enheter från Azure Machine Learning-tjänsten | Microsoft Docs
description: Lär dig hur du distribuerar en modell från Azure Machine Learning-tjänsten till Azure IoT Edge-enheter. Distribuera en modell till en edge-enhet kan du bedöma data på enheten i stället för att skicka det till molnet och väntar på ett svar.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: shipatel
author: shivanipatel
manager: cgronlun
ms.reviewer: larryfr
ms.date: 09/24/2018
ms.openlocfilehash: 03d692ddfd6f41fd559e9b921f0214a9cd2ada22
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2018
ms.locfileid: "47225233"
---
# <a name="prepare-to-deploy-models-on-iot-edge"></a>Förbereda för distribution av modeller på IoT Edge

I det här dokumentet lär du dig hur du använder Azure Machine Learning-tjänsten för att förbereda en tränad modell för distribution till en Azure IoT Edge-enhet.

En Azure IoT Edge-enhet är en Linux eller Windows-baserad enhet som kör Azure IoT Edge-körningen. Machine learning-modeller kan distribueras till dessa enheter som IoT Edge-moduler. Distribuera en modell till en IoT Edge-enhet gör att enheten använder modellen direkt, i stället för att skicka data till molnet för bearbetning. Du får kortare svarstider och överföra mindre data.

Innan du distribuerar en modell till en edge-enhet, använder du stegen i det här dokumentet för att förbereda modellen och enheten.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har ett konto kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

* En arbetsyta för Azure Machine Learning-tjänsten. Om du vill skapa ett, Följ stegen i den [Kom igång med Azure Machine Learning-tjänsten](quickstart-get-started.md) dokumentet.

* En utvecklingsmiljö. Mer information finns i den [så här konfigurerar du en utvecklingsmiljö](how-to-configure-environment.md) dokumentet.

* En [Azure IoT Hub](../../iot-hub/iot-hub-create-through-portal.md) i Azure-prenumerationen. 

* En tränad modell. Ett exempel på hur du tränar en modell finns i den [tränar en modell för klassificering av avbildning med Azure Machine Learning](tutorial-train-models-with-aml.md) dokumentet.

## <a name="prepare-the-iot-device"></a>Förbereda IoT-enhet

Om du vill veta hur du registrerar din enhet och installera IoT-runtime, följer du stegen i den [Snabbstart: Distribuera ditt första IoT Edge-modul till en enhet med Linux x64](../../iot-edge/quickstart-linux.md) dokumentet.

## <a name="register-the-model"></a>Registrera modellen

Azure IoT Edge-moduler är baserade på behållaravbildningar. Använd följande steg för att distribuera din modell till en IoT Edge-enhet genom att registrera din modell i Azure Machine Learning-arbetsytan och skapa en Docker-avbildning. 

> [!IMPORTANT]
> Hoppa över steg 3 i det här fallet om du har använt Azure Machine Learning för att träna din modell som redan kan registreras i din arbetsyta.

1. Initiera arbetsytan och läsa in config.json-fil:

    ```python
    from azureml.core  import Workspace

    #Load existing workspace from the config file info.
    ws  = Workspace.from_config()
    ```    

1. Registrera modellen på din arbetsyta. Ersätt standardtexten med din modell sökväg, namn, taggar och beskrivning:

    ```python
    from azureml.core.model import Model
    model = Model.register(model_path = "model.pkl", # this path points to the local file
                        model_name = "best_model", # the model gets registered as this name
                        tags = {'attribute': "myattribute", 'classification': "myclassification"},
                        description = "My awesome model",
                        workspace = ws)
    ```    

1. Hämta den registrerade modellen: 

    ```python
    from azureml.core.model import Model

    model_name = "best_model"
    model = Model(ws, model_name)                     
    ```    

## <a name="create-a-docker-image"></a>Skapa en Docker-avbildning

1. Skapa en **bedömning skriptet** med namnet `score.py`. Den här filen används för att köra modellen i avbildningen. Det måste innehålla följande funktioner:

    * Den `init()` funktion, som vanligtvis läser in modellen i ett globala objekt. Den här funktionen körs en gång när Docker-behållaren startas. 

    * Den `run(input_data)` funktionen använder modellen för att förutsäga ett värde baserat på indata. Indata och utdata kör vanligtvis använder JSON för serialisering och deserialisering, men andra format som stöds.

    Ett exempel finns i den [bild klassificering självstudien](tutorial-deploy-models-with-aml.md#make-script).

1. Skapa en **miljöfil** med namnet `myenv.yml`. Den här filen är en specifikation för Conda-miljö och visar en lista över alla beroenden som krävs av skript och modellen. Ett exempel finns i den [bild klassificering självstudien](tutorial-deploy-models-with-aml.md#make-myenv).

1. Konfigurera Docker-avbildning med den `score.py` och `myenv.yml` filer:
    
    ```python
    from azureml.core.image import Image, ContainerImage
    
    #Image configuration
    image_config = ContainerImage.image_configuration( runtime = "python", 
                           execution_script = "score.py",
                           conda_file = "myenv.yml", 
                           tags = {"attributes", "calssification"},
                           description = "Image that contains my model",
                           
                        )
    ```    

1. Skapa avbildningen med hjälp av modell- och bildfiler konfigurationen:

    ```python
    image = ContainerImage.create (name = "myimage", 
                           models = [model], #this is the model object
                           image_config = image_config,
                           workspace = ws
                        )
    ```     

    Skapa avbildningar tar cirka 5 minuter.

## <a name="get-the-container-registry-credentials"></a>Hämta autentiseringsuppgifter för registret

Azure IoT måste autentiseringsuppgifterna för behållarregistret som Azure Machine Learning-tjänsten lagrar docker-avbildningar i. Använd följande steg för att hämta autentiseringsuppgifter:

1. Logga in på [Azure Portal](https://portal.azure.com/signin/index).

1. Gå till din Azure Machine Learning-arbetsyta och välj __översikt__. Gå till behållaren för registerinställningarna genom att välja den __registret__ länk.

    ![En bild av registerposten behållare](./media/how-to-deploy-to-iot/findregisteredcontainer.png)

1. Välj en gång i behållarregistret **åtkomstnycklar** och sedan aktivera administratörsanvändare.

    ![En bild av skärmen för åtkomst-nycklar](./media/how-to-deploy-to-iot/findaccesskey.png)

1. Spara värdena för inloggningsserver, användarnamn och lösenord. 

   Dessa autentiseringsuppgifter krävs för att ge IoT edge-Enhetsåtkomst till avbildningar i ditt privata behållarregister.

## <a name="next-steps"></a>Nästa steg

Du har slutfört förberedelser för distribution. Nu kan använda stegen i den [distribuera Azure IoT Edge-moduler från Azure portal](../../iot-edge/how-to-deploy-modules-portal.md) dokumentet för att distribuera till edge-enhet. När du konfigurerar den __registerinställningar__ för enheten, använder du de autentiseringsuppgifter som du tidigare konfigurerat.
