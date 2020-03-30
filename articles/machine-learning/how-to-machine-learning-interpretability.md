---
title: Modelltolkbarhet i Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Lär dig hur du förklarar varför din modell gör förutsägelser med Hjälp av Azure Machine Learning SDK. Den kan användas under träning och slutledning för att förstå hur din modell gör förutsägelser.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: trbye
ms.date: 10/25/2019
ms.openlocfilehash: b68d2a72dc18f683f2203429908a536db1b5124a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063987"
---
# <a name="model-interpretability-in-azure-machine-learning"></a>Modelltolkbarhet i Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

## <a name="overview-of-model-interpretability"></a>Översikt över modellens tolkningsförmåga

Tolkningsförmåga är avgörande för både dataforskare och beslutsfattare för att säkerställa efterlevnad av företagets policyer, branschstandarder och myndighetsregler:
+ Dataforskare behöver förmågan att förklara sina modeller för chefer och intressenter, så att de kan förstå värdet och riktigheten i sina resultat 
+ Beslutsfattare i näringslivet behöver sinnesro för förmågan att skapa insyn för slutanvändarna att vinna och behålla sitt förtroende

Att aktivera möjligheten att förklara en maskininlärningsmodell är viktigt under två huvudfaser av modellutveckling:
+ Under träningsfasen av maskininlärningsmodellens utvecklingscykel. Modelldesigners och utvärderare kan använda tolkningsutdata från en modell för att verifiera hypoteser och skapa förtroende hos intressenter. De använder också insikterna i modellen för felsökning, validering av modellbeteende matchar deras mål och för att kontrollera om det finns partiska eller obetydliga funktioner.
+ Under inferencing fasen, som har öppenhet kring utplacerade modeller ger chefer att förstå "när de används" hur modellen fungerar och hur dess beslut behandlar och påverkar människor i verkliga livet. 

## <a name="interpretability-with-azure-machine-learning"></a>Tolkningsbarhet med Azure Machine Learning

I den här artikeln får du lära dig hur standardtolkbarhetsbegrepp implementeras i SDK.

Med hjälp av klasser och metoder i SDK kan du få:
+ Funktionsbetensvärden för både råa och konstruerade funktioner
+ Tolkningsförmåga på verkliga datamängder i stor skala, under utbildning och slutledning.
+ Interaktiva visualiseringar som hjälper dig att upptäcka mönster i data och förklaringar vid träningstid


I maskininlärning är **funktioner** de datafält som används för att förutsäga en måldatapunkt. För att till exempel förutsäga kreditrisk kan datafält för ålder, kontostorlek och kontoålder användas. I det här fallet är ålder, kontostorlek och kontoålder **funktioner**. Funktionsvikt talar om för dig hur varje datafält påverkade modellens förutsägelser. Ålder kan till exempel användas kraftigt i förutsägelsen medan kontostorlek och ålder inte påverkar förutsägelsenoggrannheten avsevärt. Den här processen gör det möjligt för dataexperter att förklara resulterande förutsägelser, så att intressenterna får insyn i vilka datapunkter som är viktigast i modellen.

Med hjälp av dessa verktyg kan du förklara maskininlärningsmodeller **globalt på alla data**eller lokalt på en viss **datapunkt** med hjälp av den senaste tekniken på ett lätt att använda och skalbart sätt.

Tolkningsklasserna görs tillgängliga via flera SDK-paket. Lär dig hur du [installerar SDK-paket för Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

* `azureml.interpret`, huvudpaketet, som innehåller funktioner som stöds av Microsoft.

* `azureml.contrib.interpret`, förhandsgranskning och experimentella funktioner som du kan prova.

* `azureml.train.automl.automlexplainer`paket för tolkning av automatiserade maskininlärningsmodeller.

> [!IMPORTANT]
> Innehåll i `contrib` namnområdet stöds inte fullt ut. När de experimentella funktionerna blir mogna, kommer de gradvis att flyttas till huvudnamnområdet.

## <a name="how-to-interpret-your-model"></a>Så här tolkar du din modell

Du kan använda tolkningsklasser och metoder för att förstå modellens globala beteende eller specifika förutsägelser. Den förstnämnda kallas global förklaring och den senare kallas lokal förklaring.

Metoderna kan också kategoriseras baserat på om metoden är modelloberoende eller modellspecifik. Vissa metoder riktar sig till vissa typer av modeller. SHAP:s träd explainer gäller till exempel endast trädbaserade modeller. Vissa metoder behandlar modellen som en svart låda, till exempel härma explainer eller SHAP kärna explainer. Paketet `interpret` utnyttjar dessa olika metoder baserat på datauppsättningar, modelltyper och användningsfall.

Utdata är en uppsättning information om hur en viss modell gör sin förutsägelse, till exempel:
* Global/lokal relativ funktionsvikt
* Global/lokal funktions- och förutsägelserelation

### <a name="explainers"></a>Explainers

Det här paketet använder tolkningstekniker som utvecklats i [Interpret-Community](https://github.com/interpretml/interpret-community/), ett pythonpaket med öppen källkod för att träna tolkningsbara modeller och hjälpa till att förklara BLACKBOX AI-system. [Interpret-Community](https://github.com/interpretml/interpret-community/) fungerar som värd för denna SDK:s explainers som stöds och stöder för närvarande följande tolkningstekniker:

* **SHAP Tree Explainer:** [SHAP:](https://github.com/slundberg/shap)s träd explainer, som fokuserar på polynom tid snabb SHAP värde uppskattning algoritm som är specifik för träd och ensembler av träd.
* **SHAP Deep Explainer**: Baserat på förklaringen från [SHAP](https://github.com/slundberg/shap), Deep Explainer "är en höghastighets approximation algoritm för SHAP värden i djupinlärning modeller som bygger på en anslutning med DeepLIFT beskrivs i [SHAP NIPS papper](https://papers.nips.cc/paper/7062-a-unified-approach-to-interpreting-model-predictions). TensorFlow-modeller och Keras-modeller som använder TensorFlow-backend stöds (det finns också preliminärt stöd för PyTorch)".
* **SHAP Linjär explainer:** [SHAP'](https://github.com/slundberg/shap)s Linjär explainer beräknar SHAP-värden för en linjär modell, eventuellt redovisning av inter-feature korrelationer.

* **SHAP Kernel Explainer**: [SHAP's](https://github.com/slundberg/shap)Kernel explainer använder en speciellt viktad lokal linjär regression för att uppskatta SHAP-värden för alla modeller.
* **Mimic Explainer**: Mimic explainer är baserad på idén om utbildning [globala surrogatmodeller](https://christophm.github.io/interpretable-ml-book/global.html) för att efterlikna blackbox modeller. En global surrogatmodell är en i sig tolkningsbar modell som är utbildad för att approximera förutsägelserna för en svart låda modell så exakt som möjligt. Datavetare kan tolka surrogatmodellen för att dra slutsatser om den svarta lådan modellen. Du kan använda en av följande tolkningsbara modeller som din surrogatmodell: LightGBM (LGBMExplainableModel), Linjär regression (LinearExplainableModel), Stochastic Gradient Descent explainable model (SGDExplainableModel) och Decision Tree ( DecisionTreeExplainableModel).


* **Permutation Feature Importance Explainer**: Permutation Feature Importance är en teknik som används för att förklara klassificering och regressionsmodeller som är inspirerade av [Breimans Random Forests-papper](https://www.stat.berkeley.edu/~breiman/randomforest2001.pdf) (se avsnitt 10). På en hög nivå är det sätt det fungerar genom att slumpmässigt blanda data en funktion i taget för hela datauppsättningen och beräkna hur mycket prestandamåttet av intresse ändras. Ju större förändring, desto viktigare är den funktionen.

* **LIME Explainer** (`contrib`): Baserat på [LIME](https://github.com/marcotcr/lime)använder LIME Explainer den toppmoderna lokala tolkningsbara modellagnostiska förklaringsalgoritmen (LIME) för att skapa lokala surrogatmodeller. Till skillnad från de globala surrogatmodellerna fokuserar LIME på utbildning av lokala surrogatmodeller för att förklara enskilda förutsägelser.
* **HAN Text explainer** (`contrib`): HAN Text Explainer använder ett hierarkiskt attention network för att få modellförklaringar från textdata för en viss svart ruta textmodell. Det tränar HAN surrogat modell på en viss svart låda modellens förutspådde utgångar. Efter utbildning globalt över texten corpus, lägger det till en finjustera steg för ett visst dokument för att förbättra noggrannheten i förklaringarna. HAN använder en dubbelriktad RNN med två uppmärksamhet lager, för mening och ord uppmärksamhet. När DNN tränas på den svarta lådan modellen och finjusteras på ett visst dokument, kan användaren extrahera ordet vikter från uppmärksamhet lager. HAN har visat sig vara mer exakt än LIME eller SHAP för textdata men dyrare även när det gäller träningstid. Förbättringar har gjorts för att ge användaren möjlighet att initiera nätverket med GloVe ordinbäddningar för att minska träningstiden. Utbildningstiden kan förbättras avsevärt genom att köra HAN på en fjärr-Azure GPU-vm. Implementeringen av HAN beskrivs i ["Hierarkiska uppmärksamhetsnätverk för dokumentklassificering (Yang et al., 2016)".](https://www.researchgate.net/publication/305334401_Hierarchical_Attention_Networks_for_Document_Classification)


* **Tabell explainer** `TabularExplainer` : använder följande logik för att anropa Direct [SHAP](https://github.com/slundberg/shap) Explainers:

    1. Om det är en trädbaserad modell `TreeExplainer`kan du använda SHAP, annars
    2. Om det är en DNN-modell använder du SHAP, `DeepExplainer`annars
    3. Om det är en linjär modell, använd SHAP `LinearExplainer`, annars
    3. Behandla den som en svart låda modell och tillämpa SHAP`KernelExplainer`


`TabularExplainer`har också gjort betydande funktioner och prestandaförbättringar över den direkta SHAP Explainers:

* **Summering av initieringsdatauppsättningen**. I de fall där förklaringshastighet är viktigast sammanfattar vi initieringsdatauppsättningen och genererar en liten uppsättning representativa exempel, vilket snabbar upp både global och lokal förklaring.
* **Provtagning av utvärderingsdatauppsättningen**. Om användaren passerar i en stor uppsättning utvärderingsexempel men faktiskt inte behöver alla utvärderas, kan samplingsparametern ställas in på true för att påskynda den globala förklaringen.

Följande diagram visar den aktuella strukturen för direkta och meta explainers.

[![Maskininlärningsretolkbarhetsarkitektur](./media/how-to-machine-learning-interpretability/interpretability-architecture.png)](./media/how-to-machine-learning-interpretability/interpretability-architecture.png#lightbox)


### <a name="models-supported"></a>Modeller som stöds

Alla modeller som tränas på `numpy.array`datauppsättningar `pandas.DataFrame`i Python , , `iml.datatypes.DenseData`eller `scipy.sparse.csr_matrix` format stöds av SDK:s tolkningspaket. `explain`

Förklaringsfunktionerna accepterar både modeller och rörledningar som indata. Om en modell tillhandahålls måste modellen implementera `predict` `predict_proba` förutsägelsefunktionen eller som överensstämmer med Scikit-konventionen. Om en pipeline (namnet på pipeline-skriptet) tillhandahålls förutsätter förklaringsfunktionen att det pipeline-skript som körs returnerar en förutsägelse. Vi stödjer modeller som tränas via PyTorch, TensorFlow och Keras deep learning-ramverk.

### <a name="local-and-remote-compute-target"></a>Lokalt och avlägset beräkningsmål

Paketet `explain` är utformat för att fungera med både lokala och fjärranslutna beräkningsmål. Om körs lokalt kommer SDK-funktionerna inte att kontakta några Azure-tjänster. Du kan fjärrköra förklaring på Azure Machine Learning Compute och logga förklaringsinformationen i Azure Machine Learning Run History Services. När den här informationen har loggats är rapporter och visualiseringar från förklaringen lätt tillgängliga på Azure Machine Learning-arbetsytan för användaranalys.


## <a name="next-steps"></a>Nästa steg

Se [hur du](how-to-machine-learning-interpretability-aml.md) aktiverar tolkningsbarhet för modeller som tränar både lokalt och på Azure Machine Learning fjärrberäkningsresurser. Mer information finns i [exempeldatorerna](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model) för ytterligare scenarier.
