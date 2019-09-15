---
title: Hantera och begära resurskvoter
titleSuffix: Azure Machine Learning
description: Den här guiden beskrivs de olika kvoterna på resurser för Azure Machine Learning och hur du visar och begär högre kvot.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nishankgu
ms.author: nigup
ms.date: 05/10/2019
ms.custom: seodec18
ms.openlocfilehash: 2ee819625f772c96ac6c5a771935da17ee05f3aa
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/15/2019
ms.locfileid: "71002695"
---
# <a name="manage-and-request-quotas-for-azure-resources"></a>Hantera och begära kvoter för Azure-resurser

Precis som med andra Azure-tjänster finns det gränser för vissa resurser som är kopplade till Azure Machine Learning. Dessa gränser sträcker sig från ett tak på antalet arbets ytor som du kan skapa för att begränsa den faktiska underliggande beräkning som används för modell utbildning eller härledning/bedömning. 

Den här artikeln ger mer information om förkonfigurerade begränsningar i olika Azure-resurser för din prenumeration och innehåller också praktiska länkar på begäran kvot förbättringar för varje typ av resurs. Dessa gränser lagts in för att förhindra att budget över körs på grund av bedrägeri och Använd Azure-kapacitet villkor.

Behåll dessa kvoter i åtanke när du utformar och skalar upp Azure Machine Learning resurser för produktions arbets belastningar. Om klustret till exempel inte når antalet noder som du har angett kan du ha nått gränsen för Azure Machine Learning beräknings kärnor för din prenumeration. Om du vill höja gränsen eller kvot över gränsen som standard öppnar du en kundsupportärende utan kostnad. Gränserna kan inte höjas ovan maxgränsen värdet som visas i följande tabeller på grund av Azure-kapacitet. Om det finns ingen övre gräns-kolumn måste har inte resursen justerbara begränsningar.

## <a name="special-considerations"></a>Att tänka på

+ En kvot är en kreditgräns, inte en garanti för kapacitet. Om du har programpaketfunktionen behov kan du kontakta Azure-supporten.

+ Din kvot delas mellan alla tjänster i dina prenumerationer, inklusive Azure Machine Learning. Det enda undantaget är Azure Machine Learning Compute som har en separat kvot från bas beräknings kvoten. Glöm inte att beräkna kvotanvändning för alla tjänster när du utvärderar din kapacitetsbehoven.

+ Standardgränser variera beroende på erbjudandets kategorityp, t.ex kostnadsfri utvärderingsversion, betala per användning och serie, t.ex Dv2, F, G, och så vidare.

## <a name="default-resource-quotas"></a>Standard resurskvoter

Här är en uppdelning av kvotgränser av olika typer av resurser i Azure-prenumerationen.

> [!Important]
> Gränserna kan komma att ändras. Den senaste versionen alltid finns på tjänstnivå kvoten [dokumentet](https://docs.microsoft.com/azure/azure-subscription-service-limits/) för hela Azure.

### <a name="virtual-machines"></a>Virtuella datorer
Det finns en gräns för antalet virtuella datorer som du kan etablera på en Azure-prenumeration över dina tjänster eller i ett fristående sätt. Den här gränsen är på regionsnivån på totalt antal kärnor och också på basis av per familj.

Det är viktigt att betona att virtuella datorkärnor har en regional total gräns och en regional per storleksgränsen för serie (Dv2, F osv.) som tillämpas separat. Anta till exempel att en prenumeration i regionen USA, östra har en gräns för totalt antal VM-kärnor på 30, en gräns för antal kärnor i A-serien på 30 och en gräns för antal kärnor i D-serien på 30. Den här prenumerationen kan distribuera 30 virtuella datorer i A1-serien eller 30 virtuella datorer i D1-serien, eller en kombination av båda som inte överstiger sammanlagt 30 kärnor (t.ex. 10 virtuella datorer i A1-serien och 20 virtuella datorer i D1-serien).

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../../includes/azure-subscription-limits-azure-resource-manager.md)]

En mer utförlig och aktuell lista över kvotgränser, hittar du i Azure hela kvoten artikeln [här](https://docs.microsoft.com/azure/azure-subscription-service-limits).

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning-beräkning
Det finns en standardgräns för kvot för både antalet kärnor och antal unika beräkningsresurser som tillåts per region för en prenumeration för Azure Machine Learning Compute. Den här kvoten skiljer sig från den virtuella datorn kärnkvoten ovan och core gränserna delas inte för närvarande mellan de två resurstyperna.

Tillgängliga resurser:
+ Dedikerade kärnor per region har en standard gräns på 24-300 beroende på typ av prenumerations erbjudande.  Antalet reserverade kärnor per prenumeration kan ökas. Kontakta Azure-supporten diskutera alternativ för utökning.

+ Kärnor med låg prioritet per region har en standard gräns på 24-300 beroende på typ av prenumerations erbjudande.  Antalet lågprioritetskärnor per prenumeration kan ökas. Kontakta Azure-supporten diskutera alternativ för utökning.

+ Kluster per region har en standardgräns på 100 och maxgränsen på 200. Kontakta Azure-supporten om du vill begära en ökning utöver denna gräns.

+ Det finns **andra mycket begränsat** som inte får överskridas når en gång.

| **Resurs** | **Övre gräns** |
| --- | --- |
| Maximal arbetsytor per resursgrupp | 800 |
| Högsta antalet noder i en enskild resurs i Azure Machine Learning Compute (AmlCompute) | 100 noder |
| Den maximala GPU MPI bearbetar per nod | 1 – 4 |
| Den maximala GPU arbetare per nod | 1 – 4 |
| Maximal jobbet livslängd | 90 dagar<sup>1</sup> |
| Maximal jobb livs längd för en nod med låg prioritet | 1 dag<sup>2</sup> |
| Maximal parametern servrar per nod | 1 |

<sup>1</sup> högsta livstid som refererar till den tid som en körning startas och när den är klar. Slutförda körningar kvar på obestämd tid; data för körs inte slutförs inom den maximala livstiden är inte tillgänglig.
<sup>2</sup> jobb på en nod med låg prioritet kan vara i förväg rekvirerad när det finns en kapacitets begränsning. Vi rekommenderar att du implementerar kontroll punkter i jobbet.

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
Det finns en gräns för antalet storage-konton per region samt i en viss prenumeration. Standardgränsen är 200 och inkluderar både Standard och Premium Storage-konton. Om du behöver fler än 200 lagrings konton i en specifik region gör du en begäran via [supporten för Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/). Azure Storage-teamet granskar ditt affärsfall och kan godkänna upp till 250 lagringskonton för en viss region.


## <a name="find-your-quotas"></a>Hitta dina kvoter

Visa din kvot för olika resurser, till exempel virtuella datorer, lagring, nätverk, är enkelt via Azure portal.

1. Välj **alla tjänster** i den vänstra rutan och välj sedan **prenumerationer** under kategorin Allmänt.

1. Välj den prenumeration vars kvot som du letar efter listan över prenumerationer.

   **Det finns ett villkor för**, särskilt för att visa Azure Machine Learning Compute-kvoten. Som nämnts ovan är är den kvoten separat från compute-kvoten för din prenumeration.

1. I den vänstra rutan väljer du **Machine Learning tjänst** och väljer sedan en arbets yta i listan som visas

1. På nästa blad under den **Support och felsökning** Välj **användning + kvoter** att visa din aktuella kvotgränser och användning.

1. Välj en prenumeration för att visa kvotgränserna. Kom ihåg att filtrera till den region som du är intresserad av.


## <a name="request-quota-increases"></a>Kvoter för begäran

Om du vill höja gränsen eller kvoten över standard gränsen kan du [öppna en kund support förfrågan online](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/) utan kostnad.

Gränserna kan inte höjas ovanför det maximala gräns värde som visas i tabellerna. Om det inte finns någon maxgräns har resursen inte några justerbara gränser. [Detta](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors) artikeln beskriver hur kvot ökning i detalj.

När begära en utökad kvot måste du markera den tjänst som du begär för att öka kvoten mot, vilket kan orsaka tjänster som Machine Learning-tjänstkvot, Container instances eller lagringskvoten. Dessutom för Azure Machine Learning Compute, kan du helt enkelt klicka på den **begär kvot** knappen medan du visar kvoten följa stegen ovan.

> [!NOTE]
> [Kostnadsfria utvärderingsversioner](https://azure.microsoft.com/offers/ms-azr-0044p) berättigar inte till gränsen eller kvot ökar. Om du har en [kostnadsfri utvärderingsprenumeration](https://azure.microsoft.com/offers/ms-azr-0044p), du kan uppgradera till en [användningsbaserad](https://azure.microsoft.com/offers/ms-azr-0003p/) prenumeration. Mer information finns i [uppgradera kostnadsfri utvärderingsversion av Azure till betala per användning](../../billing/billing-upgrade-azure-subscription.md) och [kostnadsfri utvärderingsprenumeration vanliga frågor och svar](https://azure.microsoft.com/free/free-account-faq).
