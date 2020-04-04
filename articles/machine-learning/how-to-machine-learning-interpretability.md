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
ms.reviewer: Luis.Quintanilla
ms.date: 04/02/2020
ms.openlocfilehash: fcb837af85a54102e8c9eafc33249af9dba6b5ce
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631399"
---
# <a name="model-interpretability-in-azure-machine-learning"></a>Modelltolkbarhet i Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

## <a name="overview-of-model-interpretability"></a>Översikt över modellens tolkningsförmåga

Tolkningsförmåga är avgörande för både dataforskare, revisorer och beslutsfattare för att säkerställa efterlevnad av företagets policyer, branschstandarder och myndighetsregler:

+ Dataforskare behöver förmågan att förklara sina modeller för chefer och intressenter, så att de kan förstå värdet och riktigheten i sina resultat. De kräver också tolkningsbarhet för att felsöka sina modeller och fatta välgrundade beslut om hur de kan förbättras. 

+ Juridiska revisorer kräver verktyg för att validera modeller med avseende på regelefterlevnad och övervaka hur modellernas beslut påverkar människor. 

+ Beslutsfattare i näringslivet behöver sinnesro genom att ha möjlighet att skapa insyn för slutanvändarna. Detta gör det möjligt för dem att tjäna och behålla förtroendet.


Att aktivera möjligheten att förklara en maskininlärningsmodell är viktigt under två huvudfaser av modellutveckling:
+ Under utbildningsfasen kan modelldesigners och utvärderare använda tolkningsresultat av en modell för att verifiera hypoteser och bygga förtroende hos intressenter. De använder också insikterna i modellen för felsökning, validering av modellbeteende matchar deras mål och för att kontrollera om modellen är orättvis eller obetydliga funktioner.

+ Under inferencing fasen, som har öppenhet kring utplacerade modeller ger chefer att förstå "när de används" hur modellen fungerar och hur dess beslut behandlar och påverkar människor i verkliga livet. 

## <a name="interpretability-with-azure-machine-learning"></a>Tolkningsbarhet med Azure Machine Learning

Tolkningsklasserna görs tillgängliga via flera SDK-paket: (Lär dig hur du [installerar SDK-paket för Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py))

* `azureml.interpret`, huvudpaketet, som innehåller funktioner som stöds av Microsoft.

* `azureml.contrib.interpret`, förhandsgranskning och experimentella funktioner som du kan prova.

* `azureml.train.automl.automlexplainer`paket för tolkning av automatiserade maskininlärningsmodeller.

Använd `pip install azureml-interpret` `pip install azureml-interpret-contrib` och för allmänt `pip install azureml-interpret-contrib` bruk, och för AutoML-användning för att hämta tolkningspaketen.


> [!IMPORTANT]
> Innehåll i `contrib` namnområdet stöds inte fullt ut. När de experimentella funktionerna blir mogna, kommer de gradvis att flyttas till huvudnamnområdet.
.



## <a name="how-to-interpret-your-model"></a>Så här tolkar du din modell

Med hjälp av klasser och metoder i SDK kan du:
+ Förklara modellprediktion genom att generera funktionsbetensvärden för hela modellen och/eller enskilda datapunkter. 
+ Uppnå modelltolkbarhet på verkliga datauppsättningar i stor skala, under utbildning och slutledning.
+ Använda en interaktiv instrumentpanel för visualisering för att identifiera mönster i data och förklaringar vid träningstid


I maskininlärning är **funktioner** de datafält som används för att förutsäga en måldatapunkt. För att till exempel förutsäga kreditrisk kan datafält för ålder, kontostorlek och kontoålder användas. I det här fallet är ålder, kontostorlek och kontoålder **funktioner**. Funktionsvikt talar om för dig hur varje datafält påverkade modellens förutsägelser. Ålder kan till exempel användas kraftigt i förutsägelsen medan kontostorlek och ålder inte påverkar förutsägelsevärdena nämnvärt. Den här processen gör det möjligt för dataforskare att förklara resulterande förutsägelser, så att intressenterna får insyn i vilka funktioner som är viktigast i modellen.

Lär dig mer om tolkningstekniker som stöds, modeller för maskininlärning som stöds och kör miljöer som stöds här.


## <a name="supported-interpretability-techniques"></a>Tolkningstekniker som stöds

 `azureml-interpret`använder de tolkningstekniker som utvecklats i [Interpret-Community](https://github.com/interpretml/interpret-community/), ett pythonpaket med öppen källkod för att träna tolkningsbara modeller och hjälpa till att förklara BLACKBOX AI-system. [Interpret-Community](https://github.com/interpretml/interpret-community/) fungerar som värd för denna SDK:s explainers som stöds och stöder för närvarande följande tolkningstekniker:

|Tolkningsteknik|Beskrivning|Typ|
|--|--|--------------------|
|1. SHAP Träd Explainer| [SHAP:](https://github.com/slundberg/shap)s träd explainer, som fokuserar på polynom tid snabb SHAP värde uppskattning algoritm som är specifik för **träd och ensembler av träd**.|Modellspecifik|
|2. SHAP Djup explainer| Baserat på förklaringen från [SHAP](https://github.com/slundberg/shap), Deep Explainer "är en höghastighets approximation algoritm för SHAP värden i djupinlärning modeller som bygger på en anslutning med DeepLIFT beskrivs i [SHAP NIPS papper](https://papers.nips.cc/paper/7062-a-unified-approach-to-interpreting-model-predictions). **TensorFlow-modeller** och **Keras-modeller** som använder TensorFlow-backend stöds (det finns också preliminärt stöd för PyTorch)".|Modellspecifik|
|3. SHAP linjär explainer| [SHAP's](https://github.com/slundberg/shap)Linjär explainer beräknar SHAP-värden för en **linjär modell**, eventuellt redovisning av inter-feature korrelationer.|Modellspecifik|
|4. SHAP Kernel Explainer| [SHAP's](https://github.com/slundberg/shap)Kernel explainer använder en speciellt viktad lokal linjär regression för att uppskatta SHAP-värden för **alla modeller**.|Modellagnostiker|
|5. Härma Explainer (Global Surrogat)| Mimic explainer är baserad på idén att utbilda [globala surrogatmodeller](https://christophm.github.io/interpretable-ml-book/global.html) för att efterlikna blackbox-modeller. En global surrogatmodell är en i sig tolkningsbar modell som är utbildad för att approximera förutsägelserna för **en svart låda modell** så exakt som möjligt. Dataforskare kan tolka surrogatmodellen för att dra slutsatser om den svarta lådan modellen. Du kan använda en av följande tolkningsbara modeller som din surrogatmodell: LightGBM (LGBMExplainableModel), Linjär regression (LinearExplainableModel), Stokastisk Gradient Descent förklaras modell (SGDExplainableModel) och Decision Tree (DecisionTreeExplainableModel).|Modellagnostiker|
|6. Permutation Feature Betydelse Explainer (PFI)| Permutationsfunktionsbetydelse är en teknik som används för att förklara klassificerings- och regressionsmodeller som är inspirerade av [Breimans Random Forests-papper](https://www.stat.berkeley.edu/~breiman/randomforest2001.pdf) (se avsnitt 10). På en hög nivå är det sätt det fungerar genom att slumpmässigt blanda data en funktion i taget för hela datauppsättningen och beräkna hur mycket prestandamåttet av intresse ändras. Ju större förändring, desto viktigare är den funktionen. PFI kan förklara det övergripande beteendet hos **alla underliggande modeller** men förklarar inte enskilda förutsägelser. |Modellagnostiker|




Förutom de tolkningstekniker som beskrivs ovan stöder vi `TabularExplainer`en annan [SHAP-baserad explainer](https://github.com/slundberg/shap), kallad . Beroende på modell `TabularExplainer` använder du en av shap-explainers som stöds:

* TreeExplainer för alla trädbaserade modeller
* DeepExplainer för DNN-modeller
* Linjärutförsening för linjära modeller
* KernelExplainer för alla andra modeller

`TabularExplainer`har också gjort betydande funktioner och prestandaförbättringar över den direkta SHAP Explainers:

* **Summering av initieringsdatauppsättningen**. I de fall där förklaringshastighet är viktigast sammanfattar vi initieringsdatauppsättningen och genererar en liten uppsättning representativa prover, vilket snabbar upp genereringen av övergripande och individuella funktionsbetensviktsvärden.
* **Provtagning av utvärderingsdatauppsättningen**. Om användaren passerar i en stor uppsättning utvärderingsexempel men faktiskt inte behöver alla utvärderas, kan samplingsparametern ställas in på true för att påskynda beräkningen av övergripande modellförklaringar.

Följande diagram visar den aktuella strukturen för explainers som stöds.

[![Maskininlärningsretolkbarhetsarkitektur](./media/how-to-machine-learning-interpretability/interpretability-architecture.png)](./media/how-to-machine-learning-interpretability/interpretability-architecture.png#lightbox)


## <a name="supported-machine-learning-models"></a>Maskininlärningsmodeller som stöds

Paketet `azureml.interpret` med SDK stöder modeller som tränas med följande datauppsättningsformat:
- `numpy.array`
- `pandas.DataFrame`
- `iml.datatypes.DenseData`
- `scipy.sparse.csr_matrix`

Förklaringsfunktionerna accepterar både modeller och rörledningar som indata. Om en modell tillhandahålls måste modellen implementera `predict` `predict_proba` förutsägelsefunktionen eller som överensstämmer med Scikit-konventionen. Om din modell inte stöder detta kan du slå in din modell `predict` `predict_proba` i en funktion som genererar samma resultat som eller i Scikit och använda den omslagsfunktionen med den valda explainern. Om en pipeline tillhandahålls förutsätter förklaringsfunktionen att pipeline-skriptet som körs returnerar en förutsägelse. Med hjälp av `azureml.interpret` denna förpackningsteknik kan stödja modeller som tränas via PyTorch, TensorFlow och Keras deep learning-ramverk samt klassiska maskininlärningsmodeller.

## <a name="local-and-remote-compute-target"></a>Lokalt och avlägset beräkningsmål

Paketet `azureml.interpret` är utformat för att fungera med både lokala och fjärranslutna beräkningsmål. Om körs lokalt kommer SDK-funktionerna inte att kontakta några Azure-tjänster. 

Du kan fjärrköra förklaring på Azure Machine Learning Compute och logga förklaringsinformationen i Azure Machine Learning Run History Service. När den här informationen har loggats är rapporter och visualiseringar från förklaringen lätt tillgängliga i Azure Machine Learning Studio för användaranalys.


## <a name="next-steps"></a>Nästa steg

Se [hur du](how-to-machine-learning-interpretability-aml.md) aktiverar tolkningsbarhet för modeller som tränar både lokalt och på Azure Machine Learning fjärrberäkningsresurser. Mer information finns i [exempeldatorerna](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model) för ytterligare scenarier.
