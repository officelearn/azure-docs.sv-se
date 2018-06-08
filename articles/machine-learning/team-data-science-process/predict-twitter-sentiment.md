---
title: Förutsäga Twitter-åsikter med word inbäddningar med hjälp av Team av vetenskapliga data i Azure | Microsoft Docs
description: De steg som behövs för att köra datavetenskap projekt.
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: deguhath
ms.openlocfilehash: f47668cd706b78977418925d64eca583d7878cd3
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "34838317"
---
# <a name="predict-twitter-sentiment-with-word-embeddings-by-using-the-team-data-science-process"></a>Förutsäga Twitter-åsikter med word inbäddningar med hjälp av Team av vetenskapliga data

Den här artikeln visar hur du samarbeta effektivt med hjälp av den _Word2Vec_ word bädda in algoritmen och _Sentiment-specifika ord bädda in (SSWE)_ algoritmen för att förutsäga Twitter-åsikter med [Azure Machine Learning](../service/index.yml). Läs mer på Twitter-sentiment polaritet att förutsäga den [MachineLearningSamples TwitterSentimentPrediction databasen](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction) på GitHub. Nyckeln till att underlätta effektiva gruppsamarbete på datavetenskap projekt är att standardisera struktur och dokumentation av projekt med en fastställd datavetenskap livscykel. Den [Team Data vetenskap processen (TDSP)](overview.md) ger den här typen av strukturerade [livscykel](lifecycle.md). 

Skapa datavetenskap projekt med den _TDSP mallen_ innehåller det standardiserade ramverket för Azure Machine Learning-projekt. Tidigare TDSP-teamet släppte en [GitHub-lagringsplatsen för TDSP projektstruktur och mallar](https://github.com/Azure/Azure-TDSP-ProjectTemplate). Nu Machine Learning-projekt som instansieras med [TDSP mallar för Azure Machine Learning](https://github.com/amlsamples/tdsp) är aktiverade. Instruktioner finns i hur du använder [TDSP struktur projekt med mallen TDSP](../desktop-workbench/how-to-use-tdsp-in-azure-ml.md) i Azure Machine Learning. 


## <a name="twitter-sentiment-polarity-sample"></a>Twitter-sentiment polaritet exempel

Den här artikeln används ett exempel på hur du kan skapa och köra ett Machine Learning-projekt. Används av TDSP struktur och mallar i Azure Machine Learning-arbetsstationen. Hela exemplet tillhandahålls i [den här genomgången](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/blob/master/docs/deliverable_docs/Step_By_Step_Tutorial.md). Aktiviteten modellering beräknar sentiment polaritet (positiv eller negativ) med hjälp av texten från tweets. Den här artikeln beskrivs data modellering uppgifter som beskrivs i den här genomgången. Den här genomgången innehåller följande uppgifter:

- Datagranskning, utbildning och distribution av machine learning-modell som gäller förutsägelse problemet som beskrivs i case översikt för användning. [Twitter-sentiment data](http://cs.stanford.edu/people/alecmgo/trainingandtestdata.zip) används för dessa uppgifter.
- Körningen av projektet med hjälp av Azure Machine Learning TDSP mallen för det här projektet. TDSP livscykel används för körning av projektet och rapportering.
- Operationalization lösning direkt från Azure Machine Learning i Azure Container Service.

Projektet visar följande funktioner i Azure Machine Learning:

- Instansieringen och användning av TDSP-strukturen.
- Körning av kod i Azure Machine Learning-arbetsstationen.
- Enkelt operationalization i Container Service med hjälp av Docker och Kubernetes.

## <a name="team-data-science-process"></a>TDSP (Team Data Science Process)
För att köra det här exemplet använder du de TDSP projektmallarna struktur och dokumentation i Azure Machine Learning-arbetsstationen. Exemplet implementerar den [TDSP livscykel](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md)som visas i följande bild:

![TDSP livscykel](./media/predict-twitter-sentiment/tdsp-lifecycle.PNG)

TDSP-projekt har skapats i Azure Machine Learning arbetsstationen baserat på [instruktionerna](https://github.com/amlsamples/tdsp/blob/master/docs/how-to-use-tdsp-in-azure-ml.md)som visas i följande bild:

![Skapa TDSP i Azure Machine Learning-arbetsstationen](./media/predict-twitter-sentiment/tdsp-instantiation.PNG) 


## <a name="data-acquisition-and-preparationhttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode01dataacquisitionandunderstanding"></a>[Datainsamling och förberedelse](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/01_data_acquisition_and_understanding)
Det första steget i det här exemplet är att hämta sentiment140 datauppsättningen och dela in data i träning och testning datauppsättningar. Sentiment140 datamängden innehåller det faktiska innehållet i tweet (med uttryckssymboler tas bort). Dataset innehåller också polaritet för varje tweet (negativt = 0, positivt = 4) med neutral tweets bort. När data delas av att träningsinformationen har 1.3 miljoner rader och tester data har 320,000 rader.


## <a name="model-developmenthttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling"></a>[Modell-utveckling](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling)

Nästa steg i samplet som är att utveckla en modell för data. Aktiviteten modellering är uppdelat i tre delar:

- Egenskapsval: Generera funktioner för modellen med hjälp av olika word bädda in algoritmer. 
- Skapa modellen: träna modeller att förutsäga sentiment av indatatexten. Exempel på dessa modeller är _Logistic Regression_ och _toning förstärkning_.
- Modellen utvärdering: utvärdera tränade modeller över tester data.


### <a name="feature-engineeringhttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling01featureengineering"></a>[Funktionen tekniker](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling/01_FeatureEngineering)

Algoritmer för Word2Vec och SSWE används för att generera word inbäddningar. 


#### <a name="word2vec-algorithm"></a>Word2Vec algoritm

Algoritmen Word2Vec används i Skip-g-modellen. Den här modellen är förklaras i dokumentet Tomas Mikolov m.fl. ”[Distribuerade representationer av ord och fraser och deras Compositionality. Utvecklingen av neural bearbeta system information. ](https://arxiv.org/abs/1310.4546)" 2013.

Hoppa över Gram modellen är ett lite neurala nätverk. Indata är målordet som är kodade som en en-hot vector som används för att förutsäga Närliggande ord. Om **V** är storleken på ordförråd, storleken på utdata-lagret är __C * V__ där C är storleken på fönstret kontext. Följande bild visar en arkitektur som baseras på Skip Gram modellen:

![Hoppa över Gram modellen](./media/predict-twitter-sentiment/skip-gram-model.PNG)

Detaljerad säkerhetsnivån Word2Vec algoritmen och hoppa över Gram modellen är utanför omfattningen för det här exemplet. Mer information finns i följande referenser:

- [02_A_Word2Vec.PY kod med refererade TensorFlow-exempel](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/examples/tutorials/word2vec/word2vec_basic.py) 
- [Vektorn representationer av ord](https://www.tensorflow.org/tutorials/word2vec)
- [Hur fungerar word2vec?](http://www.1-4-5.net/~dmm/ml/how_does_word2vec_work.pdf)
- [Information om bruset Contrastive uppskattning och negativa provtagning](http://demo.clab.cs.cmu.edu/cdyer/nce_notes.pdf)




#### <a name="sentiment-specific-word-embedding-algorithm"></a>Bädda in sentiment-specifika ord algoritm
Algoritmen SSWE försöker lösa en svaghet av algoritmen Word2Vec där ord med liknande kontexter och motsatt polaritet kan ha liknande word angreppsmetoderna. Likheterna kan orsaka Word2Vec-algoritmen för att genomföra inte korrekt för uppgifter som sentiment analys. Algoritmen SSWE försöker hantera den här svaghet genom att inkludera både meningen polaritet och ordet kontext till dess förlust-funktionen.

En variant av algoritmen SSWE används på följande sätt:

- Ursprungligt _ngram_ och den skadade _ngram_ används som indata.
- En rangordning format led förlust av funktionen används för både syntaktiska förlust och semantiska går förlorade.
- Funktionen ultimate förlust är viktad kombination av både syntaktiska förlust och semantiska går förlorade.
- För enkelhetens skull används bara den semantiska mellan entropi som funktionen går förlorade.

Exemplet visar att prestandan för inbäddning av SSWE även med funktionen enklare förlust är bättre än Word2Vec bädda in. Följande bild visar convolutional modellen som används för att generera sentiment-specifika ord bädda in:

![Neurala nätverket modellen inspirerat av SSWE](./media/predict-twitter-sentiment/embedding-model-2.PNG)

När utbildning processen är klar genereras två inbäddning filer i formatet tabbavgränsade värden (TVS) för modellering steg.

Mer information om algoritmerna SSWE finns i dokumentet av Duyu Tang m.fl. ”[Learning Sentiment-specifika Word inbäddning av Twitter-Sentiment klassificering](http://www.aclweb.org/anthology/P14-1146)”. Associationen för beräkningar Linguistics (1). 2014.


### <a name="model-creationhttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling02modelcreation"></a>[Skapa en modell](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling/02_ModelCreation)
När word angreppsmetoderna har genererats med hjälp av algoritmen SSWE eller Word2Vec tränas klassificering modeller att förutsäga den faktiska sentiment polaritet. Två typer av funktioner: Word2Vec och SSWE, tillämpas på två modeller: toning förstärkning modellen och Logistic Regression modell. Därför tränas fyra olika modeller.


### <a name="model-evaluationhttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling03modelevaluation"></a>[Modellen utvärdering](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling/03_ModelEvaluation)
När modeller tränas för modeller att testa Twitter textdata och utvärdera varje modell prestanda. Exemplet utvärderar följande fyra modeller:

- Toning Boosting över SSWE bädda in.
- Logistic Regression över SSWE bädda in.
- Toning Boosting över Word2Vec bädda in.
- Logistic Regression över Word2Vec bädda in.

En jämförelse av de fyra modellerna visas i följande bild:

![Mottagaren operativsystem karakteristiken (ROC) modellen jämförelse](./media/predict-twitter-sentiment/roc-model-comparison.PNG)

Toning förstärkning modellen med funktionen SSWE ger bästa prestanda när du vill jämföra modeller med hjälp av området under kurvan (AUC) mått.


## <a name="deploymenthttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode03deployment"></a>[Distribution](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/03_deployment)

Det sista steget är distributionen av utbildade sentiment förutsägelse modellen till en webbtjänst på ett kluster i Azure Container Service. Modellen toning förstärkning används med algoritmen SSWE inbäddning som den tränade modellen. Operationalization miljön etablerar Docker och Kubernetes i klustret för att hantera distributionen av webbtjänsten som visas i följande bild: 

![Kubernetes-instrumentpanel](./media/predict-twitter-sentiment/kubernetes-dashboard.PNG)

Läs mer om operationalization [distribuera en Azure Machine Learning-modell som en webbtjänst](../desktop-workbench/model-management-service-deploy.md).

## <a name="conclusion"></a>Sammanfattning

I den här artikeln beskrivs hur du tränar en modell för inbäddning av word med hjälp av Word2Vec och Sentiment-specifika ord inbäddning algoritmer. Extraherade inbäddningar användes för att träna flera modeller att förutsäga sentiment poängen för Twitter-textdata som funktioner. Funktionen SSWE används med toningen förstärkning modellen gav bästa prestanda. Modellen distribueras sedan som en realtid webbtjänst i Container Service med hjälp av Azure Machine Learning-arbetsstationen.


## <a name="references"></a>Referenser

* [TDSP](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview) 
* [Hur du använder Team Data vetenskap processen (TDSP) i Azure Machine Learning](https://aka.ms/how-to-use-tdsp-in-aml)
* [TDSP projektmallar för Azure Machine Learning](https://aka.ms/tdspamlgithubrepo)
* [Azure Machine Learning Workbench](../service/index.yml)
* [USA intäkter datauppsättning från UCI ML-databasen](https://archive.ics.uci.edu/ml/datasets/adult)
* [Biomedicinska entitet recognition med hjälp av TDSP mallar](../desktop-workbench/scenario-tdsp-biomedical-recognition.md)
* [Mikolov Tomas, m.fl. ”Distribuerade representationer av ord och fraser och deras Compositionality. Avancerar i neural bearbeta system information ”. 2013.](https://arxiv.org/abs/1310.4546)
* [Tang Duyu, m.fl. ”Utbildning Sentiment-specifika ord inbäddning av klassificering för Twitter-Sentiment”. ACL (1). 2014.](http://www.aclweb.org/anthology/P14-1146)
