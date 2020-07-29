---
title: Log ML-experiment & mått
titleSuffix: Azure Machine Learning
description: Övervaka dina Azure ML-experiment och övervaka körnings mått för att förbättra skapande processen för modeller. Lägg till loggning i ditt utbildnings skript och Visa de loggade resultaten för en körning.  Använd kör. log, kör. start_logging eller ScriptRunConfig.
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 07/14/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 9833c0974af9a5bcc069ad41cfb57631dbed34dc
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87320960"
---
# <a name="monitor-azure-ml-experiment-runs-and-metrics"></a>Övervaka körningar och mått för Azure ML-experiment
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Förbättra skapande processen för modeller genom att spåra experiment och övervaka körnings mått. I den här artikeln lär du dig att lägga till loggnings kod i ditt utbildnings skript, skicka in en experiment körning, Övervakare som körs och granska resultaten i Azure Machine Learning.

> [!NOTE]
> Azure Machine Learning kan också logga information från andra källor under utbildningen, till exempel automatiserade maskin inlärnings körningar eller Docker-behållaren som kör övnings jobbet. Dessa loggar är inte dokumenterade. Om du stöter på problem och kontaktar Microsoft-supporten kan det hända att de kan använda dessa loggar under fel sökning.

> [!TIP]
> Informationen i det här dokumentet är främst avsedd för data experter och utvecklare som vill övervaka processen för modell inlärning. Om du är administratör som är intresse rad av övervakning av resursanvändningen och händelser från Azure Machine Learning, till exempel kvoter, slutförda inlärnings körningar eller slutförd modell distribution, se [övervaknings Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="available-metrics-to-track"></a>Tillgängliga mått som ska spåras

Du kan lägga till följande mått i en körning medan du tränar ett experiment. Om du vill visa en mer detaljerad lista över vad som kan spåras i en körning, se [referens dokumentationen för körnings klassen](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py).

|Typ| Python-funktionen | Obs!|
|----|:----|:----|
|Skalära värden |Funktioner<br>`run.log(name, value, description='')`<br><br>Exempel:<br>Kör. log ("noggrannhet", 0,95) |Logga ett numeriskt värde eller sträng värde för körningen med det tilldelade namnet. Att logga ett mått på en körning gör att måttet lagras i körnings posten i experimentet.  Du kan logga samma mått flera gånger i en körning, och resultatet betraktas som en Vector för det måttet.|
|Listor|Funktioner<br>`run.log_list(name, value, description='')`<br><br>Exempel:<br>Kör. log_list ("noggrannhet", [0,6, 0,7, 0,87]) | Logga en lista med värden som ska köras med det aktuella namnet.|
|Rad|Funktioner<br>`run.log_row(name, description=None, **kwargs)`<br>Exempel:<br>Kör. log_row ("Y över X", X = 1, Y = 0.4) | Om du använder *log_row* skapas ett mått med flera kolumner enligt beskrivningen i kwargs. Varje namngiven parameter genererar en kolumn med det angivna värdet.  *log_row* kan anropas en gång för att logga en godtycklig tupel eller flera gånger i en slinga för att generera en fullständig tabell.|
|Tabell|Funktioner<br>`run.log_table(name, value, description='')`<br><br>Exempel:<br>Kör. log_table ("Y över X", {"X": [1, 2, 3], "Y": [0.6, 0,7, 0,89]}) | Logga ett Dictionary-objekt i körningen med det aktuella namnet. |
|Avbildningar|Funktioner<br>`run.log_image(name, path=None, plot=None)`<br><br>Exempel:<br>`run.log_image("ROC", plot=plt)` | Logga en bild i körnings posten. Använd log_image för att logga en. PNG-bildfil eller en matplotlib-rityta till körningen.  De här bilderna visas och är jämförbara i körnings posten.|
|Tagga en körning|Funktioner<br>`run.tag(key, value=None)`<br><br>Exempel:<br>Kör. tag ("vald", "Ja") | Tagga körningen med en sträng nyckel och ett valfritt sträng värde.|
|Ladda upp fil eller katalog|Funktioner<br>`run.upload_file(name, path_or_stream)`<br> <br> Exempel:<br>Kör. upload_file ("best_model. PKL", "./Model.PKL") | Ladda upp en fil till körnings posten. Kör automatiskt avbilda fil i den angivna utdatakatalogen som är "./outputs" som standard för de flesta körnings typer.  Använd upload_file endast när ytterligare filer behöver överföras eller om ingen utmatnings katalog har angetts. Vi föreslår att `outputs` du lägger till namnet så att det överförs till katalogen utdata. Du kan visa en lista över alla filer som är associerade med den här körnings posten genom att anropa`run.get_file_names()`|

> [!NOTE]
> Mått för skalärer, listor, rader och tabeller kan ha typen: Float, Integer eller String.

## <a name="choose-a-logging-option"></a>Välj ett loggnings alternativ

Om du vill spåra eller övervaka experimentet måste du lägga till kod för att starta loggning när du skickar in körningen. Följande är exempel på hur du kan starta sändningen:
* __Kör. start_logging__ – Lägg till loggnings funktioner i utbildnings skriptet och starta en interaktiv inloggningssession i det angivna experimentet. **start_logging** skapar en interaktiv körning för användning i scenarier som Notebooks. Alla mått som loggas under sessionen läggs till i körnings posten i experimentet.
* __ScriptRunConfig__ – Lägg till loggnings funktioner i ditt utbildnings skript och Läs in hela skript-mappen med körningen.  **ScriptRunConfig** är en klass för att konfigurera konfigurationer för skript körningar. Med det här alternativet kan du lägga till övervaknings kod för att få ett meddelande om slut för ande eller för att få en visuell widget att övervaka.
* __Design loggning__ – Lägg till loggnings funktioner i en dra-&-Drop designer-pipeline med hjälp av modulen __köra Python-skript__ . Lägg till python-kod till log designer-experiment. 

## <a name="set-up-the-workspace"></a>Konfigurera arbets ytan
Innan du lägger till loggning och skickar ett experiment måste du konfigurera arbets ytan.

1. Läs in arbets ytan. Mer information om hur du anger arbets ytans konfiguration finns i [konfigurations filen för arbets ytan](how-to-configure-environment.md#workspace).

[! Notebook – python [] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb? namn = load_ws)]


## <a name="option-1-use-start_logging"></a>Alternativ 1: Använd start_logging

**start_logging** skapar en interaktiv körning för användning i scenarier som Notebooks. Alla mått som loggas under sessionen läggs till i körnings posten i experimentet.

I följande exempel tågen en enkel sklearn Ridge-modell lokalt i en lokal Jupyter-anteckningsbok. Mer information om hur du skickar experiment till olika miljöer finns i [Konfigurera beräknings mål för modell utbildning med Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-set-up-training-targets).

### <a name="load-the-data"></a>Läs in data

I det här exemplet används diabetes-datauppsättningen, en välkänd liten data uppsättning som medföljer scikit-information. Den här cellen läser in data uppsättningen och delar den i slumpmässiga inlärnings-och test uppsättningar.

[! Notebook – python [] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb? namn = load_data)]

### <a name="add-tracking"></a>Lägg till spårning
Lägg till experiment uppföljning med Azure Machine Learning SDK och ladda upp en beständiga modell i experiment körnings posten. Följande kod lägger till taggar, loggar och laddar upp en modell fil till experimentet.

[! Notebook – python [] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb? namn = create_experiment)]

Skriptet slutar med ```run.complete()``` , vilket anger att körningen är slutförd.  Den här funktionen används vanligt vis i interaktiva scenarier för bärbara datorer.

## <a name="option-2-use-scriptrunconfig"></a>Alternativ 2: Använd ScriptRunConfig

[**ScriptRunConfig**](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) är en klass för att konfigurera konfigurationer för skript körningar. Med det här alternativet kan du lägga till övervaknings kod för att få ett meddelande om slut för ande eller för att få en visuell widget att övervaka.

Det här exemplet expanderas i den grundläggande sklearn Ridge-modellen från ovan. Det gör att en enkel parameter svep för att svepa över alfa värden i modellen för att samla in mått och utbildade modeller i körs under experimentet. Exemplet körs lokalt mot en användar hanterad miljö. 

1. Skapa ett utbildnings skript `train.py` .

   [! code-python [] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train.py)]

2. `train.py`Skript referenser `mylib.py` som gör att du kan hämta listan med alfa värden som ska användas i Ridge-modellen.

   [! code-python [] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/mylib.py)] 

3. Konfigurera en användare hanterad lokal miljö.

   [! Notebook – python [] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb? namn = user_managed_env)]


4. Skicka ```train.py``` skriptet som ska köras i den användar hanterade miljön. Hela skriptets mapp skickas för utbildning, inklusive ```mylib.py``` filen.

   [! Notebook – python [] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb? Name = src)] [! Notebook-python [] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb? namn = kör)]

## <a name="option-3-log-designer-experiments"></a>Alternativ 3: log designer-experiment

Använd modulen __Kör Python-skript__ för att lägga till loggnings logik i design experimenten. Du kan logga alla värden med hjälp av det här arbets flödet, men det är särskilt användbart att logga mått från modulen __utvärdera modell__ för att spåra modell prestanda mellan olika körningar.

1. Anslut en __köra python-skriptfil__ till utdata från modulen __utvärdera modell__ . __Utvärdera modell__ kan resultera i utvärderings resultat av 2 modeller. I följande exempel visas hur du loggar måtten på 2 utgående portar på den överordnade körnings nivån. 

    ![Anslut kör python-skriptfil för att utvärdera modell modulen](./media/how-to-track-experiments/designer-logging-pipeline.png)

1. Klistra in följande kod i __Kör Python-skript__ kod redigeraren för att logga medelvärdet för det absoluta felet för din tränade modell:

    ```python
    # dataframe1 contains the values from Evaluate Model
    def azureml_main(dataframe1=None, dataframe2=None):
        print(f'Input pandas.DataFrame #1: {dataframe1}')
    
        from azureml.core import Run
    
        run = Run.get_context()
    
        # Log the mean absolute error to the parent run to see the metric in the run details page.
        # Note: 'run.parent.log()' should not be called multiple times because of performance issues.
        # If repeated calls are necessary, cache 'run.parent' as a local variable and call 'log()' on that variable.

        # Log left output port result of Evaluate Model. This also works when evaluate only 1 model.
        run.parent.log(name='Mean_Absolute_Error (left port)', value=dataframe1['Mean_Absolute_Error'][0])

        # Log right output port result of Evaluate Model.
        run.parent.log(name='Mean_Absolute_Error (right port)', value=dataframe1['Mean_Absolute_Error'][1])
    
        return dataframe1,
    ```

1. När pipeline-körningen är klar kan du se *Mean_Absolute_Error* på experiment sidan.

    ![Anslut kör python-skriptfil för att utvärdera modell modulen](./media/how-to-track-experiments/experiment-page-metrics-across-runs.png)

## <a name="manage-a-run"></a>Hantera en körning

[Övnings körningen starta, övervaka och Avbryt visar](how-to-manage-runs.md) mer information Azure Machine Learning arbets flöden för hur du hanterar experiment.

## <a name="view-run-details"></a>Visa körnings information

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

Modell träning och övervakning sker i bakgrunden så att du kan köra andra uppgifter medan du väntar. Du kan också vänta tills modellen har slutfört utbildningen innan du kör mer kod. När du använder **ScriptRunConfig**kan du använda ```run.wait_for_completion(show_output = True)``` för att visa när modell träningen är klar. ```show_output```Flaggan ger dig utförliga utdata. 

<a id="queryrunmetrics"></a>

### <a name="query-run-metrics"></a>Kör mått för fråga

Du kan visa måtten för en utbildad modell med ```run.get_metrics()``` . Nu kan du hämta alla mått som loggades i exemplet ovan för att fastställa den bästa modellen.

<a name="view-the-experiment-in-the-web-portal"></a>
## <a name="view-the-experiment-in-your-workspace-in-azure-machine-learning-studio"></a>Visa experimentet i din arbets yta i [Azure Machine Learning Studio](https://ml.azure.com)

När ett experiment har körts kan du bläddra till den registrerade experiment körnings posten. Du kan komma åt historiken från [Azure Machine Learning Studio](https://ml.azure.com).

Gå till fliken experiment och välj experimentet. Du kommer till experiment körnings instrument panelen där du kan se spårade mått och diagram som loggas för varje körning. 

Du kan redigera tabellen kör lista om du vill visa antingen det senaste, lägsta eller högsta loggade värdet för dina körningar. Du kan markera eller avmarkera flera körningar i körnings listan och de valda körningarna fyller diagrammen med dina data. Du kan också lägga till nya diagram eller redigera diagram för att jämföra de inloggade måtten (lägsta, högsta, sista eller alla värden) över flera körningar. För att utforska dina data mer effektivt kan du också maximera dina diagram.

:::image type="content" source="media/how-to-track-experiments/experimentation-tab.gif" alt-text="Kör information i Azure Machine Learning Studio":::

Du kan öka detalj nivån till en speciell körning för att visa dess utdata eller loggar, eller ladda ned ögonblicks bilden av experimentet som du har skickat så att du kan dela experiment-mappen med andra.

### <a name="viewing-charts-in-run-details"></a>Visa diagram i körnings information

Det finns olika sätt att använda loggnings-API: er för att registrera olika typer av mått under en körning och visa dem som diagram i Azure Machine Learning Studio.

|Loggat värde|Exempelkod| Visa i portalen|
|----|----|----|
|Logga en matris med numeriska värden| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|linje diagram med en variabel|
|Logga ett enkelt numeriskt värde med samma mått namn som används upprepade gånger (som i en for-slinga)| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| Linje diagram med en variabel|
|Logga en rad med två numeriska kolumner upprepade gånger|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|Linje diagram med två variabler|
|Logg tabell med 2 numeriska kolumner|`run.log_table(name='Sine Wave', value=sines)`|Linje diagram med två variabler|


## <a name="example-notebooks"></a>Exempelnotebook-filer
Följande antecknings böcker demonstrerar begrepp i den här artikeln:
* [instruktionen att använda – azureml/Training/Train-i-Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook)
* [instruktionen att använda – azureml/Training/träna-on-Local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [How-to-use-azureml/Track-and-Monitor-experiment/Logging-API](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Nästa steg

Prova följande steg och lär dig hur du använder Azure Machine Learning-SDK för Python:

* Se ett exempel på hur du registrerar den bästa modellen och distribuerar den i självstudien, [träna en bild klassificerings modell med Azure Machine Learning](tutorial-train-models-with-aml.md).

* Lär dig hur du [tränar PyTorch-modeller med Azure Machine Learning](how-to-train-pytorch.md).
