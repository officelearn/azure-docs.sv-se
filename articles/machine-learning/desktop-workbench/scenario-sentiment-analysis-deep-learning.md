---
title: Attitydanalys med Djupinlärning med Azure Machine Learning | Microsoft Docs
description: Så här utför attitydanalys med djupinlärning med Azure ML Workbench.
services: machine-learning
documentationcenter: ''
author: miprasad
manager: kristin.tolle
editor: miprasad
ms.assetid: ''
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/20/2018
ms.author: miprasad
ROBOTS: NOINDEX
ms.openlocfilehash: e2fbb0b7b0dede198be0e57ffcd2b58a7da7fce7
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46947779"
---
# <a name="sentiment-analysis-using-deep-learning-with-azure-machine-learning"></a>Attitydanalys med Djupinlärning med Azure Machine Learning

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 



Attitydanalys är en välkänd aktivitet inom ramen för bearbetning av naturligt språk. Med en uppsättning texter är syftet att avgöra känslan i texten. Målet med den här lösningen är att använda Djupinlärning för att förutsäga sentiment från filmrecensioner.

Lösningen finns i https://github.com/Azure/MachineLearningSamples-SentimentAnalysis

## <a name="link-to-the-gallery-github-repository"></a>Länka till galleriet GitHub-lagringsplatsen

Följ den här länken till den offentliga GitHub-lagringsplatsen:

[https://github.com/Azure/MachineLearningSamples-SentimentAnalysis](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis)

## <a name="use-case-overview"></a>Översikt över användningsfall

Nedbrytning av data och den ökande mängden av mobila enheter har skapat möjligheter för kunder att deras känslor och attityder om vad som helst och allt när som helst. Den här åsikt eller ”attitydanalys” skapas ofta via sociala kanaler i form av granskningar, Chat, delar, gilla, tweets och så vidare. Attityden kan vara ovärderliga för företag som vill förbättra produkter och tjänster, fatta mer välgrundade beslut och bättre uppgradera sina varumärken.

Om du vill få ut värde av attitydanalys måste företag ha möjlighet att utvinna enorma lager av Ostrukturerade sociala data för användbara insikter. I det här exemplet utveckla vi modeller för djup maskininlärning för att utföra känsloanalys av filmrecensioner med AMLWorkbench

## <a name="prerequisites"></a>Förutsättningar

* En [Azure-konto](https://azure.microsoft.com/free/) (kostnadsfria utvärderingsversioner är tillgängliga).

* En installerad kopia av [Azure Machine Learning Workbench](../service/overview-what-is-azure-ml.md) följande den [installation snabbstartsguiden](quickstart-installation.md) att installera programmet och skapa en arbetsyta.

* Det är bäst för driftsättning, om du har Docker-motorn installerad och köras lokalt. Om inte, du kan använda alternativet för klustret. Kör ett Azure Container Service (ACS) kan dock vara dyra.

* Den här lösningen förutsätter att du använder Azure Machine Learning Workbench på Windows 10 med Docker-motorn installerad lokalt. På en macOS är instruktionen i stort sett densamma.

## <a name="data-description"></a>Databeskrivning

Den datauppsättning som används för det här exemplet är en liten datamängd hand särskild och finns i den [datamapp](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis/tree/master/data).

Den första kolumnen innehåller filmrecensioner och den andra kolumnen innehåller sina åsikter (0 – negativt och 1 – positiv). Datauppsättningen används endast i demonstrationssyfte men vanligtvis robust sentimentpoäng får du behöver en stor datauppsättning. Till exempel den [IMDB film granskar sentiment klassificeringsproblem](https://keras.io/datasets/#datasets ) från Keras består av en datauppsättning över 25 000 filmer granskningar från IMDB, märkts av sentiment (positivt eller negativt). Avsikten med den här övningen är att visa dig hur du utför attitydanalys med djupinlärning med AMLWorkbench.

## <a name="scenario-structure"></a>Scenario-struktur

Mappstrukturen är ordnade enligt följande:

1. All kod som rör attitydanalys med AMLWorkbench finns i rotmappen
2. data: innehåller den datauppsättning som används i lösningen
3. docs: innehåller alla praktiska labbövningar

Ordningen praktiska övningar för att utföra lösningen är följande:

| Beställa| Filnamn | Relaterade filer |
|--|-----------|------|
| 1 | [`SentimentAnalysisDataPreparation.md`](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis/blob/master/docs/SentimentAnalysisDataPreparation.md) | 'data/sampleReviews.txt' |
| 2 | [`SentimentAnalysisModelingKeras.md`](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis/blob/master/docs/SentimentAnalysisModelingKeras.md) | 'SentimentExtraction.py' |
| 3 | [`SentimentAnalysisOperationalization.md`](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis/blob/master/docs/SentimentAnalysisOperationalization.md) | 'Operaionalization' |

## <a name="conclusion"></a>Sammanfattning

Sammanfattningsvis, den här lösningen ger en introduktion till Deep Learning för att utföra attitydanalys med Azure Machine Learning Workbench. Vi även utföra åtgärder med HDF5 modeller.
