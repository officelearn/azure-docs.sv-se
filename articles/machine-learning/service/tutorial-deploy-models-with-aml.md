---
title: 'Självstudie: Distribuera en bildklassificeringsmodell i Azure Container Instances (ACI) med Azure Machine Learning-tjänsten'
description: Den här självstudien visar hur du använder Azure Machine Learning-tjänsten till att distribuera en bildklassificeringsmodell med scikit-learn i en Python Jupyter Notebook.  Självstudien är del två i en serie med två delar.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: tutorial
author: hning86
ms.author: haining
ms.reviewer: sgilley
ms.date: 09/24/2018
ms.openlocfilehash: ad6b296543cffedb215c87b1fc893a22b29a3052
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2018
ms.locfileid: "49427356"
---
# <a name="tutorial-2--deploy-an-image-classification-model-in-azure-container-instance-aci"></a>Självstudie nr 2: Distribuera en bildklassificeringsmodell i Azure Container Instances (ACI)

Självstudien är **del två i en självstudieserie i två delar**. I den [föregående självstudien](tutorial-train-models-with-aml.md) tränade du maskininlärningsmodeller och registrerade sedan en modell på din arbetsyta i molnet.  

Nu är du redo att distribuera modellen som en webbtjänst i [Azure Container Instances](https://docs.microsoft.com/azure/container-instances/) (ACI). En webbtjänst är en avbildning, i det här fallet en Docker-avbildning, som innehåller både bedömningslogiken och själva modellen. 

I den här delen av självstudien använder du Azure Machine Learning-tjänsten (förhandsversion) till att:

> [!div class="checklist"]
> * Konfigurera din testmiljö
> * Hämta modellen från din arbetsyta
> * Testa modellen lokalt
> * Distribuera modellen till ACI
> * Testa den distribuerade modellen

ACI är inte idealiskt för produktionsdistributioner, men den fungerar bra vid testning och för att förstå arbetsflödet. För skalbara produktionsdistributioner kan du använda [Azure Kubernetes Service](how-to-deploy-to-aks.md).

## <a name="get-the-notebook"></a>Hämta anteckningsboken

Denna självstudie finns tillgänglig som en [Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/02.deploy-models.ipynb). Kör anteckningsboken `02.deploy-models.ipynb` antingen i Azure Notebooks eller i din egen Jupyter Notebook-server.

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-in-azure-notebook.md)]


## <a name="prerequisites"></a>Nödvändiga komponenter

Slutför modellträningen i anteckningsboken [Självstudie 1: Träna en bildklassificeringsmodell med Azure Machine Learning-tjänsten](tutorial-train-models-with-aml.md).  


## <a name="set-up-the-environment"></a>Konfigurera miljön

Börja med att konfigurera en testmiljö.

### <a name="import-packages"></a>Importera paket

Importera de Python-paket som behövs i självstudien.

```python
%matplotlib inline
import numpy as np
import matplotlib
import matplotlib.pyplot as plt
 
import azureml
from azureml.core import Workspace, Run

# display the core SDK version number
print("Azure ML SDK Version: ", azureml.core.VERSION)
```

### <a name="retrieve-the-model"></a>Hämta modellen

Du registrerade en modell på din arbetsyta i föregående självstudie. Nu kan läsa in arbetsytan och ladda ner modellen till din lokala katalog.


```python
from azureml.core import Workspace
from azureml.core.model import Model

ws = Workspace.from_config()
model=Model(ws, 'sklearn_mnist')
model.download(target_dir = '.')
import os 
# verify the downloaded model file
os.stat('./sklearn_mnist_model.pkl')
```

## <a name="test-model-locally"></a>Testa modellen lokalt

Innan du distribuerar bör du kontrollera att din modell fungerar lokalt genom att:
* Läsa in testdata
* Förutsäga testdata
* Undersöka felmatrisen

### <a name="load-test-data"></a>Läsa in testdata

Läs in testdatan från katalogen **./data/** som skapades i träningssjälvstudien.

```python
from utils import load_data

# note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the neural network converge faster

X_test = load_data('./data/test-images.gz', False) / 255.0
y_test = load_data('./data/test-labels.gz', True).reshape(-1)

```

### <a name="predict-test-data"></a>Förutsäga testdata

Mata in testdatauppsättningen i modellen för att få förutsägelser.

```python
import pickle
from sklearn.externals import joblib

clf = joblib.load('./sklearn_mnist_model.pkl')
y_hat = clf.predict(X_test)
```

###  <a name="examine-the-confusion-matrix"></a>Undersöka felmatrisen

Generera en felmatris om du vill se hur många exempel från testuppsättningen som har klassificerats på rätt sätt. Observera det felaktigt klassificerade värdet för de felaktiga förutsägelserna. 

```python
from sklearn.metrics import confusion_matrix

conf_mx = confusion_matrix(y_test, y_hat)
print(conf_mx)
print('Overall accuracy:', np.average(y_hat == y_test))
```

Utdatan visar felmatrisen:

    [[ 960    0    1    2    1    5    6    3    1    1]
     [   0 1112    3    1    0    1    5    1   12    0]
     [   9    8  920   20   10    4   10   11   37    3]
     [   4    0   17  921    2   21    4   12   20    9]
     [   1    2    5    3  915    0   10    2    6   38]
     [  10    2    0   41   10  770   17    7   28    7]
     [   9    3    7    2    6   20  907    1    3    0]
     [   2    7   22    5    8    1    1  950    5   27]
     [  10   15    5   21   15   27    7   11  851   12]
     [   7    8    2   13   32   13    0   24   12  898]]
    Overall accuracy: 0.9204
   

Använd `matplotlib` till att visa felmatrisen som ett diagram. I det här diagrammet motsvarar X-axeln faktiska värden och Y-axeln visar de förväntade värdena. Färgen i varje rutnät representerar felfrekvensen. Ju ljusare färg, desto högre är felfrekvensen. Till exempel är många 5:or felaktigt klassificerade som 3:or. Därför visas ett ljust rutnät vid (5,3).

```python
# normalize the diagnal cells so that they don't overpower the rest of the cells when visualized
row_sums = conf_mx.sum(axis=1, keepdims=True)
norm_conf_mx = conf_mx / row_sums
np.fill_diagonal(norm_conf_mx, 0)

fig = plt.figure(figsize=(8,5))
ax = fig.add_subplot(111)
cax = ax.matshow(norm_conf_mx, cmap=plt.cm.bone)
ticks = np.arange(0, 10, 1)
ax.set_xticks(ticks)
ax.set_yticks(ticks)
ax.set_xticklabels(ticks)
ax.set_yticklabels(ticks)
fig.colorbar(cax)
plt.ylabel('true labels', fontsize=14)
plt.xlabel('predicted values', fontsize=14)
plt.savefig('conf.png')
plt.show()
```

![felmatris](./media/tutorial-deploy-models-with-aml/confusion.png)

## <a name="deploy-as-web-service"></a>Distribuera som en webbtjänst

När du har testat modellen och är nöjd med resultatet, ska du distribuera modellen som en webbtjänst i ACI. 

Skapa rätt miljö för ACI genom att ange följande:
* Ett bedömningsskript som visar hur man ska använda modellen
* En miljöfil som visar vilka paket som måste installeras
* En konfigurationsfil för att skapa ACI
* Modellen som du tränade tidigare

<a name="make-script"></a>

### <a name="create-scoring-script"></a>Skapa ett bedömningsskript

Skapa ett bedömningsskript med namnet score.py. Det kommer att användas av webbtjänstanropet för att visa hur modellen ska användas.

Du måste inkludera två obligatoriska funktioner i bedömningsskriptet:
* Funktionen `init()`, som vanligtvis läser in modellen i ett globalt objekt. Den här funktionen körs endast en gång när Docker-containern startas. 

* Funktionen `run(input_data)` använder modellen till att förutsäga ett värde som baseras på indatan. Indatan och utdatan i körningen använder vanligtvis JSON för serialisering och deserialisering, men andra format stöds också.

```python
%%writefile score.py
import json
import numpy as np
import os
import pickle
from sklearn.externals import joblib
from sklearn.linear_model import LogisticRegression

from azureml.core.model import Model

def init():
    global model
    # retreive the path to the model file using the model name
    model_path = Model.get_model_path('sklearn_mnist')
    model = joblib.load(model_path)

def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    y_hat = model.predict(data)
    return json.dumps(y_hat.tolist())
```

<a name="make-myenv"></a>

### <a name="create-environment-file"></a>Skapa en miljöfil

Därefter skapar du en miljöfil med namnet myenv.yml, som anger alla paketberoenden i skriptet. Filen används för att säkerställa att alla dessa beroenden är installerade i Docker-avbildningen. Modellen måste innehålla `scikit-learn` och `azureml-sdk`.

```python
from azureml.core.conda_dependencies import CondaDependencies 

myenv = CondaDependencies()
myenv.add_conda_package("scikit-learn")

with open("myenv.yml","w") as f:
    f.write(myenv.serialize_to_string())
```
Granska `myenv.yml`-filens innehåll.

```python
with open("myenv.yml","r") as f:
    print(f.read())
```

### <a name="create-configuration-file"></a>Skapa konfigurationsfilen

Skapa en konfigurationsfil för konfigurationen samt ange hur många processorer och gigabyte RAM-minne som behövs till din ACI-container. Även om det beror på din modell, räcker standardvärdet 1 kärna och 1 GB RAM-minne oftast för de flesta modeller. Om du märker att du behöver fler senare måste du återskapa avbildningen och distribuera om tjänsten.

```python
from azureml.core.webservice import AciWebservice

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1, 
                                               memory_gb=1, 
                                               tags={"data": "MNIST",  "method" : "sklearn"}, 
                                               description='Predict MNIST with sklearn')
```

### <a name="deploy-in-aci"></a>Distribuera i ACI
Uppskattad tidsåtgång: **cirka 7–8 minuter**

Konfigurera avbildningen och distribuera. Följande kod går igenom de här stegen:

1. Skapa en avbildning med hjälp av:
   * Bedömningsfilen (`score.py`)
   * Miljöfilen (`myenv.yml`)
   * Modellfilen
1. Registrera avbildningen på arbetsytan. 
1. Skicka avbildningen till ACI-containern.
1. Starta en container i ACI med avbildningen.
1. Hämta webbtjänstens HTTP-slutpunkt.


```python
%%time
from azureml.core.webservice import Webservice
from azureml.core.image import ContainerImage

# configure the image
image_config = ContainerImage.image_configuration(execution_script="score.py", 
                                                  runtime="python", 
                                                  conda_file="myenv.yml")

service = Webservice.deploy_from_model(workspace=ws,
                                       name='sklearn-mnist-svc',
                                       deployment_config=aciconfig,
                                       models=[model],
                                       image_config=image_config)

service.wait_for_deployment(show_output=True)
```

Hämta bedömningswebbtjänstens HTTP-slutpunkt, som accepterar REST-klientanrop. Den här slutpunkten kan delas med alla som vill testa webbtjänsten eller integrera den i ett program. 

```python
print(service.scoring_uri)
```


## <a name="test-deployed-service"></a>Testa distribuerad tjänst

Tidigare bedömde du alla testdata med den lokala versionen av modellen. Nu kan du testa den distribuerade modellen med ett slumpmässigt urval av 30 bilder från testdatan.  

Följande kod går igenom de här stegen:
1. Skicka datan som en JSON-matris till webbtjänsten i ACI. 

1. Använd SDK:ernas `run`-API till att anropa tjänsten. Du kan också göra rå-anrop med valfritt HTTP-verktyg, till exempel curl.

1. Skriv ut de returnerade förutsägelserna och rita dem tillsammans med indatabilderna. Rött teckensnitt och en inverterad bild (vit på svart) används för att visa de felklassificerade exemplen. 

 Eftersom modellens precision är hög, kan du behöva köra följande kod ett par gånger innan du kan se ett felklassificerat exempel.

```python
import json

# find 30 random samples from test set
n = 30
sample_indices = np.random.permutation(X_test.shape[0])[0:n]

test_samples = json.dumps({"data": X_test[sample_indices].tolist()})
test_samples = bytes(test_samples, encoding = 'utf8')

# predict using the deployed model
result = json.loads(service.run(input_data=test_samples))

# compare actual value vs. the predicted values:
i = 0
plt.figure(figsize = (20, 1))

for s in sample_indices:
    plt.subplot(1, n, i + 1)
    plt.axhline('')
    plt.axvline('')
    
    # use different color for misclassified sample
    font_color = 'red' if y_test[s] != result[i] else 'black'
    clr_map = plt.cm.gray if y_test[s] != result[i] else plt.cm.Greys
    
    plt.text(x=10, y =-10, s=result[i], fontsize=18, color=font_color)
    plt.imshow(X_test[s].reshape(28, 28), cmap=clr_map)
    
    i = i + 1
plt.show()
```

Här är resultatet av ett slumpmässigt urval av testbilder: ![resultat](./media/tutorial-deploy-models-with-aml/results.png)

Du kan också skicka en rå HTTP-begäran för att testa webbtjänsten.

```python
import requests
import json

# send a random row from the test set to score
random_index = np.random.randint(0, len(X_test)-1)
input_data = "{\"data\": [" + str(list(X_test[random_index])) + "]}"

headers = {'Content-Type':'application/json'}

# for AKS deployment you'd need to the service key in the header as well
# api_key = service.get_key()
# headers = {'Content-Type':'application/json',  'Authorization':('Bearer '+ api_key)} 

resp = requests.post(service.scoring_uri, input_data, headers=headers)

print("POST to url", service.scoring_uri)
#print("input data:", input_data)
print("label:", y_test[random_index])
print("prediction:", resp.text)
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill behålla resursgruppen och arbetsytan för andra självstudier och undersökningar, kan du nöja dig med att ta bort ACI-distributionen med hjälp av det här API-anropet:

```python
service.delete()
```

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]


## <a name="next-steps"></a>Nästa steg

I den här självstudien för Azure Machine Learning-tjänsten har du använt Python för att:

> [!div class="checklist"]
> * Konfigurera din testmiljö
> * Hämta modellen från din arbetsyta
> * Testa modellen lokalt
> * Distribuera modellen till ACI
> * Testa den distribuerade modellen
 
Du kan också prova självstudien [Automatiskt algoritmval](tutorial-auto-train-models.md) om du vill se hur Azure Machine Learning-tjänsten automatiskt väljer och finjusterar den bästa algoritmen för din modell och sedan skapar modellen åt dig.
