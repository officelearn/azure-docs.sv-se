---
title: Spåra experiment och mått för utbildning – Azure Machine Learning | Microsoft Docs
description: Du kan använda Azure Machine Learning-tjänsten för att spåra dina experiment och övervaka mått för att förbättra modellen skapandeprocessen. Lär dig hur du lägger till loggning i utbildningsskript, skicka experimentet, kontrollera status för ett jobb som körs och hur du visar resultatet av en körning.
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 09/24/2018
ms.openlocfilehash: 9af7e57db0e465f59f43c93d0b5f6ec220836ff7
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2018
ms.locfileid: "52308196"
---
# <a name="track-experiments-and-training-metrics-in-azure-machine-learning"></a>Spåra experiment och utbildning mått i Azure Machine Learning

I Azure Machine Learning-tjänsten kan du spåra dina experiment och övervaka mått för att förbättra modellen skapandeprocessen. I den här artikeln får du får lära dig om olika sätt att lägga till loggning i manuset utbildning, hur du skickar experiment med **start_logging** och **ScriptRunConfig**, så kontrollera status för en jobb som körs, och hur du visar resultatet av en körning. 

>[!NOTE]
> Koden i den här artikeln har testats med Azure Machine Learning SDK version 0.1.74 

## <a name="list-of-training-metrics"></a>Lista över mått för utbildning 

Följande mått kan läggas till en körning vid utbildning ett experiment. Om du vill visa en mer detaljerad lista över vad som kan spåras på en körning, se den [SDK referensdokumentation](https://aka.ms/aml-sdk).

|Typ| Python-funktion | Exempel | Anteckningar|
|----|:----|:----|:----|
|Skalära värden | `run.log(name, value, description='')`| `run.log("accuracy", 0.95) ` |Logga en numeriska eller Anslutningssträngens värde ska köras med det angivna namnet. Logga ett mått på en körning gör det måttet som ska lagras i den kör posten i experimentet.  Du kan logga samma mått flera gånger inom en körning resultatet ses som en vektor för att mått.|
|Listor| `run.log_list(name, value, description='')`| `run.log_list("accuracies", [0.6, 0.7, 0.87])` | Logga in en lista med värden ska köras med det angivna namnet.|
|Rad| `run.log_row(name, description=None, **kwargs)`| `run.log_row("Y over X", x=1, y=0.4)` | Med hjälp av *log_row* skapar ett mått med flera kolumner enligt beskrivningen i kwargs. Varje namngivna parametern genererar en kolumn med det angivna värdet.  *log_row* kan anropas en gång för att logga en godtycklig tuppel eller flera gånger i en loop för att generera en hel tabell.|
|Tabell| `run.log_table(name, value, description='')`| `run.log_table("Y over X", {"x":[1, 2, 3], "y":[0.6, 0.7, 0.89]})` | Logga ett katalogobjekt ska köras med det angivna namnet. |
|Avbildningar| `run.log_image(name, path=None, plot=None)`| `run.log_image("ROC", plt)` | Logga in en avbildning kör posten. Använd log_image för att logga en fil eller en matplotlib ritning körningen.  Dessa avbildningar är synlig och jämförbar i posten kör.|
|Tagga en körning| `run.tag(key, value=None)`| `run.tag("selected", "yes")` | Tagga kör med en strängnyckel och ett valfritt strängvärde.|
|Ladda upp filen eller katalogen|`run.upload_file(name, path_or_stream)`| Run.upload_file (”best_model.pkl” ”,. / model.pkl”) | Ladda upp en fil till kör posten. Körs automatiskt samla in fil i den angivna katalogen, där standardinställningen är ”. / matar ut” för de flesta typer som körs.  Använd upload_file endast när ytterligare filer måste överföras eller en utdatakatalog har inte angetts. Vi rekommenderar att lägga till `outputs` namn så att den hämtar överförs till utdata-katalogen. Du kan lista alla filer som är associerade med detta kör post efter kallas `run.get_file_names()`|

> [!NOTE]
> Mått för skalärer, listor, rader och tabeller kan ha typen: flyttal, heltal eller string.

## <a name="log-metrics-for-experiments"></a>Log mått för experiment

Om du vill spåra och övervaka ditt experiment, måste du lägga till kod för att starta loggning när du skickar in körningen. Här följer några sätt att utlösa kör överföringen:
* __Run.start_logging__ – Lägg till loggningsfunktioner i dina utbildningsskript och starta en interaktiv loggningssession i det angivna experimentet. **start_logging** skapar en interaktiv körning för användning i scenarier, till exempel bärbara datorer. Alla mått som är inloggad under sessionen har lagts till den kör posten i experimentet.
* __ScriptRunConfig__ – Lägg till loggningsfunktioner i utbildningsskript och läsa in mappen hela skriptet med alternativet Kör.  **ScriptRunConfig** är en klass för att konfigurera konfigurationer för skriptet körs. Du kan använda det här alternativet för att lägga till koden för övervakning för att aviseras om slutförande eller för att hämta en visual widget att övervaka.

## <a name="set-up-the-workspace-and-experiment"></a>Ställa in arbetsytan och experiment
Innan du lägger till loggning och skicka ett experiment, måste du ställa in den arbetsytan och experiment.

1. Läs in arbetsytan. Om du vill veta mer om hur du arbetsytans konfiguration, följer du de [snabbstarten](https://docs.microsoft.com/azure/machine-learning/service/quickstart-get-started).

  ```python
  from azureml.core import Workspace, Run
  import azureml.core
  
  ws = Workspace(workspace_name = <<workspace_name>>,
               subscription_id = <<subscription_id>>,
               resource_group = <<resource_group>>)
   ```

2. Skapa experimentet.

  ```python
  from azureml.core import Experiment

  # make up an arbitrary name
  experiment_name = 'train-in-notebook'
  ```
  
## <a name="option-1-use-startlogging"></a>Alternativ 1: Använd start_logging

**start_logging** skapar en interaktiv körning för användning i scenarier, till exempel bärbara datorer. Alla mått som är inloggad under sessionen har lagts till den kör posten i experimentet.

I följande exempel träna en enkel modell sklearn upphöjning lokalt i en lokal Jupyter-anteckningsbok. Mer information om att skicka experiment till olika miljöer finns [konfigurera beräkningsmål för modellträning med Azure Machine Learning-tjänsten](https://docs.microsoft.com/azure/machine-learning/service/how-to-set-up-training-targets).

1. Skapa ett inlärningsskript i en lokal Jupyter-anteckningsbok. 

  ``` python
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
  experiment = Experiment(workspace = ws, name = experiment_name)
  run = experiment.start_logging()
  run.tag("Description","My first run!")
  run.log('alpha', 0.03)
  reg = Ridge(alpha = 0.03)
  reg.fit(data['train']['X'], data['train']['y'])
  preds = reg.predict(data['test']['X'])
  run.log('mse', mean_squared_error(preds, data['test']['y']))
  joblib.dump(value = reg, filename = 'model.pkl')
  # Upload file directly to the outputs folder
  run.upload_file(name = 'outputs/model.pkl', path_or_stream = './model.pkl')

  run.complete()
  ```

Skriptet slutar med ```run.complete()```, som markeras körningen som slutfört.  Detta är vanligt i scenarier med interaktiva anteckningsboken.

## <a name="option-2-use-scriptrunconfig"></a>Alternativ 2: Använd ScriptRunConfig

**ScriptRunConfig** är en klass för att konfigurera konfigurationer för skriptet körs. Du kan använda det här alternativet för att lägga till koden för övervakning för att aviseras om slutförande eller för att hämta en visual widget att övervaka.

Det här exemplet kan utökas med grundläggande sklearn upphöjning modellen ovan. Den gör en enkel parameter Svep svep över alfavärden av modellen för att hämta mått och tränade modeller i körs under experimentet. Exemplet körs lokalt mot en användarhanterade miljö. 

1. Skapa ett inlärningsskript. Här används ```%%writefile%%``` att skriva kod för utbildning till mappen skript som ```train.py```.

  ```python
  %%writefile $project_folder/train.py

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

2. Den ```train.py``` skript referenser ```mylib.py```. Den här filen kan du hämta en lista över alfanumeriska värden som ska användas i modellen upphöjning.

  ```python
  %%writefile $script_folder/mylib.py
  import numpy as np

  def get_alphas():
      # list of numbers from 0.0 to 1.0 with a 0.05 interval
      return np.arange(0.0, 1.0, 0.05)
  ```

3. Konfigurera en lokal miljö med användarhanterade.

  ```python
  from azureml.core.runconfig import RunConfiguration

  # Editing a run configuration property on-fly.
  run_config_user_managed = RunConfiguration()

  run_config_user_managed.environment.python.user_managed_dependencies = True

  # You can choose a specific Python environment by pointing to a Python path 
  #run_config.environment.python.interpreter_path = '/home/user/miniconda3/envs/sdk2/bin/python'
  ```

4. Skicka den ```train.py``` skript som ska köras i användarhanterade-miljö. Den här mappen för hela skriptet har skickats för utbildning, inklusive den ```mylib.py``` filen.

  ```python
  from azureml.core import ScriptRunConfig

  src = ScriptRunConfig(source_directory = './', script = 'train.py', run_config = run_config_user_managed)
  run = experiment.submit(src)
  ```
  
## <a name="view-run-details"></a>Visa körningsinformation

### <a name="monitor-run-with-jupyter-notebook-widgets"></a>Övervakare som körs med Jupyter notebook widgetar
När du använder den **ScriptRunConfig** metod för att skicka körs, du kan se förloppet för körning med en Jupyter-anteckningsbok widget. Precis som körningsöverföringen är widgeten asynkron och tillhandahåller liveuppdateringar var 10:e till var 15:e sekund tills jobbet har slutförts.

1. Visa widgeten Jupyter under väntan på Kör för att slutföra.

  ```python
  from azureml.train.widgets import RunDetails
  RunDetails(run).show()
  ```

  ![Skärmbild av Jupyter notebook widget](./media/how-to-track-experiments/widgets.PNG)

### <a name="get-log-results-upon-completion"></a>Hämta loggresultat när åtgärden har slutförts

Modellen tränas och övervakning sker i bakgrunden så att du kan köra andra uppgifter medan du väntar. Du kan också vänta tills modellen har slutförts utbildning innan du kör mer kod. När du använder **ScriptRunConfig**, du kan använda ```run.wait_for_completion(show_output = True)``` ska visas när modellträning är klar. Den ```show_output``` flaggan ger dig utförliga utdata. 
  
### <a name="query-run-metrics"></a>Frågekörningen mått

Du kan visa mått för en tränade modellen med hjälp av ```run.get_metrics()```. Nu kan du få alla mått som loggats i exemplet ovan för att avgöra den bästa modellen.

<a name='view-the-experiment-in-the-web-portal'/>
## <a name="view-the-experiment-in-the-azure-portal"></a>Visa experiment i Azure portal

När ett experiment har körts kan du bläddra till inspelade experimentet körningsposten. Du kan göra detta på två sätt:

* Hämta URL kör direkt ```print(run.get_portal_url())```
* Visa körningsinformation genom att skicka in namnet på körningen (i det här fallet ```run```). Detta pekar du på experimentnamnet, ID, typ, status, informationssidan, en länk till Azure-portalen och en länk till dokumentationen.

Länk för körningen öppnar du direkt till sidan körningsinformation i Azure-portalen. Här kan du se alla egenskaper, spårade mått, bilder och diagram som loggas i experimentet. I det här fallet loggas vi MSE och alfanumeriska värden.

  ![Skärmbild av körningsinformation i Azure portal](./media/how-to-track-experiments/run-details-page-web.PNG)

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
* [01.Getting-Started/01.Train-within-notebook/01.Train-within-notebook.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/01.train-within-notebook)
* [01.Getting-Started/02.Train-on-Local/02.Train-on-Local.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/02.train-on-local)
* [01.Getting-Started/06.Logging-API/06.Logging-API.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/06.logging-api/06.logging-api.ipynb)

Hämta dessa anteckningsböcker:

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Nästa steg

Prova de här nästa steg om du vill veta hur du använder Azure Machine Learning-SDK för Python:

* Se ett exempel på hur du registrerar den bästa modellen och distribuerar den i självstudierna [tränar en modell för klassificering av avbildning med Azure Machine Learning](tutorial-train-models-with-aml.md).

* Lär dig hur du [skapa PyTorch-modeller med Azure Machine Learning](how-to-train-pytorch.md).
