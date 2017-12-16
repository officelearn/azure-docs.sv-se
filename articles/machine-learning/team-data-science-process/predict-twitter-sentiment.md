---
title: "Förutsäga Twitter-åsikter med word inbäddningar med Team vetenskap av data - Azure | Microsoft Docs"
description: "De steg som krävs för att köra datavetenskap projekt"
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: bradsev;
ms.openlocfilehash: fe1c87df40102a62e1e0c8873b25fa3df7d743bc
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2017
---
# <a name="predict-twitter-sentiment-with-word-embeddings-using-the-team-data-science-process"></a>Förutsäga Twitter-åsikter med word inbäddningar med Team av vetenskapliga data

Den här artikeln visar hur du samarbeta effektivt när du använder den **Word2Vec** word bädda in algoritmen och **Sentiment specifika ord bädda in (SSWE) algoritmen** att förutsäga Twitter-åsikter med den [Azure Machine Learning](../preview/index.yml). För ytterligare information om aktiviteten för att förutsäga twitter-sentiment polaritet, se [databasen](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction). Nyckeln till att underlätta effektiva gruppsamarbete på datavetenskap projekt är att standardisera struktur och dokumentation av projekt med en fastställd datavetenskap livscykel. Den [Team Data vetenskap processen (TDSP)](overview.md) innehåller sådant strukturerade [livscykel](lifecycle.md). 

Skapa datavetenskap projekt med den **TDSP mallen** innehåller den här standardiserade ramverk för Azure Machine Learning-projekt. Tidigare TDSP-teamet har publicerat en [GitHub-lagringsplatsen för TDSP projektstruktur och mallar](https://github.com/Azure/Azure-TDSP-ProjectTemplate). Nu skapas av Azure Machine Learning-projekt som instansieras med [TDSP struktur och dokumentation mallar för Azure Machine Learning](https://github.com/amlsamples/tdsp) har aktiverats. Instruktioner om hur du använder TDSP struktur och mallar i Azure Machine Learning finns [struktur projekt med mallen Team datavetenskap Process](../preview/how-to-use-tdsp-in-azure-ml.md). 


## <a name="the-sentiment-polarity-sample"></a>Sentiment polaritet exempel

Ett exempel som visar hur du initiera och kör machine learning-projekt med Team datavetenskap Process strukturen och mallar i Azure Machine Learning Arbetsbänk har angetts i det här [genomgången](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/blob/master/docs/deliverable_docs/Step_By_Step_Tutorial.md). Aktiviteten modellering är att förutsäga sentiment polaritet (positiv eller negativ) med hjälp av texten från tweets. Den här artikeln beskriver datamodeller uppgifter som beskrivs i den här genomgången. Den här genomgången innehåller följande uppgifter:

- Datagranskning, utbildning och distribution av en maskininlärningsmodell som göra förutsägelser problemet som beskrivs i fallet översikt för användning. För detta ändamål [Twitter-Sentiment data](http://cs.stanford.edu/people/alecmgo/trainingandtestdata.zip) används.
- Körningen av projektet i Azure Machine Learning med mallen Team Data vetenskap processen (TDSP) från Azure Machine Learning för det här projektet. TDSP livscykel används för körning av projektet och rapportering.
- Operationalization lösning direkt från Azure Machine Learning i Azure Container Services.

Projektet visar flera funktioner i Azure Machine Learning sådana TDSP struktur instansiering och Använd, körning av kod i Azure Machine Learning Arbetsbänk och enkelt operationalization i Azure Container Services med Docker och Kubernetes.

## <a name="team-data-science-process"></a>TDSP (Team Data Science Process)
Du kan använda projektet TDSP mallar struktur och dokumentation för att köra det här exemplet. Följer den [TDSP livscykel]((https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md)). Projektet har skapats utifrån anvisningarna [här](https://github.com/amlsamples/tdsp/blob/master/docs/how-to-use-tdsp-in-azure-ml.md).

![TDSP livscykel](./media/predict-twitter-sentiment/tdsp-lifecycle.PNG)

![Skapa en instans av TDSP](./media/predict-twitter-sentiment/tdsp-instantiation.PNG) 


## <a name="data-acquisition-and-understandinghttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode01dataacquisitionandunderstanding"></a>[Datainsamling och förstå](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/01_data_acquisition_and_understanding)
Det första steget i det här exemplet är att hämta sentiment140 datauppsättningen och dela upp den i träning och testning datauppsättningar. Sentiment140 datamängden innehåller det faktiska innehållet i tweet (med uttryckssymboler bort) tillsammans med polaritet för var och en av tweet (negativt = 0, positivt = 4), med neutral tweets tas bort. När uppdelat, resulterande träningsdata har 1.3 miljoner rader och tester data har 320 kB rader.


## <a name="modelinghttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling"></a>[Modeling](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling)

Den här delen av provet delas upp ytterligare i tre delar:
 
- **Egenskapsval** motsvarar generering av funktioner som använder olika word bädda in algoritmer. 
- **Modellen skapa** behandlar utbildning av olika modeller som _logistic regression_ och _toning förstärkning_ att förutsäga sentiment av indatatexten. 
- **Modellen utvärdering** gäller den tränade modellen över tester data.


### <a name="feature-engineeringhttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling01featureengineering"></a>[Funktionen tekniker](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling/01_FeatureEngineering)

Word2Vec och SSWE är ordet bädda in algoritmer som används här för att generera word inbäddningar. 


#### <a name="word2vec"></a>Word2Vec

Algoritmen Word2Vec används i Skipgram-läge. Det här sättet att generera word inbäddningar förklaras i dokumentet av Tomas Mikolov et al.: [distribuerade garantier ord och fraser och deras Compositionality. Utvecklingen av neural bearbeta system information. 2013.](https://arxiv.org/abs/1310.4546).

Hoppa över gram är ett lite neurala nätverk. Indata är målet ordet kodad som en en varm vector som använder för att förutsäga Närliggande ord. Om **V** är storleken på ordförråd, storleken på utdata-lager skulle vara __C * V__ där C är storleken på fönstret kontext. Hoppa över gram-baserad arkitektur visas i följande bild:

![Hoppa över gram modellen](./media/predict-twitter-sentiment/skip-gram-model.PNG)

***Hoppa över gram modellen***

Detaljerad säkerhetsnivån word2vec algoritmen och hoppa över gram modellen är utanför omfattningen för det här exemplet. Läsare är intresserad av mer information om dess bearbetningen kan finns i följande referenser:

- [Koden 02_A_Word2Vec.py refereras TensorFlow exempel](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/examples/tutorials/word2vec/word2vec_basic.py)
- [Vektorn Representation av ord](https://www.tensorflow.org/tutorials/word2vec)
- [Hur fungerar word2vec?](http://www.1-4-5.net/~dmm/ml/how_does_word2vec_work.pdf)
- [Information om bruset Contrastive uppskattning och negativa provtagning](http://demo.clab.cs.cmu.edu/cdyer/nce_notes.pdf)


#### <a name="sswe"></a>SSWE
Den **Sentiment specifika ord bädda in (SSWE) algoritmen** försöker lösa en svaghet i Word2vec algoritmen att ord med liknande kontexter och motsatt polaritet kan ha liknande word angreppsmetoderna. Den här likhet innebär att Word2vec inte kan genomföra korrekt för uppgifter som sentiment analys. Algoritmen SSWE försöker hantera den här svaghet genom att inkludera både meningen polaritet och ordet kontext till dess förlust-funktionen.

Det här exemplet används en variant av SSWE. SSWE använder både den ursprungliga ngram och skadat ngram som indata och den använder en rangordning stil led förlust av funktionen för både syntaktiska förlust och semantiska går förlorade. Ultimate förlust funktionen är viktad kombination av både syntaktiska dataförlust och semantiska dataförlust. För enkelhetens skull används bara den semantiska mellan entropi som funktionen går förlorade. Som du ser senare, även om den här funktionen för enklare förlust är prestanda för SSWE bädda in bättre än Word2Vec bädda in.

SSWE inspirerat neurala nätverket modellen som du använder i det här exemplet visas i följande bild:

![Jämförelse av ROC modellen](./media/predict-twitter-sentiment/embedding-model-2.PNG)

***Convolutional modellen för att generera sentiment-specifika ord bädda in.***


När utbildning processen är klar genereras två inbäddning filer i formatet TVS för modellering steg.

Mer information om algoritmerna SSWE finns i dokumentet av Duyu Tang et al.: [Learning Sentiment-specifika ord inbäddning av Twitter-Sentiment klassificering. ACL (1). 2014.](http://www.aclweb.org/anthology/P14-1146) 


### <a name="model-creationhttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling02modelcreation"></a>[Skapa en modell](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling/02_ModelCreation)
När word angreppsmetoderna har skapats med antingen SSWE eller Word2vec algoritmer, är nästa steg att träna modeller att förutsäga faktiska sentiment polaritet klassificering. Två typer av funktioner, Word2Vec och SSWE, tillämpas på två modeller GBM modell och Logistic regressionsmodell. Så fyra olika modeller är håller på att tränas.


### <a name="model-evaluationhttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling03modelevaluation"></a>[Modellen utvärdering](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling/03_ModelEvaluation)
Nu kan du använda fyra modeller tränas i föregående steg i att testa data för att utvärdera modellens prestanda. 

1. Toning Boosting över SSWE bädda in
2. Logistic Regression över SSWE bädda in
3. Toning Boosting över Word2Vec bädda in
4. Logistic Regression över Word2Vec bädda in

![Jämförelse av ROC modellen](./media/predict-twitter-sentiment/roc-model-comparison.PNG)

GBM modellen med SSWE funktioner är den bästa med AUC mått.


## <a name="deploymenthttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode03deployment"></a>[Distribution](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/03_deployment)

Den här delen distribuerar utbildade sentiment förutsägelse modellen (SSWE bädda in + GBM modellen) till en webbtjänst på ett kluster i Azure Container Service (ACS). Operationalization miljö bestämmelserna Docker och Kubernetes i klustret för att hantera distributionen av webbtjänsten. Du hittar mer information om operationalization [här](https://docs.microsoft.com/en-us/azure/machine-learning/preview/model-management-service-deploy).

![kubenetes_dashboard](./media/predict-twitter-sentiment/kubernetes-dashboard.PNG)


## <a name="conclusion"></a>Slutsats

Information om hur du tränar en modell för inbäddning av word med algoritmer Word2Vec och SSWE har förklaras och extraherade inbäddningar som funktioner som användes för att träna flera modeller att förutsäga sentiment poängen för Twitter textdata. Funktionen Sentiment specifika ordval Embeddings(SSWE) med toning ökat trädet modell gav bästa prestanda. Den här modellen distribuerades sedan som en webbtjänst som realtid i Azure Container tjänster med hjälp av Azure Machine Learning Arbetsbänk.


## <a name="references"></a>Referenser

* [Team av vetenskapliga data](https://docs.microsoft.com/en-us/azure/machine-learning/team-data-science-process/overview) 
* [Hur du använder Team Data vetenskap processen (TDSP) i Azure Machine Learning](https://aka.ms/how-to-use-tdsp-in-aml)
* [TDSP projektmall för Azure Machine Learning](https://aka.ms/tdspamlgithubrepo)
* [Azure ML arbete bänk](https://docs.microsoft.com/en-us/azure/machine-learning/preview/)
* [USA intäkter datauppsättning från UCI ML-databas](https://archive.ics.uci.edu/ml/datasets/adult)
* [Biomedicinska entitet bokföring med hjälp av TDSP mall](https://docs.microsoft.com/en-us/azure/machine-learning/preview/scenario-tdsp-biomedical-recognition)
* [Mikolov Tomas, m.fl. Distribuerade representationer av ord och fraser och deras compositionality. Utvecklingen av neural bearbeta system information. 2013.](https://arxiv.org/abs/1310.4546)
* [Tang Duyu, m.fl. ”Utbildning Sentiment-specifika ord inbäddning av klassificering för Twitter-Sentiment”. ACL (1). 2014.](http://www.aclweb.org/anthology/P14-1146)