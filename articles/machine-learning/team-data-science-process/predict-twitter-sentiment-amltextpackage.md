---
title: Twitter-sentiment klassificering med Azure Machine Learning (AML)-paketet för textanalys (AMLPTA) och Team Data vetenskap processen (TDSP) | Microsoft Docs
description: Beskriver användningen av TDSP (Team vetenskap av data) och AMLPTA för sentiment klassificering
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
ms.openlocfilehash: 559af47bcf41cd6af59f8ba1b27ff8e64e849925
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2018
ms.locfileid: "36296171"
---
# <a name="twitter-sentiment-classification-with-azure-machine-learning-aml-package-for-text-analytics-amlpta-and-team-data-science-process-tdsp"></a>Twitter-sentiment klassificering med Azure Machine Learning (AML)-paketet för textanalys (AMLPTA) och Team Data vetenskap processen (TDSP)

## <a name="introduction"></a>Introduktion
Standardisering av strukturen och dokumentation av datavetenskap projekt som är förankrad vid en upprättad [datavetenskap livscykel](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md), för att underlätta effektivt samarbete i datavetenskap team.

Vi hade tidigare ut en [GitHub-lagringsplatsen för TDSP projektstruktur och mallar](https://github.com/Azure/Azure-TDSP-ProjectTemplate). Vi har nu aktiverats skapandet av Azure Machine Learning-projekt som instansieras med [TDSP struktur och dokumentation mallar för Azure Machine Learning](https://github.com/amlsamples/tdsp). Anvisningar om hur du använder TDSP struktur och mallar i Azure Machine Learning tillhandahålls [här](https://docs.microsoft.com/en-us/azure/machine-learning/preview/how-to-use-tdsp-in-azure-ml). 

I det här exemplet ska vi visa hur Azure Machine Learning-paketet för Text Analytics och TDSP att utveckla och distribuera förutsägelsemodeller för Twitter-sentiment klassificering.


## <a name="use-case"></a>Användningsfall
### <a name="twitter-sentiment-polarity-sample"></a>Twitter-sentiment polaritet exempel
Den här artikeln används ett exempel på hur du kan skapa och köra ett Machine Learning-projekt. Används av TDSP struktur och mallar i Azure Machine Learning-arbetsstationen. Hela exemplet tillhandahålls i den här genomgången. Aktiviteten modellering beräknar sentiment polaritet (positiv eller negativ) med hjälp av texten från tweets. Den här artikeln beskrivs data modellering uppgifter som beskrivs i den här genomgången. Den här genomgången innehåller följande uppgifter:

1. Datagranskning, utbildning och distribution av machine learning-modell som gäller förutsägelse problemet som beskrivs i case översikt för användning. Twitter-sentiment data används för dessa uppgifter.
2. Körningen av projektet med hjälp av Azure Machine Learning TDSP mallen för det här projektet. TDSP livscykel används för körning av projektet och rapportering.
3. Operationalization lösning direkt från Azure Machine Learning i Azure Container Service.

Projektet visar användningen av Text Analytics paketet för Azure Machine Learning.


## <a name="link-to-github-repository"></a>Länka till GitHub-lagringsplatsen
Länk till GitHub-lagringsplatsen är [här](https://github.com/Azure/MachineLearningSamples-AMLTextPackage-TwitterSentimentPrediction). 

### <a name="purpose"></a>Syfte
Det huvudsakliga syftet med det här exemplet är att visa hur du initiera och kör machine learning-projekt med Team Data vetenskap processen (TDSP) struktur och mallar i Azure Machine Learning Arbetsbänk. För detta ändamål som vi använder [Twitter-Sentiment data](http://cs.stanford.edu/people/alecmgo/trainingandtestdata.zip). Aktiviteten modellering är att förutsäga sentiment polaritet (positiv eller negativ) med hjälp av texten från tweets.

### <a name="scope"></a>Omfång
- Datagranskning, utbildning och distribution av en maskininlärningsmodell som göra förutsägelser problemet som beskrivs i fallet översikt för användning.
- Körningen av projektet i Azure Machine Learning med mallen Team Data vetenskap processen (TDSP) från Azure Machine Learning för det här projektet. För körning av projektet och rapportering, är det dags att använda TDSP livscykel.
- Operationalization lösning direkt från Azure Machine Learning i Azure Container Services.

Projektet visar flera funktioner i Azure Machine Learning sådana TDSP struktur instansiering och Använd, körning av kod i Azure Machine Learning Arbetsbänk och enkelt operationalization i Azure Container Services med Docker och Kubernetes.

## <a name="team-data-science-process-tds"></a>Team datavetenskap processen (TDS)
Vi använder de TDSP projektmallarna struktur och dokumentation för att köra det här exemplet. Följer den [TDSP livscykel](https://docs.microsoft.com/en-us/azure/machine-learning/team-data-science-process/lifecycle). Projektet har skapats utifrån anvisningarna [här](https://github.com/amlsamples/tdsp/blob/master/docs/how-to-use-tdsp-in-azure-ml.md).


<img src="./media/predict-twitter-sentiment-amltextpackage/tdsp-lifecycle2.png" alt="tdsp-lifecycle" width="800" height="600">


## <a name="use-case-overview"></a>Använd case-översikt
Aktiviteten är att förutsäga varje twitter sentiment binära polaritet använder word inbäddningar funktioner som extraheras från twitter text. Detaljerad beskrivning refererar till den här [databasen](https://github.com/Azure/MachineLearningSamples-AMLTextPackage-TwitterSentimentPrediction).

### <a name="data-acquisition-and-understandinghttpsgithubcomazuremachinelearningsamples-amltextpackage-twittersentimentpredictiontreemastercode01dataacquisitionandunderstanding"></a>[Datainsamling och förstå](https://github.com/Azure/MachineLearningSamples-AMLTextPackage-TwitterSentimentPrediction/tree/master/code/01_data_acquisition_and_understanding)
Det första steget i det här exemplet är att hämta sentiment140 datauppsättningen och dela upp den i tåg och testa datauppsättningar. Sentiment140 datauppsättningen innehåller det faktiska innehållet i tweet (med uttryckssymboler bort) tillsammans med polaritet för var och en av tweet (negativt = 0, positivt = 4), samt med neutral tweets tas bort. Den resulterande träningsdata har 1.3 miljoner rader och testa data har 320 kB rader.

### <a name="modelinghttpsgithubcomazuremachinelearningsamples-amltextpackage-twittersentimentpredictiontreemastercode02modeling"></a>[Modeling](https://github.com/Azure/MachineLearningSamples-AMLTextPackage-TwitterSentimentPrediction/tree/master/code/02_modeling)
Den här delen av exemplet är ytterligare indelade i tre kapitel: 
- **Egenskapsval** motsvarar generering av funktioner som använder olika word bädda in algoritm, nämligen Word2Vec. 
- **Modellen skapa** behandlar utbildning av olika modeller som _logistic regression_ och _toning förstärkning_ att förutsäga sentiment av indatatexten. 
- **Modellen utvärdering** gäller den tränade modellen över tester data.

#### <a name="feature-engineering"></a>Funktionstekniker
Vi använder <b>Word2Vec</b> att generera word inbäddningar. Första vi använder algoritmen Word2Vec i Skipgram läge enligt beskrivningen i dokumentet [Mikolov Tomas, m.fl. Distribuerade representationer av ord och fraser och deras compositionality. Utvecklingen av neural bearbeta system information. 2013.](https://arxiv.org/abs/1310.4546) Generera word inbäddningar.

Hoppa över gram är ett lite neurala nätverk med målordet kodas som en en varm vector som indata och använder den för att förutsäga Närliggande ord. Om V är storleken på en terminologi och storleken på utdata-lagret är __C * V__ där C är storleken på fönstret kontext. Hoppa över gram-baserad arkitektur visas i följande bild.
 
<table class="image" align="center">
<caption align="bottom">Hoppa över gram modellen</caption>
<tr><td><img src="https://s3-ap-south-1.amazonaws.com/av-blog-media/wp-content/uploads/2017/06/05000515/Capture2-276x300.png" alt="Skip-gram model"/></td></tr>
</table>

Information om Word2Vec algoritmen och hoppa över gram modellen är utanför omfattningen för det här exemplet och berörda läsare begär att gå igenom följande länkar för mer information. Den koden 02_A_Word2Vec.py refererar till [tensorflow exempel](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/examples/tutorials/word2vec/word2vec_basic.py)

* [Vektorn Representation av ord](https://www.tensorflow.org/tutorials/word2vec)
* [Hur fungerar word2vec?](http://www.1-4-5.net/~dmm/ml/how_does_word2vec_work.pdf)
* [Information om bruset Contrastive uppskattning och negativa provtagning](http://demo.clab.cs.cmu.edu/cdyer/nce_notes.pdf)

När utbildning processen är klar genereras två inbäddning filer i formatet TVS för modellering steg.

#### <a name="model-training"></a>modell-utbildning
När word angreppsmetoderna har genererats med någon av algoritmen SSWE eller Word2vec, är nästa steg att träna modeller att förutsäga faktiska sentiment polaritet klassificering. Det använda två typer av funktioner: Word2Vec och SSWE i två modeller: Logistic regression och Convolutional Neurala nätverk (GP). 

#### <a name="model-evaluation"></a>Modellen utvärdering
Vi ger kod att läsa in och utvärdera flera tränade modeller på test datauppsättning.


### <a name="deploymenthttpsgithubcomazuremachinelearningsamples-amltextpackage-twittersentimentpredictiontreemastercode03deployment"></a>[Distribution](https://github.com/Azure/MachineLearningSamples-AMLTextPackage-TwitterSentimentPrediction/tree/master/code/03_deployment)
Den här delen som vi tillhandahåller länkar till anvisningar om hur du operationalisera en förutsägelse modell i förväg utbildade sentiment till en webbtjänst på ett kluster i Azure Container Service (AKS). Operationalization miljö bestämmelserna Docker och Kubernetes i klustret för att hantera distributionen av webbtjänsten. Du hittar mer information om operationalization [här](https://docs.microsoft.com/en-us/azure/machine-learning/preview/model-management-service-deploy).

## <a name="conclusion"></a>Sammanfattning
Vi har gått igenom informationen om hur du tränar en inbäddning word-modell med Word2Vec och sedan använda den extraherade inbäddningar som funktioner för att träna två olika modeller för att förutsäga sentiment resultat på Twitter textdata. En av dessa modeller distribueras i Azure Container tjänster (AKS). 

## <a name="next-steps"></a>Nästa steg
Läs dokumentationen på [Azure Machine Learning-paketet för Text Analytics (AMLPTA)](https://docs.microsoft.com/en-us/python/api/overview/azure-machine-learning/textanalytics?view=azure-ml-py-latest) och [Team Data vetenskap processen (TDSP)](https://aka.ms/tdsp) att komma igång.

## <a name="references"></a>Referenser
* [TDSP](https://docs.microsoft.com/en-us/azure/machine-learning/team-data-science-process/overview) 
* [Hur du använder Team Data vetenskap processen (TDSP) i Azure Machine Learning](https://aka.ms/how-to-use-tdsp-in-aml)
* [TDSP projektmall för Azure Machine Learning](https://aka.ms/tdspamlgithubrepo)
* [Azure ML arbete bänk](https://docs.microsoft.com/en-us/azure/machine-learning/preview/)
* [Mikolov Tomas, m.fl. Distribuerade representationer av ord och fraser och deras compositionality. Utvecklingen av neural bearbeta system information. 2013.](https://arxiv.org/abs/1310.4546)
