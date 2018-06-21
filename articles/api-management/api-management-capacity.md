---
title: Kapacitet för en instans av Azure API Management | Microsoft Docs
description: Den här artikeln förklarar måtten kapacitet är och hur att fatta välgrundade beslut om du vill skala en Azure API Management-instans.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 06/18/2018
ms.author: apimpm
ms.openlocfilehash: 4983854a14a6efe9214692dc677dedeada73933b
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2018
ms.locfileid: "36296108"
---
# <a name="capacity-of-an-azure-api-management-instance"></a>Kapacitet för en instans av Azure API Management

**Kapacitet** är enskilt viktigaste [Azure-Monitor mått](api-management-howto-use-azure-monitor.md#view-metrics-of-your-apis) för att göra välgrundade beslut om du vill skala en API Management-instans för att hantera mer belastning. Dess konstruktion är komplex och inför vissa funktioner.

Den här artikeln förklarar vad det **kapacitet** är och hur den fungerar. Den visar hur du kommer åt **kapacitet** mått i Azure-portalen och ger förslag på när du ska överväga skalning eller uppgradera din API Management-instans.

## <a name="prerequisites"></a>Förutsättningar

Om du vill följa stegen från den här artikeln, måste du ha:

+ En aktiv Azure-prenumeration.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ En APIM-instans. Mer information finns i [skapa en instans av Azure API Management](get-started-create-service-instance.md).

## <a name="what-is-capacity"></a>Vad är kapacitet

![Kapacitet mått](./media/api-management-capacity/capacity-ingredients.png)

**Kapacitet** är en indikator på belastningen på en APIM-instans. Den återspeglar användning av resurser (CPU, minne) och kölängder för nätverket. Processor- och minnesanvändning visar resurser förbrukningen av:

+ APIM tjänster, till exempel management åtgärder eller begäran om bearbetning, vilket kan innefatta vidarebefordrar begäranden eller köra en Grupprincip
+ valda operativsystemprocesser, inklusive processer som inbegriper kostnaden för SSL-handskakningar på på nya anslutningar.

Totalt antal **kapacitet** medelvärdet av värdena från varje enhet med en API Management-instans.

## <a name="capacity-metric-behavior"></a>Kapacitet mått beteende

På grund av dess konstruktion i verkligheten **kapacitet** kan påverkas av många faktorer, till exempel:

+ anslutningen mönster (ny anslutning på en begäran om vs återanvända den befintliga anslutningen)
+ storleken på en begäran och svar
+ principer som konfigurerats på varje API eller antalet klienter som skickar begäranden.

Mer komplexa åtgärder på begäran är, desto högre den **kapacitet** förbrukning blir. Till exempel använda principer för komplexa anspråksomvandling mycket mer CPU än en enkel begäran vidarebefordran. Långsam backend service svar ökar den för.

> [!IMPORTANT]
> **Kapacitet** är inte ett direkt mått av antalet begäranden som bearbetas.

![Kapacitet mått toppar](./media/api-management-capacity/capacity-spikes.png)

**Kapacitet** kan också ökar periodvis eller kan vara större än noll även om det finns inga förfrågningar som bearbetas. Det händer på grund av system - eller plattformsspecifika åtgärder och ska inte tas med i beräkningen när du bestämmer om du vill skala en instans.
  
## <a name="use-the-azure-portal-to-examine-capacity"></a>Använda Azure Portal för att undersöka kapacitet
  
![Kapacitet mått](./media/api-management-capacity/capacity-metric.png)  

1. Navigera till din APIM instans i den [Azure-portalen](https://portal.azure.com/).
2. Välj **mått (förhandsgranskning)**.
3. I avsnittet lila väljer **kapacitet** mått från tillgängliga mått och lämna standard **Avg** aggregering.

    > [!TIP]
    > Du bör alltid visas i en **kapacitet** mått uppdelning per plats för att undvika fel tolkningar.

4. I avsnittet grön väljer **plats** för att dela måttet per dimension.
5. Välj en önskad tidsram från överst i avsnittet.

    Du kan ange en mått avisering så att du vet när något oväntat händer. Till exempel få meddelanden när APIM-instansen har exceededing sin förväntad maximal kapacitet för över 20 minuter.

    >[!TIP]
    > Du kan konfigurera aviseringar så att du vet när tjänsten har brist på kapacitet eller använda Azure-Monitor autoskalning funktionen för att automatiskt lägga till en Azure API Management-enhet. Skalning åtgärden kan ta runt 30 minuter, så du bör planera dina regler i enlighet med detta.  
    > Endast skalning master platsen är tillåtet.

## <a name="use-capacity-for-scaling-decisions"></a>Använd kapacitet för skalning beslut

**Kapacitet** är mått för att fatta beslut om du vill skala en API Management-instans för att hantera mer belastning. Överväg att:

+ Tittar på ett beständigt trend och medelvärde.
+ Ignorerar plötsliga toppar som förmodligen inte relaterade till en ökning i belastning (se avsnittet ”kapacitet mått beteende” förklaring).
+ Uppgradera eller skala din instans när **kapacitet**'s värdet överskrider 60% eller 70% under en längre tidsperiod (till exempel 30 minuter). Olika värden kan fungera bättre för din tjänst eller ditt scenario.

>[!TIP]  
> Om du ska kunna beräkna trafik på förhand testa din APIM instans på arbetsbelastningar du förväntar dig. Du kan öka belastningen på din klient gradvis och övervaka vilka värdet kapacitet mått som motsvarar din belastning. Följ stegen i föregående avsnitt du använder Azure-portalen för att förstå hur mycket kapacitet används vid en given tidpunkt.

## <a name="next-steps"></a>Nästa steg

[Så här skalar eller uppgradera en Azure API Management service-instans](upgrade-and-scale.md)