---
title: Övervaka och Visa ML-körnings loggar & mått
titleSuffix: Azure Machine Learning
description: Övervaka dina Azure ML-experiment och Visa körnings mått för att förbättra skapande processen för modeller. Använd widgetar och Studio-portalen för att utforska körnings status och Visa körnings poster.
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 07/30/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 11e4b79bf76a4612728992f7c16000d840b9c639
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93285949"
---
# <a name="monitor-and-view-ml-run-logs-and-metrics"></a>Övervaka och Visa ML körnings loggar och mått



I den här artikeln får du lära dig hur du övervakar Azure Machine Learning kör och visar deras loggar. Innan du kan visa loggar måste du aktivera dem först. Mer information finns i [Aktivera loggning i Azure ml Training-körningar](how-to-track-experiments.md).

Loggarna kan hjälpa dig att diagnostisera fel och varningar eller spåra prestanda mått som parametrar och modell noggrannhet. I den här artikeln får du lära dig hur du visar loggar med följande metoder:

> [!div class="checklist"]
> * Övervakare körs i Studio
> * Övervakare som körs med hjälp av Jupyter Notebook-widgeten
> * Övervaka automatiserade maskin inlärnings körningar
> * Visa utgående loggar vid slut för ande
> * Visa utgående loggar i Studio

Allmän information om hur du hanterar dina experiment finns i [starta, övervaka och avbryta inlärnings körningar](how-to-manage-runs.md).

## <a name="monitor-runs-in-the-studio"></a>Övervakare körs i Studio

Använd följande steg för att övervaka körningar för ett bestämt beräknings mål från webbläsaren:

1. I [Azure Machine Learning Studio](https://ml.azure.com/)väljer du din arbets yta och väljer sedan __Beräkna__ på sidans vänstra sida.

1. Välj __utbildnings kluster__ om du vill visa en lista med beräknings mål som används för utbildning. Välj sedan klustret.

    ![Välj utbildnings kluster](./media/how-to-track-experiments/select-training-compute.png)

1. Välj __körningar__. Listan över körningar som använder det här klustret visas. Om du vill visa information om en speciell körning använder du länken i kolumnen __Kör__ . Om du vill visa information om experimentet använder du länken i kolumnen __experimentera__ .

    ![Välj körningar för utbildnings kluster](./media/how-to-track-experiments/show-runs-for-compute.png)
    
    > [!TIP]
    > Eftersom inlärnings mål är en delad resurs kan de ha flera körningar i kö eller aktiva vid en specifik tidpunkt.
    > 
    > En körning kan innehålla underordnade körningar, så ett utbildnings jobb kan resultera i flera poster.

När en körning har slutförts visas den inte längre på den här sidan. Om du vill visa information om slutförda körningar går du till avsnittet __experiment__ i Studio och väljer experimentet och kör. Mer information finns i avsnittet [Visa mått för slutförda körningar](#view-the-experiment-in-the-web-portal).

## <a name="monitor-runs-using-the-jupyter-notebook-widget"></a>Övervaka körningar med hjälp av widgeten Jupyter Notebook

När du använder **ScriptRunConfig** -metoden för att skicka körningar kan du se förloppet för körningen med hjälp av [Jupyter-widgeten](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py&preserve-view=true). Precis som körningsöverföringen är widgeten asynkron och tillhandahåller liveuppdateringar var 10:e till var 15:e sekund tills jobbet har slutförts.

Visa widgeten Jupyter i väntan på att körningen ska slutföras.
    
```python
from azureml.widgets import RunDetails
RunDetails(run).show()
```

![Skärm bild av widgeten Jupyter Notebook](./media/how-to-track-experiments/run-details-widget.png)

Du kan också hämta en länk till samma visning i din arbets yta.

```python
print(run.get_portal_url())
```

## <a name="monitor-automated-machine-learning-runs"></a>Övervaka automatiserade maskin inlärnings körningar

För automatisk maskin inlärning körs, för att få åtkomst till diagram från en tidigare körning, ersätter du `<<experiment_name>>` med lämpligt experiment namn:

```python
from azureml.widgets import RunDetails
from azureml.core.run import Run

experiment = Experiment (workspace, <<experiment_name>>)
run_id = 'autoML_my_runID' #replace with run_ID
run = Run(experiment, run_id)
RunDetails(run).show()
```

![Jupyter Notebook-widget för automatiserad Machine Learning](./media/how-to-track-experiments/azure-machine-learning-auto-ml-widget.png)

## <a name="show-output-upon-completion"></a>Visa utdata vid slut för ande

När du använder **ScriptRunConfig** kan du använda ```run.wait_for_completion(show_output = True)``` för att visa när modell träningen är klar. ```show_output```Flaggan ger dig utförliga utdata. Mer information finns i avsnittet ScriptRunConfig i [så här aktiverar du loggning](how-to-track-experiments.md#scriptrun-logs).

<a id="queryrunmetrics"></a>
## <a name="query-run-metrics"></a>Kör mått för fråga

Du kan visa måtten för en utbildad modell med ```run.get_metrics()``` . Du kan till exempel använda detta med exemplet ovan för att fastställa den bästa modellen genom att leta efter modellen med det lägsta värdet för det lägsta medelvärdet (MSE).

<a name="view-the-experiment-in-the-web-portal"></a>
## <a name="view-run-records-in-the-studio"></a>Visa körnings poster i Studio

Du kan bläddra bland slutförda körnings poster, inklusive loggade mått, i [Azure Machine Learning Studio](https://ml.azure.com).

Gå till fliken **experiment** . Om du vill visa alla dina körningar i arbets ytan över experiment väljer du fliken **alla körningar** . Du kan öka detalj nivån för körningar för vissa experiment genom att använda experiment filtret i den översta meny raden. 

Välj fliken **alla experiment** i den enskilda experiment visningen. På instrument panelen för experimentet kan du se spårade mått och loggar för varje körning. 

Öka detalj nivån till en speciell körning för att visa dess utdata eller loggar, eller ladda ned ögonblicks bilden av experimentet så att du kan dela experiment-mappen med andra.

Du kan också redigera tabellen kör lista för att välja flera körningar och Visa antingen det senaste, lägsta eller högsta loggade värdet för dina körningar. Anpassa dina diagram för att jämföra de inloggade måtten och agg regeringar för flera körningar.

![Kör information i Azure Machine Learning Studio](media/how-to-track-experiments/experimentation-tab.gif)

### <a name="format-charts-in-the-studio"></a>Formatera diagram i Studio

Använd följande metoder i loggnings-API: erna för att påverka Studio visualiserar dina mått.

|Loggat värde|Exempelkod| Format i portalen|
|----|----|----|
|Logga en matris med numeriska värden| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|linje diagram med en variabel|
|Logga ett enkelt numeriskt värde med samma mått namn som används upprepade gånger (som i en for-slinga)| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| Linje diagram med en variabel|
|Logga en rad med två numeriska kolumner upprepade gånger|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|Linje diagram med två variabler|
|Logg tabell med 2 numeriska kolumner|`run.log_table(name='Sine Wave', value=sines)`|Linje diagram med två variabler|


## <a name="next-steps"></a>Nästa steg

Prova följande steg för att lära dig hur du använder Azure Machine Learning:

* Lär dig hur du [spårar experiment och aktiverar loggar i Azure Machine Learning designer](how-to-track-designer-experiments.md).

* Se ett exempel på hur du registrerar den bästa modellen och distribuerar den i självstudien [Träna en bildklassificeringsmodell med Azure Machine Learning](tutorial-train-models-with-aml.md).

