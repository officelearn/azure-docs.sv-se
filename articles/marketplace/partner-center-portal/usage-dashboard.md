---
title: Instrumentpanel för användning i analys av kommersiella marknadsplatser i Partner center
description: Lär dig hur du kommer åt alla virtuella datorer erbjuder användning och mätvärden för fakturering.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 0cd6614c1ccc51a2b25f115dccf0ee389075aa70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285137"
---
# <a name="usage-dashboard-in-commercial-marketplace-analytics"></a>Instrumentpanel för användning i analys av kommersiella marknadsplatser

Den här artikeln innehåller information om instrumentpanelen användning i Partnercenter. Den här instrumentpanelen visar alla vm-erbjudanden användning och statistik med datapriser på två separata flikar: användning av virtuella datorer och faktureringsanvändning med datapriser.

Öppna instrumentpanelen **[Analysera](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** under Kommersiell marknadsplats för att komma åt instrumentpanelen för användning.

>[!NOTE]
> Detaljerade definitioner av analysterminologi finns [i Vanliga frågor och terminologi för Analys av kommersiella marknadsplatser](./faq-terminology.md).

## <a name="usage-dashboard"></a>Instrumentpanelen Användning

Användningsinstrumentpanelen representerar måtten för alla virtuella datorer (VM) som erbjuder användning och faktureringsanvändning med datapriser. Dessa finns i två separata flikar: vm-användning och användning med datapriser.

På fliken Användning av virtuell dator finns det grafiska representationer av följande objekt:

- [Sammanfattning av användning](#usage-summary)
- [Användning efter geografi](#usage-by-geography)
- [Användning av erbjudanden](#usage-by-offers)
- [Användningstrend efter erbjudanden och SKU:er](#usage-trend-by-offers-and-skus)
- [Användning efter erbjudandetyp](#usage-by-offer-type)
- [Användning efter vm-storlek](#usage-by-vm-size)
- [Användning per försäljningskanal](#usage-by-sales-channel)
- [Detaljerade användningsdata](#detailed-usage-data)

> [!NOTE]
> Analytics-rapporter visas på olika sätt i Cloud Partner Portal (CPP) och Partner Center. **Säljarinsikter** i CPP har en order- och användningsflik, som visar data för både användningsbaserade erbjudanden och icke-användningsbaserade erbjudanden. I Partnercenter visas användningsmåtten på en separat sida.

### <a name="usage-summary"></a>Användningssammanfattning

I tabellen användningssammanfattning visas kundanvändningstimmarna för alla erbjudanden som de har köpt.

- Normaliserade användningstimmar definieras som de användartimmar som normaliserats för att ta hänsyn till antalet VM-kärnor ([antal VM-kärnor] x [timmar av råanvändning]). Virtuella datorer som betecknas som "SHAREDCORE" använder 1/6 (eller 0,1666) som multiplikator [antal VM-kärnor].
- Råanvändningstimmar definieras som den tid som virtuella datorer har körts i timmar.
- Procentvärdet representerar förbrukningstillväxtförändring för det valda datumintervallet ([förra månadens användning – användning av första månaden])/ första månaden).
- Gröna trianglar som pekar uppåt indikerar en tillväxtförändring.
- Röd triangel som pekar nedåt indikerar en negativ tillväxtförändring jämfört med föregående månad.
- Mikrofältsdiagram representerar månatliga värden. Du kan visa värdet för varje månad genom att hovra över kolumnerna i diagrammet.

### <a name="usage-by-geography"></a>Användning efter geografi

Den **normaliserade användningen efter geografi** heatmap visar användningstider mappas enligt kundlandet. Landsfärgvariation representerar normaliserad användningskoncentration. Återgå till den ursprungliga vyn genom att trycka på **hemknappen** på kartan.

### <a name="usage-by-offers"></a>Användning av erbjudanden

- Den **normaliserade användningen efter erbjudande** cirkeldiagram visar en uppdelning av normaliserade användningstimmar efter erbjudanden enligt det valda datumintervallet. De 5 bästa erbjudandena visas i diagram, medan resten är grupperade i kategorin "vila alla".
- Stapeldiagrammet visar en tillväxttrend månad för månad för det valda datumintervallet. Månadskolumnerna representerar användningstimmar från erbjudandena med de högsta användningstimmarna för respektive månad. Linjediagrammet visar tillväxtprocenttrenden som ritats på den sekundära Y-axeln.
- Använd skjutreglaget högst upp i diagrammet för att bläddra åt höger till vänster längs x-axeln och/eller fokusera på specifika datapunkter.

### <a name="usage-trend-by-offers-and-skus"></a>Användningstrend efter erbjudanden och SKU:er

I det här diagrammet visas trenden för normaliserad användning för de valda SKU:erna för ett erbjudande. Erbjudandetopplistan visar de 50 bästa erbjudandena med den högsta användningen och sorteras efter användningstider. SKU-rankningslistan visar de 50 största SKU:erna med den högsta användningen för det valda erbjudandet.

### <a name="usage-by-offer-type"></a>Användning efter erbjudandetyp

- **Användningen efter cirkeldiagram för erbjudandetyp** ordnar användningen enligt erbjudandetypen.
- De översta erbjudandena visas i diagrammet och resten av erbjudandena är grupperade som "Vila alla".
- **Trenddiagrammet** visar tillväxttrender månad för månad. Kolumnen Månad representerar användningen efter de vanligaste erbjudandetyperna under den månaden.

### <a name="usage-by-vm-size"></a>Användning efter vm-storlek

Det här diagrammet representerar användningstrenden för valda VM-storlekar (max 5) för alla dina erbjudanden/SKU:er. Stapeldiagrammet är staplat med användningstimmarna för de valda VM-storlekarna.

Topplistan visar de 50 bästa vm-storlekarna med högsta användning och sorterade efter användningstider.

### <a name="usage-by-sales-channel"></a>Användning per försäljningskanal

- Användningen efter cirkeldiagram för försäljningskanaler organiserar användningen enligt försäljningskanalen
- Den översta försäljningskanalen med den högsta användningen visas i diagrammet och resten av försäljningskanalen grupperas som "Vila alla".
- Kolumnen Månad representerar användningen efter den översta försäljningskanalen under den månaden.
- Funktionerna i det här diagrammet är desamma som diagrammet "Användning av erbjudanden"

### <a name="detailed-usage-data"></a>Detaljerade användningsdata

Tabellen **användningsinformation** visar en numrerad lista över de 1000 vanligaste användningsposterna sorterade efter användning.

- Varje kolumn i rutnätet är sorterbar.
- Data kan extraheras till en CSV-fil om antalet poster är mindre än 1000.
- Om antalet poster är över 1000 placeras exportdata asynkront på en nedladdningssida som kommer att vara tillgänglig under de kommande 30 dagarna.
- Filter kan tillämpas på **detaljerade användningsdata** för att bara visa de data som du är intresserad av. Data kan filtreras efter land, försäljningskanal, Marketplace-licenstyp, användningstyp, erbjudandenamn, erbjudandetyp, kostnadsfria utvärderingsversioner, Marketplace-prenumerations-ID, kund-ID och företagsnamn.

> [!NOTE]
> Välj **användningstypen** i sidfiltret om du vill visa diagram på sidan i "Normaliserad vy" eller "Rå vy". Standardvyn för dessa diagram är "Normaliserad vy".

**Filtren för användningssidan** används på sidnivå. Du kan välja flera filter för att återge diagrammet för de villkor du väljer att visa och de data som du vill ska visas i rutnätet /export av "detaljerade användningsdata". Filter används på de data som extraheras för det dataområde som du har valt längst upp till höger på ordersidan.

- **Erbjudandetyper** och **erbjudandenamn** listas endast för de erbjudanden du har köpt under det valda datumintervallet. Erbjudandenamn i listan visas för erbjudandetyper som är valda i listan.
- Standardvalet är "Alla" för vart och ett av filteralternativen, med undantag för **användningstyp**. Standardvalet för **användningstyp** är normaliserad användning. Om du vill visa råanvändning i diagrammen väljer du "råanvändning".
- Tillämpade filter visar antal val för de filterval som gjordes. Tillämpade filter visas inte för standardvalen.

> [!NOTE]
> En detaljerad definition av vart och ett av fälten i rutnätet "detaljerade orderdata", sidfilter och alla möjliga val definieras i avsnittet dataordlista i artikeln [vanliga frågor och annan terminologi.](link needed)

På fliken **Faktureringsanvändning med datapriser** visas användningsinformation för erbjudandetyper där användningen mäts per mätardimension. SaaS erbjudande typ överage presenteras för närvarande. På fliken visas grafiska representationer av överförbrukningstrender för Faktureringsanvändning med saas-datapriser:

- **Överkörningstrend per mätardimension**: Visar den månatliga överkörningstrenden för den valda mätardimensionen i ett erbjudande. X-Axis representerar månaden och Y-axeln representerar användningskvantiteten. Måttenheten för den anpassade mätaren visas också på Y-axeln.
- **Överförbrukningstrend efter SKU**: Representerar trenden för användningskvantiteten för den valda mätardimensionen efter SKU:er. De SKU:er som visas representerar de fem största SKU:erna med den högsta mängden användning för det valda erbjudandet.
- **Överförbrukning trend av Top 50 Kunder:** De 50 erbjudanden med högst användning timmar visas på en ***leader board*** och rankas efter den högsta användningen av den anpassade mätaren. Välj en kund i topplistan om du vill visa användningstrenden för en vald mätardimension.
- **Överförbrukning trend av toppkunder:** Presenterar topp kund percentil (s) som bidrar till % av den totala användningen. Den översta kund percentilen visas längs X-axeln och bestäms av kundens användningskvantitet. Y-axeln visar användningskvantiteten. Du kan visa information genom att hovra över punkter längs linjediagrammet.

> [!NOTE]
> Användningsinformationen och alla diagram på den här sidan visas för den mätardimension som väljs för sidfiltret.

## <a name="next-steps"></a>Efterföljande moment

- En översikt över analysrapporter som är tillgängliga på Den kommersiella marknaden för partnercenter finns i [Analytics för den kommersiella marknadsplatsen i Partner Center](./analytics.md).
- Diagram, trender och värden för aggregerade data som sammanfattar marketplace-aktivitet för ditt erbjudande finns [i Sammanfattningsinstrumentpanel i analys av kommersiella marknadsplatser](./summary-dashboard.md).
- Information om dina beställningar i ett grafiskt och nedladdningsbart format finns [i Instrumentpanelen för order i Analys av Kommersiella marknadsplatser](./orders-dashboard.md).
- Detaljerad information om dina kunder, inklusive tillväxttrender, finns [i Customer Dashboard i Kommersiell Marketplace-analys](./customer-dashboard.md).
- En lista över dina hämtningsbegäranden under de senaste 30 dagarna finns [i Hämta instrumentpanel i Kommersiell Marketplace-analys](./downloads-dashboard.md).
- Information om hur du ser en konsoliderad vy över kundfeedback för erbjudanden på Azure Marketplace och AppSource finns [i instrumentpanelen Klassificeringar och recensioner i Kommersiell Marketplace-analys](./ratings-reviews.md).
- Vanliga frågor och svar om Analys av kommersiella marknadsplatser och en omfattande ordlista med datatermer finns i [Vanliga frågor och terminologi för Kommersiell Marketplace-analys](./faq-terminology.md).
