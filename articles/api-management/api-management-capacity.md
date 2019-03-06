---
title: Kapacitet för en Azure API Management-instans | Microsoft Docs
description: Den här artikeln förklarar vad kapacitet mått är och hur du fatta välgrundade beslut om du vill skala en Azure API Management-instans.
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
ms.openlocfilehash: fe77361c4c9bed9310f8443ed4ff37faf7ea53a9
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2019
ms.locfileid: "57454515"
---
# <a name="capacity-of-an-azure-api-management-instance"></a>Kapaciteten för en Azure API Management-instans

**Kapacitet** är enskilt viktigaste [Azure Monitor-mått](api-management-howto-use-azure-monitor.md#view-metrics-of-your-apis) för att göra välgrundade beslut om du vill skala en API Management-instans för att hantera högre belastning. Dess konstruktion är komplex och inför vissa beteende.

Den här artikeln förklarar vad det **kapacitet** är och hur den fungerar. Den visar hur du kommer åt **kapacitet** mått i Azure-portalen och ger förslag på när du ska du överväga att skala eller uppgraderar din API Management-instans.

## <a name="prerequisites"></a>Förutsättningar

Om du vill följa stegen från den här artikeln, måste du ha:

+ En aktiv Azure-prenumeration.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ En APIM-instansen. Mer information finns i [skapa en Azure API Management-instans](get-started-create-service-instance.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="what-is-capacity"></a>Vad är kapacitet

![Kapacitetsmått](./media/api-management-capacity/capacity-ingredients.png)

**Kapacitet** är en indikator för belastningen på ett APIM-instansen. Visas Resursanvändning (processor, minne) och kölängder för nätverket. CPU och minnesanvändning visar resurser förbrukning av:

+ APIM-tjänster, till exempel hantering åtgärder eller begäran om bearbetning, vilket kan innefatta vidarebefordrar begäranden eller köra en Grupprincip
+ valda operativsystemprocesser, inklusive processer som inbegriper kostnaden för SSL-handskakningar på nya anslutningar.

Totalt antal **kapacitet** värdet ett medelvärde av värdena från varje enhet av en API Management-instans.

## <a name="capacity-metric-behavior"></a>Kapacitet måttbeteende

På grund av dess konstruktion i verkligheten **kapacitet** kan påverkas av många faktorer, till exempel:

+ mönster för anslutningen (ny anslutning på en begäran vs återanvända den befintliga anslutningen)
+ storleken på en begäran och svar
+ principer som konfigureras i varje API- eller antal klienter skickar begäranden.

Mer avancerade åtgärder i begäranden är, desto högre den **kapacitet** kommer att vara i användning. Exempelvis förbruka komplexa omvandlingsprinciper mycket mer Processorkraft än en enkel begäran-vidarebefordran. Långsamma backend-svar för tjänsten kommer att öka det för.

> [!IMPORTANT]
> **Kapacitet** är inte ett direkt mått på hur många begäranden som bearbetas.

![Kapacitet mått toppar](./media/api-management-capacity/capacity-spikes.png)

**Kapacitet** kan också utnyttja periodvis eller vara större än noll, även om det finns inga förfrågningar som bearbetas. Det inträffar på grund av system - eller plattformsspecifika åtgärder och bör inte beaktas när du bestämmer om du vill skala en instans.
  
## <a name="use-the-azure-portal-to-examine-capacity"></a>Använda Azure Portal för att granska kapacitet
  
![Kapacitetsmått](./media/api-management-capacity/capacity-metric.png)  

1. Navigera till APIM-instansen i den [Azure-portalen](https://portal.azure.com/).
2. Välj **mått (förhandsgranskning)**.
3. Välj avsnittet lila **kapacitet** mått från tillgängliga mått och lämna standardvärdet **genomsn** aggregering.

    > [!TIP]
    > Du bör alltid visas i en **kapacitet** mått uppdelade efter plats för att undvika fel tolkningar.

4. Från avsnittet grön Välj **plats** för att dela måttet per dimension.
5. Välj ett önskat tidsintervall från det översta fältet i avsnittet.

    Du kan ange en metrisk varning så att du vet när något oväntat sker. Till exempel få meddelanden när APIM-instansen har tagits överskrider sin förväntad maximal kapacitet för över 20 minuter.

    >[!TIP]
    > Du kan konfigurera aviseringar så att du vet när tjänsten har brist på kapacitet eller använda Azure Monitor-funktion för automatisk skalning att automatiskt lägga till en Azure API Management-enhet. Skala åtgärden kan ta upp till 30 minuter, så du bör planera dina regler på lämpligt sätt.  
    > Endast skalning master plats tillåts.

## <a name="use-capacity-for-scaling-decisions"></a>Använd kapacitet för att skala beslut

**Kapacitet** är mått för att fatta beslut om du vill skala en API Management-instans för att hantera högre belastning. Överväg att:

+ Titta på en långsiktig trend och genomsnitt.
+ Ignorerar plötsliga toppar som mest sannolikt inte relaterade till alla belastningsökningar (se avsnittet ”kapacitet måttbeteende” förklaring).
+ Uppgradera eller skala din instans när **kapacitet**'s värdet överskrider 60% eller 70% under en längre tid (till exempel 30 minuter). Olika värden fungera bättre för din tjänst eller scenario.

>[!TIP]  
> Om du kan beräkna din trafik i förväg, testa APIM-instansen på arbetsbelastningar som du förväntar dig. Du kan öka belastningen på din klient gradvis och övervaka vilka värdet kapacitet måttet motsvarar hög belastning. Följ stegen i föregående avsnitt du använder Azure-portalen för att förstå hur mycket kapacitet används vid en given tidpunkt.

## <a name="next-steps"></a>Nästa steg

[Hur skala eller uppgradera en Azure API Management-tjänstinstans](upgrade-and-scale.md)