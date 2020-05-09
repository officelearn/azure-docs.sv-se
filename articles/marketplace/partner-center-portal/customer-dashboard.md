---
title: Kund instrument panel i Microsofts marknads plats analys på Partner Center
description: Lär dig hur du får åtkomst till information om dina kunder, inklusive tillväxt trender, med hjälp av kund instrument panelen i den kommersiella Marketplace-analysen.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 4d9dc0315a5c87ed67390e8edb23d44ee55f242b
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/03/2020
ms.locfileid: "82732775"
---
# <a name="customer-dashboard-in-commercial-marketplace-analytics"></a>Kund instrument panel på kommersiell Marketplace-analys

Den här artikeln innehåller information om **kund instrument panelen** i Partner Center. Den här instrument panelen visar information om dina kunder, inklusive tillväxt trender, som presenteras i ett grafiskt och nedladdnings Bart format.

Öppna instrument panelen för kunder genom att öppna **[instrument panelen](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** för att få åtkomst till **instrument**panelen för kommersiella marknads platser.

>[!NOTE]
> Detaljerade definitioner av analys terminologi finns i [vanliga frågor och terminologi för affärs platsers analys](./faq-terminology.md).

## <a name="customer-dashboard"></a>Instrumentpanelen Kund

På **instrument panelen** för kunder i **analys** menyn visas data för kunder som har köpt dina erbjudanden. Du kan visa grafiska representationer av följande objekt:

- [Kund Sammanfattning](#customer-summary)
- [Kund efter geografi](#customer-by-geography)
- [Kund trender](#customer-trends)
- [Kunder per order och användning](#customers-by-orders-and-usage)
- [Kunder per SKU](#customers-by-skus)
- [Beställningar och användning efter kundtyp](#orders-and-usage-by-customer-type)
- [Tabellen Kund information](#customer-details-table)
- [Kund sid filter](#customer-page-filters)

### <a name="customer-summary"></a>Kund Sammanfattning

Avsnittet kund Sammanfattning visar antalet kunder, inklusive nya, befintliga och omsättnings bara under det valda datum intervallet.

- Totalt antal kunder definieras som antalet kunder som har köpt ditt erbjudande och har minst en order som inte har annullerats.
- Kundernas procentuella tillväxt jämfört med föregående månad anges av talet och uppåt i den gröna eller nedåtriktade indikatorn i rött.
- Tillväxt trender representeras av Stolp diagram och visar värdet för varje månad genom att hovra över kolumnerna i diagrammet.

Det finns tre **kund typer**: nya, befintliga och uppdelade.

- En ny kund har skaffat ett eller flera av dina erbjudanden för första gången inom den valda månaden.
- En befintlig kund har köpt ett eller flera av dina erbjudanden före den månad som valts.
- En upprättad kund har avbrutit alla erbjudanden som tidigare har köpts.

### <a name="customer-by-geography"></a>Kund efter geografi

Diagrammet **kund per geografi** visar antalet kunder och kunder som har köpts under det valda datum intervallet och som mappas baserat på kundens land. Ljuset till mörkt färg på kartan representerar det låga värdet för kund antalet. Klicka på en post i tabellen om du vill zooma in i ett land.

Termisk karta visar kund antalet och% per kund land. Du kan flytta kartan för att visa den exakta platsen och zooma in på en bestämd plats. Den här kartan har ett kompletterande rutnät som gör att du kan visa% av kunderna efter plats, samt kunder som nyligen har lagts till på den platsen.

### <a name="customer-trends"></a>Kund trender

I tabellen med **kund trender** visas antalet kunder, inklusive nya, befintliga och uppkopplade, med en tillväxt trend per månad.

- Linje diagrammet representerar de övergripande procent andelen av kundens tillväxt.
- Kolumnen månad representerar antalet kunder som är staplade efter nya, befintliga och uppdelade kunder.
- Antalet uppräknade kunder visas i Y-axelns negativa riktning.
- Du kan välja särskilda förklarings objekt för att visa mer detaljerade vyer. Välj till exempel nya kunder från förklaringen för att endast visa nya kunder.
- Du kan använda skjutreglaget överst i diagrammet för att rulla åt höger och vänster på x-axeln och fokusera på vissa data punkter för att visa mer information.
- Om du hovrar över en kolumn i diagrammet visas endast information för den månaden.

### <a name="customers-by-orders-and-usage"></a>Kunder per order och användning

Diagrammet **kunder per order/användning** har tre flikar, "order", "normaliserad användning" och "rå användning". Den **främsta kund percentilen** visas längs x-axeln, vilket bestäms av antalet beställningar. Y-axeln visar kundens order antal. Den sekundära y-axeln (linje diagram) visar den ackumulerade procent andelen av det totala antalet beställningar. Du kan visa information genom att hovra över punkter längs linje diagrammet.

Som exempel kan du se diagrammet nedan för normaliserad användning: den översta 30: e percentilen av kunder bidrar till 87% av den normaliserade användningen ackumulerat. Den 30: e percentilen av kunder bidrar bara till 1 570 000 timmars användning.

### <a name="customers-by-skus"></a>Kunder per SKU

Diagrammen **kunder efter SKU/användning** beskrivs nedan.

1. Brädet presenterar information om de främsta 50 kunderna rangordnas efter antal beställningar. När du har valt en kund visas information om kunden i avsnitten 2, 3 och 4 i den här ledaren.
2. Kund profil informationen visas i det här utrymmet när utgivare är inloggad med en ägar roll. Om utgivare är inloggade med en deltagar roll kommer informationen i det här avsnittet inte att vara tillgänglig.
3. I diagrammet beställningar per SKU ring visas en uppdelning av beställningar som har köpts för SKU: er. De 5 främsta SKU: erna med det högsta antalet order visas, medan resten av orderna grupperas under rest all.
4. Diagrammet platser per SKU ring visar en uppdelning av platser som har beställts för SKU: er. De 5 främsta SKU: erna med de högsta platserna visas, medan resten av orderna grupperas under rest all.

### <a name="orders-and-usage-by-customer-type"></a>Beställningar och användning efter kundtyp

I diagrammet **order/användning per kund typ** visas antalet order, normaliserad användning och rå data timmar som delas mellan nya kunder och befintliga kunder. baserat på valet av order, normaliserad och rå användning i diagrammet.

- En ny kund har köpt en eller flera av dina erbjudanden eller rapporterad användning för första gången inom samma kalender månad (y-axeln).
- En befintlig kund har tidigare förvärvat ett erbjudande från dig eller rapporterat användning före den kalender månad som rapporter ATS (på y-axeln).

### <a name="customer-details-table"></a>Tabellen Kund information

Tabellen **kund information** visar en numrerad lista över de 1000 främsta kunderna sorterat efter det datum då de först köpte ett av dina erbjudanden.

- Kundens personliga information kommer bara att vara tillgänglig om kunden har gett sitt medgivande. Du kan bara se den här informationen om du har loggat in med behörighets nivån ägare av rollen. Läs mer om användar roller och behörigheter.
- Varje kolumn i rutnätet är sorterbar.
- Data kan extraheras till en TSV-fil om antalet poster är mindre än 1000.
- Om poster är över 1000 kommer exporterade data att placeras asynkront på en nedladdnings sida under de närmaste 30 dagarna.
- Filter kan tillämpas på tabellen för att visa endast de data som du är intresse rad av. Data kan filtreras efter företags namn, kund-ID, marknads plats prenumerations-ID, typ av Azure-licens, datum för inhämtat, datum förlorat, kund-e-post, kund land/delstat/ort/post, kund språk osv.

### <a name="customer-page-filters"></a>Kund sid filter

**Sidan kunders** filter tillämpas på sidan kunders sid nivå. Du kan välja flera filter för att återge diagrammet för de kriterier du väljer att visa och de data som du vill se i "detaljerade order data"-rutnät/-export. Filter tillämpas på de data som extraheras för det data intervall som du har valt i det övre högra hörnet på sidan beställningar.

>[!NOTE]
> Detaljerade definitioner för var och en av fälten i kund rutnät, sid filter och deras möjliga val finns i [vanliga frågor och terminologi för affärs marknads analyser](./faq-terminology.md).

## <a name="next-steps"></a>Nästa steg

- En översikt över analys rapporter som är tillgängliga i partner Centers kommersiella marknads platser finns i [analys för den kommersiella Marketplace i Partner Center](./analytics.md).
- För grafer, trender och värden för sammanställda data som sammanfattar Marketplace-aktivitet för ditt erbjudande, se [översikts instrument panel i](./summary-dashboard.md)marknads plats analys.
- Information om dina beställningar i ett grafiskt och nedladdnings Bart format finns i [order instrument panel i kommersiell Marketplace-analys](./orders-dashboard.md).
- För virtuell dator (VM) erbjuder vi användnings-och mätnings mått i [användnings instrument panelen i den kommersiella Marketplace-analysen](./usage-dashboard.md).
- En lista över dina nedladdnings begär Anden under de senaste 30 dagarna finns i [Hämta instrument panel i kommersiell Marketplace-analys](./downloads-dashboard.md).
- Om du vill se en sammanställd vy över kundfeedback för erbjudanden på Azure Marketplace och AppSource, se [klassificering och granskning på instrument panelen i kommersiell Marketplace-analys](./ratings-reviews.md).
- Vanliga frågor och svar om den kommersiella Marketplace-analysen och en omfattande ord lista med data termer finns i [vanliga frågor och termer för att få en kommersiell Marketplace-analys](./faq-terminology.md).
