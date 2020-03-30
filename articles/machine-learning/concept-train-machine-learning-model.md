---
title: Modell utbildningsmetoder
titleSuffix: Azure Machine Learning
description: Lär dig de olika metoder du kan använda för att träna modell med Azure Machine Learning. Estimatorer är ett enkelt sätt att arbeta med populära ramverk som Scikit-learn, TensorFlow, Keras, PyTorch och Chainer. Machine Learning-pipelines gör det enkelt att schemalägga obevakade körningar, använda heterogena beräkningsmiljöer och återanvända delar av arbetsflödet. Och kör konfigurationer ger detaljerad kontroll över de beräkningsmål som utbildningsprocessen körs på.
services: machine-learning
ms.service: machine-learning
author: Blackmist
ms.author: larryfr
ms.subservice: core
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: 68ad9cc47d68f7bc3ae952f7e458781cdc1c4ab2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79129765"
---
# <a name="train-models-with-azure-machine-learning"></a>Träna modeller med Azure Machine Learning

Azure Machine Learning erbjuder flera sätt att träna dina modeller, från första lösningar med kod först med SDK till lågkodslösningar som automatiserad maskininlärning och den visuella designern. Använd följande lista för att avgöra vilken träningsmetod som är rätt för dig:

+ [Azure Machine Learning SDK för Python](#python-sdk): Python SDK erbjuder flera sätt att träna modeller, var och en med olika funktioner.

    | Träningsmetod | Beskrivning |
    | ----- | ----- |
    | [Kör konfiguration](#run-configuration) | Ett **generiskt sätt att träna modeller** är att använda ett träningsskript och köra konfiguration. Körningskonfigurationen innehåller den information som behövs för att konfigurera träningsmiljön som används för att träna din modell. Du kan ta en körningskonfiguration, ditt träningsskript och ett beräkningsmål (träningsmiljön) och köra ett utbildningsarbete. |
    | [Automatiserad maskininlärning](#automated-machine-learning) | Automatiserad maskininlärning gör att du kan **träna modeller utan omfattande datavetenskap eller programmeringskunskap.** För personer med datavetenskap och programmeringsbakgrund är det ett sätt att spara tid och resurser genom att automatisera algoritmval och hyperparameterjustering. Du behöver inte oroa dig för att definiera en körningskonfiguration när du använder automatiserad maskininlärning. |
    | [Skattningar](#estimators) | Skattningsklasser **gör det enkelt att träna modeller baserat på populära ramverk för maskininlärning.** Det finns skattningsklasser för **Scikit-learn**, **PyTorch**, **TensorFlow**och **Chainer**. Det finns också en allmän uppskattning som kan användas med ramverk som inte redan har en dedikerad uppskattningsklass. Du behöver inte oroa dig för att definiera en körningskonfiguration när du använder kalkylatorer. |
    | [Pipeline för maskininlärning](#machine-learning-pipeline) | Pipelines är inte en annan utbildningsmetod, utan ett **sätt att definiera ett arbetsflöde med hjälp av modulära, återanvändbara steg**, som kan innehålla utbildning som en del av arbetsflödet. Machine learning-pipelines stöder automatisk maskininlärning, kalkylatorer och kör konfiguration för att träna modeller. Eftersom rörledningarna inte är särskilt inriktade på utbildning är skälen till att använda en rörledning mer varierande än de andra utbildningsmetoderna. I allmänhet kan du använda en pipeline när:<br>* Du vill **schemalägga obevakade processer** som långvariga utbildning jobb eller data förberedelse.<br>* Använd **flera steg** som är samordnade över heterogena beräkningsresurser och lagringsplatser.<br>* Använd pipelinen som en **återanvändbar mall** för specifika scenarier, till exempel omskolning eller batchbedömning.<br>* **Spåra och versionsdatakällor, indata och utdata** för arbetsflödet.<br>* Ditt arbetsflöde **implementeras av olika team som arbetar med specifika steg självständigt.** Steg kan sedan sammanfogas i en pipeline för att implementera arbetsflödet. |

+ [Azure Machine Learning SDK för Python](#r-sdk): SDK använder paketet för att binda till Azure Machine Learnings Python SDK. På så sätt kan du komma åt kärnobjekt och metoder som implementeras i Python SDK från alla R-miljöer.

+ **Designer:** Azure Machine Learning designer (preview) ger en enkel ingång till maskininlärning för att skapa bevis på begrepp, eller för användare med liten kodning erfarenhet. Det låter dig träna modeller med hjälp av ett användargränssnitt för att dra och släppa. Du kan använda Python-kod som en del av designen eller träna modeller utan att skriva någon kod.

+ **CLI**: Machine learning CLI innehåller kommandon för vanliga uppgifter med Azure Machine Learning och används ofta för **skript och automatisera uppgifter**. När du till exempel har skapat ett utbildningsskript eller en pipeline kan du använda CLI för att starta en utbildningskörning enligt ett schema eller när de datafiler som används för utbildningen uppdateras. För utbildningsmodeller innehåller den kommandon som skickar utbildningsjobb. Den kan skicka jobb med hjälp av körningskonfigurationer eller pipelines.

Var och en av dessa träningsmetoder kan använda olika typer av beräkningsresurser för utbildning. Sammantaget kallas dessa resurser för [__beräkningsmål__](concept-azure-machine-learning-architecture.md#compute-targets). Ett beräkningsmål kan vara en lokal dator eller en molnresurs, till exempel en Azure Machine Learning Compute, Azure HDInsight eller en fjärrdator.

## <a name="python-sdk"></a>Python SDK

Med Azure Machine Learning SDK för Python kan du skapa och köra arbetsflöden för maskininlärning med Azure Machine Learning. Du kan interagera med tjänsten från en interaktiv Python-session, Jupyter-anteckningsböcker, Visual Studio-kod eller annan IDE.

* [Vad är Azure Machine Learning SDK för Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [Installera/uppdatera SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
* [Konfigurera en utvecklingsmiljö för Azure Machine Learning](how-to-configure-environment.md)

### <a name="run-configuration"></a>Kör konfiguration

Ett allmänt utbildningsjobb med Azure Machine Learning kan definieras med [runconfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py). Körningskonfigurationen används sedan, tillsammans med dina träningsskript för att träna en modell på ett beräkningsmål.

Du kan börja med en körningskonfiguration för den lokala datorn och sedan växla till en för ett molnbaserat beräkningsmål efter behov. När du ändrar beräkningsmålet ändrar du bara den körningskonfiguration du använder. En körning loggar också information om utbildningsjobbet, till exempel indata, utdata och loggar.

* [Vad är en körningskonfiguration?](concept-azure-machine-learning-architecture.md#run-configurations)
* [Handledning: Träna din första ML-modell](tutorial-1st-experiment-sdk-train.md)
* [Exempel: Jupyter Notebook exempel på träningsmodeller](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training)
* [Så här ställer du in och använder beräkningsmål för modellutbildning](how-to-set-up-training-targets.md)

### <a name="automated-machine-learning"></a>Automatiserad maskininlärning

Definiera iterationer, hyperparameterinställningar, medarisering och andra inställningar. Under utbildningen försöker Azure Machine Learning olika algoritmer och parametrar parallellt. Träningen avbryts när den når de utgångskriterier som du har definierat. Du behöver inte oroa dig för att definiera en körningskonfiguration när du använder kalkylatorer.

> [!TIP]
> Förutom Python SDK kan du även använda Automated ML via [Azure Machine Learning studio](https://ml.azure.com).

* [Vad är automatiserad maskininlärning?](concept-automated-ml.md)
* [Självstudiekurs: Skapa din första klassificeringsmodell med automatiserad maskininlärning](tutorial-first-experiment-automated-ml.md)
* [Handledning: Använd automatiserad maskininlärning för att förutsäga taxipriser](tutorial-auto-train-models.md)
* [Exempel: Exempel på bärbara Jupyter för automatiserad maskininlärning](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)
* [Så här konfigurerar du automatiserade ML-experiment i Python](how-to-configure-auto-train.md)
* [Så här tränar du en prognosmodell för tidsserier](how-to-auto-train-forecast.md)
* [Så här skapar, utforskar och distribuerar du automatiserade maskininlärningsexperiment med Azure Machine Learning studio](how-to-use-automated-ml-for-ml-models.md)

### <a name="estimators"></a>Skattningar

Estimatorer gör det enkelt att träna modeller med hjälp av populära ML-ramverk. Om du använder **Scikit-learn,** **PyTorch,** **TensorFlow**eller **Chainer**bör du överväga att använda en uppskattning för träning. Det finns också en allmän uppskattning som kan användas med ramverk som inte redan har en dedikerad uppskattningsklass. Du behöver inte oroa dig för att definiera en körningskonfiguration när du använder kalkylatorer.

* [Vad är skattställare?](concept-azure-machine-learning-architecture.md#estimators)
* [Självstudiekurs: Träna bildklassificeringsmodeller med MNIST-data och scikit-learn med Hjälp av Azure Machine Learning](tutorial-train-models-with-aml.md)
* [Exempel: Jupyter Notebook exempel på att använda kalkylatorer](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning)
* [Så här skapar du skattningar i utbildningen](how-to-train-ml-models.md)

### <a name="machine-learning-pipeline"></a>Pipeline för maskininlärning

Machine learning-pipelines kan använda de tidigare nämnda träningsmetoderna (kör konfiguration, uppskattning och automatiserad maskininlärning). Pipelines handlar mer om att skapa ett arbetsflöde, så de omfattar mer än bara utbildning av modeller. I en pipeline kan du träna en modell med hjälp av automatiserad maskininlärning, kalkylatorer eller köra konfigurationer.

* [Vad är ML-pipelines i Azure Machine Learning?](concept-ml-pipelines.md)
* [Skapa och kör pipelines för maskininlärning med Azure Machine Learning SDK](how-to-create-your-first-pipeline.md)
* [Självstudiekurs: Använd Azure Machine Learning Pipelines för batchbedömning](tutorial-pipeline-batch-scoring-classification.md)
* [Exempel: Exempel på bärbara jupyter för maskininlärningspipelor](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines)
* [Exempel: Pipeline med automatiserad maskininlärning](https://aka.ms/pl-automl)
* [Exempel: Pipeline med skattningar](https://aka.ms/pl-estimator)

## <a name="r-sdk"></a>R SDK

Med R SDK kan du använda R-språket med Azure Machine Learning. SDK använder paketet för att binda till Azure Machine Learnings Python SDK. På så sätt kan du komma åt kärnobjekt och metoder som implementeras i Python SDK från alla R-miljöer.

Mer information finns i följande artiklar:

* [Självstudiekurs: Skapa en modell för logistisk regression](tutorial-1st-r-experiment.md)
* [Azure Machine Learning SDK för R-referens](https://azure.github.io/azureml-sdk-for-r/index.html)

## <a name="azure-machine-learning-designer"></a>Azure Machine Learning-designer

Designern kan du träna modeller med hjälp av ett dra och släpp-gränssnitt i din webbläsare.

+ [Vad är designern?](concept-designer.md)
+ [Handledning : Förutsäga bil pris](tutorial-designer-automobile-price-train-score.md)
+ [Regression: Förutsäga pris](how-to-designer-sample-regression-automobile-price-basic.md)
+ [Klassificering: Förutsäga intäkter](how-to-designer-sample-classification-predict-income.md)
+ [Klassificering: Förutsäga omsättning, aptitretare och merförsäljning](how-to-designer-sample-classification-churn.md)
+ [Klassificering med anpassat R-skript: Förutsäg flygförseningar](how-to-designer-sample-classification-flight-delay.md)
+ [Textklassificering: Wikipedia SP 500 Dataset](how-to-designer-sample-text-classification.md)

## <a name="cli"></a>CLI

Machine learning CLI är ett tillägg för Azure CLI. Det ger flera PLATTFORMAR CLI-kommandon för att arbeta med Azure Machine Learning. Vanligtvis använder du CLI för att automatisera uppgifter, till exempel utbildning av en maskininlärningsmodell.

* [Använda CLI-tillägget för Azure Machine Learning](reference-azure-machine-learning-cli.md)
* [MLOps på Azure](https://github.com/microsoft/MLOps)

## <a name="next-steps"></a>Nästa steg

Läs om hur du [konfigurerar träningsmiljöer](how-to-set-up-training-targets.md).
