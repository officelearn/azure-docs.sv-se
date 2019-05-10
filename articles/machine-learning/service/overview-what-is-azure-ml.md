---
title: Vad är
titleSuffix: Azure Machine Learning service
description: Översikt över Azure Machine Learning-tjänsten – en integrerad lösning från slutpunkt till slutpunkt som gör att dataforskare kan utveckla, experimentera och distribuera avancerade analysprogram i molnskala.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: j-martens
ms.author: jmartens
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: c9ebacd8caf992874969106df58531538ea399ed
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/09/2019
ms.locfileid: "65510438"
---
# <a name="what-is-azure-machine-learning-service"></a>Vad är tjänsten Azure Machine Learning?

Azure Machine Learning-tjänsten är en molntjänst som du använder för att träna, distribuera, automatisera och hantera maskininlärningsmodeller i den omfattande skala som molnet ger.

## <a name="what-is-machine-learning"></a>Vad är maskininlärning?

Maskininlärning är en datavetenskapsteknik som gör att datorer kan använda befintliga data för att göra prognoser om framtida beteenden, resultat och trender. Genom att använda maskininlärning kan datorer lära sig utan att vara explicit programmerade.

Prognoser eller förutsägelser från maskininlärning kan göra appar och enheter smartare. När du handlar på nätet kan maskininlärning till exempel användas för att rekommendera andra produkter som du kanske gillar baserat på vad du har köpt. När ditt kreditkort dras används maskininlärning för att jämföra transaktionen med en transaktionsdatabas, vilket gör det lättare att upptäcka bedrägerier. När din robotdammsugare dammsuger ett rum tar den hjälp av maskininlärning för att avgöra om jobbet är klart.

## <a name="what-is-azure-machine-learning-service"></a>Vad är tjänsten Azure Machine Learning?

Tjänsten Azure Machine Learning tillhandahåller en molnbaserad miljö som du kan använda för att förbereda data och träna, testa, distribuera, hantera och spåra maskininlärningsmodeller. Börja öva på den lokala datorn och sedan skala ut till molnet. Tjänsten har fullständigt stöd för tekniker för öppen källkod som PyTorch, TensorFlow och scikit-Läs och kan användas för alla typer av machine learning, från klassisk ml som djupinlärning, övervakad och oövervakad inlärning. 

Utforska och förbereda data, träna och testa modeller och distribuera dem med hjälp av omfattande verktyg som:
+ En [visuella gränssnittet](ui-quickstart-run-experiment.md) i som du kan dra och släpp-moduler för att skapa experiment och distribuera modeller
+ [Jupyter-anteckningsböcker](https://jupyter.org) i som du använder den [SDK: er](https://docs.microsoft.com/azure/machine-learning/service/#reference) att skriva egen kod som [dessa exempelanteckningsböcker](https://aka.ms/aml-notebooks)
+ [Visual Studio Code-tillägg](how-to-vscode-tools.md)

## <a name="what-can-i-do-with-azure-machine-learning-service"></a>Vad kan jag göra med tjänsten Azure Machine Learning?

Använd den <a href="https://aka.ms/aml-sdk" target="_blank">Azure Machine Learning Python SDK</a> med öppen källkod Python-paket eller Använd den [visuella gränssnittet (förhandsversion)](ui-quickstart-run-experiment.md) att skapa och träna mycket exakta machine learning och modeller för djupinlärning själv i en arbetsyta för Azure Machine Learning-tjänsten.

Du kan välja bland många machine learning komponenter som finns tillgängliga i Python-paket med öppen källkod, till exempel <a href="https://scikit-learn.org/stable/" target="_blank">Scikit-Läs</a>, <a href="https://www.tensorflow.org" target="_blank">Tensorflow</a>, <a href="https://pytorch.org" target="_blank">PyTorch</a>, och <a href="https://mxnet.io" target="_blank">MXNet</a>.

Oavsett om du skriver kod eller Använd det visuella gränssnittet kan du spåra flera körningar eftersom för att hitta den bästa lösningen samt hantera distribuerade modeller.

### <a name="code-first-experience"></a>Kod först upplevelse

Börja öva på din lokala dator med hjälp av den <a href="https://aka.ms/aml-sdk" target="_blank">Azure Machine Learning Python SDK</a> och sedan skala ut till molnet. Tack vare de många tillgängliga [beräkningsmålen](how-to-set-up-training-targets.md), däribland Azure Machine Learning Compute och [Azure Databricks](/azure/azure-databricks/what-is-azure-databricks), och med [tjänster för finjustering av avancerade hyperparametrar](how-to-tune-hyperparameters.md) kan du skapa bättre modeller snabbare genom att använda hela styrkan från molnet.

Du kan också [automatisera modellträning och justera](tutorial-auto-train-models.md) med hjälp av SDK.

### <a name="code-free--low-code-experience"></a>Kodlös / låg kodupplevelse

För kodfria utbildning, Prova:

+ Det visuella gränssnittet för att dra och släpp experimentera och distribution
    
    ![Visuella gränssnittet för Azure Machine Learning-tjänsten](media/overview-what-is-azure-ml/visual-interface.png)

+ Alternativet för Azure portal för automatiserade ML-experiment

### <a name="operationalization-mlops"></a>Driftsättning (MLOps)

När du har rätt modellen kan använda du enkelt den i en webbtjänst på en IoT-enhet eller från Power BI. Mer information finns i artikeln om [hur och var man distribuerar](how-to-deploy-and-where.md). 

Du kan sedan hantera dina distribuerade modeller med hjälp av [Azure Machine Learning SDK för Python](https://aka.ms/aml-sdk) eller [Azure-portalen](https://portal.azure.com/). 

Dessa modeller kan användas och returnerar förutsägelser i [realtid](how-to-consume-web-service.md) eller [asynkront](how-to-run-batch-predictions.md) på stora mängder data.

Och med avancerade [machine learning-pipelines](concept-ml-pipelines.md), kan samarbeta kring varje steg från dataförberedelser, modellinlärning och utvärdering till distribution.

Kom igång med tjänsten Azure Machine Learning genom att gå till [Nästa steg](#next-steps).

## <a name="how-does-azure-machine-learning-service-differ-from-studio"></a>Hur skiljer sig Azure Machine Learning-tjänsten från Studio?

[Machine Learning Studio](../studio/what-is-ml-studio.md) är en gemensam, dra och släpp visual arbetsyta där du kan skapa, testa och distribuera maskininlärningslösningar utan att behöva skriva kod. Den använder fördefinierade och förkonfigurerade machine learning-algoritmer och moduler för datahantering, samt en egen compute-plattformen.

Azure Machine Learning-tjänsten tillhandahåller både SDK: er **- och -** en visual interface(preview) att snabbt Förbered data, träna och distribuera machine learning-modeller. Den här visuella gränssnittet (förhandsversion) ger en liknande dra och släpp-upplevelse till Studio. Dock använder egna beräkningsresurser det visuella gränssnittet till skillnad från den egna beräkningsplattformen Studio och är helt integrerat i Azure Machine Learning-tjänsten.

Här är en snabb jämförelse.

|| Machine Learning Studio | Azure Machine Learning-tjänsten:<br/>Visuellt gränssnitt|
|---| --- | --- |
|| Allmänt tillgänglig (GA) | Förhandsversion|
|Moduler för gränssnittet| Många | Inledande uppsättning populära moduler|
|Beräkningsmål för utbildning| Upphovsrättsskyddad beräkningsmål, CPU-stöd| Har stöd för Azure Machine Learning-beräkning, GPU eller CPU.<br/>(Andra beräkningar som stöds i SDK)|
|Beräkningsmål för distribution| Upphovsrättsskyddad web service-format, inte kan anpassas | Enterprise säkerhetsalternativ & Azure Kubernetes Service. <br/>([Andra beräkningarna](how-to-deploy-and-where.md) stöds i SDK) |
|Automatiserad träning och finjustering av hyperparametrar | Nej | Ännu ej i visuella gränssnittet. <br/> (Stöds i SDK och Azure-portalen.) | 

Prova att använda det visuella gränssnittet (förhandsversion) med [snabbstarten: Förbereda och visualisera data utan att skriva kod](ui-quickstart-run-experiment.md)

> [!NOTE]
> Modeller som skapats i Studio kan inte distribueras eller hanteras av Azure Machine Learning-tjänsten. Modeller som skapas och distribueras i det visuella gränssnittet för tjänsten kan dock hanteras via arbetsytan Azure Machine Learning-tjänsten.

## <a name="free-trial"></a>Gratis utvärderingsversion

Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnadsfria eller betalversionen av Azure Machine Learning-tjänsten](https://aka.ms/AMLFree) i dag.

Du får krediter som du kan använda för att köpa Azure-tjänster. När de är slut kan du behålla kontot och använda [kostnadsfria Azure-tjänster](https://azure.microsoft.com/free/). Ditt kreditkort debiteras aldrig om du inte specifikt ändrar dina inställningar och ber om debitering. Du kan även [aktivera MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F), vilket ger dig krediter varje månad som du kan använda för Azure-betaltjänster.

## <a name="next-steps"></a>Nästa steg

- [Skapa en arbetsyta för Machine Learning-tjänsten](setup-create-workspace.md) att komma igång.

- Följ de fullständiga självstudierna: 
  + [Träna en modell för bildklassificering med Azure Machine Learning-tjänsten](tutorial-train-models-with-aml.md) 
  + [Förbereda data och använda automatiserad maskininlärning för att automatiskt träna en regressionsmodell](tutorial-data-prep.md)

- Lär dig mer om [pipelines för maskininlärning](/azure/machine-learning/service/concept-ml-pipelines) för att skapa, optimera och hantera dina maskininlärningsscenarier.

- Läs den ingående artikeln om [arkitekturen och begrepp för Azure Machine Learning-tjänsten](concept-azure-machine-learning-architecture.md).

- Mer information finns i [andra produkter för maskininlärning från Microsoft](/azure/architecture/data-guide/technology-choices/data-science-and-machine-learning).
