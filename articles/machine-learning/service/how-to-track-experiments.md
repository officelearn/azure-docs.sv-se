---
title: Log ML-experiment & mått
titleSuffix: Azure Machine Learning
description: Övervaka dina Azure ML-experiment och övervaka körnings mått för att förbättra skapande processen för modeller. Lägg till loggning i ditt utbildnings skript och Visa de loggade resultaten för en körning.  Använd kör. log, kör. start_logging eller ScriptRunConfig.
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/11/2019
ms.custom: seodec18
ms.openlocfilehash: 2d8bf44f5e5e7a3f8c328a47480599f9dd18b845
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489512"
---
# <a name="monitor-azure-ml-experiment-runs-and-metrics"></a>Övervaka körningar och mått för Azure ML-experiment
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Förbättra skapande processen för modeller genom att spåra experiment och övervaka körnings mått. I den här artikeln lär du dig att lägga till loggnings kod i ditt utbildnings skript, skicka in en experiment körning, Övervakare som körs och granska resultaten i Azure Machine Learning.

> [!NOTE]
> Azure Machine Learning kan också logga information från andra källor under utbildningen, till exempel automatiserade maskin inlärnings körningar eller Docker-behållaren som kör övnings jobbet. Dessa loggar är inte dokumenterade. Om du stöter på problem och kontaktar Microsoft-supporten kan det hända att de kan använda dessa loggar under fel sökning.

## <a name="available-metrics-to-track"></a>Tillgängliga mått som ska spåras

Du kan lägga till följande mått i en körning medan du tränar ett experiment. Om du vill visa en mer detaljerad lista över vad som kan spåras i en körning, se [referens dokumentationen för körnings klassen](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py).

|Typ| Python-funktionen | Anteckningar|
|----|:----|:----|
|Skalära värden |Funktioner<br>`run.log(name, value, description='')`<br><br>Exempel:<br>Kör. log ("noggrannhet", 0,95) |Logga ett numeriskt värde eller sträng värde för körningen med det tilldelade namnet. Att logga ett mått på en körning gör att måttet lagras i körnings posten i experimentet.  Du kan logga samma mått flera gånger i en körning, och resultatet betraktas som en Vector för det måttet.|
|Vilka|Funktioner<br>`run.log_list(name, value, description='')`<br><br>Exempel:<br>Kör. log _list ("felaktigheter", [0,6, 0,7, 0,87]) | Logga en lista med värden som ska köras med det aktuella namnet.|
|radhöjd|Funktioner<br>`run.log_row(name, description=None, **kwargs)`<br>Exempel:<br>Kör. log _row ("Y över X", X = 1, Y = 0.4) | Om du använder *log_row* skapas ett mått med flera kolumner enligt beskrivningen i kwargs. Varje namngiven parameter genererar en kolumn med det angivna värdet.  *log_row* kan anropas en gång för att logga en godtycklig tupel eller flera gånger i en slinga för att generera en fullständig tabell.|
|Tabell|Funktioner<br>`run.log_table(name, value, description='')`<br><br>Exempel:<br>Kör. log _table ("Y över X", {"X": [1, 2, 3], "Y": [0.6, 0,7, 0,89]}) | Logga ett Dictionary-objekt i körningen med det aktuella namnet. |
|Avbildningar|Funktioner<br>`run.log_image(name, path=None, plot=None)`<br><br>Exempel:<br>`run.log_image("ROC", plt)` | Logga en bild i körnings posten. Använd log_image för att logga en bildfil eller en matplotlib-rityta för körningen.  De här bilderna visas och är jämförbara i körnings posten.|
|Tagga en körning|Funktioner<br>`run.tag(key, value=None)`<br><br>Exempel:<br>Kör. tag ("vald", "Ja") | Tagga körningen med en sträng nyckel och ett valfritt sträng värde.|
|Ladda upp fil eller katalog|Funktioner<br>`run.upload_file(name, path_or_stream)`<br> <br> Exempel:<br>Kör. upload_file ("best_model. PKL", "./Model.PKL") | Ladda upp en fil till körnings posten. Kör automatiskt avbilda fil i den angivna utdatakatalogen som är "./outputs" som standard för de flesta körnings typer.  Använd endast upload_file när ytterligare filer behöver överföras eller om ingen utmatnings katalog har angetts. Vi rekommenderar att du lägger till `outputs` till namnet så att det överförs till katalogen utdata. Du kan visa en lista över alla filer som är associerade med den här körnings posten genom att anropa `run.get_file_names()`|

> [!NOTE]
> Mått för skalärer, listor, rader och tabeller kan ha typen: Float, Integer eller String.

## <a name="choose-a-logging-option"></a>Välj ett loggnings alternativ

Om du vill spåra eller övervaka experimentet måste du lägga till kod för att starta loggning när du skickar in körningen. Följande är exempel på hur du kan starta sändningen:
* __Kör. start_logging__ – Lägg till loggnings funktioner i ditt utbildnings skript och starta en interaktiv loggningsmodul i det angivna experimentet. **start_logging** skapar en interaktiv körning för användning i scenarier som antecknings böcker. Alla mått som loggas under sessionen läggs till i körnings posten i experimentet.
* __ScriptRunConfig__ – Lägg till loggnings funktioner i ditt utbildnings skript och Läs in hela skript-mappen med körningen.  **ScriptRunConfig** är en klass för att konfigurera konfigurationer för skript körningar. Med det här alternativet kan du lägga till övervaknings kod för att få ett meddelande om slut för ande eller för att få en visuell widget att övervaka.

## <a name="set-up-the-workspace"></a>Konfigurera arbets ytan
Innan du lägger till loggning och skickar ett experiment måste du konfigurera arbets ytan.

1. Läs in arbets ytan. Mer information om hur du anger arbets ytans konfiguration finns i [konfigurations filen för arbets ytan](how-to-configure-environment.md#workspace).

   ```python
   from azureml.core import Experiment, Run, Workspace
   import azureml.core
  
   ws = Workspace.from_config()
   ```
  
## <a name="option-1-use-start_logging"></a>Alternativ 1: Använd start_logging

**start_logging** skapar en interaktiv körning för användning i scenarier som antecknings böcker. Alla mått som loggas under sessionen läggs till i körnings posten i experimentet.

I följande exempel tågen en enkel sklearn Ridge-modell lokalt i en lokal Jupyter-anteckningsbok. Mer information om hur du skickar experiment till olika miljöer finns i [Konfigurera beräknings mål för modell utbildning med Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/how-to-set-up-training-targets).

1. Skapa ett utbildnings skript i en lokal Jupyter-anteckningsbok. 

   ```python
   # load diabetes dataset, a well-known small dataset that comes with scikit-learn
   from sklearn.datasets import load_diabetes
   from sklearn.linear_model import Ridge
   from sklearn.metrics import mean_squared_error
   from sklearn.model_selection import train_test_split
   from sklearn.externals import joblib

   X, y = load_diabetes(return_X_y = True)
   columns = ['age', 'gender', 'bmi', 'bp', 's1', 's2', 's3', 's4', 's5', 's6']
   X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 0.2, random_state = 0)
   data = {
      "train":{"X": X_train, "y": y_train},        
      "test":{"X": X_test, "y": y_test}
   }
   reg = Ridge(alpha = 0.03)
   reg.fit(data['train']['X'], data['train']['y'])
   preds = reg.predict(data['test']['X'])
   print('Mean Squared Error is', mean_squared_error(preds, data['test']['y']))
   joblib.dump(value = reg, filename = 'model.pkl');
   ```

2. Lägg till experiment uppföljning med Azure Machine Learning SDK och ladda upp en beständiga modell i experiment körnings posten. Följande kod lägger till taggar, loggar och laddar upp en modell fil till experimentet.

   ```python
    # Get an experiment object from Azure Machine Learning
    experiment = Experiment(workspace=ws, name="train-within-notebook")
    
    # Create a run object in the experiment
    run =  experiment.start_logging()
    # Log the algorithm parameter alpha to the run
    run.log('alpha', 0.03)
    
    # Create, fit, and test the scikit-learn Ridge regression model
    regression_model = Ridge(alpha=0.03)
    regression_model.fit(data['train']['X'], data['train']['y'])
    preds = regression_model.predict(data['test']['X'])
    
    # Output the Mean Squared Error to the notebook and to the run
    print('Mean Squared Error is', mean_squared_error(data['test']['y'], preds))
    run.log('mse', mean_squared_error(data['test']['y'], preds))
    
    # Save the model to the outputs directory for capture
    model_file_name = 'outputs/model.pkl'
    
    joblib.dump(value = regression_model, filename = model_file_name)
    
    # upload the model file explicitly into artifacts 
    run.upload_file(name = model_file_name, path_or_stream = model_file_name)
    
    # Complete the run
    run.complete()
   ```

    Skriptet slutar med ```run.complete()```, vilket markerar kör som slutfört.  Den här funktionen används vanligt vis i interaktiva scenarier för bärbara datorer.

## <a name="option-2-use-scriptrunconfig"></a>Alternativ 2: Använd ScriptRunConfig

[**ScriptRunConfig**](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) är en klass för att konfigurera konfigurationer för skript körningar. Med det här alternativet kan du lägga till övervaknings kod för att få ett meddelande om slut för ande eller för att få en visuell widget att övervaka.

Det här exemplet expanderas i den grundläggande sklearn Ridge-modellen från ovan. Det gör att en enkel parameter svep för att svepa över alfa värden i modellen för att samla in mått och utbildade modeller i körs under experimentet. Exemplet körs lokalt mot en användar hanterad miljö. 

1. Skapa ett utbildnings skript `train.py`.

   ```python
   # train.py

   import os
   from sklearn.datasets import load_diabetes
   from sklearn.linear_model import Ridge
   from sklearn.metrics import mean_squared_error
   from sklearn.model_selection import train_test_split
   from azureml.core.run import Run
   from sklearn.externals import joblib

   import numpy as np

   #os.makedirs('./outputs', exist_ok = True)

   X, y = load_diabetes(return_X_y = True)

   run = Run.get_context()

   X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 0.2, random_state = 0)
   data = {"train": {"X": X_train, "y": y_train},
          "test": {"X": X_test, "y": y_test}}

   # list of numbers from 0.0 to 1.0 with a 0.05 interval
   alphas = mylib.get_alphas()

   for alpha in alphas:
      # Use Ridge algorithm to create a regression model
      reg = Ridge(alpha = alpha)
      reg.fit(data["train"]["X"], data["train"]["y"])

      preds = reg.predict(data["test"]["X"])
      mse = mean_squared_error(preds, data["test"]["y"])
      # log the alpha and mse values
      run.log('alpha', alpha)
      run.log('mse', mse)

      model_file_name = 'ridge_{0:.2f}.pkl'.format(alpha)
      # save model in the outputs folder so it automatically get uploaded
      with open(model_file_name, "wb") as file:
          joblib.dump(value = reg, filename = model_file_name)

      # upload the model file explicitly into artifacts 
      run.upload_file(name = model_file_name, path_or_stream = model_file_name)

      # register the model
      #run.register_model(file_name = model_file_name)

      print('alpha is {0:.2f}, and mse is {1:0.2f}'.format(alpha, mse))
  
   ```

2. `train.py` skript referenser `mylib.py` som gör att du kan hämta en lista över de alpha-värden som ska användas i Ridge-modellen.

   ```python
   # mylib.py
  
   import numpy as np

   def get_alphas():
      # list of numbers from 0.0 to 1.0 with a 0.05 interval
      return np.arange(0.0, 1.0, 0.05)
   ```

3. Konfigurera en användare hanterad lokal miljö.

   ```python
   from azureml.core import Environment
    
   # Editing a run configuration property on-fly.
   user_managed_env = Environment("user-managed-env")
    
   user_managed_env.python.user_managed_dependencies = True
    
   # You can choose a specific Python environment by pointing to a Python path 
   #user_managed_env.python.interpreter_path = '/home/johndoe/miniconda3/envs/myenv/bin/python'
   ```

4. Skicka ```train.py``` skriptet som ska köras i den användar hanterade miljön. Hela skriptets mapp skickas för utbildning, inklusive ```mylib.py```-filen.

   ```python
   from azureml.core import ScriptRunConfig
    
   exp = Experiment(workspace=ws, name="train-on-local")
   src = ScriptRunConfig(source_directory='./', script='train.py')
   src.run_config.environment = user_managed_env
   run = exp.submit(src)
   ```

## <a name="manage-a-run"></a>Hantera en körning

[Övnings körningen starta, övervaka och Avbryt visar](how-to-manage-runs.md) mer information Azure Machine Learning arbets flöden för hur du hanterar experiment.

## <a name="view-run-details"></a>Visa körnings information

### <a name="monitor-run-with-jupyter-notebook-widget"></a>Övervaka körning med Jupyter Notebook-widget
När du använder **ScriptRunConfig** -metoden för att skicka körningar kan du se förloppet för körningen med en [Jupyter-widget](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py). Precis som körningsöverföringen är widgeten asynkron och tillhandahåller liveuppdateringar var 10:e till var 15:e sekund tills jobbet har slutförts.

1. Visa widgeten Jupyter i väntan på att körningen ska slutföras.

   ```python
   from azureml.widgets import RunDetails
   RunDetails(run).show()
   ```

   ![Skärm bild av widgeten Jupyter Notebook](./media/how-to-track-experiments/run-details-widget.png)

Du kan också hämta en länk till samma visning i din arbets yta.

```python
print(run.get_portal_url())
```

2. **[För automatiserade maskin inlärnings körningar]** För att få åtkomst till diagram från en tidigare körning. Ersätt `<<experiment_name>>` med lämpligt experiment namn:

   ``` 
   from azureml.widgets import RunDetails
   from azureml.core.run import Run

   experiment = Experiment (workspace, <<experiment_name>>)
   run_id = 'autoML_my_runID' #replace with run_ID
   run = Run(experiment, run_id)
   RunDetails(run).show()
   ```

   ![Jupyter Notebook-widget för automatiserad Machine Learning](./media/how-to-track-experiments/azure-machine-learning-auto-ml-widget.png)


Om du vill visa mer information om en pipeline klickar du på pipelinen som du vill utforska i tabellen och diagrammet visas i ett popup-fönster från Azure Machine Learning Studio.

### <a name="get-log-results-upon-completion"></a>Hämta loggresultat när åtgärden har slutförts

Modell träning och övervakning sker i bakgrunden så att du kan köra andra uppgifter medan du väntar. Du kan också vänta tills modellen har slutfört utbildningen innan du kör mer kod. När du använder **ScriptRunConfig**kan du använda ```run.wait_for_completion(show_output = True)``` för att visa när modell träningen är klar. Med flaggan ```show_output``` får du utförliga utdata. 


### <a name="query-run-metrics"></a>Kör mått för fråga

Du kan visa måtten för en tränad modell med ```run.get_metrics()```. Nu kan du hämta alla mått som loggades i exemplet ovan för att fastställa den bästa modellen.

<a name="view-the-experiment-in-the-web-portal"></a>
## <a name="view-the-experiment-in-your-workspace-in-azure-machine-learning-studiohttpsmlazurecom"></a>Visa experimentet i din arbets yta i [Azure Machine Learning Studio](https://ml.azure.com)

När ett experiment har körts kan du bläddra till den registrerade experiment körnings posten. Du kan komma åt historiken från [Azure Machine Learning Studio](https://ml.azure.com).

Gå till fliken experiment och välj experimentet. Du kommer till experiment körnings instrument panelen där du kan se spårade mått och diagram som loggas för varje körning. I det här fallet loggade vi MSE och alpha-värdena.

  ![Kör information i Azure Machine Learning Studio](./media/how-to-track-experiments/experiment-dashboard.png)

Du kan öka detalj nivån till en speciell körning för att visa dess utdata eller loggar, eller ladda ned ögonblicks bilden av experimentet som du har skickat så att du kan dela experiment-mappen med andra.

### <a name="viewing-charts-in-run-details"></a>Visa diagram i körnings information

Det finns olika sätt att använda loggnings-API: er för att registrera olika typer av mått under en körning och visa dem som diagram i Azure Machine Learning Studio.

|Loggat värde|Exempel kod| Visa i portalen|
|----|----|----|
|Logga en matris med numeriska värden| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|linje diagram med en variabel|
|Logga ett enkelt numeriskt värde med samma mått namn som används upprepade gånger (som i en for-slinga)| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| linje diagram med en variabel|
|Logga en rad med två numeriska kolumner upprepade gånger|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|Linje diagram med två variabler|
|Logg tabell med 2 numeriska kolumner|`run.log_table(name='Sine Wave', value=sines)`|Linje diagram med två variabler|


## <a name="example-notebooks"></a>Exempel på bärbara datorer
Följande antecknings böcker demonstrerar begrepp i den här artikeln:
* [instruktionen att använda – azureml/Training/Train-i-Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook)
* [instruktionen att använda – azureml/Training/träna-on-Local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [How-to-use-azureml/Training/Logging-API/Logging-API. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Nästa steg

Prova följande steg för att lära dig hur du använder Azure Machine Learning SDK för python:

* Se ett exempel på hur du registrerar den bästa modellen och distribuerar den i självstudien, [träna en bild klassificerings modell med Azure Machine Learning](tutorial-train-models-with-aml.md).

* Lär dig hur du [tränar PyTorch-modeller med Azure Machine Learning](how-to-train-pytorch.md).
