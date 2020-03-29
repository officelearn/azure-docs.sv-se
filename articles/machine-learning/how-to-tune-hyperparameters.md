---
title: Justera hyperparametrar för din modell
titleSuffix: Azure Machine Learning
description: Justera hyperparametrarna effektivt för din djupinlärnings- och maskininlärningsmodell med Hjälp av Azure Machine Learning. Du får lära dig hur du definierar parametersökutrymmet, anger ett primärt mått för att optimera och avslutar körs med dåligt resultat tidigt.
ms.author: swatig
author: swatig007
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: seodec18
ms.openlocfilehash: 4ca9cac00b9d00dcdbbd27f2fe769de2983c13ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75536647"
---
# <a name="tune-hyperparameters-for-your-model-with-azure-machine-learning"></a>Justera hyperparametrar för din modell med Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Justera hyperparametrarna effektivt för din modell med Azure Machine Learning.  Hyperparameterjustering innehåller följande steg:

* Definiera parametersökutrymmet
* Ange ett primärt mått för att optimera  
* Ange kriterier för tidig uppsägning för dåligt presterande körningar
* Allokera resurser för justering av hyperparameter
* Starta ett experiment med ovanstående konfiguration
* Visualisera träningskörningarna
* Välj den konfiguration som ger bäst prestanda för din modell

## <a name="what-are-hyperparameters"></a>Vad är hyperparametrar?

Hyperparametrar är justerbara parametrar som du väljer att träna en modell som styr själva träningsprocessen. Om du till exempel vill träna ett djupt neuralt nätverk bestämmer du antalet dolda lager i nätverket och antalet noder i varje lager innan du tränar modellen. Dessa värden förblir vanligtvis konstanta under träningsprocessen.

I djupinlärnings-/maskininlärningsscenarier beror modellprestanda i hög grad på de hyperparametervärden som valts. Målet med hyperparameterutforskning är att söka över olika hyperparameterkonfigurationer för att hitta en konfiguration som ger bäst prestanda. Vanligtvis är hyperparameter utforskningsprocessen mödosamt manuell, med tanke på att sökutrymmet är stort och utvärdering av varje konfiguration kan vara dyrt.

Med Azure Machine Learning kan du automatisera utforskning av hyperparameter på ett effektivt sätt, vilket sparar mycket tid och resurser. Du anger intervallet hyperparametervärden och ett maximalt antal utbildningskörningar. Systemet startar sedan automatiskt flera samtidiga körningar med olika parameterkonfigurationer och hittar den konfiguration som resulterar i bästa prestanda, mätt med det mått du väljer. Dåligt presterande utbildningskörningar avslutas automatiskt i förtid, vilket minskar slöseriet med beräkningsresurser. Dessa resurser används i stället för att utforska andra hyperparameterkonfigurationer.


## <a name="define-search-space"></a>Definiera sökutrymme

Justera hyperparametrarna automatiskt genom att utforska det värdeintervall som definierats för varje hyperparameter.

### <a name="types-of-hyperparameters"></a>Typer av hyperparametrar

Varje hyperparameter kan antingen vara diskret eller kontinuerlig och har en fördelning av värden som beskrivs av ett [parameteruttryck](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.parameter_expressions?view=azure-ml-py).

#### <a name="discrete-hyperparameters"></a>Diskreta hyperparametrar 

Diskreta hyperparametrar anges som `choice` en bland diskreta värden. `choice`kan vara:

* ett eller flera kommaavgränsade värden
* ett `range` objekt
* godtyckliga `list` objekt


```Python
    {
        "batch_size": choice(16, 32, 64, 128)
        "number_of_hidden_layers": choice(range(1,5))
    }
```

I det `batch_size` här fallet tar på sig ett av värdena [16, 32, 64, 128] och `number_of_hidden_layers` tar på sig ett av värdena [1, 2, 3, 4].

Avancerade diskreta hyperparametrar kan också anges med hjälp av en fördelning. Följande distributioner stöds:

* `quniform(low, high, q)`- Returnerar ett värde som runda (enhetlig (låg, hög) / q) * q
* `qloguniform(low, high, q)`- Returnerar ett värde som runda (exp(enhetlig(låg, hög)) / q) * q
* `qnormal(mu, sigma, q)`- Returnerar ett värde som runda (normal(mu, sigma) / q) * q
* `qlognormal(mu, sigma, q)`- Returnerar ett värde som round(exp(normal(mu, sigma)) / q) * q

#### <a name="continuous-hyperparameters"></a>Kontinuerliga hyperparametrar 

Kontinuerliga hyperparametrar anges som en fördelning över ett kontinuerligt värdeintervall. Distributioner som stöds inkluderar:

* `uniform(low, high)`- Returnerar ett värde jämnt fördelat mellan låg och hög
* `loguniform(low, high)`- Returnerar ett värde som dras enligt exp(uniform(låg, hög)) så att logaritmen för returvärdet fördelas jämnt fördelat
* `normal(mu, sigma)`- Returnerar ett verkligt värde som normalt fördelas med medelvärdet mu och standardavvikelse sigma
* `lognormal(mu, sigma)`- Returnerar ett värde som dras enligt exp(normal(mu, sigma)) så att logaritmen för returvärdet normalt fördelas

Ett exempel på en parameterutrymmesdefinition:

```Python
    {    
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1)
    }
```

Den här koden definierar ett `learning_rate` sökutrymme med två parametrar - och `keep_probability`. `learning_rate`har en normalfördelning med medelvärde 10 och en standardavvikelse på 3. `keep_probability`har en enhetlig fördelning med ett minimivärde på 0,05 och ett högsta värde på 0,1.

### <a name="sampling-the-hyperparameter-space"></a>Provtagning av hyperparameterutrymmet

Du kan också ange den parameterprovtagningsmetod som ska användas över hyperparameterutrymmesdefinitionen. Azure Machine Learning stöder slumpmässiga samplingar, rutnätsprovtagning och Bayesiansk sampling.

#### <a name="picking-a-sampling-method"></a>Plockning av en samplingsmetod

* Rutnätsprovtagning kan användas om hyperparameterutrymmet kan definieras som ett val bland diskreta värden och om du har tillräcklig budget för att uttömmande söka igenom alla värden i det definierade sökutrymmet. Dessutom kan man använda automatiserad tidig uppsägning av dåligt presterande körningar, vilket minskar slöseri med resurser.
* Slumpmässig provtagning gör att hyperparameterutrymmet kan inkludera både diskreta och kontinuerliga hyperparametrar. I praktiken ger det goda resultat för det mesta och tillåter också användning av automatiserad förtida uppsägning av dåligt presterande körningar. Vissa användare utför en första sökning med hjälp av slumpmässiga samplingar och sedan iterativt förfina sökutrymmet för att förbättra resultaten.
* Bayesiansk sampling utnyttjar kunskap om tidigare exempel när du väljer hyperparametervärden, och försöker effektivt förbättra det rapporterade primära måttet. Bayesiansk provtagning rekommenderas när du har tillräcklig budget för att utforska hyperparameterutrymmet - för bästa resultat med Bayesiansk sampling rekommenderar vi att du använder ett maximalt antal körningar som är större än eller lika med 20 gånger så många hyperparameter som justeras. Observera att Bayesiansk sampling för närvarande inte stöder någon policy för tidig uppsägning.

#### <a name="random-sampling"></a>Stickprov

Vid slumpmässigt urval väljs hyperparametervärden slumpmässigt från det definierade sökutrymmet. [Slumpmässigt urval](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.randomparametersampling?view=azure-ml-py) gör att sökutrymmet kan inkludera både diskreta och kontinuerliga hyperparametrar.

```Python
from azureml.train.hyperdrive import RandomParameterSampling
param_sampling = RandomParameterSampling( {
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```

#### <a name="grid-sampling"></a>Provtagning av rutnät

[Rutnätsprovtagning](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.gridparametersampling?view=azure-ml-py) utför en enkel rutnätssökning över alla möjliga värden i det definierade sökutrymmet. Den kan endast användas med hyperparametrar som anges med `choice`. Följande utrymme har till exempel totalt sex samplingar:

```Python
from azureml.train.hyperdrive import GridParameterSampling
param_sampling = GridParameterSampling( {
        "num_hidden_layers": choice(1, 2, 3),
        "batch_size": choice(16, 32)
    }
)
```

#### <a name="bayesian-sampling"></a>Bayesiansk sampling

[Bayesiansk sampling](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.bayesianparametersampling?view=azure-ml-py) baseras på bayesisk optimeringsalgoritm och gör intelligenta val på hyperparametervärdena att prova nästa. Det plockar exemplet baserat på hur de tidigare exemplen utförs, så att det nya exemplet förbättrar det rapporterade primära måttet.

När du använder Bayesiansk sampling påverkar antalet samtidiga körningar justeringsprocessens effektivitet. Vanligtvis kan ett mindre antal samtidiga körningar leda till bättre samplingskonvergens, eftersom den mindre graden av parallellism ökar antalet körningar som drar nytta av tidigare avslutade körningar.

Bayesiansk sampling `choice` `uniform`stöder `quniform` bara , och distributioner över sökutrymmet.

```Python
from azureml.train.hyperdrive import BayesianParameterSampling
param_sampling = BayesianParameterSampling( {
        "learning_rate": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```

> [!NOTE]
> Bayesiansk sampling stöder inte någon policy för tidig uppsägning (Se [Ange en policy för tidig uppsägning).](#specify-early-termination-policy) När du använder Bayesian `early_termination_policy = None`parameterprovtagning `early_termination_policy` ställer du in eller lämnar parametern.

<a name='specify-primary-metric-to-optimize'/>

## <a name="specify-primary-metric"></a>Ange primärt mått

Ange det [primära mått som](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.primarymetricgoal?view=azure-ml-py) du vill att hyperparameterjusteringsexperimentet ska optimeras. Varje träningskörning utvärderas för det primära måttet. Dåligt presterande körningar (där det primära måttet inte uppfyller de kriterier som anges av principen för förtida uppsägning) kommer att avslutas. Förutom det primära måttnamnet anger du också målet för optimeringen - om du vill maximera eller minimera det primära måttet.

* `primary_metric_name`: Namnet på det primära måttet som ska optimeras. Namnet på det primära måttet måste exakt matcha namnet på det mått som loggas av träningsskriptet. Se [Loggmått för hyperparameterjustering](#log-metrics-for-hyperparameter-tuning).
* `primary_metric_goal`: Det kan `PrimaryMetricGoal.MAXIMIZE` `PrimaryMetricGoal.MINIMIZE` vara antingen eller och avgör om det primära måttet kommer att maximeras eller minimeras vid utvärdering av körningar. 

```Python
primary_metric_name="accuracy",
primary_metric_goal=PrimaryMetricGoal.MAXIMIZE
```

Optimera körningarna för att maximera "noggrannheten".  Se till att logga det här värdet i ditt träningsskript.

<a name='log-metrics-for-hyperparameter-tuning'/>

### <a name="log-metrics-for-hyperparameter-tuning"></a>Loggmått för hyperparameterjustering

Utbildningsskriptet för din modell måste logga relevanta mått under modellutbildningen. När du konfigurerar hyperparameterjusteringen anger du det primära mått som ska användas för att utvärdera körningsprestanda. (Se [Ange ett primärt mått som ska optimeras](#specify-primary-metric-to-optimize).)  I utbildningsskriptet måste du logga det här måttet så att det är tillgängligt för hyperparameterjusteringsprocessen.

Logga det här måttet i utbildningsskriptet med följande exempelkodavsnitt:

```Python
from azureml.core.run import Run
run_logger = Run.get_context()
run_logger.log("accuracy", float(val_accuracy))
```

Träningsskriptet beräknar `val_accuracy` och loggar det som "noggrannhet", som används som det primära måttet. Varje gång måttet loggas tas det emot av hyperparameterjusteringstjänsten. Det är upp till modellutvecklaren att avgöra hur ofta det här måttet ska rapporteras.

<a name='specify-early-termination-policy'/>

## <a name="specify-early-termination-policy"></a>Ange policy för tidig uppsägning

Avsluta dåligt presterande körs automatiskt med en policy för tidig uppsägning. Avslutning minskar slöseri med resurser och använder i stället dessa resurser för att utforska andra parameterkonfigurationer.

När du använder en princip för tidig avslutning kan du konfigurera följande parametrar som styr när en princip tillämpas:

* `evaluation_interval`: frekvensen för tillämpningen av policyn. Varje gång utbildningsskriptet loggar det primära måttet räknas som ett intervall. Således `evaluation_interval` kommer en av 1 att tillämpa principen varje gång utbildningsskriptet rapporterar det primära måttet. En `evaluation_interval` av 2 kommer att tillämpa principen varannan gång utbildningsskriptet rapporterar det primära måttet. Om inget anges `evaluation_interval` är värdet 1 som standard.
* `delay_evaluation`: försenar den första principutvärderingen för ett visst antal intervall. Det är en valfri parameter som gör att alla konfigurationer kan köras under ett inledande minsta antal intervall, vilket undviker förtida avslutning av utbildningskörningar. Om det anges gäller principen alla flera evaluation_interval som är större än eller lika med delay_evaluation.

Azure Machine Learning stöder följande principer för tidig uppsägning.

### <a name="bandit-policy"></a>Bandit politik

[Bandit](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.banditpolicy?view=azure-ml-py#definition) är en uppsägningspolicy baserad på slackfaktor/slackbelopp och utvärderingsintervall. Principen avslutar tidigt alla körningar där det primära måttet inte ligger inom den angivna slackfaktorn/slackbeloppet med avseende på den bäst presterande träningskörningen. Det tar följande konfigurationsparametrar:

* `slack_factor`eller `slack_amount`: det tillåtna slacket med avseende på den bäst presterande träningskörningen. `slack_factor`anger det tillåtna slacket som ett förhållande. `slack_amount`anger det tillåtna slacket som ett absolut belopp, i stället för ett förhållande.

    Tänk dig till exempel att en Bandit-princip tillämpas med intervall 10. Anta att den bäst presterande körningen med intervall 10 rapporterade ett primärt mått 0,8 med ett mål att maximera det primära måttet. Om principen angavs med `slack_factor` en av 0,2 avslutas alla utbildningskörningar, vars bästa mått med intervall 10 är mindre`slack_factor`än 0,66 (0,8/(1+ )). Om i stället angavs principen `slack_amount` med en av 0,2, kommer alla utbildningskörningar, vars bästa mått med intervall `slack_amount`10 är mindre än 0,6 (0,8 - ) att avslutas.
* `evaluation_interval`: frekvensen för tillämpning av principen (valfri parameter).
* `delay_evaluation`: försenar den första principutvärderingen för ett visst antal intervall (valfri parameter).


```Python
from azureml.train.hyperdrive import BanditPolicy
early_termination_policy = BanditPolicy(slack_factor = 0.1, evaluation_interval=1, delay_evaluation=5)
```

I det här exemplet tillämpas principen för tidig uppsägning vid varje intervall när mått rapporteras, med början vid utvärderingsintervall 5. Alla löpkörningar vars bästa mått är mindre än (1/(1+0,1) eller 91 % av de bästa körningen kommer att avslutas.

### <a name="median-stopping-policy"></a>Medianstoppspolicy

[Medianstopp](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.medianstoppingpolicy?view=azure-ml-py) är en princip för tidig uppsägning baserat på löpande medelvärden för primära mått som rapporteras av körningarna. Den här principen beräknar löpande medelvärden för alla utbildningskörningar och avslutar körningar vars prestanda är sämre än medianvärdet för de löpande medelvärdena. Den här principen tar följande konfigurationsparametrar:
* `evaluation_interval`: frekvensen för tillämpning av principen (valfri parameter).
* `delay_evaluation`: försenar den första principutvärderingen för ett visst antal intervall (valfri parameter).


```Python
from azureml.train.hyperdrive import MedianStoppingPolicy
early_termination_policy = MedianStoppingPolicy(evaluation_interval=1, delay_evaluation=5)
```

I det här exemplet tillämpas principen för förtida avslutning vid varje intervall som börjar med utvärderingsintervall 5. En körning avslutas med intervall 5 om dess bästa primära mått är sämre än medianvärdet för de löpande medelvärdena över intervall 1:5 i alla utbildningskörningar.

### <a name="truncation-selection-policy"></a>Urvalspolicy för trunkering

[Trunkeringsval](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.truncationselectionpolicy?view=azure-ml-py) avbryter en viss procentandel av de lägsta körningarna vid varje utvärderingsintervall. Körningar jämförs baserat på deras prestanda på det primära måttet och det lägsta X% avslutas. Det tar följande konfigurationsparametrar:

* `truncation_percentage`: Procentandelen av de lägst presterande körningarna att avsluta vid varje utvärderingsintervall. Ange ett heltalsvärde mellan 1 och 99.
* `evaluation_interval`: frekvensen för tillämpning av principen (valfri parameter).
* `delay_evaluation`: försenar den första principutvärderingen för ett visst antal intervall (valfri parameter).


```Python
from azureml.train.hyperdrive import TruncationSelectionPolicy
early_termination_policy = TruncationSelectionPolicy(evaluation_interval=1, truncation_percentage=20, delay_evaluation=5)
```

I det här exemplet tillämpas principen för förtida avslutning vid varje intervall som börjar med utvärderingsintervall 5. En körning avslutas med intervall 5 om dess prestanda med intervall 5 är i de lägsta 20% av prestanda för alla körningar med intervall 5.

### <a name="no-termination-policy"></a>Ingen uppsägningspolicy

Om du vill att alla utbildningskörningar ska slutföras anger du principen till Ingen. Detta kommer att leda till att någon policy för förtida uppsägning inte tillämpas.

```Python
policy=None
```

### <a name="default-policy"></a>Standardprincip

Om ingen princip anges låter hyperparameterjusteringstjänsten alla utbildningskörningar slutföras.

### <a name="picking-an-early-termination-policy"></a>Välja en policy för förtida uppsägning

* Om du letar efter en konservativ politik som ger besparingar utan att avsluta `evaluation_interval` lovande `delay_evaluation` jobb, kan du använda en median stoppa politik med 1 och 5. Dessa är konservativa inställningar, som kan ge cirka 25%-35% besparingar utan förlust på primära mått (baserat på våra utvärderingsdata).
* Om du letar efter mer aggressiva besparingar från förtida uppsägning kan du antingen använda Bandit Policy med en striktare (mindre) tillåten slack- eller Trunkering-urvalspolicy med en större trunkeringsprocent.

## <a name="allocate-resources"></a>Fördela resurser

Styr din resursbudget för hyperparameterjusteringsexperimentet genom att ange det maximala totala antalet utbildningskörningar.  Du kan också ange den maximala varaktigheten för hyperparameterjusteringsexperimentet.

* `max_total_runs`: Maximalt totalt antal utbildningskörningar som kommer att skapas. Övre gräns - det kan finnas färre körningar, till exempel om hyperparameterutrymmet är begränsat och har färre prover. Måste vara ett tal mellan 1 och 1000.
* `max_duration_minutes`: Maximal varaktighet i minuter av hyperparameterjusteringsexperimentet. Parameter är valfritt, och om det finns, alla körningar som skulle köras efter den här varaktigheten avbryts automatiskt.

>[!NOTE] 
>Om `max_total_runs` båda `max_duration_minutes` och anges avslutas hyperparameterjusteringsexperimentet när det första av dessa två tröskelvärden uppnås.

Dessutom anger du det maximala antalet utbildningskörningar som ska köras samtidigt under hyperparameterjusteringssökningen.

* `max_concurrent_runs`: Maximalt antal körningar som ska köras samtidigt vid en viss tidpunkt. Om ingen anges `max_total_runs` kommer alla att startas parallellt. Om det anges måste det vara ett tal mellan 1 och 100.

>[!NOTE] 
>Antalet samtidiga körningar är gated på de resurser som är tillgängliga i det angivna beräkningsmålet. Därför måste du se till att beräkningsmålet har tillgängliga resurser för önskad samtidighet.

Allokera resurser för hyperparameterjustering:

```Python
max_total_runs=20,
max_concurrent_runs=4
```

Den här koden konfigurerar hyperparameterjusteringsexperimentet för att använda maximalt 20 totala körningar och kör fyra konfigurationer åt gången.

## <a name="configure-experiment"></a>Konfigurera experiment

[Konfigurera hyperparameterjusteringsexperimentet](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.hyperdriverunconfig?view=azure-ml-py) med hjälp av det definierade sökutrymmet hyperparameter, principen om tidig avslutning, primärt mått och resursallokering från avsnitten ovan. Dessutom, ge `estimator` en som kommer att kallas med de samplade hyperparametrarna. Beskriver `estimator` utbildningsskriptet du kör, resurserna per jobb (en eller flera gpu) och beräkningsmålet som ska användas. Eftersom samtidighet för hyperparameterjusteringsexperimentet är gated på de tillgängliga resurserna, se till att beräkningsmålet som anges i `estimator` har tillräckliga resurser för önskad samtidighet. (Mer information om skattställare finns i [hur du tränar modeller](how-to-train-ml-models.md).)

Konfigurera hyperparameterjusteringsexperimentet:

```Python
from azureml.train.hyperdrive import HyperDriveConfig
hyperdrive_run_config = HyperDriveConfig(estimator=estimator,
                          hyperparameter_sampling=param_sampling, 
                          policy=early_termination_policy,
                          primary_metric_name="accuracy", 
                          primary_metric_goal=PrimaryMetricGoal.MAXIMIZE,
                          max_total_runs=100,
                          max_concurrent_runs=4)
```

## <a name="submit-experiment"></a>Skicka in experiment

När du har definierat hyperparameterjusteringskonfigurationen [skickar du in ett experiment:](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment%28class%29?view=azure-ml-py#submit-config--tags-none----kwargs-)

```Python
from azureml.core.experiment import Experiment
experiment = Experiment(workspace, experiment_name)
hyperdrive_run = experiment.submit(hyperdrive_run_config)
```

`experiment_name`är det namn du tilldelar hyperparameterjusteringsexperimentet och `workspace` är arbetsytan där du vill skapa experimentet (Mer information om experiment finns i Hur fungerar Azure Machine [Learning?](concept-azure-machine-learning-architecture.md))

## <a name="warm-start-your-hyperparameter-tuning-experiment-optional"></a>Varmstart ditt hyperparameterjusteringsexperiment (tillval)

Ofta kan det vara en iterativ process att hitta de bästa hyperparametervärdena för din modell, som behöver flera justeringskörningar som lär sig av tidigare hyperparameterjusteringskörningar. Återanvända kunskap från dessa tidigare körningar kommer att påskynda hyperparameter tuning processen, vilket minskar kostnaden för att trimma modellen och kommer potentiellt att förbättra det primära måttet på den resulterande modellen. När du värmestartar ett hyperparameterjusteringsexperiment med Bayesiansk sampling, kommer försök från föregående körning att användas som förkunskaper för att på ett intelligent sätt plocka nya prover för att förbättra det primära måttet. När du använder Random- eller Grid-sampling kommer dessutom alla beslut om tidig uppsägning att utnyttja mått från tidigare körningar för att avgöra dåligt utförda utbildningskörningar. 

Med Azure Machine Learning kan du värma upp din hyperparameter-justering som körs genom att utnyttja kunskap från upp till 5 tidigare slutförda/avbrutna överordnade hyperparameterjusteringskörningar. Du kan ange listan över överordnade körningar som du vill värma start från att använda det här kodavsnittet:

```Python
from azureml.train.hyperdrive import HyperDriveRun

warmstart_parent_1 = HyperDriveRun(experiment, "warmstart_parent_run_ID_1")
warmstart_parent_2 = HyperDriveRun(experiment, "warmstart_parent_run_ID_2")
warmstart_parents_to_resume_from = [warmstart_parent_1, warmstart_parent_2]
```

Dessutom kan det finnas tillfällen då enskilda utbildningskörningar av ett hyperparameterjusteringsexperiment avbryts på grund av budgetbegränsningar eller misslyckas på grund av andra orsaker. Det är nu möjligt att återuppta sådana individuella utbildning körs från den sista kontrollpunkten (förutsatt att din utbildning skript hanterar kontrollpunkter). Om du återupptar en enskild träningskörning används samma hyperparameterkonfiguration och den utdatamapp som används för den körningen. Utbildningsskriptet bör `resume-from` acceptera argumentet, som innehåller kontrollpunkten eller modellfilerna som du kan återuppta utbildningskörningen från. Du kan återuppta enskilda utbildningskörningar med hjälp av följande utdrag:

```Python
from azureml.core.run import Run

resume_child_run_1 = Run(experiment, "resume_child_run_ID_1")
resume_child_run_2 = Run(experiment, "resume_child_run_ID_2")
child_runs_to_resume = [resume_child_run_1, resume_child_run_2]
```

Du kan konfigurera hyperparameterjusteringsexperimentet så att det blir en `resume_from` `resume_child_runs` varm start från ett tidigare experiment eller återupptar enskilda utbildningskörningar med hjälp av valfria parametrar och i konfigurationen:

```Python
from azureml.train.hyperdrive import HyperDriveConfig

hyperdrive_run_config = HyperDriveConfig(estimator=estimator,
                          hyperparameter_sampling=param_sampling, 
                          policy=early_termination_policy,
                          resume_from=warmstart_parents_to_resume_from, 
                          resume_child_runs=child_runs_to_resume,
                          primary_metric_name="accuracy", 
                          primary_metric_goal=PrimaryMetricGoal.MAXIMIZE,
                          max_total_runs=100,
                          max_concurrent_runs=4)
```

## <a name="visualize-experiment"></a>Visualisera experiment

Azure Machine Learning SDK tillhandahåller en [anteckningsbokswidget](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets.rundetails?view=azure-ml-py) som visualiserar förloppet för dina utbildningskörningar. Följande utdrag visualiserar alla hyperparameterjusteringskörningar på ett ställe i en Jupyter-anteckningsbok:

```Python
from azureml.widgets import RunDetails
RunDetails(hyperdrive_run).show()
```

Den här koden visar en tabell med information om utbildningskörningar för var och en av hyperparameterkonfigurationerna.

![justeringstabell för hyperparameter](./media/how-to-tune-hyperparameters/HyperparameterTuningTable.png)

Du kan också visualisera resultatet för var och en av körningarna under utbildningens gång. 

![hyperparameterjusteringsdiagram](./media/how-to-tune-hyperparameters/HyperparameterTuningPlot.png)

Dessutom kan du visuellt identifiera korrelationen mellan prestanda och värden för enskilda hyperparametrar med hjälp av en parallell koordinater plot. 

[![hyperparameterjustering parallella koordinater](./media/how-to-tune-hyperparameters/HyperparameterTuningParallelCoordinates.png)](media/how-to-tune-hyperparameters/hyperparameter-tuning-parallel-coordinates-expanded.png)

Du kan visualisera alla dina hyperparameterjusteringskörningar i Azure-webbportalen också. Mer information om hur du visar ett experiment i webbportalen finns i [hur du spårar experiment](how-to-track-experiments.md#view-the-experiment-in-the-web-portal).

## <a name="find-the-best-model"></a>Hitta den bästa modellen

När alla hyperparameterjusteringskörningar har slutförts [identifierar du konfigurationen för bästa prestanda](/python/api/azureml-train-core/azureml.train.hyperdrive.hyperdriverun?view=azure-ml-py#get-best-run-by-primary-metric-include-failed-true--include-canceled-true--include-resume-from-runs-true-----typing-union-azureml-core-run-run--nonetype-) och motsvarande hyperparametervärden:

```Python
best_run = hyperdrive_run.get_best_run_by_primary_metric()
best_run_metrics = best_run.get_metrics()
parameter_values = best_run.get_details()['runDefinition']['Arguments']

print('Best Run Id: ', best_run.id)
print('\n Accuracy:', best_run_metrics['accuracy'])
print('\n learning rate:',parameter_values[3])
print('\n keep probability:',parameter_values[5])
print('\n batch size:',parameter_values[7])
```

## <a name="sample-notebook"></a>Exempel på anteckningsbok
Se bärbara tåg-hyperparameter-* i den här mappen:
* [how-to-use-azureml/training-with-deep-learning](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Nästa steg
* [Spåra ett experiment](how-to-track-experiments.md)
* [Distribuera en tränad modell](how-to-deploy-and-where.md)
