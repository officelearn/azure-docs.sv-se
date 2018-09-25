---
title: Distribuera webbtjänster till Azure Container Instances (ACI) – Azure Machine Learning
description: Lär dig hur du distribuerar en tränad modell som en webbtjänst på Azure Container Instances (ACI) med Azure Machine Learning-tjänsten. Den här artikeln visar tre olika sätt att distribuera en modell på ACI. De skiljer sig åt i hur många rader med kod och den kontroll du har i namngivning delar av distributionen.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: raymondl
author: raymondlaghaeian
ms.reviewer: sgilley
ms.date: 09/24/2018
ms.openlocfilehash: 5a62d4b0b324d8b2536e408132210f07f08e8bb8
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46958704"
---
# <a name="deploy-web-services-to-azure-container-instances"></a>Distribuera webbtjänster till Azure Container Instances 

Du kan distribuera den tränade modellen som en webbtjänst på [Azure Container Instances](https://azure.microsoft.com/services/container-instances/) (ACI) [Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service/) (AKS), IoT edge-enhet, eller [fältet programmable gate matriser (FPGA)](concept-accelerate-with-fpgas.md) 

ACI är vanligtvis billigare än AKS och kan ställas in i 4 – 6 rader med kod. ACI är perfekt alternativ för att testa distributioner. Senare, när du är redo att använda dina modeller och webbtjänster för användning i stor skala, produktion, kan du [distribuera dem till AKS](how-to-deploy-to-aks.md).

Den här artikeln visar tre olika sätt att distribuera en modell på ACI. De skiljer sig åt i hur många rader med kod och den kontroll du har i namngivning delar av distributionen. Från metoden med ett minimum av kod och kontroll i metoden med de flesta kod och kontroll är ACI-alternativen:

* Distribuera från modellen med `Webservice.deploy()` 
* Distribuera från registrerade modellen med `Webservice.deploy_from_model()`
* Distribuera registrerade modell från avbildningen med hjälp av `Webservice.deploy_from_image()`

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.


## <a name="prerequisites"></a>Förutsättningar

- Azure Machine Learning-arbetsyta och Azure Machine Learning-SDK för Python installerat. Lär dig hur du hämtar dessa krav med hjälp av den [Kom igång med Azure Machine Learning Snabbstart](quickstart-get-started.md).

- Objekt för Azure Machine Learning-arbetsyta

    ```python
    from azureml.core import Workspace
    ws = Workspace.from_config()
    ```

- En modell för att distribuera. Exemplen i det här dokumentet använder modellen som skapats när du följer den ”[tränar en modell](tutorial-train-models-with-aml.md)” självstudien. Om du inte använder den här modellen kan du ändra stegen att hänvisa till ditt namn.  Du måste också skriva egna bedömningsskriptet för att köra din modell.


## <a name="configure-an-image"></a>Konfigurera en avbildning

Konfigurera Docker-avbildningen som används för att lagra alla modellfiler som.
1. Skapa ett bedömningsskript (score.py) [använder dessa instruktioner](tutorial-deploy-models-with-aml.md#create-scoring-script)

1. Skapa en miljöfil (myenv.yml) [använder dessa instruktioner](tutorial-deploy-models-with-aml.md#create-environment-file) 

1. Använd dessa två filer för att konfigurera Docker-avbildningen i Python med hjälp av SDK på följande sätt:

    ```python
    from azureml.core.image import ContainerImage

    image_config = ContainerImage.image_configuration(execution_script = "score.py",
                                                      runtime = "python",
                                                      conda_file = "myenv.yml",
                                                      description = "Image with mnist model",
                                                      tags = {"data": "mnist", "type": "classification"}
                                                     )
    ```

## <a name="configure-the-aci-container"></a>Konfigurera ACI-behållaren

Konfigurera ACI-behållaren genom att ange hur många processorer och gigabyte RAM-minne som behövs för din ACI-behållare. I stället för en kärna och 1 gigabyte ram räcker för många modeller. Om du anser att behöver du fler senare återskapa avbildningen och distribuera om tjänsten.  

```python
from azureml.core.webservice import AciWebservice

aciconfig = AciWebservice.deploy_configuration(cpu_cores = 1, 
                                               memory_gb = 1, 
                                               tags = {"data": "mnist", "type": "classification"},
                                               description = 'Handwriting recognition')
```

## <a name="register-a-model"></a>Registrera en modell

> Hoppa över det här kravet om du är [distribuera från en modellfil](#deploy-from-model-file) (`Webservice.deploy()`).

Registrera en modell ska använda [ `Webservice.deploy_from_model` ](#deploy-from-registered-model) eller [ `Webservice.deploy_from_image` ](#deploy-from-image). Eller om du redan har en registrerad modell, hämta.

### <a name="retrieve-a-registered-model"></a>Hämta en registrerad modell
Om du använder Azure Machine Learning för att träna din modell kan modellen redan registreras i din arbetsyta.  Till exempel det sista steget i den [tränar en modell](tutorial-train-models-with-aml.md) självstudien] registrerade modellen.  Du kan sedan hämta den registrerade modellen för distribution.

```python
from azureml.core.model import Model

model_name = "sklearn_mnist"
model=Model(ws, model_name)
```
  
### <a name="register-a-model-file"></a>Registrera en modellfil

Om din modell har skapats någon annanstans, kan du fortfarande registrera den på din arbetsyta.  Att registrera en modell, modellfilen (`sklearn_mnist_model.pkl` i det här exemplet) måste finnas i den aktuella arbetskatalogen. Registrera den fil som en modell som kallas `sklearn_mnist` på arbetsytan med `Model.register()`.
    
```python
from azureml.core.model import Model

model_name = "sklearn_mnist"
model = Model.register(model_path = "sklearn_mnist_model.pkl",
                        model_name = model_name,
                        tags = {"data": "mnist", "type": "classification"},
                        description = "Mnist handwriting recognition",
                        workspace = ws)
```


## <a name="option-1-deploy-from-model-file"></a>Alternativ 1: Distribuera från modellfilen

För att distribuera från en modellfil kräver minst mängd kod för att skriva, men ger även den minsta uppsättningen kontroll över vid namngivning av komponenter. Det här alternativet börjar med en modellfil och registrerar den till arbetsytan för dig.  Du kan inte namnge modellen eller associera taggar eller en beskrivning för den.  

Det här alternativet använder SDK-metoden Webservice.deploy().  

**Uppskattad tidsåtgång**: det tar cirka 6 – 7 minuter att distribuera.

1. Kontrollera att modellfilen finns i din lokala arbetskatalog.

1. Öppna de nödvändiga modellfilen, score.py och ändra den `init()` avsnitt:

    ```python
    def init():
        global model
        # retreive the local path to the model using the model name
        model_path = Model.get_model_path('sklearn_mnist_model.pkl')
        model = joblib.load(model_path)
    ```

1. Distribuera din modellfil.

    ```python
    from azureml.core.webservice import Webservice
    
    service_name = 'aci-mnist-1'
    service = Webservice.deploy(deployment_config = aciconfig,
                                    image_config = image_config,
                                    model_paths = ['sklearn_mnist_model.pkl'],
                                    name = service_name,
                                    workspace = ws)
    
    service.wait_for_deployment(show_output = True)
    print(service.state)
    ```

1. Du kan nu [testa webbtjänsten](#test-web-service).

## <a name="option-2-deploy-from-registered-model"></a>Alternativ 2: Distribuera från registrerade modellen

För att distribuera en registrerad modellfil tar några fler rader med kod och tillåter vissa kontroll över vid namngivning av utdata. Det här alternativet är ett praktiskt sätt att distribuera en registrerad modell som du redan har.  Men det går inte att du namnger Docker-avbildningen.  

Det här alternativet använder SDK-metoden Webservice.deploy_from_model().

**Uppskattad tidsåtgång**: distribution med det här alternativet tar cirka 8 minuter.

1. Kör kod för att konfigurera Docker-behållaren och ACI-behållaren och ange den registrerade modellen.

    ```python
    from azureml.core.webservice import Webservice

    service_name = 'aci-mnist-2'
    service = Webservice.deploy_from_model(deployment_config = aciconfig,
                                           image_config = image_config,
                                           models = [model], # this is the registered model object
                                           name = service_name,
                                           workspace = ws)
    service.wait_for_deployment(show_output = True)
    print(service.state)
    ```

1. Du kan nu [testa webbtjänsten](#test-web-service).

## <a name="option-3-deploy-from-image"></a>Alternativ 3: Distribuera från avbildningen

Distribuera en registrerad modell (`model`) med hjälp av `Webservice.deploy_from_image()`. Den här metoden kan du skapa Docker-avbildningen separat och sedan distribuera från avbildningen.

1. Skapa och registrera Docker-avbildningen under arbetsytan med hjälp av `ContainerImage.create()`

    Den här metoden ger dig större kontroll över avbildningen genom att skapa den i ett separat steg.  Den registrerade modellen (`model`) ingår i avbildningen.
    
    ```python
    from azureml.core.image import ContainerImage
    
    image = ContainerImage.create(name = "myimage1",
                                  models = [model], # this is the registered model object
                                  image_config = image_config,
                                  workspace = ws)
    
    image.wait_for_creation(show_output = True)
    ```
**Uppskattad tidsåtgång**: ca 3 minuter.

1. Distribuera Docker-avbildning som en tjänst med hjälp av `Webservice.deploy_from_image()`

    Nu distribuera avbildningen till ACI.  
    
    ```python
    from azureml.core.webservice import Webservice
    
    service_name = 'aci-mnist-3'
    service = Webservice.deploy_from_image(deployment_config = aciconfig,
                                                image = image,
                                                name = service_name,
                                                workspace = ws)
    service.wait_for_deployment(show_output = True)
    print(service.state)
    ```   
 
**Uppskattad tidsåtgång**: ca 3 minuter.

Den här metoden ger dig de flesta kontroll över hur du skapar och namnger komponenterna i distributionen.

Nu kan du testa webbtjänsten.

## <a name="test-the-web-service"></a>Testa webbtjänsten

Webbtjänsten är samma oavsett vilken metod du använde.  För att få förutsägelser kan använda den `run` metoden för tjänsten.  

```python
# Load Data
import os
import urllib

os.makedirs('./data', exist_ok = True)

urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz', filename = './data/test-images.gz')

from utils import load_data
X_test = load_data('./data/test-images.gz', False) / 255.0

from sklearn import datasets
import numpy as np
import json

# find 5 random samples from test set
n = 5
sample_indices = np.random.permutation(X_test.shape[0])[0:n]

test_samples = json.dumps({"data": X_test[sample_indices].tolist()})
test_samples = bytes(test_samples, encoding = 'utf8')

# predict using the deployed model
prediction = service.run(input_data = test_samples)
print(prediction)
```


## <a name="clean-up-resources"></a>Rensa resurser

Om du inte planerar att använda den här webbtjänsten tar du bort den så att du inte betala något.

```python
service.delete()
```

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [distribuera till Azure Kubernetes Service](how-to-deploy-to-aks.md) för distribueras i större skala. 
