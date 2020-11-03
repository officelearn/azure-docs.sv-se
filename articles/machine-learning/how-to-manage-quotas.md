---
title: Hantera resurser och kvoter
titleSuffix: Azure Machine Learning
description: Lär dig mer om kvoterna för resurser för Azure Machine Learning och hur du begär kvot ökningar.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: jmartens
author: nishankgu
ms.author: nigup
ms.date: 10/13/2020
ms.topic: conceptual
ms.custom: troubleshooting,contperfq4, contperfq2
ms.openlocfilehash: a8a04d9e1b17c7e65be946cb51bfc41019e0706d
ms.sourcegitcommit: bbd66b477d0c8cb9adf967606a2df97176f6460b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93234010"
---
# <a name="manage-and-increase-quotas-for-resources-with-azure-machine-learning"></a>Hantera och öka kvoter för resurser med Azure Machine Learning

Azure använder gränser och kvoter för att förhindra budget överskridningar på grund av bedrägerier och för att respektera Azures kapacitets begränsningar. Överväg dessa begränsningar när du skalar för produktions arbets belastningar. I den här artikeln får du lära dig om:

> [!div class="checklist"]
> + Standard gränser för Azure-resurser som är relaterade till [Azure Machine Learning](overview-what-is-azure-ml.md).
> + Skapar kvoter på arbets ytans nivå.
> + Visa dina kvoter och begränsningar.
> + Begäran om kvot ökar.
> + Privat slut punkt och DNS-kvoter.

Tillsammans med hanterings kvoter kan du lära dig hur du [planerar och hanterar kostnader för Azure Machine Learning](concept-plan-manage-cost.md).

## <a name="special-considerations"></a>Särskilda överväganden

+ En kvot är en kredit gräns, inte en kapacitets garanti. Om du har storskaliga kapacitets behov kan du [kontakta Azure-supporten för att öka din kvot](#request-quota-increases).

+ En kvot delas mellan alla tjänster i dina prenumerationer, inklusive Azure Machine Learning. Beräkna användningen för alla tjänster när du utvärderar kapacitet.
 
  Azure Machine Learning Compute är ett undantag. Den har en separat kvot från bas beräknings kvoten. 

+ Standard gränserna varierar beroende på kategori typ, till exempel kostnads fri utvärderings version, betala per användning och serie för virtuell dator (VM) (till exempel Dv2, F och G).

## <a name="default-resource-quotas"></a>Standard resurs kvoter

I det här avsnittet får du lära dig om standard-och Max kvot gränserna för följande resurser:

+ Virtuella datorer
+ Azure Machine Learning Compute
+ Azure Machine Learning pipelines
+ Azure Container Instances
+ Azure Storage

> [!IMPORTANT]
> Begränsningar kan komma att ändras. Den senaste informationen finns i [Azure-prenumeration och tjänst begränsningar, kvoter och begränsningar](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits/) för alla Azure.

### <a name="virtual-machines"></a>Virtuella datorer
Varje Azure-prenumeration har en gräns för antalet virtuella datorer i alla tjänster. Virtuella dator kärnor har en regional total gräns och en regional gräns per storleks serie. Båda gränserna tillämpas separat.

Anta till exempel att en prenumeration i regionen USA, östra har en gräns för totalt antal VM-kärnor på 30, en gräns för antal kärnor i A-serien på 30 och en gräns för antal kärnor i D-serien på 30. Den här prenumerationen skulle kunna distribuera 30 a1-VM: ar eller 30 D1-datorer, eller en kombination av de två som inte överstiger totalt 30 kärnor.

Du kan inte höja gränserna för virtuella datorer över värdena som visas i följande tabell.

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning Compute
[Azure Machine Learning Compute](concept-compute-target.md#azure-machine-learning-compute-managed) har en standard kvot gräns för både antalet kärnor och antalet unika beräknings resurser som tillåts per region i en prenumeration. Den här kvoten är separat från den virtuella datorns kärn kvot från föregående avsnitt.

[Begär en kvot ökning](#request-quota-increases) för att höja gränserna i det här avsnittet upp till den maximala gränsen som visas i tabellen.

Tillgängliga resurser:
+ **Dedikerade kärnor per region** har en standard gräns på 24 till 300, beroende på typ av prenumerations erbjudande. Du kan öka antalet dedikerade kärnor per prenumeration för varje VM-serie. Specialiserade VM-familjer som NCv2-, NCv3-eller ND-serien börjar med standardvärdet noll kärnor.

+ **Kärnor med låg prioritet per region** har en standard gräns på 100 till 3 000, beroende på typ av prenumerations erbjudande. Antalet kärnor med låg prioritet per prenumeration kan ökas och är ett enda värde för virtuella dator familjer.

+ **Kluster per region** har en standard gräns på 200. De delas mellan ett utbildnings kluster och en beräknings instans. (En beräknings instans betraktas som ett kluster med en nod i kvot syfte.)

I följande tabell visas ytterligare gränser som du inte kan överskrida.

| **Resurs** | **Maximal gräns** |
| --- | --- |
| Arbets ytor per resurs grupp | 800 |
| Noder i en AmlCompute-resurs (Single Azure Machine Learning Compute) | 100 noder |
| Processer för GPU-MPI per nod | 1-4 |
| GPU-arbetare per nod | 1-4 |
| Jobbets livs längd | 21 dagar<sup>1</sup> |
| Jobb livs längd på en nod med låg prioritet | 7 dagar<sup>2</sup> |
| Parameter servrar per nod | 1 |

<sup>1</sup> maximal livs längd är varaktigheten mellan när en körning startar och när den har slutförts. Slutförda körningar sparas oändligt. Data för körningar som inte slutförts inom den maximala livs längden är inte tillgängliga.
<sup>2</sup> jobb på en nod med låg prioritet kan åsidosättas när det finns en kapacitets begränsning. Vi rekommenderar att du implementerar kontroll punkter i jobbet.

### <a name="azure-machine-learning-pipelines"></a>Azure Machine Learning pipelines
[Azure Machine Learning pipelines](concept-ml-pipelines.md) har följande gränser.

| **Resurs** | **Gräns** |
| --- | --- |
| Steg i en pipeline | 30 000 |
| Arbets ytor per resurs grupp | 800 |

### <a name="container-instances"></a>Container Instances

Mer information finns i [container instances gränser](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#container-instances-limits).

### <a name="storage"></a>Storage
Azure Storage har en gräns på 250 lagrings konton per region, per prenumeration. Den här gränsen omfattar både standard-och Premium lagrings konton.

Om du vill öka gränsen gör du en begäran via [supporten för Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/). Azure Storages teamet granskar ditt ärende och kan godkänna upp till 250 lagrings konton för en region.


## <a name="workspace-level-quotas"></a>Kvoter på arbets ytans nivå

Använd kvoter på arbets ytans nivå för att hantera Azure Machine Learning beräknings mål tilldelning mellan flera [arbets ytor](concept-workspace.md) i samma prenumeration.

Som standard delar alla arbets ytor samma kvot som prenumerations nivå kvoten för VM-familjer. Du kan dock ange en maximal kvot för enskilda VM-familjer på arbets ytor i en prenumeration. På så sätt kan du dela kapacitet och undvika problem med resurs konflikter.

1. Gå till en arbets yta i din prenumeration.
1. I det vänstra fönstret väljer du **användningar + kvoter**.
1. Välj fliken **Konfigurera kvoter** för att Visa kvoterna.
1. Expandera en VM-serie.
1. Ange en kvot gräns för alla arbets ytor som anges i den virtuella dator serien.

Du kan inte ange ett negativt värde eller ett värde som är högre än kvoten på prenumerations nivå.

[![Skärm bild som visar en Azure Machine Learning kvot på arbets ytans nivå.](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)

> [!NOTE]
> Du behöver behörigheter på prenumerations nivå för att ange en kvot på arbets ytans nivå.

## <a name="view-your-usage-and-quotas"></a>Visa användning och kvoter

Om du vill visa din kvot för olika Azure-resurser, till exempel virtuella datorer, lagrings enheter eller nätverk, använder du Azure Portal:

1. Välj **alla tjänster** i den vänstra rutan och välj sedan **prenumerationer** under kategorin **Allmänt** .

2. I listan över prenumerationer väljer du den prenumeration vars kvot du söker.

3. Välj **användning och kvoter** för att visa dina aktuella kvot gränser och användning. Använd filtren för att välja Provider och platser. 

Du hanterar Azure Machine Learning Compute-kvoten för din prenumeration separat från andra Azure-kvoter: 

1. Gå till arbets ytan **Azure Machine Learning** i Azure Portal.

2. I det vänstra fönstret i avsnittet **support och fel sökning** väljer du **användning och kvoter** för att visa dina aktuella kvot gränser och användning.

3. Välj en prenumeration för att Visa kvot gränserna. Filtrera till den region som du är intresse rad av.

4. Du kan växla mellan en vy på prenumerations nivå och en vy på arbets ytans nivå.

## <a name="request-quota-increases"></a>Begär kvotökningar

Om du vill höja gränsen eller kvoten ovanför standard gränsen, [öppnar du en kund support förfrågan online](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/) utan kostnad.

Du kan inte höja gränserna över de maximala värdena som visas i föregående tabeller. Om det inte finns någon övre gräns kan du inte ändra gränsen för resursen.

När du begär en kvot ökning väljer du den tjänst som du har i åtanke. Välj till exempel Azure Machine Learning, Container Instances eller lagring. För Azure Machine Learning Compute kan du välja knappen **begär kvot** när du visar kvoten i föregående steg.

> [!NOTE]
> [Kostnads fria utvärderings prenumerationer](https://azure.microsoft.com/offers/ms-azr-0044p) är inte berättigade till begränsning eller kvot ökningar. Om du har en kostnads fri utvärderings prenumeration kan du uppgradera till en prenumeration [där du betalar per](https://azure.microsoft.com/offers/ms-azr-0003p/) användning. Mer information finns i [Uppgradera Azures kostnads fria utvärderings version till](https://docs.microsoft.com/azure/cost-management-billing/manage/upgrade-azure-subscription) [vanliga frågor och svar om](https://azure.microsoft.com/free/free-account-faq)att betala per användning och kostnads fritt Azure-konto.

## <a name="private-endpoint-and-private-dns-quota-increases"></a>Högre kvot för privat slutpunkt och privat DNS

Det finns gränser för antalet privata slut punkter och privata DNS-zoner som du kan skapa i en prenumeration.

Azure Machine Learning skapar resurser i din prenumeration (kund), men vissa scenarier skapar resurser i en Microsoft-ägd prenumeration.

 I följande scenarier kan du behöva begära en kvot tilldelning i den Microsoft-ägda prenumerationen:

* Azure Privates länk aktive rad arbets yta med en kundhanterad nyckel (CMK)
* Azure Container Registry för arbetsytan bakom ditt virtuella nätverk
* Bifoga ett Azure Kubernetes Service-kluster med privat länk till din arbetsyta

Använd följande steg för att begära en ersättning för dessa scenarier:

1. [Skapa en support förfrågan för Azure](/azure/azure-portal/supportability/how-to-create-azure-support-request#create-a-support-request) och välj följande alternativ i avsnittet __grundläggande__ information:

    | Fält | Urval |
    | ----- | ----- |
    | Typ av problem | **Teknik** |
    | Tjänst | **Mina tjänster**. Välj sedan __Machine Learning__ i list rutan. |
    | Problem typ | **Arbets ytans konfiguration och säkerhet** |
    | Problem under typ | **Begäran om tilldelning av privat slut punkt och Privat DNS zon** |

2. I avsnittet __information__ använder du fältet __Beskrivning__ för att tillhandahålla Azure-regionen och det scenario som du planerar att använda. Om du behöver begära kvot ökningar för flera prenumerationer kan du ange prenumerations-ID: n i det här fältet.

3. Välj __skapa__ för att skapa begäran.

:::image type="content" source="media/how-to-manage-quotas/quota-increase-private-endpoint.png" alt-text="Skärm bild av en privat slut punkt och en privat DNS-kvot ökar begäran.":::

## <a name="next-steps"></a>Nästa steg

+ [Planera och hantera kostnader för Azure Machine Learning](concept-plan-manage-cost.md)
