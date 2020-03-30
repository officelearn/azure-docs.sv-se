---
title: Kapacitet för en Azure API Management-instans | Microsoft-dokument
description: I den här artikeln beskrivs vad kapacitetsmåttet är och hur du fattar välgrundade beslut om du vill skala en Azure API Management-instans.
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
ms.custom: fasttrack-edit
ms.openlocfilehash: b6d949b50be348e72cedfc3710383308b04de106
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336012"
---
# <a name="capacity-of-an-azure-api-management-instance"></a>Kapacitet hos en Azure API Management-instans

**Kapacitet** är det viktigaste [Azure Monitor-måttet](api-management-howto-use-azure-monitor.md#view-metrics-of-your-apis) för att fatta välgrundade beslut om att skala en API Management-instans för att hantera mer belastning. Dess konstruktion är komplex och ställer på sig ett visst beteende.

I den här artikeln förklaras vad **kapaciteten** är och hur den beter sig. Den visar hur du kommer åt **kapacitetsmått** i Azure-portalen och föreslår när du ska överväga att skala eller uppgradera din API Management-instans.

> [!IMPORTANT]
> I den här artikeln beskrivs hur du kan övervaka och skala din Azure API Management-instans baserat på dess kapacitetsmått. Det är dock lika viktigt att förstå vad som händer när en enskild API Management-instans faktiskt har *nått* sin kapacitet. Azure API Management kommer inte att tillämpa någon begränsning på tjänstnivå för att förhindra en fysisk överbelastning av instanserna. När en instans når sin fysiska kapacitet, kommer det att bete sig som alla överbelastade webbserver som inte kan bearbeta inkommande begäranden: svarstiden kommer att öka, anslutningar kommer att tas bort, timeout-fel kommer att inträffa, etc. Detta innebär att API-klienter bör vara beredda att hantera denna möjlighet liknande som med alla andra externa tjänster (t.ex. genom att tillämpa återförsöksprinciper).

## <a name="prerequisites"></a>Krav

Om du vill följa stegen i den här artikeln måste du ha:

+ En aktiv Azure-prenumeration.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ En APIM-instans. Mer information finns i [Skapa en Azure API Management-instans](get-started-create-service-instance.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="what-is-capacity"></a>Vad är kapacitet

![Kapacitetsmått](./media/api-management-capacity/capacity-ingredients.png)

**Kapacitet** är en indikator på belastning på en API Management-instans. Den återspeglar resursanvändning (CPU, minne) och nätverkskölängder. CPU- och minnesanvändning visar resursförbrukning genom att:

+ API Management dataplan tjänster, till exempel begäran bearbetning, som kan omfatta vidarebefordran av begäranden eller köra en princip.
+ API Management management-plantjänster, till exempel hanteringsåtgärder som tillämpas via Azure Portal eller ARM, eller belastning som kommer från [utvecklarportalen](api-management-howto-developer-portal.md).
+ Valda operativsystemprocesser, inklusive processer som innebär kostnader för TLS-handslag på nya anslutningar.

Total **kapacitet** är ett genomsnitt av sina egna värden från varje enhet i en API Management-instans.

Även om **kapacitetsmåttet** är utformat för att visa problem med API Management-instansen, finns det fall där problem inte återspeglas i ändringar i **kapacitetsmåttet**.

## <a name="capacity-metric-behavior"></a>Beteende för kapacitetsmått

På grund av dess konstruktion, i verkliga livet **kapacitet** kan påverkas av många variabler, till exempel:

+ anslutningsmönster (ny anslutning på en begäran jämfört med återanvändning av den befintliga anslutningen)
+ storleken på en begäran och svar
+ principer som konfigurerats på varje API eller antal klienter som skickar begäranden.

Ju mer komplexa åtgärder som görs på begäranden är, desto högre blir **kapacitetsförbrukningen.** Komplexa omvandlingsprinciper förbrukar till exempel mycket mer CPU än en enkel vidarebefordran av begäran. Långsam backend service svar kommer att öka det också.

> [!IMPORTANT]
> **Kapaciteten** är inte ett direkt mått på antalet begäranden som behandlas.

![Kapacitetsmått spikar](./media/api-management-capacity/capacity-spikes.png)

**Kapaciteten** kan också öka periodvis eller vara större än noll även om det inte finns några begäranden som bearbetas. Det sker på grund av system- eller plattformsspecifika åtgärder och bör inte beaktas när man beslutar om en instans ska skalas.

**Mått med** låg kapacitet innebär inte nödvändigtvis att din API Management-instans inte har några problem.
  
## <a name="use-the-azure-portal-to-examine-capacity"></a>Använd Azure Portal för att undersöka kapacitet
  
![Kapacitetsmått](./media/api-management-capacity/capacity-metric.png)  

1. Navigera till APIM-instansen i [Azure-portalen](https://portal.azure.com/).
2. Välj **Mått**.
3. Välj **Kapacitetsmått** från tillgängliga mått i avsnittet Lila och lämna **standardg-aggregering.**

    > [!TIP]
    > Du bör alltid **capacity** titta på en kapacitetsmåttfördelning per plats för att undvika felaktiga tolkningar.

4. Välj **Plats** för att dela upp måttet efter dimension i det gröna avsnittet.
5. Välj en önskad tidsram från det övre fältet i avsnittet.

    Du kan ställa in en måttavisering så att du vet när något oväntat händer. Du kan till exempel få aviseringar när APIM-instansen har överskridit den förväntade toppkapaciteten i över 20 minuter.

    >[!TIP]
    > Du kan konfigurera aviseringar så att du vet när tjänsten har ont om kapacitet eller använder azure monitor-funktionen för automatisk skalning för att automatiskt lägga till en Azure API Management-enhet. Skalning kan ta cirka 30 minuter, så du bör planera dina regler därefter.  
    > Endast skalning av huvudplatsen är tillåten.

## <a name="use-capacity-for-scaling-decisions"></a>Använda kapacitet för skalningsbeslut

**Kapacitet** är måttet för att fatta beslut om att skala en API Management-instans för att hantera mer belastning. Överväg:

+ Om man tittar på en långsiktig trend och genomsnitt.
+ Ignorera plötsliga toppar som sannolikt inte är relaterade till någon ökning av belastningen (se avsnittet Kapacitetsmåttbeteende för förklaring).
+ Uppgradera eller skala din instans, när **kapacitetens**värde överstiger 60% eller 70% under en längre tidsperiod (till exempel 30 minuter). Olika värden kan fungera bättre för din tjänst eller ditt scenario.

>[!TIP]  
> Om du kan uppskatta trafiken i förväg testar du APIM-instansen på arbetsbelastningar som du förväntar dig. Du kan öka belastningen för begäran på din klient gradvis och övervaka vilket värde av kapacitetsmåttet motsvarar din toppbelastning. Följ stegen från föregående avsnitt för att använda Azure-portalen för att förstå hur mycket kapacitet som används vid en given tidpunkt.

## <a name="next-steps"></a>Nästa steg

[Skala eller uppgradera en Azure API Management-tjänstinstans](upgrade-and-scale.md)