---
title: Justera hyperparametrar för din modell
titleSuffix: Azure Machine Learning
description: Justera de flesta parametrar för din djup inlärnings-och maskin inlärnings modell med hjälp av Azure Machine Learning. Du får lära dig hur du definierar parameter Sök utrymmet, anger ett primärt mått som ska optimeras och tidigt avslutar dåligt utförande av körningar.
ms.author: swatig
author: swatig007
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: seodec18
ms.openlocfilehash: 5d30f59252a5282c1b0e43249d2cab1e6136b539
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74276667"
---
# <a name="tune-hyperparameters-for-your-model-with-azure-machine-learning"></a>Justera dina modellers egenskaper med Azure Machine Learning
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Justera dina modeller på ett effektivt sätt med hjälp av Azure Machine Learning.  Finjustering av hyperparametrar omfattar följande steg:

* Definiera parametern search utrymme
* Ange ett primära mått för att optimera  
* Ange villkor för tidig uppsägning för att utföra dåligt körningar
* Allokera resurser för finjustering av hyperparametrar
* Starta ett experiment med ovanstående konfiguration
* Visualisera träningskörningar
* Välj den bästa konfigurationen för din modell

## <a name="what-are-hyperparameters"></a>Vad är hyperparametrar?

Hyperparametrar är justerbara parametrar som du väljer att träna en modell och som reglerar hur utbildning. Till exempel för att träna ett djupa neurala nätverk bestämmer du hur många dolda lager i nätverket och antalet noder i varje lager före träna modellen. Dessa värden Håll vanligtvis konstant under utbildning.

I deep learning / machine learning-scenarier beroende modellprestanda kraftigt finjustering värden som väljs. Målet med finjustering utforskning är att söka i olika finjustering konfigurationer för att hitta en konfiguration som ger bästa prestanda. Finjustering utforskning processen är vanligtvis mödosamt manuell tanke på att området search är stora och utvärdering av varje konfiguration kan vara dyrt.

Azure Machine Learning kan du automatisera finjustering utforskning på ett effektivt sätt sparar mycket tid och resurser. Du anger finjustering värdeintervallet och ett högsta antal utbildning körs. Systemet sedan startas flera samtidiga körningar med olika parameterkonfigurationer och söker efter den konfiguration som ger bästa prestanda, enligt de mått som du väljer. Dåligt utför träningskörningar är automatiskt tidig avbröts, vilket minskar slöseri av beräkningsresurser. Dessa resurser används i stället för att utforska andra finjustering-konfigurationer.


## <a name="define-search-space"></a>Definiera search utrymme

Justera hyperparametrar genom att utforska vilka värden som definierats för varje finjustering.

### <a name="types-of-hyperparameters"></a>Typer av hyperparametrar

Varje enskild parameter kan antingen vara diskret eller kontinuerlig och har en fördelning av värden som beskrivs av ett [parameter uttryck](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.parameter_expressions?view=azure-ml-py).

#### <a name="discrete-hyperparameters"></a>Diskret hyperparametrar 

Diskreta hyperparametrar har angetts som en `choice` mellan diskreta värden. `choice` kan vara:

* minst en fil med kommaavgränsade värden
* en `range` objekt
* alla godtyckliga `list` objekt


```Python
    {
        "batch_size": choice(16, 32, 64, 128)
        "number_of_hidden_layers": choice(range(1,5))
    }
```

I det här fallet `batch_size` tar på något av värdena [16, 32, 64, 128] och `number_of_hidden_layers` tar på något av värdena [1, 2, 3, 4].

Avancerade diskreta hyperparametrar kan också anges med hjälp av en distributionsplats. Stöds följande distributioner:

* `quniform(low, high, q)` – Returnerar ett värde som resursallokering (uniform (lägsta, högsta) / q) * q
* `qloguniform(low, high, q)` – Returnerar ett värde som resursallokering (exp (uniform (lägsta, högsta)) / q) * q
* `qnormal(mu, sigma, q)` – Returnerar ett värde som resursallokering (normal (mu, sigma) / q) * q
* `qlognormal(mu, sigma, q)` – Returnerar ett värde som resursallokering (exp (normal (mu, sigma)) / q) * q

#### <a name="continuous-hyperparameters"></a>Kontinuerlig hyperparametrar 

Kontinuerlig hyperparametrar har angetts som en distributionsplats över en kontinuerlig rad med värden. Distributioner som stöds är:

* `uniform(low, high)` – Returnerar ett värde som är jämnt fördelade mellan låg och hög
* `loguniform(low, high)` – Returnerar ett-värde ritas enligt exp (uniform (lägsta, högsta)) så att logaritmen för det returnera värdet är jämnt
* `normal(mu, sigma)` – Returnerar ett verkliga värde som normalt är distribuerade med mean mu och standardavvikelsen sigma
* `lognormal(mu, sigma)` – Returnerar ett-värde ritas enligt exp (normal (mu, sigma)) så att logaritmen för returvärdet distribueras normalt

Ett exempel på en parameterdefinition utrymme:

```Python
    {    
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1)
    }
```

Den här koden definierar search kan med två parametrar – `learning_rate` och `keep_probability`. `learning_rate` har en normalfördelning med medelvärdet 10 och en standardavvikelse på 3. `keep_probability` har en jämn fördelning med en minsta värde av 0,05 och det högsta värdet 0,1.

### <a name="sampling-the-hyperparameter-space"></a>Sampling finjustering utrymme

Du kan även ange parametern-samplingsmetoden för att använda via finjustering utrymme definition. Azure Machine Learning stöder slumpmässig provtagning, rutnäts sampling och Bayesian-sampling.

#### <a name="picking-a-sampling-method"></a>Plocka en samplings metod

* Rutnäts sampling kan användas om ditt områdes parameter utrymme kan definieras som ett alternativ bland diskreta värden och om du har tillräcklig budget för att söka igenom alla värden i det definierade Sök utrymmet. Dessutom kan en automatiserad avstängning av dåligt utförande, vilket minskar svinnt av resurser.
* Med slumpmässig sampling kan du ta med både diskreta och kontinuerliga grundparametrar. I praktiken ger den bra resultat i de flesta tider och kan också användas för att automatisera tidig avstängning av dåligt utförda körningar. Vissa användare utför en inledande sökning med slumpmässig sampling och förfina sedan Sök utrymmet iterativt för att förbättra resultaten.
* Bayesian-samplingen använder kunskaper om tidigare exempel när du väljer värden för en parameter, vilket effektivt försöker förbättra det rapporterade primära måttet. Bayesian-sampling rekommenderas när du har tillräckligt med budget för att utforska området för den övre parametern, för bästa resultat med Bayesian-sampling rekommenderar vi att du använder ett maximalt antal körningar som är större än eller lika med 20 gånger antalet som är justerade. Observera att Bayesian-sampling för närvarande inte stöder någon tidig avslutnings princip.

#### <a name="random-sampling"></a>Stickprov

I stickprov väljs slumpmässigt finjustering värden från det definierade search-utrymmet. Med [slumpmässig sampling](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.randomparametersampling?view=azure-ml-py) kan Sök utrymmet innehålla både diskreta och kontinuerliga disponeringsparametrarna.

```Python
from azureml.train.hyperdrive import RandomParameterSampling
param_sampling = RandomParameterSampling( {
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```

#### <a name="grid-sampling"></a>Rutnätet sampling

Vid [Rutnäts sampling](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.gridparametersampling?view=azure-ml-py) utförs en enkel rutnäts sökning över alla genomförbara värden i det definierade Sök utrymmet. Det kan bara användas med hyperparametrar anges med `choice`. Till exempel har följande område totalt sex exempel:

```Python
from azureml.train.hyperdrive import GridParameterSampling
param_sampling = GridParameterSampling( {
        "num_hidden_layers": choice(1, 2, 3),
        "batch_size": choice(16, 32)
    }
)
```

#### <a name="bayesian-sampling"></a>Bayesian sampling

[Bayesian-sampling](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.bayesianparametersampling?view=azure-ml-py) baseras på algoritmen för Bayesian-optimering och gör intelligenta val på de båda parametervärdena för att sampla härnäst. Det använder exemplet baserat på hur de föregående exempel utförs, så att nya exemplet förbättrar rapporterade primära mått.

När du använder Bayesian sampling, påverkar antalet samtidiga körningar effektiviteten i justering processen. Vanligtvis kan ett mindre antal samtidiga körningar leda till bättre sampling konvergens, eftersom mindre graden av parallellitet ökar antalet körningar som har nytta av tidigare slutförda körningar.

Bayesian-sampling stöder endast `choice`, `uniform`och `quniform` distributioner via Sök utrymmet.

```Python
from azureml.train.hyperdrive import BayesianParameterSampling
param_sampling = BayesianParameterSampling( {
        "learning_rate": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```

> [!NOTE]
> Bayesian sampling har inte stöd för tidig uppsägning princip (se [ange en princip för tidig uppsägning](#specify-early-termination-policy)). När du använder Bayesian parametern sampling anger `early_termination_policy = None`, eller lämna den `early_termination_policy` parametern.

<a name='specify-primary-metric-to-optimize'/>

## <a name="specify-primary-metric"></a>Ange primär mått

Ange det [primära mått](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.primarymetricgoal?view=azure-ml-py) som du vill att det bästa experimentet för att justera ska optimera. Varje körning utbildning utvärderas för den primära måtten. Dåligt utför körs (där den primära måtten inte uppfyller villkor som anges av principen för tidig uppsägning) kommer att avslutas. Förutom det primära måttnamnet du även ange målet med optimering - om du vill maximera eller minimera primära mått.

* `primary_metric_name`: Namnet på den primära måtten att optimera. Namnet på den primära måtten måste exakt matcha namnet på det mått som loggats av skriptet utbildning. Se [logga mått för finjustering av hyperparametrar](#log-metrics-for-hyperparameter-tuning).
* `primary_metric_goal`: Det kan vara antingen `PrimaryMetricGoal.MAXIMIZE` eller `PrimaryMetricGoal.MINIMIZE` och avgör om den primära måtten ska maximerat eller minimeras vid utvärdering av körningarna. 

```Python
primary_metric_name="accuracy",
primary_metric_goal=PrimaryMetricGoal.MAXIMIZE
```

Optimera körs för att maximera ”precision”.  Se till att logga in det här värdet i dina utbildningsskript.

<a name='log-metrics-for-hyperparameter-tuning'/>

### <a name="log-metrics-for-hyperparameter-tuning"></a>Log mått för finjustering av hyperparametrar

Skriptet utbildning för din modell måste logga in mått som är relevanta under modellträning. När du konfigurerar den finjustering av hyperparametrar kan ange du primära mått som ska använda för att utvärdera kör prestanda. (Se [Ange ett primärt mått att optimera](#specify-primary-metric-to-optimize).)  I ditt utbildnings skript måste du logga det här måttet så att det är tillgängligt för justerings processen för den egna parametern.

Logga in med det här måttet i utbildning skriptet med följande exempel kodfragment:

```Python
from azureml.core.run import Run
run_logger = Run.get_context()
run_logger.log("accuracy", float(val_accuracy))
```

Skriptet utbildning beräknar den `val_accuracy` och loggar som ”Precision” som används som primär måttet. Varje gång som måttet loggas som den tas emot av finjustering justering service. Det är upp till modellen utvecklaren att fastställa hur ofta du vill rapportera det här måttet.

<a name='specify-early-termination-policy'/>

## <a name="specify-early-termination-policy"></a>Ange princip för tidig uppsägning

Avsluta dåligt utför körs automatiskt med en princip för tidig uppsägning. Avslutning minskar slöseri med resurser och i stället använder de här resurserna för att utforska andra parameterkonfigurationer.

När du använder en tidig uppsägning princip kan konfigurera du följande parametrar som styr när en princip tillämpas:

* `evaluation_interval`: frekvensen för att tillämpa principen. Varje gång utbildning skriptet loggar den primära måtten räknas som ett intervall. Därför en `evaluation_interval` 1 gäller principen varje gång utbildning skriptet rapporterar primära mått. En `evaluation_interval` 2 ska tillämpa principen för varje gång som utbildning skriptet rapporterar primära mått. Om inte anges `evaluation_interval` anges till 1 som standard.
* `delay_evaluation`: fördröjer första principutvärdering för ett angivet antal intervall. Det är en valfri parameter som gör att alla konfigurationer att köra för en inledande minsta antalet intervall, undvika för tidig uppsägning av utbildning körs. Om principen gäller alla multipel av evaluation_interval som är större än eller lika med delay_evaluation.

Azure Machine Learning stöder följande tidiga avslutnings principer.

### <a name="bandit-policy"></a>Bandit princip

[Bandit](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.banditpolicy?view=azure-ml-py#definition) är en avslutnings princip som baseras på slack-faktorn/slack och utvärderings intervallet. Principen avbryter tidigt några körningar där den primära måtten ligger inte inom den angivna slack faktorn / slack mycket med avseende på den bästa utbildning köra. Det tar följande konfigurationsparametrar:

* `slack_factor` eller `slack_amount`: slack tillåtet med avseende på den bästa utbildning som kör. `slack_factor` Anger det tillåtna slacket som ett förhållande. `slack_amount` Anger det tillåtna slacket som ett absolut värde, i stället för ett förhållande.

    Anta exempelvis att en Bandit-principer som tillämpas på intervallet 10. Anta att den bästa utför som körs vid intervall 10 rapporterat ett primära mått 0,8 med målet att maximera den primära måtten. Om principen har angetts med en `slack_factor` på 0,2, vilken utbildning körs, vars bästa mått på intervallet 10 är mindre än 0.66 (0,8 / (1 +`slack_factor`)) kommer att avslutas. Om du i stället principen angavs med en `slack_amount` på 0,2, vilken utbildning körs, vars bästa mått på intervallet 10 är mindre än 0,6 (0,8 - `slack_amount`) kommer att avslutas.
* `evaluation_interval`: frekvensen för att tillämpa principen (valfri parameter).
* `delay_evaluation`: fördröjer första principutvärdering för ett angivet antal intervall (valfri parameter).


```Python
from azureml.train.hyperdrive import BanditPolicy
early_termination_policy = BanditPolicy(slack_factor = 0.1, evaluation_interval=1, delay_evaluation=5)
```

I det här exemplet tillämpas principen för tidig uppsägning vid varje intervall när mått rapporteras, med början vid utvärderingsintervall 5. Alla kör vars bästa mått är mindre än (1/(1+0.1) eller 91% av bäst prestanda kör kommer att avslutas.

### <a name="median-stopping-policy"></a>Median stoppar princip

[Median stopp](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.medianstoppingpolicy?view=azure-ml-py) är en princip för tidig avslutning som baseras på löpande medelvärden av primära mått som rapporteras av körningarna. Den här principen beräkningar i följd över alla träningskörningar och avslutar körningar vars prestanda är sämre än medianen för de i följd. Den här principen tar följande konfigurationsparametrar:
* `evaluation_interval`: frekvensen för att tillämpa principen (valfri parameter).
* `delay_evaluation`: fördröjer första principutvärdering för ett angivet antal intervall (valfri parameter).


```Python
from azureml.train.hyperdrive import MedianStoppingPolicy
early_termination_policy = MedianStoppingPolicy(evaluation_interval=1, delay_evaluation=5)
```

I det här exemplet tillämpas tidig uppsägning principen vid varje intervall med början vid utvärderingsintervall 5. En körning kommer att avslutas med intervall 5 om dess bästa primära mått sämre än medianen för pågående medelvärden över intervall 1:5 mellan alla träningskörningar.

### <a name="truncation-selection-policy"></a>Trunkering av val av princip

[Avtrunkering av markering](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.truncationselectionpolicy?view=azure-ml-py) avbryter en viss procent andel av de lägsta körnings körningarna vid varje utvärderings intervall. Körningar jämförs baserat på deras prestanda på den primära måtten och det lägsta X % avslutas. Det tar följande konfigurationsparametrar:

* `truncation_percentage`: procentandelen lägsta utför körs för att sluta vid varje utvärderingsintervall. Ange ett heltal mellan 1 och 99.
* `evaluation_interval`: frekvensen för att tillämpa principen (valfri parameter).
* `delay_evaluation`: fördröjer första principutvärdering för ett angivet antal intervall (valfri parameter).


```Python
from azureml.train.hyperdrive import TruncationSelectionPolicy
early_termination_policy = TruncationSelectionPolicy(evaluation_interval=1, truncation_percentage=20, delay_evaluation=5)
```

I det här exemplet tillämpas tidig uppsägning principen vid varje intervall med början vid utvärderingsintervall 5. En körning avslutas med intervall 5 om dess prestanda vid intervall 5 är i den lägsta 20% av prestandan för alla körningar i intervallet 5.

### <a name="no-termination-policy"></a>Ingen princip för uppsägning

Om du vill att alla träningskörningar till att slutföras, ställa in principen till ingen. Detta har effekten av att tidig uppsägning principer tillämpas inte.

```Python
policy=None
```

### <a name="default-policy"></a>Standardprincipen

Om ingen princip har angetts, kommer tjänsten för egenskaps justering att låta all utbildning köras på Slutför.

### <a name="picking-an-early-termination-policy"></a>Plocka en princip för tidig avslutning

* Om du letar efter en konservativ princip som ger besparingar utan avslutande lovande jobb kan du använda en Median stoppar princip med `evaluation_interval` 1 och `delay_evaluation` 5. Det här är konservativ inställningar som kan ge cirka 25% – 35% billigare utan att förlora på primära mått (baserat på vår utvärdering av data).
* Om du vill ha mer aggressiva besparingar från tidiga uppsägningar kan du antingen använda bandit-principen med en striktare (mindre) tillåten slack eller trunkering princip för urvals princip med en större trunkning i procent.

## <a name="allocate-resources"></a>Tilldela resurser

Kontrollera din resurs budget för din finjustering justering experiment genom att ange det maximala antalet träningskörningar.  Du kan också ange den högsta tiden för din finjustering justering experiment.

* `max_total_runs`: Högsta antal träningskörningar som ska skapas. Övre gränsen – det kan vara färre, exempelvis om finjustering utrymme är begränsad och har färre exempel. Måste vara ett tal mellan 1 och 1000.
* `max_duration_minutes`: Maximal varaktighet i minuter för finjustering justering experiment. Parametern är valfri och om det finns några körningar som skulle köras efter varaktigheten avbryts automatiskt.

>[!NOTE] 
>Om både `max_total_runs` och `max_duration_minutes` anges, finjustering justering experiment avslutas när först av dessa två tröskelvärden har uppnåtts.

Dessutom ange det maximala antalet utbildning körs för att köra samtidigt under din finjustering justering sökning.

* `max_concurrent_runs`: Maximalt antal körningar körs samtidigt vid ett givet tillfälle. Om inget anges, alla `max_total_runs` kommer att startas samtidigt. Om anges måste vara ett tal mellan 1 och 100.

>[!NOTE] 
>Antalet samtidiga körningar är gated på de tillgängliga resurserna i den angivna beräkningsmål. Därför måste du kontrollera att beräkningsmål har de tillgängliga resurserna för den önskade samtidigheten.

Allokera resurser för finjustering av hyperparametrar:

```Python
max_total_runs=20,
max_concurrent_runs=4
```

Den här koden konfigurerar experimentet för inställning av parameter till att använda maximalt 20 Totalt antal körningar som kör fyra konfigurationer i taget.

## <a name="configure-experiment"></a>Konfigurera experiment

[Konfigurera ditt för inställnings](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.hyperdriverunconfig?view=azure-ml-py) experiment med det definierade områdets sökområde, tidig avslutnings princip, primärt mått och resursallokering från avsnitten ovan. Dessutom ger en `estimator` som kommer att anropas med provade hyperparametrar. Den `estimator` beskriver inlärningsskript som du kör, resurserna per projekt (en eller flera gpu) och beräkningsmål att använda. Eftersom samtidighet för din finjustering justering experimentet är gated på resurserna som är tillgängliga, kontrollera att beräkningsmål som anges i den `estimator` har tillräckligt med resurser för din önskade samtidighet. (Läs mer på estimators [hur du tränar modeller](how-to-train-ml-models.md).)

Konfigurera din finjustering justering experiment:

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

## <a name="submit-experiment"></a>Skicka experiment

När du har definierat konfigurationen för konfiguration av den egna parametern kan du [Skicka ett experiment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment%28class%29?view=azure-ml-py#submit-config--tags-none----kwargs-):

```Python
from azureml.core.experiment import Experiment
experiment = Experiment(workspace, experiment_name)
hyperdrive_run = experiment.submit(hyperdrive_run_config)
```

`experiment_name` är det namn som du tilldelar till ditt för inställnings experiment med din parameter och `workspace` är arbets ytan där du vill skapa experimentet (mer information om experiment finns i [Hur fungerar Azure Machine Learning?](concept-azure-machine-learning-architecture.md))

## <a name="warm-start-your-hyperparameter-tuning-experiment-optional"></a>Varm kom igång med ditt inställnings experiment för en parameter (valfritt)

Ofta kan det vara en iterativ process att hitta bästa möjliga parameter värden för din modell, vilket kräver flera justerings körningar som lär sig från tidigare justerings körningar i en parameter. Om du återanvänder kunskaper från dessa tidigare körningar påskyndas justerings processen för en parameter, vilket minskar kostnaden för att justera modellen och kan förbättra den resulterande modellens primära mått. När du startar en inställning för en parameter justering med Bayesian-sampling, används försök från föregående körning som tidigare kunskap för att på ett intelligent sätt välja nya exempel för att förbättra det primära måttet. När slumpmässiga eller rutnäts sampling används, kommer eventuella beslut om besluts fattande att dra nytta av mått från de tidigare körningarna för att fastställa dåligt utförda utbildnings körningar. 

Azure Machine Learning ger dig möjlighet att starta din egenskaps justering för din egen parameter genom att dra nytta av kunskap från upp till 5 tidigare slutförda/avbrutna konfigurations inställningar för den överordnade parametern. Du kan ange listan över överordnade körningar som du vill komma igång med att använda kodfragmentet:

```Python
from azureml.train.hyperdrive import HyperDriveRun

warmstart_parent_1 = HyperDriveRun(experiment, "warmstart_parent_run_ID_1")
warmstart_parent_2 = HyperDriveRun(experiment, "warmstart_parent_run_ID_2")
warmstart_parents_to_resume_from = [warmstart_parent_1, warmstart_parent_2]
```

Det kan också uppstå tillfällen när enskilda utbildningar körs på ett justerings experiment med en parameter för att avbrytas på grund av budget begränsningar eller Miss lyckas på grund av andra orsaker. Det är nu möjligt att återuppta en sådan enskild utbildning som körs från den senaste kontroll punkten (förutsatt att ditt utbildnings skript hanterar kontroll punkter). Om du återupptar en enskild övnings körning används samma konfiguration för den egna katalogen och den mappen utdata som används för den körs. Övnings skriptet bör godkänna argumentet `resume-from`, som innehåller de kontroll punkter eller modeller som du kan använda för att återuppta utbildningen. Du kan återuppta enskilda inlärnings körningar med följande kodfragment:

```Python
from azureml.core.run import Run

resume_child_run_1 = Run(experiment, "resume_child_run_ID_1")
resume_child_run_2 = Run(experiment, "resume_child_run_ID_2")
child_runs_to_resume = [resume_child_run_1, resume_child_run_2]
```

Du kan konfigurera ditt inställnings experiment för att komma igång med ett tidigare experiment eller återuppta enskilda inlärnings körningar med hjälp av valfria parametrar `resume_from` och `resume_child_runs` i konfigurationen:

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

Azure Machine Learning SDK innehåller en [anteckningsbok-widget](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets.rundetails?view=azure-ml-py) som visualiserar förloppet för din utbildning körs. Följande kodavsnitt visar dina finjustering justering körs på en plats i en Jupyter-anteckningsbok:

```Python
from azureml.widgets import RunDetails
RunDetails(hyperdrive_run).show()
```

Den här koden visar en tabell med information om träningskörningar för var och en av de finjustering-konfigurationerna.

![finjustering justering tabell](media/how-to-tune-hyperparameters/HyperparameterTuningTable.png)

Du kan också visualisera prestanda för alla körningar som utbildning fortskrider. 

![finjustering justering diagram](media/how-to-tune-hyperparameters/HyperparameterTuningPlot.png)

Du kan dessutom identifiera sambandet mellan prestanda och värdena för enskilda hyperparametrar med hjälp av en samordnar Rita parallella visuellt. 

[![för att justera parallella koordinater för en parameter](media/how-to-tune-hyperparameters/HyperparameterTuningParallelCoordinates.png)](media/how-to-tune-hyperparameters/hyperparameter-tuning-parallel-coordinates-expanded.png)

Du kan visualisera dina finjustering justering körs i Azure web-portalen. Läs mer om hur du visar ett experiment i webbportalen, [hur du spårar experiment](how-to-track-experiments.md#view-the-experiment-in-the-web-portal).

## <a name="find-the-best-model"></a>Hitta den bästa modellen

När alla konfigurations inställningar för den här parametern har slutförts, [identifierar du bästa möjliga konfiguration](/python/api/azureml-train-core/azureml.train.hyperdrive.hyperdriverun?view=azure-ml-py#get-best-run-by-primary-metric-include-failed-true--include-canceled-true--include-resume-from-runs-true-----typing-union-azureml-core-run-run--nonetype-) och motsvarande värden för båda parametrarna:

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

## <a name="sample-notebook"></a>Exempel-anteckningsbok
Referera till träna-Real-parameter-* Notebooks i den här mappen:
* [How-to-use-azureml/Training-with-Deep-Learning](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Nästa steg
* [Spåra ett experiment](how-to-track-experiments.md)
* [Distribuera en tränad modell](how-to-deploy-and-where.md)
