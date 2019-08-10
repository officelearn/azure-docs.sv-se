---
title: Vad är Azure Machine Learning tjänst
description: Översikt över Azure Machine Learning-tjänsten – en integrerad lösning från slutpunkt till slutpunkt som gör att dataforskare kan utveckla, experimentera och distribuera avancerade analysprogram i molnskala.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: j-martens
ms.author: jmartens
ms.date: 08/05/2019
ms.custom: seodec18
ms.openlocfilehash: 80a04333260b7aa710c1598b64a414ce53a1037a
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883318"
---
# <a name="what-is-azure-machine-learning-service"></a>Vad är tjänsten Azure Machine Learning?

Azure Machine Learning-tjänsten är en molntjänst som du använder för att träna, distribuera, automatisera och hantera maskininlärningsmodeller i den omfattande skala som molnet ger.

## <a name="what-is-machine-learning"></a>Vad är maskininlärning?

Maskininlärning är en datavetenskapsteknik som gör att datorer kan använda befintliga data för att göra prognoser om framtida beteenden, resultat och trender. Genom att använda maskininlärning kan datorer lära sig utan att vara explicit programmerade.

Prognoser eller förutsägelser från maskininlärning kan göra appar och enheter smartare. När du handlar på nätet kan maskininlärning till exempel användas för att rekommendera andra produkter som du kanske gillar baserat på vad du har köpt. När ditt kreditkort dras används maskininlärning för att jämföra transaktionen med en transaktionsdatabas, vilket gör det lättare att upptäcka bedrägerier. När din robotdammsugare dammsuger ett rum tar den hjälp av maskininlärning för att avgöra om jobbet är klart.

## <a name="what-is-azure-machine-learning-service"></a>Vad är tjänsten Azure Machine Learning?

Tjänsten Azure Machine Learning tillhandahåller en molnbaserad miljö som du kan använda för att förbereda data och träna, testa, distribuera, hantera och spåra maskininlärningsmodeller. Starta utbildningen på den lokala datorn och skala sedan ut till molnet. Tjänsten har fullt stöd för tekniker med öppen källkod som PyTorch, TensorFlow och scikit – lära sig och kan användas för alla typer av maskin inlärning, från klassisk ml till djup inlärning, övervakad och oövervakad inlärning.

Utforska och Förbered data, träna och testa modeller och distribuera dem med hjälp av omfattande verktyg som:
+ Ett [visuellt gränssnitt](ui-tutorial-automobile-price-train-score.md) där du kan dra-n-släpp-moduler för att bygga experiment och sedan distribuera modeller
+ [Jupyter-anteckningsböcker](https://jupyter.org) där du använder [SDK](https://docs.microsoft.com/azure/machine-learning/service/#reference) : er för att skriva din egen kod, till exempel [de här exempel antecknings böckerna](https://aka.ms/aml-notebooks)
+ [Visual Studio Code-tillägg](how-to-vscode-tools.md)

## <a name="what-can-i-do-with-azure-machine-learning-service"></a>Vad kan jag göra med tjänsten Azure Machine Learning?

Använd <a href="https://aka.ms/aml-sdk" target="_blank">Azure Machine Learning python SDK</a> med python-paket med öppen källkod, eller Använd [Visual Interface (för hands version)](ui-tutorial-automobile-price-train-score.md) för att skapa och träna bättre maskin inlärnings-och djup inlärnings modeller själv i en Azure Machine Learning tjänst Platsen.

Du kan välja bland flera Machine Learning-komponenter som är tillgängliga i python-paket med öppen källkod, till exempel <a href="https://scikit-learn.org/stable/" target="_blank">Scikit – lära</a>, <a href="https://www.tensorflow.org" target="_blank">Tensorflow</a>, <a href="https://pytorch.org" target="_blank">PyTorch</a>och <a href="https://mxnet.io" target="_blank">MXNet</a>.

Oavsett om du skriver kod eller använder det visuella gränssnittet kan du spåra flera körningar när du experimenterar för att hitta den bästa lösningen och hantera de distribuerade modellerna.

### <a name="code-first-experience"></a>Kod-första upplevelse

Starta utbildningen på den lokala datorn med <a href="https://aka.ms/aml-sdk" target="_blank">Azure Machine Learning python SDK</a> och skala sedan ut till molnet. Tack vare de många tillgängliga [beräkningsmålen](how-to-set-up-training-targets.md), däribland Azure Machine Learning Compute och [Azure Databricks](/azure/azure-databricks/what-is-azure-databricks), och med [tjänster för finjustering av avancerade hyperparametrar](how-to-tune-hyperparameters.md) kan du skapa bättre modeller snabbare genom att använda hela styrkan från molnet.

Du kan också [Automatisera modell träning och-justering](tutorial-auto-train-models.md) med hjälp av SDK.

### <a name="code-free--low-code-experience"></a>Kod för kostnads fri/låg kod

Prova följande för kod fri utbildning:

+ Det visuella gränssnittet för experimentering och distribution med dra-n-drop

    ![Visuellt gränssnitt för Azure Machine Learning tjänst](media/overview-what-is-azure-ml/visual-interface.png)

+ Alternativet Azure Portal för automatiserade ML-experiment

### <a name="operationalization-mlops"></a>Driftsättning (MLOps)

När du har rätt modell kan du enkelt använda den i en webb tjänst, på en IoT-enhet eller från Power BI. Mer information finns i artikeln om [hur och var man distribuerar](how-to-deploy-and-where.md).

Du kan sedan hantera dina distribuerade modeller med hjälp av [Azure Machine Learning SDK för Python](https://aka.ms/aml-sdk) eller [Azure-portalen](https://portal.azure.com/).

Dessa modeller kan förbrukas och returnera förutsägelser i [real tid](how-to-consume-web-service.md) eller [asynkront](how-to-run-batch-predictions.md) på stora mängder data.

Och med avancerade [maskin inlärnings pipeliner](concept-ml-pipelines.md)kan du samar beta med varje steg från förberedelse av data, modell utbildning och utvärdering, via distribution. Med pipelines kan du:

* Automatisera dator inlärnings processen från slut punkt till slut punkt i molnet
* Återanvänd komponenter och kör bara stegen igen när du behöver
* Använd olika beräknings resurser i varje steg
* köra batch-bedömnings uppgifter

Kom igång med tjänsten Azure Machine Learning genom att gå till [Nästa steg](#next-steps).

## <a name="how-does-azure-machine-learning-service-differ-from-studio"></a>Hur skiljer sig Azure Machine Learning tjänsten från Studio?

[Machine Learning Studio](../studio/what-is-ml-studio.md) är en gemensam, dra och släpp visuell arbets yta där du kan bygga, testa och distribuera maskin inlärnings lösningar utan att behöva skriva kod. Den använder förinställda och förkonfigurerade Machine Learning-algoritmer och data hanterings moduler samt en patentskyddad beräknings plattform.

Azure Machine Learning-tjänsten tillhandahåller både SDK **-och-** ett visuellt gränssnitt (för hands version) för att snabbt kunna utveckla data, träna och distribuera maskin inlärnings modeller. Det här visuella gränssnittet (för hands version) innehåller en liknande dra-och-släpp-upplevelse till Studio. Till skillnad från den patentskyddade beräknings plattformen i Studio använder Visual-gränssnittet dock dina egna beräknings resurser och är helt integrerade i Azure Machine Learning-tjänsten.

Här är en snabb jämförelse.

|| Machine Learning Studio | Azure Machine Learning tjänst:<br/>Visuellt gränssnitt|
|---| --- | --- |
|| Allmänt tillgänglig (GA) | Förhandsversion|
|Moduler för gränssnitt| Mycket | Inledande uppsättning populära moduler|
|Inlärnings mål| Patentskyddat beräknings mål, endast CPU-stöd| Stöder Azure Machine Learning Compute, GPU eller CPU.<br/>(Andra beräkningar som stöds i SDK)|
|Beräknings mål för distribution| Eget webb tjänst format, inte anpassningsbart | Företags säkerhets alternativ & Azure Kubernetes-tjänsten. <br/>([Andra beräkningar](how-to-deploy-and-where.md) som stöds i SDK) |
|Automatisk modell inlärning och inställning för att justera dem | Nej | Ännu inte i Visual Interface. <br/> (Stöds i SDK och Azure Portal.) |

Prova Visual Interface (för hands version) med [Självstudier: Förutsäg bil priset med det visuella gränssnittet](ui-tutorial-automobile-price-train-score.md).

> [!NOTE]
> Modeller som skapats i Studio kan inte distribueras eller hanteras av Azure Machine Learning tjänsten. Modeller som skapas och distribueras i tjänstens visuella gränssnitt kan dock hanteras via Azure Machine Learning-tjänstens arbets yta.

## <a name="free-trial"></a>Kostnadsfri utvärderingsversion

Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnadsfria eller betalversionen av Azure Machine Learning-tjänsten](https://aka.ms/AMLFree) i dag.

Du får krediter som du kan använda för att köpa Azure-tjänster. När de är slut kan du behålla kontot och använda [kostnadsfria Azure-tjänster](https://azure.microsoft.com/free/). Ditt kreditkort debiteras aldrig om du inte specifikt ändrar dina inställningar och ber om debitering. Du kan även [aktivera MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F), vilket ger dig krediter varje månad som du kan använda för Azure-betaltjänster.

## <a name="next-steps"></a>Nästa steg

- Kom igång genom att [skapa en Machine Learning service-arbetsyta](how-to-manage-workspace.md) .

- Följ de fullständiga självstudierna:
  + [Träna en modell för bildklassificering med Azure Machine Learning-tjänsten](tutorial-train-models-with-aml.md)
  + [Förbereda data och använda automatiserad maskininlärning för att automatiskt träna en regressionsmodell](tutorial-data-prep.md)

- Lär dig mer om [pipelines för maskininlärning](/azure/machine-learning/service/concept-ml-pipelines) för att skapa, optimera och hantera dina maskininlärningsscenarier.

- Läs den ingående artikeln om [arkitekturen och begrepp för Azure Machine Learning-tjänsten](concept-azure-machine-learning-architecture.md).

- Mer information finns i [andra produkter för maskininlärning från Microsoft](/azure/architecture/data-guide/technology-choices/data-science-and-machine-learning).
