---
title: Logga ML-experiment & mått
titleSuffix: Azure Machine Learning
description: Övervaka dina Azure ML-experiment och övervaka körningsmått för att förbättra processen för att skapa modeller. Lägg till loggning i ditt träningsskript och visa de loggade resultaten av en körning.  Använd run.log, Run.start_logging eller ScriptRunConfig.
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/12/2020
ms.custom: seodec18
ms.openlocfilehash: 0c77e9d0aa4f44f33b1345a6021fc0378459ee85
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79296973"
---
# <a name="monitor-azure-ml-experiment-runs-and-metrics"></a>Övervaka Azure ML-experimentkörningar och mått
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Förbättra processen för att skapa modeller genom att spåra dina experiment och övervaka körningsmått. I den här artikeln får du lära dig hur du lägger till loggningskod i utbildningsskriptet, skickar en experimentkörning, övervakar den körningen och kontrollerar resultaten i Azure Machine Learning.

> [!NOTE]
> Azure Machine Learning kan också logga information från andra källor under utbildning, till exempel automatiska maskininlärningskörningar eller Docker-behållaren som kör utbildningsjobbet. Dessa loggar är inte dokumenterade. Om du stöter på problem och kontaktar Microsoft-supporten kan de kanske använda dessa loggar under felsökningen.

> [!TIP]
> Informationen i det här dokumentet är främst till datavetare och utvecklare som vill övervaka modellutbildningsprocessen. Om du är administratör som är intresserad av att övervaka resursanvändning och händelser från Azure Machine learning, till exempel kvoter, slutförda utbildningskörningar eller slutförda modelldistributioner, läser [du Övervaka Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="available-metrics-to-track"></a>Tillgängliga mått för att spåra

Följande mått kan läggas till i en körning medan du tränar ett experiment. Mer detaljerad lista över vad som kan spåras i en körning finns i [referensdokumentationen Kör](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py).

|Typ| Python(funktionen Python) | Anteckningar|
|----|:----|:----|
|Skalärvärden |Funktion:<br>`run.log(name, value, description='')`<br><br>Exempel:<br>run.log("noggrannhet", 0,95) |Logga ett numeriskt värde eller strängvärde till körningen med förnamnet. Om du loggar ett mått på en körning lagras det måttet i körningsposten i experimentet.  Du kan logga samma mått flera gånger i en körning, vilket innebär att resultatet betraktas som en vektor för det måttet.|
|Listor|Funktion:<br>`run.log_list(name, value, description='')`<br><br>Exempel:<br>run.log_list("noggrannhet", [0,6, 0,7, 0,87]) | Logga en lista med värden till körningen med förnamnet.|
|Rad|Funktion:<br>`run.log_row(name, description=None, **kwargs)`<br>Exempel:<br>run.log_row("Y över X", x=1, y=0,4) | Om du använder *log_row* skapas ett mått med flera kolumner enligt beskrivningen i kwargs. Varje namngiven parameter genererar en kolumn med det angivna värdet.  *log_row* kan anropas en gång för att logga en godtycklig tuppel, eller flera gånger i en loop för att generera en komplett tabell.|
|Tabell|Funktion:<br>`run.log_table(name, value, description='')`<br><br>Exempel:<br>run.log_table("Y över X", {"x":[1, 2, 3], "y":[0.6, 0.7, 0.89]}) | Logga ett ordlisteobjekt till körningen med förnamnet. |
|Avbildningar|Funktion:<br>`run.log_image(name, path=None, plot=None)`<br><br>Exempel:<br>`run.log_image("ROC", plot=plt)` | Logga en bild till körningsposten. Använd log_image för att logga en bildfil eller en matplotlib-ritplats till körningen.  Dessa bilder kommer att vara synliga och jämförbara i körningsposten.|
|Tagga en körning|Funktion:<br>`run.tag(key, value=None)`<br><br>Exempel:<br>run.tag("vald", "ja") | Tagga körningen med en strängnyckel och tillvalt strängvärde.|
|Ladda upp fil eller katalog|Funktion:<br>`run.upload_file(name, path_or_stream)`<br> <br> Exempel:<br>run.upload_file("best_model.pkl", "./model.pkl") | Ladda upp en fil till körningsposten. Körs automatiskt fånga filen i den angivna utdatakatalogen, som standard "./outputs" för de flesta körningstyper.  Använd endast upload_file när ytterligare filer behöver överföras eller en utdatakatalog inte har angetts. Vi föreslår `outputs` att du lägger till namnet så att det laddas upp till utdatakatalogen. Du kan lista alla filer som är associerade med den här körningsposten med`run.get_file_names()`|

> [!NOTE]
> Mått för skalär, listor, rader och tabeller kan ha typ: float, heltal eller sträng.

## <a name="choose-a-logging-option"></a>Välj ett loggningsalternativ

Om du vill spåra eller övervaka experimentet måste du lägga till kod för att börja logga när du skickar körningen. Följande är sätt att utlösa körningen inlämning:
* __Run.start_logging__ - Lägg till loggningsfunktioner i ditt träningsskript och starta en interaktiv loggningssession i det angivna experimentet. **start_logging** skapar en interaktiv körning som kan användas i scenarier som anteckningsböcker. Alla mått som loggas under sessionen läggs till i körningsposten i experimentet.
* __ScriptRunConfig__ - Lägg till loggningsfunktioner i ditt träningsskript och läs in hela skriptmappen med körningen.  **ScriptRunConfig** är en klass för att konfigurera konfigurationer för skriptkörningar. Med det här alternativet kan du lägga till övervakningskod som ska meddelas om slutförande eller för att få en visuell widget att övervaka.

## <a name="set-up-the-workspace"></a>Konfigurera arbetsytan
Innan du lägger till loggning och skickar ett experiment måste du konfigurera arbetsytan.

1. Läs in arbetsytan. Mer information om hur du anger konfigurationen för arbetsytan finns i [konfigurationsfilen för arbetsytan](how-to-configure-environment.md#workspace).

[!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb?name=load_ws)]


## <a name="option-1-use-start_logging"></a>Alternativ 1: Använd start_logging

**start_logging** skapar en interaktiv körning som kan användas i scenarier som anteckningsböcker. Alla mått som loggas under sessionen läggs till i körningsposten i experimentet.

Följande exempel tränar en enkel sklearn Ridge modell lokalt i en lokal Jupyter anteckningsbok. Mer information om hur du skickar in experiment till olika miljöer finns i [Konfigurera beräkningsmål för modellutbildning med Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-set-up-training-targets).

### <a name="load-the-data"></a>Läs in data

I det här exemplet används diabetesdatauppsättningen, en välkänd liten datauppsättning som följer med scikit-learn. Den här cellen läser in datauppsättningen och delar upp den i slumpmässiga tränings- och testuppsättningar.

[!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb?name=load_data)]

### <a name="add-tracking"></a>Lägg till spårning
Lägg till experimentspårning med Hjälp av Azure Machine Learning SDK och ladda upp en beständig modell till experimentkörningsposten. Följande kod lägger till taggar, loggar och överför en modellfil till experimentkörningen.

[!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb?name=create_experiment)]

Skriptet slutar ```run.complete()```med , som markerar körningen som slutförd.  Den här funktionen används vanligtvis i interaktiva scenarier för bärbara datorer.

## <a name="option-2-use-scriptrunconfig"></a>Alternativ 2: Använd ScriptRunConfig

[**ScriptRunConfig**](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) är en klass för att konfigurera konfigurationer för skriptkörningar. Med det här alternativet kan du lägga till övervakningskod som ska meddelas om slutförande eller för att få en visuell widget att övervaka.

Detta exempel expanderar på den grundläggande sklearn Ridge-modellen ovanifrån. Det gör en enkel parameter svep för att svepa över alfavärden för modellen för att fånga mått och utbildade modeller i körningar under experimentet. Exemplet körs lokalt mot en användarhanterad miljö. 

1. Skapa ett `train.py`utbildningsskript .

   [!code-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train.py)]

2. Skriptet `train.py` referenser `mylib.py` som gör att du kan få listan över alfavärden att använda i åsen modellen.

   [!code-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/mylib.py)] 

3. Konfigurera en användarhanterad lokal miljö.

   [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=user_managed_env)]


4. Skicka ```train.py``` skriptet som ska köras i den användarhanterade miljön. Hela den här skriptmappen ```mylib.py``` skickas för utbildning, inklusive filen.

   [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=src)] [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=run)]




## <a name="manage-a-run"></a>Hantera en körning

Artikeln [Start, övervakar och avbrytr utbildning kör](how-to-manage-runs.md) artikeln belyser specifika Azure Machine Learning-arbetsflöden för hur du hanterar dina experiment.

## <a name="view-run-details"></a>Visa körinformation

### <a name="view-activequeued-runs-from-the-browser"></a>Visa aktiva/köade körningar från webbläsaren

Beräkningsmål som används för att träna modeller är en delad resurs. De kan därför ha flera körningar i kö eller aktiv vid en viss tidpunkt. Så här visar du körningar för ett visst beräkningsmål från webbläsaren:

1. Välj arbetsytan i [Azure Machine Learning-studion](https://ml.azure.com/)och välj sedan __Beräkna__ från vänster sida av sidan.

1. Välj __Utbildningskluster__ om du vill visa en lista över beräkningsmål som används för utbildning. Välj sedan klustret.

    ![Välj utbildningsklustret](./media/how-to-track-experiments/select-training-compute.png)

1. Välj __Körs__. Listan över körningar som använder det här klustret visas. Om du vill visa information för en viss körning använder du länken i kolumnen __Kör.__ Om du vill visa information för experimentet använder du länken i kolumnen __Experiment.__

    ![Välj körningar för träningskluster](./media/how-to-track-experiments/show-runs-for-compute.png)
    
    > [!TIP]
    > En körning kan innehålla underordnade körningar, så ett utbildningsarbete kan resultera i flera poster.

När en körning är klar visas den inte längre på den här sidan. Om du vill visa information om slutförda körningar besöker du avsnittet __Experiment__ i studion och väljer experimentet och kör. Mer information finns i avsnittet [Frågekörningsmått.](#queryrunmetrics)

### <a name="monitor-run-with-jupyter-notebook-widget"></a>Bildskärm kör med Jupyter anteckningsbok widget
När du använder **metoden ScriptRunConfig** för att skicka körs kan du titta på förloppet för körningen med en [Jupyter-widget](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py). Precis som körningsöverföringen är widgeten asynkron och tillhandahåller liveuppdateringar var 10:e till var 15:e sekund tills jobbet har slutförts.

1. Visa jupyter-widgeten i väntan på att körningen ska slutföras.

   ```python
   from azureml.widgets import RunDetails
   RunDetails(run).show()
   ```

   ![Skärmbild av widgeten Jupyters anteckningsbok](./media/how-to-track-experiments/run-details-widget.png)

   Du kan också få en länk till samma skärm på arbetsytan.

   ```python
   print(run.get_portal_url())
   ```

2. **[För automatiska maskininlärningskörningar]** Så här öppnar du diagrammen från en tidigare körning. Ersätt `<<experiment_name>>` med lämpligt experimentnamn:

   ``` 
   from azureml.widgets import RunDetails
   from azureml.core.run import Run

   experiment = Experiment (workspace, <<experiment_name>>)
   run_id = 'autoML_my_runID' #replace with run_ID
   run = Run(experiment, run_id)
   RunDetails(run).show()
   ```

   ![Jupyter notebook widget för automatiserad maskininlärning](./media/how-to-track-experiments/azure-machine-learning-auto-ml-widget.png)


Om du vill visa mer information om en pipeline klickar du på den pipeline som du vill utforska i tabellen, och diagrammen återges i ett popup-fönster från Azure Machine Learning-studion.

### <a name="get-log-results-upon-completion"></a>Hämta loggresultat när åtgärden har slutförts

Modellutbildning och övervakning sker i bakgrunden så att du kan köra andra uppgifter medan du väntar. Du kan också vänta tills modellen har slutfört utbildningen innan du kör mer kod. När du använder **ScriptRunConfig** ```run.wait_for_completion(show_output = True)``` kan du använda för att visa när modellutbildningen är klar. Flaggan ```show_output``` ger dig utförlig utdata. 

<a id="queryrunmetrics"></a>

### <a name="query-run-metrics"></a>Frågekörningsmått

Du kan visa måtten för ```run.get_metrics()```en tränad modell med . Du kan nu hämta alla mått som har loggats i exemplet ovan för att bestämma den bästa modellen.

<a name="view-the-experiment-in-the-web-portal"></a>
## <a name="view-the-experiment-in-your-workspace-in-azure-machine-learning-studio"></a>Visa experimentet på din arbetsyta i [Azure Machine Learning Studio](https://ml.azure.com)

När ett experiment har körts kan du bläddra till den inspelade experimentkörningsposten. Du kan komma åt historiken från [Azure Machine Learning-studion](https://ml.azure.com).

Navigera till fliken Experiment och välj experiment. Du kommer till instrumentpanelen för experimentkörning, där du kan se spårade mått och diagram som loggas för varje körning. I det här fallet loggade vi MSE och alfavärdena.

  ![Kör information i Azure Machine Learning-studion](./media/how-to-track-experiments/experiment-dashboard.png)

Du kan öka detaljnivån till en viss körning för att visa dess utdata eller loggar, eller hämta ögonblicksbilden av experimentet du skickade så att du kan dela experimentmappen med andra.

### <a name="viewing-charts-in-run-details"></a>Visa diagram i körinformation

Det finns olika sätt att använda loggnings-API:erna för att registrera olika typer av mått under en körning och visa dem som diagram i Azure Machine Learning Studio.

|Loggat värde|Exempelkod| Visa i portalen|
|----|----|----|
|Logga en matris med numeriska värden| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|en variabel linjediagram|
|Logga ett enda numeriskt värde med samma måttnamn som används upprepade gånger (till exempel inifrån en för-loop)| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| En variabel linjediagram|
|Logga en rad med 2 numeriska kolumner upprepade gånger|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|Tvåvariabelt linjediagram|
|Loggtabell med 2 numeriska kolumner|`run.log_table(name='Sine Wave', value=sines)`|Tvåvariabelt linjediagram|


## <a name="example-notebooks"></a>Exempel på anteckningsböcker
Följande anteckningsböcker visar begrepp i den här artikeln:
* [how-to-use-azureml/training/train-within-notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook)
* [how-to-use-azureml/training/train-on-local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [how-to-use-azureml/track-and-monitor-experiments/logging-api](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Nästa steg

Prova följande steg och lär dig hur du använder Azure Machine Learning-SDK för Python:

* Se ett exempel på hur du registrerar den bästa modellen och distribuerar den i självstudien, [Träna en avbildningsklassificeringsmodell med Azure Machine Learning](tutorial-train-models-with-aml.md).

* Lär dig hur du [tränar PyTorch-modeller med Azure Machine Learning](how-to-train-pytorch.md).
