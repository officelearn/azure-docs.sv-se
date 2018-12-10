---
title: Vad är tjänsten Azure Machine Learning?
description: Maskininlärning i molnet och vad du kan använda det till. Översikt över Azure Machine Learning-tjänsten – en integrerad lösning från slutpunkt till slutpunkt som gör att dataforskare kan utveckla, experimentera och distribuera avancerade analysprogram i molnskala.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: overview
ms.reviewer: jmartens
author: garyericson
ms.author: garye
ms.date: 12/04/2018
ms.openlocfilehash: fc241b284ab9370e03debf3e0851394c32087a57
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52870704"
---
# <a name="what-is-azure-machine-learning-service"></a>Vad är tjänsten Azure Machine Learning?

Azure Machine Learning-tjänsten är en molntjänst som du kan använda för att träna, distribuera, automatisera och hantera maskininlärningsmodeller i den omfattande skala som molnet ger.

## <a name="what-is-machine-learning"></a>Vad är maskininlärning?

Maskininlärning är en datavetenskapsteknik som gör att datorer kan använda befintliga data för att göra prognoser om framtida beteenden, resultat och trender. Med maskininlärning kan datorer lära sig utan att vara explicit programmerade.

Prognoser eller förutsägelser från maskininlärning kan göra appar och enheter smartare. När du handlar på nätet kan maskininlärning till exempel användas för att rekommendera andra produkter som du kanske gillar baserat på vad du har köpt. När ditt kreditkort dras används maskininlärning för att jämföra transaktionen med en transaktionsdatabas, vilket gör det lättare att upptäcka bedrägerier. När din robotdammsugare dammsuger ett rum tar den hjälp av maskininlärning för att avgöra om jobbet är klart.

## <a name="what-is-azure-machine-learning-service"></a>Vad är tjänsten Azure Machine Learning?

Tjänsten Azure Machine Learning tillhandahåller en molnbaserad miljö som du kan använda för att utveckla, träna, testa, distribuera, hantera och spåra maskininlärningsmodeller.

[ ![Arbetsflöde i tjänsten Azure Machine Learning](./media/overview-what-is-azure-ml/aml.png) ] (./media/overview-what-is-azure-ml/aml.png#lightbox)

Tjänsten Azure Machine Learning stöder tekniker för öppen källkod, vilket betyder att du kan använda tiotusentals Python-paket med öppen källkod med maskininlärningskomponenter som TensorFlow och scikit-learn.
Stöd för avancerade verktyg såsom [Jupyter-notebooks](http://jupyter.org) eller tillägget [Azure Machine Learning for Visual Studio Code](https://visualstudio.microsoft.com/downloads/ai-tools-vscode/) gör det enkelt att interaktivt utforska data, transformera dem och sedan utveckla och testa modeller.
Tjänsten Azure Machine Learning innehåller även funktioner som [automatiserar modellgenereringen och modeljusteringen](tutorial-auto-train-models.md) så att du enkelt och effektivt kan skapa modeller med hög precision.

Med tjänsten Azure Machine Learning kan du börja träna på den lokala datorn och sedan skala ut till molnet. Tack vare de många tillgängliga [beräkningsmålen](how-to-set-up-training-targets.md), däribland Azure Machine Learning Compute och [Azure Databricks](/azure/azure-databricks/what-is-azure-databricks), och med [tjänster för finjustering av avancerade hyperparametrar](how-to-tune-hyperparameters.md) kan du skapa bättre modeller snabbare med hela styrkan från molnet.

När du har rätt modell kan du enkelt distribuera den i en container, till exempel Docker. Det innebär att det är enkelt att distribuera till [Azure Container Instances](how-to-deploy-to-aci.md) eller [Azure Kubernetes Service](how-to-deploy-to-aks.md). Du kan också använda containern i dina egna distributioner, antingen lokalt eller i molnet.
Du kan hantera de distribuerade modellerna och spåra flera körningar när du experimenterar för att hitta den bästa lösningen.
När modellen har distribuerats kan den returnera förutsägelser i [realtid](how-to-consume-web-service.md) eller [asynkront](how-to-run-batch-predictions.md) på stora mängder data.

Och med avancerade [maskininlärningspipelines](concept-ml-pipelines.md) kan du samarbeta på alla steg för förberedelse av data, modellträning och -utvärdering samt distribution.

## <a name="what-can-i-do-with-azure-machine-learning-service"></a>Vad kan jag göra med tjänsten Azure Machine Learning?

Azure Machine Learning-tjänsten kan träna och justera en modell automatiskt.
Ett exempel finns i [Tutorial: Automatically train a classification model with Azure Automated Machine Learning](tutorial-auto-train-models.md) (Självstudie: Träna automatiskt en klassificeringsmodell med Azure Automated Machine Learning).

Du kan använda Azure Machine Learning <a href="https://aka.ms/aml-sdk" target="_blank">SDK</a> för Python tillsammans med Python-paket med öppen källkod och skapa och själv träna mycket exakta maskininlärnings- och djupinlärningsmodeller på en arbetsyta i Azure Machine Learning-tjänsten.
Du kan välja bland många maskininlärningskomponenter som är tillgängliga i Python-paket med öppen källkod, till exempel följande:

- <a href="https://scikit-learn.org/stable/" target="_blank">Scikit-learn</a>
- <a href="https://www.tensorflow.org" target="_blank">Tensorflow</a>
- <a href="https://pytorch.org" target="_blank">PyTorch</a>
- <a href="https://www.microsoft.com/en-us/cognitive-toolkit/" target="_blank">CNTK</a>
- <a href="http://mxnet.io" target="_blank">MXNet</a>

När du har en modell kan du bara använda den för att skapa en container (till exempel Docker) som kan distribueras lokalt för testning. När testningen är klar kan modellen distribueras som en produktionswebbtjänst i antingen [Azure Container Instances](how-to-deploy-to-aci.md) eller [Azure Kubernetes Service](how-to-deploy-to-aks.md).

Du kan sedan hantera dina distribuerade modeller med hjälp av [Azure Machine Learning SDK för Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) eller [Azure-portalen](https://portal.azure.com/).
Du kan utvärdera modellmått, träna om och distribuera om nya versioner av modellen samtidigt som du spårar modellens experiment.

Kom igång med tjänsten Azure Machine Learning genom att gå till [Nästa steg](#next-steps) nedan.

## <a name="how-is-azure-machine-learning-service-different-from-studio"></a>Hur skiljer sig tjänsten Azure Machine Learning från Studio?

Azure Machine Learning Studio är en gemensam, visuell ”dra och släpp”-arbetsyta där du kan skapa, testa och distribuera maskininlärningslösningar utan att behöva skriva kod. Den använder fördefinierade och förkonfigurerade maskininlärningsalgoritmer och datahanteringsmoduler.

Använd Machine Learning Studio om du snabbt och enkelt vill experimentera med maskininlärningsmodeller och om de inbyggda maskininlärningsalgoritmerna räcker för dina lösningar.

Använd Machine Learning-tjänsten om du arbetar i en Python-miljö och vill ha mer kontroll över maskininlärningsalgoritmerna eller om du vill använda maskininlärningsbibliotek med öppen källkod.

> [!NOTE]
> Modeller som skapas i Azure Machine Learning Studio kan inte distribueras eller hanteras av Azure Machine Learning-tjänsten.

## <a name="free-trial"></a>Kostnadsfri utvärderingsversion
Om du inte är prenumerant kan du [öppna ett Azure-konto utan kostnad](https://aka.ms/amlfree). Du får krediter som du kan använda för att köpa Azure-tjänster. När de är slut kan du behålla kontot och använda [kostnadsfria Azure-tjänster](https://azure.microsoft.com/free/). Ditt kreditkort debiteras aldrig om du inte specifikt ändrar dina inställningar och ber om debitering. Du kan även [aktivera MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F), vilket ger dig krediter varje månad som du kan använda för Azure-betaltjänster.

## <a name="next-steps"></a>Nästa steg

- Skapa en arbetsyta för maskininlärningstjänsten för att komma igång [med hjälp av Azure-portalen](quickstart-get-started.md) eller [i Python](quickstart-create-workspace-with-python.md).

- Följ den fullständiga självstudien, [Träna och distribuera en bildklassificeringsmodell med Azure Machine Learning](tutorial-train-models-with-aml.md).

- [Använd Azure Machine Learning för att automatiskt skapa och justera en modell](tutorial-auto-train-models.md).

- Lär dig mer om [pipelines för maskininlärning](/azure/machine-learning/service/concept-ml-pipelines) för att skapa, optimera och hantera dina maskininlärningsscenarier.

- Läs den ingående artikeln om [arkitekturen och begrepp för Azure Machine Learning-tjänsten](concept-azure-machine-learning-architecture.md).

- Mer information om andra maskininlärningsprodukter från Microsoft finns i artikeln om [andra maskininlärningsprodukter från Microsoft](./overview-more-machine-learning.md).


<!-- 

An intro to AML or an end-to-end quickstart video could go here.

In this 9-minute video, learn how you can benefit your app. You'll learn about key features and what a typical workflow looks like. 

>[!VIDEO https://channel9.msdn.com/Events/Connect/2016/138/player]
 
+ 0-3 minutes covers key features and use-cases.
+ 3-4 minutes covers service provisioning. 
+ 4-6 minutes covers Import Data wizard used to create an index using the built-in real estate dataset.

-->
