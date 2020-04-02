---
title: Konvertera maskininlärningsexperimentkod till produktionskod
titleSuffix: Azure Machine Learning
description: Lär dig hur du konverterar maskininlärning experimentell kod till produktionskod med hjälp av MLOpsPython-kodmallen.
author: bjcmit
ms.author: brysmith
ms.service: machine-learning
ms.topic: tutorial
ms.date: 03/13/2020
ms.openlocfilehash: e3c9b16ae3d2b06ec19ecd29d15762a065c0c1ae
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521444"
---
# <a name="tutorial-convert-ml-experimental-code-to-production-code"></a>Självstudiekurs: Konvertera ML-experimentell kod till produktionskod

Ett maskininlärningsprojekt kräver experiment där hypoteser testas med agila verktyg som Jupyter Notebook med riktiga datamängder. När modellen är klar för produktion ska modellkoden placeras i en produktionskoddatabas. I vissa fall måste modellkoden konverteras till Python-skript som ska placeras i produktionskoddatabasen. Den här självstudien beskriver en rekommenderad metod för hur du exporterar experimentkod till Python-skript.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Ren okänslig kod
> * Refactor Jupyter Notebook kod i funktioner
> * Skapa Python-skript för relaterade uppgifter
> * Skapa enhetstester

## <a name="prerequisites"></a>Krav

- Generera [MLOpsPython-mallen](https://github.com/microsoft/MLOpsPython/generate) `experimentation/Diabetes Ridge Regression Training.ipynb` och `experimentation/Diabetes Ridge Regression Scoring.ipynb` använd och anteckningsböcker. Dessa anteckningsböcker används som ett exempel på konvertering från experiment till produktion. Du hittar dessa anteckningsböcker på [https://github.com/microsoft/MLOpsPython/tree/master/experimentation](https://github.com/microsoft/MLOpsPython/tree/master/experimentation).
- Installera `nbconvert`. Följ endast installationsanvisningarna under avsnittet __Installera nbconvert__ på [installationssidan.](https://nbconvert.readthedocs.io/en/latest/install.html)

## <a name="remove-all-nonessential-code"></a>Ta bort all okänslig kod

Viss kod som skrivits under experiment är endast avsedd för förberedande ändamål. Därför är det första steget för att konvertera experimentell kod till produktionskod att ta bort den här ouppnämliga koden. Om du tar bort ingen oavstängd kod blir koden mer underhållsbar. I det här avsnittet ska du `experimentation/Diabetes Ridge Regression Training.ipynb` ta bort kod från anteckningsboken. De uttalanden som `X` skriver `y` ut formen `features.describe` på och och cellen ringer är bara för datautforskning och kan tas bort. När du har tagit bort `experimentation/Diabetes Ridge Regression Training.ipynb` ingen oavstängd kod, bör se ut som följande kod utan markdown:

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import joblib
import pandas as pd

sample_data = load_diabetes()

df = pd.DataFrame(
    data=sample_data.data,
    columns=sample_data.feature_names)
df['Y'] = sample_data.target

X = df.drop('Y', axis=1).values
y = df['Y'].values

X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=0)
data = {"train": {"X": X_train, "y": y_train},
        "test": {"X": X_test, "y": y_test}}

args = {
    "alpha": 0.5
}

reg_model = Ridge(**args)
reg.fit(data["train"]["X"], data["train"]["y"])

preds = reg_model.predict(data["test"]["X"])
mse = mean_squared_error(preds, y_test)
metrics = {"mse": mse}
print(metrics)

model_name = "sklearn_regression_model.pkl"
joblib.dump(value=reg, filename=model_name)
```

## <a name="refactor-code-into-functions"></a>Refactor kod i funktioner

För det andra måste Jupyter-koden omstruktureras till funktioner. Om du gör omarifieringskoden i funktioner underlättar enhetstestningen och gör koden mer underhållsbar. I det här avsnittet ska du omfactor:

- Den Diabetes Ridge Regression`experimentation/Diabetes Ridge Regression Training.ipynb`Training anteckningsbok( )
- Den Diabetes Ridge Regression`experimentation/Diabetes Ridge Regression Scoring.ipynb`Scoring anteckningsbok( )

### <a name="refactor-diabetes-ridge-regression-training-notebook-into-functions"></a>Refactor Diabetes Ridge Regression Utbildning anteckningsbok i funktioner

I `experimentation/Diabetes Ridge Regression Training.ipynb`slutför du följande steg:

1. Skapa en `split_data` funktion som kallas att dela upp dataramen i test- och tågdata. Funktionen ska ta dataramen `df` som en parameter och returnera `train` `test`en ordlista som innehåller nycklarna och .

    Flytta koden under rubriken *Dela data till utbildnings- och valideringsuppsättningar* till funktionen och ändra den `split_data` `data` för att returnera objektet.

1. Skapa en `train_model`funktion som kallas `data` `args` , som tar parametrarna och returnerar en tränad modell.

    Flytta koden under rubriken *Utbildningsmodell på träningsuppsättning* till funktionen `reg_model` och ändra den `train_model` för att returnera objektet. Ta `args` bort ordlistan, värdena `args` kommer från parametern.

1. Skapa en `get_model_metrics`funktion som `reg_model` kallas `data`, som tar parametrar och , och utvärderar modellen returnerar sedan en ordlista med mått för den tränade modellen.

    Flytta koden under rubriken *Validera modell vid valideringsuppsättning* till `metrics` funktionen och ändra den `get_model_metrics` för att returnera objektet.

De tre funktionerna bör vara följande:

```python
# Split the dataframe into test and train data
def split_data(df):
    X = df.drop('Y', axis=1).values
    y = df['Y'].values

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}
    return data


# Train the model, return the model
def train_model(data, args):
    reg_model = Ridge(**args)
    reg_model.fit(data["train"]["X"], data["train"]["y"])
    return reg_model


# Evaluate the metrics for the model
def get_model_metrics(reg_model, data):
    preds = reg_model.predict(data["test"]["X"])
    mse = mean_squared_error(preds, data["test"]["y"])
    metrics = {"mse": mse}
    return metrics
```

Fortfarande `experimentation/Diabetes Ridge Regression Training.ipynb`i , slutföra följande steg:

1. Skapa en ny `main`funktion som kallas , som inte tar några parametrar och returnerar ingenting.
1. Flytta koden under rubriken "Läs in `main` data" till funktionen.
1. Lägg till anrop för de nyskrivna funktionerna i `main` funktionen:
    ```python
    # Split Data into Training and Validation Sets
    data = split_data(df)
    ```

    ```python
    # Train Model on Training Set
    args = {
        "alpha": 0.5
    }
    reg = train_model(data, args)
    ```

    ```python
    # Validate Model on Validation Set
    metrics = get_model_metrics(reg, data)
    ```
1. Flytta koden under rubriken "Spara modell" till `main` funktionen.

Funktionen `main` ska se ut som följande kod:

```python
def main():
    # Load Data
    sample_data = load_diabetes()

    df = pd.DataFrame(
        data=sample_data.data,
        columns=sample_data.feature_names)
    df['Y'] = sample_data.target

    # Split Data into Training and Validation Sets
    data = split_data(df)

    # Train Model on Training Set
    args = {
        "alpha": 0.5
    }
    reg = train_model(data, args)

    # Validate Model on Validation Set
    metrics = get_model_metrics(reg, data)

    # Save Model
    model_name = "sklearn_regression_model.pkl"

    joblib.dump(value=reg, filename=model_name)
```

I det här skedet bör det inte finnas någon kod kvar i anteckningsboken som inte finns i en funktion, förutom importsatser i den första cellen.

Lägg till ett `main` utdrag som anropar funktionen.

```python
main()
```

Efter refactoring, `experimentation/Diabetes Ridge Regression Training.ipynb` bör se ut som följande kod utan markdown:

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import pandas as pd
import joblib


# Split the dataframe into test and train data
def split_data(df):
    X = df.drop('Y', axis=1).values
    y = df['Y'].values

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}
    return data


# Train the model, return the model
def train_model(data, args):
    reg_model = Ridge(**args)
    reg_model.fit(data["train"]["X"], data["train"]["y"])
    return reg_model


# Evaluate the metrics for the model
def get_model_metrics(reg_model, data):
    preds = reg_model.predict(data["test"]["X"])
    mse = mean_squared_error(preds, data["test"]["y"])
    metrics = {"mse": mse}
    return metrics


def main():
    # Load Data
    sample_data = load_diabetes()

    df = pd.DataFrame(
        data=sample_data.data,
        columns=sample_data.feature_names)
    df['Y'] = sample_data.target

    # Split Data into Training and Validation Sets
    data = split_data(df)

    # Train Model on Training Set
    args = {
        "alpha": 0.5
    }
    reg = train_model(data, args)

    # Validate Model on Validation Set
    metrics = get_model_metrics(reg, data)

    # Save Model
    model_name = "sklearn_regression_model.pkl"

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

Konvertera anteckningsboken till ett körbart skript genom att köra följande `nbconvert` uttryck i `experimentation/Diabetes Ridge Regression Training.ipynb`en kommandotolk, som använder paketet och sökvägen till:

```
jupyter nbconvert -- to script "Diabetes Ridge Regression Training.ipynb" –output train
```

När anteckningsboken har konverterats till `train.py`tar du bort oönskade kommentarer. Ersätt anropet till `main()` i slutet av filen med en villkorlig anrop som följande kod:

```python
if __name__ == '__main__':
    main()
```

Filen `train.py` ska se ut som följande kod:

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import pandas as pd
import joblib


# Split the dataframe into test and train data
def split_data(df):
    X = df.drop('Y', axis=1).values
    y = df['Y'].values

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}
    return data


# Train the model, return the model
def train_model(data, args):
    reg_model = Ridge(**args)
    reg_model.fit(data["train"]["X"], data["train"]["y"])
    return reg_model


# Evaluate the metrics for the model
def get_model_metrics(reg_model, data):
    preds = reg_model.predict(data["test"]["X"])
    mse = mean_squared_error(preds, data["test"]["y"])
    metrics = {"mse": mse}
    return metrics


def main():
    # Load Data
    sample_data = load_diabetes()

    df = pd.DataFrame(
        data=sample_data.data,
        columns=sample_data.feature_names)
    df['Y'] = sample_data.target

    # Split Data into Training and Validation Sets
    data = split_data(df)

    # Train Model on Training Set
    args = {
        "alpha": 0.5
    }
    reg = train_model(data, args)

    # Validate Model on Validation Set
    metrics = get_model_metrics(reg, data)

    # Save Model
    model_name = "sklearn_regression_model.pkl"

    joblib.dump(value=reg, filename=model_name)

if __name__ == '__main__':
    main()
```

`train.py`kan nu anropas från en `python train.py`terminal genom att köra .
Funktionerna `train.py` från kan också anropas från andra filer.

Filen `train_aml.py` som finns `diabetes_regression/training` i katalogen i MLOpsPython-databasen anropar de funktioner som definierats i `train.py` samband med en Azure Machine Learning-experimentkörning. Funktionerna kan också anropas i enhetstester, som senare täcks i den här guiden.

### <a name="create-python-file-for-the-diabetes-ridge-regression-scoring-notebook"></a>Skapa Python-fil för den bärbara filen Diabetes Ridge Regression Scoring

Dolde anteckningsboken till ett körbart skript genom att köra följande `nbconvert` uttryck i `experimentation/Diabetes Ridge Regression Scoring.ipynb`en kommandotolk som använder paketet och sökvägen till:

```
jupyter nbconvert -- to script "Diabetes Ridge Regression Scoring.ipynb" –output score
```

När anteckningsboken har konverterats till `score.py`tar du bort oönskade kommentarer. Filen `score.py` ska se ut som följande kod:

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

Variabeln `model` måste vara global så att den är synlig i hela skriptet. Lägg till följande sats `init` i början av funktionen:

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

För det fjärde, skapa enhetstester för dina Python-funktioner. Enhetstester skyddar koden mot funktionella regressioner och gör det lättare att underhålla. I det här avsnittet skapar du enhetstester `train.py`för funktionerna i .

`train.py`innehåller flera funktioner, men vi skapar bara ett `train_model` enstaka enhetstest för funktionen med pytest-ramverket i den här självstudien. Pytest är inte det enda Python-ramverket för testning av python-enheter, men det är ett av de vanligaste. Mer information finns i [Pytest](https://pytest.org).

Ett enhetstest innehåller vanligtvis tre huvudåtgärder:

- Ordna objekt - skapa och ställa in nödvändiga objekt
- Agera på ett objekt
- Hävda vad som förväntas

Enhetstestet `train_model` anropar med vissa hårdkodade data `train_model` och argument och validerar som agerade som förväntat med hjälp av den resulterande tränade modellen för att göra en förutsägelse och jämföra förutsägelsen med ett förväntat värde.

```python
import numpy as np
from code.training.train import train_model


def test_train_model():
    # Arrange
    X_train = np.array([1, 2, 3, 4, 5, 6]).reshape(-1, 1)
    y_train = np.array([10, 9, 8, 8, 6, 5])
    data = {"train": {"X": X_train, "y": y_train}}

    # Act
    reg_model = train_model(data, {"alpha": 1.2})

    # Assert
    preds = reg_model.predict([[1], [2]])
    np.testing.assert_almost_equal(preds, [9.93939393939394, 9.03030303030303])
```

## <a name="next-steps"></a>Nästa steg

Nu när du förstår hur du konverterar från ett experiment till produktionskod läser du följande länkar för mer information och nästa steg:

+ [MLOpsPython](https://github.com/microsoft/MLOpsPython/blob/master/docs/custom_model.md): Skapa en CI/CD-pipeline för att träna, utvärdera och distribuera din egen modell med Azure Pipelines och Azure Machine Learning
+ [Övervaka Azure ML-experimentkörningar och mått](https://docs.microsoft.com/azure/machine-learning/how-to-track-experiments)
+ [Övervaka och samla in data från ML-webbtjänstslutpunkter](https://docs.microsoft.com/azure/machine-learning/how-to-enable-app-insights)
