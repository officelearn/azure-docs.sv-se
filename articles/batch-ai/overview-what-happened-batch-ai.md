---
title: Vad händer med Azure Batch AI? | Microsoft Docs
description: Läs om vad som händer med Azure Batch AI och beräkningsalternativen i Azure Machine Learning-tjänsten.
ms.service: batch-ai
services: batch-ai
ms.topic: overview
ms.author: jmartens
author: j-martens
ms.date: 2/28/2019
ms.openlocfilehash: edd6a7e5f385d766d51631d77f5889233af2469a
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/01/2019
ms.locfileid: "57194510"
---
# <a name="whats-happening-to-azure-batch-ai"></a>Vad händer med Azure Batch AI?

**Azure Batch AI-tjänsten tas ur bruk i mars.** De skalenliga tränings- och bedömningsfunktionerna i Batch AI är nu tillgängliga i [Azure Machine Learning Services](../machine-learning/service/overview-what-is-azure-ml.md), som blev allmänt tillgänglig den 4 december 2018.

Tillsammans med många andra funktioner för maskininlärning innehåller Azure Machine Learning-tjänsten ett molnbaserat hanterat beräkningsmål för att träna, distribuera och bedöms maskininlärningsmodeller. Den här beräkningsmålet kallas för [Azure Machine Learning Compute](../machine-learning/service/how-to-set-up-training-targets.md#amlcompute). [Börja migrera och använda det idag](#migrate). Du kan interagera med Azure Machine Learning-tjänsten via dess [Python SDK: er](../machine-learning/service/quickstart-create-workspace-with-python.md), kommandoradsgränssnitt och [Azure Portal](../machine-learning/service/quickstart-get-started.md).

## <a name="support-timeline"></a>Supporttidslinje

Just nu, kan du använda din befintliga Azure Batch AI-prenumerationer som innan. Men inga **nya prenumerationer** är möjliga och inga nya investeringar görs.

Startar mars&nbsp;31&#x2c;&nbsp;2019, oanvända Batch AI-prenumerationer fungerar inte längre.

## <a name="compare-to-azure-machine-learning"></a>Jämför med Azure Machine Learning
Det är en molntjänst som du använder för att träna, distribuera, automatisera och hantera maskininlärningsmodeller i den omfattande skala som molnet ger. Få en större förståelse för [Azure Machine Learning Service i den här översikten](../machine-learning/service/overview-what-is-azure-ml.md).
 

En vanlig livscykel för modellutveckling innefattar förberedelse av data, träning och experimentering samt en distributionsfas. Du kan dirigera cykeln från slutpunkt till slutpunkt med hjälp av Machine Learning-pipelines.

![Flödesdiagram](./media/overview-what-happened-batch-ai/lifecycle.png)


[Mer information om hur tjänsten fungerar och dess huvudsakliga begrepp](../machine-learning/service/concept-azure-machine-learning-architecture.md). Många av begreppen i modellens träningsarbetsflöde liknar befintliga begrepp i Batch AI. 

Här visas en mappning av hur du kan tänka på dem mer specifikt:
 
|Batch AI-tjänst|  Azure Machine Learning-tjänst|
|:--:|:---:|
|Arbetsyta|Arbetsyta|
|Kluster|   Beräkning av typen `AmlCompute`|
|Filservrar|DataStores|
|Experiment|Experiment|
|Jobb|Körningar (tillåter kapslade körningar)|
 
Här är en annan vy i samma tabell som hjälper dig att visualisera ytterligare:
 
### <a name="batch-ai-hierarchy"></a>Batch AI-hierarki
![Flödesdiagram](./media/overview-what-happened-batch-ai/batchai-heirarchy.png) 
 
### <a name="azure-machine-learning-service-hierarchy"></a>Azure Machine Learning-tjänsten hierarki
![Flödesdiagram](./media/overview-what-happened-batch-ai/azure-machine-learning-service-heirarchy.png) 

## <a name="platform-capabilities"></a>Plattformsfunktioner
I Azure Machine Learning Service finns en uppsättning nya funktioner, inklusive en tränings->distributionsstack från slutpunkt till slutpunkt som du kan använda för din AI-utveckling utan att behöva hantera några Azure-resurser. I tabellen nedan jämförs funktioner som stöds för träning mellan de två tjänsterna.

|Funktion|Batch AI-tjänst|Azure Machine Learning-tjänst|
|-------|:-------:|:-------:|
|Alternativ för VM-storlek |CPU/GPU    |CPU/GPU. Stöder också FPGA för inferens|
|AI-förberett kluster (drivrutiner, Docker etc.)|  Ja |Ja|
|Nodförberedelse| Ja|    Nej|
|Val av operativsystemfamilj   |Delvis    |Nej|
|Dedikerade och virtuella LowPriority-datorer  |Ja    |Ja|
|Automatisk skalning   |Ja    |Ja (som standard)|
|Väntetid för automatisk skalning  |Nej |Ja|
|SSH    |Ja|   Ja|
|Montering på klusternivå |Ja (filresurser, blobar, NFS, anpassad)   |Ja (montera eller ladda ned ditt datalager)|
|Distribuerad träning|  Ja |Ja|
|Jobbkörningsläge|    Virtuell dator eller container|    Container|
|Anpassad containeravbildning|    Ja |Ja|
|Valfri verktygssats    |Ja    |Ja (kör Python-skript)|
|JobPreparation|    Ja |Inte ännu|
|Montering på jobbnivå |Ja (filresurser, blobar, NFS, anpassad)   |Ja (filresurser, blobar)|
|Jobbövervakning     |via GetJob|    via körningshistorik (mer omfattande information, anpassad körning för att få fler mått)|
|Hämta jobbloggar och filer/modeller |   via ListFiles och Storage-API:er  |via artefakttjänsten|
 |Stöd för Tensorboard   |Nej|    Ja|
|VM-familjens nivåkvoter |Ja    |Ja (med din tidigare kapacitet som flyttas framåt)|
 
Förutom den föregående tabellen finns det funktioner i Azure Machine Learning Service som normalt sett inte stöds i BatchAI.

|Funktion|Batch AI-tjänst|Azure Machine Learning-tjänst|
|-------|:-------:|:-------:|
|Förberedelse av miljö    |Nej |Ja (Conda-förberedelse och uppladdning till ACR)|
|Justering av hyperparameter  |Nej|    Ja|
|Modellhantering   |Nej |Ja|
|Driftsättning/distribution| Nej  |Via AKS och ACI|
|Förberedelse av data   |Nej |Ja|
|Beräkningsmål    |Virtuella Azure-datorer  |Lokal, BatchAI (som AmlCompute), DataBricks, HDInsight|
|Automatiserad maskininlärning |Nej|    Ja|
|Pipelines  |Nej |Ja|
|Batchbedömning  |Ja    |Ja|
|Portal/CLI-stöd|    Ja |Ja|


## <a name="programming-interfaces"></a>Programmeringsgränssnitt

Tabellen visar de olika programmeringsgränssnitt som är tillgängliga för varje tjänst.
    
|Funktion|BatchAI-tjänst|Azure Machine Learning-tjänst|
|-------|:-------:|:-------:|
|SDK    |Java, C#, Python, Nodejs   |Python (kör både konfigurations- och beräkningsbaserat för vanliga ramverk)|
|CLI    |Ja    |Inte ännu|
|Azure Portal   |Ja    |Ja (utom jobböverföring)|
|REST-API   |Ja    |Ja, men distribuerade över mikrotjänster|


Uppgradera från förhandsversion Batch AI till GA'ed Azure Machine Learning-tjänsten ger dig en bättre upplevelse igenom koncept som är enklare att använda, till exempel Estimators och datalager. Du har även garanterad kundsupport och SLA:er på GA-nivå för Azure-tjänsten.

Azure Machine Learning service medför också nya funktioner som automatisk machine learning, finjustering av hyperparametrar och ML pipelines, vilket är användbart i de flesta storskaliga AI-arbetsbelastningar. Möjligheten att distribuera en tränad modell utan att växla till en separat tjänst hjälper till att komplettera data science-slinga från förberedelse av data (med Data Prep SDK) hela vägen till driftsättning och övervakning av modellen.

<a name="migrate"></a>
## <a name="migrate"></a>Migrera

Lär dig hur du migrerar och hur den kod som du använder mappar till kod i Azure Machine Learning-tjänsten i den [migrera till Azure Machine Learning-tjänsten](how-to-migrate.md) artikeln.

## <a name="get-support"></a>Få support

Batch AI är som ska ta ur bruk 31 mars och redan blockerar nya prenumerationer från att registrera mot tjänsten om det inte är godkänd av utlöser ett undantag med support.  Kontakta oss på [Förhandsversionen av Azure Batch AI-träningen](mailto:AzureBatchAITrainingPreview@service.microsoft.com) om du har frågor eller feedback när du migrerar till Azure Machine Learning Service.

Microsoft Azure Machine Learning Service är nu allmänt tillgänglig. Det innebär att den innehåller ett dedikerat serviceavtal och olika supportplaner att välja bland.

Priser för att använda Azure-infrastrukturen via Batch AI-tjänsten eller via Azure Machine Learning-tjänsten ska inte varierar som debiterar vi bara priset för den underliggande beräkningskraften i båda fallen. Mer information finns på sidan med [priskalkylatorn](https://azure.microsoft.com/pricing/details/machine-learning-service/).

Visa regional tillgänglighet mellan de två tjänsterna i [Azure-portalen](https://azure.microsoft.com/global-infrastructure/services/?products=batch-ai,machine-learning-service&regions=all).


## <a name="next-steps"></a>Nästa steg

+ Lär dig [hur du migrerar](how-to-migrate.md) och hur du använder nu koden mappar till kod i Azure Machine Learning-tjänsten.

+ Läs [Översikt över Azure Machine Learning-tjänsten](../machine-learning/service/overview-what-is-azure-ml.md).

+ [Konfigurera ett beräkningsmål för modellträning](../machine-learning/service/how-to-set-up-training-targets.md) med Azure Machine Learning-tjänsten.

+ Granska [Azure-översikten](https://azure.microsoft.com/updates/) för att lära dig av andra Azure-tjänstuppdateringar.
