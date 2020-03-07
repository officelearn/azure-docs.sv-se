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
ms.date: 12/05/2019
ms.custom: seodec18
ms.openlocfilehash: e6b2f73540a0af7ed9c12469406a77d1bed8a2b4
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78396423"
---
# <a name="monitor-azure-ml-experiment-runs-and-metrics"></a>Övervaka körningar och mått för Azure ML-experiment
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Förbättra skapande processen för modeller genom att spåra experiment och övervaka körnings mått. I den här artikeln lär du dig att lägga till loggnings kod i ditt utbildnings skript, skicka in en experiment körning, Övervakare som körs och granska resultaten i Azure Machine Learning.

> [!NOTE]
> Azure Machine Learning kan också logga information från andra källor under utbildningen, till exempel automatiserade maskin inlärnings körningar eller Docker-behållaren som kör övnings jobbet. Dessa loggar är inte dokumenterade. Om du stöter på problem och kontaktar Microsoft-supporten kan det hända att de kan använda dessa loggar under fel sökning.

> [!TIP]
> Informationen i det här dokumentet är främst avsedd för data experter och utvecklare som vill övervaka processen för modell inlärning. Om du är administratör som är intresse rad av övervakning av resursanvändningen och händelser från Azure Machine Learning, till exempel kvoter, slutförda inlärnings körningar eller slutförd modell distribution, se [övervaknings Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="available-metrics-to-track"></a>Tillgängliga mått som ska spåras

Följande mått kan läggas till en körning vid utbildning ett experiment. Om du vill visa en mer detaljerad lista över vad som kan spåras i en körning, se [referens dokumentationen för körnings klassen](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py).

|Typ| Python-funktion | Anteckningar|
|----|:----|:----|
|Skalära värden |Funktionen:<br>`run.log(name, value, description='')`<br><br>Exempel:<br>Run.log (”Precision”, 0,95) |Logga en numeriska eller Anslutningssträngens värde ska köras med det angivna namnet. Logga ett mått på en körning gör det måttet som ska lagras i den kör posten i experimentet.  Du kan logga samma mått flera gånger inom en körning resultatet ses som en vektor för att mått.|
|Visar en lista över|Funktionen:<br>`run.log_list(name, value, description='')`<br><br>Exempel:<br>Run.log_list (”noggrannhet” [0,6, 0,7, 0.87]) | Logga in en lista med värden ska köras med det angivna namnet.|
|Rad|Funktionen:<br>`run.log_row(name, description=None, **kwargs)`<br>Exempel:<br>Run.log_row (”Y över X” x = 1, y = 0.4) | Om du använder *log_row* skapas ett mått med flera kolumner enligt beskrivningen i kwargs. Varje namngivna parametern genererar en kolumn med det angivna värdet.  *log_row* kan anropas en gång för att logga en godtycklig tupel eller flera gånger i en slinga för att generera en fullständig tabell.|
|Tabell|Funktionen:<br>`run.log_table(name, value, description='')`<br><br>Exempel:<br>Run.log_table (”Y över X”, {”x”: [1, 2, 3], ”y”: [0,6, 0,7, 0,89 tum]}) | Logga ett katalogobjekt ska köras med det angivna namnet. |
|Avbildningar|Funktionen:<br>`run.log_image(name, path=None, plot=None)`<br><br>Exempel:<br>`run.log_image("ROC", plot=plt)` | Logga in en avbildning kör posten. Använd log_image för att logga en fil eller en matplotlib ritning körningen.  Dessa avbildningar är synlig och jämförbar i posten kör.|
|Tagga en körning|Funktionen:<br>`run.tag(key, value=None)`<br><br>Exempel:<br>Run.tag (”valda”, ”yes”) | Tagga kör med en strängnyckel och ett valfritt strängvärde.|
|Ladda upp filen eller katalogen|Funktionen:<br>`run.upload_file(name, path_or_stream)`<br> <br> Exempel:<br>Run.upload_file (”best_model.pkl” ”,. / model.pkl”) | Ladda upp en fil till kör posten. Körs automatiskt samla in fil i den angivna katalogen, där standardinställningen är ”. / matar ut” för de flesta typer som körs.  Använd upload_file endast när ytterligare filer måste överföras eller en utdatakatalog har inte angetts. Vi rekommenderar att du lägger till `outputs` till namnet så att det överförs till katalogen utdata. Du kan visa en lista över alla filer som är associerade med den här körnings posten genom att anropa `run.get_file_names()`|

> [!NOTE]
> Mått för skalärer, listor, rader och tabeller kan ha typen: flyttal, heltal eller string.

## <a name="choose-a-logging-option"></a>Välj ett loggnings alternativ

Om du vill spåra och övervaka ditt experiment, måste du lägga till kod för att starta loggning när du skickar in körningen. Här följer några sätt att utlösa kör överföringen:
* __Kör. start_logging__ – Lägg till loggnings funktioner i utbildnings skriptet och starta en interaktiv inloggningssession i det angivna experimentet. **start_logging** skapar en interaktiv körning för användning i scenarier som Notebooks. Alla mått som är inloggad under sessionen har lagts till den kör posten i experimentet.
* __ScriptRunConfig__ – Lägg till loggnings funktioner i ditt utbildnings skript och Läs in hela skript-mappen med körningen.  **ScriptRunConfig** är en klass för att konfigurera konfigurationer för skript körningar. Du kan använda det här alternativet för att lägga till koden för övervakning för att aviseras om slutförande eller för att hämta en visual widget att övervaka.

## <a name="set-up-the-workspace"></a>Ställ in arbetsytan
Innan du lägger till loggning och skicka ett experiment, måste du ställa in arbetsytan.

1. Läs in arbetsytan. Mer information om hur du anger arbets ytans konfiguration finns i [konfigurations filen för arbets ytan](how-to-configure-environment.md#workspace).

   ```python
   from azureml.core import Experiment, Run, Workspace
   import azureml.core
  
   ws = Workspace.from_config()
   ```
  
## <a name="option-1-use-start_logging"></a>Alternativ 1: Använd start_logging

**start_logging** skapar en interaktiv körning för användning i scenarier som Notebooks. Alla mått som är inloggad under sessionen har lagts till den kör posten i experimentet.

I följande exempel träna en enkel modell sklearn upphöjning lokalt i en lokal Jupyter-anteckningsbok. Mer information om hur du skickar experiment till olika miljöer finns i [Konfigurera beräknings mål för modell utbildning med Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-set-up-training-targets).

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

2. Lägg till experiment uppföljning med Azure Machine Learning SDK och ladda upp en beständiga modell i experiment körnings posten. Följande kod lägger till taggar, loggar, och laddar upp en modellfil till körningen av experimentet.

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

    Skriptet slutar med ```run.complete()```, vilket markerar kör som slutfört.  Den här funktionen används vanligtvis i interaktiva notebook-scenarier.

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

2. `train.py` skript referenser `mylib.py` som gör att du kan hämta en lista över de alpha-värden som ska användas i Ridge-modellen.

   ```python
   # mylib.py
  
   import numpy as np

   def get_alphas():
      # list of numbers from 0.0 to 1.0 with a 0.05 interval
      return np.arange(0.0, 1.0, 0.05)
   ```

3. Konfigurera en lokal miljö med användarhanterade.

   ```python
   from azureml.core.environment import Environment
    
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

## <a name="view-run-details"></a>Visa körningsinformation

### <a name="view-activequeued-runs-from-the-browser"></a>Visa aktiva/köade körningar från webbläsaren

Beräknings mål som används för att träna modeller är en delad resurs. Därför kan de ha flera körningar i kö eller aktiva vid en specifik tidpunkt. Använd följande steg för att se körningarna för ett bestämt beräknings mål i webbläsaren:

1. Välj din arbets yta i [Azure Machine Learning Studio](https://ml.azure.com/)och välj sedan __Beräkna__ på sidans vänstra sida.

1. Välj __utbildnings kluster__ om du vill visa en lista med beräknings mål som används för utbildning. Välj sedan klustret.

    ![Välj utbildnings kluster](./media/how-to-track-experiments/select-training-compute.png)

1. Välj __körningar__. Listan över körningar som använder det här klustret visas. Om du vill visa information om en speciell körning använder du länken i kolumnen __Kör__ . Om du vill visa information om experimentet använder du länken i kolumnen __experimentera__ .

    ![Välj körningar för utbildnings kluster](./media/how-to-track-experiments/show-runs-for-compute.png)
    
    > [!TIP]
    > En körning kan innehålla underordnade körningar, så ett utbildnings jobb kan resultera i flera poster.

När en körning har slutförts visas den inte längre på den här sidan. Om du vill visa information om slutförda körningar går du till avsnittet __experiment__ i Studio och väljer experimentet och kör. Mer information finns i avsnittet [köra mått för frågor](#queryrunmetrics) .

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

2. **[För automatiserade maskin inlärnings körningar]** För att få åtkomst till diagram från en tidigare körning. Ersätt `<<experiment_name>>` med lämpligt experiment namn:

   ``` 
   from azureml.widgets import RunDetails
   from azureml.core.run import Run

   experiment = Experiment (workspace, <<experiment_name>>)
   run_id = 'autoML_my_runID' #replace with run_ID
   run = Run(experiment, run_id)
   RunDetails(run).show()
   ```

   ![Jupyter notebook widget för automatiserad Machine Learning](./media/how-to-track-experiments/azure-machine-learning-auto-ml-widget.png)


Om du vill visa mer information om en pipeline klickar du på pipelinen som du vill utforska i tabellen och diagrammet visas i ett popup-fönster från Azure Machine Learning Studio.

### <a name="get-log-results-upon-completion"></a>Hämta loggresultat när åtgärden har slutförts

Modellen tränas och övervakning sker i bakgrunden så att du kan köra andra uppgifter medan du väntar. Du kan också vänta tills modellen har slutförts utbildning innan du kör mer kod. När du använder **ScriptRunConfig**kan du använda ```run.wait_for_completion(show_output = True)``` för att visa när modell träningen är klar. Med flaggan ```show_output``` får du utförliga utdata. 

<a id="queryrunmetrics"></a>

### <a name="query-run-metrics"></a>Frågekörningen mått

Du kan visa måtten för en tränad modell med ```run.get_metrics()```. Nu kan du få alla mått som loggats i exemplet ovan för att avgöra den bästa modellen.

<a name="view-the-experiment-in-the-web-portal"></a>
## <a name="view-the-experiment-in-your-workspace-in-azure-machine-learning-studio"></a>Visa experimentet i din arbets yta i [Azure Machine Learning Studio](https://ml.azure.com)

När ett experiment har körts kan du bläddra till inspelade experimentet körningsposten. Du kan komma åt historiken från [Azure Machine Learning Studio](https://ml.azure.com).

Gå till fliken experiment och välj experimentet. Du kommer till experiment körnings instrument panelen där du kan se spårade mått och diagram som loggas för varje körning. I det här fallet loggas vi MSE och alfanumeriska värden.

  ![Kör information i Azure Machine Learning Studio](./media/how-to-track-experiments/experiment-dashboard.png)

Du kan öka detalj nivån till en speciell körning för att visa dess utdata eller loggar, eller ladda ned ögonblicks bilden av experimentet som du har skickat så att du kan dela experiment-mappen med andra.

### <a name="viewing-charts-in-run-details"></a>Visa diagram i körningsinformation

Det finns olika sätt att använda loggnings-API: er för att registrera olika typer av mått under en körning och visa dem som diagram i Azure Machine Learning Studio.

|Loggade värde|Exempelkod| Visa på portal|
|----|----|----|
|Logga en matris med numeriska värden| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|Single-variable linjediagram|
|Logga ett numeriskt värde med samma måttnamnet används flera gånger (liknande inifrån en slinga)| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| Single-variable linjediagram|
|Logga en rad med 2 numeriska kolumner upprepade gånger|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|Två variabler linjediagram|
|Tabell med 2 numeriska kolumner|`run.log_table(name='Sine Wave', value=sines)`|Två variabler linjediagram|


## <a name="example-notebooks"></a>Exempel-anteckningsböcker
Följande anteckningsböcker demonstrera begreppen i den här artikeln:
* [instruktionen att använda – azureml/Training/Train-i-Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook)
* [instruktionen att använda – azureml/Training/träna-on-Local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [How-to-use-azureml/Track-and-Monitor-experiment/Logging-API](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Nästa steg

Prova följande steg och lär dig hur du använder Azure Machine Learning-SDK för Python:

* Se ett exempel på hur du registrerar den bästa modellen och distribuerar den i självstudien, [träna en bild klassificerings modell med Azure Machine Learning](tutorial-train-models-with-aml.md).

* Lär dig hur du [tränar PyTorch-modeller med Azure Machine Learning](how-to-train-pytorch.md).
