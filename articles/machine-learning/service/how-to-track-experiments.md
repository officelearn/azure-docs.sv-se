---
title: Logga mått under utbildnings körningar
titleSuffix: Azure Machine Learning service
description: Du kan spåra experiment och övervaka mått för att förbättra skapande processen för modeller. Lär dig hur du lägger till loggning i ditt utbildnings skript, hur du skickar experimentet, hur du kontrollerar förloppet för ett pågående jobb och hur du visar de loggade resultaten för en körning.
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/11/2019
ms.custom: seodec18
ms.openlocfilehash: 7b14ed2c18c1106477e21062afaa4cc8f672c203
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/10/2019
ms.locfileid: "68946389"
---
# <a name="log-metrics-during-training-runs"></a>Logga mått under utbildnings körningar

Förbättra skapande processen för modeller genom att spåra dina experiment och övervaknings mått. I den här artikeln lär du dig hur du lägger till loggning i ditt utbildnings skript, skickar en experiment körning, övervakar körningen och visar resultatet av en körning i Azure Machine Learning-tjänsten.

> [!NOTE]
> Azure Machine Learning tjänsten kan också logga information från andra källor under utbildningen, till exempel AutoML eller Docker-behållaren som kör övnings jobbet. Dessa loggar är inte dokumenterade. Om du stöter på problem och kontaktar Microsoft-supporten kan det hända att de kan använda dessa loggar under fel sökning.

## <a name="list-of-training-metrics"></a>Lista över mått för utbildning 

Följande mått kan läggas till en körning vid utbildning ett experiment. Om du vill visa en mer detaljerad lista över vad som kan spåras på en körning, se den [kör klass referensdokumentation](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py).

|Typ| Python-funktion | Anteckningar|
|----|:----|:----|
|Skalära värden |Funktionen:<br>`run.log(name, value, description='')`<br><br>Exempel:<br>Run.log (”Precision”, 0,95) |Logga en numeriska eller Anslutningssträngens värde ska köras med det angivna namnet. Logga ett mått på en körning gör det måttet som ska lagras i den kör posten i experimentet.  Du kan logga samma mått flera gånger inom en körning resultatet ses som en vektor för att mått.|
|Visar en lista över|Funktionen:<br>`run.log_list(name, value, description='')`<br><br>Exempel:<br>Run.log_list (”noggrannhet” [0,6, 0,7, 0.87]) | Logga in en lista med värden ska köras med det angivna namnet.|
|Rad|Funktionen:<br>`run.log_row(name, description=None, **kwargs)`<br>Exempel:<br>Run.log_row (”Y över X” x = 1, y = 0.4) | Med hjälp av *log_row* skapar ett mått med flera kolumner enligt beskrivningen i kwargs. Varje namngivna parametern genererar en kolumn med det angivna värdet.  *log_row* kan anropas en gång för att logga en godtycklig tuppel eller flera gånger i en loop för att generera en hel tabell.|
|Tabell|Funktionen:<br>`run.log_table(name, value, description='')`<br><br>Exempel:<br>Run.log_table (”Y över X”, {”x”: [1, 2, 3], ”y”: [0,6, 0,7, 0,89 tum]}) | Logga ett katalogobjekt ska köras med det angivna namnet. |
|Avbildningar|Funktionen:<br>`run.log_image(name, path=None, plot=None)`<br><br>Exempel:<br>`run.log_image("ROC", plt)` | Logga in en avbildning kör posten. Använd log_image för att logga en fil eller en matplotlib ritning körningen.  Dessa avbildningar är synlig och jämförbar i posten kör.|
|Tagga en körning|Funktionen:<br>`run.tag(key, value=None)`<br><br>Exempel:<br>Run.tag (”valda”, ”yes”) | Tagga kör med en strängnyckel och ett valfritt strängvärde.|
|Ladda upp filen eller katalogen|Funktionen:<br>`run.upload_file(name, path_or_stream)`<br> <br> Exempel:<br>Run.upload_file (”best_model.pkl” ”,. / model.pkl”) | Ladda upp en fil till kör posten. Körs automatiskt samla in fil i den angivna katalogen, där standardinställningen är ”. / matar ut” för de flesta typer som körs.  Använd upload_file endast när ytterligare filer måste överföras eller en utdatakatalog har inte angetts. Vi rekommenderar att lägga till `outputs` namn så att den hämtar överförs till utdata-katalogen. Du kan lista alla filer som är associerade med detta kör post efter kallas `run.get_file_names()`|

> [!NOTE]
> Mått för skalärer, listor, rader och tabeller kan ha typen: flyttal, heltal eller string.

## <a name="start-logging-metrics"></a>Starta loggning mått

Om du vill spåra och övervaka ditt experiment, måste du lägga till kod för att starta loggning när du skickar in körningen. Här följer några sätt att utlösa kör överföringen:
* __Run.start_logging__ – Lägg till loggningsfunktioner i dina utbildningsskript och starta en interaktiv loggningssession i det angivna experimentet. **start_logging** skapar en interaktiv körning för användning i scenarier, till exempel bärbara datorer. Alla mått som är inloggad under sessionen har lagts till den kör posten i experimentet.
* __ScriptRunConfig__ – Lägg till loggningsfunktioner i utbildningsskript och läsa in mappen hela skriptet med alternativet Kör.  **ScriptRunConfig** är en klass för att konfigurera konfigurationer för skriptet körs. Du kan använda det här alternativet för att lägga till koden för övervakning för att aviseras om slutförande eller för att hämta en visual widget att övervaka.

## <a name="set-up-the-workspace"></a>Ställ in arbetsytan
Innan du lägger till loggning och skicka ett experiment, måste du ställa in arbetsytan.

1. Läs in arbetsytan. Mer information om hur du anger arbets ytans konfiguration finns i [konfigurations filen för arbets ytan](how-to-configure-environment.md#workspace).

   ```python
   from azureml.core import Experiment, Run, Workspace
   import azureml.core
  
   ws = Workspace.from_config()
   ```
  
## <a name="option-1-use-start_logging"></a>Alternativ 1: Använd start_logging

**start_logging** skapar en interaktiv körning för användning i scenarier, till exempel bärbara datorer. Alla mått som är inloggad under sessionen har lagts till den kör posten i experimentet.

I följande exempel träna en enkel modell sklearn upphöjning lokalt i en lokal Jupyter-anteckningsbok. Mer information om att skicka experiment till olika miljöer finns [konfigurera beräkningsmål för modellträning med Azure Machine Learning-tjänsten](https://docs.microsoft.com/azure/machine-learning/service/how-to-set-up-training-targets).

1. Skapa ett inlärningsskript i en lokal Jupyter-anteckningsbok. 

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

2. Lägg till experimentet spårning med Azure Machine Learning-tjänst-SDK och ladda upp en bestående modell till experimentet körningsposten. Följande kod lägger till taggar, loggar, och laddar upp en modellfil till körningen av experimentet.

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

    Skriptet slutar med ```run.complete()```, som markeras körningen som slutfört.  Den här funktionen används vanligtvis i interaktiva notebook-scenarier.

## <a name="option-2-use-scriptrunconfig"></a>Alternativ 2: Använd ScriptRunConfig

[**ScriptRunConfig**](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) är en klass för att konfigurera konfigurationer för skript körningar. Du kan använda det här alternativet för att lägga till koden för övervakning för att aviseras om slutförande eller för att hämta en visual widget att övervaka.

Det här exemplet kan utökas med grundläggande sklearn upphöjning modellen ovan. Den gör en enkel parameter Svep svep över alfavärden av modellen för att hämta mått och tränade modeller i körs under experimentet. Exemplet körs lokalt mot en användarhanterade miljö. 

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

2. `train.py` Skript referenser`mylib.py` som gör att du kan hämta listan med alfa värden som ska användas i Ridge-modellen.

   ```python
   # mylib.py
  
   import numpy as np

   def get_alphas():
      # list of numbers from 0.0 to 1.0 with a 0.05 interval
      return np.arange(0.0, 1.0, 0.05)
   ```

3. Konfigurera en lokal miljö med användarhanterade.

   ```python
   from azureml.core import Environment
    
   # Editing a run configuration property on-fly.
   user_managed_env = Environment("user-managed-env")
    
   user_managed_env.python.user_managed_dependencies = True
    
   # You can choose a specific Python environment by pointing to a Python path 
   #user_managed_env.python.interpreter_path = '/home/johndoe/miniconda3/envs/myenv/bin/python'
   ```

4. Skicka den ```train.py``` skript som ska köras i användarhanterade-miljö. Den här mappen för hela skriptet har skickats för utbildning, inklusive den ```mylib.py``` filen.

   ```python
   from azureml.core import ScriptRunConfig
    
   exp = Experiment(workspace=ws, name="train-on-local")
   src = ScriptRunConfig(source_directory='./', script='train.py')
   src.run_config.environment = user_managed_env
   run = exp.submit(src)
   ```

## <a name="manage-a-run"></a>Hantera en körning

[Övnings körningen starta, övervaka och Avbryt visar](how-to-manage-runs.md) mer information Azure Machine Learning arbets flöden för hur du hanterar experiment.

## <a name="view-run-details"></a>Visa körningsinformation

### <a name="monitor-run-with-jupyter-notebook-widget"></a>Övervaka körning med Jupyter Notebook-widget
När du använder **ScriptRunConfig** -metoden för att skicka körningar kan du se förloppet för körningen med en [Jupyter-widget](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py). Precis som körningsöverföringen är widgeten asynkron och tillhandahåller liveuppdateringar var 10:e till var 15:e sekund tills jobbet har slutförts.

1. Visa widgeten Jupyter under väntan på Kör för att slutföra.

   ```python
   from azureml.widgets import RunDetails
   RunDetails(run).show()
   ```

   ![Skärmbild av Jupyter notebook widget](./media/how-to-track-experiments/run-details-widget.png)

Du kan också hämta en länk till samma visning i din arbets yta.

```python
print(run.get_portal_url())
```

2. **[Automatiserad machine learning i körningar]**  Att komma åt diagrammen från en tidigare körning. Ersätt `<<experiment_name>>` med lämpligt experiment namn:

   ``` 
   from azureml.widgets import RunDetails
   from azureml.core.run import Run

   experiment = Experiment (workspace, <<experiment_name>>)
   run_id = 'autoML_my_runID' #replace with run_ID
   run = Run(experiment, run_id)
   RunDetails(run).show()
   ```

   ![Jupyter notebook widget för automatiserad Machine Learning](./media/how-to-track-experiments/azure-machine-learning-auto-ml-widget.png)


Om du vill visa information om en pipeline-Klicka på pipelinen du vill utforska i tabellen och diagrammen återges i ett popup-fönster i Azure Portal.

### <a name="get-log-results-upon-completion"></a>Hämta loggresultat när åtgärden har slutförts

Modellen tränas och övervakning sker i bakgrunden så att du kan köra andra uppgifter medan du väntar. Du kan också vänta tills modellen har slutförts utbildning innan du kör mer kod. När du använder **ScriptRunConfig**, du kan använda ```run.wait_for_completion(show_output = True)``` ska visas när modellträning är klar. Den ```show_output``` flaggan ger dig utförliga utdata. 


### <a name="query-run-metrics"></a>Frågekörningen mått

Du kan visa mått för en tränade modellen med hjälp av ```run.get_metrics()```. Nu kan du få alla mått som loggats i exemplet ovan för att avgöra den bästa modellen.

<a name="view-the-experiment-in-the-web-portal"></a>
## <a name="view-the-experiment-in-the-azure-portal"></a>Visa experiment i Azure portal

När ett experiment har körts kan du bläddra till inspelade experimentet körningsposten. Du kan komma åt historiken på två sätt:

* Hämta URL kör direkt ```print(run.get_portal_url())```
* Visa körningsinformation genom att skicka in namnet på körningen (i det här fallet ```run```). Det här sättet pekar du på experimentnamnet, ID, typ, status, informationssidan, en länk till Azure-portalen och en länk till dokumentationen.

Länk för körningen öppnar du direkt till sidan körningsinformation i Azure-portalen. Här kan du se alla egenskaper, spårade mått, bilder och diagram som loggas i experimentet. I det här fallet loggas vi MSE och alfanumeriska värden.

  ![Information om körningen i Azure portal](./media/how-to-track-experiments/run-details-page.png)

Du kan också visa alla utdata och loggar för körningen eller ladda ned ögonblicksbild av experimentet som du har skickat in så att du kan dela mappen experiment med andra.

### <a name="viewing-charts-in-run-details"></a>Visa diagram i körningsinformation

Det finns olika sätt att använda loggning API: er till olika posttyper mått under en körning och visa dem som diagram i Azure-portalen. 

|Loggade värde|Exempelkod| Visa på portal|
|----|----|----|
|Logga en matris med numeriska värden| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|Single-variable linjediagram|
|Logga ett numeriskt värde med samma måttnamnet används flera gånger (liknande inifrån en slinga)| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| Single-variable linjediagram|
|Logga en rad med 2 numeriska kolumner upprepade gånger|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|Två variabler linjediagram|
|Tabell med 2 numeriska kolumner|`run.log_table(name='Sine Wave', value=sines)`|Två variabler linjediagram|


## <a name="example-notebooks"></a>Exempel-anteckningsböcker
Följande anteckningsböcker demonstrera begreppen i den här artikeln:
* [How-to-use-azureml/Training/Train-within-notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook)
* [How-to-use-azureml/Training/Train-on-Local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [How-to-use-azureml/Training/Logging-API/Logging-API.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Nästa steg

Prova de här nästa steg om du vill veta hur du använder Azure Machine Learning-SDK för Python:

* Se ett exempel på hur du registrerar den bästa modellen och distribuerar den i självstudierna [tränar en modell för klassificering av avbildning med Azure Machine Learning](tutorial-train-models-with-aml.md).

* Lär dig hur du [skapa PyTorch-modeller med Azure Machine Learning](how-to-train-pytorch.md).
