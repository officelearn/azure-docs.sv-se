---
title: Hantera resurser & kvoter
titleSuffix: Azure Machine Learning
description: Lär dig mer om kvoterna för resurser för Azure Machine Learning och hur du begär mer kvot.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: jmartens
author: nishankgu
ms.author: nigup
ms.date: 10/13/2020
ms.topic: conceptual
ms.custom: troubleshooting,contperfq4, contperfq2
ms.openlocfilehash: a81af14992c8557c245ab3a1073f031a6c505084
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92019400"
---
# <a name="manage--increase-quotas-for-resources-with-azure-machine-learning"></a>Hantera & öka kvoter för resurser med Azure Machine Learning

Azure använder gränser och kvoter för att förhindra budget överskridningar på grund av bedrägerier, och för att respektera Azures kapacitets begränsningar. Överväg dessa begränsningar när du skalar för produktions arbets belastningar. I den här artikeln får du lära dig om:

> [!div class="checklist"]
> + Standard gränser för Azure-resurser som är relaterade till [Azure Machine Learning](overview-what-is-azure-ml.md).
> + Visa dina kvoter och begränsningar.
> + Skapa nivå kvoter för arbets ytan.
> + Begär ande kvoten ökar.
> + Privat slut punkt och DNS-kvoter.

Tillsammans med hanterings kvoter kan du också lära dig hur du [planerar & hantera kostnader för Azure Machine Learning](concept-plan-manage-cost.md).

## <a name="special-considerations"></a>Särskilda överväganden

+ En kvot är en kredit gräns, inte en kapacitets garanti. Om du har storskaliga kapacitets behov kan du [kontakta Azure-supporten för att öka din kvot](#request-quota-increases).

+ Kvoten delas mellan alla tjänster i dina prenumerationer, inklusive Azure Machine Learning. Beräkna användningen för alla tjänster när du utvärderar kapaciteten.
    + Azure Machine Learning Compute är ett undantag och har en separat kvot från bas beräknings kvoten. 

+ Standard gränserna varierar beroende på kategori typ, t. ex. kostnads fri utvärderings version, betala per användning och virtuell dator (VM), till exempel Dv2, F, G och så vidare.

## <a name="default-resource-quotas"></a>Standard resurs kvoter

I det här avsnittet får du lära dig om standard-och Max kvot gränserna för följande resurser:

+ Virtuella datorer
+ Azure Machine Learning-beräkning
+ Azure Machine Learning pipelines
+ Container Instances
+ Storage

> [!IMPORTANT]
> Begränsningar kan komma att ändras. De senaste kan alltid finnas på tjänst nivå kvot [dokumentet](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits/) för hela Azure.

### <a name="virtual-machines"></a>Virtuella datorer
Varje Azure-prenumeration har en gräns för antalet virtuella datorer i alla tjänster. Virtuella dator kärnor har en regional total gräns och en regional gräns per storleks serie (Dv2, F osv.). Båda gränserna tillämpas separat.

Anta till exempel att en prenumeration i regionen USA, östra har en gräns för totalt antal VM-kärnor på 30, en gräns för antal kärnor i A-serien på 30 och en gräns för antal kärnor i D-serien på 30. Den här prenumerationen skulle kunna distribuera 30 a1-VM: ar eller 30 D1-datorer, eller en kombination av de två som inte överstiger totalt 30 kärnor.

Det går inte att öka gränserna för virtuella datorer ovanför värdet som visas i följande tabell.

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning-beräkning
[Azure Machine Learning Compute](concept-compute-target.md#azure-machine-learning-compute-managed) har en standard kvot gräns för både antalet kärnor och antalet unika beräknings resurser som tillåts per region i en prenumeration. Den här kvoten är separat från den virtuella datorns kärn kvot från föregående avsnitt.

[Begär en kvot ökning](#request-quota-increases) för att höja gränserna i det här avsnittet upp till den **maximala gränsen** som visas i tabellen.

Tillgängliga resurser:
+ **Dedikerade kärnor per region** har en standard gräns på 24-300 beroende på typ av prenumerations erbjudande.  Antalet dedikerade kärnor per prenumeration kan ökas för varje VM-serie. Specialiserade VM-familjer som NCv2-, NCv3-eller ND-serien börjar med standardvärdet noll kärnor.

+ **Kärnor med låg prioritet per region** har en standard gräns på 100-3000 beroende på typ av prenumerations erbjudande. Antalet kärnor med låg prioritet per prenumeration kan ökas och är ett enda värde för virtuella dator familjer.

+ **Kluster per region** har en standard gräns på 200. De delas mellan ett utbildnings kluster och en beräknings instans (som betraktas som ett kluster med en nod i kvot syfte).

I följande tabell visas ytterligare gränser som inte får överskridas.

| **Resurs** | **Maximal gräns** |
| --- | --- |
| Arbets ytor per resurs grupp | 800 |
| Noder i en AmlCompute-resurs (Single Azure Machine Learning Compute) | 100 noder |
| Processer för GPU-MPI per nod | 1-4 |
| GPU-arbetare per nod | 1-4 |
| Jobbets livs längd | 21 dagar<sup>1</sup> |
| Jobbets livs längd på en Low-Priority nod | 7 dagar<sup>2</sup> |
| Parameter servrar per nod | 1 |

<sup>1</sup> maximal livs längd motsvarar varaktigheten mellan när en körning startar och slutar. Slutförda körningar sparas oändligt. Data för körningar som inte slutförts inom den maximala livs längden är inte tillgängliga.
<sup>2</sup> jobb på en Low-Priority-nod kan när som helst vänta på att det finns en kapacitets begränsning. Vi rekommenderar att du implementerar kontroll punkter i jobbet.

### <a name="azure-machine-learning-pipelines"></a>Azure Machine Learning pipelines
[Azure Machine Learning pipelines](concept-ml-pipelines.md) har följande gränser.

| **Resurs** | **Gräns** |
| --- | --- |
| Steg i en pipeline | 30 000 |
| Arbets ytor per resurs grupp | 800 |

### <a name="container-instances"></a>Containerinstanser

Mer information finns i [container instances gränser](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#container-instances-limits).

### <a name="storage"></a>Storage
Azure Storage-konton har en gräns på 250 lagrings konton per region, per prenumeration. Detta omfattar både standard-och Premium Storage konton.

Om du vill öka gränsen gör du en begäran via [supporten för Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/). Azure Storages teamet granskar ditt ärende och kan godkänna upp till 250 lagrings konton för en region.


## <a name="workspace-level-quota"></a>Kvot på arbets ytans nivå

Använd kvoter för arbets ytans nivå för att hantera Azure Machine Learning beräknings mål tilldelning mellan flera [arbets ytor](concept-workspace.md) i samma prenumeration.

Som standard delar alla arbets ytor samma kvot som prenumerations nivå kvoten för VM-familjer. Du kan dock ange en maximal kvot för enskilda VM-familjer på arbets ytor i en prenumeration. På så sätt kan du dela kapacitet och undvika problem med resurs konflikter:

1. Navigera till en arbets yta i din prenumeration.
1. I det vänstra fönstret väljer du **användningar + kvoter**.
1. Välj fliken **Konfigurera kvoter** för att Visa kvoterna.
1. Expandera en VM-serie.
1. Ange en kvot gräns för alla arbets ytor som anges i den virtuella dator serien.

Du kan inte ange ett negativt värde eller ett värde som är högre än kvoten för prenumerations nivån.

[![Kvot för Azure Machine Learning arbets ytans nivå](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)

> [!NOTE]
> Du behöver behörighet för prenumerations nivå för att ange kvot på arbets ytans nivå.

## <a name="view-your-usage-and-quotas"></a>Visa användning och kvoter

Om du vill visa din kvot för olika Azure-resurser som Virtual Machines, lagring, nätverk, använder du Azure Portal:

1. Välj **alla tjänster** i den vänstra rutan och välj sedan **prenumerationer** under kategorin Allmänt.

2. I listan över prenumerationer väljer du den prenumeration vars kvot du söker.

3. Välj **användning och kvoter** för att visa dina aktuella kvot gränser och användning. Använd filtren för att välja Provider och platser. 

Azure Machine Learning Compute-kvoten för din prenumeration hanteras separat från andra Azure-kvoter. 

1. Navigera till arbets ytan **Azure Machine Learning** i Azure Portal.

2. I det vänstra fönstret, under **avsnittet support och fel sökning** , väljer du **användning och kvoter** för att visa dina aktuella kvot gränser och användning.

3. Välj en prenumeration för att Visa kvot gränserna. Kom ihåg att filtrera till den region som du är intresse rad av.

4. Du kan växla mellan en vy på prenumerations nivå och en vy på arbets ytans nivå.

## <a name="request-quota-increases"></a>Begär kvotökningar

Om du vill höja gränsen eller kvoten ovanför standard gränsen, [öppnar du en kund support förfrågan online](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/) utan kostnad.

Gränser kan inte höjas över det maximala gräns värde som visas i tabellerna ovan. Om det inte finns någon övre gräns kan du inte ändra gränsen för resursen.

När du begär en kvot ökning väljer du den tjänst som du begär för att öka kvoten mot. Till exempel Azure Machine Learning, Container Instances, lagring osv. För Azure Machine Learning Compute kan du välja knappen **begär kvot** när du visar kvoten enligt stegen ovan.

> [!NOTE]
> [Kostnads fria utvärderings prenumerationer](https://azure.microsoft.com/offers/ms-azr-0044p) är inte berättigade till begränsning eller kvot ökningar. Om du har en [kostnads fri utvärderings prenumeration](https://azure.microsoft.com/offers/ms-azr-0044p)kan du uppgradera till en prenumeration [där du betalar per](https://azure.microsoft.com/offers/ms-azr-0003p/) användning. Mer information finns i [uppgradera den kostnads fria utvärderings versionen av Azure till "betala per](../billing/billing-upgrade-azure-subscription.md) användning" och  [vanliga frågor om utvärderings prenumeration](https://azure.microsoft.com/free/free-account-faq).

## <a name="private-endpoint-and-private-dns-quota-increases"></a>Privat slut punkt och privat DNS-kvot ökar

Det finns begränsningar för antalet privata slut punkter och privata DNS-zoner som kan skapas i en prenumeration.

Medan Azure Machine Learning skapar resurser i din prenumeration (kund) finns det några scenarier som skapar resurser i en Microsoft-ägd prenumeration.

 I följande scenarier kan du behöva begära en kvot tilldelning i den Microsoft-ägda prenumerationen:

* __Privat länk aktive rad arbets yta med en kundhanterad nyckel (CMK)__
* __Azure Container Registry för arbets ytan bakom ditt virtuella nätverk__
* __Bifoga ett Azure Kubernetes service-kluster med en privat länk till din arbets yta__.

Använd följande steg för att begära en ersättning för dessa scenarier:

1. [Skapa en support förfrågan för Azure](/azure/azure-portal/supportability/how-to-create-azure-support-request#create-a-support-request) och välj följande alternativ i avsnittet __grundläggande__ information:

    | Fält | Urval |
    | ----- | ----- |
    | Typ av problem | Teknik |
    | Tjänst | Mina tjänster. Välj __Machine Learning__ i list rutan. |
    | Problem typ | Installation av arbets yta, SDK och CLI |
    | Problem under typ | Problem med etablering eller hantering av arbetsyta |

2. I avsnittet __information__ använder du fältet __Beskrivning__ för att ange den Azure-region som du vill använda och det scenario som du planerar att använda. Om du behöver begära kvot ökningar för flera prenumerationer kan du även ange prenumerations-ID: n i det här fältet.

3. Välj __skapa__ för att skapa begäran.

## <a name="next-steps"></a>Nästa steg

+ [Planera & hantera kostnader för Azure Machine Learning](concept-plan-manage-cost.md)
