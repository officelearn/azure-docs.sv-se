---
title: Twitter-sentimentklassificering med Azure Machine Learning (AML)-paketet för textanalys (AMLPTA) och Team Data Science Process (TDSP) | Microsoft Docs
description: Beskriver användning av TDSP (Team Data Science Process) och AMLPTA för sentimentklassificering
services: machine-learning, team-data-science-process
documentationcenter: ''
author: deguhath
manager: deguhath
editor: cgronlun
ms.assetid: b8fbef77-3e80-4911-8e84-23dbf42c9bee
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: deguhath
ms.openlocfilehash: 9e5018bc4c7b90897f7f8c91169410284217b172
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/14/2018
ms.locfileid: "45577022"
---
# <a name="twitter-sentiment-classification-with-azure-machine-learning-aml-package-for-text-analytics-amlpta-and-team-data-science-process-tdsp"></a>Twitter-sentimentklassificering med Azure Machine Learning (AML)-paketet för textanalys (AMLPTA) och Team Data Science Process (TDSP)

## <a name="introduction"></a>Introduktion
Standardisering av strukturen och dokumentation om data science projekt som är förankrad vid en etablerad [livscykeln för datavetenskap](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md), är nyckeln till att underlätta effektivt samarbete i data science teams.

Vi hade tidigare gavs ut en [GitHub-lagringsplatsen för TDSP projektstruktur och mallar](https://github.com/Azure/Azure-TDSP-ProjectTemplate). Nu har vi aktiverat skapandet av Azure Machine Learning-projekt som instansieras med [TDSP struktur och dokumentation mallar för Azure Machine Learning](https://github.com/amlsamples/tdsp). Anvisningar om hur du använder TDSP struktur och mallar i Azure Machine Learning tillhandahålls [här](https://docs.microsoft.com/azure/machine-learning/preview/how-to-use-tdsp-in-azure-ml). 

I det här exemplet ska vi att demonstrera hur Azure Machine Learning-paketet för textanalys och TDSP att utveckla och distribuera förutsägande modeller för Twitter sentimentklassificering.


## <a name="use-case"></a>Användningsfall
### <a name="twitter-sentiment-polarity-sample"></a>Twitter-sentiment polaritet exemplet
Den här artikeln används ett exempel på hur du kan skapa en instans av och köra en Machine Learning-projekt. Exemplet använder TDSP struktur och mallar i Azure Machine Learning Workbench. Hela exemplet finns i den här genomgången. Aktiviteten modellering förutsäger sentiment polaritet (positivt eller negativt) med hjälp av texten från tweets. Den här artikeln beskrivs de datamodellering uppgifter som beskrivs i den här genomgången. Den här genomgången beskriver följande uppgifter:

1. Datagranskning, utbildning och distribution av en maskininlärningsmodell som åtgärdar förutsägelse problemet som beskrivs i case-översikt för användning. Twitter-sentiment data används för dessa uppgifter.
2. Körning av projekt med TDSP-mall från Azure Machine Learning för det här projektet. TDSP-livscykeln används för projektkörning och rapportering.
3. Driftsättning av lösningen direkt från Azure Machine Learning i Azure Container Service.

Projektet visar användningen av Text Analytics-paketet för Azure Machine Learning.


## <a name="link-to-github-repository"></a>Länka till GitHub-lagringsplats
Länk till GitHub-lagringsplatsen är [här](https://github.com/Azure/MachineLearningSamples-AMLTextPackage-TwitterSentimentPrediction). 

### <a name="purpose"></a>Syfte
Det huvudsakliga syftet med det här exemplet är att visa hur du skapa en instans av och kör en machine learning-projekt med hjälp av Team Data Science Process (TDSP) struktur och mallar i Azure Machine Learning Arbetsbänk. För detta ändamål använder vi [Twitter-Sentiment data](http://cs.stanford.edu/people/alecmgo/trainingandtestdata.zip). Modellering-uppgift är att förutsäga sentiment polaritet (positivt eller negativt) med hjälp av texten från tweets.

### <a name="scope"></a>Omfång
- Datagranskning, utbildning och distribution av en maskininlärningsmodell som göra förutsägelser problemet som beskrivs i det fallet översikt över.
- Körning av projekt i Azure Machine Learning med hjälp av Team Data Science Process TDSP ()-mall från Azure Machine Learning för det här projektet. För projektkörning och rapportering ska vi använda TDSP-livscykeln.
- Driftsättning av lösningen direkt från Azure Machine Learning i Azure Container Services.

Projektet visar flera funktioner i Azure Machine Learning, sådana TDSP struktur instansiering och användning, körning av kod i Azure Machine Learning Arbetsbänk och enkelt driftsättning i Azure Container Services med Docker och Kubernetes.

## <a name="team-data-science-process-tds"></a>Team Data Science Process (TDS)
Vi använder de TDSP projektmallarna struktur och dokumentation för att köra det här exemplet. Det följer den [TDSP livscykel](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/lifecycle). Projektet skapas baserat på instruktioner [här](https://github.com/amlsamples/tdsp/blob/master/docs/how-to-use-tdsp-in-azure-ml.md).


<img src="./media/predict-twitter-sentiment-amltextpackage/tdsp-lifecycle2.png" alt="tdsp-lifecycle" width="800" height="600">


## <a name="use-case-overview"></a>Översikt över användningsfall
Uppgiften är att förutsäga varje twitter sentiment binära polaritet med word inbäddningar-funktioner som extraheras från twitter text. Detaljerad beskrivning finns i den här [databasen](https://github.com/Azure/MachineLearningSamples-AMLTextPackage-TwitterSentimentPrediction).

### <a name="data-acquisition-and-understandinghttpsgithubcomazuremachinelearningsamples-amltextpackage-twittersentimentpredictiontreemastercode01dataacquisitionandunderstanding"></a>[Data förvärv och förståelse av](https://github.com/Azure/MachineLearningSamples-AMLTextPackage-TwitterSentimentPrediction/tree/master/code/01_data_acquisition_and_understanding)
Det första steget i det här exemplet är att hämta sentiment140 datauppsättningen och delas in i träna och testa datauppsättningar. Sentiment140 datauppsättningen innehåller det faktiska innehållet till tweeten (med uttryckssymboler tas bort) tillsammans med polaritet för var och en tweetens (negativ = 0, positivt = 4), med neutral tweets som tagits bort. Den resulterande träningsdata har 1.3 miljoner rader och testa data har 320 k rader.

### <a name="modelinghttpsgithubcomazuremachinelearningsamples-amltextpackage-twittersentimentpredictiontreemastercode02modeling"></a>[Modellering](https://github.com/Azure/MachineLearningSamples-AMLTextPackage-TwitterSentimentPrediction/tree/master/code/02_modeling)
Den här delen av exemplet är ytterligare indelade i tre kapitlen: 
- **Funktionstekniker** motsvarar generering av funktioner som använder olika word inbäddning-algoritmen, nämligen Word2Vec. 
- **Modellera skapa** behandlar utbildningen av olika modeller som _logistic regression_ och _gradient boosting_ att förutsäga känslan i indatatexten. 
- **Modellera utvärdering** används den tränade modellen över testdata.

#### <a name="feature-engineering"></a>Funktionstekniker
Vi använder <b>Word2Vec</b> att generera ordinbäddningar. Först vi använder algoritmen Word2Vec i Skipgram-läge som beskrivs i dokumentet [Mikolov Tomas, m.fl. Distribuerade framställningar av ord och fraser och deras compositionality. Utvecklingen av neurala information bearbetningssystem. 2013.](https://arxiv.org/abs/1310.4546) att generera ordinbäddningar.

Hoppa över-gram är en ytlig neuralt nätverk som tar target ordet kodas som en en frekvent vector som indata och använder det för att förutsäga Närliggande ord. Om V är storleken på vokabulär och storleken på utdata-lagret är __C * V__ där C är storleken på fönstret kontext. Hoppa över-gram-baserad arkitektur visas i följande bild.
 
<table class="image" align="center">
<caption align="bottom">Hoppa över-gram-modell</caption>
<tr><td><img src="https://s3-ap-south-1.amazonaws.com/av-blog-media/wp-content/uploads/2017/06/05000515/Capture2-276x300.png" alt="Skip-gram model"/></td></tr>
</table>

Information om modellen Word2Vec algoritmen och hoppa över gram är utanför omfattningen för det här exemplet och berörda läsare begärs gå igenom följande länkar för mer information. Den kod 02_A_Word2Vec.py som refererar till [tensorflow-exempel](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/examples/tutorials/word2vec/word2vec_basic.py)

* [Vektor Representation av ord](https://www.tensorflow.org/tutorials/word2vec)
* [Hur fungerar word2vec?](http://www.1-4-5.net/~dmm/ml/how_does_word2vec_work.pdf)
* [Anmärkningar om bruset Contrastive uppskattning och negativa Sampling](http://demo.clab.cs.cmu.edu/cdyer/nce_notes.pdf)

När utbildning processen är klar, genereras två bädda in filer i formatet för TSV för modellering scenen.

#### <a name="model-training"></a>Modellträning
När word vektorer har genererats med någon av algoritmen SSWE eller Word2vec, är nästa steg att skapa klassificering modeller att förutsäga faktiska sentiment polaritet. Vi använder två typer av funktioner: Word2Vec och SSWE i två modeller: Logistic regression och Convolutional Neurala nätverk (CNN). 

#### <a name="model-evaluation"></a>Modell-utvärdering
Vi tillhandahåller kod att läsa in och utvärdera flera tränade modeller på test-datauppsättning.


### <a name="deploymenthttpsgithubcomazuremachinelearningsamples-amltextpackage-twittersentimentpredictiontreemastercode03deployment"></a>[Distribution](https://github.com/Azure/MachineLearningSamples-AMLTextPackage-TwitterSentimentPrediction/tree/master/code/03_deployment)
Den här delen som vi tillhandahåller länkar till anvisningar om hur du operationalisera en modell för förutsägelse av förtränade sentiment till en webbtjänst på ett kluster i Azure Container Service (AKS). Driftsättning miljön etablerar Docker och Kubernetes i klustret för att hantera distributionen av webbtjänsten. Du hittar mer information om processen för driftsättning [här](https://docs.microsoft.com/azure/machine-learning/preview/model-management-service-deploy).

## <a name="conclusion"></a>Sammanfattning
Vi har gått igenom informationen om hur du tränar en bädda in word-modell med hjälp av Word2Vec och använda extraherade inbäddningar som funktioner för att träna två olika modeller för att förutsäga sentimentresultat på Twitter textdata. En av dessa modeller distribueras i Azure Container Services (AKS). 

## <a name="next-steps"></a>Nästa steg
Läs dokumentationen på [Azure Machine Learning-paket för Text Analytics (AMLPTA)](https://docs.microsoft.com/python/api/overview/azure-machine-learning/textanalytics?view=azure-ml-py-latest) och [Team Data Science Process (TDSP)](https://aka.ms/tdsp) att komma igång.

## <a name="references"></a>Referenser
* [TDSP](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview) 
* [Hur du använder Team Data Science Process (TDSP) i Azure Machine Learning](https://aka.ms/how-to-use-tdsp-in-aml)
* [TDSP projektmall för Azure Machine Learning](https://aka.ms/tdspamlgithubrepo)
* [Azure ML Arbetsbänk](https://docs.microsoft.com/azure/machine-learning/preview/)
* [Mikolov Tomas, m.fl. Distribuerade framställningar av ord och fraser och deras compositionality. Utvecklingen av neurala information bearbetningssystem. 2013.](https://arxiv.org/abs/1310.4546)
