---
title: Hantera resurser & kvoter
titleSuffix: Azure Machine Learning
description: Lär dig mer om kvoterna för resurser för Azure Machine Learning och hur du begär mer kvot.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nishankgu
ms.author: nigup
ms.date: 11/04/2019
ms.openlocfilehash: d44fb94978c450808c8a1c0852d4c771a100857e
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/12/2019
ms.locfileid: "73961722"
---
# <a name="manage-and-request-quotas-for-azure-resources"></a>Hantera och begära kvoter för Azure-resurser
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Den här artikeln innehåller information om förkonfigurerade gränser för Azure-resurser för din prenumeration. Här finns också anvisningar om hur du begär kvot förbättringar för varje typ av resurs. Dessa gränser lagts in för att förhindra att budget över körs på grund av bedrägeri och Använd Azure-kapacitet villkor.

Precis som med andra Azure-tjänster finns det gränser för vissa resurser som är kopplade till Azure Machine Learning. Dessa gränser sträcker sig från ett tak på antalet arbets ytor till gränser för den faktiska underliggande beräkning som används för modell träning eller härledning/bedömning. 

När du utformar och skalar Azure Machine Learning resurser för produktions arbets belastningar bör du tänka på dessa gränser. Om klustret till exempel inte når mål antalet noder kan du ha nått gränsen för Azure Machine Learning beräknings kärnor för din prenumeration. Om du vill höja gränsen eller kvot över gränsen som standard öppnar du en kundsupportärende utan kostnad. Gränserna kan inte höjas ovan maxgränsen värdet som visas i följande tabeller på grund av Azure-kapacitet. Om det finns ingen övre gräns-kolumn måste har inte resursen justerbara begränsningar.

## <a name="special-considerations"></a>Att tänka på

+ En kvot är en kreditgräns, inte en garanti för kapacitet. Om du har programpaketfunktionen behov kan du kontakta Azure-supporten.

+ Din kvot delas mellan alla tjänster i dina prenumerationer, inklusive Azure Machine Learning. Det enda undantaget är Azure Machine Learning Compute som har en separat kvot från bas beräknings kvoten. Glöm inte att beräkna kvotanvändning för alla tjänster när du utvärderar din kapacitetsbehoven.

+ Standard gränserna varierar beroende på kategori typ, t. ex. kostnads fri utvärderings version, betala per användning och VM-serien, till exempel Dv2, F, G och så vidare.

## <a name="default-resource-quotas"></a>Standard resurskvoter

Här är en uppdelning av kvotgränser av olika typer av resurser i Azure-prenumerationen.

> [!Important]
> Gränserna kan komma att ändras. Den senaste versionen alltid finns på tjänstnivå kvoten [dokumentet](https://docs.microsoft.com/azure/azure-subscription-service-limits/) för hela Azure.

### <a name="virtual-machines"></a>Virtuella datorer
För varje Azure-prenumeration finns det en gräns för hur många virtuella datorer du kan ha på dina tjänster eller fristående. Den här gränsen är på regionsnivån på totalt antal kärnor och också på basis av per familj.

Virtuella dator kärnor har en regional total gräns och en region per storleks serie (Dv2, F osv.), som båda tillämpas separat. Anta till exempel att en prenumeration i regionen USA, östra har en gräns för totalt antal VM-kärnor på 30, en gräns för antal kärnor i A-serien på 30 och en gräns för antal kärnor i D-serien på 30. Den här prenumerationen kan distribuera 30 virtuella datorer i A1-serien eller 30 virtuella datorer i D1-serien, eller en kombination av båda som inte överstiger sammanlagt 30 kärnor (t.ex. 10 virtuella datorer i A1-serien och 20 virtuella datorer i D1-serien).

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../../includes/azure-subscription-limits-azure-resource-manager.md)]

En mer utförlig och aktuell lista över kvotgränser, hittar du i Azure hela kvoten artikeln [här](https://docs.microsoft.com/azure/azure-subscription-service-limits).

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning-beräkning
Det finns en standardgräns för kvot för både antalet kärnor och antal unika beräkningsresurser som tillåts per region för en prenumeration för Azure Machine Learning Compute. Den här kvoten är separat från den virtuella datorns kärn kvot och kärn gränserna delas inte mellan de två resurs typerna eftersom AmlCompute är en hanterad tjänst som distribuerar resurser i en modell som är värd för en värd.

Tillgängliga resurser:
+ Dedikerade kärnor per region har en standard gräns på 24-300 beroende på din prenumerations erbjudande typ med högre standardvärden för erbjudanden av typen EA och CSP.  Antalet dedikerade kärnor per prenumeration kan ökas och skiljer sig åt för varje VM-serie. Vissa specialiserade VM-familjer som NCv2-, NCv3-och ND-serien börjar med standardvärdet noll kärnor. Kontakta Azure-supporten genom att höja en kvot förfrågan för att diskutera öknings alternativ.

+ Kärnor med låg prioritet per region har en standard gräns på 100-3000 beroende på din prenumerations erbjudande typ med högre standardvärden för erbjudande typerna EA och CSP. Antalet kärnor med låg prioritet per prenumeration kan ökas och är ett enda värde för virtuella dator familjer. Kontakta Azure-supporten diskutera alternativ för utökning.

+ Kluster per region har en standard gräns på 200. De delas mellan ett utbildnings kluster och en beräknings instans (som betraktas som ett kluster med en nod i kvot syfte). Kontakta Azure-supporten om du vill begära en ökning utöver denna gräns.

+ Det finns * * andra strikta gränser som inte kan överskridas när de har nåtts.

| **Resurs** | **Övre gräns** |
| --- | --- |
| Maximal arbetsytor per resursgrupp | 800 |
| Högsta antalet noder i en enskild resurs i Azure Machine Learning Compute (AmlCompute) | 100 noder |
| Den maximala GPU MPI bearbetar per nod | 1 – 4 |
| Den maximala GPU arbetare per nod | 1 – 4 |
| Maximal jobbet livslängd | 90 dagar<sup>1</sup> |
| Maximal jobb livs längd för en nod med låg prioritet | 7 dagar<sup>2</sup> |
| Maximal parametern servrar per nod | 1 |

<sup>1</sup> högsta livstid som refererar till den tid som en körning startas och när den är klar. Slutförda körningar kvar på obestämd tid; data för körs inte slutförs inom den maximala livstiden är inte tillgänglig.
<sup>2</sup> jobb på en nod med låg prioritet kan åsidosättas när som helst i en kapacitets begränsning. Vi rekommenderar att du implementerar kontroll punkter i jobbet.

### <a name="azure-machine-learning-pipelines"></a>Azure Machine Learning pipelines
För Azure Machine Learning pipeliner finns en kvot gräns för antalet steg i en pipeline och antalet schemabaserade körningar av publicerade pipelines per region i en prenumeration.
- Maximalt antal steg som tillåts i en pipeline är 30 000
- Högsta antalet schemabaserade körningar och blob-hämtningar för blogg utlösta scheman över publicerade pipelines per prenumeration är 100 000

> [!NOTE]
> Kontakta [Microsoft Support](https://azure.microsoft.com/support/options/)om du vill öka den här gränsen.

### <a name="container-instances"></a>Containerinstanser

Det finns också en gräns för antalet behållarinstanser som kan sätta upp i en viss tidsperiod (begränsade per timme) eller i hela prenumerationen.

[!INCLUDE [container-instances-limits](../../../includes/container-instances-limits.md)]

En mer utförlig och aktuell lista över kvotgränser, hittar du i Azure hela kvoten artikeln [här](https://docs.microsoft.com/azure/azure-subscription-service-limits#container-instances-limits).

### <a name="storage"></a>Storage
Det finns en gräns för antalet storage-konton per region samt i en viss prenumeration. Standard gränsen är 250 och omfattar både standard-och Premium Storages konton. Om du behöver fler än 250 lagrings konton i en specifik region gör du en begäran via [supporten för Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/). Azure Storage-teamet granskar ditt affärsfall och kan godkänna upp till 250 lagringskonton för en viss region.


## <a name="workspace-level-quota"></a>Kvot på arbets ytans nivå

Vi har introducerat en funktion som gör det möjligt att distribuera prenumerations nivå kvoter (per VM-familj) och konfigurera dem på arbets ytans nivå för att bättre hantera resurs tilldelningar för Amlcompute mellan olika arbets ytor. Standard beteendet är att alla arbets ytor har samma kvot som prenumerations nivå kvoten för alla VM-serier. I takt med att antalet arbets ytor ökar, och arbets belastningar med varierande prioritet börjar dela samma resurser, vill användarna dock ha ett sätt att bättre dela kapaciteten och undvika problem med resurs konflikter. Azure Machine Learning tillhandahåller en lösning med det hanterade Compute-erbjudandet genom att tillåta att användarna anger en maximal kvot för en viss VM-serie på varje arbets yta. Detta är detsamma som att distribuera din kapacitet mellan arbets ytor och användarna kan välja att överallokeras till maximal användning. 

Om du vill ange kvoter på arbets ytans nivå går du till valfri arbets yta i din prenumeration och klickar på **användnings områden + kvoter** i det vänstra fönstret. Välj sedan fliken **Konfigurera kvoter** för att Visa kvoterna, expandera alla VM-serier och ange en kvot gräns för alla arbets ytor som visas i den virtuella dator serien. Kom ihåg att du inte kan ange ett negativt värde eller ett värde som är högre än kvoten för prenumerations nivån. Som standard tilldelas alla arbets ytor som standard hela prenumerations kvoten för att tillåta fullständig användning av den tilldelade kvoten.

[kvot för ![Azure Machine Learning arbets ytans nivå](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)


> [!NOTE]
> Detta är endast en Enterprise-version-funktion. Om du har både en Basic-och en Enterprise Edition-arbetsyta i din prenumeration kan du använda den för att bara ange kvoter på dina företags arbets ytor. Dina grundläggande arbets ytor kommer att fortsätta att ha prenumerations nivå kvoten som är standard beteendet.
>
> Du behöver behörighet för prenumerations nivå för att ange kvot på arbets ytans nivå. Detta gäller så att enskilda arbets ytans ägare inte kan redigera eller öka sina kvoter och starta encroaching till resurser som har tagits ur bruk för en annan arbets yta. Därför passar en prenumerations administratör bäst att allokera och distribuera dessa kvoter mellan arbets ytor.



## <a name="view-your-usage-and-quotas"></a>Visa användning och kvoter

Visa din kvot för olika resurser, till exempel virtuella datorer, lagring, nätverk, är enkelt via Azure portal.

1. Välj **alla tjänster** i den vänstra rutan och välj sedan **prenumerationer** under kategorin Allmänt.

1. Välj den prenumeration vars kvot som du letar efter listan över prenumerationer.

   **Det finns ett villkor för**, särskilt för att visa Azure Machine Learning Compute-kvoten. Som nämnts ovan är är den kvoten separat från compute-kvoten för din prenumeration.

1. I den vänstra rutan väljer du **Machine Learning tjänst** och väljer sedan en arbets yta i listan som visas

1. På nästa blad under den **Support och felsökning** Välj **användning + kvoter** att visa din aktuella kvotgränser och användning.

1. Välj en prenumeration för att visa kvotgränserna. Kom ihåg att filtrera till den region som du är intresserad av.

1. Nu kan du växla mellan en vy på prenumerations nivå och en arbets yta på nivån:
    + **Prenumerationsvy:** På så sätt kan du Visa din användning av kärn kvoten för VM-serien, expandera den efter arbets ytan och utöka den ytterligare med hjälp av de faktiska kluster namnen. Den här vyn är optimal för att snabbt komma igång med information om kärn användningen för en viss VM-serie för att se avbrottet per arbets yta och ytterligare av de underliggande klustren för var och en av dessa arbets ytor. Den allmänna konventionen i den här vyn är (användning/kvot), där användningen är det aktuella antalet skalade kärnor och kvoten är det logiska maximala antalet kärnor som resursen kan skalas till. För varje **arbets yta**skulle kvoten vara kvoten för arbets ytans nivå (enligt beskrivningen ovan) som anger det maximala antalet kärnor som du kan skala till för en viss VM-serie. För ett **kluster** på samma sätt är kvoten i själva verket de kärnor som motsvarar det högsta antalet noder som klustret kan skala till som definieras av egenskapen max_nodes.

    + **Vyn arbets yta:** På så sätt kan du Visa din användning av kärn kvoten per arbets yta, expandera den av VM-serien och utöka den ytterligare genom att utöka den med de faktiska kluster namnen. Den här vyn är optimal för att snabbt komma igång med information om kärn användningen för en viss arbets yta för att se upprensningen av VM-familjer och ytterligare av de underliggande klustren för var och en av dessa familjer.

## <a name="request-quota-increases"></a>Kvoter för begäran

Om du vill höja gränsen eller kvoten över standard gränsen kan du [öppna en kund support förfrågan online](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/) utan kostnad.

Gränserna kan inte höjas ovanför det maximala gräns värde som visas i tabellerna. Om det inte finns någon maxgräns har resursen inte några justerbara gränser. [Detta](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors) artikeln beskriver hur kvot ökning i detalj.

När begära en utökad kvot måste du markera den tjänst som du begär för att öka kvoten mot, vilket kan orsaka tjänster som Machine Learning-tjänstkvot, Container instances eller lagringskvoten. Förutom Azure Machine Learning Compute kan du klicka på knappen **begär kvot** och Visa kvoten enligt stegen ovan.

> [!NOTE]
> [Kostnadsfria utvärderingsversioner](https://azure.microsoft.com/offers/ms-azr-0044p) berättigar inte till gränsen eller kvot ökar. Om du har en [kostnadsfri utvärderingsprenumeration](https://azure.microsoft.com/offers/ms-azr-0044p), du kan uppgradera till en [användningsbaserad](https://azure.microsoft.com/offers/ms-azr-0003p/) prenumeration. Mer information finns i [uppgradera kostnadsfri utvärderingsversion av Azure till betala per användning](../../billing/billing-upgrade-azure-subscription.md) och [kostnadsfri utvärderingsprenumeration vanliga frågor och svar](https://azure.microsoft.com/free/free-account-faq).
