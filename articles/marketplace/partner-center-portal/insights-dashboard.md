---
title: Marknads insikter – Microsoft Commercial Marketplace
description: Få åtkomst till en sammanfattning av Marketplace Web Analytics, som gör att du kan mäta kund engagemang i Microsoft AppSource och Azure Marketplace.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: 2d9691279b987f9d45f755ab340ff70671d350c0
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/21/2020
ms.locfileid: "83745659"
---
# <a name="marketplace-insights-dashboard-in-partner-center"></a>Marketplace insikter-instrument panel i Partner Center

Den här artikeln innehåller information om instrument panelen för marknads insikter i Partner Center. Den här instrument panelen visar en sammanfattning av Marketplace Web Analytics, som gör det möjligt för utgivare att mäta kund engagemang för sina respektive produkt informations sidor på Marketplace butiker: Microsoft AppSource och Azure Marketplace.

## <a name="marketplace-insights-dashboard"></a>Instrumentpanelen Marknadsplatsinsikter

Om du vill komma åt **Marketplace Insights-instrumentpanelen** i Partner Center öppnar du **[fliken analysera](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** under kommersiell marknads plats.

Du kan visa grafiska representationer av följande objekt:  

- [Sammanfattning av marknads insikter](#marketplace-insights-summary)
- [Sid besök per geografi](#page-visits-by-geography)  
- [Sid besök jämfört med unika besökare](#page-visits-versus-unique-visitors-trend)
- [Anrop till åtgärd respektive unika besökare med CTAs](#call-to-action-versus-unique-visitors-with-ctas)
- [Sid besök och anrop till åtgärder efter erbjudanden](#page-visits-and-calls-to-action-by-offers)
- [Anrop till procentuell trend för åtgärder](#call-to-action-percentage-trend)
- [Sid besök och anrop till åtgärder efter referens domäner](#page-visits-and-calls-to-action-by-referral-domains)
- [Informations tabell för Marketplace](#marketplace-insights-details-table)

>[!NOTE]
> Detaljerade definitioner av analys terminologi finns i [vanliga frågor och terminologi för affärs platsers analys](./faq-terminology.md).

### <a name="insights-dashboard-layout"></a>Layout för insikter-instrumentpanel

Du kan visa Marketplace-mått på flera olika sätt:

- Butik-flikar
- Sid filter
- Datumfilter

**Butik-flikar**: du kan visa måtten för dina erbjudanden separat via AppSource-& Azure Marketplace-flikar. Välj erbjudandet i list rutan erbjudande till höger om du vill se en visualisering av måtten för de valda erbjudandet. Som standard är alla erbjudanden markerade.

![List rutan med instrument panelen för insikter för partner Center](./media/insights-offer-dropdown.png)

**Sid filter för insikter**: dessa filter tillämpas på erbjudande nivån (produkt informations sidan). Du kan välja flera filter för de kriterier som du vill visa. Det här filtret gäller för hela Marketplace Insights-avsnittet, inklusive diagram och information.

![Instrument panels sid filter för partner Center Insights](./media/insights-page-filter.png)

- Erbjudande namn visas bara för erbjudanden som har sid besök i det valda datum intervallet.  
- Standard valet är all för varje filter alternativ
- Tillämpade filter visar antalet markeringar för de val som har gjorts. Tillämpade filter kommer inte att visas för standard alternativet all.

![Använda Partner Center Insights-filter](./media/insights-page-filter-two.png)

**Datum filter för insikter**: det här filtret gäller för hela Marketplace Insights-avsnittet. Filter kan innehålla fördefinierade datum intervall eller ett anpassat datum intervall.

![Datum filter för partner Center Insights](./media/insights-date-range.png)

## <a name="marketplace-insights-summary"></a>Sammanfattning av marknads insikter

I avsnittet Översikt över marknads insikter visas ett antal **sid besök**, **anrop till åtgärd**och **unika besökare** för det valda datum intervallet.

### <a name="page-visits"></a>Sid besök

Det här talet representerar antalet distinkta användarsessioner på sidan erbjudande (produkt information) för det valda datum intervallet. Indikatorn för rött/grönt representerar tillväxten i procent av sid besöken. Trend diagrammet visar antalet sid besök per månad.

### <a name="unique-visitors"></a>Unika besökare

Antalet visar antalet distinkta besökare under det valda datum intervallet för de offerter som har marker ATS i sid filtret. En besökare som har besökt en eller flera produkt informations sidor räknas som en unik besökare.

### <a name="call-to-action"></a>Anrop till åtgärd

Det här talet representerar knappen för **att** Klicka på slutförd på sidan erbjudande på sidan erbjudande (produkt informations sida). **Anrop till åtgärden** räknas när knapparna **Hämta nu**, **kontakta mig**och **Free trial** **testa enhet** är markerade.

![Samtal till åtgärds Sammanfattning för partner Center](./media/insights-summary.png)

## <a name="page-visits-by-geography"></a>Sid besök per geografi

I termisk karta nedan visas antalet **sid besök**, **anrop till åtgärd**och **unika besökare enligt kund land**. Högre sid besök representeras av mörkare kart färger och lägre sid besök representeras av ljusare kart färger.

![Geografisk spridning för partner Center Insights](./media/insights-geography.png)

Termisk karta innehåller följande funktioner:

- Termisk karta har ett extra rutnät för att visa information om **sid besök**, **samtal till åtgärd** och **unika besökare** på en angiven plats. Du kan zooma in en viss plats om du föredrar det.  
- **Uppslag i länder/regioner** är antalet länder/regioner från vilka kunderna har rapporterat sid besök under det valda datum intervallet.
- Du kan söka efter och välja ett land i rutnätet för att zooma in på platsen i kartan. Återgå till den ursprungliga vyn genom att välja **Start** på kartan.

## <a name="page-visits-versus-unique-visitors-trend"></a>Sid besök jämfört med unika besökare

Kolumnerna nedan representerar antalet sid besök som visas på Y-axeln (axel till vänster i diagrammet). Trend linjen representerar den månatliga trenden för unika besökare, som visas på den sekundära Y-axeln (axeln till höger i diagrammet) för dina erbjudanden som publiceras i butiker: Azure Marketplace och AppSource.

![Partner Center Insights sid besök och unika besökares trender](./media/insights-page-vists-unique-visitors.png)

## <a name="call-to-action-versus-unique-visitors-with-ctas"></a>Anrop till åtgärd respektive unika besökare med CTAs

Staplade kolumner representerar månads samtal till åtgärd (centrum), som är uppdelat efter typer av flygkrafts typer (**Hämta det nu**, **kontakta mig**och en **kostnads fri utvärderings version**) och ritas på Y-axeln (axel till vänster på sidan). Trend linjen representerar den månatliga trenden för unika besökare med CTAs, som visas på den sekundära Y-axeln (axel till höger i diagrammet) för dina erbjudanden som publiceras i Azure Marketplace och AppSource.

![Partner Center insikter anrop till åtgärder respektive unika besökare med CTAs](./media/insights-call-to-action-unique-visitors.png)

## <a name="page-visits-and-calls-to-action-by-offers"></a>Sid besök och anrop till åtgärder efter erbjudanden

Det yttre cirkel diagrammet representerar en uppdelning av **sid besök** baserat på erbjudanden som du har publicerat i Marketplace och valt i filtret. Det inre diagrammet representerar **anrop till åtgärds** analys för samma erbjudanden.

![Partner Center Insights sid besök och samtal till åtgärder efter erbjudanden](./media/insights-page-visits-and-cta-by-offer.png)

## <a name="call-to-action-percentage-trend"></a>Anrop till procentuell trend för åtgärder

I **trenden för åtgärder i procent** presenteras den procentuella procent satsen för de erbjudanden som publicerats på Marketplace. CENTRUM% = (CTAs/sid-besök) * 100.

![Partner Center Insights-anrop till procentuell trend](./media/insights-call-to-action-percentage-trend.png)

## <a name="page-visits-and-calls-to-action-by-referral-domains"></a>Sid besök och anrop till åtgärder efter referens domäner

I diagrammet nedan visas de främsta referens domänerna för 50. Om du väljer en angiven hänvisnings domän visas den månatliga trenden för sid besök och anrop till åtgärden i diagrammet till höger.

![Partner Center Insights sid besök och anrop till åtgärder efter referens domäner och kampanjer](./media/insights-page-visits-call-to-actions.png)

## <a name="marketplace-insights-details-table"></a>Informations tabell för Marketplace

Den här tabellen innehåller en listvy över sid besöken och anrop till åtgärder för dina valda erbjudanden sorterade efter datum.

![Informations tabell för partner Center Insights](./media/insights-details-page.png)

- Data kan extraheras till en CSV-fil om antalet poster är mindre än 1000.
- Om antalet poster är över 1000 placeras exporterade data asynkront på sidan nedladdningar under de närmaste 30 dagarna.
- Filter kan användas för att visa de data som du är intresse rad av. Data kan filtreras efter erbjudande namn och kampanj namn.  

## <a name="next-steps"></a>Nästa steg

- En översikt över analys rapporter som är tillgängliga i partner Centers kommersiella marknads platser finns i [analys för den kommersiella Marketplace i Partner Center](./analytics.md).
- För grafer, trender och värden för sammanställda data som sammanfattar Marketplace-aktivitet för ditt erbjudande, se [översikts instrument panel i](./summary-dashboard.md)marknads plats analys.
- Information om dina beställningar i ett grafiskt och nedladdnings Bart format finns i [order instrument panel i kommersiell Marketplace-analys](./orders-dashboard.md).
- För virtuell dator (VM) erbjuder vi användnings-och mätnings mått i [användnings instrument panelen i den kommersiella Marketplace-analysen](./usage-dashboard.md).
- Detaljerad information om dina kunder, inklusive tillväxt trender, finns [på kund instrument panel i affärs Marketplace-analys](./customer-dashboard.md).
- En lista över dina nedladdnings begär Anden under de senaste 30 dagarna finns i [Hämta instrument panel i kommersiell Marketplace-analys](./downloads-dashboard.md).
- Om du vill se en sammanställd vy över kundfeedback för erbjudanden på Azure Marketplace och AppSource, se [klassificering och granskning på instrument panelen i kommersiell Marketplace-analys](./ratings-reviews.md).
- Vanliga frågor och svar om den kommersiella Marketplace-analysen och en omfattande ord lista med data termer finns i [vanliga frågor och termer för att få en kommersiell Marketplace-analys](./faq-terminology.md).
