---
title: Vad är automatiserad ML / AutoML
titleSuffix: Azure Machine Learning
description: Lär dig hur Azure Machine Learning automatiskt kan välja en algoritm åt dig och generera en modell från den för att spara tid genom att använda de parametrar och kriterier som du anger för att välja den bästa algoritmen för din modell.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: cartacioS
ms.author: sacartac
ms.date: 02/28/2020
ms.openlocfilehash: 095561f02fdeff6688b78d69cc1becc4ee0f8901
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115217"
---
# <a name="what-is-automated-machine-learning"></a>Vad är automatiserad maskininlärning?

Automatiserad maskininlärning, även kallad automatiserad ML, är processen att automatisera tidskrävande, iterativa uppgifter maskininlärning modellutveckling. Det gör det möjligt för datavetare, analytiker och utvecklare att bygga ML-modeller med hög skala, effektivitet och produktivitet samtidigt som modellkvaliteten upprätthålls. Automated ML bygger på ett genombrott från vår [Microsoft Research-avdelning.](https://arxiv.org/abs/1705.05355)

Traditionell maskininlärningsmodellutveckling är resurskrävande, vilket kräver betydande domänkunskap och tid för att producera och jämföra dussintals modeller. Med automatiserad maskininlärning påskyndar du den tid det tar att få produktionsklara ML-modeller med stor lätthet och effektivitet.

## <a name="when-to-use-automated-ml"></a>När ska automatiserad ML användas

Använd automatiserad ML när du vill att Azure Machine Learning ska träna och finjustera en modell åt dig med hjälp av det målmått du anger. Automatiserad ML demokratiserar maskininlärningsmodellens utvecklingsprocess och ger användarna, oavsett expertis inom datavetenskap, möjlighet att identifiera en end-to-end machine learning pipeline för eventuella problem.

Dataforskare, analytiker och utvecklare i olika branscher kan använda automatiserad ML för att:

+ Implementera maskininlärningslösningar utan omfattande programmeringskunskaper
+ Spara tid och resurser
+ Utnyttja bästa praxis för datavetenskap
+ Ge flexibel problemlösning

I följande tabell visas vanliga automatiska ML-användningsfall. 

Klassificering| Prognostisering av tidsserier | Regression
---|---|---
[Upptäckt av bedrägerier](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb)|[Försäljningsprognoser](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb)|[Förutsägelse av CPU-prestanda](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression-hardware-performance-explanation-and-featurization/auto-ml-regression-hardware-performance-explanation-and-featurization.ipynb)
|[Marknadsföring Förutsägelse](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)|[Efterfrågeprognostisering](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)|
|[Klassificering av diskussionsgruppsdata](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-text-dnn/auto-ml-classification-text-dnn.ipynb)|[Prognos för dryckesproduktion](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb)|

## <a name="design-automated-ml-experiments"></a>Designa automatiserade ML-experiment

Med Hjälp av **Azure Machine Learning**kan du utforma och köra dina automatiserade ML-utbildningsexperiment med följande steg:

1. **Identifiera ml-problemet** som ska lösas: klassificering, prognoser eller regression

1. **Ange källan och formatet för de märkta träningsdata:** Numpy arrayer eller Pandas dataram

1. **Konfigurera beräkningsmålet för modellutbildning**, till exempel din [lokala dator, Azure Machine Learning Computes, fjärr-virtuella datorer eller Azure Databricks](how-to-set-up-training-targets.md).  Läs mer om automatiserad utbildning [på en fjärrresurs](how-to-auto-train-remote.md).

1. **Konfigurera de automatiska maskininlärningsparametrarna** som avgör hur många iterationer över olika modeller, hyperparameterinställningar, avancerad förbearbetning/medbearbetning och vilka mått som ska undersökas när du fastställer den bästa modellen.  Du kan konfigurera inställningarna för experiment för automatisk utbildning i [Azure Machine Learning studio](https://ml.azure.com)eller med [SDK](how-to-configure-auto-train.md). 

    [!INCLUDE [aml-applies-to-enterprise-sku](../../includes/aml-applies-to-enterprise-sku-inline.md)]

1. **Skicka in träningskörningen.**

## <a name="how-automated-ml-works"></a>Så här fungerar automatiserad ML

Under utbildningen skapar Azure Machine Learning ett antal parallella pipelines som provar olika algoritmer och parametrar. Tjänsten itererar genom ML algoritmer paras ihop med funktionsval, där varje iteration producerar en modell med en utbildning poäng. Ju högre poäng, desto bättre anses modellen "passa" dina data.  Det kommer att sluta när det träffar exit kriterier som definieras i experimentet. Följande diagram illustrerar den här processen. 

  ![Automatiserad maskininlärning](./media/concept-automated-ml/automl-concept-diagram2.png)


Du kan också granska den loggade körningsinformationen, som [innehåller mått](how-to-understand-automated-ml.md) som samlats in under körningen. Utbildningskörningen ger ett Serialiserat`.pkl` Python-objekt ( fil) som innehåller modellen och dataförbearbetningen.

Medan modellbyggnaden är automatiserad kan du också [lära dig hur viktiga eller relevanta funktioner är](how-to-configure-auto-train.md#explain) för de genererade modellerna.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Xc9t]

<a name="preprocess"></a>

## <a name="preprocessing"></a>Förbehandling

I varje automatiserat maskininlärningsexperiment förarbetas dina data med standardmetoderna och eventuellt genom avancerad förbearbetning.

> [!NOTE]
> Automatiserade förbearbetningssteg för maskininlärning (funktionsnormalisering, hantering av data som saknas, konvertering av text till numeriska osv.) blir en del av den underliggande modellen. När du använder modellen för förutsägelser tillämpas samma förbearbetningssteg som tillämpas under träningen automatiskt på dina indata.

### <a name="automatic-preprocessing-standard"></a>Automatisk förbehandling (standard)

I varje automatiserat maskininlärningsexperiment skalas eller normaliseras dina data automatiskt för att hjälpa algoritmer att fungera bra.  Under modellutbildningen tillämpas en av följande skalnings- eller normaliseringstekniker på varje modell.

|Skalning&nbsp;&&nbsp;normalisering| Beskrivning |
| ------------- | ------------- |
| [StandardScaleWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | Standardisera funktioner genom att ta bort medelvärdet och skalningen till enhetsavvikelse  |
| [MinMaxScalar (påminn)](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | Omvandlar funktioner genom att skala varje funktion efter den kolumnens lägsta och högsta  |
| [MaxAbsScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |Skala varje funktion efter dess högsta absoluta värde |
| [RobustScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) |Denna Scaler funktioner av deras quantile sortiment |
| [Pca](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) |Linjär dimensionalitetsminskning med hjälp av singularvärdesdedelning av data för att projicera den till ett lägre dimensionellt utrymme |
| [TrunkeradSVDWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |Denna transformator utför linjär dimensionalitetsminskning med hjälp av trunkerad singularvärdesdekomposit (SVD). I motsats till PCA, centrerar denna estimator inte data innan du beräknar singularvärdet nedbrytning, vilket innebär att det kan arbeta med scipy.sparse matriser effektivt |
| [SparseNormalizer](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | Varje prov (det vill säga varje rad i datamatrisen) med minst en komponent som inte är noll skalas om skalas om oberoende av andra prover så att dess norm (l1 eller l2) är lika med en |

### <a name="advanced-preprocessing-optional-featurization"></a>Avancerad förbearbetning: valfri featurization

Ytterligare avancerad förbearbetning och medsättning är också tillgängliga, till exempel dataskyddsräckor, kodning och transformeringar. [Läs mer om vad medsättning ingår](how-to-use-automated-ml-for-ml-models.md#featurization). Aktivera den här inställningen med:

+ Azure Machine Learning studio: Aktivera **automatisk featurization** i avsnittet **Visa ytterligare konfiguration** med dessa [steg](how-to-use-automated-ml-for-ml-models.md#create-and-run-experiment).

+ Python SDK: `"feauturization": 'auto' / 'off' / 'FeaturizationConfig'` Ange för [ `AutoMLConfig` klassen](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig). 

## <a name="classification--regression"></a>Klassificering & regression

Klassificering och regression är de vanligaste typerna av maskininlärningsuppgifter. Båda är typer av övervakad inlärning där modeller lär sig med hjälp av träningsdata och tillämpar dessa lärdomar på nya data. Azure Machine Learning erbjuder featurizations specifikt för dessa uppgifter, till exempel djup neural nätverkstext med hjälp av klassificering. Läs mer om [featurization alternativ](how-to-use-automated-ml-for-ml-models.md#featurization). 

Huvudsyftet med klassificeringsmodeller är att förutsäga vilka kategorier nya data som kommer att falla in baserat på lärdomar från dess utbildningsdata. Vanliga klassificeringsexempel är bedrägeriidentifiering, handskriftsigenkänning och objektidentifiering.  Läs mer och se ett exempel [på klassificering med automatiserad maskininlärning](tutorial-train-models-with-aml.md).

Skiljer sig från klassificeringen där förväntade utdatavärden är kategoriska, regressionsmodeller förutsäga numeriska utdatavärden baserat på oberoende prediktorer. I regression är målet att bidra till att fastställa förhållandet mellan dessa oberoende prediktorvariabler genom att uppskatta hur en variabel påverkar de andra. Till exempel bil pris baserat på funktioner som, gas körsträcka, säkerhetsbetyg, etc. Läs mer och se ett exempel [på regression med automatiserad maskininlärning](tutorial-auto-train-models.md).

## <a name="time-series-forecasting"></a>Prognostisering av tidsserier

Byggprognoser är en integrerad del av alla företag, oavsett om det är intäkter, lager, försäljning eller kundefterfrågan. Du kan använda automatiserad ML för att kombinera tekniker och metoder och få en rekommenderad, högkvalitativ tidsserieprognos.

Ett automatiserat tidsserieexperiment behandlas som ett multivariat regressionsproblem. Tidigare tidsserievärden är "pivoterade" för att bli ytterligare dimensioner för regressor tillsammans med andra prediktorer. Detta tillvägagångssätt, till skillnad från klassiska tidsseriemetoder, har en fördel av att naturligt införliva flera kontextuella variabler och deras förhållande till varandra under träning. Automatiserad ML lär sig en enda, men ofta internt grenade modell för alla objekt i datauppsättningen och förutsägelsehorisonterna. Mer data är således tillgängliga för att uppskatta modellparametrar och generalisering till osynliga serier blir möjligt.

Läs mer och se ett exempel på [automatiserad maskininlärning för tidsserieprognoser](how-to-auto-train-forecast.md). Eller se [anteckningsboken](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb) för energiförbrukning för detaljerade kodexempel på avancerad prognoskonfiguration, inklusive:

* semester upptäckt och featurization
* tidsserier och DNN-elever (Auto-ARIMA, Profet, ForecastTCN)
* många modeller stöder genom gruppering
* validering av korsvalidering av rullande ursprung
* konfigurerbara fördröjningar
* sammanlagt funktioner för rullande fönster

## <a name="ensemble-models"></a><a name="ensemble"></a>Ensemble modeller

Automatiserad maskininlärning stöder ensemblemodeller som är aktiverade som standard. Ensemble lärande förbättrar maskininlärning resultat och prediktiva prestanda genom att kombinera flera modeller i motsats till att använda enstaka modeller. Ensemble iterationer visas som de slutliga iterationer av din körning. Automatiserad maskininlärning använder både röstnings- och staplingsensemblemetoder för att kombinera modeller:

* **Röstning**: förutsäger baserat på det vägda genomsnittet av förväntade klasspronolikheter (för klassificeringsuppgifter) eller förväntade regressionsmål (för regressionsaktiviteter).
* **Stapling:** stapling kombinerar heterogena modeller och tränar en metamodell baserad på utdata från de enskilda modellerna. De aktuella standardmetamodellerna är LogisticRegression för klassificeringsuppgifter och ElasticNet för regressions-/prognosaktiviteter.

[Caruana ensemble urval algoritm](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf) med sorterad ensemble initiering används för att bestämma vilka modeller som ska användas i ensemblen. På en hög nivå initierar denna algoritm ensemblen med upp till fem modeller med de bästa individuella poängen, och verifierar att dessa modeller ligger inom 5% tröskel för bästa poäng för att undvika en dålig första ensemble. Sedan för varje ensemble iteration, en ny modell läggs till den befintliga ensemblen och den resulterande poängen beräknas. Om en ny modell förbättrade den befintliga ensemblen poäng, är ensemblen uppdateras för att inkludera den nya modellen.

Se [hur du](how-to-configure-auto-train.md#ensemble) ändrar standardinställningarna för ensemble i automatiserad maskininlärning.

## <a name="use-with-onnx"></a>Använd med ONNX

Med Azure Machine Learning kan du använda automatiserad ML för att skapa en Python-modell och få den konverterad till ONNX-formatet. När modellerna är i ONNX-format kan de köras på en mängd olika plattformar och enheter. Läs mer om [att accelerera ML-modeller med ONNX](concept-onnx.md).

Se hur du konverterar till ONNX-format [i det här exemplet på Den bärbara Jupyter.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb) Lär dig vilka algoritmer som [stöds i ONNX](how-to-configure-auto-train.md#select-your-experiment-type).

ONNX-körningen stöder också C#, så du kan använda den modell som skapats automatiskt i dina C#-appar utan att behöva ändra om eller någon av de nätverksdynningar som REST-slutpunkter introducerar. Läs mer om [att dra in ONNX-modeller med ONNX-körningen C# API](https://github.com/Microsoft/onnxruntime/blob/master/docs/CSharp_API.md). 

## <a name="automated-ml-in-azure-machine-learning"></a>Automatiserat ML i Azure Machine Learning

Azure Machine Learning erbjuder två upplevelser för att arbeta med automatiserad ML

* För kod erfarna kunder, [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 

* För kunder med begränsad/ingen kodupplevelse kan Azure Machine Learning studio på[https://ml.azure.com](https://ml.azure.com/)  

Följande sammanfattar de automatiserade ML-funktioner på hög nivå som stöds i varje upplevelse.

<a name="parity"></a>

### <a name="experiment-settings"></a>Inställningar för experiment 

Med följande inställningar kan du konfigurera det automatiska ML-experimentet. 

| | Python SDK| Studio
----|:----:|:----:
Dela upp data i tåg-/valideringsuppsättningar| ✓|✓
Stöder ML-uppgifter: klassificering, regression och prognostisering| ✓| ✓
Optimerar baserat på primärt mått| ✓| ✓
Stöder AML-beräkning som beräkningsmål | ✓|✓
Konfigurera prognoshorisont, målfördröjningar & rullande fönster|✓|✓
Ange avslutningsvillkor |✓|✓ 
Ange samtidiga iterationer| ✓|✓
Släpp kolumner| ✓|✓
Blockera algoritmer|✓|✓
Korsvalidering |✓|✓
Stöder utbildning i Azure Databricks-kluster| ✓|
Visa konstruerade funktionsnamn|✓|
Sammanfattning av medarisering| ✓|
Semester med|✓|
Verbositetsnivå för loggfiler| ✓|

### <a name="model-settings"></a>Modellinställningar

Dessa inställningar kan tillämpas på den bästa modellen som ett resultat av ditt automatiserade ML-experiment.

||Python SDK|Studio
----|:----:|:----:
Bästa modellregistrering| ✓|✓
Bästa modelldistribution| ✓| ✓
Bästa modellen förklaringsförmåga| ✓|✓
Aktivera röstningsensemblen & stackensemblens modeller| ✓|✓
Visa bästa modell baserat på icke-primärt mått|✓|
Aktivera/inaktivera ONNX-modellkompatibilitet|✓|
Testa modellen | ✓| |

### <a name="run-control-settings"></a>Kör kontrollinställningar

Med de här inställningarna kan du granska och styra experimentkörningarna och dess underordnade körningar. 

||Python SDK| Studio
----|:----:|:----:
Kör sammanfattningstabell| ✓|✓
Avbryt körning| ✓|✓
Avbryt underordnad körning| ✓| ✓
Få skyddsräcken| ✓|✓
Pausa körning| ✓| 
Återuppta körningen| ✓| 

## <a name="next-steps"></a>Nästa steg

Se exempel och lär dig hur du bygger modeller med automatiserad maskininlärning:

+ Följ [självstudien: Träna automatiskt en regressionsmodell med Azure Machine Learning](tutorial-auto-train-models.md)

+ Konfigurera inställningarna för automatiska träningsexperiment:
  + I Azure Machine Learning studio [använder du dessa steg](how-to-use-automated-ml-for-ml-models.md).
  + Använd dessa [steg](how-to-configure-auto-train.md)med Python SDK .

+ Lär dig hur du tränar automatiskt med hjälp av tidsseriedata, [gör så här](how-to-auto-train-forecast.md).

+ Prova [Jupyter notebook-exempel för automatiserad maskininlärning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/)

* Automatiserad ML finns även i andra Microsoft-lösningar, till exempel [ML.NET,](https://docs.microsoft.com/dotnet/machine-learning/automl-overview) [HDInsight,](../hdinsight/spark/apache-spark-run-machine-learning-automl.md) [Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-automated) och [SQL Server](https://cloudblogs.microsoft.com/sqlserver/2019/01/09/how-to-automate-machine-learning-on-sql-server-2019-big-data-clusters/)
