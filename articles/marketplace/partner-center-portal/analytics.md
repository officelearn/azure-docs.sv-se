---
title: Analytics för kommersiella Marketplace i Partnercenter
description: Lär dig hur du kommer åt analytiska rapporter för att övervaka försäljning, utvärdera prestanda och optimera dina marketplace-erbjudanden.
author: mattwojo
manager: evansma
ms.author: shthota
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/31/2019
ms.openlocfilehash: 0da95dbdca52232b4b24ad921bf2d9c78dc76d56
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66247731"
---
# <a name="analytics-for-the-commercial-marketplace-in-partner-center"></a>Analytics för kommersiella Marketplace i Partnercenter

Lär dig hur du kommer åt analysrapporter i Microsoft Partner Center för att övervaka försäljning, utvärdera prestanda och optimera dina erbjudanden i marketplace. Som partner kan du övervaka din erbjudanderegistreringar med hjälp av datavisualisering och insight-diagram som stöds av Partner Center och hitta sätt att maximera din försäljning. Förbättrad analytics-verktyg kan du agera på prestandaresultat och underhålla relationerna med dina kunder och återförsäljare. 

Om du vill få åtkomst till Partner Center analytics-verktyg, öppna den **[analysera](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** instrumentpanelen under kommersiella Marketplace.

|**Instrumentpanel**|**Visade innehåll**|
|:---|:---|
|[Sammanfattning](#summary-dashboard)|Diagram, trender och värdena för aggregerade data som sammanfattar marknadsplatsaktivitet för dina erbjudanden|
|[Order](#orders-dashboard)|Information om dina order på ett grafiskt och nedladdningsbara format|
|[Kunder](#customer-dashboard)|Information om dina kunder, inklusive tillväxt trender, visas i en grafisk och nedladdningsbara format|
|[Nedladdningar](#downloads-dashboard)|En lista över dina hämtningsbegäranden under de senaste 30 dagarna|

## <a name="summary-dashboard"></a>Instrumentpanel för sammanfattningar

Den **sammanfattning** instrumentpanelen visar en översikt baserat på varje typ av erbjudande. **Insikter** visar viktig information på ett ögonblick och ger en bred vy över försäljningsaktivitet av dina erbjudanden. Du kan visualisera dessa rapporter med hjälp av den **sammanfattning** instrumentpanelen:

- [Totalt antal beställningar](#totals)
- [Totalt antal kunder](#totals)
- [Geografisk plats för dina kunder](#customers-by-geography)
- [Kommande trender baserat på informationen kunden och ordning](#growth-trend-chart)
- [Kunden rankningslista med högsta order](#customer-leaderboard)
- [Antal beställningar ordnade efter erbjudandenamn](#offers-by-orders)

### <a name="date-range"></a>Datumintervall

Du hittar en val av datum tidsintervall i det övre högra hörnet på varje sida. Utdata från den **sammanfattning** sidan diagram kan anpassas genom att välja ett datumintervall baserat på senaste 3, 6 eller 12 månader eller genom att välja ett eget datumintervall med en maximal varaktighet för 12 månader. Standardvärden är sex månader.

![Partner Center analysera instrumentpanel](./media/analyze-dashboard.png)

### <a name="totals"></a>Summor

Den **summor** avsnittet visas en uppräkning av alla order som skapas eller kunder som har köpt under det valda datumintervallet. 

- Procentvärdet bredvid **Totalt antal beställningar** och **totala kunder** representerar det tillväxten jämfört med föregående månad. 
- En grön triangel som pekar anger uppåt ett positivt Tillväxttrend. En röd triangel som pekar anger nedåt en negativ Tillväxttrend i förhållande till föregående månad. 
- Ordning och kundernas tillväxt trender representeras av diagram och visar värdet för varje månad genom att hovra över kolumner i diagrammet.

![Partner Center analysera summor](./media/analyze-totals.png)

### <a name="customers-by-geography"></a>Kunder efter geografi

Den **kunder efter geografi** termisk karta visar antalet kunder på en världskarta. 

- Du kan flytta kartan för att visa den exakta platsen. 
- Du kan zooma in en viss plats. 
- Den termiska kartan har ett rutnät som aviserar om ytterligare för att visa information om antalet kunder (eller [order antal](#orders-by-geography)) på specifik plats. 
- Du kan söka efter och välj ett land i rutnätet för att zooma till plats på kartan. Återgå till den ursprungliga vyn genom att trycka på den **Start** knappen på kartan.
- En **nya** kunden har köpt en av dina erbjudanden för första gången under månaden i det valda datumintervallet.

![Partner Center analysera kundgeografi](./media/analyze-customer-geography.png)

### <a name="growth-trend-chart"></a>Trenddiagram för tillväxt

Du kan visa trender baserat på tillväxten av dina **order som skapas** eller **kunder som har köpt**, visas per månad enligt det valda datumintervallet. Du kan ytterligare analysera dessa trender genom att välja länkarna nedan diagrammet, som går du till respektive **ordning** eller **kunden** sidor.

![Partner Center analysera tillväxten trender](./media/analyze-growth-trends.png)

### <a name="customer-leaderboard"></a>Kunden rankningslista

Upp 50 kunder med det högsta antalet beställningar visas på en *ledartavla*, sorterade efter högst antal i ordning och i procent för ordern. 

- Välj en kund att visa deras profilinformation, order ordnade efter erbjudandet eller beställningar ordnade efter Azure licens typ och priser kanal. 
- Den **erbjuder med order** ringdiagram presenterar de översta fyra erbjudandena (efter ordning antal) och de återstående erbjuder grupperade i som Rest alla.

> [!NOTE]
> Kunders personliga information visas bara om kunden har gett ditt medgivande. Du kan visa den här informationen om du har loggat in med ett **ägare** behörigheter rollnivå. Användare med den **deltagare** rollen kommer inte att kunna visa den här informationen. [Mer information om roller och behörigheter](./manage-account.md#define-user-roles-and-permissions).

![Partner Center analysera tillväxten trender](./media/analyze-growth-trends.png)

### <a name="offers-by-orders"></a>Erbjudanden med order

Den **erbjuder med order** diagrammet ordnar dina aktiva order enligt erbjudandenamn. 

- Du kan dra över sektorer från vänster ringdiagrammet till rätt ringdiagram för att visa mer information om erbjudandet som du har dragit. De här två diagrammen kan du jämföra ett specifikt erbjudande med prestanda hos alla dina erbjudanden som (”Rest alla”). 

![Partner Center analysera erbjudanden med order](./media/analyze-offer-by-order.png)

## <a name="orders-dashboard"></a>Order-instrumentpanel

Den **order** instrumentpanelen för den **analysera** menyn visar aktuella order för alla dina SaaS-erbjudanden. Du kan visa grafiska representationer av:

- [Ordning summor](#order-totals)
- [Order efter geografi](#orders-by-geography)
- [Trender för aktiva och avbrutna order](#trends-for-active-and-canceled-orders)
- [Order ordnade efter marketplace licenstyp](#orders-by-marketplace-license-type)
- [Order ordnade efter nya och befintliga kunder](#orders-by-customer-type)
- [Information om sorteringstabellen](#order-details-table)

> [!NOTE]
> Det finns skillnader mellan hur analysrapporter visas i Cloud Partner Portal (CPP) och det nya kommersiella Marketplace-programmet i Partnercenter. Ett visst sätt är att den **försäljning Insights** i CPP har en **beställningar och användning** fliken som visar data för användningsbaserade erbjudanden och icke-användningsbaserade erbjudanden. Partner Center den **order** sidan har en separat flik för SaaS-erbjudanden.

### <a name="order-totals"></a>Ordning summor

Den **Order summor** avsnittet visas en uppräkning av alla order som skapas, inklusive både **Active** och **har avbrutits** beställningar under den valda [datumintervall ](#date-range). 

- Procentvärdet bredvid **Totalt antal beställningar** representerar det tillväxten jämfört med föregående månad. 
- En grön triangel som pekar anger uppåt ett positivt Tillväxttrend. En röd triangel som pekar anger nedåt en negativ Tillväxttrend i förhållande till föregående månad. 
- Tillväxt trender representeras av diagram och visar värdet för varje månad genom att hovra över kolumner i diagrammet.

### <a name="orders-by-geography"></a>Order efter geografi

Den **order efter geografi** termisk karta som visar antalet dina order på en världskarta i hela och fungerar på samma sätt som den  **[kunder efter geografi termisk karta](#customers-by-geography)** .

### <a name="trends-for-active-and-canceled-orders"></a>Trender för aktiva och avbrutna order

Den **aktiv beställningar av erbjudanden** ringdiagram graph organiserar alla dina aktiva order enligt deras namn för erbjudandet.

- Främsta 4 erbjudanden visas i diagrammet och grupperas resten av dina erbjudanden som Rest alla.
- Du kan välja specifika erbjudanden i förklaringen för att visa endast de erbjudandena i diagrammet. 
- Hovra över en sektor i diagrammet visar antalet order och procentandel av erbjudandet jämfört med det totala antalet beställningar över alla erbjudanden.
- **Beställningar av erbjudanden trend** visar per månad tillväxt trender. Kolumnen månad representerar antalet order av erbjudandenamn. Linjediagrammet visar Tillväxttrend för procent ritas på en z-axel.
- Du kan använda skjutreglaget överst i diagrammet Rulla höger och vänster längs x-axeln och fokusera på specifika datapunkter.
- Du kan visa trend diagrammet genom att välja ett specifikt objekt i förklaringen.
- Du kan också välja att visa trender och data för **har avbrutits orders**. Diagrammet kommer att fungera på samma sätt som aktiva order.

![Partner Center analysera aktiva order](./media/analyze-active-orders.png)

### <a name="orders-by-marketplace-license-type"></a>Order per marketplace licenstyp
<!-- Section needs review and clarification!  -->
Den **order per marketplace licenstyp** diagrammet visar antalet per månad ordning baserat på licenstyp och fakturera i marketplace-erbjudandet. Licenstyper är:

- **Fakturerade via Azure**: Microsoft debiterar kunder för din räkning när du väljer att [sälja ditt erbjudande via Microsoft](./create-new-saas-offer.md#sell-through-microsoft) med en sådan licens. Betalningstyper är betala per användning via kreditkort eller Enterprise fakturering.
- **Använd din egen licens**: Microsoft debiterar inte kunder för deras användning av den här typen av marketplace-erbjudandet. Visas som **[blir nu (kostnadsfritt)](./create-new-saas-offer.md#get-it-now-free)** i marketplace.
- **Kostnadsfria**: Microsoft debiterar inte kunder för deras användning av den här typen av marketplace-erbjudandet. Visas som **[kostnadsfri utvärderingsversion](./create-new-saas-offer.md#free-trial)** i marketplace.
- **Microsoft som återförsäljare**: Representerar erbjudanden som säljs av Microsoft-återförsäljare som en del av den  **[Cloud Solution Provider (CSP) program](./create-new-saas-offer.md#csp-program-opt-in)** .

![Partner Center analysera order per licenstyp](./media/analyze-license-type.png)

### <a name="orders-by-customer-type"></a>Order per kundtyp

Den **order per kundtyp** liggande diagram visar antalet order fördelade mellan **nya kunder** och **befintliga kunder**. 

- En **ny kund** har fått en eller flera av dina erbjudanden för första gången inom samma kalendermånad (y-axeln). En **befintliga kund** tidigare skaffat ett erbjudande från dig innan per kalendermånad rapporterade (på y-axeln). 
- En ytterligare cirkeldiagram representerar alla order som skapas av ny eller befintlig kunden för det valda datumintervallet.
- I båda diagrammen kan du välja att visa endast nya eller bara befintliga kunder genom att välja respektive förklaringen.

![Partner Center analysera order per kundtyp](./media/analyze-order-by-customer.png)

### <a name="order-details-table"></a>Information om sorteringstabellen

Den **ordning informationstabellen** visar en numrerad lista över 1000 översta beställningar sorteras efter datumet för inköp.

- Varje kolumn i rutnätet är kan sorteras.
- Du kan extrahera data till en TSV-fil om antalet poster är mindre än 1 000.
- Om antalet poster 1000, exporteras placeras data asynkront i en hämtningssidan för de nästa 30 dagarna.
- Filter kan tillämpas på den **ordning informationstabellen** att visa endast de data som du är intresserad av. Data kan filtreras efter land, Azure licenstyp, licenstyp för Marketplace, typ av erbjudande, beställningsstatus, kostnadsfria spår, Marketplace-ID, kund-ID och företag prenumerationsnamn.

![Partner Center analysera beställningsinformation](./media/analyze-order-details.png)

## <a name="customer-dashboard"></a>Kund-instrumentpanel

Den **kunden** instrumentpanelen för den **analysera** menyn visar data för kunder som har hämtat dina erbjudanden. Du kan visa grafiska representationer av:

- [Kunden summor](#customer-totals)
- [Kunder efter geografi](#customers-by-geography)
- [Kundernas trender](#customer-trends)
- [Kunder med order](#customers-by-orders)
- [Information om kundtabell](#customer-details-table)

### <a name="customer-totals"></a>Kunden summor

Den **kunden summor** avsnittet visas en uppräkning av alla kunder, inklusive nya, befintliga och churned, under den valda [datumintervall](#date-range).

- Kunder procentandelen tillväxten jämfört med föregående månad visas med antalet och uppåtgående indikatorn i gröna eller nedåtgående indikatorn i rött.
- Tillväxt trender representeras av diagram och visar värdet för varje månad genom att hovra över kolumner i diagrammet.

#### <a name="customer-types"></a>Kundtyper

Det finns tre kundtyper: nya, befintliga och churned. 

- En ny kund har fått en eller flera av dina erbjudanden för första gången inom den valda månaden.
- En kund har fått en eller flera av dina erbjudanden innan den månad som valts.
- En churned kund har avbrutit alla erbjudanden som redan har köpt.

### <a name="customer-trends"></a>Kundernas trender

Den **kundernas trender** diagrammet visar en uppräkning av alla kunder, inklusive nya, befintliga och churned med en Tillväxttrend för per månad.

- Linjediagrammet representerar de övergripande kunden tillväxt procenttal. 
- Kolumnen månad representerar antalet kunder liggande av nya, befintliga och churned kunder.
- Antalet churned kunden visas på Y-axeln negativ riktning.
- Du kan välja specifika förklaring objekt som ska visas mer detaljerade vyer. Välj till exempel nya kunder i förklaringen att endast visa nya kunder.
- Du kan använda skjutreglaget överst i diagrammet Rulla höger och vänster på x-axeln och fokusera på specifika datapunkter och visa i detalj.
- Hovra över en kolumn i diagrammet visas information för endast den månaden.

![Partner Center analysera kundernas trender](./media/analyze-customer-trends.png)

### <a name="customers-by-orders"></a>Kunder med order

Den **kunder med order** diagram visar den **upp kund: e percentilen** längs x-axeln, i enlighet med deras antalet beställningar. Y-axeln visar kundens orderantal. Z-axeln (linjediagram) visar den ackumulerade procenten av det totala antalet beställningar. Du kan visa information genom att hovra över punkter längs linjediagrammet.

Exempelvis kan du lära dig från den här kundinformation ordning som de översta 30% av dina kunder bidrar till 83% order, lika med 2,130 order.

![Partner Center analysera kundorder](./media/analyze-customer-orders.png)

### <a name="customer-details-table"></a>Information om kundtabell

Den **information kundtabell** visar en numrerad lista över de översta 1000 kunder sorterade efter det datum som de förvärvade en av dina erbjudanden.

- Kunders personliga information är endast tillgängligt om kunden har gett ditt medgivande. Du kan bara visa den här informationen om du har loggat in med ett **ägare** rollen behörighetsnivå. [Mer information om roller och behörigheter](./manage-account.md#define-user-roles-and-permissions).
- Varje kolumn i rutnätet är kan sorteras.
- Du kan extrahera data till en TSV-fil om antalet poster är mindre än 1 000.
- Om antalet poster 1000, exporteras placeras data asynkront i en hämtningssidan för de nästa 30 dagarna.
- Filter kan tillämpas på tabellen för att visa endast de data som du är intresserad av. Data kan vara filtreras efter företagets namn, kund-ID, Marketplace prenumerations-ID, licenstyp för Azure, Date förvärvat, datum tappas bort, kundernas e-, kund land/tillstånd/stad/Zip, kunden språk, osv.

![Partner Center analysera kundinformation](./media/analyze-customer-table.png)

## <a name="downloads-dashboard"></a>Hämtar instrumentpanel

Den **hämtar** instrumentpanelen för den **analysera** menyn visar begäranden för alla nedladdningar som innehåller över 1 000 rader med data för kunden eller ordning.

Du får ett popup-meddelande och e-postmeddelande med en länk till den **hämtar** instrumentpanelen när du försöker hämta en fil med över 1 000 rader med data. Hämtningar data blir tillgängliga för en period på 30 dagar och tas sedan bort.

![Partner Center analysera nedladdningar](./media/analyze-downloads.png)

### <a name="dictionary-of-data-terms"></a>Data termordlista

| Attributnamn | Rapporter | Definition|
|---|---|---|
| Azure licenstyp | Kund-, Order | Typ av licensavtal som används av kunder för att köpa Azure. Även känt som kanal |
| Azure licenstyp: Cloud Solution Provider | Kund-, Order | Slutkunden procures Azure och ditt Marketplace-erbjudande via sin Molnlösningsleverantör som fungerar som en återförsäljare.|
| Azure licenstyp: Företag | Kund-, Order | Slutkunden procures Azure och din Marketplace-erbjudande via ett Enterprise Agreement, signerade direkt med Microsoft.|
| Azure licenstyp: Enterprise via återförsäljare  | Kund-, Order | Slutkunden procures Azure och ditt Marketplace-erbjudande genom en återförsäljare som underlättar Enterprise-avtal med Microsoft.|  |
| Azure licenstyp: Betala| Kund-, Order | Slutkunden procures Azure och din Marketplace-erbjudande via en ”betala”-avtal som undertecknas direkt med Microsoft.||
| Instansnamn för molnet| Beställa| Microsoft Cloud som en VM-distribution inträffade.||
| Cloud-instansnamn: Azure Global| Beställa| Offentliga global Microsoft-molnet.|| |
| Cloud-instansnamn: Azure Government | Beställa| Myndighetsspecifika Microsoft-molnet för en av följande myndigheter: Kina, Tyskland eller i USA.| |
| Kunden stad| Kunden| Ortnamn som tillhandahålls av kunden. Stad kan skilja sig från ort i en kunds Azure-prenumeration.||
| Kunden Kommunikationsspråk  | Kunden| Det språk som föredras av kunden för kommunikation.||
| Kundföretaget namn | Kund-, Order | Företagsnamnet som tillhandahålls av kunden. Namnet kan vara annorlunda än ort i en kunds Azure-prenumeration.|  |
| Kundens land | Kund-, Order | Landsnamn som tillhandahålls av kunden. Land kan skilja sig från landet i en kunds Azure-prenumeration.|  |
| Kunden e-post| Kunden| E-postadress som tillhandahålls av slutkunden. E-post kan skilja sig från den e-postadressen i en kunds Azure-prenumeration.||
| Kundens förnamn| Kunden| Namnet som tillhandahålls av kunden. Namnet kan vara annorlunda än det namn som i en kunds Azure-prenumeration.| |
| Kund-ID | Kund-, Order | Den unika identifieraren som tilldelats till en kund. En kund kan ha noll eller flera Azure Marketplace-prenumerationer.|  |
| Kunden postnummer  | Kunden| Postnummer som tillhandahålls av kunden. Koden kan vara annorlunda än postnumret i en kunds Azure-prenumeration.| |
| Kund-tillstånd| Kunden| Status (adress) tillhandahålls av kunden. Tillstånd kan skilja sig från det senaste namn i en kunds Azure-prenumeration.| |
| Inköpsdatum| Kunden| Det första datumet som kunden har köpt ett erbjudande som publicerats av dig.| |
| Datum förlorat| Kunden| Det sista datumet kunden har avbrutits sist för alla erbjudanden som redan har köpt.||
| Är ny kund  | Beställa| Värdet identifierar en ny kund som hämtar en eller flera av dina erbjudanden för första gången (eller inte). Värdet ska vara ”Ja” om inom samma kalendermånad för ”Date förvärvat”. Värdet kommer att ”Nej” om kunden har köpt någon av dina erbjudanden innan rapporteras per kalendermånad. |
| Är förhandsversionen av SKU| Beställa| Värdet meddelar dig om du har taggat SKU: N som ”förhandsversion”. Värdet ska vara ”Ja” om SKU: N har taggats i enlighet med detta och endast Azure-prenumerationer som du har godkänt kan distribuera och använda den här avbildningen. Värdet kommer att ”Nej” om SKU: N inte har identifierats som ”förhandsversion”.  |
| Priset kontakta är anmäla| Kunden| Värdet meddelar dig om kunden proaktivt har valt för erbjudanden kontakt från utgivare. Just nu, vi inte förmedla alternativet till kunder, så vi har angett ”Nej” för tavlan. När den här funktionen har distribuerats, börjar vi uppdaterar därefter.|
| Marketplace-licenstyp| Beställa| Fakturera i Marketplace-erbjudandet.||
| Marketplace-licenstyp: Fakturerade via Azure| Beställa| Microsoft är ditt ombud för det här erbjudandet i Marketplace och fakturerar kunder för din räkning. (PAYG kreditkort eller faktura Enterprise)||
| Marketplace-licenstyp: Använd din egen licens | Beställa| Den virtuella datorn kräver en licensnyckel som tillhandahålls av kunden att distribuera. Microsoft debiterar inte kunder för att lista över erbjudanden på så vis via marketplace.||
| Marketplace-licenstyp: Lediga| Beställa| Erbjudandet är konfigurerad för att vara kostnadsfritt för alla användare. Microsoft debiterar inte kunder för deras användning av erbjudandet.||
| Marketplace-licenstyp: Microsoft som din återförsäljare  | Beställa| Microsoft är din återförsäljare för det här erbjudandet i Marketplace.|  |
| Marketplace prenumerations-ID | Kund-, Order | Den unika identifieraren som är associerade med Azure-prenumeration kunden använde för att köpa Marketplace-erbjudande. ID var tidigare GUID för Azure-prenumeration.||
| Erbjudandets namn  | Beställa| Namnet på Marketplace-erbjudande.|| |
| Erbjudandetyp  | Beställa| Typ av Microsoft Marketplace-erbjudande.|||
| Typ av erbjudande: Hanterat program  | Ordning, | Använda Azure-appen: hanterade erbjudande apptyp när följande villkor krävs: Du distribuerar antingen en prenumeration-baserad lösning för din kund som använder en virtuell dator eller en hel IaaS-baserad lösning. Du eller dina kunder kräver att lösningen hanteras av en partner. |
| Typ av erbjudande: Azure Application| Ordning, | Använd Azure-program lösning malltyp erbjudandet när din lösning kräver ytterligare distribution och konfiguration av automation utöver en enkel virtuell dator.||
| Typ av erbjudande: Konsulttjänst| Beställa| Konsulttjänster på Azure Marketplace hjälpa till att ansluta kunder med stöd för och utöka deras användning av Azure-tjänster.| |
| Typ av erbjudande: Container | Beställa| Använd typ av erbjudande behållaren när din lösning är en Docker-behållaravbildning som tillhandahålls som en Kubernetes-baserade Azure-behållartjänst.||
| Typ av erbjudande: Dynamics 365 Business Central| Beställa| Använd den här erbjudandetypen när lösningen är integrerad med Dynamics 365 för finans och åtgärder| |
| Typ av erbjudande: Dynamics 365 för Customer Engagement | Beställa| Använd den här erbjudandetypen när lösningen är integrerad med Dynamics 365 för kundengagemang.||
| Typ av erbjudande: IoT Edge-modul | Beställa| Azure IoT Edge-moduler är de minsta beräkning enheter som hanteras av IoT Edge och kan innehålla Microsoft-tjänster (till exempel Azure Stream Analytics), tjänster 3 part eller din egen Lösningsspecifika kod. |
| Typ av erbjudande: Power BI-program | Beställa| Använd typ av erbjudande för Power BI-program när du distribuerar ett program som är integrerad med Power BI.|  |
| Typ av erbjudande: SaaS-program| Beställa| Aktivera kunden att köpa din SaaS-baserade, teknisk lösning som en prenumeration med hjälp av erbjudandetypen för SaaS-app.||
| Typ av erbjudande: Virtuell dator | Beställa| Använd typ av erbjudande för virtuell dator när du distribuerar en virtuell installation till den prenumeration som är associerad med kunden.||
| Typ av erbjudande: Visual Studio Marketplace-tillägg  | Beställa| Erbjudandetyp tidigare var tillgängliga för utvecklare för Azure DevOps-tillägget. Gå framåt Azure DevOps sälja tillägget utvecklare sina tillägget direkt till kunder. Tillägget erbjudanden kan konfigureras som Betal eller inklusive en utvärderingsversion. |
| Avbryt beställningsdatum| Beställa| Det datum som Marketplace-order avbröts.||
| Beställnings-ID| Beställa| Den unika identifieraren för kund för din Marketplace-tjänst. Användningsbaserade erbjudanden för virtuell dator är inte kopplade till en order.| |
| Köp beställningsdatum| Beställa| Det datum då Marketplace-Order skapades.|||
| Beställningsstatus| Beställa| Status för en Marketplace-order när data uppdaterades senast.|     |
| Beställningsstatus: Aktiv  | Beställa| Kunden har köpt en order och inte har avbrutit deras inbördes ordning.|         |
| Orderstatus: har avbrutits | Beställa| Kunden redan har köpt en order och därefter har avbrutits deras inbördes ordning.||
| Leverantör e-post| Kunden| E-postadress som ingår i relationen mellan Microsoft och slutkunden providern. Om kunden är ett företag via din återförsäljare, kommer detta att återförsäljaren. Om en Cloud Solution Provider (CSP) ingår, kommer detta att CSP: N.|
| Providernamn| Kunden| Namnet på providern som ingår i relationen mellan Microsoft och end-kund. Om kunden är ett företag via din återförsäljare, kommer detta att återförsäljaren. Om en Cloud Solution Provider (CSP) ingår, kommer detta att CSP: N.|
| SKU| Beställa| SKU-namnet som definierats under publiceringen. Ett erbjudande kan ha många SKU: er, men en SKU kan bara associeras med ett erbjudande.||
| Slutdatum för utvärdering| Beställa| Datum utvärderingsperioden för den här ordningen avslutas eller har avslutats.||