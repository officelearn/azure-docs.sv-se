---
title: Sammanfattnings instrument panel för partner Center Analytics på kommersiell marknads plats
description: Lär dig hur du kommer åt diagram, trender och värden för sammanställda data som sammanfattar Marketplace-aktivitet från sammanfattnings instrument panelen i Partner Center.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/11/2019
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: 8e58de7975bc7f5de1fe2ad71f97c02c901c0c21
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87304130"
---
# <a name="summary-dashboard-in-commercial-marketplace-analytics"></a>Instrumentpanelen för sammanfattning i Commercial Marketplace-analys

Den här artikeln innehåller information om sammanfattnings instrument panelen i Partner Center. Den här instrument panelen visar diagram, trender och värden för sammanställda data som sammanfattar Marketplace-aktivitet för dina erbjudanden.

Öppna sammanfattnings instrument panelen genom att öppna **[analys instrument panelen](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** under kommersiell marknads plats.

>[!NOTE]
> Detaljerade definitioner av analys terminologi finns i [vanliga frågor och terminologi för affärs platsers analys](./faq-terminology.md).

## <a name="summary-dashboard"></a>Instrumentpanel för sammanfattningar

På instrument panelen **Sammanfattning** presenteras en översikt baserad på varje erbjudande typ. **Insikter** visar viktig information snabbt och ger en bred överblick över Sälj aktiviteten för dina erbjudanden. Du kan visualisera dessa rapporter med **sammanfattnings** instrument panelen. Den här artikeln går till mer information om vart och ett av följande element:

- [Datum intervall](#date-range)
- [Sammanfattnings avsnitt](#summary-section)
- [Kunder efter geografi](#customers-by-geography)
- [Diagram över tillväxt trend](#growth-trend-charts)
- [Kund ranknings lista](#customer-leaderboard)
- [Trend för antal platser](#seat-count-trend)
- [Kostnads fria utvärderingar SaaS order trend](#free-trials-saas-orders-trend)

## <a name="elements-of-the-summary-dashboard"></a>Element i sammanfattnings instrument panelen

I följande avsnitt beskrivs hur du använder sammanfattnings instrument panelen och hur du läser data.

### <a name="date-range"></a>Datumintervall

Du kan hitta ett datum intervall i det övre högra hörnet på varje sida. Anpassa utdata för **sammanfattnings** sidans diagram genom att välja ett datum intervall baserat på de senaste 3, 6 eller 12 månaderna, eller genom att välja ett anpassat datum intervall med en maximal varaktighet på 12 månader. Standard datum intervallet är sex månader.

![Partner Center analys instrument panel](./media/analyze-dashboard.png)

### <a name="summary-section"></a>Sammanfattnings avsnitt

Avsnittet Sammanfattning visar antalet order som skapats, kunder som har köpts och användning rapporter ATS under det valda datum intervallet. Delvis aktuell månad kommer att uteslutas från beräkningen av dessa mått. Exempel: om du har valt tidsintervallet 6 M beräknas förbruknings timmarna för de sex månaderna före den aktuella månaden. Om ett anpassat datum intervall väljs kommer en del av beloppet från den aktuella månaden att undantas från beräkningen.

![Tillväxt trender i sammanfattnings instrument panelen](./media/summary-summary-section.png)

#### <a name="reading-the-summary-section"></a>Läser sammanfattnings avsnittet

- **Order**: antal inköpta order (exklusive annullerade order) för erbjudanden som du har publicerat hittills.
- **Kunder**: antal kunder som köpte dina erbjudanden och har minst en icke annullerad order.
- **Normaliserad användnings tid**: definieras som användnings timmar normaliserade för antalet VM-kärnor ([antal VM-kärnor] x [timmar RAW Usage]). Virtuella datorer som anges som "SHAREDCORE" använder 1/6 (eller 0,1666) som [antal VM-kärnor] multiplikatorn.
- **Rå data timmar**: antalet virtuella datorer som har körts i timmar. Procentvärdet bredvid **Totalt antal beställningar**, **Totalt antal kunder**, **normaliserad användnings tid**, **rå data timmar**, **sid besök**och **anrop till åtgärder** representerar mängden användnings tillväxt för det valda datum intervallet ([senaste månads användning – första månads användning])/första månads användning). Som det beskrivs ovan kommer en del av den aktuella månaden uteslutas från detta mått.
- **Tillväxt trender**: om du hovrar över kolumnerna i diagrammet visas värdet för varje månad i Stolp diagram.
- **Grön triangel som pekar uppåt**: anger en positiv tillväxt trend.
- **Röd triangel som pekar nedåt**: anger en negativ tillväxt trend i förhållande till föregående månad.

### <a name="customers-by-geography"></a>Kunder efter geografi

Termisk karta **kunder per geografi** visar ett kund antal på en världs karta.

![Kunder efter geografi i sammanfattnings instrument panel](./media/summary-customers-by-geography.png)

- Du kan flytta kartan för att visa den exakta platsen.
- Du kan zooma in på en bestämd plats.
- Termisk karta har ett kompletterande rutnät för att visa information om antalet kunder, antalet order, normaliserad användnings tid på den specifika platsen.
- Du kan söka efter och välja ett land/en region i rutnätet för att zooma till platsen i kartan. Återgå till den ursprungliga vyn genom att trycka på knappen **Start** på kartan.
- En **ny** kund har köpt ett av dina erbjudanden för första gången under månaden inom det valda datum intervallet.

### <a name="growth-trend-charts"></a>Diagram över tillväxt trend

Du kan visa trender baserat på tillväxten av dina **inköps order** (inklusive annullerade order), **Kunder som har köpts** (inklusive förlorade kunder) och **användning** som rapporteras, som visas månad efter månad enligt det valda datum intervallet. Du kan analysera dessa trender ytterligare genom att välja länkar under diagrammet, som navigerar till respektive **order**, **användning**, **kund**eller **Marketplace Insights** -sidor.

Besöks sidan för Marketplace-erbjudanden **och samtal till åtgärds** trend diagram visas för Azure Marketplace och AppSource på två flikar.

![Sid besök och anrop till åtgärder trend diagram i sammanfattnings instrument panel](./media/summary-page-visits-and-cta.png)

Diagrammet **order by-erbjudanden** ordnar dina beställningar enligt namnet på erbjudandet.

Cirkel diagrammet **order efter försäljnings kanal** ordnar dina beställningar (inklusive order som avbrutits av kunderna) under det valda datum intervallet, efter försäljnings kanal. Försäljnings kanal är den typ av licens avtal som kunder använder för att köpa Azure, som är CSP (Cloud Solution Provider), Enterprise, Enterprise genom åter försäljare, GTM och betala per användning.

**Användning per erbjudande** och **användning efter försäljnings kanal** cirkel diagram visar analys av användning per främsta erbjudanden respektive försäljnings kanaler. Det inre cirkel diagrammet representerar rå användning och yttre cirkel diagram visar normaliserad användning.

I cirkel diagram av typen **beställningar efter Marketplace licens typ** och **användning av Marketplace** visas en uppdelning av beställningar och användning av respektive licens typ. Licens typer är:

- **Faktureras via Azure: Microsoft fakturerar**kunder åt dig när du väljer att sälja ditt erbjudande via Microsoft med den här licens typen. Betalnings typerna omfattar betala per användning via kredit kort eller företags fakturering.
- **Bring Your Own License**: Microsoft debiterar inte kunder för användning med den här typen av Marketplace-erbjudande. Den här användningen anges som **Hämta nu (kostnads fri)** i Marketplace.
- **Kostnads fritt**: Microsoft debiterar inte kunder för användning med den här typen av Marketplace-erbjudande. Den här användningen visas som en **kostnads fri utvärderings version** i Marketplace.
- **Microsoft as åter försäljare**: representerar erbjudanden som säljs av Microsoft-återförsäljare som en del av **CSP-programmet (Cloud Solution Provider)**.

### <a name="customer-leaderboard"></a>Kund ranknings lista

De 50 främsta kunderna med högst antal beställningar visas på en *ledare*, sorterat efter högsta antal beställningar och order procent.

- Välj en kund om du vill visa profil information, beställningar som organiserats efter erbjudande eller beställningar som organiseras av Azures licens typ och prissättnings kanal.
- Ring diagrammet för **erbjudanden per order** innehåller de fyra främsta erbjudandena (per antal order) och återstående erbjudanden grupperade i som "rest all".
- Det **normaliserade användnings diagrammet per erbjudande** ring visar de fem främsta erbjudandena efter användning.

> [!NOTE]
> Kundens personliga information visas bara om kunden har gett sitt medgivande. Du kan visa den här informationen om du har loggat in med behörighets nivån för **ägar** rollen. Användare med rollen **deltagare** kommer inte att kunna visa den här informationen. [Läs mer om användar roller och behörigheter](./manage-account.md#define-user-roles-and-permissions).

### <a name="seat-count-trend"></a>Trend för antal platser

Diagrammet **order by per klient/per plats** visar fördelningen av alla beställningar som köpts enligt pris sättnings modellen. **Trend diagrammet antal platser** visar platser och beställningar som har köpts för alla erbjudanden per klient program vara som en tjänst (SaaS).

### <a name="free-trials-saas-orders-trend"></a>Kostnads fria utvärderingar SaaS order trend

I **trend diagrammet för kostnads fria utvärderings versioner SaaS order** visas trenden för beställningar för erbjudanden om kostnads fria utvärderings versioner med en 30-dagars utvärderings period.

## <a name="next-steps"></a>Nästa steg

- En översikt över analys rapporter som är tillgängliga i partner Centers kommersiella marknads platser finns i [analys för den kommersiella Marketplace i Partner Center](./analytics.md).
- Information om dina beställningar i ett grafiskt och nedladdnings Bart format finns i [order instrument panel i kommersiell Marketplace-analys](./orders-dashboard.md).
- För virtuell dator (VM) erbjuder vi användnings-och mätnings mått i [användnings instrument panelen i den kommersiella Marketplace-analysen](./usage-dashboard.md).
- Detaljerad information om dina kunder, inklusive tillväxt trender, finns [på kund instrument panel i affärs Marketplace-analys](./customer-dashboard.md).
- En lista över dina nedladdnings begär Anden under de senaste 30 dagarna finns i [Hämta instrument panel i kommersiell Marketplace-analys](./downloads-dashboard.md).
- Om du vill se en sammanställd vy över kundfeedback för erbjudanden på Azure Marketplace och AppSource, se [klassificering och granskning på instrument panelen i kommersiell Marketplace-analys](./ratings-reviews.md).
- Vanliga frågor och svar om den kommersiella Marketplace-analysen och en omfattande ord lista med data termer finns i [vanliga frågor och termer för att få en kommersiell Marketplace-analys](./faq-terminology.md).
