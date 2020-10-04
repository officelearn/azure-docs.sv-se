---
title: Vad är beräknings mål
titleSuffix: Azure Machine Learning
description: Definiera var du vill träna eller distribuera din modell med Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 09/29/2020
ms.openlocfilehash: ca23bb49a3592dcc139bcc04875f3867018e158d
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/04/2020
ms.locfileid: "91707750"
---
#  <a name="what-are-compute-targets-in-azure-machine-learning"></a>Vad är beräknings mål i Azure Machine Learning? 

Ett **beräknings mål** är en viss beräknings resurs/miljö där du kör ditt utbildnings skript eller är värd för tjänst distributionen. Den här platsen kan vara din lokala dator eller en molnbaserad beräknings resurs. Genom att använda beräknings mål kan du enkelt ändra beräknings miljön utan att behöva ändra koden.  

I en typisk modell utvecklings livs cykel kan du:
1. Börja med att utveckla och experimentera med en liten mängd data. I det här skedet rekommenderar vi din lokala miljö (lokal dator eller molnbaserad virtuell dator) som beräknings mål. 
2. Skala upp till större data eller distribuera utbildning med någon av dessa [inlärnings mål](#train).  
3. När modellen är klar distribuerar du den till en webb värd miljö eller IoT-enhet med någon av dessa [distributions mål](#deploy).

De beräknings resurser som du använder för beräknings målen är kopplade till en [arbets yta](concept-workspace.md). Andra beräknings resurser än den lokala datorn delas av användare av arbets ytan.

## <a name="training-compute-targets"></a><a name="train"></a> Inlärnings mål
Azure Machine Learning har varierande stöd för olika beräknings mål. En typisk modell utvecklings livs cykel börjar med utveckling/experiment på en liten mängd data. I det här skedet rekommenderar vi att du använder en lokal miljö. Till exempel din lokala dator eller en molnbaserad virtuell dator. När du skalar din utbildning på större data uppsättningar eller utför distribuerad träning rekommenderar vi att du använder Azure Machine Learning Compute för att skapa ett kluster med en eller flera noder som autoskalar varje gång du skickar en körning. Du kan också koppla din egen beräknings resurs, även om stöd för olika scenarier kan variera enligt beskrivningen nedan:

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]

Lär dig mer om hur du [skickar in en utbildning som körs till ett beräknings mål](how-to-set-up-training-targets.md).

## <a name="compute-targets-for-inference"></a><a name="deploy"></a> Beräknings mål för härledning

Följande beräknings resurser kan användas som värd för modell distributionen.

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

När du utför en härledning skapar Azure Machine Learning en Docker-behållare som är värd för modellen och tillhör ande resurser som krävs för att använda den. Den här behållaren används sedan i något av följande distributions scenarier:

* Som en __webb tjänst__ som används för real tids härledning. Webb tjänst distributioner använder något av följande beräknings mål:

    * [Lokal dator](how-to-attach-compute-targets.md#local)
    * [Azure Machine Learning-beräkningsinstans](how-to-create-manage-compute-instance.md)
    * [Azure Container Instances](how-to-attach-compute-targets.md#aci)
    * [Azure Kubernetes Services](how-to-create-attach-kubernetes.md)
    * Azure Functions (för hands version). Distribution till Azure Functions baseras bara på Azure Machine Learning för att bygga Docker-behållaren. Därifrån distribueras den med hjälp av Azure Functions. Mer information finns i [distribuera en maskin inlärnings modell till Azure Functions (för hands version)](how-to-deploy-functions.md).

* Som en slut punkt för __batch-härledning__ som används för att regelbundet bearbeta batchar med data. Batch-inferences använder [Azure Machine Learning beräknings kluster](how-to-create-attach-compute-cluster.md).

* Till en __IoT-enhet__ (för hands version). Distribution till en IoT-enhet förlitar sig bara på Azure Machine Learning för att bygga Docker-behållaren. Därifrån distribueras den med hjälp av Azure IoT Edge. Mer information finns i [distribuera som en IoT Edge modul (för hands version)](/azure/iot-edge/tutorial-deploy-machine-learning).

Lär dig [hur och hur du distribuerar din modell till ett beräknings mål](how-to-deploy-and-where.md).

<a name="amlcompute"></a>
## <a name="azure-machine-learning-compute-managed"></a>Azure Machine Learning Compute (hanterad)

En hanterad beräknings resurs skapas och hanteras av Azure Machine Learning. Den här beräkningen är optimerad för Machine Learning-arbetsbelastningar. Azure Machine Learning beräknings kluster och [beräknings instanser](concept-compute-instance.md) är de enda hanterade beräkningarna. 

Du kan skapa Azure Machine Learning beräknings instanser eller beräknings kluster från:
* [Azure Machine Learning Studio](how-to-create-attach-compute-studio.md)
* Python SDK och CLI:
    * [Beräkninsinstans](how-to-create-manage-compute-instance.md)
    * [Beräknings kluster](how-to-create-attach-compute-cluster.md)
* [R SDK](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-compute-targets) (för hands version)
* Resource Manager-mall. En exempel-mall finns i [create Azure Machine Learning Compute Template](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-amlcompute).
* Machine Learning- [tillägget för Azure CLI](reference-azure-machine-learning-cli.md#resource-management).  

När du skapar dessa beräknings resurser automatiskt en del av din arbets yta, till skillnad från andra typer av beräknings mål.


|Kapacitet  |Beräknings kluster  |Beräkninsinstans  |
|---------|---------|---------|
|Kluster med en eller flera noder     |    **&check;**       |         |
|Autoskalar varje gången du skickar en körning     |     **&check;**      |         |
|Automatisk kluster hantering och schemaläggning av jobb     |   **&check;**        |     **&check;**      |
|Stöd för både CPU-och GPU-resurser     |  **&check;**         |    **&check;**       |


> [!NOTE]
> När ett beräknings kluster är inaktivt skalas det till 0 noder, så du betalar inte när det inte används.  En beräknings *instans*är dock alltid aktive rad och har inte autoskalning.  Du bör [stoppa beräknings instansen](how-to-create-manage-compute-instance.md#manage) när du inte använder den för att undvika extra kostnader. 

### <a name="supported-vm-series-and-sizes"></a>VM-serien och storlekar som stöds

När du väljer en Node-storlek för en hanterad beräknings resurs i Azure Machine Learning kan du välja mellan Välj VM-storlekar som är tillgängliga i Azure. Azure erbjuder ett antal storlekar för Linux och Windows för olika arbets belastningar. Mer information om de olika [VM-typerna och-storlekarna](https://docs.microsoft.com/azure/virtual-machines/linux/sizes)hittar du här.

Det finns några undantag och begränsningar för att välja en VM-storlek:
* En del VM-serien stöds inte i Azure Machine Learning.
* En del VM-serien är begränsad. Om du vill använda en begränsad serie kontaktar du supporten och begär en kvot ökning för serien. Information om hur du kontaktar support finns i [Support alternativ för Azure](https://azure.microsoft.com/support/options/)

I följande tabell finns mer information om vilka serier och begränsningar som stöds. 

| **VM-serien som stöds**  | **Begränsningar** |
|------------|------------|
| D | Ingen |
| Dv2 | Ingen |  
| Dv3 | Ingen|
| DSv2 | Ingen | 
| DSv3 | Ingen|
| FSv2 | Ingen | 
| HBv2 | Godkännande krävs |  
| HCS UPPDATERINGSKLIENTEN | Godkännande krävs |  
| M | Godkännande krävs |
| NC | Ingen |    
| NCsv2 | Godkännande krävs |
| NCsv3 | Godkännande krävs |  
| NDs | Godkännande krävs |
| NDv2 | Godkännande krävs |
| NV | Ingen |
| NVv3 | Godkännande krävs | 


Även om Azure Machine Learning stöder dessa VM-serier är de inte tillgängliga i alla Azure-regioner. Du kan kontrol lera om det finns tillgängliga VM-serier här: [produkter tillgängliga per region](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines).

### <a name="compute-isolation"></a>Beräknings isolering

Azure Machine Learning Compute erbjuder storlekar för virtuella datorer som är isolerade till en viss maskin varu typ och som är dedikerad till en enda kund. Isolerade storlekar på virtuella datorer lämpar sig bäst för arbets belastningar som kräver en hög grad av isolering från andra kunders arbets belastningar av skäl som innehåller krav på efterlevnad och regler. Användning av en isolerad storlek garanterar att den virtuella datorn är den enda som körs på den aktuella Server instansen.

De aktuella isolerade virtuella dator erbjudandena är:
* Standard_M128ms
* Standard_F72s_v2
* Standard_NC24s_v3
* Standard_NC24rs_v3 *

*RDMA-stöd

Här kan du läsa mer om att [isolera i det offentliga Azure-molnet](https://docs.microsoft.com/azure/security/fundamentals/isolation-choices).

## <a name="unmanaged-compute"></a>Ohanterad beräkning

Ett ohanterat beräknings mål hanteras *inte* av Azure Machine Learning. Du skapar den här typen av beräknings mål utanför Azure Machine Learning och kopplar den sedan till din arbets yta. Ohanterade beräknings resurser kan kräva ytterligare åtgärder för att underhålla eller förbättra prestanda för Machine Learning-arbetsbelastningar.

## <a name="next-steps"></a>Nästa steg

Lär dig att:
* [Använd ett beräknings mål för att träna din modell](how-to-set-up-training-targets.md)
* [Distribuera din modell till ett beräknings mål](how-to-deploy-and-where.md)
