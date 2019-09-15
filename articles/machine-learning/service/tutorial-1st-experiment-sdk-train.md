---
title: 'Självstudier: Träna din första ML-modell'
titleSuffix: Azure Machine Learning
description: I den här självstudien får du lära dig grundläggande design mönster i Azure Machine Learning och träna en enkel scikit-modell som baseras på diabetes data uppsättning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 09/03/2019
ms.openlocfilehash: b5d3a687adc8ecefcf581f7eda3b9e13d1973c62
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/15/2019
ms.locfileid: "71004028"
---
# <a name="tutorial-train-your-first-ml-model"></a>Självstudier: Träna din första ML-modell

Självstudien är **del två i en självstudieserie i två delar**. I föregående självstudie [skapade du en arbets yta och valde en utvecklings miljö](tutorial-1st-experiment-sdk-setup.md). I den här självstudien får du lära dig grundläggande design mönster i Azure Machine Learning och träna en enkel scikit-modell som baseras på diabetes data uppsättning. När du har slutfört den här självstudien får du praktisk kunskap om SDK: n för att skala upp för att utveckla mer komplexa experiment och arbets flöden.

I den här självstudien kommer du att lära dig följande:

> [!div class="checklist"]
> * Anslut din arbets yta och skapa ett experiment
> * Läs in data och träna scikit – lär dig modeller
> * Visa utbildnings resultat i portalen
> * Hämta den bästa modellen

## <a name="prerequisites"></a>Förutsättningar

Den enda förutsättningen är att köra en del av den här självstudien, [installations miljön och arbets ytan](tutorial-1st-experiment-sdk-setup.md).

I den här delen av självstudien kör du koden i exempel Jupyter Notebook `tutorials/tutorial-1st-experiment-sdk-train.ipynb` som öppnades i slutet av del en. Den här artikeln vägleder dig genom samma kod som finns i antecknings boken.

## <a name="launch-jupyter-web-interface"></a>Starta Jupyter-webbgränssnitt

1. På arbets ytans sida i Azure Portal väljer du **Notebook VM** : ar till vänster.

1. Välj **Jupyter** i **URI** -kolumnen för den virtuella dator som du skapade i del ett av den här självstudien.

    ![Starta Jupyter Notebook-servern](./media/tutorial-1st-experiment-sdk-setup/start-server.png)

   Länken startar din Notebook-Server och öppnar Jupyter Notebook-webbsida på en ny flik i webbläsaren.  Den här länken fungerar bara för den person som skapar den virtuella datorn. Varje användare av arbets ytan måste skapa sin egen virtuella dator.

1. På webb sidan för Jupyter Notebook väljer du den översta mappnamn som innehåller ditt användar namn.  

   Den här mappen finns i [lagrings kontot](concept-workspace.md#resources) för arbets ytan i stället för på själva Notebook-datorn.  Om du tar bort den virtuella Notebook-datorn behåller du fortfarande allt ditt arbete.  När du skapar en ny Notebook VM senare, kommer den att läsa in samma mapp. Om du delar din arbets yta med andra, kommer de att se din mapp och du ser dem.

1. Öppna under katalogen och öppna Jupyter-anteckningsboken `tutorials/tutorial-1st-experiment-sdk-train.ipynb`, **inte** `.yml` filen med samma namn. `samples-*` 

## <a name="connect-workspace-and-create-experiment"></a>Anslut arbets ytan och skapa experiment

> [!Important]
> Resten av den här artikeln innehåller samma innehåll som du ser i antecknings boken.  
>
> Växla till antecknings boken för Jupyter nu om du vill läsa den samtidigt som du kör koden. 
> Om du vill köra en enda kod cell i en bärbar dator klickar du på cellen kod och trycker på **SKIFT + RETUR**. Du kan också köra hela antecknings boken genom att välja **Cell > kör alla** på den översta menyn.

Importera- `config.json` `from_config().` klassen och Läs in din prenumerations information från filen med funktionen som söker efter JSON-filen i den aktuella katalogen som standard, men du kan också ange en Sök vägs parameter för att peka på filen `Workspace` använda `from_config(path="your/file/path")`. I en molnbaserad Notebook-Server är filen automatiskt i rot katalogen.

Om följande kod frågar efter ytterligare autentisering, klistrar du bara in länken i en webbläsare och anger autentiseringstoken.

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

Nu ska du skapa ett experiment i din arbets yta. Ett experiment är en annan grundläggande moln resurs som representerar en samling av försök (enskilda modell körningar). I den här självstudien använder du experimentet för att skapa körningar och spåra din modell utbildning i Azure Portal. Parametrar är en referens till din arbets yta och ett sträng namn för experimentet.


```python
from azureml.core import Experiment
experiment = Experiment(workspace=ws, name="diabetes-experiment")
```

## <a name="load-data-and-prepare-for-training"></a>Läs in data och Förbered för utbildning

I den här självstudien använder du diabetes-datauppsättningen, som är en förnormaliserad data uppsättning som ingår i scikit-lär. Den här data uppsättningen använder funktioner som ålder, kön och BMI för att förutsäga diabetes sjukdoms förlopp. Läs in data från den `load_diabetes()` statiska funktionen och dela upp dem i utbildning och test uppsättningar med `train_test_split()`. Den här funktionen åtskiljer data så att modellen har osett data som ska användas för att testa följande utbildning.


```python
from sklearn.datasets import load_diabetes
from sklearn.model_selection import train_test_split

X, y = load_diabetes(return_X_y = True)
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=66)
```

## <a name="train-a-model"></a>Träna en modell

Träna en enkel scikit-läre modell kan enkelt göras lokalt för småskalig utbildning, men vid inlärning av många iterationer med dussin tals olika typer av funktions permutationer och inställningar för en viss parameter, är det enkelt att förlora spår över vilka modeller du har tränat och hur du tränade dem. Följande design mönster visar hur du kan utnyttja SDK: n för att enkelt hålla koll på din utbildning i molnet.

Bygg ett skript som tågen Ridge modeller i en slinga genom olika alpha-värden för de olika parametrarna.


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

Ovanstående kod utför följande:

1. För varje alpha-värde i `alphas` matrisen skapas en ny körning i experimentet. Alfa-värdet loggas för att skilja mellan varje körning.
1. I varje körning instansieras en Ridge-modell, tränas och används för att köra förutsägelser. Roten – genomsnitts-kvadratvärdet beräknas för de faktiska respektive förväntade värdena och logga sedan in på körningen. I det här läget har metadata kopplade till både alpha-värdet och rmse precision.
1. Därefter serialiseras modellen för varje körning och överförs till körningen. På så sätt kan du ladda ned modell filen från körningen i portalen.
1. I slutet av varje iteration slutförs körningen genom att anropa `run.complete()`.

När utbildningen har slutförts anropar `experiment` du variabeln för att hämta en länk till experimentet i portalen.

```python
experiment
```

<table style="width:100%"><tr><th>Name</th><th>Arbetsyta</th><th>Rapport sida</th><th>Sidan dokument</th></tr><tr><td>diabetes – experiment</td><td>ditt-arbetsyte namn</td><td>Länka till Azure Portal</td><td>Länk till dokumentation</td></tr></table>

## <a name="view-training-results-in-portal"></a>Visa utbildnings resultat i portalen

När du följer **länken till Azure Portal** går du till huvud experiment sidan. Här ser du alla enskilda körningar i experimentet. Alla anpassade-loggade värden`alpha_value` ( `rmse`och, i det här fallet) blir fält för varje körning och blir också tillgängliga för diagram och paneler överst på experiment sidan. För att lägga till ett inloggat mått i ett diagram eller en panel, Hovra över den, klicka på knappen Redigera och hitta ditt anpassade-loggade mått.

När utbildnings modeller skalas över hundratals och tusentals separata körningar, gör den här sidan det enkelt att se alla modeller som du har tränat, särskilt hur de har tränats, och hur dina unika mått har ändrats över tid.

![Huvud experiment sidan i portalen](./media/tutorial-quickstart/experiment-main.png)

Om du klickar på en körnings nummer `RUN NUMBER` länk i kolumnen går du till sidan för varje enskild körning. På fliken standard visas mer **detaljerad information om** varje körning. Navigera till fliken **utdata** och se `.pkl` filen för den modell som överfördes till körningen under varje inlärnings upprepning. Här kan du ladda ned modell filen i stället för att behöva träna den manuellt.

![Sidan kör information i portalen](./media/tutorial-quickstart/model-download.png)

## <a name="get-the-best-model"></a>Få den bästa modellen

Förutom att kunna ladda ned presentationsfiler från experimentet i portalen kan du också ladda ned dem program mässigt. Följande kod itererar igenom varje körning i experimentet och kommer åt både de loggade körnings måtten och körnings informationen (som innehåller run_id). Detta håller reda på den bästa körningen, i det här fallet körs med lägsta rot-genomsnitt-kvadratvärdet.

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

Använd det bästa körnings-ID: t för att hämta `Run` den enskilda körningen med hjälp av konstruktorn tillsammans med experiment-objektet. Anropa `get_file_names()` sedan för att se alla filer som är tillgängliga för nedladdning från den här körningen. I det här fallet överför du bara en fil för varje körning under utbildningen.

```python
from azureml.core import Run
best_run = Run(experiment=experiment, run_id=minimum_rmse_runid)
print(best_run.get_file_names())
```

    ['model_alpha_0.1.pkl']

Anropa `download()` på objektet kör och ange modell fil namnet som ska hämtas. Som standard laddas den här funktionen ned till den aktuella katalogen.

```python
best_run.download_file(name="model_alpha_0.1.pkl")
```

## <a name="clean-up-resources"></a>Rensa resurser

Slutför inte det här avsnittet om du tänker köra andra Azure Machine Learning själv studie kurser.

### <a name="stop-the-notebook-vm"></a>Stoppa den virtuella Notebook-datorn

Om du har använt en molnbaserad Notebook-Server stoppar du den virtuella datorn när du inte använder den för att minska kostnaderna.

1. I arbets ytan väljer du **Notebook VM**: ar.

   ![Stoppa VM-servern](./media/tutorial-1st-experiment-sdk-setup/stop-server.png)

1. Välj den virtuella datorn i listan.

1. Välj **stoppa**.

1. När du är redo att använda servern igen väljer du **Starta**.

### <a name="delete-everything"></a>Ta bort allt

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

Du kan också behålla resursgruppen men ta bort en enstaka arbetsyta. Visa arbetsytans egenskaper och välj **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du följande uppgifter:

> [!div class="checklist"]
> * Anslöt din arbets yta och skapade ett experiment
> * Inlästa data och utbildade scikit – lär dig modeller
> * Visa inlärnings resultat i portalen och hämtade modeller

[Distribuera modellen](tutorial-deploy-models-with-aml.md) med Azure Machine Learning.
Lär dig hur du utvecklar [automatiserade maskin inlärnings](tutorial-auto-train-models.md) experiment.
