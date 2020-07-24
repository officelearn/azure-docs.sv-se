---
title: Instrument panel för partner Center på den kommersiella Marketplace-analys, Microsoft AppSource och Azure Marketplace
description: Lär dig hur du får åtkomst till analys rapporter om dina kommersiella Marketplace-erbjudanden i ett grafiskt och nedladdnings Bart format.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 07/22/2020
ms.openlocfilehash: 7a704936b35ba5c747ab5884c61f3f5413ac5f07
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2020
ms.locfileid: "87127323"
---
# <a name="orders-dashboard-in-commercial-marketplace-analytics"></a>Analys av orderinstrumentpanelen på kommersiell marknadsplats

Den här artikeln innehåller information om **instrument panelen för order** i Partner Center. Den här instrument panelen visar information om dina beställningar i ett grafiskt och nedladdnings Bart format.

För att komma åt **instrument panelen för order** i Partner Center Analytics-verktyg, öppnar du **[analys instrument panelen](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** under kommersiell marknads plats.

>[!NOTE]
> Detaljerade definitioner av analys terminologi finns i [vanliga frågor och terminologi för affärs platsers analys](./faq-terminology.md).

## <a name="orders-dashboard"></a>Orderinstrumentpanel

På **instrument panelen för order** i menyn **analysera** visas aktuella beställningar för alla dina SaaS-erbjudanden. Du kan visa grafiska representationer av följande objekt:

- [Order Sammanfattning](#order-summary)
- [Beställningar efter geografi](#orders-by-geography)
- [Beställningar per erbjudanden](#orders-by-offers)
- [Order trend per plats kontra per plats](#orders-trend-per-site-versus-per-seat)
- [Order by-planer](#orders-by-plans)
- [Trend för beställningar och platser](#orders-and-seats-trend)
- [Order detalj tabell](#order-details-table)

Den maximala svars tiden mellan skapande och rapportering av beställningar i Partner Center är 48 timmar.

## <a name="order-dashboard-details"></a>Information om order instrument panel

I det här avsnittet beskrivs analys rapporterna i detalj.

### <a name="order-summary"></a>Order Sammanfattning

I avsnittet order Sammanfattning visas antalet inköpta order (exklusive annullerade order), annullerade order och platser.

Procentvärdet bredvid totalt antal beställningar representerar den mängd tillväxt som det valda datum intervallet.

![Översikt över analys av Partner Center](./media/order-summary.png)

- En grön triangel som pekar uppåt anger en positiv tillväxt trend.
- En röd triangel som pekar nedåt visar en negativ tillväxt trend i förhållande till föregående månad.
- Tillväxt trender representeras av Micro bar-diagram. Du kan visa värdet för varje månad genom att hovra över kolumnerna i diagrammet.
- Annullerade beställningar är antalet order som tidigare har köpts och sedan avbrutits under det valda datum intervallet.
- Platser är ett antal platser som skapats under det valda datum intervallet.

### <a name="orders-by-geography"></a>Beställningar efter geografi

Värme kartan för **beställningar efter geografi** visar ett antal beställningar på en världs karta och visar platser som har mappats baserat på kundens land/region. Den här termisk karta fungerar på samma sätt som **[kunden efter geografi termisk karta](./customer-dashboard.md#customer-by-geography)**.

![Partner Center, analysera beställningar efter geografi](./media/orders-by-geography.png)

### <a name="orders-by-offers"></a>Beställningar per erbjudanden

Med ring diagram för **beställningar per** erbjudande ordnas beställningar (inklusive annullerade order) enligt deras namn.

- De främsta erbjudandena visas i diagrammet och resten av erbjudandena grupperas som "rest all".
- Du kan välja vissa erbjudanden i förklaringen om du bara vill visa de erbjudanden som finns i diagrammet.
- Om du hovrar över en sektor i diagrammet visas antalet order och procent andel av erbjudandet jämfört med det totala antalet beställningar i alla erbjudanden.
- I **trenderna order by-erbjudanden** visas trender i månaden. Kolumnen månad representerar antalet order efter namnet på erbjudandet. Linje diagrammet visar trenden för tillväxt i procent som ritats på en z-axel.
- Du kan använda skjutreglaget överst i diagrammet för att rulla åt höger och vänster längs x-axeln och fokusera på vissa data punkter.
- Du kan visa trend diagrammet genom att välja ett speciellt objekt i förklaringen.
- Du kan också välja att visa trender och data för **annullerade order**. Grafen fungerar på samma sätt som grafen **order by** .

### <a name="orders-trend-per-site-versus-per-seat"></a>Order trend per plats kontra per plats

Ring diagrammet **per-och-plats-per-** plats motsvarar fördelningen av SaaS per webbplats och per plats SaaS beställningar som köpts av kunder (det här diagrammet innehåller annullerade order). Stapeldiagrammet representerar trenden för per webbplats SaaS och per plats SaaS beställningar som köpts av kunder (det här diagrammet innehåller annullerade order).

### <a name="orders-by-plans"></a>Order by-planer

Diagrammet **order by-planer** representerar trenden för beställningar på plan nivå för alla dina erbjudanden (Detta inkluderar annullerade order). Ring diagrammet representerar en uppdelning av de fem översta plan orderna och stapeldiagrammet representerar trenden för beställningar för de fem främsta planerna.

### <a name="orders-and-seats-trend"></a>Trend för beställningar och platser

**Trend diagrammet order och platser** visar de översta 50-erbjudandena med det högsta antalet beställningar. De visas på en ledare och sorteras efter det högsta antalet order och ordningen i procent.

- **Order by-planer** – Välj ett erbjudande för att Visa fördelningen av order antal för de fem främsta planerna i diagrammet.
- **Platser efter planer** – den månatliga trenden för platser för de fem främsta planerna. Om erbjudandet du väljer inte är ett erbjudande per klient kommer inga data att visas här.

### <a name="canceled-orders-by-offers"></a>Annullerade beställningar efter erbjudanden

Cirkel diagrammet **avbrutna order by erbjuder** organiserar alla dina avbrutna beställningar enligt deras namn. De främsta erbjudandena visas i diagrammet och resten av erbjudandena grupperas som "rest alla". Du kan välja vissa erbjudanden i förklaringen som ska visas i diagrammet.

- Om du hovrar över en sektor i diagrammet visas antalet order och procent andelen av det valda erbjudandet jämfört med det totala antalet beställningar i alla erbjudanden.
- I stapeldiagrammet visas månads månads trender. Kolumnerna representerar antalet annullerade order efter namn. Du kan använda skjutreglaget ovanpå diagrammet för att rulla åt höger och vänster längs x-axeln och fokusera på vissa data punkter. Du kan visa trend diagrammet genom att välja ett speciellt objekt i förklaringen.

### <a name="order-details-table"></a>Order detalj tabell

Tabellen Order information visar en numrerad lista över de 1000 främsta order som sorteras efter förvärvs datum.

- Varje kolumn i rutnätet är sorterbar.
- Data kan extraheras till en TSV-fil om antalet poster är mindre än 1000.
- Om poster nummer över 1000 placeras exporterade data asynkront på sidan nedladdningar under de närmaste 30 dagarna.
- Använd filter i **tabellen Orderspecifikationer** om du bara vill visa de data du är intresse rad av. Filtrera efter land/region, typ av Azure-licens, licens typ för kommersiell Marketplace, erbjudande typ, order status, kostnads fria historik, prenumerations-ID för handels-Marketplace, kund-ID och företags namn.
- Eftersom SaaS-erbjudanden som köpts via Azure Marketplace eller AppSource inte kräver en Azure-prenumeration, så visas prenumerations-ID: t för Marketplace som 00000000-0000-0000-0000-000000000000 i avsnittet **detaljerade order data** .

#### <a name="orders-page-filters"></a>Sid filter för order

Filtren tillämpas på sidnivå.

Du kan välja flera filter för att återge diagrammet för de kriterier du väljer att visa och de data som du vill ska visas i det **detaljerade order data** rutnätet/exporten. Filter tillämpas på de data som extraheras för det data intervall som du har valt i det övre högra hörnet på sidan beställningar.

- Erbjudande typer och erbjudande namn visas bara för erbjudanden som du har beställningar för under det valda datum intervallet. Namnet på erbjudandet i listan visas för de erbjudande typer som du har valt i listan.
- Tillämpade filter visar de totala måtten inom varje markering (er) för varje valt filter. Tillämpade filter visas inte när du väljer standard valet.
- Om **alla** väljs för någon av list rutorna, kommer alla mått på den valda sidan att aggregeras. Till exempel: "alla" i filter alternativet erbjudande typer innebär att alla erbjudande typer har marker ATS. Detta är standard valet för List rutorna. Tillämpade filter visar inte något när **alla** är markerat.
- **Val av flera värden**: alla mått på sidan sammanställs för alla val som görs under List rutan. Om flera val görs visas antalet gjorda val i det filter som används. Se bilden nedan som referens.

    ![Analys av Partner Center med flera värden som tillämpas på filtret](./media/filters-applied.png)

- **Val av enstaka värde**: om ett värde är markerat visar det applicerade filtret antalet för det valda filtret. Se referensen nedan.

     ![Analys av Partner Center med ett enda värde som tillämpas på filtret](./media/filters-applied-single.png)

## <a name="next-steps"></a>Nästa steg

- En översikt över analys rapporter som är tillgängliga i partner Centers kommersiella marknads platser finns i [analys för den kommersiella Marketplace i Partner Center](./analytics.md).
- För grafer, trender och värden för sammanställda data som sammanfattar Marketplace-aktivitet för ditt erbjudande, se [översikts instrument panel i](./summary-dashboard.md)marknads plats analys.
- För virtuell dator (VM) erbjuder vi användnings-och mätnings mått i [användnings instrument panelen i den kommersiella Marketplace-analysen](./usage-dashboard.md).
- Detaljerad information om dina kunder, inklusive tillväxt trender, finns [på kund instrument panel i affärs Marketplace-analys](./customer-dashboard.md).
- En lista över dina nedladdnings begär Anden under de senaste 30 dagarna finns i [Hämta instrument panel i kommersiell Marketplace-analys](./downloads-dashboard.md).
- Om du vill se en sammanställd vy över kundfeedback för erbjudanden på Azure Marketplace och AppSource, se [klassificering och granskning på instrument panelen i kommersiell Marketplace-analys](./ratings-reviews.md).
- Vanliga frågor och svar om den kommersiella Marketplace-analysen och en omfattande ord lista med data termer finns i [vanliga frågor och termer för att få en kommersiell Marketplace-analys](./faq-terminology.md).
