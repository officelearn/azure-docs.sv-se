---
title: Konvertera experiment kod för maskin inlärning till produktions kod
titleSuffix: Azure Machine Learning
description: Lär dig hur du konverterar experimentell kod för maskin inlärning till produktions kod med MLOpsPython-kod mal len.
author: bjcmit
ms.author: brysmith
ms.service: machine-learning
ms.topic: tutorial
ms.date: 02/10/2020
ms.openlocfilehash: 7f5e24261fd5d006004a51186e22f6bfe1b8ab32
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/25/2020
ms.locfileid: "77589189"
---
# <a name="tutorial-convert-ml-experimental-code-to-production-code"></a>Självstudie: konvertera ML experimentell kod till produktions kod

Ett Machine Learning-projekt kräver experimentering där Hypotheses testas med flexibla verktyg som Jupyter Notebook med hjälp av verkliga data uppsättningar. När modellen är redo för produktion ska modell koden placeras i en produktions kod lagrings plats. I vissa fall måste modell koden konverteras till Python-skript som ska placeras i produktions kod lagrings platsen. I den här självstudien beskrivs en rekommenderad metod för att exportera experiment kod till Python-skript.  

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Rensa icke-väsentlig kod
> * ÅterJupyter Notebooks kod i functions
> * Skapa Python-skript för relaterade aktiviteter
> * Skapa enhets test

## <a name="prerequisites"></a>Förutsättningar

- Generera [MLOpsPython-mallen](https://github.com/microsoft/MLOpsPython/generate) och Använd `experimentation/Diabetes Ridge Regression Training.ipynb` och `experimentation/Diabetes Ridge Regression Scoring.ipynb` antecknings böcker. Dessa antecknings böcker används som exempel på konvertering från experiment till produktion.
- Installera nbconvert. Följ bara installations anvisningarna under avsnittet __Installera nbconvert__ på [installations](https://nbconvert.readthedocs.io/en/latest/install.html) sidan.

## <a name="remove-all-nonessential-code"></a>Ta bort all kod som inte behövs

Viss kod som skrivs under experimentet är endast avsedd för test ändamål. Det första steget för att konvertera experimentell kod till produktions koden är därför att ta bort den här icke-nödvändiga koden. Att ta bort icke-väsentlig kod gör också koden mer hanterbar. I det här avsnittet ska du ta bort kod från `experimentation/Diabetes Ridge Regression Training.ipynb` Notebook. Uttrycken som skriver ut formen på `X` och `y` och cellen som anropar `features.describe` är bara för data utforskning och kan tas bort. När du har tagit bort icke-väsentlig kod bör `experimentation/Diabetes Ridge Regression Training.ipynb` se ut så här utan markdown:

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import joblib
 
X, y = load_diabetes(return_X_y=True)

X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
data = {"train": {"X": X_train, "y": y_train},
        "test": {"X": X_test, "y": y_test}}

alpha = 0.5

reg = Ridge(alpha=alpha)
reg.fit(data["train"]["X"], data["train"]["y"])

preds = reg.predict(data["test"]["X"])
print("mse", mean_squared_error(preds, data["test"]["y"]))

model_name = "sklearn_regression_model.pkl"
joblib.dump(value=reg, filename=model_name)
```

## <a name="refactor-code-into-functions"></a>Omträds kod i functions

För det andra måste Jupyter-koden omstruktureras till functions. Omstrukturering av kod i funktioner gör enhets testningen enklare och gör koden mer hanterbar. I det här avsnittet kommer du att göra följande:
- Diabetes Ridge regression Training Notebook (`experimentation/Diabetes Ridge Regression Training.ipynb`)
- Antecknings boken diabetes Ridge regression poängsättning (`experimentation/Diabetes Ridge Regression Scoring.ipynb`)

### <a name="refactor-diabetes-ridge-regression-training-notebook-into-functions"></a>Diabetes Ridge regression Training Notebook in Functions
I `experimentation/Diabetes Ridge Regression Training.ipynb`utför du följande steg:

1. Skapa en funktion som kallas `train_model`, som tar parametrarna `data` och `alpha` och returnerar en modell. 
1. Kopiera koden under rubrikerna "träna modell on Training set" och "validera modell på validerings uppsättning" i funktionen `train_model`.

Funktionen `train_model` bör se ut som följande kod:

```python
def train_model(data, alpha):
    reg = Ridge(alpha=alpha)
    reg.fit(data["train"]["X"], data["train"]["y"])
    preds = reg.predict(data["test"]["X"])
    print("mse", mean_squared_error(
        preds, data["test"]["y"]))
    return reg
```

När den `train_model` funktionen har skapats ersätter du koden under rubrikerna "träna modell på inlärnings uppsättning" och "validera modell på validerings uppsättning" med följande instruktion:

```python
reg = train_model(data, alpha)
```

Den tidigare instruktionen anropar funktionen `train_model` som skickar `data`-och `alpha` parametrar och returnerar modellen.

I `experimentation/Diabetes Ridge Regression Training.ipynb`utför du följande steg:

1. Skapa en ny funktion som kallas `main`, som inte tar några parametrar och som inte returnerar något.
1. Kopiera koden under rubrikerna "Läs in data", "dela data i utbildning och validerings uppsättningar" och "Spara modell" i funktionen `main`.
1. Kopiera det nyligen skapade anropet till `train_model` i `main`-funktionen.

Funktionen `main` bör se ut som följande kod:

```python
def main():

    model_name = "sklearn_regression_model.pkl"
    alpha = 0.5
    
    X, y = load_diabetes(return_X_y=True)

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}

    reg = train_model(data, alpha)

    joblib.dump(value=reg, filename=model_name)
```

När `main` funktionen har skapats ersätter du all kod under rubrikerna "Läs in data", "dela data i inlärnings-och validerings uppsättningar" och "Spara modell" tillsammans med det nya anropet till `train_model` med följande uttryck:

```python
main()
```

Efter omfactoring bör `experimentation/Diabetes Ridge Regression Training.ipynb` se ut som följande kod utan markdown:

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import joblib


def train_model(data, alpha):
    reg = Ridge(alpha=alpha)
    reg.fit(data["train"]["X"], data["train"]["y"])
    preds = reg.predict(data["test"]["X"])
    print("mse", mean_squared_error(
        preds, data["test"]["y"]))
    return reg

def main():

    model_name = "sklearn_regression_model.pkl"
    alpha = 0.5
    
    X, y = load_diabetes(return_X_y=True)

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}

    reg = train_model(data, alpha)

    joblib.dump(value=reg, filename=model_name)

main()
```

### <a name="refactor-diabetes-ridge-regression-scoring-notebook-into-functions"></a>Diabetes Ridge Regressions-antecknings bok till Functions
I `experimentation/Diabetes Ridge Regression Scoring.ipynb`utför du följande steg:

1. Skapa en ny funktion som kallas `init`, vilket inte tar några parametrar och returnerar ingenting.
1. Kopiera koden under rubriken "läsa modell" till funktionen `init`.

Funktionen `init` bör se ut som följande kod:

```python
def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)
```

När `init` funktionen har skapats ersätter du all kod under rubriken "belastnings modell" med ett enda anrop till `init` enligt följande:

```python
init()
```

I `experimentation/Diabetes Ridge Regression Scoring.ipynb`utför du följande steg:

1. Skapa en ny funktion som kallas `run`, som tar `raw_data` och `request_headers` som parametrar och returnerar en ord lista med resultat enligt följande:

    ```python
    {"result": result.tolist()}
    ```

1. Kopiera koden under rubrikerna "Förbered data" och "Poäng data" i `run` funktionen.

    Funktionen `run` bör se ut som följande kod (kom ihåg att ta bort de instruktioner som anger variablerna `raw_data` och `request_headers`, som kommer att användas senare när `run`-funktionen anropas):

    ```python
    def run(raw_data, request_headers):
        data = json.loads(raw_data)["data"]
        data = numpy.array(data)
        result = model.predict(data)

        return {"result": result.tolist()}
    ```

När `run` funktionen har skapats ersätter du all kod under rubrikerna "Förbered data" och "Poäng data" med följande kod:

```python
raw_data = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(raw_data, request_header)
print("Test result: ", prediction)
```
Föregående kod ställer in variabler `raw_data` och `request_header`, anropar `run`-funktionen med `raw_data` och `request_header`och skriver ut förutsägelserna.

Efter omfactoring bör `experimentation/Diabetes Ridge Regression Scoring.ipynb` se ut som följande kod utan markdown:

```python
import json
import numpy
from azureml.core.model import Model
import joblib

def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)

def run(raw_data, request_headers):
    data = json.loads(raw_data)["data"]
    data = numpy.array(data)
    result = model.predict(data)

    return {"result": result.tolist()}

init()
test_row = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(test_row, {})
print("Test result: ", prediction)
```

## <a name="combine-related-functions-in-python-files"></a>Kombinera relaterade funktioner i python-filer
Tredje, relaterade funktioner måste slås samman till python-filer för att bättre kunna använda kod åter användning. I det här avsnittet skapar du python-filer för följande antecknings böcker:
- Diabetes Ridge regression Training Notebook (`experimentation/Diabetes Ridge Regression Training.ipynb`)
- Antecknings boken diabetes Ridge regression poängsättning (`experimentation/Diabetes Ridge Regression Scoring.ipynb`)

### <a name="create-python-file-for-the-diabetes-ridge-regression-training-notebook"></a>Skapa python-fil för notebook diabetes Ridge regression Training
Konvertera antecknings boken till ett körbart skript genom att köra följande instruktion i en kommando tolk som använder nbconvert-paketet och sökvägen till `experimentation/Diabetes Ridge Regression Training.ipynb`:

```
jupyter nbconvert -- to script "Diabetes Ridge Regression Training.ipynb" –output train
```

Ta bort alla kommentarer när antecknings boken har konverterats till `train.py`. `train.py`-filen bör se ut som följande kod:

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import joblib


def train_model(data, alpha):
    reg = Ridge(alpha=alpha)
    reg.fit(data["train"]["X"], data["train"]["y"])
    preds = reg.predict(data["test"]["X"])
    print("mse", mean_squared_error(
        preds, data["test"]["y"]))
    return reg

def main():
    model_name = "sklearn_regression_model.pkl"
    alpha = 0.5
    
    X, y = load_diabetes(return_X_y=True)

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}

    reg = train_model(data, alpha)

    joblib.dump(value=reg, filename=model_name)

main()
```

`train.py`-filen som finns i katalogen `diabetes_regression/training` i MLOpsPython-lagringsplatsen stöder kommando rads argument (dvs `build_id`, `model_name`och `alpha`). Stöd för kommando rads argument kan läggas till i `train.py`-filen för att stödja dynamiska modell namn och `alpha` värden, men det är inte nödvändigt för att koden ska kunna köras.

### <a name="create-python-file-for-the-diabetes-ridge-regression-scoring-notebook"></a>Skapa python-fil för notebook diabetes Ridge regression Poängsättning
Konvertera din bärbara dator till ett körbart skript genom att köra följande instruktion i en kommando tolk som använder nbconvert-paketet och sökvägen till `experimentation/Diabetes Ridge Regression Scoring.ipynb`:

```
jupyter nbconvert -- to script "Diabetes Ridge Regression Scoring.ipynb" –output score
```

Ta bort alla kommentarer när antecknings boken har konverterats till `score.py`. `score.py`-filen bör se ut som följande kod:

```python
import json
import numpy
from azureml.core.model import Model
import joblib

def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)

def run(raw_data, request_headers):
    data = json.loads(raw_data)["data"]
    data = numpy.array(data)
    result = model.predict(data)

    return {"result": result.tolist()}

init()
test_row = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(test_row, request_header)
print("Test result: ", prediction)
```

Funktionen `train_model` behöver ändra för att instansiera en global variabel modell så att den visas i hela skriptet. Lägg till följande-instruktion i början av `init`-funktionen:

```python
global model
```

När du har lagt till föregående instruktion bör `init`-funktionen se ut så här:

```python
def init():
    global model

    # load the model from file into a global object
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)
```

## <a name="create-unit-tests-for-each-python-file"></a>Skapa enhets test för varje python-fil
Fjärde måste du skapa enhets test för varje python-fil som gör kod mer robust och enklare att underhålla. I det här avsnittet ska du skapa ett enhets test för en av funktionerna i `train.py`.

`train.py` innehåller två funktioner: `train_model` och `main`. Varje funktion behöver ett enhets test, men vi skapar bara ett enda enhets test för funktionen `train_model` med Pytest-ramverket i den här självstudien.  Pytest är inte det enda testnings ramverket python unit, men det är en av de som används oftast. Mer information finns på [Pytest](https://pytest.org).

Ett enhets test innehåller vanligt vis tre huvudsakliga åtgärder:
- Ordna objekt – skapa och konfigurera nödvändiga objekt
- Agera på ett objekt
- Assert förväntat

Ett vanligt villkor för `train_model` är när `data` och ett `alpha`-värde skickas. Det förväntade resultatet är att `Ridge.train` och `Ridge.predict` funktioner ska anropas. Eftersom metoder för Machine Learning-utbildning ofta inte körs snabbt, kommer anropet till `Ridge.train` att visas. Eftersom returvärdet för `Ridge.train` är ett skissat objekt, kommer vi också att modellera `Ridge.predict`. Enhets testet för `train_model` testar sändningen av `data` och ett `alpha` värde med det förväntade resultatet av `Ridge.train` och `Ridge.predict` funktioner som anropas med hjälp av skisser och Pytest-ramverket bör se ut som följande kod:

```python
import pytest
from code.training.train import train_model

class TestTrain:

    @staticmethod
    def test_train_model(mocker):
        # Arrange
        test_data = {"train": {"X": [[1, 2, 3]], "y": [0]},
                     "test": {"X": [[4, 5, 6]], "y": [0]}}
        test_alpha = 0.5
        mock_ridge_fit = mocker.patch('Ridge.fit')
        mock_ridge_predict = mocker.patch('Ridge.predict')

        # Act
        train_model(test_data, test_alpha)

        # Assert
        mock_ridge_fit.assert_called()
        mock_ridge_predict.assert_called()
```

## <a name="use-your-own-model-with-mlopspython-code-template"></a>Använd din egen modell med MLOpsPython-kod mal len
Om du har gått igenom stegen i den här guiden har du en uppsättning skript som motsvarar de tåg-/Poäng-/test-skript som finns i MLOpsPython-lagringsplatsen.  Enligt den struktur som anges ovan går följande steg igenom vad som behövs för att använda de här filerna för ditt eget Machine Learning-projekt:  

1.  Följ Komma igångs guiden
2.  Ersätt inlärnings koden
3.  Ersätt Poäng koden
4.  Uppdatera utvärderings koden

### <a name="follow-the-getting-started-guide"></a>Följ Komma igångs guiden
Följande kom igång-guide är nödvändig för att stödja infrastrukturen och pipeliner för att köra MLOpsPython.  Vi rekommenderar att du distribuerar MLOpsPython-koden i befintligt skick innan du lägger till din egen kod för att säkerställa att strukturen och pipelinen fungerar korrekt.  Det är också användbart att bekanta dig med lagrings platsens kod struktur.

### <a name="replace-training-code"></a>Ersätt inlärnings kod
Att ersätta den kod som används för att träna modellen och ta bort eller ersätta motsvarande enhets test krävs för att lösningen ska fungera med din egen kod.  Följ dessa steg särskilt:

1. Ersätt `diabetes_regression\training\train.py`. Det här skriptet tränar din modell lokalt eller på Azure ML-beräkningen.
1. Ta bort eller Ersätt test av utbildnings enhet som finns i `tests/unit/code_test.py`

### <a name="replace-score-code"></a>Ersätt Poäng kod
För att modellen ska kunna tillhandahålla funktioner för att få real tids härledning måste Poäng koden ersättas. MLOpsPython-mallen använder poängsättnings koden för att distribuera modellen för att utföra real tids resultat på ACI, AKS eller Web Apps.  Om du vill behålla poängen ersätter du `diabetes_regression/scoring/score.py`.

### <a name="update-evaluation-code"></a>Uppdatera utvärderings kod
MLOpsPython-mallen använder evaluate_model-skriptet för att jämföra prestanda hos den nytränade modellen och den aktuella produktions modellen baserat på ett kvadratvärde i genomsnitt. Om den nya intränade modellens prestanda är bättre än den aktuella produktions modellen fortsätter pipelinerna. Annars stoppas pipelinerna. Om du vill behålla utvärderingen ersätter du alla instanser av `mse` i `diabetes_regression/evaluate/evaluate_model.py` med det mått som du vill använda. 

För att få RID-utvärderingen ställer du in variabeln DevOps `RUN_EVALUATION` i `.pipelines\diabetes_regression-variables` till `false`.

## <a name="next-steps"></a>Nästa steg

Nu när du förstår hur du konverterar från ett experiment till produktions koden kan du använda följande länkar för att lära dig hur du övervakar experiment körningar och modeller som distribueras som webb tjänster:

> [!div class="nextstepaction"]
> [Övervaka Azure ml experiment körningar och mät värden](https://docs.microsoft.com/azure/machine-learning/how-to-track-experiments)
> [övervaka och samla in data från ml webb tjänst slut punkter](https://docs.microsoft.com/azure/machine-learning/how-to-enable-app-insights)
