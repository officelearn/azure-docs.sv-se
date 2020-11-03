---
title: Planera och hantera kostnader
titleSuffix: Azure Machine Learning
description: Planera och hantera kostnader för Azure Machine Learning med kostnads analys i Azure Portal. När du skapar maskin inlärnings modeller bör du lära dig ytterligare kostnads besparingar för att sänka kostnaderna.
author: sdgilley
ms.author: sgilley
ms.custom: subject-cost-optimization
ms.reviewer: nigup
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: f73643baa92956280d67d0d931459af3a80925ee
ms.sourcegitcommit: bbd66b477d0c8cb9adf967606a2df97176f6460b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93233756"
---
# <a name="plan-and-manage-costs-for-azure-machine-learning"></a>Planera och hantera kostnader för Azure Machine Learning

I den här artikeln beskrivs hur du planerar och hanterar kostnader för Azure Machine Learning. Först använder du pris Kalkylatorn för Azure för att planera för kostnader innan du lägger till några resurser. När du lägger till Azure-resurserna granskar du de uppskattade kostnaderna. Använd slutligen kostnads besparingar när du tränar din modell med hanterade Azure Machine Learning beräknings kluster.

När du har börjat använda Azure Machine Learning-resurser kan du använda funktionerna för kostnads hantering för att ställa in budgetar och övervaka kostnader. Granska även de prognostiserade kostnaderna och identifiera utgifts trender för att identifiera områden där du kanske vill handla.

Ta reda på att kostnaderna för Azure Machine Learning bara är en del av månads kostnaderna på din Azure-faktura. Om du använder andra Azure-tjänster debiteras du för alla Azure-tjänster och-resurser som används i din Azure-prenumeration, inklusive tjänster från tredje part. Den här artikeln beskriver hur du planerar för och hanterar kostnader för Azure Machine Learning. När du är bekant med att hantera kostnader för Azure Machine Learning bör du använda liknande metoder för att hantera kostnader för alla Azure-tjänster som används i din prenumeration.

När du tränar dina Machine Learning-modeller använder du hanterade Azure Machine Learning beräknings kluster för att dra nytta av mer kostnads besparingar:

* Konfigurera utbildnings kluster för automatisk skalning
* Ange kvoter för din prenumeration och dina arbets ytor
* Ange avslutnings principer för din utbildnings körning
* Använd virtuella datorer med låg prioritet (VM)
* Använd en Azure-reserverad VM-instans

## <a name="prerequisites"></a>Krav

Kostnadsanalys stöder en mängd olika typer av Azure-konton. Om du vill se hela listan med kontotyper som stöds kan du läsa [Förstå Cost Management-data](../cost-management-billing/costs/understand-cost-mgt-data.md). Om du vill visa kostnadsdata behöver du minst läsbehörighet för ditt Azure-konto. 

Mer information om hur du får åtkomst till Azure Cost Management finns i [Tilldela åtkomst till data](../cost-management-billing/costs/assign-access-acm-data.md).

## <a name="estimate-costs"></a>Uppskatta kostnader

Använd [pris Kalkylatorn för Azure](https://azure.microsoft.com/pricing/calculator/) för att beräkna kostnaderna innan du skapar resurserna i ett Azure Machine Learning konto. Välj **AI + Machine Learning** till vänster och välj sedan **Azure Machine Learning** för att börja.  

Följande skärm bild visar kostnads uppskattningen med hjälp av Kalkylatorn:

:::image type="content" source="media/concept-plan-manage-cost/capacity-calculator-cost-estimate.png" alt-text="Kostnads uppskattning i Azure kalkylatorn":::

När du lägger till nya resurser i din arbets yta går du tillbaka till den här kalkylatorn och lägger till samma resurs här för att uppdatera dina kostnads beräkningar.

Mer information finns i [Azure Machine Learning prissättning](https://azure.microsoft.com/pricing/details/machine-learning/).

## <a name="get-cost-alerts"></a>Hämta kostnads aviseringar

Skapa [budgetar](../cost-management/tutorial-acm-create-budgets.md) för att hantera kostnader och skapa [aviseringar](../cost-management/cost-mgt-alerts-monitor-usage-spending.md) som automatiskt meddelar intressenter om utgifts avvikelser och överförbrukning av risker. Aviseringar baseras på utgifter jämfört med budget- och kostnadströsklar. Budgetar och aviseringar skapas för Azure-prenumerationer och resurs grupper, så de är användbara som en del av en övergripande kostnads övervaknings strategi. Budgetar och aviseringar kan dock ha begränsade funktioner för att hantera enskilda kostnader för Azure-tjänster eftersom de är utformade för att spåra kostnader på en högre nivå.

## <a name="monitor-costs"></a>Övervaka kostnader

När du använder resurser med Azure Machine Learning debiteras du kostnader. Kostnaderna för Azure Resource Usage varierar beroende på tidsintervaller (sekunder, minuter, timmar och dagar) eller med användning av begär ande enhets användning. Så snart som användningen av Azure Machine Learning startar debiteras kostnaderna. Visa dessa kostnader i fönstret [kostnads analys](../cost-management/quick-acm-cost-analysis.md) i Azure Portal.

Du kan visa kostnader i grafer och tabeller i olika tidsintervall. Du kan också Visa kostnader för budgetar och prognostiserade kostnader. Genom att växla till längre vyer över tid kan du identifiera utgifts trender och se var överförbrukningen kan ha inträffat. Om du har skapat budgetar, se var de överskreds.  

Du kan inte se ett separat tjänst utrymme för Machine Learning.  I stället ser du de olika resurser som du har lagt till i dina Machine Learning-arbetsytor. 

Observera att medan själva Machine Learning-arbetsytan inte har direkt kostnad debiteras du på hanterade beräknings resurser. Om du har lagt till taggar till arbets ytorna, eftersom det inte finns någon direkt kostnad för den, så återspeglar fönstret kostnads analys detta för arbets ytan. För korrekt kostnads hantering via Taggar måste du tagga de associerade beräknings resurserna.  

## <a name="use-azure-machine-learning-compute-cluster-amlcompute"></a>Använda Azure Machine Learning Compute Cluster (AmlCompute)

Med ständigt föränderliga data behöver du snabb och strömlinjeformad modell utbildning och omträning för att upprätthålla korrekta modeller. Kontinuerlig utbildning kommer dock till en kostnad, särskilt för djup inlärnings modeller på GPU: er. 

Azure Machine Learning användare kan använda det hanterade Azure Machine Learning Compute Cluster, även kallat AmlCompute. AmlCompute stöder olika GPU-och CPU-alternativ. AmlCompute är internt värd för prenumerations uppdrag genom Azure Machine Learning. Det ger samma säkerhet, efterlevnad och styrning i företags klass med Azure IaaS Cloud Scale.

Eftersom dessa Compute-pooler finns inuti Azures IaaS-infrastruktur kan du distribuera, skala och hantera din utbildning med samma krav på säkerhet och efterlevnad som resten av infrastrukturen.  Dessa distributioner sker i din prenumeration och följer dina styrnings regler. Läs mer om [Azure Machine Learning Compute](how-to-create-attach-compute-cluster.md).

## <a name="configure-training-clusters-for-autoscaling"></a>Konfigurera utbildnings kluster för automatisk skalning

Automatisk skalning av kluster baserat på kraven i din arbets belastning hjälper dig att minska kostnaderna så att du bara använder det du behöver.

AmlCompute-kluster har utformats för att skalas dynamiskt baserat på din arbets belastning. Klustret kan skalas upp till det maximala antalet noder som du konfigurerar. När varje körning slutförts släpper klustret noderna och skalar till det konfigurerade antalet noder.

[!INCLUDE [min-nodes-note](../../includes/machine-learning-min-nodes.md)]

Du kan också konfigurera hur lång tid noden är inaktiv innan den skalas ned. Som standard är inaktiv tid före nedskalning inställd på 120 sekunder.

+ Om du utför mindre iterativ experimentering bör du minska den här tiden för att spara kostnaderna.
+ Om du utför en mycket iterativ utveckling/testning experimentering kan du behöva öka tiden så att du inte betalar för kontinuerlig skalning upp och ned efter varje ändring i ditt utbildnings skript eller miljö.

AmlCompute-kluster kan konfigureras för att ändra arbets belastnings kraven i Azure Portal med hjälp av [AMLCOMPUTE SDK-klassen](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py&preserve-view=true), [AmlCompute CLI](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute)med [REST-API: er](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/machinelearningservices/resource-manager/Microsoft.MachineLearningServices/stable).

```azurecli
az ml computetarget create amlcompute --name testcluster --vm-size Standard_NC6 --min-nodes 0 --max-nodes 5 --idle-seconds-before-scaledown 300
```

## <a name="set-quotas-on-resources"></a>Ange kvoter för resurser

AmlCompute levereras med en [kvot konfiguration (eller gräns)](how-to-manage-quotas.md#azure-machine-learning-compute). Den här kvoten är av VM-serien (till exempel Dv2-serien, NCv3-serien) och varierar beroende på region för varje prenumeration. Prenumerationer börjar med små standardvärden och hjälper dig att komma igång, men Använd den här inställningen för att kontrol lera hur mycket Amlcompute resurser som är tillgängliga för att kunna användas i din prenumeration. 

Konfigurera även [kvoten för arbets ytan med VM-serien](how-to-manage-quotas.md#workspace-level-quotas)för varje arbets yta i en prenumeration. På så sätt kan du ha mer detaljerad kontroll över kostnaderna som varje arbets yta kan ådra sig och begränsa vissa VM-familjer. 

Om du vill ange kvoter på arbets ytans nivå börjar du med [Azure Portal](https://portal.azure.com).  Välj en arbets yta i din prenumeration och välj **användnings områden + kvoter** i det vänstra fönstret. Välj sedan fliken **Konfigurera kvoter** för att Visa kvoterna. Du måste ha behörighet för prenumerations omfånget för att kunna ange kvoten, eftersom det är en inställning som påverkar flera arbets ytor.

## <a name="set-run-autotermination-policies"></a>Ange kör principer för autoavslutning 

I vissa fall bör du konfigurera dina utbildnings körningar för att begränsa deras varaktighet eller avsluta dem tidigt. Till exempel när du använder Azure Machine Learning den inbyggda inställningen för min parameter eller Automatisk maskin inlärning.

Här följer några alternativ som du har:
* Definiera en parameter som anropas `max_run_duration_seconds` i din RunConfiguration för att styra den längsta tid som en körning kan utökas till den beräkning du väljer (antingen lokal eller fjärrstyrd moln beräkning).
* För [justering](how-to-tune-hyperparameters.md#early-termination)av den här inställningen definierar du en princip för tidig avslutning från en bandit-princip, en princip för att stoppa en median eller en princip för avtrunkering. Om du vill kontrol lera om det finns ytterligare kontroller av de flesta parametrar använder du parametrar som `max_total_runs` eller `max_duration_minutes` .
* För [Automatisk maskin inlärning](how-to-configure-auto-train.md#exit)ställer du in liknande avslutnings principer med hjälp av  `enable_early_stopping` flaggan. Använd också egenskaper som `iteration_timeout_minutes` och `experiment_timeout_minutes` för att kontrol lera maximal varaktighet för en körning eller för hela experimentet.

## <a name="use-low-priority-vms"></a><a id="low-pri-vm"></a> Använd virtuella datorer med låg prioritet

Med Azure kan du använda överflödigt outnyttjad kapacitet som Low-Priority virtuella datorer i skalnings uppsättningar för virtuella datorer, batch och tjänsten Machine Learning. Dessa allokeringar är emptible men kommer till ett reducerat pris jämfört med dedikerade virtuella datorer. I allmänhet rekommenderar vi att du använder Low-Priority virtuella datorer för batch-arbetsbelastningar. Du bör också använda dem där avbrott kan återskapas antingen via omsändningar (för batch-Inferencing) eller genom omstarter (för djup inlärnings utbildning med kontroll punkt).

Low-Priority virtuella datorer har en enda kvot som är separat från det dedikerade kvot svärdet, som ingår i VM-serien. Läs [mer om AmlCompute-kvoter](how-to-manage-quotas.md).

 Low-Priority virtuella datorer fungerar inte för beräknings instanser eftersom de behöver stöd för interaktiva Notebook-upplevelser.

## <a name="use-reserved-instances"></a>Använda reserverade instanser

Ett annat sätt att spara pengar på beräknings resurser är Azure reserverad VM-instans. Med detta erbjudande genomför du ett år eller tre års villkor. Dessa rabatter är upp till 72% av priserna för betala per användning och tillämpas direkt på din månatliga Azure-faktura.

Azure Machine Learning Compute har stöd för reserverade instanser. Om du köper en räkenskapsårs-eller tre-års reserverad instans kommer vi automatiskt att tillämpa rabatt mot din Azure Machine Learning hanterade beräkning.


## <a name="next-steps"></a>Nästa steg

Läs mer om:
* [Hantera och öka resurs kvoter](how-to-manage-quotas.md)
* [Hantera kostnader med kostnads analys](../cost-management-billing/costs/quick-acm-cost-analysis.md).
* Skapa Azure Machine Learning Compute med [SDK](how-to-create-attach-compute-cluster.md) eller i [Studio](how-to-create-attach-compute-studio.md#amlcompute).
