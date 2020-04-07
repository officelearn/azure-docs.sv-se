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
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756605"
---
# <a name="manage-and-request-quotas-for-azure-resources"></a>Hantera och begära kvoter för Azure-resurser
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Den här artikeln innehåller information om förkonfigurerade gränser för Azure-resurser för din prenumeration. Dessutom ingår instruktioner om hur du begär kvotförbättringar för varje typ av resurs. Dessa gränser har införts för att förhindra budgetöverkörningar på grund av bedrägeri och för att uppfylla Azure-kapacitetsbegränsningar.

Precis som med andra Azure-tjänster finns det gränser för vissa resurser som är associerade med Azure Machine Learning. Dessa gränser sträcker sig från ett tak för antalet arbetsytor till begränsningar för den faktiska underliggande beräkning som används för modellträning eller slutledning/bedömning. 

När du utformar och skalar dina Azure Machine Learning-resurser för produktionsarbetsbelastningar bör du tänka på dessa gränser. Om ditt kluster till exempel inte når målantalet noder kan du ha nått en Azure Machine Learning Compute-kärnor för din prenumeration. Om du vill höja gränsen eller kvoten över standardgränsen öppnar du en kundsupportbegäran online utan kostnad. Gränserna kan inte höjas över det högsta gränsvärdet som visas i följande tabeller på grund av begränsningar för Azure-kapacitet. Om det inte finns någon maxgränskolumn har resursen inga justerbara gränser.

## <a name="special-considerations"></a>Särskilda överväganden

+ En kvot är en kreditgräns, inte en kapacitetsgaranti. Om du har stora kapacitetsbehov kontaktar du Azure-supporten.

+ Din kvot delas mellan alla tjänster i dina prenumerationer, inklusive Azure Machine Learning. Det enda undantaget är Azure Machine Learning-beräkning som har en separat kvot från kärnberäkningskvoten. Var noga med att beräkna kvotanvändningen för alla tjänster när du utvärderar dina kapacitetsbehov.

+ Standardgränserna varierar beroende på erbjudandekategorityp, till exempel kostnadsfri utvärderingsversion, användningsbaserad betalning och VM-serie, till exempel Dv2, F, G och så vidare.

## <a name="default-resource-quotas"></a>Standardresurskvoter

Här är en uppdelning av kvotgränserna efter olika resurstyper i din Azure-prenumeration.

> [!IMPORTANT]
> Gränser kan komma att ändras. Det senaste finns alltid på [kvotdokumentet](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits/) på tjänstnivå för hela Azure.

### <a name="virtual-machines"></a>Virtuella datorer
För varje Azure-prenumeration finns det en gräns för hur många virtuella datorer du kan ha för alla dina tjänster eller fristående. Denna gräns är på regionnivå både på den totala kärnor och även på en per familj basis.

Virtuella maskinkärnor har en regional totalgräns och en regional serie per storlek (Dv2, F, etc.) gräns, som båda tillämpas separat. Anta till exempel att en prenumeration i regionen USA, östra har en gräns för totalt antal VM-kärnor på 30, en gräns för antal kärnor i A-serien på 30 och en gräns för antal kärnor i D-serien på 30. Den här prenumerationen kan distribuera 30 virtuella datorer i A1-serien eller 30 virtuella datorer i D1-serien, eller en kombination av båda som inte överstiger sammanlagt 30 kärnor (t.ex. 10 virtuella datorer i A1-serien och 20 virtuella datorer i D1-serien).

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../includes/azure-subscription-limits-azure-resource-manager.md)]

En mer detaljerad och aktuell lista över kvotgränser finns i kvotartikeln för hela Azure [här](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits).

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning-beräkning
För Azure Machine Learning Compute finns det en standardkvotgräns för både antalet kärnor och antalet unika beräkningsresurser som tillåts per region i en prenumeration. Den här kvoten är skild från kärnkvoten för den virtuella datorn ovan och kärngränserna delas inte mellan de två resurstyperna eftersom AmlCompute är en hanterad tjänst som distribuerar resurser i en värdbaserad modell för den skull.

Tillgängliga resurser:
+ Dedikerade kärnor per region har en standardgräns på 24 till 300 beroende på din prenumerationserbjudandetyp med högre standardvärden för EA- och CSP-erbjudandetyper.  Antalet dedikerade kärnor per prenumeration kan ökas och är olika för varje vm-familj. Vissa specialiserade VM-familjer som NCv2, NCv3 eller ND-serien börjar med en standard på noll kärnor. Kontakta Azure-supporten genom att öka en kvotbegäran för att diskutera ökningsalternativ.

+ Lågprioriterade kärnor per region har en standardgräns på 100– 3000 beroende på din prenumerationserbjudandetyp med högre standardvärden för EA- och CSP-erbjudandetyper. Antalet lågprioriterade kärnor per prenumeration kan ökas och är ett enda värde för virtuella vm-familjer. Kontakta Azure-supporten för att diskutera ökningsalternativ.

+ Kluster per region har en standardgräns på 200. Dessa delas mellan ett utbildningskluster och en beräkningsinstans (som betraktas som ett enda nodkluster för kvotändamål). Kontakta Azure-supporten om du vill begära en ökning utöver den här gränsen.

+ Det finns andra strikta gränser som inte kan överskridas när träffen.

| **Resurs** | **Högsta gräns** |
| --- | --- |
| Maximala arbetsytor per resursgrupp | 800 |
| Maximala noder i en enda Azure Machine Learning Compute -resurs (AmlCompute) | 100 noder |
| Maximal GPU MPI-processer per nod | 1-4 |
| Maximal GPU-arbetare per nod | 1-4 |
| Maximal livslängd för jobbet | 90 dagar<sup>1</sup> |
| Maximal jobblivslängd på en nod med låg prioritet | 7 dagar<sup>2</sup> |
| Maximala parameterservrar per nod | 1 |

<sup>1</sup> Den maximala livslängden avser den tid som en körning startar och när den är klar. Slutförda körningar kvarstår på obestämd tid. data för körningar som inte har slutförts inom den maximala livslängden är inte tillgängliga.
<sup>2</sup> Jobb på en nod med låg prioritet kan föregripas när helst det finns en kapacitetsbegränsning. Vi rekommenderar att du implementerar kontrollpunkter i ditt jobb.

### <a name="azure-machine-learning-pipelines"></a>Azure Machine Learning Pipelines
För Azure Machine Learning Pipelines finns det en kvotgräns för antalet steg i en pipeline och antalet schemabaserade körningar av publicerade pipelines per region i en prenumeration.
- Maximalt antal steg som tillåts i en pipeline är 30 000
- Maximalt antal av summan av schemabaserade körningar och blob-dragningar för bloggutlösta scheman för publicerade pipelines per prenumeration och månad är 100 000

> [!NOTE]
> Om du vill öka den här gränsen kontaktar du [Microsoft Support](https://azure.microsoft.com/support/options/).

### <a name="container-instances"></a>Containerinstanser

Det finns också en gräns för hur många behållarinstanser som du kan snurra upp under en viss tidsperiod (begränsad per timme) eller över hela prenumerationen.

[!INCLUDE [container-instances-limits](../../includes/container-instances-limits.md)]

En mer detaljerad och aktuell lista över kvotgränser finns i kvotartikeln för hela Azure [här](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#container-instances-limits).

### <a name="storage"></a>Storage
Det finns en gräns för antalet lagringskonton per region samt i en viss prenumeration. Standardgränsen är 250 och innehåller både Standard- och Premium Storage-konton. Om du behöver fler än 250 lagringskonton i en viss region kan du göra en begäran via [Azure Support](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/). Azure Storage-teamet granskar ditt affärsfall och kan godkänna upp till 250 lagringskonton för en viss region.


## <a name="workspace-level-quota"></a>Kvot på arbetsytasnivå

För att bättre hantera resursallokeringar för Amlcompute mellan olika arbetsytor har vi infört en funktion som gör att du kan distribuera prenumerationsnivåkvoter (efter VM-familj) och konfigurera dem på arbetsytasnivå. Standardbeteendet är att alla arbetsytor har samma kvot som prenumerationsnivåkvoten för alla vm-familjer. Men eftersom antalet arbetsytor ökar och arbetsbelastningar med varierande prioritet börjar dela samma resurser, vill användarna ha ett sätt att bättre dela kapacitet och undvika problem med resurskonkurrens. Azure Machine Learning tillhandahåller en lösning med sitt hanterade beräkningserbjudande genom att tillåta användare att ange en maximal kvot för en viss virtuell datorfamilj på varje arbetsyta. Detta är analogt med att fördela kapaciteten mellan arbetsytor, och användarna kan välja att också överallokera för att driva maximal användning. 

Om du vill ange kvoter på arbetsytan går du till valfri arbetsyta i prenumerationen och klickar på **Förbrukningar + kvoter** i den vänstra rutan. Välj sedan fliken **Konfigurera kvoter** om du vill visa kvoterna, expandera en virtuell datorfamilj och ange en kvotgräns för alla arbetsytor som visas under den virtuella datorn. Kom ihåg att du inte kan ange ett negativt värde eller ett värde som är högre än prenumerationsnivåkvoten. Som du kan observera tilldelas dessutom alla arbetsytor som standard hela prenumerationskvoten för att möjliggöra full användning av den tilldelade kvoten.

[![Kvot på azure machine learning-arbetsyta](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)


> [!NOTE]
> Detta är endast en Enterprise Edition-funktion. Om du har både en Basic- och en Enterprise Edition-arbetsyta i prenumerationen kan du använda den för att bara ange kvoter på enterprise-arbetsytorna. Dina grundläggande arbetsytor fortsätter att ha kvoten för prenumerationsnivå som är standardbeteendet.
>
> Du behöver behörigheter på prenumerationsnivå för att ange kvot på arbetsytasnivå. Detta tillämpas så att enskilda arbetsplatsägare inte redigerar eller ökar sina kvoter och börjar inkräkta på resurser som avsatts för en annan arbetsyta. Därför är en prenumerationsadministratör bäst lämpad att allokera och distribuera dessa kvoter över arbetsytor.



## <a name="view-your-usage-and-quotas"></a>Visa din användning och dina kvoter

Det är enkelt att visa kvoten för olika resurser, till exempel virtuella datorer, lagring, nätverk, via Azure-portalen.

1. I den vänstra rutan väljer du **Alla tjänster** och väljer sedan **Prenumerationer** under kategorin Allmänt.

1. Välj den prenumeration vars kvot du söker i listan över prenumerationer.

   **Det finns en varning**, särskilt för att visa Azure Machine Learning Compute kvot. Som nämnts ovan är den kvoten skild från beräkningskvoten för din prenumeration.

1. I den vänstra rutan väljer du **Machine Learning-tjänst** och väljer sedan en arbetsyta i listan som visas

1. På nästa blad väljer du Användning + **kvoter** under **avsnittet Support + felsökning** för att visa dina aktuella kvotgränser och användning.

1. Välj en prenumeration för att visa kvotgränserna. Kom ihåg att filtrera till den region du är intresserad av.

1. Nu kan du växla mellan en prenumerationsnivåvy och en arbetsytenivåvy:
    + **Prenumerationsvy:** På så sätt kan du visa din användning av kärnkvoten efter vm-familj, utöka den efter arbetsyta och ytterligare utöka den med de faktiska klusternamnen. Den här vyn är optimal för att snabbt komma in i information om grundläggande användning för en viss virtuell dator familj för att se upplösningen av arbetsytor och ytterligare av de underliggande kluster för var och en av dessa arbetsytor. Den allmänna konventionen i den här vyn är (användning/kvot), där användningen är det aktuella antalet skalade kärnor och kvot är det logiska maximala antalet kärnor som resursen kan skala till. För varje **arbetsyta**är kvoten kvoten för arbetsytan (som förklaras ovan) som anger det maximala antalet kärnor som du kan skala till för en viss virtuell datorfamilj. För ett **kluster** på samma sätt är kvoten faktiskt de kärnor som motsvarar det maximala antalet noder som klustret kan skalas till definierat av egenskapen max_nodes.

    + **Arbetsyta vy:** På så sätt kan du visa din användning av kärnkvoten efter arbetsyta, utöka den efter vm-familjen och ytterligare utöka den med de faktiska klusternamnen. Den här vyn är optimal för att snabbt komma in i detaljerna i grundläggande användning för en viss arbetsyta för att se upplösningen av virtuella virtuella familjer och ytterligare av de underliggande kluster för var och en av dessa familjer.

## <a name="request-quota-increases"></a>Begär kvotökningar

Om du vill höja gränsen eller kvoten över standardgränsen [öppnar du en kundsupportbegäran online](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/) utan kostnad.

Gränserna kan inte höjas över det maximala gränsvärdet som visas i tabellerna. Om det inte finns någon maximal gräns har resursen inga justerbara gränser. [Den här](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors) artikeln täcker processen för kvotökning mer i detalj.

När du begär en kvotökning måste du välja den tjänst som du begär att höja kvoten mot, vilket kan vara tjänster som Machine Learning-tjänstkvot, behållarinstanser eller lagringskvot. Dessutom för Azure Machine Learning Compute kan du klicka på knappen **Begär kvot** när du visar kvoten enligt stegen ovan.

> [!NOTE]
> [Kostnadsfria utvärderingsprenumerationer](https://azure.microsoft.com/offers/ms-azr-0044p) är inte berättigade till limit- eller kvothöjningar. Om du har en [kostnadsfri utvärderingsprenumeration](https://azure.microsoft.com/offers/ms-azr-0044p)kan du uppgradera till en [prenumeration på användningsbaserad betalning.](https://azure.microsoft.com/offers/ms-azr-0003p/) Mer information finns i [Uppgradera Azure Free Trial till Pay-As-You-Go](../billing/billing-upgrade-azure-subscription.md) och Gratis [utvärderingsversion.](https://azure.microsoft.com/free/free-account-faq)
