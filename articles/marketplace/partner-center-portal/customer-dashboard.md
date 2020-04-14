---
title: Kundinstrumentpanel i kommersiell marknadsplatsanalys på Partner Center
description: Läs om hur du får tillgång till information om dina kunder, inklusive tillväxttrender, med hjälp av kundinstrumentpanelen i kommersiell marknadsplatsanalys.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: a8379ed883311d219bb6eeb56bd4424dfb470bc9
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81251647"
---
# <a name="customer-dashboard-in-commercial-marketplace-analytics"></a>Kundinstrumentpanel i kommersiell marknadsplatsanalys

Den här artikeln innehåller information om **kundinstrumentpanelen** i Partnercenter. Den här instrumentpanelen visar information om dina kunder, inklusive tillväxttrender, som presenteras i ett grafiskt och nedladdningsbart format.

Öppna instrumentpanelen **[Analysera](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** under Kommersiell marknadsplats för att komma åt **kundinstrumentpanelen.**

>[!NOTE]
> Detaljerade definitioner av analysterminologi finns i [Vanliga frågor och terminologi för kommersiell marknadsplatsanalys](./faq-terminology.md).

## <a name="customer-dashboard"></a>Instrumentpanelen Kund

**Kundinstrumentpanelen** på menyn **Analysera** visar data för kunder som har köpt dina erbjudanden. Du kan visa grafiska representationer av följande objekt:

- [Kundsammanfattning](#customer-summary)
- [Kund efter geografi](#customer-by-geography)
- [Kundtrender](#customer-trends)
- [Kunder efter order och användning](#customers-by-orders-and-usage)
- [Kunder efter SKU:er](#customers-by-skus)
- [Order och användning efter kundtyp](#orders-and-usage-by-customer-type)
- [Tabell för kundinformation](#customer-details-table)
- [Filter för kundsida](#customer-page-filters)

### <a name="customer-summary"></a>Kundsammanfattning

I avsnittet Kundsammanfattning visas antalet kunder, inklusive nya, befintliga och omtalade, under det valda datumintervallet.

- Totalt antal kunder definieras som antalet kunder som har köpt ditt erbjudande och har minst en order som inte har annullerats.
- Kundernas procentuella tillväxt jämfört med föregående månad indikeras av tal- och uppåtindikatorn i grönt eller nedåtgående resultat i rött.
- Tillväxttrender representeras av stapeldiagram och visar värdet för varje månad genom att hovra över diagrammets kolumner.

Det finns tre **kundtyper:** nya, befintliga och spottade.

- En ny kund har skaffat ett eller flera av dina erbjudanden för första gången inom den valda månaden.
- En befintlig kund har förvärvat ett eller flera av dina erbjudanden före den valda månaden.
- En spottad kund har avbrutit alla erbjudanden som tidigare köpts.

### <a name="customer-by-geography"></a>Kund efter geografi

Diagrammet **Kund efter geografi** visar antalet kunder och kunder som förvärvats under det valda datumintervallet och mappas baserat på kundlandet. Ljus till mörk färg på kartan representerar det låga till höga värdet av antalet kunder. Klicka på en post i tabellen om du vill zooma in på ett land.

Värmekartan visar antalet kunder och % per kundland. Du kan flytta kartan för att visa den exakta platsen och zooma in på en viss plats. Den här kartan har ett kompletterande rutnät som gör att du kan visa % av kunderna efter plats, samt kunder som nyligen lagts till på den platsen.

### <a name="customer-trends"></a>Kundtrender

Diagrammet **Kundtrender** visar antalet kunder, inklusive nya, befintliga och spottade, med en tillväxttrend månad för månad.

- Linjediagrammet representerar de totala kundtillväxtprocenterna.
- Kolumnen Månad representerar antalet kunder staplade av nya, befintliga och spottade kunder.
- Det inströjda kundantalet visas i den negativa riktningen för Y-axeln.
- Du kan välja specifika förklaringsobjekt som ska visas mer detaljerade vyer. Välj till exempel nya kunder från förklaringen om du bara vill visa nya kunder.
- Du kan använda skjutreglaget högst upp i diagrammet för att rulla åt höger och vänster på x-axeln och fokusera på specifika datapunkter för att visa mer i detalj.
- Om du hovrar över en kolumn i diagrammet visas information för bara den månaden.

### <a name="customers-by-orders-and-usage"></a>Kunder efter order och användning

Diagrammet **Kunder efter order/användning** har tre flikar, "order", "normaliserad användning" och "råanvändning". Den **översta kund percentilen** visas längs x-axeln, som bestäms av deras antal order. Y-axeln visar kundens orderantal. Den sekundära y-axeln (linjediagram) visar den ackumulerade procentandelen av det totala antalet order. Du kan visa information genom att hovra över punkter längs linjediagrammet.

Som ett exempel, se diagrammet nedan för normaliserad användning: Den översta 30: e percentilen av kunder bidrar till 87% av den normaliserade användningen kumulativt. Den 30: e percentilen av kunder bidrar bara 1,57 miljoner timmars användning.

### <a name="customers-by-skus"></a>Kunder efter SKU:er

Kunderna **efter SKU/användningsdiagram** beskrivs nedan.

1. Leader-styrelsen presenterar information om de 50 bästa kunderna rangordnade efter orderantal. När du har valt kund presenteras kundens uppgifter i avsnitten 2, 3 och 4 i denna leaderboard.
2. Kundprofilinformationen visas i det här utrymmet när utgivare är inloggade med en ägarroll. Om utgivare är inloggade med en deltagarroll är informationen i det här avsnittet inte tillgänglig.
3. I donutdiagrammet Order efter SKU visars fördelningen av order som köpts för SKU:er. De översta 5 SKU:erna med det högsta orderantalet visas, medan resten av beställningarna grupperas under "vila alla".
4. I tabellen Säten efter SKU-donut visas fördelningen av de platser som beställts för SKU:er. De översta 5 SKU:erna med de högsta platserna visas, medan resten av beställningarna är grupperade under vila alla.

### <a name="orders-and-usage-by-customer-type"></a>Order och användning efter kundtyp

Diagrammet **Order/användning efter kundtyp** visar antalet order, normaliserad användning och råanvändningstimmar som delas mellan nya kunder och befintliga kunder. baserat på val av order, normaliserad respektive råanvändning i diagrammet.

- En ny kund har förvärvat ett eller flera av dina erbjudanden eller rapporterade användning för första gången inom samma kalendermånad (y-axeln).
- En befintlig kund har tidigare förvärvat ett erbjudande från dig eller rapporterat användning före den rapporterade kalendermånaden (på y-axeln).

### <a name="customer-details-table"></a>Tabell för kundinformation

Tabellen **Kundinformation** visar en numrerad lista över de 1000 kunder som sorterats efter det datum då de först förvärvade ett av dina erbjudanden.

- Kundens personliga information kommer endast att vara tillgänglig om kunden har gett sitt samtycke. Du kan bara visa den här informationen om du har loggat in med en ägarrollsnivå med behörigheter. Läs mer om användarroller och behörigheter.
- Varje kolumn i rutnätet är sorterbar.
- Data kan extraheras till en TSV-fil om antalet poster är mindre än 1000.
- Om postnumret är över 1000 placeras exporterade data asynkront på en nedladdningssida under de kommande 30 dagarna.
- Filter kan användas på tabellen för att bara visa de data som du är intresserad av. Data kan filtreras efter företagsnamn, kund-ID, Marketplace-prenumerations-ID, Azure-licenstyp, Inköpt datum, förlorade datum, förlorade kundn-e-post, Kundland/Stat/Stad/Zip, Kundspråk etc.

### <a name="customer-page-filters"></a>Filter för kundsida

Sidfiltren **Kunder** används på kundsidan. Du kan välja flera filter för att återge diagrammet för de kriterier du väljer att visa och de data du vill se i rutnätet "Detaljerad orderdata" /export. Filter används på de data som extraheras för det dataområde som du har valt längst upp till höger på ordersidan.

>[!NOTE]
> Detaljerade definitioner för vart och ett av fälten i kundrutnätet, sidfilter och deras möjliga val finns i [Vanliga frågor och terminologi för kommersiell marketplace-analys](./faq-terminology.md).

## <a name="next-steps"></a>Nästa steg

- En översikt över analysrapporter som är tillgängliga på den kommersiella marknaden för Partner Center finns i [Analytics för den kommersiella marknadsplatsen i Partner Center](./analytics.md).
- Diagram, trender och värden för aggregerade data som sammanfattar marketplace-aktivitet för ditt erbjudande finns [i Sammanfattningsinstrumentpanel i kommersiell marknadsplatsanalys](./summary-dashboard.md).
- Information om dina beställningar i ett grafiskt och nedladdningsbart format finns [i Order dashboard i kommersiell marknadsplatsanalys](./orders-dashboard.md).
- För virtuell dator (VM) erbjuder användnings- och datapriser för faktureringsmått, se [Användningsinstrumentpanel i kommersiell marknadsplatsanalys](./usage-dashboard.md).
- En lista över dina hämtningsbegäranden under de senaste 30 dagarna finns [i Hämta instrumentpanel i kommersiell marknadsplatsanalys](./downloads-dashboard.md).
- Information om hur du ser en konsoliderad vy över kundfeedback för erbjudanden på Azure Marketplace och AppSource finns [i instrumentpanelen Klassificeringar och recensioner i kommersiell marketplace-analys](./ratings-reviews.md).
- Vanliga frågor och svar om kommersiell marknadsplatsanalys och en omfattande ordlista med datatermer finns i [Vanliga frågor och terminologi för kommersiell marknadsplatsanalys](./faq-terminology.md).
