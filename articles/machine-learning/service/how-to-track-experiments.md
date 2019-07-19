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
ms.openlocfilehash: 269568c172ff6c65c9877f9ad22067a11125b339
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/12/2019
ms.locfileid: "67847425"
---
# <a name="log-metrics-during-training-runs-in-azure-machine-learning"></a>Logga mått under inlärnings körningar i Azure Machine Learning

Förbättra skapande processen för modeller genom att spåra dina experiment och övervaknings mått. I den här artikeln lär du dig hur du lägger till loggning i ditt utbildnings skript, skickar en experiment körning, övervakar körningen och visar resultatet av en körning i Azure Machine Learning-tjänsten.

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

1. Läs in arbetsytan. Om du vill veta mer om hur du anger konfigurationen för arbets ytan följer du stegen i [skapa en Azure Machine Learning service-arbetsyta](setup-create-workspace.md#sdk).

   ```python
   from azureml.core import Experiment, Run, Workspace
   import azureml.core
  
   ws = Workspace.from_config()
   ```
  
## <a name="option-1-use-startlogging"></a>Alternativ 1: Använd start_logging

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

I avsnittet [starta, övervaka och avbryta utbildnings körningar](how-to-manage-runs.md) finns mer information om hur du hanterar dina experiment genom att markera Azure Machine Learning arbets flöden.

## <a name="view-run-details"></a>Visa körningsinformation

### <a name="monitor-run-with-jupyter-notebook-widgets"></a>Övervakare som körs med Jupyter notebook widgetar
När du använder den **ScriptRunConfig** metod för att skicka körs, du kan se förloppet för körning med en Jupyter-anteckningsbok widget. Precis som körningsöverföringen är widgeten asynkron och tillhandahåller liveuppdateringar var 10:e till var 15:e sekund tills jobbet har slutförts.

1. Visa widgeten Jupyter under väntan på Kör för att slutföra.

   ```python
   from azureml.widgets import RunDetails
   RunDetails(run).show()
   ```

   ![Skärmbild av Jupyter notebook widget](./media/how-to-track-experiments/run-details-widget.png)

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

<a name="auto"></a>
## <a name="understanding-automated-ml-charts"></a>Så här fungerar automatisk ML-diagram

När du har skickat ett automatiserade ML-jobb på en bärbar dator, finns en historik över de här körs i din machine learning-arbetsyta. 

Läs mer om:
+ [Diagram och kurvor för klassificering modeller](#classification)
+ [Tabeller och diagram för regressionsmodeller](#regression)
+ [Modellen förklara möjlighet](#model-explain-ability-and-feature-importance)


### <a name="view-the-run-charts"></a>Visa kör diagram

1. Gå till din arbetsyta. 

1. Välj **experiment** på panelen längst till vänster i din arbetsyta.

   ![Skärmbild av menyn för experiment](./media/how-to-track-experiments/azure-machine-learning-auto-ml-experiment-menu.png)

1. Välj experiment som du är intresserad av.

   ![Experiment lista](./media/how-to-track-experiments/azure-machine-learning-auto-ml-experiment-list.png)

1. I tabellen, väljer du hur många som kör.

   ![Körningen av experimentet](./media/how-to-track-experiments/azure-machine-learning-auto-ml-experiment-run.png)

1. Välj Iteration numret för den modell som du vill utforska vidare i tabellen.

   ![Experiment modell](./media/how-to-track-experiments/azure-machine-learning-auto-ml-experiment-model.png)



### <a name="classification"></a>Klassificering

För varje klassificering-modell som du skapar med hjälp av de automatiserade machine learning-funktionerna i Azure Machine Learning, kan du se följande diagram: 
+ [Felmatris](#confusion-matrix)
+ [Precisionsåterkallningsdiagram diagram](#precision-recall-chart)
+ [Mottagare operativa egenskaper (eller ROC)](#roc)
+ [Flytta kurva](#lift-curve)
+ [Vinster kurva](#gains-curve)
+ [Kalibreringskurva](#calibration-plot)

#### <a name="confusion-matrix"></a>Felmatris

En felmatris används för att beskriva resultatet av en modell för klassificering. Varje rad visar instanserna av klassen SANT och varje kolumn representerar instanser av den förväntade klassen. Felmatrisen visar korrekt klassificerade etiketter och felaktigt klassificerad etiketter för en viss modell.

För klassificering, problem ger Azure Machine Learning automatiskt en felmatris för varje modell som har skapats. För varje felmatris visas automatiserade ML korrekt klassificerade etiketterna som gröna och felaktigt klassificerad etiketter rött. Storleken på cirkeln representerar antalet prov i lagerplatsen. Dessutom finns frekvens antal förväntade etiketterna och varje SANT etikett i intilliggande stapeldiagram. 

Exempel 1: En klassificerings modell med dålig ![precision för en klassificerings modell med dålig precision](./media/how-to-track-experiments/azure-machine-learning-auto-ml-confusion-matrix1.png)

Exempel 2: En klassificerings modell med hög noggrannhet (idealisk ![) en klassificerings modell med hög exakthet](./media/how-to-track-experiments/azure-machine-learning-auto-ml-confusion-matrix2.png)


#### <a name="precision-recall-chart"></a>Precisionsåterkallningsdiagram diagram

Du kan jämföra precisionsåterkallningsdiagram kurvor för varje modell att avgöra vilken modell som har en godtagbar relation mellan precision och återkallande för din specifika affärsproblem med det här diagrammet. Det här diagrammet visar makrot genomsnittlig Precisionsåterkallningsdiagram, Micro genomsnittliga Precisionsåterkallningsdiagram och precisionsåterkallningsdiagram som är associerade med alla klasser för en modell.

Termen Precision representerar den möjligheten för en klassificerare att märka alla instanser korrekt. Återkallande representerar möjligheten för en klassificerare att hitta alla instanser av en viss etikett. Precisionsåterkallningsdiagram kurvan visar relationen mellan dessa två begrepp. Vi rekommenderar skulle modellen ha 100% noggrannhet och 100% noggrannhet.

Exempel 1: En klassificerings modell med låg precision och låg ![återkallning av en klassificerings modell med låg precision och låg återkallning](./media/how-to-track-experiments/azure-machine-learning-auto-ml-precision-recall1.png)

Exempel 2: En klassificerings modell med ~ 100% precision och ~ 100% återkalla (idealisk ![) en klassificerings modell med hög precision och återkallande](./media/how-to-track-experiments/azure-machine-learning-auto-ml-precision-recall2.png)

#### <a name="roc"></a>ROC

Mottagare som fungerar egenskap (eller ROC) är en rityta för korrekt klassificerade etiketterna jämfört med felaktigt klassificerad etiketter för en viss modell. ROC-kurvan kan vara mindre informativa när modeller för utbildning på datauppsättningar med hög partiskt, som det inte visas falskt positivt etiketter.

Exempel 1: En klassificerings modell med låg sant etikett och etikett ![modell med hög falsk etikett med låg sant etiketter och höga Felaktiga etiketter](./media/how-to-track-experiments/azure-machine-learning-auto-ml-roc-1.png)

Exempel 2: En klassificerings modell med High True Labels och low ![false etiketter en klassificerings modell med höga sanna etiketter och låg falskt etiketter](./media/how-to-track-experiments/azure-machine-learning-auto-ml-roc-2.png)

#### <a name="lift-curve"></a>Flytta kurva

Du kan jämföra hissen av modellen som skapats automatiskt med Azure Machine Learning till i baslinjen för att kunna visa vinsten värdet för den specifika modellen.

Lift scheman används för att utvärdera prestanda för en modell för klassificering. Den visar hur mycket bättre du kan förvänta dig att göra med en modell som jämfört med utan en modell. 

Exempel 1: Modellen presterar sämre än en slumpmässig markerings modell ![som är en klassificerings modell som är sämre än en slumpmässig markerings modell](./media/how-to-track-experiments/azure-machine-learning-auto-ml-lift-curve1.png)

Exempel 2: Modellen fungerar bättre än en slumpmässig markerings ![modell som en klassificerings modell som fungerar bättre](./media/how-to-track-experiments/azure-machine-learning-auto-ml-lift-curve2.png)

#### <a name="gains-curve"></a>Vinster kurva

Ett diagram får utvärderar prestanda för en modell för klassificering av varje del av data. Den visar för vardera percentil för datamängd, hur mycket bättre du kan förvänta dig att utföra jämfört med mot ett slumpmässigt urval-modellen.

Använda ackumulerade vinster diagrammet för att hjälpa dig att välja den klassificering brytfrekvens med hjälp av en procentsats som motsvarar en önskad vinst från modellen. Den här informationen innehåller ett annat sätt att titta på resultaten i den medföljande ökningsdiagrammet.

Exempel 1: En klassificerings modell med minimal ![förstärkning av en klassificerings modell med minimal vinst](./media/how-to-track-experiments/azure-machine-learning-auto-ml-gains-curve1.png)

Exempel 2: En klassificerings modell med betydande ![förstärkning av en klassificerings modell med betydande vinst](./media/how-to-track-experiments/azure-machine-learning-auto-ml-gains-curve2.png)

#### <a name="calibration-plot"></a>Kalibreringskurva

Alla klassificering problem, kan du läsa kalibrering raden för micro medelvärde, makrot medelvärde och varje klass i en viss förutsägelsemodell. 

En kalibreringskurva används för att visa förtroendet hos en förutsägelsemodell. Det gör du genom att som visar relationen mellan sannolikheten förväntade och faktiska sannolikheten, där ”sannolikhet” representerar sannolikheten att en viss instans hör under vissa etiketten. En bra justerat modell överensstämmer med y = x rad, där det är ganska säker på i dess förutsägelser. En modell för över confident överensstämmer med y = 0 rad, där den förväntade sannolikheten finns men det finns inga faktiska sannolikheten.

Exempel 1: En mer välkalibrerad modell ![ som är mer bra kalibrerad modell](./media/how-to-track-experiments/azure-machine-learning-auto-ml-calib-curve1.png)

Exempel 2: En över-trygg modell ![för en över-säker modell](./media/how-to-track-experiments/azure-machine-learning-auto-ml-calib-curve2.png)

### <a name="regression"></a>Regression
För varje regressionsmodell du skapar med hjälp av de automatiserade machine learning-funktionerna i Azure Machine Learning kan du se följande diagram: 
+ [Förväntade vs. SANT](#pvt)
+ [Histogram för restbelopp](#histo)

<a name="pvt"></a>

#### <a name="predicted-vs-true"></a>Förväntade vs. True

Förväntade vs. SANT visar förhållandet mellan ett prognostiserat värde och dess correlating SANT värde för ett regressionsproblem. Det här diagrammet kan användas för att mäta prestanda för en modell som närmare y = x rad de förväntade värdena är, desto bättre noggrannhet för en förutsägelsemodell.

Efter varje körning visas en förväntad kontra SANT graph för varje regressionsmodell. För att skydda integriteten för värden är binned tillsammans och storleken på varje lagerplats visas som ett stapeldiagram på den nedre delen av diagramområdet. Du kan jämföra förutsägande modell med området ljusare nyans som visar fel marginaler mot perfekt värdet för där modellen ska vara.

Exempel 1: En Regressions modell med låg noggrannhet i ![förutsägelser en Regressions modell med låg precision i förutsägelserna](./media/how-to-track-experiments/azure-machine-learning-auto-ml-regression1.png)

Exempel 2: En Regressions modell med hög noggrannhet i dess ![förutsägelser en Regressions modell med hög noggrannhet i dess förutsägelser](./media/how-to-track-experiments/azure-machine-learning-auto-ml-regression2.png)

<a name="histo"></a>

#### <a name="histogram-of-residuals"></a>Histogram för restbelopp

En återstående representerar en observerade y – förväntade y. Om du vill visa en felmarginalen med låg bias bör histogram för restbelopp skapas som en klockformad kurva inriktade på 0. 

Exempel 1: En Regressions modell med bias i sina ![fel sa Regressions modell med en förskjutning i sina fel](./media/how-to-track-experiments/azure-machine-learning-auto-ml-regression3.png)

Exempel 2: En Regressions modell med mer jämn fördelning av ![fel en Regressions modell med mer jämna distributioner av fel](./media/how-to-track-experiments/azure-machine-learning-auto-ml-regression4.png)

### <a name="model-explain-ability-and-feature-importance"></a>Modeller förklara möjligheten och funktionen prioritet

Funktionen vikten ger ett värde som anger hur värdefull varje funktion befann sig i konstruktion av en modell. Du kan granska funktionen vikten poäng för modellen övergripande samt per klass på en förutsägelsemodell. Per funktion kan du se hur vikten jämförs mot varje klass och övergripande.

![Funktionen förklara möjlighet](./media/how-to-track-experiments/azure-machine-learning-auto-ml-feature-explain1.png)

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
