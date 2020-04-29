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
ms.date: 03/05/2020
ms.openlocfilehash: 530647c3d32b62f0cac250795ccce580b182fa92
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80756605"
---
# <a name="manage-and-request-quotas-for-azure-resources"></a>Hantera och begär kvoter för Azure-resurser
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Den här artikeln innehåller information om förkonfigurerade gränser för Azure-resurser för din prenumeration. Här finns också anvisningar om hur du begär kvot förbättringar för varje typ av resurs. Dessa gränser är placerade för att förhindra budget överskridningar på grund av bedrägerier och för att uppfylla Azures kapacitets begränsningar.

Precis som med andra Azure-tjänster finns det gränser för vissa resurser som är kopplade till Azure Machine Learning. Dessa gränser sträcker sig från ett tak på antalet arbets ytor till gränser för den faktiska underliggande beräkning som används för modell träning eller härledning/bedömning. 

När du utformar och skalar Azure Machine Learning resurser för produktions arbets belastningar bör du tänka på dessa gränser. Om klustret till exempel inte når mål antalet noder kan du ha nått gränsen för Azure Machine Learning beräknings kärnor för din prenumeration. Om du vill höja gränsen eller kvoten över standard gränsen kan du öppna en kund support förfrågan online utan kostnad. Gränserna kan inte höjas över det maximala gräns värde som visas i följande tabeller på grund av begränsningar i Azure-kapaciteten. Om det inte finns någon Max gräns kolumn har inte resursen några justerbara gränser.

## <a name="special-considerations"></a>Särskilda överväganden

+ En kvot är en kredit gräns, inte en kapacitets garanti. Kontakta Azure-supporten om du har storskaliga kapacitets behov.

+ Din kvot delas mellan alla tjänster i dina prenumerationer, inklusive Azure Machine Learning. Det enda undantaget är Azure Machine Learning Compute som har en separat kvot från bas beräknings kvoten. Se till att beräkna kvot användningen för alla tjänster när du utvärderar dina kapacitets behov.

+ Standard gränserna varierar beroende på kategori typ, t. ex. kostnads fri utvärderings version, betala per användning och VM-serien, till exempel Dv2, F, G och så vidare.

## <a name="default-resource-quotas"></a>Standard resurs kvoter

Här följer en sammanställning av kvot gränserna av olika resurs typer i din Azure-prenumeration.

> [!IMPORTANT]
> Begränsningar kan komma att ändras. De senaste kan alltid finnas på tjänst nivå kvot [dokumentet](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits/) för hela Azure.

### <a name="virtual-machines"></a>Virtuella datorer
För varje Azure-prenumeration finns det en gräns för hur många virtuella datorer du kan ha på dina tjänster eller fristående. Den här gränsen är på regions nivå både på det totala antalet kärnor och även per familj.

Virtuella dator kärnor har en regional total gräns och en region per storleks serie (Dv2, F osv.), som båda tillämpas separat. Anta till exempel att en prenumeration i regionen USA, östra har en gräns för totalt antal VM-kärnor på 30, en gräns för antal kärnor i A-serien på 30 och en gräns för antal kärnor i D-serien på 30. Den här prenumerationen kan distribuera 30 virtuella datorer i A1-serien eller 30 virtuella datorer i D1-serien, eller en kombination av båda som inte överstiger sammanlagt 30 kärnor (t.ex. 10 virtuella datorer i A1-serien och 20 virtuella datorer i D1-serien).

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../includes/azure-subscription-limits-azure-resource-manager.md)]

Om du vill ha en mer detaljerad och uppdaterad lista över kvot gränser kontrollerar du kvot artikeln för hela Azure [här](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits).

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning-beräkning
För Azure Machine Learning Compute finns en standard kvot gräns för både antalet kärnor och antalet unika beräknings resurser som tillåts per region i en prenumeration. Den här kvoten är separat från den virtuella datorns kärn kvot och kärn gränserna delas inte mellan de två resurs typerna eftersom AmlCompute är en hanterad tjänst som distribuerar resurser i en modell som är värd för en värd.

Tillgängliga resurser:
+ Dedikerade kärnor per region har en standard gräns på 24-300 beroende på din prenumerations erbjudande typ med högre standardvärden för erbjudanden av typen EA och CSP.  Antalet dedikerade kärnor per prenumeration kan ökas och skiljer sig åt för varje VM-serie. Vissa specialiserade VM-familjer som NCv2-, NCv3-och ND-serien börjar med standardvärdet noll kärnor. Kontakta Azure-supporten genom att höja en kvot förfrågan för att diskutera öknings alternativ.

+ Kärnor med låg prioritet per region har en standard gräns på 100-3000 beroende på din prenumerations erbjudande typ med högre standardvärden för erbjudande typerna EA och CSP. Antalet kärnor med låg prioritet per prenumeration kan ökas och är ett enda värde för virtuella dator familjer. Kontakta Azure-supporten för att diskutera öknings alternativ.

+ Kluster per region har en standard gräns på 200. De delas mellan ett utbildnings kluster och en beräknings instans (som betraktas som ett kluster med en nod i kvot syfte). Kontakta Azure-supporten om du vill begära en ökning utöver den här gränsen.

+ Det finns andra strikta gränser som inte kan överskridas när de har nåtts.

| **Resurs** | **Maximal gräns** |
| --- | --- |
| Maximalt antal arbets ytor per resurs grupp | 800 |
| Maximalt antal noder i en AmlCompute-resurs (Single Azure Machine Learning Compute) | 100 noder |
| Högsta antal GPU MPI-processer per nod | 1-4 |
| Maximalt antal GPU-arbetare per nod | 1-4 |
| Maximal jobb livs längd | 90 dagar<sup>1</sup> |
| Maximal jobb livs längd för en nod med låg prioritet | 7 dagar<sup>2</sup> |
| Maximalt antal parameter servrar per nod | 1 |

<sup>1</sup> den högsta livstiden avser den tid som en körning startar och när den har slutförts. Slutförda körningar sparas oändligt. data för körningar som inte slutförts inom den maximala livs längden är inte tillgängliga.
<sup>2</sup> jobb på en nod med låg prioritet kan åsidosättas när som helst i en kapacitets begränsning. Vi rekommenderar att du implementerar kontroll punkter i jobbet.

### <a name="azure-machine-learning-pipelines"></a>Azure Machine Learning pipelines
För Azure Machine Learning pipeliner finns en kvot gräns för antalet steg i en pipeline och antalet schemabaserade körningar av publicerade pipelines per region i en prenumeration.
- Maximalt antal steg som tillåts i en pipeline är 30 000
- Högsta antalet schemabaserade körningar och blob-hämtningar för blogg utlösta scheman över publicerade pipelines per prenumeration är 100 000

> [!NOTE]
> Kontakta [Microsoft Support](https://azure.microsoft.com/support/options/)om du vill öka den här gränsen.

### <a name="container-instances"></a>Containerinstanser

Det finns också en gräns för antalet behållar instanser som du kan sätta upp under en viss tids period (i varje timme) eller i hela prenumerationen.

[!INCLUDE [container-instances-limits](../../includes/container-instances-limits.md)]

Om du vill ha en mer detaljerad och uppdaterad lista över kvot gränser kontrollerar du kvot artikeln för hela Azure [här](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#container-instances-limits).

### <a name="storage"></a>Storage
Det finns en gräns för antalet lagrings konton per region och i en viss prenumeration. Standard gränsen är 250 och omfattar både standard-och Premium Storages konton. Om du behöver fler än 250 lagrings konton i en specifik region gör du en begäran via [supporten för Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/). Azure Storages teamet granskar ditt affärs ärende och kan godkänna upp till 250 lagrings konton för en specifik region.


## <a name="workspace-level-quota"></a>Kvot på arbets ytans nivå

Vi har introducerat en funktion som gör det möjligt att distribuera prenumerations nivå kvoter (per VM-familj) och konfigurera dem på arbets ytans nivå för att bättre hantera resurs tilldelningar för Amlcompute mellan olika arbets ytor. Standard beteendet är att alla arbets ytor har samma kvot som prenumerations nivå kvoten för alla VM-serier. I takt med att antalet arbets ytor ökar, och arbets belastningar med varierande prioritet börjar dela samma resurser, vill användarna dock ha ett sätt att bättre dela kapaciteten och undvika problem med resurs konflikter. Azure Machine Learning tillhandahåller en lösning med det hanterade Compute-erbjudandet genom att tillåta att användarna anger en maximal kvot för en viss VM-serie på varje arbets yta. Detta är detsamma som att distribuera din kapacitet mellan arbets ytor och användarna kan välja att överallokeras till maximal användning. 

Om du vill ange kvoter på arbets ytans nivå går du till valfri arbets yta i din prenumeration och klickar på **användnings områden + kvoter** i det vänstra fönstret. Välj sedan fliken **Konfigurera kvoter** för att Visa kvoterna, expandera alla VM-serier och ange en kvot gräns för alla arbets ytor som visas i den virtuella dator serien. Kom ihåg att du inte kan ange ett negativt värde eller ett värde som är högre än kvoten för prenumerations nivån. Som standard tilldelas alla arbets ytor som standard hela prenumerations kvoten för att tillåta fullständig användning av den tilldelade kvoten.

[![Kvot för Azure Machine Learning arbets ytans nivå](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)


> [!NOTE]
> Detta är endast en Enterprise-version-funktion. Om du har både en Basic-och en Enterprise Edition-arbetsyta i din prenumeration kan du använda den för att bara ange kvoter på dina företags arbets ytor. Dina grundläggande arbets ytor kommer att fortsätta att ha prenumerations nivå kvoten som är standard beteendet.
>
> Du behöver behörighet för prenumerations nivå för att ange kvot på arbets ytans nivå. Detta gäller så att enskilda arbets ytans ägare inte kan redigera eller öka sina kvoter och starta encroaching till resurser som har tagits ur bruk för en annan arbets yta. Därför passar en prenumerations administratör bäst att allokera och distribuera dessa kvoter mellan arbets ytor.



## <a name="view-your-usage-and-quotas"></a>Visa användning och kvoter

Att visa din kvot för olika resurser, till exempel Virtual Machines, lagring, nätverk, är enkelt genom Azure Portal.

1. Välj **alla tjänster** i den vänstra rutan och välj sedan **prenumerationer** under kategorin Allmänt.

1. I listan över prenumerationer väljer du den prenumeration vars kvot du söker.

   **Det finns en**varning, särskilt för att Visa Azure Machine Learning beräknings kvoten. Som nämnts ovan separeras kvoten från beräknings kvoten för din prenumeration.

1. I den vänstra rutan väljer du **Machine Learning tjänst** och väljer sedan en arbets yta i listan som visas

1. På nästa blad går du till **avsnittet support och fel sökning** och väljer **användning och kvoter** för att visa dina aktuella kvot gränser och användning.

1. Välj en prenumeration för att Visa kvot gränserna. Kom ihåg att filtrera till den region som du är intresse rad av.

1. Nu kan du växla mellan en vy på prenumerations nivå och en arbets yta på nivån:
    + **Prenumerationsvy:** På så sätt kan du Visa din användning av kärn kvoten för VM-serien, expandera den efter arbets ytan och utöka den ytterligare med hjälp av de faktiska kluster namnen. Den här vyn är optimal för att snabbt komma igång med information om kärn användningen för en viss VM-serie för att se avbrottet per arbets yta och ytterligare av de underliggande klustren för var och en av dessa arbets ytor. Den allmänna konventionen i den här vyn är (användning/kvot), där användningen är det aktuella antalet skalade kärnor och kvoten är det logiska maximala antalet kärnor som resursen kan skalas till. För varje **arbets yta**skulle kvoten vara kvoten för arbets ytans nivå (enligt beskrivningen ovan) som anger det maximala antalet kärnor som du kan skala till för en viss VM-serie. För ett **kluster** på samma sätt är kvoten i själva verket de kärnor som motsvarar det högsta antalet noder som klustret kan skala till som definieras av egenskapen max_nodes.

    + **Vyn arbets yta:** På så sätt kan du Visa din användning av kärn kvoten per arbets yta, expandera den av VM-serien och utöka den ytterligare genom att utöka den med de faktiska kluster namnen. Den här vyn är optimal för att snabbt komma igång med information om kärn användningen för en viss arbets yta för att se upprensningen av VM-familjer och ytterligare av de underliggande klustren för var och en av dessa familjer.

## <a name="request-quota-increases"></a>Begär kvotökningar

Om du vill höja gränsen eller kvoten över standard gränsen kan du [öppna en kund support förfrågan online](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/) utan kostnad.

Gränserna kan inte höjas ovanför det maximala gräns värde som visas i tabellerna. Om det inte finns någon maxgräns har resursen inte några justerbara gränser. I [den här](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors) artikeln beskrivs processen öka kvoten mer detaljerat.

När du begär en kvot ökning måste du välja den tjänst som du begär för att öka kvoten mot, vilket kan vara tjänster som Machine Learning tjänst kvot, container instanser eller lagrings kvot. Förutom Azure Machine Learning Compute kan du klicka på knappen **begär kvot** och Visa kvoten enligt stegen ovan.

> [!NOTE]
> [Kostnads fria utvärderings prenumerationer](https://azure.microsoft.com/offers/ms-azr-0044p) är inte berättigade till begränsning eller kvot ökningar. Om du har en [kostnads fri utvärderings prenumeration](https://azure.microsoft.com/offers/ms-azr-0044p)kan du uppgradera till en prenumeration [där du betalar per](https://azure.microsoft.com/offers/ms-azr-0003p/) användning. Mer information finns i [uppgradera den kostnads fria utvärderings versionen av Azure till "betala per](../billing/billing-upgrade-azure-subscription.md) användning" och [vanliga frågor om utvärderings prenumeration](https://azure.microsoft.com/free/free-account-faq).
