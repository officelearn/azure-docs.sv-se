---
title: 'Självstudiekurs: Träna din första Azure ML-modell i Python'
titleSuffix: Azure Machine Learning
description: I den här självstudien lär du dig de grundläggande designmönstren i Azure Machine Learning och tränar en enkel scikit-learn-modell baserat på diabetesdatauppsättningen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 02/10/2020
ms.openlocfilehash: aa90655ecb14abe38ec8fdfc6c18e7d292abbef3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79238680"
---
# <a name="tutorial-train-your-first-ml-model"></a>Handledning: Träna din första ML-modell

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Självstudien är **del två i en självstudieserie i två delar**. I föregående självstudie har du [skapat en arbetsyta och valt en utvecklingsmiljö](tutorial-1st-experiment-sdk-setup.md). I den här självstudien lär du dig de grundläggande designmönstren i Azure Machine Learning och tränar en enkel scikit-learn-modell baserat på diabetesdatauppsättningen. När du har slutfört den här självstudien får du praktisk kunskap om SDK för att skala upp till att utveckla mer komplexa experiment och arbetsflöden.

I den här självstudien kommer du att lära dig följande:

> [!div class="checklist"]
> * Anslut arbetsytan och skapa ett experiment
> * Ladda data och träna scikit-learn-modeller
> * Visa träningsresultat i portalen
> * Hämta den bästa modellen

## <a name="prerequisites"></a>Krav

Den enda förutsättningen är att köra del ett av den här [självstudien, installationsmiljön och arbetsytan](tutorial-1st-experiment-sdk-setup.md).

I den här delen av självstudien kör du koden i exemplet Jupyter notebook *tutorials/create-first-ml-experiment/tutorial-1st-experiment-sdk-train.ipynb* öppnade i slutet av del ett. Den här artikeln går igenom samma kod som finns i anteckningsboken.

## <a name="open-the-notebook"></a>Öppna anteckningsboken

1. Logga in på [Azure Machine Learning studio](https://ml.azure.com/).

1. Öppna **handledningen-1st-experiment-sdk-train.ipynb** i mappen som visas i [del ett](tutorial-1st-experiment-sdk-setup.md#open).


> [!Warning]
> Skapa **inte** en *ny* bärbar dator i Jupyter-gränssnittet! Den bärbara datorn *tutorials/create-first-ml-experiment/tutorial-1st-experiment-sdk-train.ipynb* är inklusive **all kod och data som behövs** för den här guiden.

## <a name="connect-workspace-and-create-experiment"></a>Anslut arbetsytan och skapa experiment

> [!Important]
> Resten av den här artikeln innehåller samma innehåll som du ser i anteckningsboken.  
>
> Växla till Jupyter-anteckningsboken nu om du vill läsa tillsammans när du kör koden. 
> Om du vill köra en en enda kodcell i en anteckningsbok klickar du på kodcellen och trycker på **Skift+Retur**. Du kan också köra hela anteckningsboken genom att välja **Kör alla** i det övre verktygsfältet.

Importera `Workspace` klassen och läsa in prenumerationsinformationen från filen `config.json` med funktionen `from_config().` Detta söker efter JSON-filen i den aktuella katalogen `from_config(path="your/file/path")`som standard, men du kan också ange en sökvägsparameter som ska peka på filen med . I en molnanteckningsserver finns filen automatiskt i rotkatalogen.

Om följande kod ber om ytterligare autentisering, helt enkelt klistra in länken i en webbläsare och ange autentisering token.

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

Skapa nu ett experiment på arbetsytan. Ett experiment är en annan grundläggande molnresurs som representerar en samling utvärderingsversioner (enskilda modeller körs). I den här självstudien använder du experimentet för att skapa körningar och spåra din modellutbildning i Azure Machine Learning-studion. Parametrarna innehåller din arbetsytas referens och ett strängnamn för experimentet.


```python
from azureml.core import Experiment
experiment = Experiment(workspace=ws, name="diabetes-experiment")
```

## <a name="load-data-and-prepare-for-training"></a>Ladda data och förbered för träning

För den här guiden använder du diabetesdatauppsättningen, som använder funktioner som ålder, kön och BMI för att förutsäga utvecklingen av diabetessjukdomar. Läs in data från klassen [Azure Open Dataset](https://azure.microsoft.com/services/open-datasets/) och dela `train_test_split()`upp dem i tränings- och testuppsättningar med . Den här funktionen segregerar data så att modellen har osynliga data att använda för testning efter utbildning.


```python
from azureml.opendatasets import Diabetes
from sklearn.model_selection import train_test_split

x_df = Diabetes.get_tabular_dataset().to_pandas_dataframe().dropna()
y_df = x_df.pop("Y")

X_train, X_test, y_train, y_test = train_test_split(x_df, y_df, test_size=0.2, random_state=66)
```

## <a name="train-a-model"></a>Träna en modell

Utbildning en enkel scikit-lära modell kan enkelt göras lokalt för småskalig utbildning, men när utbildning många iterationer med dussintals olika funktioner permutationer och hyperparameter inställningar, är det lätt att tappa reda på vilka modeller du har tränat och hur du tränade dem. Följande designmönster visar hur du utnyttjar SDK för att enkelt hålla reda på din träning i molnet.

Bygg ett skript som tränar åsmodeller i en slinga genom olika hyperparameter alfavärden.


```python
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.externals import joblib
import math

alphas = [0.1, 0.2, 0.3, 0.4, 0.5, 0.6, 0.7, 0.8, 0.9, 1.0]

for alpha in alphas:
    run = experiment.start_logging()
    run.log("alpha_value", alpha)

    model = Ridge(alpha=alpha)
    model.fit(X=X_train, y=y_train)
    y_pred = model.predict(X=X_test)
    rmse = math.sqrt(mean_squared_error(y_true=y_test, y_pred=y_pred))
    run.log("rmse", rmse)

    model_name = "model_alpha_" + str(alpha) + ".pkl"
    filename = "outputs/" + model_name

    joblib.dump(value=model, filename=filename)
    run.upload_file(name=model_name, path_or_stream=filename)
    run.complete()
```

Ovanstående kod åstadkommer följande:

1. För varje alfahyperparametervärde i matrisen `alphas` skapas en ny körning i experimentet. Alfavärdet loggas för att skilja mellan varje körning.
1. I varje körning instansieras en Ridge-modell, tränas och används för att köra förutsägelser. Rotvärdet-medelvärdet-kvadrat-fel beräknas för de faktiska kontra förväntade värdena och loggas sedan till körningen. Vid denna punkt körningen har metadata kopplade för både alfa värde och rmse noggrannhet.
1. Därefter serialiseras modellen för varje körning och överförs till körningen. På så sätt kan du hämta modellfilen från körningen i portalen.
1. I slutet av varje iteration avslutas `run.complete()`körningen genom att anropa .

När utbildningen är klar `experiment` anropar du variabeln för att hämta en länk till experimentet i portalen.

```python
experiment
```

<table style="width:100%"><tr><th>Namn</th><th>Arbetsyta</th><th>Rapportsida</th><th>Sidan Dokument</th></tr><tr><td>diabetes-experiment</td><td>ditt arbetsytenamn</td><td>Länk till Azure-portalen</td><td>Länk till dokumentation</td></tr></table>

## <a name="view-training-results-in-portal"></a>Visa träningsresultat i portalen

När du följer **portalen Länk till Azure** går du till huvudexperimentsidan. Här ser du alla enskilda körningar i experimentet. Alla anpassade loggade`alpha_value` värden `rmse`( och , i det här fallet) blir fält för varje körning och blir också tillgängliga för diagram och paneler högst upp på experimentsidan. Om du vill lägga till ett loggat mått i ett diagram eller en panel håller du muspekaren över det, klickar på redigeringsknappen och hittar det anpassade loggade måttet.

När du tränar modeller i skala över hundratals och tusentals separata körningar gör den här sidan det enkelt att se alla modeller du har tränat, särskilt hur de har tränats och hur dina unika mätvärden har förändrats med tiden.

![Sidan Huvudexperiment i Portal](./media/tutorial-1st-experiment-sdk-train/experiment-main.png)

Om du klickar på en `RUN NUMBER` länk för körningsnummer i kolumnen kommer du till sidan för varje enskild körning. **Standardfliken Detaljer** visar mer detaljerad information om varje körning. Navigera till fliken **Utdata** och `.pkl` du ser filen för modellen som laddades upp till körningen under varje träningsiteration. Här kan du ladda ner modellfilen, i stället för att behöva omskola den manuellt.

![Sidan Kör information i Portal](./media/tutorial-1st-experiment-sdk-train/model-download.png)

## <a name="get-the-best-model"></a>Få den bästa modellen

Förutom att kunna ladda ner modellfiler från experimentet i portalen kan du också ladda ner dem programmässigt. Följande kod itererar genom varje körning i experimentet och kommer åt både de loggade körningsmåtten och körningsinformationen (som innehåller run_id). Detta håller reda på den bästa körningen, i det här fallet körningen med det lägsta rot-mean-squared-error.

```python
minimum_rmse_runid = None
minimum_rmse = None

for run in experiment.get_runs():
    run_metrics = run.get_metrics()
    run_details = run.get_details()
    # each logged metric becomes a key in this returned dict
    run_rmse = run_metrics["rmse"]
    run_id = run_details["runId"]

    if minimum_rmse is None:
        minimum_rmse = run_rmse
        minimum_rmse_runid = run_id
    else:
        if run_rmse < minimum_rmse:
            minimum_rmse = run_rmse
            minimum_rmse_runid = run_id

print("Best run_id: " + minimum_rmse_runid)
print("Best run_id rmse: " + str(minimum_rmse))
```

    Best run_id: 864f5ce7-6729-405d-b457-83250da99c80
    Best run_id rmse: 57.234760283951765

Använd det bästa körnings-ID:et för att hämta den enskilda körningen `Run` med konstruktorn tillsammans med experimentobjektet. Ring `get_file_names()` sedan för att se alla filer som är tillgängliga för nedladdning från den här körningen. I det här fallet har du bara laddat upp en fil för varje körning under träningen.

```python
from azureml.core import Run
best_run = Run(experiment=experiment, run_id=minimum_rmse_runid)
print(best_run.get_file_names())
```

    ['model_alpha_0.1.pkl']

Anropar `download()` körningsobjektet och anger modellfilnamnet som ska hämtas. Som standard hämtas den här funktionen till den aktuella katalogen.

```python
best_run.download_file(name="model_alpha_0.1.pkl")
```

## <a name="clean-up-resources"></a>Rensa resurser

Fyll inte i det här avsnittet om du planerar att köra andra Azure Machine Learning-självstudier.

### <a name="stop-the-compute-instance"></a>Stoppa beräkningsinstansen

[!INCLUDE [aml-stop-server](../../includes/aml-stop-server.md)]

### <a name="delete-everything"></a>Ta bort allt

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

Du kan också behålla resursgruppen men ta bort en enstaka arbetsyta. Visa arbetsytans egenskaper och välj **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här självstudien gjorde du följande uppgifter:

> [!div class="checklist"]
> * Kopplade din arbetsyta och skapade ett experiment
> * Laddade data och utbildade scikit-learn-modeller
> * Visade träningsresultat i portalen och hämtade modeller

[Distribuera modellen](tutorial-deploy-models-with-aml.md) med Azure Machine Learning.
Lär dig hur du utvecklar [automatiserade maskininlärningsexperiment.](tutorial-auto-train-models.md)
