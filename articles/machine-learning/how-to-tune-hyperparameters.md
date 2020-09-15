---
title: Justera dina modellers egenskaper
titleSuffix: Azure Machine Learning
description: Justera de flesta parametrar för din djup inlärnings-och maskin inlärnings modell med hjälp av Azure Machine Learning. Du får lära dig hur du definierar parameter Sök utrymmet, anger ett primärt mått som ska optimeras och tidigt avslutar dåligt utförande av körningar.
ms.author: swatig
author: swatig007
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 03/30/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 52e419e970173ddaf3d4d6176f2dd26a1e8194e2
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/15/2020
ms.locfileid: "90084677"
---
# <a name="tune-hyperparameters-for-your-model-with-azure-machine-learning"></a>Justera dina modellers egenskaper med Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Justera dina modeller på ett effektivt sätt med hjälp av Azure Machine Learning.  Inställningen för min parameter innehåller följande steg:

* Definiera parameter Sök utrymmet
* Ange ett primärt mått som ska optimeras  
* Ange kriterier för tidig avslutning för dåligt utförda körningar
* Allokera resurser för inställning av min parameter
* Starta ett experiment med konfigurationen ovan
* Visualisera inlärnings körningar
* Välj den bästa presterande konfigurationen för din modell

## <a name="what-are-hyperparameters"></a>Vad är mina parametrar?

De båda parametrarna är justerbara parametrar som du väljer för att träna en modell som styr själva inlärnings processen. Om du till exempel vill träna ett djup neurala nätverk bestämmer du antalet dolda lager i nätverket och antalet noder i varje lager innan du tränar modellen. Dessa värden är vanligt vis konstanta under inlärnings processen.

I djup inlärnings-och maskin inlärnings scenarier är modell prestanda beroende av de egenskaper för den valda parametern. Målet för utforskningen av en parameter är att söka bland olika konfigurationer för en valfri parameter för att hitta en konfiguration som resulterar i bästa prestanda. Normalt är utforsknings processen för mödosamt manuell, eftersom Sök utrymmet är stort och utvärderingen av varje konfiguration kan vara dyrt.

Med Azure Machine Learning kan du automatisera utforskningen av en hel parameter på ett effektivt sätt, vilket sparar mycket tid och resurser. Du anger intervallet mellan parameter värden och ett maximalt antal utbildnings körningar. Systemet startar sedan automatiskt flera samtidiga körningar med olika parameter konfigurationer och söker efter den konfiguration som resulterar i bästa prestanda, uppmätt av det mått du väljer. Dåligt utförda utbildnings körningar avslutas automatiskt tidigt, vilket minskar svinnt av beräknings resurser. Dessa resurser används i stället för att utforska andra konfigurations inställningar för olika konfigurationer.


## <a name="define-search-space"></a>Definiera sökområde

Justera de båda parametrarna automatiskt genom att utforska intervallet av de värden som definierats för varje-parameter.

### <a name="types-of-hyperparameters"></a>Typer av disponeringsparametrarna

Varje enskild parameter kan antingen vara diskret eller kontinuerlig och har en fördelning av värden som beskrivs av ett [parameter uttryck](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.parameter_expressions?view=azure-ml-py&preserve-view=true).

#### <a name="discrete-hyperparameters"></a>Diskreta egenskaper 

Diskreta egenskaper anges som en `choice` mellan diskreta värden. `choice` kan vara:

* ett eller flera kommaavgränsade värden
* ett `range` objekt
* valfritt godtyckligt `list` objekt


```Python
    {
        "batch_size": choice(16, 32, 64, 128)
        "number_of_hidden_layers": choice(range(1,5))
    }
```

I det här fallet `batch_size` tar ett av värdena [16, 32, 64, 128] och `number_of_hidden_layers` tar på ett av värdena [1, 2, 3, 4].

Avancerade diskreta personifieringsparametrar kan också anges med en distribution. Följande distributioner stöds:

* `quniform(low, high, q)` -Returnerar ett värde som Round (Uniform (låg, hög)/q) * q
* `qloguniform(low, high, q)` -Returnerar ett värde som Round (exp (Uniform (låg, hög))/q) * q
* `qnormal(mu, sigma, q)` -Returnerar ett värde som Round (normal (MU, Sigma)/q) * q
* `qlognormal(mu, sigma, q)` -Returnerar ett värde som Round (exp (normal (MU, Sigma))/q) * q

#### <a name="continuous-hyperparameters"></a>Kontinuerliga egenskaper 

Kontinuerliga disponeringsparametrarna anges som en distribution över ett kontinuerligt intervall med värden. Distributioner som stöds är:

* `uniform(low, high)` -Returnerar ett värde enhetligt fördelat mellan låg och hög
* `loguniform(low, high)` -Returnerar ett värde som ritats enligt exp (Uniform (låg, hög)) så att logaritmen för returvärdet är enhetligt distribuerad
* `normal(mu, sigma)` – Returnerar ett verkligt värde som normalt distribueras med medelvärdet My och standard avvikelsen Sigma
* `lognormal(mu, sigma)` -Returnerar ett värde som ritats enligt exp (normal (MU, Sigma)) så att logaritmen för returvärdet normalt distribueras

Ett exempel på en parameter utrymmes definition:

```Python
    {    
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1)
    }
```

Den här koden definierar ett Sök utrymme med två parametrar – `learning_rate` och `keep_probability` . `learning_rate` har en normal distribution med medel värde 10 och en standard avvikelse på 3. `keep_probability` har en enhetlig distribution med ett lägsta värde på 0,05 och ett högsta värde på 0,1.

### <a name="sampling-the-hyperparameter-space"></a>Sampling av områdets parameter utrymme

Du kan också ange parameter samplings metoden som ska användas över definitions området för den här parametern. Azure Machine Learning stöder slumpmässig provtagning, rutnäts sampling och Bayesian-sampling.

#### <a name="picking-a-sampling-method"></a>Plocka en samplings metod

* Rutnäts sampling kan användas om ditt områdes parameter utrymme kan definieras som ett alternativ bland diskreta värden och om du har tillräcklig budget för att söka igenom alla värden i det definierade Sök utrymmet. Dessutom kan en automatiserad avstängning av dåligt utförande, vilket minskar svinnt av resurser.
* Med slumpmässig sampling kan du ta med både diskreta och kontinuerliga grundparametrar. I praktiken ger den bra resultat i de flesta tider och kan också användas för att automatisera tidig avstängning av dåligt utförda körningar. Vissa användare utför en inledande sökning med slumpmässig sampling och förfina sedan Sök utrymmet iterativt för att förbättra resultaten.
* Bayesian-samplingen använder kunskaper om tidigare exempel när du väljer värden för en parameter, vilket effektivt försöker förbättra det rapporterade primära måttet. Bayesian-sampling rekommenderas när du har tillräckligt med budget för att utforska området för den övre parametern, för bästa resultat med Bayesian-sampling rekommenderar vi att du använder ett maximalt antal körningar som är större än eller lika med 20 gånger antalet som är justerade. Observera att Bayesian-sampling för närvarande inte stöder någon tidig avslutnings princip.

#### <a name="random-sampling"></a>Slumpmässig provtagning

I slumpmässig sampling väljs värdena för båda parametrarna slumpmässigt från det definierade Sök utrymmet. Med [slumpmässig sampling](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.randomparametersampling?view=azure-ml-py&preserve-view=true) kan Sök utrymmet innehålla både diskreta och kontinuerliga disponeringsparametrarna.

```Python
from azureml.train.hyperdrive import RandomParameterSampling
from azureml.train.hyperdrive import normal, uniform, choice
param_sampling = RandomParameterSampling( {
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```

#### <a name="grid-sampling"></a>Rutnäts sampling

Vid [Rutnäts sampling](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.gridparametersampling?view=azure-ml-py&preserve-view=true) utförs en enkel rutnäts sökning över alla genomförbara värden i det definierade Sök utrymmet. Det går endast att använda med de angivna de angivna egenskaperna `choice` . Följande utrymme har exempelvis totalt sex exempel:

```Python
from azureml.train.hyperdrive import GridParameterSampling
from azureml.train.hyperdrive import choice
param_sampling = GridParameterSampling( {
        "num_hidden_layers": choice(1, 2, 3),
        "batch_size": choice(16, 32)
    }
)
```

#### <a name="bayesian-sampling"></a>Bayesiansk sampling

[Bayesian-sampling](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.bayesianparametersampling?view=azure-ml-py&preserve-view=true) baseras på algoritmen för Bayesian-optimering och gör intelligenta val på de båda parametervärdena för att sampla härnäst. Exemplet hämtar exemplet baserat på hur föregående exempel utfördes, så att det nya exemplet förbättrar det rapporterade primära måttet.

När du använder Bayesian-sampling, har antalet samtidiga körningar påverka effektiviteten i justerings processen. Vanligt vis kan ett mindre antal samtidiga körningar leda till bättre provtagnings konvergens, eftersom den mindre graden av parallellitet ökar antalet körningar som förmånen från tidigare slutförda körningar.

Bayesian-sampling stöder bara `choice` , `uniform` och `quniform` distributioner över Sök utrymmet.

```Python
from azureml.train.hyperdrive import BayesianParameterSampling
from azureml.train.hyperdrive import uniform, choice
param_sampling = BayesianParameterSampling( {
        "learning_rate": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```

> [!NOTE]
> Bayesian-sampling stöder inte någon tidig avslutnings princip (se [Ange en princip för tidig avslutning](#early-termination)). När du använder Bayesian parameter sampling, anger `early_termination_policy = None` eller lämnar du `early_termination_policy` parametern.

## <a name="specify-primary-metric"></a><a name="specify-primary-metric-to-optimize"></a> Ange primärt mått

Ange det [primära mått](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.primarymetricgoal?view=azure-ml-py&preserve-view=true) som du vill att det bästa experimentet för att justera ska optimera. Varje tränings körning utvärderas för det primära måttet. Dåligt utförda körningar (där det primära måttet inte uppfyller villkoren som angetts i principen för tidig avslutning) avslutas. Förutom det primära mått namnet anger du även målet för optimeringen – om du vill maximera eller minimera det primära måttet.

* `primary_metric_name`: Namnet på det primära måttet som ska optimeras. Namnet på den primära mått filen måste matcha namnet på det mått som loggats av övnings skriptet. Se [logg mått för justering av en parameter](#log-metrics-for-hyperparameter-tuning).
* `primary_metric_goal`: Det kan vara antingen `PrimaryMetricGoal.MAXIMIZE` eller `PrimaryMetricGoal.MINIMIZE` och bestämmer om det primära måttet ska maximeras eller minimeras när körningarna utvärderas. 

```Python
primary_metric_name="accuracy",
primary_metric_goal=PrimaryMetricGoal.MAXIMIZE
```

Optimera körningarna för att maximera "noggrannhet".  Se till att logga det här värdet i ditt utbildnings skript.

### <a name="specify-primary-metric"></a><a name="log-metrics-for-hyperparameter-tuning"></a> Ange primärt mått

Utbildnings skriptet för din modell måste logga relevanta mått under modell träning. När du konfigurerar en inställning för den här parametern anger du det primära mått som ska användas för att utvärdera prestanda för körning. (Se [Ange ett primärt mått att optimera](#specify-primary-metric-to-optimize).)  I ditt utbildnings skript måste du logga det här måttet så att det är tillgängligt för justerings processen för den egna parametern.

Logga detta mått i ditt utbildnings skript med följande exempel-kodfragment:

```Python
from azureml.core.run import Run
run_logger = Run.get_context()
run_logger.log("accuracy", float(val_accuracy))
```

Övnings skriptet beräknar `val_accuracy` och loggar det som "noggrannhet", som används som primärt mått. Varje gången måttet loggas tas det emot av tjänsten för justering av tids parametrar. Det är upp till modell utvecklaren att avgöra hur ofta det här måttet ska rapporteras.

## <a name="specify-early-termination-policy"></a><a name="early-termination"></a> Ange princip för tidig avslutning

Avsluta dåligt utförande körs automatiskt med en princip för tidig avslutning. Terminering minskar svinn av resurser och använder i stället dessa resurser för att utforska andra parameter konfigurationer.

När du använder en tidig avslutnings princip kan du konfigurera följande parametrar som styr när en princip tillämpas:

* `evaluation_interval`: frekvensen för att tillämpa principen. Varje tillfälle ska tränings skriptet logga in det primära måttet som ett intervall. Det innebär att en `evaluation_interval` av 1 tillämpar principen varje gång utbildnings skriptet rapporterar det primära måttet. En `evaluation_interval` av 2 tillämpar principen varannan gång som tränings skriptet rapporterar det primära måttet. Om inget värde anges `evaluation_interval` anges värdet 1 som standard.
* `delay_evaluation`: fördröjer den första princip utvärderingen för ett angivet antal intervall. Det är en valfri parameter som gör att alla konfigurationer kan köras i ett ursprungligt minsta antal intervall, för att undvika att utbildningar avslutas för tidigt. Om det här alternativet anges gäller principen varje multipel av evaluation_interval som är större än eller lika med delay_evaluation.

Azure Machine Learning stöder följande tidiga avslutnings principer.

### <a name="bandit-policy"></a>Bandit-princip

[Bandit](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.banditpolicy?view=azure-ml-py#&preserve-view=truedefinition) är en avslutnings princip som baseras på slack-faktorn/slack och utvärderings intervallet. Principen tidigt avslutar alla körningar där det primära måttet inte ligger inom den angivna slack-faktorn/slacket med avseende på bästa möjliga inlärnings körning. Det tar följande konfigurations parametrar:

* `slack_factor` eller `slack_amount` : det tillåtna slacket i förhållande till bästa möjliga utbildning körs. `slack_factor` anger det tillåtna slacket som ett förhållande. `slack_amount` anger det tillåtna slacket som ett absolut belopp, i stället för en kvot.

    Anta till exempel att en bandit-princip används vid intervall 10. Anta att den bästa presterande körningen vid intervall 10 rapporterade ett primärt mått 0,8 med målet att maximera det primära måttet. Om principen har angetts med `slack_factor` 0,2, kommer all utbildning som körs, vars bästa mått vid intervallet 10 är mindre än 0,66 (0,8/(1 + `slack_factor` )) att avslutas. I stället angavs principen med `slack_amount` 0,2, vilken utbildning som helst, vars bästa mått vid intervallet 10 är mindre än 0,6 (0,8-) avbryts `slack_amount` .
* `evaluation_interval`: frekvensen för att tillämpa principen (valfri parameter).
* `delay_evaluation`: fördröjer den första princip utvärderingen för ett angivet antal intervall (valfri parameter).


```Python
from azureml.train.hyperdrive import BanditPolicy
early_termination_policy = BanditPolicy(slack_factor = 0.1, evaluation_interval=1, delay_evaluation=5)
```

I det här exemplet tillämpas principen för tidig avslutning vid varje intervall när mått rapporteras, från och med utvärderings intervallet 5. Alla körningar vars bästa mått är mindre än (1/(1 + 0,1) eller 91% av den bästa körningen avbryts.

### <a name="median-stopping-policy"></a>Median stoppar princip

[Median stopp](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.medianstoppingpolicy?view=azure-ml-py&preserve-view=true) är en princip för tidig avslutning som baseras på löpande medelvärden av primära mått som rapporteras av körningarna. Den här principen beräknar löpande medelvärden för alla utbildnings körningar och avslutar körningar vars prestanda är sämre än median värdet för de löpande medelvärdena. Den här principen använder följande konfigurations parametrar:
* `evaluation_interval`: frekvensen för att tillämpa principen (valfri parameter).
* `delay_evaluation`: fördröjer den första princip utvärderingen för ett angivet antal intervall (valfri parameter).


```Python
from azureml.train.hyperdrive import MedianStoppingPolicy
early_termination_policy = MedianStoppingPolicy(evaluation_interval=1, delay_evaluation=5)
```

I det här exemplet tillämpas principen för tidig avslutning vid varje intervall som börjar med utvärderings intervallet 5. En körning avslutas med intervallet 5 om det bästa primära måttet är sämre än median värdet för de löpande medelvärdena över intervall 1:5 för alla utbildnings körningar.

### <a name="truncation-selection-policy"></a>Princip för att välja trunkering

[Avtrunkering av markering](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.truncationselectionpolicy?view=azure-ml-py&preserve-view=true) avbryter en viss procent andel av de lägsta körnings körningarna vid varje utvärderings intervall. Körningarna jämförs baserat på deras prestanda på det primära måttet och de lägsta X% avslutas. Det tar följande konfigurations parametrar:

* `truncation_percentage`: procent andelen av de lägsta körnings körningarna som avslutas vid varje utvärderings intervall. Ange ett heltals värde mellan 1 och 99.
* `evaluation_interval`: frekvensen för att tillämpa principen (valfri parameter).
* `delay_evaluation`: fördröjer den första princip utvärderingen för ett angivet antal intervall (valfri parameter).


```Python
from azureml.train.hyperdrive import TruncationSelectionPolicy
early_termination_policy = TruncationSelectionPolicy(evaluation_interval=1, truncation_percentage=20, delay_evaluation=5)
```

I det här exemplet tillämpas principen för tidig avslutning vid varje intervall som börjar med utvärderings intervallet 5. En körning avslutas med intervall 5 om dess prestanda vid intervall 5 är i den lägsta 20% av prestandan för alla körningar i intervallet 5.

### <a name="no-termination-policy"></a>Ingen avslutnings princip

Om du vill att all utbildning ska köras för att slutföras, anger du principen till ingen. Detta kommer att leda till att du inte tillämpar någon tidig avslutnings princip.

```Python
policy=None
```

### <a name="default-policy"></a>Standard princip

Om ingen princip har angetts, kommer tjänsten för egenskaps justering att låta all utbildning köras på Slutför.

### <a name="picking-an-early-termination-policy"></a>Plocka en princip för tidig avslutning

* Om du letar efter en restriktiv princip som ger besparingar utan att avsluta löftes jobb, kan du använda en princip för att stoppa en median med `evaluation_interval` 1 och `delay_evaluation` 5. Dessa är restriktiva inställningar som kan ge cirka 25%-35% besparingar utan förlust av primärt mått (baserat på våra utvärderings data).
* Om du vill ha mer aggressiva besparingar från tidiga uppsägningar kan du antingen använda bandit-principen med en striktare (mindre) tillåten slack eller trunkering princip för urvals princip med en större trunkning i procent.

## <a name="allocate-resources"></a>Allokera resurser

Kontrol lera din resurs budget för ditt bästa experiment genom att ange det maximala totala antalet utbildningar som körs.  Du kan också ange den maximala varaktigheten för experimentet med en parameter justering.

* `max_total_runs`: Det högsta antalet utbildnings körningar som ska skapas. Övre gräns – det kan finnas färre körningar, till exempel om det finns ett begränsat parameter utrymme och har färre sampel. Måste vara ett tal mellan 1 och 1000.
* `max_duration_minutes`: Maximal varaktighet i minuter för experimentet med tids inställning. Parametern är valfri, och om den är tillgänglig avbryts alla körningar som körs efter den varaktigheten automatiskt.

>[!NOTE] 
>Om både `max_total_runs` och `max_duration_minutes` anges, avbryts experimentets inställnings experiment när den första av dessa två tröskelvärden uppnås.

Dessutom kan du ange det maximala antalet körnings körningar som ska köras samtidigt under justerings sökningen för din parameter.

* `max_concurrent_runs`: Det högsta antalet körningar som körs samtidigt vid en givet tillfälle. Om inget anges kommer alla `max_total_runs` att startas parallellt. Om det anges måste det vara ett tal mellan 1 och 100.

>[!NOTE] 
>Antalet samtidiga körningar är gated på de resurser som är tillgängliga i det angivna beräknings målet. Därför måste du se till att Compute-målet har de tillgängliga resurserna för önskad samtidighet.

Allokera resurser för inställning av min parameter:

```Python
max_total_runs=20,
max_concurrent_runs=4
```

Den här koden konfigurerar experimentet för inställning av parameter till att använda maximalt 20 Totalt antal körningar som kör fyra konfigurationer i taget.

## <a name="configure-experiment"></a>Konfigurera experiment

[Konfigurera ditt för inställnings](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.hyperdriverunconfig?view=azure-ml-py&preserve-view=true) experiment med det definierade områdets sökområde, tidig avslutnings princip, primärt mått och resursallokering från avsnitten ovan. Ange dessutom ett `estimator` som kommer att anropas med de exempelbaserade parametrarna. `estimator`Beskriver det utbildnings skript som du kör, resurserna per jobb (Single eller multi-GPU) och beräknings målet som ska användas. Eftersom samtidighet för ditt preparameter-experiment är gated på resurserna som är tillgängliga, se till att det beräknings mål som anges i `estimator` har tillräckligt med resurser för önskad samtidighet. (Mer information om uppskattningar finns i [så här tränar du modeller](how-to-train-ml-models.md).)

Konfigurera ditt inställnings experiment för en parameter:

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

När du har definierat konfigurationen för konfiguration av den egna parametern kan du [Skicka ett experiment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment%28class%29?view=azure-ml-py#&preserve-view=truesubmit-config--tags-none----kwargs-):

```Python
from azureml.core.experiment import Experiment
experiment = Experiment(workspace, experiment_name)
hyperdrive_run = experiment.submit(hyperdrive_run_config)
```

`experiment_name` är det namn som du tilldelar till ditt för inställnings experiment med din parameter och `workspace` är den arbets yta där du vill skapa experimentet (mer information om experiment finns i [hur fungerar Azure Machine Learning?](concept-azure-machine-learning-architecture.md))

## <a name="warm-start-your-hyperparameter-tuning-experiment-optional"></a>Varm kom igång med ditt inställnings experiment för en parameter (valfritt)

Ofta kan det vara en iterativ process att hitta bästa möjliga parameter värden för din modell, vilket kräver flera justerings körningar som lär sig från tidigare justerings körningar i en parameter. Om du återanvänder kunskaper från dessa tidigare körningar påskyndas justerings processen för en parameter, vilket minskar kostnaden för att justera modellen och kan förbättra den resulterande modellens primära mått. När du startar en inställning för en parameter justering med Bayesian-sampling, används försök från föregående körning som tidigare kunskap för att på ett intelligent sätt välja nya exempel för att förbättra det primära måttet. När slumpmässiga eller rutnäts sampling används, kommer eventuella beslut om besluts fattande att dra nytta av mått från de tidigare körningarna för att fastställa dåligt utförda utbildnings körningar. 

Azure Machine Learning ger dig möjlighet att starta din egenskaps justering för din egen parameter genom att dra nytta av kunskap från upp till 5 tidigare slutförda/avbrutna konfigurations inställningar för den överordnade parametern. Du kan ange listan över överordnade körningar som du vill komma igång med att använda kodfragmentet:

```Python
from azureml.train.hyperdrive import HyperDriveRun

warmstart_parent_1 = HyperDriveRun(experiment, "warmstart_parent_run_ID_1")
warmstart_parent_2 = HyperDriveRun(experiment, "warmstart_parent_run_ID_2")
warmstart_parents_to_resume_from = [warmstart_parent_1, warmstart_parent_2]
```

Det kan också uppstå tillfällen när enskilda utbildningar körs på ett justerings experiment med en parameter för att avbrytas på grund av budget begränsningar eller Miss lyckas på grund av andra orsaker. Det är nu möjligt att återuppta en sådan enskild utbildning som körs från den senaste kontroll punkten (förutsatt att ditt utbildnings skript hanterar kontroll punkter). Om du återupptar en enskild övnings körning används samma konfiguration för den egna katalogen och den mappen utdata som används för den körs. Övnings skriptet bör godkänna `resume-from` argumentet, som innehåller de kontroll punkter eller modeller som används för att återuppta inlärnings körningen. Du kan återuppta enskilda inlärnings körningar med följande kodfragment:

```Python
from azureml.core.run import Run

resume_child_run_1 = Run(experiment, "resume_child_run_ID_1")
resume_child_run_2 = Run(experiment, "resume_child_run_ID_2")
child_runs_to_resume = [resume_child_run_1, resume_child_run_2]
```

Du kan konfigurera ditt inställnings experiment för att komma igång med ett tidigare experiment eller återuppta enskilda inlärnings körningar med valfria parametrar `resume_from` och `resume_child_runs` i konfigurationen:

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

Azure Machine Learning SDK innehåller en [anteckningsbok-widget](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets.rundetails?view=azure-ml-py&preserve-view=true) som visualiserar förloppet för din utbildning körs. Följande kodfragment visualiserar alla dina för inställnings justeringar på en plats i en Jupyter Notebook:

```Python
from azureml.widgets import RunDetails
RunDetails(hyperdrive_run).show()
```

Den här koden visar en tabell med information om inlärnings körningarna för var och en av konfigurationerna för de olika parametrarna.

![justerings tabell för grundparameter](./media/how-to-tune-hyperparameters/HyperparameterTuningTable.png)

Du kan också visualisera prestanda för var och en av körningarna när inlärningen fortskrider. 

![ritytans justerings område](./media/how-to-tune-hyperparameters/HyperparameterTuningPlot.png)

Du kan dessutom visuellt identifiera korrelationen mellan prestanda och värden för enskilda egenskaper med hjälp av ett parallellt koordinat diagram. 

[![parametrar för att justera parallella koordinater](./media/how-to-tune-hyperparameters/HyperparameterTuningParallelCoordinates.png)](media/how-to-tune-hyperparameters/hyperparameter-tuning-parallel-coordinates-expanded.png)

Du kan visualisera alla dina inställnings justeringar för din parameter i Azure-webbportalen också. Mer information om hur du visar ett experiment i webb portalen finns i [så här spårar du experiment](how-to-monitor-view-training-logs.md#view-the-experiment-in-the-web-portal).

## <a name="find-the-best-model"></a>Hitta den bästa modellen

När alla konfigurations inställningar för den här parametern har slutförts, [identifierar du bästa möjliga konfiguration](/python/api/azureml-train-core/azureml.train.hyperdrive.hyperdriverun?view=azure-ml-py#&preserve-view=trueget-best-run-by-primary-metric-include-failed-true--include-canceled-true--include-resume-from-runs-true-----typing-union-azureml-core-run-run--nonetype-) och motsvarande värden för båda parametrarna:

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

## <a name="sample-notebook"></a>Exempel på Notebook
Referera till träna-Real-parameter-* Notebooks i den här mappen:
* [How-to-use-azureml/Training-with-djupgående-Learning](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Nästa steg
* [Spåra ett experiment](how-to-track-experiments.md)
* [Distribuera en tränad modell](how-to-deploy-and-where.md)
