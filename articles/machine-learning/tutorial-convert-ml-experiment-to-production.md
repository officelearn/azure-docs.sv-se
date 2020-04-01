---
title: Konvertera maskininlärningsexperimentkod till produktionskod
titleSuffix: Azure Machine Learning
description: Lär dig hur du konverterar maskininlärning experimentell kod till produktionskod med hjälp av MLOpsPython-kodmallen.
author: bjcmit
ms.author: brysmith
ms.service: machine-learning
ms.topic: tutorial
ms.date: 03/13/2020
ms.openlocfilehash: f40c2b5f7134458b3f8cb492652bebf14388634c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79477144"
---
# <a name="tutorial-convert-ml-experimental-code-to-production-code"></a>Självstudiekurs: Konvertera ML-experimentell kod till produktionskod

Ett maskininlärningsprojekt kräver experiment där hypoteser testas med agila verktyg som Jupyter Notebook med riktiga datamängder. När modellen är klar för produktion ska modellkoden placeras i en produktionskoddatabas. I vissa fall måste modellkoden konverteras till Python-skript som ska placeras i produktionskoddatabasen. Den här självstudien beskriver en rekommenderad metod för hur du exporterar experimentkod till Python-skript.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
>
> * Ren okänslig kod
> * Refactor Jupyter Notebook kod i funktioner
> * Skapa Python-skript för relaterade uppgifter
> * Skapa enhetstester

## <a name="prerequisites"></a>Krav

- Generera [MLOpsPython-mallen](https://github.com/microsoft/MLOpsPython/generate) `experimentation/Diabetes Ridge Regression Training.ipynb` och `experimentation/Diabetes Ridge Regression Scoring.ipynb` använd och anteckningsböcker. Dessa anteckningsböcker används som ett exempel på konvertering från experiment till produktion. Du hittar dessa anteckningsböcker på [https://github.com/microsoft/MLOpsPython/tree/master/experimentation](https://github.com/microsoft/MLOpsPython/tree/master/experimentation).
- Installera nbconvert. Följ endast installationsanvisningarna under avsnittet __Installera nbconvert__ på [installationssidan.](https://nbconvert.readthedocs.io/en/latest/install.html)

## <a name="remove-all-nonessential-code"></a>Ta bort all okänslig kod

Viss kod som skrivits under experiment är endast avsedd för förberedande ändamål. Därför är det första steget för att konvertera experimentell kod till produktionskod att ta bort den här ouppnämliga koden. Om du tar bort ingen oavstängd kod blir koden mer underhållsbar. I det här avsnittet ska du `experimentation/Diabetes Ridge Regression Training.ipynb` ta bort kod från anteckningsboken. De uttalanden som `X` skriver `y` ut formen `features.describe` på och och cellen ringer är bara för datautforskning och kan tas bort. När du har tagit bort `experimentation/Diabetes Ridge Regression Training.ipynb` ingen oavstängd kod, bör se ut som följande kod utan markdown:

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

## <a name="refactor-code-into-functions"></a>Refactor kod i funktioner

För det andra måste Jupyter-koden omstruktureras till funktioner. Om du gör omarifieringskoden i funktioner underlättar enhetstestningen och gör koden mer underhållsbar. I det här avsnittet ska du omfactor:

- Den Diabetes Ridge Regression`experimentation/Diabetes Ridge Regression Training.ipynb`Training anteckningsbok( )
- Den Diabetes Ridge Regression`experimentation/Diabetes Ridge Regression Scoring.ipynb`Scoring anteckningsbok( )

### <a name="refactor-diabetes-ridge-regression-training-notebook-into-functions"></a>Refactor Diabetes Ridge Regression Utbildning anteckningsbok i funktioner

I `experimentation/Diabetes Ridge Regression Training.ipynb`slutför du följande steg:

1. Skapa en `train_model`funktion som kallas `data` `alpha` , som tar parametrarna och och returnerar en modell.
1. Kopiera koden under rubrikerna "Train Model on Training Set" och "Validera modell på valideringsuppsättning" till `train_model` funktionen.

Funktionen `train_model` ska se ut som följande kod:

```python
def train_model(data, alpha):
    reg = Ridge(alpha=alpha)
    reg.fit(data["train"]["X"], data["train"]["y"])
    preds = reg.predict(data["test"]["X"])
    print("mse", mean_squared_error(
        preds, data["test"]["y"]))
    return reg
```

När `train_model` funktionen har skapats ersätter du koden under rubrikerna "Train Model on Training Set" och "Validate Model on Validation Set" med följande uttryck:

```python
reg = train_model(data, alpha)
```

Den tidigare satsen anropar `train_model` funktionen `data` som passerar och `alpha` parametrarna och returnerar modellen.

I `experimentation/Diabetes Ridge Regression Training.ipynb`slutför du följande steg:

1. Skapa en ny `main`funktion som kallas , som inte tar några parametrar och returnerar ingenting.
1. Kopiera koden under rubrikerna "Läs in data", "Dela upp data i tränings- `main` och valideringsuppsättningar" och "Spara modell" i funktionen.
1. Kopiera det nyskapade `train_model` `main` anropet till i funktionen.

Funktionen `main` ska se ut som följande kod:

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

När `main` funktionen har skapats ersätter du all kod under rubrikerna "Läs in data", "Dela upp data i `train_model` tränings- och valideringsuppsättningar" och "Spara modell" tillsammans med det nyligen skapade anropet till med följande sats:

```python
main()
```

Efter refactoring, `experimentation/Diabetes Ridge Regression Training.ipynb` bör se ut som följande kod utan markdown:

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

### <a name="refactor-diabetes-ridge-regression-scoring-notebook-into-functions"></a>Refactor Diabetes Ridge Regression Scoring anteckningsbok i funktioner

I `experimentation/Diabetes Ridge Regression Scoring.ipynb`slutför du följande steg:

1. Skapa en ny `init`funktion som kallas , som inte tar några parametrar och returnerar ingenting.
1. Kopiera koden under rubriken "Läs in `init` modell" till funktionen.

Funktionen `init` ska se ut som följande kod:

```python
def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)
```

När `init` funktionen har skapats ersätter du all kod under rubriken "Läs in modell" med ett enda anrop till `init` följande:

```python
init()
```

I `experimentation/Diabetes Ridge Regression Scoring.ipynb`slutför du följande steg:

1. Skapa en ny `run`funktion `raw_data` som `request_headers` kallas , som tar och som parametrar och returnerar en ordlista med resultat enligt följande:

    ```python
    {"result": result.tolist()}
    ```

1. Kopiera koden under rubrikerna "Förbered data" och "Poängdata" till `run` funktionen.

    Funktionen `run` ska se ut som följande kod (Kom ihåg `raw_data` att `request_headers`ta bort de satser som anger variablerna och , som kommer att användas senare när `run` funktionen anropas):

    ```python
    def run(raw_data, request_headers):
        data = json.loads(raw_data)["data"]
        data = numpy.array(data)
        result = model.predict(data)

        return {"result": result.tolist()}
    ```

När `run` funktionen har skapats ersätter du all kod under rubrikerna "Förbered data" och "Poängdata" med följande kod:

```python
raw_data = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(raw_data, request_header)
print("Test result: ", prediction)
```

Den tidigare koden `raw_data` anger `request_header`variabler `run` och `raw_data` `request_header`anropar funktionen med och och skriver ut förutsägelserna.

Efter refactoring, `experimentation/Diabetes Ridge Regression Scoring.ipynb` bör se ut som följande kod utan markdown:

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

## <a name="combine-related-functions-in-python-files"></a>Kombinera relaterade funktioner i Python-filer

För det tredje måste relaterade funktioner slås samman till Python-filer för att bättre hjälpa till att återanvända kod. I det här avsnittet skapar du Python-filer för följande anteckningsböcker:

- Den Diabetes Ridge Regression`experimentation/Diabetes Ridge Regression Training.ipynb`Training anteckningsbok( )
- Den Diabetes Ridge Regression`experimentation/Diabetes Ridge Regression Scoring.ipynb`Scoring anteckningsbok( )

### <a name="create-python-file-for-the-diabetes-ridge-regression-training-notebook"></a>Skapa Python-fil för den bärbara filen Diabetes Ridge Regression Training

Konvertera anteckningsboken till ett körbart skript genom att köra följande uttryck i en kommandotolk, som använder nbconvert-paketet och sökvägen `experimentation/Diabetes Ridge Regression Training.ipynb`till:

```
jupyter nbconvert -- to script "Diabetes Ridge Regression Training.ipynb" –output train
```

När anteckningsboken har konverterats till `train.py`tar du bort alla kommentarer. Filen `train.py` ska se ut som följande kod:

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

Filen `train.py` som finns `diabetes_regression/training` i katalogen i MLOpsPython-databasen `build_id`stöder `model_name`kommandoradsargument (nämligen , och `alpha`). Stöd för kommandoradsargument `train.py` kan läggas till i `alpha` filen för att stödja dynamiska modellnamn och värden, men det är inte nödvändigt för att koden ska kunna köras.

### <a name="create-python-file-for-the-diabetes-ridge-regression-scoring-notebook"></a>Skapa Python-fil för den bärbara filen Diabetes Ridge Regression Scoring

Dold din bärbara dator till ett körbart skript genom att köra följande uttryck i en `experimentation/Diabetes Ridge Regression Scoring.ipynb`kommandotolk som använder nbconvert-paketet och sökvägen till:

```
jupyter nbconvert -- to script "Diabetes Ridge Regression Scoring.ipynb" –output score
```

När anteckningsboken har konverterats till `score.py`tar du bort alla kommentarer. Filen `score.py` ska se ut som följande kod:

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

Funktionen `train_model` behöver ändras för att instansiera en global variabelmodell så att den är synlig i hela skriptet. Lägg till följande sats `init` i början av funktionen:

```python
global model
```

När du har lagt `init` till föregående sats ska funktionen se ut så här:

```python
def init():
    global model

    # load the model from file into a global object
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)
```

## <a name="create-unit-tests-for-each-python-file"></a>Skapa enhetstester för varje Python-fil

För det fjärde måste enhetstester skapas för varje Python-fil, vilket gör koden mer robust och enklare att underhålla. I det här avsnittet ska du skapa ett enhetstest för en av funktionerna i `train.py`.

`train.py`innehåller två `train_model` funktioner: `main`och . Varje funktion behöver ett enhetstest, men vi skapar `train_model` bara ett enstaka enhetstest för funktionen med pytest-ramverket i den här självstudien. Pytest är inte det enda Python-ramverket för testning av python-enheter, men det är ett av de vanligaste. Mer information finns i [Pytest](https://pytest.org).

Ett enhetstest innehåller vanligtvis tre huvudåtgärder:

- Ordna objekt - skapa och ställa in nödvändiga objekt
- Agera på ett objekt
- Hävda vad som förväntas

Ett vanligt `train_model` villkor `data` för `alpha` är när och ett värde skickas. Det förväntade resultatet `Ridge.train` är `Ridge.predict` att funktionerna och ska anropas. Eftersom utbildningsmetoder för maskininlärning ofta inte `Ridge.train` är snabbkörning, kommer anropet att hånas. Eftersom returvärdet `Ridge.train` för är ett hånat objekt, `Ridge.predict`kommer vi också att håna . Enhetstestet `train_model` för att `data` testa `alpha` passning av och `Ridge.train` `Ridge.predict` ett värde med det förväntade resultatet av och funktioner som anropas med hjälp av hån och Pytest-ramverket ska se ut som följande kod:

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

## <a name="use-your-own-model-with-mlopspython-code-template"></a>Använd din egen modell med MLOpsPython-kodmall

Om du har följt stegen i den här guiden har du en uppsättning skript som korrelerar till tåg-/poäng-/testskripten som är tillgängliga i MLOpsPython-databasen.  Enligt den struktur som nämns ovan kommer följande steg att gå igenom vad som behövs för att använda dessa filer för ditt eget maskininlärningsprojekt:

1. Följ guiden [Komma igång med](https://github.com/microsoft/MLOpsPython/blob/master/docs/getting_started.md) MLOpsPython
2. Följ MLOpsPython [bootstrap instruktioner](https://github.com/microsoft/MLOpsPython/blob/master/bootstrap/README.md) för att skapa ditt projekt startpunkt
3. Byt ut träningskoden
4. Ersätt poängkoden
5. Uppdatera utvärderingskoden

### <a name="follow-the-getting-started-guide"></a>Följ komma igång-guiden
Efter [komma igång-guiden](https://github.com/microsoft/MLOpsPython/blob/master/docs/getting_started.md) är nödvändigt att ha den stödjande infrastrukturen och rörledningarna för att köra MLOpsPython.

### <a name="follow-the-bootstrap-instructions"></a>Följ Bootstrap-instruktionerna

[Bootstrap från MLOpsPython förvaringsguide](https://github.com/microsoft/MLOpsPython/blob/master/bootstrap/README.md) hjälper dig att snabbt förbereda förvaret för ditt projekt.

**Anm.:** Eftersom bootstrap-skriptet byter namn på diabetes_regression-mappen till det projektnamn du väljer, `[project name]` refererar vi till ditt projekt som när sökvägar är inblandade.

### <a name="replace-training-code"></a>Ersätt träningskod

Att byta ut den kod som används för att träna modellen och ta bort eller ersätta motsvarande enhetstester krävs för att lösningen ska fungera med din egen kod. Följ dessa steg specifikt:

1. Byt ut `[project name]/training/train.py`. Det här skriptet tränar din modell lokalt eller på Azure ML-beräkningen.
1. Ta bort eller byt ut utbildningsenhetstester som finns i`[project name]/training/test_train.py`

### <a name="replace-score-code"></a>Ersätt poängkod

För att modellen ska kunna dra in funktioner för inferens i realtid måste poängkoden ersättas. MlOpsPython-mallen använder poängkoden för att distribuera modellen för att göra bedömning i realtid på ACI-, AKS- eller webbappar. Om du vill fortsätta `[project name]/scoring/score.py`göra mål ersätter du .

### <a name="update-evaluation-code"></a>Uppdatera utvärderingskod

MlOpsPython-mallen använder det evaluate_model skriptet för att jämföra prestanda för den nyutbildade modellen och den aktuella produktionsmodellen baserat på Medelkvadratfel. Om prestanda för den nyutbildade modellen är bättre än den aktuella produktionsmodellen fortsätter pipelines. Annars avbryts pipelines. Om du vill behålla utvärderingen ersätter du alla införekomster av `mse` in `[project name]/evaluate/evaluate_model.py` med det mått som du vill ha.

Om du vill bli av med utvärderingen `.pipelines/[project name]-variables-template.yml` `false`ställer du in DevOps-pipelinevariabeln `RUN_EVALUATION` på .

## <a name="next-steps"></a>Nästa steg

Nu när du förstår hur du konverterar från ett experiment till produktionskod använder du följande länkar för att lära dig hur du övervakar experimentkörningar och modeller som distribueras som webbtjänster:

> [!div class="nextstepaction"]
> [Övervaka Azure ML-experimentkörningar](https://docs.microsoft.com/azure/machine-learning/how-to-track-experiments)
> och mått[Övervaka och samla in data från ml-slutpunkter för webbtjänsten](https://docs.microsoft.com/azure/machine-learning/how-to-enable-app-insights)
