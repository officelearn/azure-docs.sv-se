---
title: Modell tolkning i Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Lär dig hur du förklarar varför din modell gör förutsägelser med hjälp av Azure Machine Learning SDK. Den kan användas under utbildning och härledning för att förstå hur din modell gör förutsägelser.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: trbye
ms.date: 10/25/2019
ms.openlocfilehash: 339ab811969a3de6ce87d529e1bf77f325be4071
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2020
ms.locfileid: "75968491"
---
# <a name="model-interpretability-in-azure-machine-learning"></a>Modell tolkning i Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

## <a name="overview-of-model-interpretability"></a>Översikt över modell tolkning

Tolkning är avgörande för data vetenskaps-och affärs besluts fattare som är lika för att säkerställa efterlevnaden av företagets principer, bransch standarder och myndighets bestämmelser:
+ Data experter behöver kunna förklara sina modeller till chefer och intressenter, så att de kan förstå värdet och noggrannheten i sina resultat 
+ Besluts fattare i företag behöver tryggare möjlighet att tillhandahålla insyn för slutanvändare för att få och upprätthålla sitt förtroende

Att göra det möjligt att förklara en maskin inlärnings modell är viktigt under två huvud faser av modell utveckling:
+ Under övnings fasen i utvecklings cykeln för Machine Learning-modeller. Modell designers och utvärderare kan använda tolknings resultat för en modell för att verifiera Hypotheses och bygga förtroende med intressenter. De använder också insikter i modellen för fel sökning, validering av modell beteendet matchar deras mål och för att kontrol lera om det finns några kompensations-eller obetydliga funktioner.
+ I inferencing-fasen, som har genomskinlighet kring distribuerade modeller, ger chefer möjlighet att förstå "när de distribueras", hur modellen fungerar och hur dess beslut behandlas och påverkar personer i real tid. 

## <a name="interpretability-with-azure-machine-learning"></a>Tolkning med Azure Machine Learning

I den här artikeln får du lära dig hur modell tolknings koncept implementeras i SDK.

Med hjälp av klasserna och metoderna i SDK kan du hämta:
+ Funktions prioritets värden för både rå och uttillverkade funktioner
+ Tolkning av verkliga data uppsättningar i stor skala, under utbildning och härledning.
+ Interaktiva visualiseringar som hjälper dig att upptäcka mönster i data och förklaringar i utbildnings tid


I Machine Learning är **funktioner** de data fält som används för att förutsäga en mål data punkt. För att förutsäga kredit risken kan exempelvis data fält för ålder, konto storlek och konto ålder användas. I det här fallet är ålder, konto storlek och konto ålder **funktioner**. Funktions prioriteten visar hur varje data fält påverkar modellens förutsägelser. Till exempel kan ålder användas kraftigt i förutsägelsen när kontots storlek och ålder inte påverkar förutsägelse noggrannheten avsevärt. Den här processen gör det möjligt för data experter att förklara de resulterande förutsägelserna, så att intressenterna får insyn i de data punkter som är viktigast i modellen.

Med hjälp av dessa verktyg kan du förklara maskin inlärnings modeller **globalt på alla data**eller **lokalt på en viss data punkt** med hjälp av de avancerade teknikerna i en lättanvänd och skalbar miljö.

Klasser för tolkning kan göras tillgängliga via flera SDK-paket. Lär dig hur du [installerar SDK-paket för Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

* `azureml.interpret`, huvud paketet, som innehåller funktioner som stöds av Microsoft.

* `azureml.contrib.interpret`-, för hands-och experiment funktioner som du kan prova.

* `azureml.train.automl.automlexplainer`-paket för att tolka automatiserade maskin inlärnings modeller.

> [!IMPORTANT]
> Innehållet i namn området `contrib` stöds inte fullt ut. När experiment funktionerna blir mogna, kommer de gradvis att flyttas till huvud namn rummet.

## <a name="how-to-interpret-your-model"></a>Så här tolkar du din modell

Du kan använda klasser och metoder för tolkning för att förstå modellens globala beteende eller vissa förutsägelser. Den tidigare kallas global förklaring och den senare kallas lokal förklaring.

Metoderna kan också kategoriseras baserat på om metoden är modell oberoende eller modell Specific. Vissa metoder riktar sig till viss typ av modeller. Till exempel gäller SHAP träd förklaringar endast för trädbaserade modeller. Vissa metoder behandlar modellen som en svart ruta, t. ex. imiterare eller SHAPs kernel-förklaring. `interpret`-paketet utnyttjar dessa olika metoder baserat på data uppsättningar, modell typer och användnings fall.

Utdata är en uppsättning information om hur en specifik modell gör dess förutsägelse, till exempel:
* Global/lokal relativ funktions prioritet
* Global/lokal funktion och förutsägelse relation

### <a name="explainers"></a>Förklaringar

Det här paketet använder tolknings tekniker som har utvecklats i [tolkning – community](https://github.com/interpretml/interpret-community/), ett python-paket med öppen källkod för inlärnings bara modeller och hjälper till att förklara blackbox AI-system. [Tolkning – community](https://github.com/interpretml/interpret-community/) fungerar som värd för den här SDK: s support förklaringar och stöder för närvarande följande tolknings tekniker:

* **SHAP Tree-förklaring**: [SHAP](https://github.com/slundberg/shap): s träd förklarare, som fokuserar på polynom Time fast SHAP värde uppskattnings algoritm som är särskilt avsedd för träd och ensembler i träd.
* **SHAP djup förklaring**: baserat på förklaringen från [SHAP](https://github.com/slundberg/shap)är djupgående Förklaran en höghastighets approximation för SHAP värden i djup inlärnings modeller som bygger på en anslutning med DeepLIFT som beskrivs i [SHAP Nips-papper](https://papers.nips.cc/paper/7062-a-unified-approach-to-interpreting-model-predictions). TensorFlow-modeller och keras-modeller med TensorFlow-backend stöds (det finns även stöd för PyTorch) ".
* **SHAP linjär förklara**: [SHAPs](https://github.com/slundberg/shap)linjära förklaring beräknar SHAP-värden för en linjär modell, vilket kan vara att redovisa mellan funktions korrelationer.

* **SHAP kernel-förklaring**: [SHAPens](https://github.com/slundberg/shap)kernel-förklaring använder en särskilt viktad lokal linjär regression för att uppskatta SHAP-värden för alla modeller.
* **Imitera förklaring**: imitera förklaring är baserad på idén med att träna [globala surrogat modeller](https://christophm.github.io/interpretable-ml-book/global.html) för att efterlikna blackbox-modeller. En global surrogat modell är en modell med en inbyggd tolkning som är tränad att approximera förutsägelserna av en svart Box-modell så fort som möjligt. Data expert kan tolka surrogat modellen för att rita slut satser om den svarta Box-modellen. Du kan använda någon av följande tolknings bara modeller som surrogat modell: LightGBM (LGBMExplainableModel), linjär regression (LinearExplainableModel), Stochastic gradient brantaste-modell (SGDExplainableModel) och besluts träd ( DecisionTreeExplainableModel).


* **Förklaring av permutation-funktions prioritet**: permutations funktionens betydelse är en teknik som används för att förklara klassificerings-och Regressions modeller som inspireras av [Breiman-bladet för slumpmässiga skogar](https://www.stat.berkeley.edu/~breiman/randomforest2001.pdf) (se avsnitt 10). På en hög nivå är det sättet som det fungerar genom att slumpmässigt blandning data en funktion i taget för hela data uppsättningen och att beräkna hur mycket prestanda måtten för räntan förändras. Ju större ändringen är, desto viktigare är funktionen.

* **Lime-förklaring** (`contrib`): baserad på [lime](https://github.com/marcotcr/lime), kalk förklarar den avancerade oberoende förklaringarna (kalk) för att skapa lokala surrogat modeller. Till skillnad från globala surrogat modeller fokuserar kalk på att träna lokala surrogat modeller för att förklara enskilda förutsägelser.
* **Text förklaring** (`contrib`): han text-förklaring använder ett hierarkiskt Attention-nätverk för att få modell förklaringar från text data för en specifik text modell i svart ruta. Det tränar till HAN-surrogat-modellen på en viss svart Box-modells förväntade utdata. Efter att ha tränat över texten sökkorpus lägger det till ett fin justerings steg för ett särskilt dokument för att förbättra noggrannheten i förklaringarna. HAN använder en dubbelriktad RNN med två åtgärds lager, för mening och ord uppmärksamhet. När DNN har tränats på en svart Box-modell och finjusteras för ett specifikt dokument kan användaren extrahera ordets betydelse från åtgärds lagren. HAN har visat sig vara mer exakt än kalk eller SHAP för text data, men mer kostsamhet vad gäller inlärnings tiden. Förbättringar har gjorts för att ge användaren möjlighet att initiera nätverket med assisterad Word-inbäddningar för att minska inlärnings tiden. Inlärnings tiden kan förbättras avsevärt genom att köra HAN på en virtuell dator med Azure GPU. Implementeringen av HAN beskrivs i ["hierarkiska Attention-nätverk för dokument klassificering (Yang et al., 2016)"](https://www.researchgate.net/publication/305334401_Hierarchical_Attention_Networks_for_Document_Classification).


* **Förklaring av tabell**: `TabularExplainer` använder följande logik för att anropa de direkta [SHAP](https://github.com/slundberg/shap) -förklaringarna:

    1. Om det är en träd-baserad modell använder du SHAP `TreeExplainer`, annars
    2. Om det är en DNN-modell använder du SHAP `DeepExplainer`, annars
    3. Om det är en linjär modell använder du SHAP `LinearExplainer`, annars
    3. Behandla den som en svart Box-modell och Använd SHAP `KernelExplainer`


`TabularExplainer` har också gjort betydande förbättringar av funktioner och prestanda i de direkta SHAP-förklaringarna:

* **Sammanfattning av initierings data uppsättningen**. I de fall då förklaringen är viktigast sammanfattar vi initierings data uppsättningen och genererar en liten uppsättning representativa exempel, vilket påskyndar både global och lokal förklaring.
* **Sampling av utvärderings data uppsättningen**. Om användaren går igenom en stor uppsättning utvärderings exempel, men inte behöver alla dem för att utvärderas, kan exempel parametern anges till sant för att påskynda den globala förklaringen.

Följande diagram visar den aktuella strukturen för direkta och meta-förklaringar.

[Arkitektur för ![Machine Learning tolken](./media/how-to-machine-learning-interpretability/interpretability-architecture.png)](./media/how-to-machine-learning-interpretability/interpretability-architecture.png#lightbox)


### <a name="models-supported"></a>Modeller som stöds

Alla modeller som har tränats på data uppsättningar i python `numpy.array`, `pandas.DataFrame`, `iml.datatypes.DenseData`eller `scipy.sparse.csr_matrix` format stöds av tolknings `explain` paketet för SDK.

Förklarings funktionerna accepterar både modeller och pipeliner som inmatade. Om en modell anges måste modellen implementera förutsägelse funktionen `predict` eller `predict_proba` som följer Scikit-konventionen. Om en pipeline (namnet på pipelinen) anges förutsätter förklarings funktionen att skriptet för pipeline-körningen returnerar en förutsägelse. Vi har stöd för modeller som har tränats via PyTorch, TensorFlow och keras djup inlärnings ramverk.

### <a name="local-and-remote-compute-target"></a>Lokalt och fjärrstyrt beräknings mål

`explain`-paketet är utformat för att fungera med både lokala och fjärranslutna beräknings mål. Om kör lokalt kontaktar inte SDK-funktionerna några Azure-tjänster. Du kan köra förklaringen via fjärr anslutning på Azure Machine Learning beräkna och logga förklarings informationen i Azure Machine Learning köra historik tjänster. När den här informationen har loggats finns rapporter och visualiseringar från förklaringen enkelt på Azure Machine Learning arbets yta för användar analys.


## <a name="next-steps"></a>Nästa steg

Se [anvisningar för att](how-to-machine-learning-interpretability-aml.md) aktivera tolkning för modell utbildning både lokalt och på Azure Machine Learning fjärrstyrda beräknings resurser. Se [exempel antecknings böcker](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model) för ytterligare scenarier.
