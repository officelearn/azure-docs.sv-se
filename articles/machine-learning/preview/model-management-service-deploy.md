---
title: Azure Machine Learning modellen Management Web Service-distributionen | Microsoft Docs
description: "Det här dokumentet beskriver steg som ingår i distributionen av en maskininlärningsmodell med hjälp av Azure Machine Learning modellen Management."
services: machine-learning
author: raymondl
ms.author: raymondl, aashishb
manager: hjerez
ms.reviewer: jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 01/03/2018
ms.openlocfilehash: c5942bed5e171c23864ef3ee10665273e04640a6
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2018
---
# <a name="deploying-a-machine-learning-model-as-a-web-service"></a>Distribuera en Maskininlärningsmodell som en webbtjänst

Azure Machine Learning modellen Management tillhandahåller gränssnitt för att distribuera modeller som av Docker-baserat webbtjänsterna. Du kan distribuera modeller som du skapar med ramar, till exempel Spark, Microsoft kognitiva Toolkit (CNTK), Keras, Tensorflow och Python. 

Det här dokumentet beskriver steg för att distribuera modeller som webbtjänster som använder Azure Machine Learning modellen Management kommandoradsgränssnittet (CLI).

## <a name="deploying-web-services"></a>Distribuera webbtjänster
Med CLIs kan använda du webbtjänster som ska köras på den lokala datorn eller på ett kluster.

Vi rekommenderar att du börjar med en lokal distribution. Du först verifiera att din modell koden fungerar och sedan distribuera webbtjänsten till ett kluster för produktionsskala användning. Mer information om hur du konfigurerar din miljö för klusterdistribution finns [modellen konfigurationstjänsten](deployment-setup-configuration.md). 

Här följer stegen för distributionen:
1. Använd din sparade utbildade, Machine Learning-modell
2. Skapa ett schema för din webbtjänsten indata och utdata
3. Skapa en behållare med Docker-baserade avbildning
4. Skapa och distribuera webbtjänsten

### <a name="1-save-your-model"></a>1. Spara din modell
Börja med filen sparad tränad modell, till exempel mymodel.pkl. Filnamnstillägget varierar beroende på vilken plattform som används för att träna och spara modellen. 

Du kan använda Python's Pickle biblioteket för att spara en tränad modell till en fil som ett exempel. Här är en [exempel](http://scikit-learn.org/stable/modules/model_persistence.html) med Iris datauppsättningen:

```python
import pickle
from sklearn import datasets
iris = datasets.load_iris()
X, y = iris.data, iris.target
clf = linear_model.LogisticRegression()
clf.fit(X, y)  
saved_model = pickle.dumps(clf)
```

### <a name="2-create-a-schemajson-file"></a>2. Skapa en schema.json-fil
Det här steget är valfritt. 

Skapa ett schema för att automatiskt verifiera indata och utdata för webbtjänsten. CLIs också använda schemat för att generera en Swagger-dokument för webbtjänsten.

Importera följande bibliotek för att skapa schemat:

```python
from azureml.api.schema.dataTypes import DataTypes
from azureml.api.schema.sampleDefinition import SampleDefinition
from azureml.api.realtime.services import generate_schema
```
Därefter definiera indatavariabler, till exempel en Spark dataframe, Pandas dataframe eller NumPy matris. I följande exempel används en Numpy matris:

```python
inputs = {"input_array": SampleDefinition(DataTypes.NUMPY, yourinputarray)}
generate_schema(run_func=run, inputs=inputs, filepath='service_schema.json')
```
I följande exempel används en Spark dataframe:

```python
inputs = {"input_df": SampleDefinition(DataTypes.SPARK, yourinputdataframe)}
generate_schema(run_func=run, inputs=inputs, filepath='service_schema.json')
```

I följande exempel används en PANDAS dataframe:

```python
inputs = {"input_df": SampleDefinition(DataTypes.PANDAS, yourinputdataframe)}
generate_schema(run_func=run, inputs=inputs, filepath='service_schema.json')
```

### <a name="3-create-a-scorepy-file"></a>3. Skapa en score.py-fil
Du kan ange en score.py-fil som läser in din modell och returnerar förutsägelse resultat med hjälp av modellen.

Filen måste innehålla två funktioner: init och köra.

Lägg till följande kod högst upp i filen score.py att aktivera data samling funktioner som hjälper till att samla in indata- och förutsägelse modelldata

```python
from azureml.datacollector import ModelDataCollector
```

Kontrollera [modell datainsamling](how-to-use-model-data-collection.md) mer information om hur du använder den här funktionen.

#### <a name="init-function"></a>Init-funktion
Funktionen init används för att läsa in sparade modellen.

Exempel på en enkel init-funktion modellen:

```python
def init():  
    from sklearn.externals import joblib
    global model, inputs_dc, prediction_dc
    model = joblib.load('model.pkl')

    inputs_dc = ModelDataCollector('model.pkl',identifier="inputs")
    prediction_dc = ModelDataCollector('model.pkl', identifier="prediction")
```

#### <a name="run-function"></a>Kör funktionen
Funktionen kör använder modellen och indata för att returnera en förutsägelse.

Exempel på ett enkelt köra funktionen bearbetningen av indata och returnera resultatet förutsägelse:

```python
def run(input_df):
    global clf2, inputs_dc, prediction_dc
    try:
        prediction = model.predict(input_df)
        inputs_dc.collect(input_df)
        prediction_dc.collect(prediction)
        return prediction
    except Exception as e:
        return (str(e))
```

### <a name="4-register-a-model"></a>4. Registrera en modell
Följande kommando används för att registrera en modell som skapades i steg 1 ovan,

```
az ml model register --model [path to model file] --name [model name]
```

### <a name="5-create-manifest"></a>5. Skapa manifestet
Följande kommando hjälper dig att skapa ett manifest för modellen

```
az ml manifest create --manifest-name [your new manifest name] -f [path to code file] -r [runtime for the image, e.g. spark-py]
```
Du kan lägga till en tidigare registrerad modell manifestet genom att använda argumentet `--model-id` eller `-i` i kommandot ovan. Du kan ange flera modeller med ytterligare -i argument.

### <a name="6-create-an-image"></a>6. Skapa en avbildning 
Du kan skapa en avbildning med alternativet för att ha skapat manifestet innan. 

```
az ml image create -n [image name] -manifest-id [the manifest ID]
```

Men du kan skapa manifestet och bild med ett enda kommando. 

```
az ml image create -n [image name] --model-file [model file or folder path] -f [code file, e.g. the score.py file] -r [the runtime eg.g. spark-py which is the Docker container image base]
```

>[!NOTE]
>Mer information om parametrar för kommandot Skapa anger -h i slutet av kommandot exempelvis az ml bild -h.


### <a name="7-create-and-deploy-the-web-service"></a>7. Skapa och distribuera webbtjänsten
Distribuera tjänsten med hjälp av följande kommando:

```
az ml service create realtime --image-id <image id> -n <service name>
```

>[!NOTE] 
>Du kan också använda ett enda kommando för att utföra stegen för 4. Använd -h med tjänsten skapa mer information.

### <a name="8-test-the-service"></a>8. Testa tjänsten
Använd följande kommando för att hämta information om hur du anropa tjänsten:

```
az ml service usage realtime -i <service id>
```

Anropa tjänsten med hjälp av funktionen Kör från Kommandotolken:

```
az ml service run realtime -i <service id> -d "{\"input_df\": [INPUT DATA]}"
```

I följande exempel visar ett exempel på en Iris webbtjänst:

```
az ml service run realtime -i <service id> -d "{\"input_df\": [{\"sepal length\": 3.0, \"sepal width\": 3.6, \"petal width\": 1.3, \"petal length\":0.25}]}"
```

## <a name="next-steps"></a>Nästa steg
Nu när du har testat webbtjänsten ska köras lokalt kan distribuera du den till ett kluster för storskalig användning. Mer information om hur du skapar ett kluster för web service-distributionen finns [modellen Management Configuration](deployment-setup-configuration.md). 
