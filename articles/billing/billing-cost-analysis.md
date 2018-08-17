---
title: Utforska Azure-kostnader med Cost analysis | Microsoft Docs
description: Den här artikeln hjälper dig att använda kostnadsanalys att utforska och analysera din organisations Azure-kostnader.
services: billing
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 08/16/2018
ms.topic: conceptual
ms.service: billing
manager: dougeby
ms.custom: ''
ms.openlocfilehash: eeaf02853f8ffe9ca67dbf31afc687afb7dee242
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2018
ms.locfileid: "40180914"
---
# <a name="explore-and-analyze-costs-with-cost-analysis"></a>Utforska och analysera kostnaderna med kostnadsanalys

Innan du kan korrekt styra och optimera dina kostnader för Azure, måste du förstå där kostnaderna har sitt ursprung i din organisation. Det är också bra att veta hur mycket pengar dina tjänster kostnad, och stöd för vilka miljöer och system. Insyn i hela spektrumet av kostnader är viktigt att läsa och tolka organisationens utgiftsgränsen mönster som kan användas för att genomdriva kostnaden kontrollmekanismer som din budget.

I den här artikeln använder du kostnadsanalys att utforska och analysera dina organisationens kostnader. Du kan visa aggregerade kostnader för av din organisation för att förstå där kostnader påförs och identifiera utgiftstrender. Du visa ackumulerade kostnaderna över tid för att beräkna månadsvis, Kvartalsvis eller årligen även kostnadstrender mot en budget. En budget som hjälper dig att följa finansiella begränsningar eller visa dagliga och månatliga kostnader för att isolera utgiftsgränsen oegentligheter. Och du kan hämta den aktuella rapporten data för ytterligare analys eller använda i ett externt system.

## <a name="requirements"></a>Krav

Kostnadsanalys är tillgängliga för alla kunder med Enterprise Agreement (EA). Du måste ha läsbehörighet till minst en av följande omfattningar för att visa kostnadsdata.

- Azure EA fakturering konto (registrering)
- Azure EA-prenumeration
- Resursgrupp för Azure EA-prenumeration

## <a name="review-costs-in-cost-analysis"></a>Granska kostnader i kostnadsanalys

Om du vill granska dina kostnader med kostnadsanalys öppna Azure-portalen och gå sedan till **kostnadshantering + fakturering** &gt; **fakturering konton** &gt; väljer ditt EA fakturering konto &gt; under kostnadshantering, väljer **analys av kostnader**.

Initial kostnad analysvy innehåller följande områden:

**Totalt antal** – visar den totala kostnaden för den aktuella månaden.

**Budget** – visar planerade utgiftsgränsen för det valda omfånget, om det är tillgängligt.

**Ackumulerade kostnaden** – visar det totala antalet ackumuleras dagliga utgifter, med start i början av månaden. Om du har [skapat en budget](billing-cost-management-budget-scenario.md#create-the-azure-budget) för ditt fakturering eller prenumeration, kan du snabbt se din kostnadstrend i förhållande till budgeten. Hovra över ett datum i ackumulerade kostnaden för den dagen.

**Pivotera (ringdiagram)** – ange dynamiska pivoteringsmöjligheter. De bryter ned den totala kostnaden med en gemensam uppsättning standardegenskaper. Diagrammen visar de mest till minst resurskrävande ackumuleras för den aktuella månaden. Du kan ändra pivot-diagram när som helst genom att välja en annan pivot. Som standard är kostnader uppdelade efter service (mätningskategori), plats (region) och underordnade omfång (till exempel registreringskonton under fakturering konton, resursgrupper under prenumerationerna och resurserna under resursgrupper).

![Inledande vy över kostnadsanalys](./media/billing-cost-analysis/cost-analysis-01.png)



## <a name="customizing-cost-views"></a>Anpassa kostnaden vyer

Standardvyn innehåller snabba svar på vanliga frågor som:

- Hur mycket jag ägnar?
- Följer jag min budgeten?

Men finns det många fall där du behöver djupare analys. Anpassning startar överst på sidan med alternativet datum.

Som standard visar kostnadsanalys data för den aktuella månaden. Använd Väljaren datum för att snabbt växla till den senaste månaden, den här månaden, den här Kalenderkvartal, år eller ett eget datumintervall valfri. Att välja den senaste månaden är det snabbaste sättet att analysera din senaste faktura för Azure och enkelt synkronisera avgifter. Aktuella alternativ för kvartals- och årsrapporter hjälp för att spåra kostnader mot budget mer långsiktiga. Du kan också välja ett mer detaljerade eller bredare datumintervall, från en dag till de senaste sju dagarna eller allt så långt tillbaka som en året före den aktuella månaden.

![Datum-väljare](./media/billing-cost-analysis/date-selector.png)

Även cost analysis visar som standard **ackumulerade** kostnader. Ackumulerade kostnaderna omfattar alla kostnader för varje dag utöver de senaste dagarna, för en ständigt växande vy över dina dagligen, upplupna kostnader. Den här vyn är optimerad för att visa hur du är populära mot en budget för det valda tidsintervallet.

Det finns också i **dagliga** vy. Den visar kostnaderna för varje dag och visar inte en Tillväxttrend. Dagsvyn har optimerats för att visa oegentligheter som kostnader insamling eller sjunker från dag till dag. När du väljer en budget, visar dagsvyn också en uppskattning av hur din dagliga budget kan se ut. Om dina dagliga kostnader är konsekvent över beräknade dagliga budget, och du kan förvänta dig att du ska överskrider din månatliga budget. Den beräknade dagliga budgeten är bara ett sätt att hjälpa dig att visualisera din budget på lägre nivå. När du har grund av variationer i dagliga kostnader är uppskattade dagliga budget jämförelsen så att din månatliga budget mindre exakta.

![Dagliga vy](./media/billing-cost-analysis/daily-view.png)

Du kan **Gruppera efter** att välja en grupp kategori för att ändra data som visas i diagrammet total ytan. Gruppering kan du snabbt se hur dina utgifter kategoriseras efter resurstyp. Här är en vy över Azure-tjänstekostnaderna för månadsvyn sista.

![Grupperade dagliga ackumulerad vy](./media/billing-cost-analysis/grouped-daily-accum-view.png)

Pivot-diagram är under den översta totala vyn. Du kan använda dem för att se vyer för olika gruppering och filtrering kategorier. När du väljer alla kategori, är en fullständig uppsättning data för totalt antal vy längst ned i vyn. Här är ett exempel för resursgrupper.

![Fullständiga data för aktuell vy](./media/billing-cost-analysis/full-data-set.png)

### <a name="download-cost-analysis-data"></a>Ladda ned cost analysis-data

När du **hämta** information från analys av kostnader, genereras en CSV-fil för alla data som visas i Azure-portalen. Om du har använt några filter eller en gruppering, är de ingår i filen. Vissa underliggande data för den översta totala diagrammet som aktivt inte visas i portalen ingår också i CSV-filen.

## <a name="next-steps"></a>Nästa steg

Visa andra [Azure-fakturering och dokumentation om cost management](billing-cost-management-budget-scenario.md).
