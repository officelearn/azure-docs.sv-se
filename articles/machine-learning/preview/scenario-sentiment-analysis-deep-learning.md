---
title: Sentiment analys med djup Learning med Azure Machine Learning | Microsoft Docs
description: "Så här utför sentiment analys djup learning med Azure ML-arbetsstationen."
services: machine-learning
documentationcenter: 
author: miprasad
manager: kristin.tolle
editor: miprasad
ms.assetid: 
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/16/2017
ms.author: miprasad
ms.openlocfilehash: 255c39ea21378aae23fc61da6dc882138fb66ab1
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2017
---
# <a name="sentiment-analysis-using-deep-learning-with-azure-machine-learning"></a>Sentiment analys med djup Learning med Azure Machine Learning

Sentiment analys är en välkänd uppgift i sfären av naturligt språk bearbetning. Med en uppsättning texter är syftet att avgöra sentiment av texten. Syftet med den här lösningen är att använda CNTK som serverdelen för Keras (en modell nivån bibliotek, tillhandahåller övergripande byggblock för att utveckla djup learning-modeller) och implementera sentiment analys från film granskningar.

Lösningen finns på https://github.com/Azure/MachineLearningSamples-SentimentAnalysis

## <a name="link-to-the-gallery-github-repository"></a>Länka till galleriet GitHub-lagringsplatsen

Följ länken till offentliga GitHub-lagringsplatsen:

[https://github.com/Azure/MachineLearningSamples-SentimentAnalysis](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis)

## <a name="use-case-overview"></a>Använd case-översikt

Nedbrytning av data och den ökande mängden av mobila enheter har skapat många möjligheter för kunder att uttrycka sina känslor och attityder om något och allt när som helst. Den här åsikt eller ”sentiment” skapas ofta via sociala kanaler i form av granskningar, Chat, delar, gillar tweets osv. Sentiment kan vara ovärderlig för företag som vill förbättra produkter och tjänster, fatta välgrundade beslut och bättre befordra sina varumärken.

Om du vill hämta värdet från sentiment analys har företag möjlighet att min stora datalager Ostrukturerade sociala för tillämplig insikter. I det här exemplet ska vi utvecklar djup learning-modeller för att utföra sentiment analys av film granskningar med AMLWorkbench

## <a name="prerequisites"></a>Förutsättningar

* En [Azure-konto](https://azure.microsoft.com/free/) (gratisutvärderingar finns).

* En installerad kopia av [Azure Machine Learning arbetsstationen](./overview-what-is-azure-ml.md) följande den [installation snabbstartsguiden](./quickstart-installation.md) att installera programmet och skapa en arbetsyta.

* Det är bäst för operationalization, om du har installerat och körs lokalt Docker-motorn. Om inte, kan du använda alternativet klustret. Kör ett Azure Container Service (ACS) kan dock vara dyra.

* Denna lösning förutsätter att du använder Azure Machine Learning-arbetsstationen på Windows 10 med Docker-motorn har installerats lokalt. På en macOS är instruktionen i stort sett densamma.

## <a name="data-description"></a>Beskrivning av data

Dataset som används för det här exemplet är en liten datamängd hand utformad och finns i den [datamappen](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis/tree/master/data).

Den första kolumnen innehåller film omdömen och den andra kolumnen innehåller sina sentiment (0 - negativa och 1 - positiv). Dataset används endast i demonstrationssyfte men vanligtvis robust sentiment resultat får du en stor datauppsättning. Till exempel den [IMDB film granskar sentiment klassificeringsproblem](https://keras.io/datasets/#datasets ) från Keras består av en datamängd 25 000 filmer igenom från IMDB, heter som sentiment (positivt eller negativt). Syftet med den här övningen är att visa dig hur du utför sentiment analys med AMLWorkbench djup learning.

## <a name="scenario-structure"></a>Scenario-struktur

Mappstrukturen är ordnade enligt följande:

1. All kod som rör sentiment analys med AMLWorkbench finns i rotmappen
2. data: innehåller den datamängd som används i lösningen
3. dokument: innehåller de praktiska övningarna

Ordningen praktiska övningar för att utföra lösningen är följande:

| Ordning| Filnamn | Relaterade filer |
|--|-----------|------|
| 1 | [`SentimentAnalysisDataPreparation.md`](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis/blob/master/docs/SentimentAnalysisDataPreparation.md) | 'data/sampleReviews.txt' |
| 2 | [`SentimentAnalysisModelingKeras.md`](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis/blob/master/docs/SentimentAnalysisModelingKeras.md) | 'SentimentExtraction.py' |
| 4 | [`SentimentAnalysisOperationalization.md`](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis/blob/master/docs/SentimentAnalysisOperationalization.md) | 'Operaionalization' |

## <a name="conclusion"></a>Sammanfattning

Sammanfattningsvis den här lösningen ger en introduktion till djupgående utbildning för att utföra analyser av sentiment med Azure Machine Learning-arbetsstationen. Vi operationalisera HDF5 modeller.
