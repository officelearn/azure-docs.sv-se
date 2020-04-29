---
title: Instrument panel för partner Center-beställning i kommersiell Marketplace-analys
description: Lär dig hur du får åtkomst till analys rapporter om dina Marketplace-erbjudanden i ett grafiskt och nedladdnings Bart format.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 148b80a6843168b11e4feb3558f6bf08eaeb3ace
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81261269"
---
# <a name="orders-dashboard-in-commercial-marketplace-analytics"></a>Order instrument panel i kommersiell Marketplace-analys

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
- [Beställningar efter SKU: er](#orders-by-skus)
- [Trend för beställningar och platser](#orders-and-seats-trend)
- [Order detalj tabell](#order-details-table)

> [!NOTE]
> Det finns skillnader mellan hur analys rapporter visas i Cloud Partner Portal (CPP) och det nya kommersiella Marketplace-programmet i Partner Center. Ett särskilt sätt är att **säljar insikter** i cpp har en **order & fliken användning** , som visar data för användnings erbjudanden och icke-användnings erbjudanden. Sidan **beställningar** i Partner Center har en separat flik för SaaS-erbjudanden.

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

Termisk karta för **beställningar per geografi** visar antalet beställningar på en världs karta och visar platser som har mappats baserat på kund land. Den här termisk karta fungerar på samma sätt som **[kunden efter geografi termisk karta](./customer-dashboard.md#customer-by-geography)**.

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

Ring diagrammet **per plats och per klient** representerar fördelningen av per plats-SaaS och per plats SaaS beställningar som köpts av kunder (det här diagrammet innehåller annullerade order). Stapeldiagrammet representerar trenden för per webbplats SaaS och per plats SaaS beställningar som köpts av kunder (det här diagrammet innehåller annullerade order).

### <a name="orders-by-skus"></a>Beställningar efter SKU: er

Diagrammet **order by SKU: er** representerar trenden för beställningar på lagerhållnings enhets nivån (SKU) för alla dina erbjudanden (Detta inkluderar annullerade order). Ring diagrammet representerar fördelningen av de fem främsta SKU-orderna och stapeldiagrammet representerar trenden för beställningar för de fem främsta SKU: erna.

### <a name="orders-and-seats-trend"></a>Trend för beställningar och platser

**Trend diagrammet order och platser** visar de översta 50-erbjudandena med det högsta antalet beställningar. De visas på en ledare och sorteras efter det högsta antalet order och ordningen i procent.

- **Beställningar efter SKU: er**: Välj ett erbjudande för att Visa fördelningen av order antal för de fem främsta SKU: erna i diagrammet.
- **Platser efter SKU: er**månads Visa platser för de fem främsta SKU: erna. Om erbjudandet du väljer inte är ett erbjudande per klient visas inte några data i det här ytdiagrammet-diagrammet.

### <a name="canceled-orders-by-offers"></a>Annullerade beställningar efter erbjudanden

Cirkel diagrammet **avbrutna order by erbjuder** organiserar alla dina avbrutna beställningar enligt deras namn. De främsta erbjudandena visas i diagrammet och resten av erbjudandena grupperas som "rest alla". Du kan välja vissa erbjudanden i förklaringen som ska visas i diagrammet.

- Om du hovrar över en sektor i diagrammet visas antalet order och procent andelen av det valda erbjudandet jämfört med det totala antalet beställningar i alla erbjudanden.
- I stapeldiagrammet visas månads månads trender. Kolumnerna representerar antalet annullerade order efter namn. Du kan använda skjutreglaget ovanpå diagrammet för att rulla åt höger och vänster längs x-axeln och fokusera på vissa data punkter. Du kan visa trend diagrammet genom att välja ett speciellt objekt i förklaringen.

### <a name="order-details-table"></a>Order detalj tabell

Tabellen Order information visar en numrerad lista över de 1000 främsta order som sorteras efter förvärvs datum.

- Varje kolumn i rutnätet är sorterbar.
- Data kan extraheras till en TSV-fil om antalet poster är mindre än 1000.
- Om poster nummer över 1000 placeras exporterade data asynkront på sidan nedladdningar under de närmaste 30 dagarna.
- Filter kan tillämpas på **tabellen Order information** för att visa endast de data som du är intresse rad av. Data kan filtreras efter land, Azure-licensserver, Marketplace-licens typ, erbjudande typ, order status, kostnads fria historik, prenumerations-ID för Marketplace, kund-ID och företags namn.

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
