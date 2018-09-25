---
title: Hur du hanterar och begära kvoter för Azure Machine Learning-tjänsten
description: Den här guiden beskrivs de olika kvoterna på resurser för Azure Machine Learning och hur du visar och begär högre kvot.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nishankgu
ms.author: nigup
ms.date: 9/24/2018
ms.openlocfilehash: 0ee0a29be6a313e09cd77051dc29bc0131b57a40
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46997819"
---
# <a name="manage-and-request-quotas-for-azure-resources"></a>Hantera och begära kvoter för Azure-resurser

Som med andra Azure-tjänster, det finns begränsningar för vissa resurser som är associerade med Azure Machine Learning-tjänsten. Dessa gränser mellan en gräns för antalet arbetsytor som du kan skapa gränserna på den faktiska förbrukningen av beräkningsresurser som får användas för utbildning eller inferensjobb dina modeller. Den här artikeln ger mer information om förkonfigurerade begränsningar i olika Azure-resurser för din prenumeration och innehåller också praktiska länkar på begäran kvot förbättringar för varje typ av resurs.

Tänk på dessa kvoter du design- och skala upp dina Azure ML-resurser. Exempelvis om klustret inte når målantalet noder som du angav, kanske sedan du har nått en gräns för Batch AI-kärnor för prenumerationen. Om du planerar att köra produktionsarbetsbelastningar i Batch AI kan du behöva öka en eller flera av kvoter ovanför standardvärdet. Om du vill höja gränsen eller kvot över gränsen som standard öppnar du en kundsupportärende utan kostnad. Gränserna kan inte höjas ovan maxgränsen värdet som visas i följande tabeller. Om det finns ingen övre gräns-kolumn måste har inte resursen justerbara begränsningar. 

## <a name="special-considerations"></a>Att tänka på

+ En kvot är en kreditgräns, inte en garanti för kapacitet. Om du har programpaketfunktionen behov kan du kontakta Azure-supporten.

+ Din kvot delas mellan alla tjänster, förutom Batch AI i din prenumeration, Azure Machine Learning är en av dem. Glöm inte att beräkna kvotanvändning för alla tjänster när du utvärderar din kapacitetsbehoven.

+ Standardgränser variera beroende på erbjudandets kategorityp, t.ex kostnadsfri utvärderingsversion, betala per användning och serie, t.ex Dv2, F, G, och så vidare.

## <a name="default-resource-quotas"></a>Standard resurskvoter

Här är en uppdelning av kvotgränser av olika typer av resurser i Azure-prenumerationen. 

> [!Important]
> Gränserna kan komma att ändras. Den senaste versionen alltid finns på tjänstnivå kvoten [dokumentet](https://docs.microsoft.com/azure/azure-subscription-service-limits/) för hela Azure.

### <a name="virtual-machines"></a>Virtuella datorer 
Det finns en gräns för antalet virtuella datorer som du kan etablera på en Azure-prenumeration över dina tjänster eller i ett fristående sätt. Den här gränsen är på regionsnivån på totalt antal kärnor och också på basis av per familj.

Det är viktigt att betona att virtuella datorkärnor har en regional total gräns och en regional per storleksgränsen för serie (Dv2, F osv.) som tillämpas separat. Anta till exempel att en prenumeration i regionen USA, östra har en gräns för totalt antal VM-kärnor på 30, en gräns för antal kärnor i A-serien på 30 och en gräns för antal kärnor i D-serien på 30. Den här prenumerationen kan distribuera 30 virtuella datorer i A1-serien eller 30 virtuella datorer i D1-serien, eller en kombination av båda som inte överstiger sammanlagt 30 kärnor (t.ex. 10 virtuella datorer i A1-serien och 20 virtuella datorer i D1-serien).

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../../includes/azure-subscription-limits-azure-resource-manager.md)]

En mer utförlig och aktuell lista över kvotgränser, hittar du i Azure hela kvoten artikeln [här](https://docs.microsoft.com/azure/azure-subscription-service-limits#subscription-limits-1).

### <a name="batch-ai-clusters"></a>Batch AI-kluster
I Batch AI har en standardgräns för kvoten för antal kärnor såväl antalet kluster tillåts per region för en prenumeration. Batch AI-kvoter skiljer sig från den virtuella datorn kärnkvoten ovan och core-gränser delas inte för närvarande mellan de två resurstyperna.

Tillgängliga resurser:
+ Reserverade kärnor per region har en standardgräns för 10-24.  Antalet reserverade kärnor per Batch AI-prenumeration kan ökas. Kontakta Azure-supporten diskutera alternativ för utökning.

+ Lågprioritetskärnor per region har en standardgräns för 10-24.  Antalet lågprioritetskärnor per Batch AI-prenumeration kan ökas. Kontakta Azure-supporten diskutera alternativ för utökning.

+ Kluster per region har en standardgräns 20 och maxgränsen på 200. Kontakta Azure-supporten om du vill begära en ökning utöver denna gräns.

En mer utförlig och aktuell lista över kvotgränser, hittar du i Azure hela kvoten artikeln [här](https://docs.microsoft.com/azure/azure-subscription-service-limits#batch-ai-limits).

### <a name="container-instances"></a>Containerinstanser

Det finns också en gräns för antalet behållarinstanser som kan sätta upp i en viss tidsperiod (begränsade per timme) eller i hela prenumerationen.

[!INCLUDE [container-instances-limits](../../../includes/container-instances-limits.md)]

En mer utförlig och aktuell lista över kvotgränser, hittar du i Azure hela kvoten artikeln [här](https://docs.microsoft.com/azure/azure-subscription-service-limits#container-instances-limits).

### <a name="storage"></a>Storage
Det finns en gräns för antalet storage-konton per region samt i en viss prenumeration. Standardgränsen är 200 och inkluderar både Standard och Premium Storage-konton. Om du behöver fler än 200 lagringskonton i en viss region kan skicka en förfrågan via [Azure-supporten](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/). Azure Storage-teamet granskar ditt affärsfall och kan godkänna upp till 250 lagringskonton för en viss region.


## <a name="find-your-quotas"></a>Hitta dina kvoter

Visa din kvot för olika resurser, till exempel virtuella datorer, lagring, nätverk, är enkelt via Azure portal.

1. I det vänstra fönstret, Välj **alla tjänster** och välj sedan **prenumerationer** under kategorin Allmänt.

1. Välj den prenumeration vars kvot som du letar efter listan över prenumerationer.

   **Det finns ett villkor för**, särskilt för att visa Batch AI-kvoten. Som nämnts ovan är är den kvoten separat från compute-kvoten för din prenumeration. 
   För Batch AI, när du har valt **alla tjänster**, Sök efter Batch AI och öppna tjänsten.

1. Under **inställningar**väljer **användning + kvoter** att visa din aktuella kvotgränser och användning.

1. Välj din prenumeration för att visa kvotgränserna. Kom ihåg att filtrera till tjänsten och region som du är intresserad av.


## <a name="request-quota-increases"></a>Kvoter för begäran

Om du vill höja gränsen eller kvot än Standardgränsen [öppna en supportbegäran online-kund](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/) utan kostnad. 

Gränserna kan inte höjas ovan maxgränsen värdet som visas i tabellerna. Om det finns ingen högsta gräns, har inte resursen justerbara begränsningar. [Detta](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors) artikeln beskriver hur kvot ökning i detalj.

När begära en utökad kvot måste du markera den tjänst som du begär för att öka kvoten mot, vilket kan vara tjänster, till exempel AzureML-kvot eller Batch AI-kvot lagringskvoten. 

> [!NOTE]
> [Kostnadsfria utvärderingsversioner](https://azure.microsoft.com/offers/ms-azr-0044p) berättigar inte till gränsen eller kvot ökar. Om du har en [kostnadsfri utvärderingsprenumeration](https://azure.microsoft.com/offers/ms-azr-0044p), du kan uppgradera till en [användningsbaserad](https://azure.microsoft.com/offers/ms-azr-0003p/) prenumeration. Mer information finns i [uppgradera kostnadsfri utvärderingsversion av Azure till betala per användning](../../billing/billing-upgrade-azure-subscription.md) och [kostnadsfri utvärderingsprenumeration vanliga frågor och svar](https://azure.microsoft.com/free/free-account-faq).
