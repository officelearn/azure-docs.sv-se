---
title: Vad är automatiserad ML/AutoML
titleSuffix: Azure Machine Learning
description: Lär dig hur Azure Machine Learning automatiskt kan välja en algoritm åt dig och generera en modell från den för att spara tid genom att använda de parametrar och kriterier som du anger för att välja den bästa algoritmen för din modell.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: cartacioS
ms.author: sacartac
ms.date: 11/04/2019
ms.openlocfilehash: f7a2e78ed2b1de770f7a60f1312e069dc1757cb6
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2020
ms.locfileid: "77191194"
---
# <a name="what-is-automated-machine-learning"></a>Vad är automatisk maskininlärning?

Automatisk maskin inlärning, som även kallas automatiserad ML, är en process för att automatisera tids krävande, repetitiva uppgifter om utveckling av Machine Learning-modeller. Det gör det möjligt för data experter, analytiker och utvecklare att bygga ML-modeller med hög skalbarhet, effektivitet och produktivitet samtidigt som modell kvaliteten försämras. Automatisk ML baseras på en Ban brytande från vår [Microsoft Research-avdelning](https://arxiv.org/abs/1705.05355).

Traditionell miljö utveckling för maskin inlärning är resurs intensiv, vilket kräver betydande domän kunskap och tid för att producera och jämföra dussin tals modeller. Använd automatisk ML när du vill Azure Machine Learning för att träna och finjustera en modell för att använda det målmått du anger. Tjänsten itererar sedan igenom ML-algoritmer med funktions val där varje iteration genererar en modell med en utbildnings poäng. Ju högre poäng, desto bättre blir modellen att "passa" dina data.

Med automatisk maskin inlärning kan du påskynda den tid det tar att få produktions klara ML-modeller med mycket enkelt och effektivt.

## <a name="when-to-use-automated-ml"></a>När du ska använda automatisk ML

Automatiserad ML demokratiserar sakernas utvecklings processen för maskin inlärning och ger användarna möjlighet att, oavsett data vetenskaps expert, identifiera en pipeline för maskin inlärning från slut punkt till slut punkt för alla problem.

Data experter, analytiker och utvecklare i olika branscher kan använda automatisk ML för att:

+ Implementera maskin inlärnings lösningar utan omfattande programmerings kunskap
+ Spara tid och resurser
+ Utnyttja metod tips för data vetenskap
+ Ge smidig problemlösning

I följande tabell visas vanliga automatiserade ML-användnings fall. 

Klassificering| Prognosticering för tids serier | Regression
---|---|---
[Bedrägeri identifiering](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb)|[Försäljnings prognoser](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb)|[Prestanda förutsägelse för CPU](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression-hardware-performance-explanation-and-featurization/auto-ml-regression-hardware-performance-explanation-and-featurization.ipynb)
|[Marknadsförings förutsägelse](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)|[Prognoser för efter frågan](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)|
|[Data klassificering för diskussions grupper](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-text-dnn/auto-ml-classification-text-dnn.ipynb)|[Produkt prognos för dryck](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb)|

## <a name="how-automated-ml-works"></a>Så här fungerar automatisk ML

Med hjälp av **Azure Machine Learning**kan du utforma och köra dina AUTOMATISERAde ml-experiment med följande steg:

1. **Identifiera det ml-problem** som ska lösas: klassificering, Prognosticering eller regression

1. **Ange källa och format för etiketten utbildnings data**: numpy-matriser eller Pandas-dataframe

1. **Konfigurera beräknings målet för modell träning**, till exempel din [lokala dator, Azure Machine Learning beräkningar, fjärranslutna virtuella datorer eller Azure Databricks](how-to-set-up-training-targets.md).  Lär dig mer om automatisk utbildning [på en fjär resurs](how-to-auto-train-remote.md).

1. **Konfigurera de automatiserade Machine Learning-parametrarna** som avgör hur många iterationer över olika modeller, inställningar för funktionalisering, avancerade förbearbetnings-/och vilka mått som ska visas när du bestämmer den bästa modellen.  Du kan konfigurera inställningarna för automatisk inlärnings experiment i [Azure Machine Learning Studio](https://ml.azure.com)eller [med SDK](how-to-configure-auto-train.md). 

    [!INCLUDE [aml-applies-to-enterprise-sku](../../includes/aml-applies-to-enterprise-sku-inline.md)]

1. **Skicka in utbildnings körningen.**

  ![Automatisk maskin inlärning](./media/concept-automated-ml/automl-concept-diagram2.png)

Under utbildningen skapar Azure Machine Learning ett antal parallella pipelines som testar olika algoritmer och parametrar. Det stoppas när det träffar de slut kriterier som definierats i experimentet.

Du kan också kontrol lera den loggade körnings informationen, som [innehåller mått](how-to-understand-automated-ml.md) som samlats in under körningen. Inlärnings körningen skapar ett serialiserat python-objekt (`.pkl` fil) som innehåller modellen och data förbearbetningen.

När modell byggnaden automatiseras, kan du också [lära dig hur viktiga eller relevanta funktioner är](how-to-configure-auto-train.md#explain) i de genererade modellerna.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Xc9t]

<a name="preprocess"></a>

## <a name="preprocessing"></a>Förbearbeta

I varje automatiserad maskin inlärnings experiment förbehandlas dina data med hjälp av standard metoderna och kan du välja genom avancerad förbearbetning.

> [!NOTE]
> Automatiserad bearbetning av Machine Learning för bearbetning (funktions normalisering, hantering av saknade data, konvertering av text till tal osv.) blir en del av den underliggande modellen. När du använder modellen för förutsägelser tillämpas samma för bearbetnings steg som tillämpas på dina indata-data automatiskt.

### <a name="automatic-preprocessing-standard"></a>Automatisk för bearbetning (standard)

I varje automatiserad maskin inlärnings experiment skalas dina data automatiskt eller normaliseras för att hjälpa algoritmerna att fungera bra.  I modell utbildningen används en av följande skalnings-eller normaliserings tekniker för varje modell.

|Skala&nbsp;&&nbsp;normalisering| Beskrivning |
| ------------- | ------------- |
| [StandardScaleWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | Standardisera funktioner genom att ta bort medelvärdet och skalan till enhets avvikelse  |
| [MinMaxScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | Transformerar funktioner genom att skala varje funktion efter minsta och högsta värde för kolumnen  |
| [MaxAbsScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |Skala varje funktion efter dess maximala absoluta värde |
| [RobustScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) |De här skalnings funktionerna efter deras quantile-intervall |
| [PCA](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) |Linjär linjär reducering med enkel värdes nedbrytning av data för att projicera den till ett lägre dimensionellt utrymme |
| [TruncatedSVDWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |Den här transformeringen utför linjär minskning genom trunkering av sammansatta värde diskompositioner (SVD). I motsats till PCA centrerar denna uppskattning inte data innan du beräknar ett uppdelnings värde för en enkel värde, vilket innebär att det fungerar med scipy. glesa matriser effektivt |
| [SparseNormalizer](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | Varje exempel (det vill säga varje rad i data matrisen) med minst en komponent som inte är noll skalas om oberoende av andra prover så att dess norm (L1 eller L2) är lika med ett |

### <a name="advanced-preprocessing-optional-featurization"></a>Avancerad för bearbetning: valfria funktionalisering

Ytterligare avancerade för bearbetnings-och funktionalisering är också tillgängliga, till exempel data guardrails, kodning och transformeringar. [Läs mer om vad funktionalisering ingår](how-to-create-portal-experiments.md#featurization). Aktivera den här inställningen med:

+ Azure Machine Learning Studio: aktivera **Automatisk funktionalisering** i avsnittet **Visa ytterligare konfiguration** [med de här stegen](how-to-create-portal-experiments.md#create-and-run-experiment).

+ Python SDK: Ange `"feauturization": 'auto' / 'off' / 'FeaturizationConfig'` för [`AutoMLConfig`-klassen](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig). 

## <a name="prevent-over-fitting"></a>Förhindra överanpassning

Överanpassning i maskin inlärning sker när en modell passar inlärnings data för bra, och det kan leda till att det inte är korrekt förutsägelse på osett test data. Med andra ord har modellen helt enkelt bevisat vissa mönster och brus i tränings data, men är inte tillräckligt flexibel för att göra förutsägelser på verkliga data. I de flesta egregious-fall förutsätter en överordnad modell att de kombinationer av funktions värden som visas under utbildningen alltid ger exakt samma utdata för målet. 

Det bästa sättet att förhindra överanpassning är att följa ML Best-Practices, inklusive:

* Använda fler utbildnings data och ta bort statistisk kompensation
* Förhindra mål läckage
* Använda färre funktioner
* **Regulariseringshastigheten och dess parameter optimering**
* **Komplexitets begränsningar för modeller**
* **Kors validering**

I samband med automatisk ML är de första tre objekten ovan de **bästa metoderna som du implementerar**. De tre sista objekten med fet stil är **bästa metoder för automatisk ml-implementering** som standard för att skydda mot överanpassning. I andra inställningar än automatiserade ML är alla sex bästa metoder följande för att undvika överanpassnings modeller.

### <a name="best-practices-you-implement"></a>Metod tips som du implementerar

Att använda **mer data** är det enklaste och bästa möjliga sättet att förhindra överanpassning, och som en extra bonus ökar precisionen ofta. När du använder mer data blir det svårare för modellen att memorera exakta mönster, och det tvingas att uppnå lösningar som är mer flexibla för att hantera fler villkor. Det är också viktigt att känna igen **statistisk kompensation**för att säkerställa att dina utbildnings data inte innehåller isolerade mönster som inte finns i real tids förutsägelse data. Det här scenariot kan vara svårt att lösa eftersom det kanske inte finns överanvändning mellan dina tåg-och test uppsättningar, men det kan finnas överanvändning i jämförelse med Live test-data.

Mål läckage är ett liknande problem, där du kanske inte ser överanvändning mellan träna/test-uppsättningar, men i stället visas den i förutsägelse tiden. Mål läckage uppstår när din modell "fuskar" under utbildningen genom att ha åtkomst till data som den inte bör ha vid förutsägelse tiden. Om ditt problem till exempel är att förutsäga på måndagen är ett råvaru pris på fredag, men en av dina funktioner av misstag som innehåller data från torsdagar, som skulle vara data modellen kommer inte att ha vid förutsägelse, eftersom den inte kan se i framtiden. Mål läckage är ett enkelt misstag att sakna, men karakteriseras ofta av onormalt hög precision för ditt problem. Om du försöker förutsäga börs priset och tränat en modell med 95% noggrannhet, finns det troligen mål som läcker i dina funktioner.

Att ta bort funktioner kan också bidra till överanpassning genom att förhindra att modellen har för många fält att använda för att komma ihåg vissa mönster, vilket gör att det blir mer flexibelt. Det kan vara svårt att mäta kvantitativt, men om du kan ta bort funktioner och behålla samma precision har du förmodligen gjort modellen mer flexibel och minskat risken för överanpassning.

### <a name="best-practices-automated-ml-implements"></a>Best-Practices med automatisk ML-implementering

Regulariseringshastigheten är en process för att minimera en kostnads funktion för att beivra komplexa och över-anpassade modeller. Det finns olika typer av regulariseringshastigheten-funktioner, men i allmänhet tar de med sig en koefficient för modell effektiv storlek, varians och komplexitet. Med automatisk ML används L1 (lasso), L2 (Ridge) och ElasticNet (L1 och L2 samtidigt) i olika kombinationer med olika modell inställningar för en modell som styr överanpassningen. I enkla termer varierar automatiserade ML till hur mycket en modell är reglerad och väljer det bästa resultatet.

Med automatisk ML implementeras även explicita begränsningar för modell komplexitet för att förhindra överanpassning. I de flesta fall är den här implementeringen särskilt för besluts träd eller skogs-algoritmer, där det enskilda trädets max djup är begränsat och det totala antalet träd som används i skogs-eller Ensemble-teknikerna är begränsade.

Kors validering (ka) är en process där du tar många del mängder av dina fullständiga tränings data och tränar en modell på varje delmängd. Idén är att en modell kan få "fram" och ha stor precision med en delmängd, men genom att använda många del uppsättningar kan modellen inte uppnå denna höga noggrannhet varje gång. När du gör CV anger du en data uppsättning för en verifierings spärr, anger dina CV-vikningar (antal del mängder) och automatiserade ML tränar din modell och justerar de båda parametrarna för att minimera fel i validerings uppsättningen. En ka-vikning kan vara överdrivet, men genom att använda många av dem minskar sannolikheten att den slutliga modellen är överdrivet. Kompromissen är att ka resulterar i längre inlärnings tider och därmed högre kostnad, eftersom i stället för att träna en modell en gång, träna den en gång för varje *n* CV-deluppsättning.

> [!NOTE]
> Kors validering är inte aktiverat som standard. den måste konfigureras i automatiserade ML-inställningar. Men efter att ka har kon figurer ATS och en verifierings data uppsättning har angetts automatiseras processen.

### <a name="identifying-over-fitting"></a>Identifiera överanpassning

Tänk på följande utbildade modeller och deras motsvarande tåg-och test noggrannhet.

| Modell | Träna precision | Test noggrannhet |
|-------|----------------|---------------|
| A | 99,9 % | 95% |
| B | 87% | 87% |
| C | 99,9 % | 45% |

Med tanke på modell **A**, finns det en vanlig felbegrepp att om test precisionen för osett-data är lägre än den korrekta inlärningen är modellen överdrivet. Test precisionen bör dock alltid vara mindre än inlärnings precisionen och skillnaden för överanpassning jämfört med lämplig anpassning visas till *hur mycket* mindre exakt. 

När du jämför modeller **a** och **B**är modell **a** en bättre modell eftersom den har högre test precision, och även om test noggrannheten är något lägre vid 95% är det inte en stor skillnad som tyder på överanpassning. Du väljer inte modell **B** eftersom tågets och testernas noggrannhet är närmare tillsammans.

Modell **C** är ett tydligt Skift läge vid överanpassning. inlärnings precisionen är mycket hög men test noggrannheten är inte tillgänglig någonstans i närheten. Den här skillnaden är underkänd, men kommer från kännedom om ditt problem och dina data och vilka fel som kan uppstå. 

## <a name="time-series-forecasting"></a>Prognoser för tids serier

Att skapa prognoser är en viktig del av alla verksamheter, vare sig det gäller intäkter, inventering, försäljning eller kund efter frågan. Du kan använda automatiserad ML för att kombinera teknik och metoder och få en rekommenderad prognos för tids serier med hög kvalitet.

Ett automatiserat experiment med tids serier behandlas som ett multivarierad Regressions problem. Tidigare tids serie värden är "pivoterade" för att bli ytterligare dimensioner för modellerings regressor tillsammans med andra förutsägelser. Den här metoden, till skillnad från klassiska Time Series-metoder, har en fördel med att använda flera sammanhangsbaserade variabler och deras relation till varandra under utbildningen. Med automatisk ML får du en enda, men ofta ingrenad modell för alla objekt i data uppsättningen och förutsägelserna. Mer data är därför tillgängliga för att uppskatta modell parametrar och generalisering till osett-serien blir möjlig.

Läs mer och se ett exempel på [automatiserad maskin inlärning för tids serie prognoser](how-to-auto-train-forecast.md). Eller, se [antecknings boken för energi förbrukning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb) för detaljerade kod exempel på avancerad prognos konfiguration, inklusive:

* jul avkänning och funktionalisering
* Time-Series och DNN-elever (Auto-ARIMA, Prophet, ForecastTCN)
* många modeller stöder gruppering
* kors validering av rullande ursprung
* konfigurerbar lags
* mängd funktioner för rullande fönster

## <a name="ensemble"></a>Ensemble-modeller

Automatisk Machine Learning stöder Ensemble-modeller som är aktiverade som standard. Ensemble-inlärningen förbättrar maskin inlärnings resultatet och förutsäger prestanda genom att kombinera flera modeller i stället för att använda enskilda modeller. Ensemble-iterationerna visas som de slutliga iterationerna av din körning. Automatiserad Machine Learning använder både röstnings-och stack-Ensemble-metoder för att kombinera modeller:

* **Röstning**: förutsäger baserat på viktat medelvärde för förutsebara klass sannolikheter (för klassificerings aktiviteter) eller förutsägande Regressions mål (för Regressions aktiviteter).
* **Stackning**: stackning kombinerar heterogena modeller och tågen en meta-modell som baseras på utdata från de enskilda modellerna. De aktuella standard-meta-modellerna är LogisticRegression för klassificerings uppgifter och ElasticNet för Regressions-/prognos uppgifter.

[Caruana Ensemble-algoritmen](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf) med sorterad Ensemble-initiering används för att bestämma vilka modeller som ska användas i ensemblen. På hög nivå initierar den här algoritmen ensemblen med upp till fem modeller med de bästa enskilda poängen, och kontrollerar att dessa modeller är inom tröskelvärdet 5% för att undvika en dålig inledande ensemble. För varje Ensemble-iteration läggs en ny modell till i den befintliga ensemblen och den resulterande poängen beräknas. Om en ny modell förbättrar den befintliga Ensemble-poängen uppdateras ensemblen för att inkludera den nya modellen.

Se [hur du](how-to-configure-auto-train.md#ensemble) ändrar standardinställningar för ensemble i automatiserad maskin inlärning.

## <a name="imbalance"></a>Obalanserade data

Obalanserade data finns vanligt vis i data om klassificerings scenarier för maskin inlärning och refererar till data som innehåller ett oproportionerligt förhållande mellan observationerna i varje klass. Den här obalansen kan leda till en felaktigt uppfattad positiv effekt av en modells exakthet, eftersom indatan har en förskjutning mot en klass, vilket resulterar i att den tränade modellen imiterar den bias. 

Som en del av målet att förenkla arbets flödet för Machine Learning har automatiserad ML byggts med funktioner som hjälper dig att hantera obalanserade data, till exempel 

- En **Vikt kolumn**: automatisk ml stöder en viktad kolumn som indata, vilket gör att rader i data kan viktas upp eller ned, vilket kan göra en klass mer eller mindre viktig.

- Algoritmerna som används av automatiserad ML kan hantera obalans på upp till 20:1 korrekt, vilket innebär att den vanligaste klassen kan ha 20 gånger fler rader i data än den minsta gemensamma klassen.

### <a name="identify-models-with-imbalanced-data"></a>Identifiera modeller med obalanserade data

Eftersom klassificerings algoritmer ofta utvärderas efter precision, är det ett bra sätt att kontrol lera om en modells precisions Poäng är ett bra sätt att identifiera om den har påverkats av obalanserade data. Har den verkligen hög exakthet eller verkligen låg precision för vissa klasser?

Dessutom genererar automatiserade ML-körningar följande diagram automatiskt, som kan hjälpa dig att förstå rätthet i klassificeringen av din modell och identifiera modeller som kan påverkas av data som är fördelade.

Diagram| Beskrivning
---|---
[Förvirring mat ris](how-to-understand-automated-ml.md#confusion-matrix)| Utvärderar de korrekt klassificerade etiketterna mot de faktiska etiketterna i data. 
[Precision – återkalla](how-to-understand-automated-ml.md#precision-recall-chart)| Utvärderar förhållandet mellan korrekta etiketter mot förhållandet mellan hittade etikett instanser av data 
[ROC kurvor](how-to-understand-automated-ml.md#roc)| Utvärderar förhållandet mellan korrekta etiketter mot förhållandet falskt positiva etiketter.

### <a name="handle-imbalanced-data"></a>Hantera förbalanserade data 

Följande tekniker är ytterligare alternativ för att hantera sambalanserade data utanför automatiserad ML. 

- Omsampling till till och med klassen obalans, antingen genom att ta sampling av de mindre klasserna eller nedåt, och samtidigt ta bort de större klasserna. Dessa metoder kräver expertis för att bearbeta och analysera.

- Använd ett prestanda mått som fungerar bättre med obalanserade data. Till exempel är F1-poängen ett viktat medelvärde för precision och åter kallelse. Precision mäter en klassificerares exakta värde – låg precision visar ett stort antal falska positiva identifieringar--, samtidigt som åtgärdar mått för en klassificerares komplettahet – låg återkallning anger ett stort antal falska negativa värden. 

## <a name="use-with-onnx-in-c-apps"></a>Använda med ONNX i C# appar

Med Azure Machine Learning kan du använda automatisk ML för att bygga en python-modell och konvertera den till ONNX-format. ONNX runtime stöder C#, så du kan använda modellen som skapats automatiskt i dina C# appar utan att behöva koda om eller någon av nätverks fördröjningarna som rest-slutpunkter introducerar. Prova ett exempel på det här flödet [i den här Jupyter-anteckningsboken](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb).

## <a name="automated-ml-across-microsoft"></a>Automatiserad ML över Microsoft

Automatisk ML är också tillgängligt i andra Microsoft-lösningar som:

|Integreringar|Beskrivning|
|------------|-----------|
|[ML.NET](https://docs.microsoft.com/dotnet/machine-learning/automl-overview)|Automatisk modell val och träning i .NET-appar med Visual Studio och Visual Studio Code med ML.NET automatisk ML.|
|[HDInsight](../hdinsight/spark/apache-spark-run-machine-learning-automl.md)|Skala ut dina automatiserade ML-utbildnings jobb på spark i HDInsight-kluster parallellt.|
|[Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-automated)|Anropa maskin inlärnings modeller direkt i Power BI.|
|[SQL Server](https://cloudblogs.microsoft.com/sqlserver/2019/01/09/how-to-automate-machine-learning-on-sql-server-2019-big-data-clusters/)|Skapa nya maskin inlärnings modeller över dina data i SQL Server 2019 stora data kluster.|

## <a name="next-steps"></a>Nästa steg

Se exempel och lär dig hur du skapar modeller med hjälp av automatisk maskin inlärning:

+ Följ [själv studie kursen: träna en Regressions modell automatiskt med Azures automatiserade Machine Learning](tutorial-auto-train-models.md)

+ Konfigurera inställningarna för automatiskt utbildnings experiment:
  + [Använd de här stegen](how-to-create-portal-experiments.md)i Azure Machine Learning Studio.
  + [Använd de här stegen](how-to-configure-auto-train.md)med python SDK.

+ Lär dig hur du automatiskt tränar använda Time Series-data med hjälp av [de här stegen](how-to-auto-train-forecast.md).

+ Prova [Jupyter Notebook exempel för automatisk maskin inlärning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/)
