---
title: Instrumentpanelen partnercenterorder i analys av kommersiella marknadsplatser
description: Läs om hur du kommer åt analytiska rapporter om dina marketplace-erbjudandeorder i ett grafiskt och nedladdningsbart format.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 148b80a6843168b11e4feb3558f6bf08eaeb3ace
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261269"
---
# <a name="orders-dashboard-in-commercial-marketplace-analytics"></a>Beställer instrumentpanel i kommersiell marknadsplatsanalys

Den här artikeln innehåller information om **instrumentpanelen Order** i Partnercenter. Den här instrumentpanelen visar information om dina beställningar i ett grafiskt och nedladdningsbart format.

Om du vill komma åt **instrumentpanelen Order** i analysverktygen för Partner Center öppnar du **[instrumentpanelen Analysera](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** under Kommersiell marknadsplats.

>[!NOTE]
> Detaljerade definitioner av analysterminologi finns i [Vanliga frågor och terminologi för kommersiell marknadsplatsanalys](./faq-terminology.md).

## <a name="orders-dashboard"></a>Orderinstrumentpanel

**Instrumentpanelen Order** på **menyn Analysera** visar de aktuella orderna för alla dina SaaS-erbjudanden. Du kan visa grafiska representationer av följande objekt:

- [Sammanfattning av order](#order-summary)
- [Beställningar efter geografi](#orders-by-geography)
- [Beställningar av erbjudanden](#orders-by-offers)
- [Ordertrend per plats jämfört med säte](#orders-trend-per-site-versus-per-seat)
- [Beställningar av SKU:er](#orders-by-skus)
- [Order och platser trend](#orders-and-seats-trend)
- [Tabell för orderinformation](#order-details-table)

> [!NOTE]
> Det finns skillnader mellan hur analysrapporter visas i Cloud Partner Portal (CPP) och det nya kommersiella marknadsplatsprogrammet i Partner Center. Ett specifikt sätt är att **säljaren Insights** i CPP har en **order & användning** fliken, som visar data för användningsbaserade erbjudanden och icke-användningsbaserade erbjudanden. I Partnercenter har sidan **Order** en separat flik för SaaS-erbjudanden.

## <a name="order-dashboard-details"></a>Information om beställning av instrumentpanel

I det här avsnittet beskrivs analysrapporterna mer i detalj.

### <a name="order-summary"></a>Sammanfattning av order

I avsnittet Ordersammanfattning visas antalet inköpta order (exklusive annullerade order), annullerade order och platser.

Procentvärdet bredvid Totala order representerar mängden tillväxt som det valda datumintervallet.

![Sammanfattning av ordersammanfattning i partnercenter](./media/order-summary.png)

- En grön triangel som pekar uppåt indikerar en positiv tillväxttrend.
- En röd triangel som pekar nedåt indikerar en negativ tillväxttrend jämfört med föregående månad.
- Tillväxttrender representeras av mikrofältsdiagram. Du kan visa värdet för varje månad genom att hovra över kolumnerna i diagrammet.
- Annullerade order är ett antal order som tidigare har köpts och sedan annullerats under det valda datumintervallet.
- Platser är antalet platser som skapats under det valda datumintervallet.

### <a name="orders-by-geography"></a>Beställningar efter geografi

**Heatmap Order efter geografi** visar antalet beställningar på en världskarta och visar platser som kartlagts baserat på Kundland. Denna heatmap fungerar på samma sätt som **[kunden efter geografi heatmap](./customer-dashboard.md#customer-by-geography)**.

![Partnercenter Analysera order efter geografi](./media/orders-by-geography.png)

### <a name="orders-by-offers"></a>Beställningar av erbjudanden

**Order av erbjuder** donut diagram organiserar order (inklusive annullerade beställningar) enligt deras erbjudande namn.

- De översta erbjudandena visas i diagrammet och resten av erbjudandena är grupperade som "Vila alla".
- Du kan välja specifika erbjudanden i förklaringen om du bara vill visa erbjudanden i diagrammet.
- Om du hovrar över ett segment i diagrammet visas antalet order och procentandelen av erbjudandet jämfört med det totala antalet beställningar i alla erbjudanden.
- **Orderingången efter erbjudandetrend** visar tillväxttrender månad för månad. Kolumnen Månad representerar antalet order efter erbjudandenamn. Linjediagrammet visar tillväxtprocenttrenden som ritats på en z-axel.
- Du kan använda skjutreglaget högst upp i diagrammet för att rulla åt höger och vänster längs x-axeln och fokusera på specifika datapunkter.
- Du kan visa trenddiagrammet genom att välja ett visst objekt i förklaringen.
- Du kan också välja att visa trender och data för **annullerade order**. Diagrammet fungerar på samma sätt som **order av erbjudanden** diagram.

### <a name="orders-trend-per-site-versus-per-seat"></a>Order Trend per plats kontra per plats

Diagrammet **per plats jämfört med sätesn** representerar fördelningen av SaaS per plats och SaaS-beställningar per plats som köpts av kunder (det här diagrammet innehåller annullerade order). Stapeldiagrammet representerar trenden för SaaS per plats och saaS-order per säte som köpts av kunder (det här diagrammet innehåller annullerade order).

### <a name="orders-by-skus"></a>Beställningar av SKU:er

Diagrammet **Order per SKU-diagram** representerar trenden för order på SKU-nivå (Stock Keeping Unit) för alla dina erbjudanden (detta inkluderar annullerade order). Donutdiagrammet representerar uppdelningen av de fem översta SKU-orderna och stapeldiagrammet representerar orderutvecklingen för de fem största SKU:erna.

### <a name="orders-and-seats-trend"></a>Order och platser Trend

Order- **och platstrenddiagrammet** presenterar de 50 bästa erbjudandena med det högsta antalet beställningar. Dessa visas på en leadertavla och sorteras efter högsta orderantal och orderprocent.

- **Order efter SKU:** Välj ett erbjudande om du vill visa fördelningen av orderantalet för de fem största skusarna i diagrammet.
- **Platser efter SKU:** Den månatliga trenden med platser för de fem största sku:erna. Om erbjudandet du väljer inte är ett erbjudande per plats visas inga data i det här ytdiagrammet.

### <a name="canceled-orders-by-offers"></a>Annullerade beställningar med erbjudanden

Det **annullerade order per erbjudande** cirkeldiagram organiserar alla dina annullerade order enligt deras erbjudandenamn. De översta erbjudandena visas i diagrammet och resten av erbjudandena är grupperade som "Vila alla". Du kan välja specifika erbjudanden i förklaringen som ska visas i diagrammet.

- Om du hovrar över ett segment i diagrammet visas antalet order och procentandelen av det valda erbjudandet jämfört med det totala antalet order i alla erbjudanden.
- Stapeldiagrammet visar trender månad för månad. Kolumnerna representerar antalet annullerade order efter erbjudandenamn. Du kan använda skjutreglaget högst upp i diagrammet för att rulla åt höger och vänster längs x-axeln och fokusera på specifika datapunkter. Du kan visa trenddiagrammet genom att välja ett visst objekt i förklaringen.

### <a name="order-details-table"></a>Tabell för orderinformation

Tabellen Orderinformation visar en numrerad lista över de 1000 översta order sorterade efter förvärvsdatum.

- Varje kolumn i rutnätet är sorterbar.
- Data kan extraheras till en TSV-fil om antalet poster är mindre än 1000.
- Om posternummer över 1 000 placeras exporterade data asynkront på en nedladdningssida under de kommande 30 dagarna.
- Filter kan användas i **tabellen Orderinformation** för att bara visa de data som du är intresserad av. Data kan filtreras efter land, Azure-licenstyp, Marketplace-licenstyp, Erbjudandetyp, Orderstatus, Kostnadsfria spår, Marketplace-prenumerations-ID, Kund-ID och Företagsnamn.

#### <a name="orders-page-filters"></a>Sidfilter för order

Dessa filter används på sidnivå.

Du kan välja flera filter för att återge diagrammet för de villkor du väljer att visa och de data som du vill ska visas i rutnätet **för detaljerad orderdata/export.** Filter används på de data som extraheras för det dataområde som du har valt längst upp till höger på ordersidan.

- Erbjudandetyper och erbjudandenamn listas endast för erbjudanden som du har beställningar för under det valda datumintervallet. Erbjudandenamn i listan visas för erbjudandetyper som du har valt i listan.
- Tillämpade filter visar det totala måttet i varje eller flera markerade markeringar för varje markerat filter. Tillämpade filter visas inte när standardmarkeringen väljs.
- Om **Alla** är markerat för en av listrutorna sammanställs alla mått på den valda sidan. Till exempel: "Alla" i filteralternativet erbjudandetyper innebär att alla erbjudandetyper har valts. Det här är standardvalet för listrutorna. Tillämpade filter visar inte något när **Alla** är markerat.
- **Val av flera värden**: Alla mått på sidan sammanställs för alla val som görs under listrutan. Om flera val görs visar det tillämpade filtret antalet alla val som gjorts. Se bilden nedan som referens.

    ![Partnercenter Analysera ordning med flera värden som tillämpas på filter](./media/filters-applied.png)

- **Markering med ett enda värde**: Om ett värde är markerat visar det tillämpade filtret antalet för det filter som har valts. Se nedan bild för referens.

     ![Partnercenter Analysera ordning med ett enda värde som tillämpas på filter](./media/filters-applied-single.png)

## <a name="next-steps"></a>Nästa steg

- En översikt över analysrapporter som är tillgängliga på den kommersiella marknaden för Partner Center finns i [Analytics för den kommersiella marknadsplatsen i Partner Center](./analytics.md).
- Diagram, trender och värden för aggregerade data som sammanfattar marketplace-aktivitet för ditt erbjudande finns [i Sammanfattningsinstrumentpanel i kommersiell marknadsplatsanalys](./summary-dashboard.md).
- För virtuell dator (VM) erbjuder användnings- och datapriser för faktureringsmått, se [Instrumentpanelen för användning i kommersiell marknadsplatsanalys](./usage-dashboard.md).
- Detaljerad information om dina kunder, inklusive tillväxttrender, finns [i Kundens instrumentpanel i kommersiell marknadsplatsanalys](./customer-dashboard.md).
- En lista över dina hämtningsbegäranden under de senaste 30 dagarna finns [i Instrumentpanelen Hämta i kommersiell marknadsplatsanalys](./downloads-dashboard.md).
- Information om hur du ser en konsoliderad vy över kundfeedback för erbjudanden på Azure Marketplace och AppSource finns [i instrumentpanelen Klassificeringar och recensioner i kommersiell marketplace-analys](./ratings-reviews.md).
- Vanliga frågor och svar om kommersiell marknadsplatsanalys och en omfattande ordlista med datatermer finns i [Vanliga frågor och terminologi för kommersiell marknadsplatsanalys](./faq-terminology.md).
