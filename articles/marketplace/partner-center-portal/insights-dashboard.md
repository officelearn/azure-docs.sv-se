---
title: Instrumentpanelen för Marketplace Insights i analys av kommersiella marknadsplatser i Partner Center
description: Få tillgång till en sammanfattning av marketplace-webbanalys, som gör det möjligt för utgivare att mäta kundengagemang i AppSource- och Azure Marketplace-skyltfönster.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: a547ced9df98298361360ecab88036599cd86027
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275857"
---
# <a name="marketplace-insights-dashboard-in-commercial-marketplace-analytics"></a>Instrumentpanelen för Marketplace Insights i analyser på kommersiella marknadsplatser

Den här artikeln innehåller information om instrumentpanelen för Marketplace Insights i Partner Center. Den här instrumentpanelen visar en sammanfattning av marketplace-webbanalys, som gör det möjligt för utgivare att mäta kundengagemang för sina respektive produktinformationssidor som anges på marknadsplatsfönster: AppSource och Azure Marketplace.

## <a name="marketplace-insights-dashboard"></a>Instrumentpanelen Marknadsplatsinsikter

Om du vill komma åt **instrumentpanelen för Marketplace Insights** i Partner Center öppnar du **[fliken Analysera](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** under Kommersiell marknadsplats.

Du kan visa grafiska representationer av följande objekt:  

- [Sammanfattning av Marketplace Insights](#marketplace-insights-summary)
- [Sidbesök efter geografi](#page-visits-by-geography)  
- [Sidbesök kontra unik besökstrend](#page-visits-versus-unique-visitors-trend)
- [Call to action kontra unika besökare med CTAs](#call-to-action-versus-unique-visitors-with-ctas)
- [Sidbesök och uppmaning till handling av erbjudanden](#page-visits-and-calls-to-action-by-offers)
- [Procentuell trend för uppmaning](#call-to-action-percentage-trend)
- [Sidbesök och uppmaningar från hänvisningsdomäner](#page-visits-and-calls-to-action-by-referral-domains)
- [Informationstabell för Marketplace Insights](#marketplace-insights-details-table)

>[!NOTE]
> Detaljerade definitioner av analysterminologi finns [i Vanliga frågor och terminologi för Analys av kommersiella marknadsplatser](./faq-terminology.md).

### <a name="insights-dashboard-layout"></a>Instrumentpanelslayout för insikter

Du kan visa Marketplace-måtten på flera olika sätt:

- Flikar i storefönstret
- Sidfilter
- Datumfilter

**Skyltfönster flikar:** Du kan visa måtten för dina erbjudanden separat via AppSource & Azure Marketplace flikar. Välj erbjudanden i listrutan erbjudande till höger om du vill visa en visualisering av måtten för de valda erbjudandena. Som standard är alla erbjudanden markerade.

![Listrutan Partner Center Insights-instrumentpanel erbjuder](./media/insights-offer-dropdown.png)

**Statistik sidfilter:** Dessa filter tillämpas på erbjudandet (produktinformation sida) nivå. Du kan välja flera filter för de villkor som du vill visa. Det här filtret gäller för hela Avsnittet Marketplace Insights, inklusive diagram och information.

![Instrumentpanelsfilter för Partner Center Insights](./media/insights-page-filter.png)

- Erbjudandenamn visas endast för de erbjudanden som har sidbesök i det valda datumintervallet.  
- Standardvalet är "Alla" för vart och ett av filteralternativen
- Tillämpade filter visar antalet val för de val som gjorts. Tillämpade filter visas inte för standardvalet Alla.

![Insights-filter för Partner Center](./media/insights-page-filter-two.png)

**Datumfilter för insikter**: Det här filtret gäller för hela avsnittet Marketplace Insights. Filter kan innehålla förutbestämda datumintervall eller ett anpassat datumintervall.

![Datumfilter för Partner Center Insights](./media/insights-date-range.png)

## <a name="marketplace-insights-summary"></a>Sammanfattning av Marketplace Insights

I sammanfattningsavsnittet för Marketplace-insikter visas antalet **sidbesök,** **uppmaningar**och **unika besökare** för det valda datumintervallet.

### <a name="page-visits"></a>Sidbesök

Det här numret representerar antalet olika användarsessioner på erbjudandesidan (produktinformationssida) för ett valt datumintervall. Den rödgröna procentindikatorn representerar ökningen % av sidbesöken. Trenddiagrammet representerar antalet sidbesök i månad för månad.

### <a name="unique-visitors"></a>Unika besökare

Det här numret representerar det distinkta antalet besökare under det valda datumintervallet för de erbjudanden som valts i sidfiltret. En besökare som har besökt en eller flera produktdetaljer räknas som en unik besökare.

### <a name="call-to-action"></a>Uppmaning till handling

Det här numret representerar antalet klick på call **to action-knappen** som har slutförts på erbjudandesidan (produktinformationssida). **Anrop** räknas när knapparna **Hämta den nu**, Kostnadsfri **utvärderingsversion**, **Kontakta mig**och **Testenhetsknapparna** är markerade.

![Sammanfattning av partnercenterinsikter](./media/insights-summary.png)

## <a name="page-visits-by-geography"></a>Sidbesök efter geografi

Heatmap nedan visar antalet **sidbesök,** **uppmaningar**och **unika besökare enligt kundland.** Högre sidbesök representeras av mörkare kartfärger och lägre sidbesök representeras av ljusare kartfärger.

![Partner Center Insights geografiska spridning](./media/insights-geography.png)

Heatmap innehåller följande funktioner:

- Heatmap har ett kompletterande rutnät för att visa information om **sidbesök,** **uppmaningar** och **unika besökare** på en viss plats. Du kan zooma in på en viss plats om så önskas.  
- **Spridda länder** är antalet länder från där dina kunder har rapporterat sidbesök under det valda datumintervallet.
- Du kan söka och välja ett land i rutnätet för att zooma in på platsen på kartan. Återgå till den ursprungliga vyn genom att välja **Hem** på kartan.

## <a name="page-visits-versus-unique-visitors-trend"></a>Sidbesök kontra unik besökstrend

Kolumnerna nedan representerar antalet månatliga sidbesök, som visas på Y-axeln (axel till vänster i diagrammet). Trendlinjen representerar den månatliga trenden för unika besökare, som visas på den sekundära Y-Axeln (axel till höger i diagrammet), för dina erbjudanden som publiceras i skyltfönster: Azure Marketplace och AppSource.

![Sidbesök för Partner Center Insights jämfört med unik besökstrend](./media/insights-page-vists-unique-visitors.png)

## <a name="call-to-action-versus-unique-visitors-with-ctas"></a>Call to action kontra unika besökare med CTAs

De staplade kolumnerna representerar månatliga uppmaningar (CTA), som är uppdelade efter CTA-typer (**Get it now**, Contact **me**och **Free Trial**) och ritas på Y-axeln (axel till vänster på sidan). Trendlinjen representerar den månatliga trenden för unika besökare med certifikatförvaltsförkortningar, som visas på den sekundära Y-axeln (axel till höger i diagrammet), för dina erbjudanden som publiceras i Azure Marketplace och AppSource.

![Partner Center Insights uppmaning till handling kontra unika besökare med CTA](./media/insights-call-to-action-unique-visitors.png)

## <a name="page-visits-and-calls-to-action-by-offers"></a>Sidbesök och uppmaningar från erbjudanden

Det yttre cirkeldiagrammet representerar fördelningen av **sidbesök** baserat på erbjudanden som du har publicerat på marknadsplatsen och valt i filtret. Det inre diagrammet representerar **åtgärdsuppdelningen** för samma erbjudanden.

![Besök och uppmaningar till åtgärder på partnercentersidan](./media/insights-page-visits-and-cta-by-offer.png)

## <a name="call-to-action-percentage-trend"></a>Procentuell trend för uppmaning

Trenden **Med uppmaningsprocent** visar CTA-procentsatsen för de erbjudanden som publiceras på marknaden. CTA % = (CTAs/sidbesök) * 100.

![Partner Center Insights call to action procenttrend](./media/insights-call-to-action-percentage-trend.png)

## <a name="page-visits-and-calls-to-action-by-referral-domains"></a>Sidbesök och uppmaningar från hänvisningsdomäner

Diagrammet nedan presenterar de 50 referensdomänerna. Om du väljer en specifik referensdomän visas den månatliga trenden för sidbesök och uppmaningar i diagrammet till höger.

![Sidbesök och uppmaningar till åtgärder från partnercenterinsikter och uppmaningar från hänvisningsdomäner och kampanjer](./media/insights-page-visits-call-to-actions.png)

## <a name="marketplace-insights-details-table"></a>Informationstabell för Marketplace Insights

Den här tabellen innehåller en listvy över sidbesöken och uppmaningarna för dina valda erbjudanden sorterade efter datum.

![Informationstabell för Partner Center Insights](./media/insights-details-page.png)

- Data kan extraheras till en CSV-fil om antalet poster är mindre än 1000.
- Om antalet poster är över 1000 placeras exporterade data asynkront på en hämtningssida under de kommande 30 dagarna.
- Filter kan användas för att visa de data som du är intresserad av. Data kan filtreras efter erbjudandenamn och kampanjnamn.  

## <a name="next-steps"></a>Nästa steg

- En översikt över analysrapporter som är tillgängliga på Den kommersiella marknaden för partnercenter finns i [Analytics för den kommersiella marknadsplatsen i Partner Center](./analytics.md).
- Diagram, trender och värden för aggregerade data som sammanfattar marketplace-aktivitet för ditt erbjudande finns [i Sammanfattningsinstrumentpanel i analys av kommersiella marknadsplatser](./summary-dashboard.md).
- Information om dina beställningar i ett grafiskt och nedladdningsbart format finns [i Instrumentpanelen för order i Analys av Kommersiella marknadsplatser](./orders-dashboard.md).
- För virtuell dator (VM) erbjuder användnings- och datapriser för faktureringsmått finns [i Användningsinstrumentpanel i kommersiell marketplace-analys](./usage-dashboard.md).
- Detaljerad information om dina kunder, inklusive tillväxttrender, finns [i Customer Dashboard i Kommersiell Marketplace-analys](./customer-dashboard.md).
- En lista över dina hämtningsbegäranden under de senaste 30 dagarna finns [i Hämta instrumentpanel i Kommersiell Marketplace-analys](./downloads-dashboard.md).
- Information om hur du ser en konsoliderad vy över kundfeedback för erbjudanden på Azure Marketplace och AppSource finns [i instrumentpanelen Klassificeringar och recensioner i Kommersiell Marketplace-analys](./ratings-reviews.md).
- Vanliga frågor och svar om Analys av kommersiella marknadsplatser och en omfattande ordlista med datatermer finns i [Vanliga frågor och terminologi för Kommersiell Marketplace-analys](./faq-terminology.md).
