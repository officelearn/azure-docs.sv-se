---
title: Vad är tjänsten Azure Machine Learning?
description: Förklarar grundläggande begrepp relaterade till maskininlärning i molnet och vad du kan använda det till, samt definierar viktiga termer inom maskininlärning. Översikt över Azure Machine Learning – en integrerad lösning från slutpunkt till slutpunkt som gör att dataforskare kan utveckla, experimentera och distribuera avancerade analysprogram i molnskala.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: overview
ms.reviewer: jmartens
author: garyericson
ms.author: garye
ms.date: 09/24/2018
ms.openlocfilehash: 660e5515b765e133d5bc9309be5439209b957353
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/03/2018
ms.locfileid: "48238534"
---
# <a name="what-is-azure-machine-learning-service-preview"></a>Vad är tjänsten Azure Machine Learning (förhandsversion)?

Tjänsten Azure Machine Learning (förhandsversion) är en molnbaserad tjänst som du kan använda för att utveckla och distribuera maskininlärningsmodeller. Med tjänsten Azure Machine Learning kan du spåra dina modeller när du skapar, tränar, distribuerar och hanterar dem, allt i den stora skala som molnet erbjuder.

## <a name="what-is-machine-learning"></a>Vad är maskininlärning?

Maskininlärning är en datavetenskapsteknik som gör att datorer kan använda befintliga data för att göra prognoser om framtida beteenden, resultat och trender. Med maskininlärning kan datorer lära sig utan att vara explicit programmerade.

Prognoser eller förutsägelser från maskininlärning kan göra appar och enheter smartare. När du handlar på nätet kan maskininlärning till exempel användas för att rekommendera andra produkter som du kanske gillar baserat på vad du har köpt. När ditt kreditkort dras används maskininlärning för att jämföra transaktionen med en transaktionsdatabas, vilket gör det lättare att upptäcka bedrägerier. När din robotdammsugare dammsuger ett rum tar den hjälp av maskininlärning för att avgöra om jobbet är klart.

## <a name="what-is-azure-machine-learning-service"></a>Vad är tjänsten Azure Machine Learning?

Tjänsten Azure Machine Learning tillhandahåller en molnbaserad miljö som du kan använda för att utveckla, träna, testa, distribuera, hantera och spåra maskininlärningsmodeller.

[ ![Arbetsflöde i tjänsten Azure Machine Learning](./media/overview-what-is-azure-ml/aml.png) ] (./media/overview-what-is-azure-ml/aml.png#lightbox)

Tjänsten Azure Machine Learning stöder tekniker för öppen källkod, vilket betyder att du kan använda tiotusentals Python-paket med öppen källkod med maskininlärningskomponenter som TensorFlow och scikit-learn.
Avancerade verktyg som [Jupyter Notebook](http://jupyter.org) eller [Visual Studio Code Tools for AI](https://visualstudio.microsoft.com/downloads/ai-tools-vscode/) gör det enkelt att interaktivt utforska data, transformera dem och sedan utveckla och testa modeller.
Tjänsten Azure Machine Learning innehåller även funktioner som [automatiserar modellgenereringen och modeljusteringen](tutorial-auto-train-models.md) så att du enkelt och effektivt kan skapa modeller med hög precision.

Med tjänsten Azure Machine Learning kan du börja träna på den lokala datorn och sedan skala ut till molnet. Med inbyggt stöd för [Azure Batch AI](https://azure.microsoft.com/services/batch-ai/) och med [avancerade tjänster för hyperparameterjustering](how-to-tune-hyperparameters.md) kan du skapa bättre modeller snabbare, med hela kraften i molnet. 

När du har rätt modell kan du enkelt distribuera den i en container, till exempel Docker. Det innebär att det är enkelt att distribuera till [Azure Container Instances](how-to-deploy-to-aci.md) eller [Azure Kubernetes Service](how-to-deploy-to-aks.md). Du kan också använda containern i dina egna distributioner, antingen lokalt eller i molnet.
Du kan hantera de distribuerade modellerna och spåra flera körningar när du experimenterar för att hitta den bästa lösningen.

[!INCLUDE [aml-preview-note](../../../includes/aml-preview-note.md)]

## <a name="what-can-i-do-with-azure-machine-learning-service"></a>Vad kan jag göra med tjänsten Azure Machine Learning?

Azure Machine Learning-tjänsten kan skapa en modell automatiskt och automatiskt justera den åt dig.
Ett exempel finns i [Tutorial: Automatically train a classification model with Azure Automated Machine Learning](tutorial-auto-train-models.md) (Självstudie: Träna automatiskt en klassificeringsmodell med Azure Automated Machine Learning).

Eller så kan du använda Azure Machine Learning <a href="http://aka.ms/aml-sdk" target="_blank">SDK</a> för Python tillsammans med Python-paket med öppen källkod och skapa och själv träna mycket exakta maskininlärnings- och djupinlärningsmodeller på en Azure Machine Learning-tjänstens arbetsyta.
Du kan välja bland många maskininlärningskomponenter som är tillgängliga i Python-paket med öppen källkod, till exempel följande:

- <a href="http://scikit-learn.org/stable/" target="_blank">Scikit-learn</a>
- <a href="https://www.tensorflow.org" target="_blank">Tensorflow</a>
- <a href="https://pytorch.org" target="_blank">PyTorch</a>
- <a href="https://www.microsoft.com/cognitive-toolkit/" target="_blank">CNTK</a>
- <a href="http://mxnet.io" target="_blank">MXNet</a>

När du har en modell kan du använda den för att skapa en container (till exempel Docker) som kan distribueras lokalt för testning och sedan som en produktionswebbtjänst i antingen [Azure Container Instances](how-to-deploy-to-aci.md) eller [Azure Kubernetes Service](how-to-deploy-to-aks.md).

Du kan sedan hantera dina distribuerade modeller med hjälp av [Azure-portalen](https://portal.azure.com/) eller [Azure Machine Learning CLI-tillägget](https://review.docs.microsoft.com/azure/machine-learning/service/reference-azure-machine-learning-cli).
Du kan utvärdera modellmått, träna om och distribuera om nya versioner av modellen, samtidigt som du spårar modellens experiment.

Kom igång med tjänsten Azure Machine Learning genom att gå till [Nästa steg](#next-steps) nedan.

## <a name="how-is-azure-machine-learning-service-different-from-studio"></a>Hur skiljer sig tjänsten Azure Machine Learning från Studio?

Azure Machine Learning Studio är en gemensam, visuell ”dra och släpp”-arbetsyta där du kan skapa, testa och distribuera maskininlärningslösningar utan att behöva skriva kod. Den använder fördefinierade och förkonfigurerade maskininlärningsalgoritmer och datahanteringsmoduler.

Använd Machine Learning Studio om du snabbt och enkelt vill experimentera med maskininlärningsmodeller och om de inbyggda maskininlärningsalgoritmerna räcker för dina lösningar.

Använd Machine Learning-tjänsten om du arbetar i en Python-miljö och vill ha mer kontroll över maskininlärningsalgoritmerna eller om du vill använda maskininlärningsbibliotek med öppen källkod.

> [!NOTE]
> Modeller som skapas i Azure Machine Learning Studio kan inte distribueras eller hanteras av Azure Machine Learning-tjänsten.

## <a name="free-trial"></a>Kostnadsfri utvärderingsversion
Om du inte är prenumerant kan du [öppna ett Azure-konto utan kostnad](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F). Du får krediter som du kan använda för att köpa Azure-tjänster. När de är slut kan du behålla kontot och använda [kostnadsfria Azure-tjänster](https://azure.microsoft.com/free/). Ditt kreditkort debiteras aldrig om du inte specifikt ändrar dina inställningar och ber om debitering. Du kan också [aktivera MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F): din MSDN-prenumeration ger dig krediter varje månad som kan användas för Azure-betaltjänster.

## <a name="next-steps"></a>Nästa steg

- Skapa en arbetsyta för maskininlärningstjänsten genom att följa anvisningarna i artikeln [Använda Azure-portalen för att komma igång](quickstart-get-started.md)
 
- Gå den heltäckande kursen [Train an image classification model with Azure Machine Learning](tutorial-train-models-with-aml.md) (Träna en bildklassificeringsmodell med Azure Machine Learning) om du vill lära dig hur du tränar och distribuerar modeller med Azure Machine Learning-tjänsten

- Information om hur du tillåter att Azure Machine Learning genererar och justerar din modell automatiskt finns i [Tutorial: Automatically train a classification model with Azure Automated Machine Learning](tutorial-auto-train-models.md) (Självstudie: Träna en klassificeringsmodell automatiskt med Azure Automated Machine Learning)

- En teknisk, detaljerad genomgång av tjänsten finns i [Azure Machine Learning service architecture and concepts](concept-azure-machine-learning-architecture.md) (Tjänsten Azure Machine Learning – arkitektur och begrepp)

- Mer information om andra maskininlärningsprodukter från Microsoft finns i [Other machine learning products from Microsoft](./overview-more-machine-learning.md) (Andra maskininlärningsprodukter från Microsoft)


<!-- 

An intro to AML or an end-to-end quickstart video could go here.

In this 9-minute video, learn how you can benefit your app. You'll learn about key features and what a typical workflow looks like. 

>[!VIDEO https://channel9.msdn.com/Events/Connect/2016/138/player]
 
+ 0-3 minutes covers key features and use-cases.
+ 3-4 minutes covers service provisioning. 
+ 4-6 minutes covers Import Data wizard used to create an index using the built-in real estate dataset.

-->
