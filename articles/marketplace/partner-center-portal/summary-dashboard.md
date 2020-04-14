---
title: Instrumentpanel för Partner Center-analys på kommersiell marknadsplats
description: Lär dig hur du kommer åt diagram, trender och värden för aggregerade data som sammanfattar marketplace-aktivitet från instrumentpanelen Sammanfattning i Partnercenter.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 86bce85c6ec273b4ab5f9f00cbae68fc054f53f5
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262408"
---
# <a name="summary-dashboard-in-commercial-marketplace-analytics"></a>Sammanfattningsinstrumentpanel i kommersiell marknadsplatsanalys

Den här artikeln innehåller information om instrumentpanelen Sammanfattning i Partnercenter. Den här instrumentpanelen visar diagram, trender och värden för aggregerade data som sammanfattar marketplace-aktivitet för dina erbjudanden.

Öppna **[instrumentpanelen Analysera](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** under Kommersiell marknadsplats för att komma åt instrumentpanelen Sammanfattning.

>[!NOTE]
> Detaljerade definitioner av analysterminologi finns i [Vanliga frågor och terminologi för kommersiell marknadsplatsanalys](./faq-terminology.md).

## <a name="summary-dashboard"></a>Instrumentpanel för sammanfattningar

**Instrumentpanelen Sammanfattning** visar en översikt baserad på varje erbjudandetyp. **Insikter** visar viktig information på ett ögonblick och ger en bred bild av försäljningsaktiviteten för dina erbjudanden. Du kan visualisera dessa rapporter med hjälp av **instrumentpanelen Sammanfattning.** Den här artikeln innehåller mer information om vart och ett av följande element:

- [Datumintervall](#date-range)
- [Avsnitt sammanfattning](#summary-section)
- [Kunder efter geografi](#customers-by-geography)
- [Trenddiagram för tillväxt](#growth-trend-charts)
- [Kundens topplista](#customer-leaderboard)
- [Trend för antal platser](#seat-count-trend)
- [Gratis prövningar SaaS order trend](#free-trials-saas-orders-trend)

## <a name="elements-of-the-summary-dashboard"></a>Element i instrumentpanelen sammanfattning

I följande avsnitt beskrivs hur du använder den sammanfattande instrumentpanelen och hur du läser data.

### <a name="date-range"></a>Datumintervall

Du hittar ett datumintervallval längst upp till höger på varje sida. Anpassa utdata för **diagrammen sammanfattningssida** genom att välja ett datumintervall baserat på de senaste 3, 6 eller 12 månaderna, eller genom att välja ett anpassat datumintervall med en maximal varaktighet på 12 månader. Standarddatumintervallet är sex månader.

![Instrumentpanel för analys av partnercenter](./media/analyze-dashboard.png)

### <a name="summary-section"></a>Avsnitt sammanfattning

I avsnittet Sammanfattning visas antalet alla skapade order, kunder som har förvärvats och användning som rapporterats under det valda datumintervallet. Partiell aktuell månad kommer att uteslutas från beräkningen av dessa mått. Till exempel: Om du har valt 6M-tidsramen beräknas användningstimmarna för de sex månaderna före den aktuella månaden. Om ett anpassat datumintervall väljs utesluts ett delbelopp från den aktuella månaden från beräkningen.

![Tillväxttrender i instrumentpanelen Sammanfattning](./media/summary-summary-section.png)

#### <a name="reading-the-summary-section"></a>Läsa sammanfattningsavsnittet

- **Beställningar**: Antal alla köpta order (exkluderar annullerade order) för erbjudanden som du har publicerat hittills.
- **Kunder**: Antal kunder som har köpt dina erbjudanden och har minst en ej annullerad beställning.
- **Normaliserade användningstimmar:** Definieras som de normaliserade användningstimmarna för att ta hänsyn till antalet VM-kärnor ([antal VM-kärnor] x [timmar av råanvändning]). Virtuella datorer som betecknas som "SHAREDCORE" använder 1/6 (eller 0,1666) som multiplikator [antal VM-kärnor].
- **Råanvändningstimmar:** Den tid som virtuella datorer har körts i timmar. Procentvärdet bredvid **totala order**, totala **kunder**, **normaliserade användningstimmar,** **råanvändningstimmar,** **sidbesök**och **anrop till åtgärder** representerar mängden användningstillväxt för det valda datumintervallet ([förra månadens användning – användning av första månaden])/ första månadens användning). Som beskrivits ovan kommer en delmängd av den aktuella månaden att uteslutas från det här måttet.
- **Tillväxttrender**: Om du hovrar över diagrammets kolumner visar stapeldiagram värdet för varje månad.
- **Grön triangel pekar uppåt**: Indikerar en positiv tillväxttrend.
- **Röd triangel som pekar nedåt**: Indikerar en negativ tillväxttrend i förhållande till föregående månad.

### <a name="customers-by-geography"></a>Kunder efter geografi

**Den geografiska** heatmap visar en kund räkna på en världskarta.

![Kunder efter geografi i instrumentpanelen Sammanfattning](./media/summary-customers-by-geography.png)

- Du kan flytta kartan för att visa den exakta platsen.
- Du kan zooma in på en viss plats.
- Heatmap har ett tilläggsrutnät för att visa information om antalet kunder, orderantal, normaliserade användningstimmar på den specifika platsen.
- Du kan söka och välja ett land i rutnätet för att zooma till platsen på kartan. Återgå till den ursprungliga vyn genom att trycka på **hemknappen** på kartan.
- En **ny** kund har köpt ett av dina erbjudanden för första gången under månaden inom det valda datumintervallet.

### <a name="growth-trend-charts"></a>Trenddiagram för tillväxt

Du kan visa trender baserat på tillväxten av dina **köpta order** (inkluderar annullerade order), **förvärvade kunder** (inkluderar förlorade kunder) och **rapporterade användningar** som visas månad för månad enligt det valda datumintervallet. Du kan analysera dessa trender ytterligare genom att välja länkar under diagrammet, som navigerar till respektive **order,** **användning,** **kund**eller **Marketplace Insights-sidor.**

Marketplace erbjuder **sidbesök och** trenddiagram för call to action visas för Azure marketplace och AppSource på två flikar.

![Sidbesök och uppmaning till åtgärder trenddiagram i sammanfattningsinstrumentpanelen](./media/summary-page-visits-and-cta.png)

Order **från erbjudandediagram** organiserar dina beställningar enligt erbjudandets namn.

**Cirkeldiagrammet Order efter försäljningskanal** ordnar dina order (inklusive order som kunderna har annullerat) under det valda datumintervallet, efter försäljningskanal. Försäljningskanal är den typ av licensavtal som används av kunder för att köpa Azure, som är Cloud Solution Provider (CSP), Enterprise, Enterprise via Återförsäljare, GTM och Pay As You Go.

**Användning efter erbjudanden** och **användning efter försäljningskanal** cirkeldiagram presentera fördelningen av användningen efter topp erbjudanden och försäljningskanaler, respektive. Det inre cirkeldiagrammet representerar rå användning och det yttre cirkeldiagrammet representerar normaliserad användning.

**Order efter marknadsplatslicenstyp** och **användning efter marknadsplatslicenstyp** cirkeldiagram visar en uppdelning av order och användning efter deras respektive licenstyp. Licenstyper inkluderar:

- **Faktureras via Azure:** Microsoft fakturerar kunder åt dig när du väljer att sälja ditt erbjudande via Microsoft med den här licenstypen. Betalningstyperna inkluderar betalning per dur via kreditkort eller företagsfakturering.
- **Ta med din egen licens:** Microsoft fakturerar inte kunder för deras användning med den här typen av marketplace-erbjudande. Den här användningen visas som **Get it now (Free)** på marknaden.
- **Gratis:** Microsoft fakturerar inte kunder för deras användning med den här typen av marketplace-erbjudande. Den här användningen visas som **kostnadsfri utvärderingsversion** på marknadsplatsen.
- **Microsoft som återförsäljare**: Representerar erbjudanden som säljs av Microsoft-återförsäljare som en del av **CSP-programmet (Cloud Solution Provider).**

### <a name="customer-leaderboard"></a>Kundens topplista

De 50 bästa kunderna med det högsta antalet order visas på en *leadertavla*, sorterad efter högsta orderantal och orderprocent.

- Välj en kund om du vill visa deras profilinformation, order ordnade efter erbjudande eller order ordnade efter Azure-licenstyp och priskanal.
- **Den Erbjudanden av order** donut diagram presenterar de fyra erbjudanden (efter beställning räknas) och de återstående erbjudanden grupperade som "Vila alla".
- Den **normaliserade användningen av erbjudandet** donut diagram presenterar de fem erbjudanden efter användning.

> [!NOTE]
> Kundens personliga information kommer endast att presenteras om kunden har gett sitt samtycke. Du kan visa den här informationen om du har loggat in med behörighetsnivån **Ägare.** Användare med rollen **Deltagare** kan inte visa den här informationen. [Läs mer om användarroller och behörigheter](./manage-account.md#define-user-roles-and-permissions).

### <a name="seat-count-trend"></a>Trend för antal platser

**Order per plats/ per platsdiagram** visar fördelningen av alla beställningar som köpts enligt prismodell. **Trenddiagrammet för platsantal** visar platser kontra beställningar som köpts för alla dina erbjudanden per säte(SaaS).

### <a name="free-trials-saas-orders-trend"></a>Gratis prövningar SaaS order trend

Den **kostnadsfria provperioden SaaS order trenddiagram** presenterar trenden av order för gratis prövningar SaaS erbjuder med en 30-dagars provperiod.

## <a name="next-steps"></a>Nästa steg

- En översikt över analysrapporter som är tillgängliga på den kommersiella marknaden för Partner Center finns i [Analytics för den kommersiella marknadsplatsen i Partner Center](./analytics.md).
- Information om dina beställningar i ett grafiskt och nedladdningsbart format finns [i Order dashboard i kommersiell marknadsplatsanalys](./orders-dashboard.md).
- För virtuell dator (VM) erbjuder användnings- och datapriser för faktureringsmått, se [Användningsinstrumentpanel i kommersiell marknadsplatsanalys](./usage-dashboard.md).
- Detaljerad information om dina kunder, inklusive tillväxttrender, finns [i Kundinstrumentpanelen i kommersiell marknadsplatsanalys](./customer-dashboard.md).
- En lista över dina hämtningsbegäranden under de senaste 30 dagarna finns [i Hämta instrumentpanel i kommersiell marknadsplatsanalys](./downloads-dashboard.md).
- Information om hur du ser en konsoliderad vy över kundfeedback för erbjudanden på Azure Marketplace och AppSource finns [i instrumentpanelen Klassificeringar och recensioner i kommersiell marketplace-analys](./ratings-reviews.md).
- Vanliga frågor och svar om kommersiell marknadsplatsanalys och en omfattande ordlista med datatermer finns i [Vanliga frågor och terminologi för kommersiell marknadsplatsanalys](./faq-terminology.md).
