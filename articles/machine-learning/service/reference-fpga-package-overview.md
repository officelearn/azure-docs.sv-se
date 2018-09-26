---
title: FPGA-paketet för maskinvaruacceleration för Azure Machine Learning
description: Läs mer om de python-paket som är tillgängliga för Azure Machine Learning-användare.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: reference
ms.reviewer: jmartens
ms.author: tedway
author: tedway
ms.date: 05/07/2018
ROBOTS: NOINDEX
ms.openlocfilehash: cb1abdce3bbd7349695ece70ff336c7e513c0918
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2018
ms.locfileid: "47162193"
---
# <a name="azure-machine-learning-hardware-acceleration-package"></a>Azure Machine Learning-maskinvaruacceleration-paketet

>[!Note]
>**Den här artikeln är inaktuell.** Det här paketet FPGA upphörde. Stöd för den här funktionen har lagts till i Azure ML-SDK. Stöd för det här paketet går ut om inkrementellt. [Visa tidslinje för support](overview-what-happened-to-workbench.md#timeline). Lär dig om uppdateras [stöd för FPGA](concept-accelerate-with-fpgas.md).

Maskinvaruacceleration för Azure Machine Learning-paket är ett Python-pip-installation tillägg för Azure Machine Learning som gör att dataanalytiker och AI-utvecklare att snabbt:

+ Funktionalisera bilder med en quantized version av ResNet-50

+ Träna klassificerare som baseras på de här funktionerna

+ Distribuera modeller till [fältet programmable gate matriser (FPGA)](concept-accelerate-with-fpgas.md) på Azure för extremt låg latens inferensjobb

## <a name="prerequisites"></a>Förutsättningar

1. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

1. Ett konto för Azure Machine Learning-modellhantering. Mer information om hur du skapar kontot finns i den [installationen av Azure Machine Learning-Quickstart och Workbench](../desktop-workbench/quickstart-installation.md) dokumentet. 

1. Paketet måste installeras. 

 
## <a name="how-to-install-the-package"></a>Så här installerar du paketet

1. Ladda ned och installera den senaste versionen av [Git](https://git-scm.com/downloads).

2. Installera [Anaconda (Python 3.6)](https://conda.io/miniconda.html)

   För att hämta en förinställd Anaconda-miljö, använder du följande kommando i Git-Kommandotolken:

    ```
    git clone https://aka.ms/aml-real-time-ai
    ```
1. För att skapa miljön, öppna en **Anaconda fråga** och använder du följande kommando:

    ```
    conda env create -f aml-real-time-ai/environment.yml
    ```

1. Om du vill aktivera miljön, använder du följande kommando:

    ```
    conda activate amlrealtimeai
    ```

## <a name="sample-code"></a>Exempelkod

Den här exempelkoden vägleder dig genom att använda SDK: N för att distribuera en modell till en FPGA.

1. Importera paketet:
   ```python
   import amlrealtimeai
   from amlrealtimeai import resnet50
   ```

1. Förbearbeta avbildningen:
   ```python 
   from amlrealtimeai.resnet50.model import LocalQuantizedResNet50
   model_path = os.path.expanduser('~/models')
   model = LocalQuantizedResNet50(model_path)
   print(model.version)
   ```

1. Funktionalisera avbildningarna:
   ```python 
   from amlrealtimeai.resnet50.model import LocalQuantizedResNet50
   model_path = os.path.expanduser('~/models')
   model = LocalQuantizedResNet50(model_path)
   print(model.version)
   ```

1. Skapa en klassificerare:
   ```python
   model.import_graph_def(include_featurizer=False)
   print(model.classifier_input)
   print(model.classifier_output)
   ```

1. Skapa tjänstdefinitionen för:
   ```python
   from amlrealtimeai.pipeline import ServiceDefinition, TensorflowStage, BrainWaveStage
   save_path = os.path.expanduser('~/models/save')
   service_def_path = os.path.join(save_path, 'service_def.zip')

   service_def = ServiceDefinition()
   service_def.pipeline.append(TensorflowStage(tf.Session(), in_images, image_tensors))
   service_def.pipeline.append(BrainWaveStage(model))
   service_def.pipeline.append(TensorflowStage(tf.Session(), model.classifier_input, model.classifier_output))
   service_def.save(service_def_path)
   print(service_def_path)
   ```
 
1. Förbered modellen ska köras på en FPGA:
   ```python
   from amlrealtimeai import DeploymentClient

   subscription_id = "<Your Azure Subscription ID>"
   resource_group = "<Your Azure Resource Group Name>"
   model_management_account = "<Your AzureML Model Management Account Name>"

   model_name = "resnet50-model"
   service_name = "quickstart-service"

   deployment_client = DeploymentClient(subscription_id, resource_group, model_management_account)
   ```

1. Distribuera modellen ska köras på en FPGA:
   ```python
   service = deployment_client.get_service_by_name(service_name)
   model_id = deployment_client.register_model(model_name, service_def_path)

   if(service is None):
      service = deployment_client.create_service(service_name, model_id)    
   else:
      service = deployment_client.update_service(service.id, model_id)
   ```

1. Skapa klienten:
    ```python
   from amlrealtimeai import PredictionClient
   client = PredictionClient(service.ipAddress, service.port)  
   ```

1. Anropa API:
   ```python
   image_file = R'C:\path_to_file\image.jpg'
   results = client.score_image(image_file)
   ```

## <a name="reporting-issues"></a>Rapporteringsproblem

Använd den [forum](https://aka.ms/aml-forum) rapportera eventuella problem du stöter på med paketet.

## <a name="next-steps"></a>Nästa steg

[Distribuera en modell som en webbtjänst på en FPGA](how-to-deploy-fpga-web-service.md)