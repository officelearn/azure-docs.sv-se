---
title: Vad är Azure Machine Learning
description: Översikt över Azure Machine Learning - En integrerad, heltäckande datavetenskapslösning för professionella dataexperter för att utveckla, experimentera och distribuera avancerade analysprogram i molnskala.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: j-martens
ms.author: jmartens
ms.date: 11/04/2019
ms.openlocfilehash: b8dbbb2810277bef20cb3b9b47a63deeea3e0ff9
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "79241614"
---
# <a name="what-is-azure-machine-learning"></a>Vad är Azure Machine Learning?

I den här artikeln får du lära dig mer om Azure Machine Learning, en molnbaserad miljö som du kan använda för att träna, distribuera, automatisera, hantera och spåra ML-modeller. 

Azure Machine Learning kan användas för alla typer av maskininlärning, från klassisk ml till djupinlärning, övervakad och oövervakad inlärning. Oavsett om du föredrar att skriva Python- eller R-kod eller nollkods-/lågkodsalternativ som [designern](tutorial-designer-automobile-price-train-score.md)kan du skapa, träna och spåra mycket exakta maskininlärnings- och djupinlärningsmodeller i en Azure Machine Learning Workspace. 

Börja träna på din lokala dator och skala sedan ut till molnet. 

Tjänsten är också interoperates med populära verktyg med öppen källkod, till exempel PyTorch, TensorFlow och scikit-learn.

> [!VIDEO https://channel9.msdn.com/Events/Connect/Microsoft-Connect--2018/D240/player]

> [!Tip]
> **Gratis provperiod!**  Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnadsfria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree) idag. Du får krediter som du kan använda för att köpa Azure-tjänster. När de är slut kan du behålla kontot och använda [kostnadsfria Azure-tjänster](https://azure.microsoft.com/free/). Ditt kreditkort debiteras aldrig om du inte specifikt ändrar dina inställningar och ber om debitering.


## <a name="what-is-machine-learning"></a>Vad är maskininlärning?

Maskininlärning är en datavetenskapsteknik som gör att datorer kan använda befintliga data för att göra prognoser om framtida beteenden, resultat och trender. Genom att använda maskininlärning kan datorer lära sig utan att vara explicit programmerade.

Prognoser eller förutsägelser från maskininlärning kan göra appar och enheter smartare. När du handlar på nätet kan maskininlärning till exempel användas för att rekommendera andra produkter som du kanske gillar baserat på vad du har köpt. När ditt kreditkort dras används maskininlärning för att jämföra transaktionen med en transaktionsdatabas, vilket gör det lättare att upptäcka bedrägerier. När din robotdammsugare dammsuger ett rum tar den hjälp av maskininlärning för att avgöra om jobbet är klart.

## <a name="machine-learning-tools-to-fit-each-task"></a>Verktyg för maskininlärning som passar varje uppgift 

Azure Machine Learning innehåller alla verktyg som utvecklare och datavetare behöver för sina arbetsflöden för maskininlärning, inklusive:
+ [Azure Machine Learning designer](tutorial-designer-automobile-price-train-score.md) (förhandsversion): dra-n-släpp moduler för att skapa dina experiment och sedan distribuera pipelines.

+ Jupyter-anteckningsböcker: använd våra [exempelböcker eller](https://aka.ms/aml-notebooks) skapa egna anteckningsböcker för att utnyttja våra <a href="https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">SDK for Python-exempel</a> för din maskininlärning. 

+ R-skript eller anteckningsböcker där du använder <a href="https://azure.github.io/azureml-sdk-for-r/reference/index.html" target="_blank">SDK för R för</a> att skriva din egen kod, eller använda R-modulerna i designern.

+ [Visual Studio Code-tillägg](tutorial-setup-vscode-extension.md)

+ [Maskininlärning CLI](reference-azure-machine-learning-cli.md)

+ Ramverk med öppen källkod som PyTorch, TensorFlow och scikit-learn och många fler

Du kan till och med använda [MLflow för att spåra mått och distribuera modeller](how-to-use-mlflow.md) eller Kubeflow för att skapa [end-to-end arbetsflödespipelledningar](https://www.kubeflow.org/docs/azure/).

## <a name="build-ml-models-in-python-or-r"></a>Bygg ML-modeller i Python eller R

Starta träningen på din lokala dator med Azure Machine Learning <a href="https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">Python SDK</a> eller <a href="https://azure.github.io/azureml-sdk-for-r/reference/index.html" target="_blank">R SDK</a>. Sedan kan du skala ut till molnet. 

Tack vare de många tillgängliga [beräkningsmålen](how-to-set-up-training-targets.md), däribland Azure Machine Learning Compute och [Azure Databricks](/azure/azure-databricks/what-is-azure-databricks), och med [tjänster för finjustering av avancerade hyperparametrar](how-to-tune-hyperparameters.md) kan du skapa bättre modeller snabbare genom att använda hela styrkan från molnet.

Du kan också [automatisera modellträning och justering](tutorial-auto-train-models.md) med hjälp av SDK.

## <a name="build-ml-models-with-no-code-tools"></a>Bygg ML-modeller utan kodverktyg

Prova att träna och distribuera kodfritt eller låg kod

+ **Azure Machine Learning designer (förhandsversion)**

  Använd designern för att förbereda data, träna, testa, distribuera, hantera och spåra maskininlärningsmodeller utan att skriva någon kod. Det krävs ingen programmering, du ansluter visuellt datauppsättningar och moduler för att konstruera din modell. Prova [designern handledning](tutorial-designer-automobile-price-train-score.md).

  Läs mer i [översiktsartikeln](concept-designer.md)för Azure Machine Learning designer . 

  ![Exempel på Azure Machine Learning-designer](./media/overview-what-is-azure-ml/designer-drag-and-drop.gif)

+ **Användargränssnitt för automatiserad maskininlärning**

  Lär dig hur du skapar [automatiserade ML-experiment](tutorial-first-experiment-automated-ml.md) i det lätt använda gränssnittet. 

  [![Navigeringsfönstret i Azure Machine Learning studio](./media/overview-what-is-azure-ml/azure-machine-learning-automated-ml-ui.jpg)](./media/overview-what-is-azure-ml/azure-machine-learning-automated-ml-ui.jpg)

## <a name="mlops-deploy--lifecycle-management"></a>MLOps: Distribuera & livscykelhantering
När du har rätt modell kan du enkelt använda den i en webbtjänst, på en IoT-enhet eller från Power BI. Mer information finns i artikeln om [hur och var man distribuerar](how-to-deploy-and-where.md).

Sedan kan du hantera dina distribuerade modeller med hjälp av [Azure Machine Learning SDK för Python,](https://aka.ms/aml-sdk) [Azure Machine Learning studio](https://ml.azure.com)eller machine [learning CLI](reference-azure-machine-learning-cli.md).

Dessa modeller kan förbrukas och returnera förutsägelser i [realtid](how-to-consume-web-service.md) eller [asynkront](how-to-use-parallel-run-step.md) på stora mängder data.

Och med avancerade [machine learning pipelines](concept-ml-pipelines.md)kan du samarbeta på varje steg från dataförberedelse, modellutbildning och utvärdering, genom distribution. Med pipelines kan du:

* Automatisera processen för maskininlärning från på sluten till slutsida i molnet
* Återanvända komponenter och kör bara steg igen när det behövs
* Använda olika beräkningsresurser i varje steg
* Kör batchbedömningsuppgifter

Om du vill använda skript för att automatisera arbetsflödet för maskininlärning tillhandahåller [MACHINE LEARNING CLI](reference-azure-machine-learning-cli.md) kommandoradsverktyg som utför vanliga uppgifter, till exempel skicka in en utbildningskörning eller distribuera en modell.

Information om hur du kommer igång med Azure Machine Learning finns i [Nästa steg](#next-steps).

## <a name="integration-with-other-services"></a>Integration med andra tjänster

Azure Machine Learning fungerar med andra tjänster på Azure-plattformen och integrerar även med verktyg med öppen källkod som Git och MLFlow.

+ Beräkningsmål som __Azure Kubernetes Service__, Azure Container __Instances__, __Azure Databricks__, Azure Data Lake __Analytics__och __Azure HDInsight__. Mer information om beräkningsmål finns i [Vad är beräkningsmål?](concept-compute-target.md).
+ __Azure-händelserutnät__. Mer information finns i [Använda Azure Machine Learning-händelser](concept-event-grid-integration.md).
+ __Azure Monitor__. Mer information finns i [Övervaka Azure Machine Learning](monitor-azure-machine-learning.md).
+ Datalager som __Azure Storage-konton,__ __Azure Data Lake Storage,__ __Azure SQL Database__, Azure Database __för PostgreSQL__och __Azure Open Datasets__. Mer information finns [i Komma åt data i Azure-lagringstjänster](how-to-access-data.md) och Skapa [datauppsättningar med Azure Open Datasets](how-to-create-register-datasets.md#create-datasets-with-azure-open-datasets).
+ __Virtuella Azure-nätverk__. Mer information finns [i Säker experiment och slutledning i ett virtuellt nätverk](how-to-enable-virtual-network.md).
+ __Azure Pipelines__. Mer information finns i [Träna och distribuera maskininlärningsmodeller](/azure/devops/pipelines/targets/azure-machine-learning).
+ __Git-databasloggar__. Mer information finns i [Git-integrering](concept-train-model-git-integration.md).
+ __MLFlow__. Mer information finns i [MLflow för att spåra mått och distribuera modeller](how-to-use-mlflow.md) 
+ __Kubeflow__. Mer information finns i [skapa heltäckande arbetsflödespipelpipelsar](https://www.kubeflow.org/docs/azure/).

### <a name="secure-communications"></a>Säker kommunikation

Ditt Azure Storage-konto, beräkningsmål och andra resurser kan användas säkert i ett virtuellt nätverk för att träna modeller och utföra inferens. Mer information finns [i Säker experiment och slutledning i ett virtuellt nätverk](how-to-enable-virtual-network.md).

## <a name="basic--enterprise-editions"></a><a name="sku"></a>Grundläggande & Enterprise-utgåvor

Azure Machine Learning erbjuder två versioner som är skräddarsydda för dina maskininlärningsbehov:
+ Grundläggande (allmänt tillgänglig)
+ Företag (förhandsgranskning)

Dessa utgåvor avgör vilka verktygsinlärningsverktyg som är tillgängliga för utvecklare och datavetare från deras arbetsyta.   

Med grundläggande arbetsytor kan du fortsätta använda Azure Machine Learning och betala för endast De Azure-resurser som förbrukas under maskininlärningsprocessen. Arbetsytor för Enterprise Edition debiteras endast för sin Azure-förbrukning medan utgåvan är i förhandsversion. Läs mer om vad som är tillgängligt i översikten över Azure Machine [Learning-versionen & prissidan](https://azure.microsoft.com/pricing/details/machine-learning/). 

Du tilldelar utgåvan när du skapar en arbetsyta. Och, redan existerande arbetsytor har konverterats till Basic-utgåvan för dig. Basic edition innehåller alla funktioner som redan var allmänt tillgängliga från och med oktober 2019. Alla experiment på de arbetsytor som har skapats med Funktioner i Enterprise Edition fortsätter att vara tillgängliga för dig i skrivskyddad tills du uppgraderar till Enterprise. Lär dig hur du [uppgraderar en grundläggande arbetsyta till Enterprise Edition](how-to-manage-workspace.md#upgrade). 

Kunder ansvarar för kostnader för beräkning och andra Azure-resurser under den här tiden.

## <a name="next-steps"></a>Nästa steg

- Skapa ditt första experiment med önskad metod:
  + [Använda Python-anteckningsböcker för att träna & distribuera ML-modeller](tutorial-1st-experiment-sdk-setup.md)
  + [Använd R Markdown för att träna & distribuera ML-modeller](tutorial-1st-r-experiment.md) 
  + [Använd automatiserad maskininlärning för att träna & distribuera ML-modeller](tutorial-first-experiment-automated-ml.md) 
  + [Använd designerns dra & släpp-funktioner för att träna & distribuera](tutorial-designer-automobile-price-train-score.md) 
  + [Använd machine learning CLI för att träna och distribuera en modell](tutorial-train-deploy-model-cli.md)

- Lär dig mer om [pipelines för maskininlärning](concept-ml-pipelines.md) för att skapa, optimera och hantera dina maskininlärningsscenarier.

- Läs den djupgående [azure machine learning-arkitekturen och begreppsartikeln.](concept-azure-machine-learning-architecture.md)
