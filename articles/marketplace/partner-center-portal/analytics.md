---
title: Analys för den kommersiella marknads platsen i Partner Center
description: Lär dig hur du får åtkomst till analys rapporter för att övervaka försäljning, utvärdera prestanda och optimera dina Marketplace-erbjudanden.
author: ChJenk
manager: evansma
ms.author: v-chjen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/14/2019
ms.openlocfilehash: 2b1d3167828f2c2c49bb22580222fbd0e54d31d8
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73813196"
---
# <a name="analytics-for-the-commercial-marketplace-in-partner-center"></a>Analys för den kommersiella marknads platsen i Partner Center

Lär dig hur du får åtkomst till analys rapporter i Microsoft Partner Center för att övervaka försäljning, utvärdera prestanda och optimera dina erbjudanden på Marketplace. Som partner kan du övervaka dina erbjudande listor med hjälp av diagrammet data visualisering och insikter som stöds av Partner Center och hitta sätt att maximera din försäljning. Med de förbättrade analys verktygen kan du arbeta med prestanda resultat och upprätthålla bättre relationer med dina kunder och åter försäljare. 

Om du vill komma åt Partner Center Analytics-verktyg öppnar du **[analys](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** instrument panelen under kommersiell marknads plats.

|**Instrumentpanel**|**Visat innehåll**|
|:---|:---|
|[Sammanfattning](#summary-dashboard)|Diagram, trender och värden för sammanställda data som sammanfattar Marketplace-aktivitet för dina erbjudanden|
|[Order](#orders-dashboard)|Information om dina beställningar i ett grafiskt och nedladdnings Bart format|
|[Kunden](#customer-dashboard)|Information om dina kunder, inklusive tillväxt trender, som presenteras i ett grafiskt och nedladdnings Bart format|
|[–](#downloads-dashboard)|En lista över dina nedladdnings begär Anden under de senaste 30 dagarna|
|[Klassificeringar och recensioner](#ratings-and-reviews)| Information om klassificering och recensioner som kunderna lämnar för de erbjudanden som de har köpt.

## <a name="summary-dashboard"></a>Sammanfattnings instrument panel

På instrument panelen **Sammanfattning** presenteras en översikt baserad på varje erbjudande typ. **Insikter** visar viktig information snabbt och ger en bred överblick över Sälj aktiviteten för dina erbjudanden. Du kan visualisera dessa rapporter med **sammanfattnings** instrument panelen:

- [Totalt antal beställningar](#totals)
- [Totalt antal kunder](#totals)
- [Geografisk plats för dina kunder](#customers-by-geography)
- [Trender som är nya baserat på din kund och beställnings information](#growth-trend-chart)
- [Kund ranknings lista med högst order](#customer-leaderboard)
- [Antal beställningar sorterade efter namn på erbjudande](#offers-by-orders)

### <a name="date-range"></a>Datumintervall

Du kan hitta ett datum intervall i det övre högra hörnet på varje sida. Utdata från **sammanfattnings** sidans diagram kan anpassas genom att välja ett datum intervall baserat på de senaste 3, 6 eller 12 månaderna, eller genom att välja ett anpassat datum intervall med en maximal varaktighet på 12 månader. Standard datum intervallet är sex månader.

![Partner Center analys instrument panel](./media/analyze-dashboard.png)

### <a name="totals"></a>Summor

Avsnittet **summor** visar antalet order som skapats, eller kunder som har köpts, under det valda datum intervallet. 

- Procentvärdet bredvid **Totalt antal beställningar** och **Totalt antal kunder** representerar tillväxten jämfört med föregående månad. 
- En grön triangel som pekar uppåt anger en positiv tillväxt trend. En röd triangel som pekar nedåt visar en negativ tillväxt trend i förhållande till föregående månad. 
- Trender för order och kund tillväxt representeras av Stolp diagram och visar värdet för varje månad genom att hovra över kolumnerna i diagrammet.

![Total summor för partner Center](./media/analyze-totals.png)

### <a name="customers-by-geography"></a>Kunder efter geografi

Termisk karta **kunder per geografi** visar ett kund antal på en världs karta.

- Du kan flytta kartan för att visa den exakta platsen.
- Du kan zooma in på en bestämd plats.
- Termisk karta har ett extra rutnät för att visa information om antalet kunder (eller [antalet order](#orders-by-geography)) på den specifika platsen.
- Du kan söka efter och välja ett land i rutnätet för att zooma till platsen i kartan. Återgå till den ursprungliga vyn genom att trycka på knappen **Start** på kartan.
- En **ny** kund har köpt ett av dina erbjudanden för första gången under månaden inom det valda datum intervallet.

![Partner Center, analysera kund geografi](./media/analyze-customer-geography.png)

### <a name="growth-trend-chart"></a>Diagram över tillväxt trend

Du kan visa trender baserat på tillväxten av dina **beställningar som skapats** eller kunder som har **köpts**, med månads Visa månad enligt det valda datum intervallet. Du kan analysera trender ytterligare genom att välja länkar under diagrammet, som går till respektive **order** -eller **kund** sidor.

![Partner Center analys av tillväxt trender](./media/analyze-growth-trends.png)

### <a name="customer-leaderboard"></a>Kund ranknings lista

De 50 främsta kunderna med högst antal beställningar visas på en *ledare*, sorterat efter högsta antal beställningar och order procent. 

- Välj en kund om du vill visa profil information, beställningar som organiserats efter erbjudande eller beställningar som organiseras av Azures licens typ och prissättnings kanal. 
- Ring diagrammet för **erbjudanden per order** innehåller de fyra främsta erbjudandena (per antal order) och återstående erbjudanden grupperade i som "rest all".

> [!NOTE]
> Kundens personliga information visas bara om kunden har gett sitt medgivande. Du kan visa den här informationen om du har loggat in med behörighets nivån för **ägar** rollen. Användare med rollen **deltagare** kommer inte att kunna visa den här informationen. [Läs mer om användar roller och behörigheter](./manage-account.md#define-user-roles-and-permissions).

![Partner Center analys av tillväxt trender](./media/analyze-growth-trends.png)

### <a name="offers-by-orders"></a>Erbjudanden per order

Diagrammet **erbjudanden per order** ordnar dina aktiva beställningar enligt namnet på erbjudandet. 

- Du kan dra över segment från det vänstra ring diagrammet till höger ring diagram för att visa mer information om erbjudandet som du har dragit. Med de här två diagrammen kan du jämföra ett speciellt erbjudande med prestanda för alla andra erbjudanden ("rest all"). 

![Partner Center, analysera erbjudanden per order](./media/analyze-offer-by-order.png)

## <a name="orders-dashboard"></a>Order instrument panel

På instrument panelen för **order** i menyn **analysera** visas aktuella beställningar för alla dina SaaS-erbjudanden. Du kan visa grafiska representationer av:

- [Order summor](#order-totals)
- [Beställningar efter geografi](#orders-by-geography)
- [Trender för aktiva och annullerade beställningar](#trends-for-active-and-canceled-orders)
- [Beställningar ordnade efter Marketplace-licens typ](#orders-by-marketplace-license-type)
- [Beställningar som organiseras av nya och befintliga kunder](#orders-by-customer-type)
- [Order detalj tabell](#order-details-table)

> [!NOTE]
> Det finns skillnader mellan hur analys rapporter visas i Cloud Partner Portal (CPP) och det nya kommersiella Marketplace-programmet i Partner Center. Ett särskilt sätt är att **säljar insikter** i cpp har en **order & fliken användning** , som visar data för användnings erbjudanden och icke-användnings erbjudanden. Sidan **beställningar** i Partner Center har en separat flik för SaaS-erbjudanden.

### <a name="order-totals"></a>Order summor

I avsnittet **order summor** visas antalet order som skapats, inklusive både **aktiva** och **annullerade** order, under det valda [datum intervallet](#date-range). 

- Procentvärdet bredvid **Totalt antal beställningar** representerar tillväxten jämfört med föregående månad. 
- En grön triangel som pekar uppåt anger en positiv tillväxt trend. En röd triangel som pekar nedåt visar en negativ tillväxt trend i förhållande till föregående månad. 
- Tillväxt trender representeras av Stolp diagram och visar värdet för varje månad genom att hovra över kolumnerna i diagrammet.

### <a name="orders-by-geography"></a>Beställningar efter geografi

Termisk karta för **beställningar per geografi** visar antalet beställningar på en världs karta och fungerar på samma sätt som **[kunderna efter geografi termisk karta](#customers-by-geography)** .

### <a name="trends-for-active-and-canceled-orders"></a>Trender för aktiva och annullerade beställningar

Med ring diagram över **aktiva beställningar per** erbjudande kan du ordna alla dina aktiva beställningar enligt deras namn.

- De fyra främsta erbjudandena visas i diagrammet och resten av erbjudandena grupperas som "rest all".
- Du kan välja vissa erbjudanden i förklaringen om du bara vill visa de erbjudanden som finns i diagrammet. 
- Om du hovrar över en sektor i diagrammet visas antalet order och procent andel av erbjudandet jämfört med det totala antalet beställningar i alla erbjudanden.
- **I order by** -trender visas trender i månads tillväxt. Kolumnen månad representerar antalet order efter namnet på erbjudandet. Linje diagrammet visar trenden för tillväxt i procent som ritats på en z-axel.
- Du kan använda skjutreglaget överst i diagrammet för att rulla åt höger och vänster längs x-axeln och fokusera på vissa data punkter.
- Du kan visa trend diagrammet genom att välja ett speciellt objekt i förklaringen.
- Du kan också välja att visa trender och data för **annullerade order**. Grafen fungerar på samma sätt som aktiva beställningar.

![Partner Center analysera aktiva beställningar](./media/analyze-active-orders.png)

### <a name="orders-by-marketplace-license-type"></a>Order efter Marketplace-licens typ
<!-- Section needs review and clarification!  -->
I diagrammet **beställningar efter Marketplace licens typ** visas månads Visa antal per månad baserat på licens typen och fakturerings metoden för Marketplace-erbjudandet. Licens typer är:

- **Faktureras via Azure: Microsoft fakturerar**kunder åt dig när du väljer att [sälja ditt erbjudande via Microsoft](./create-new-saas-offer.md#sell-through-microsoft) med den här licens typen. Betalnings typerna omfattar betala per användning via kredit kort eller företags fakturering.
- **Bring Your Own License**: Microsoft debiterar inte kunder för deras användning av den här typen av Marketplace-erbjudande. Anges som **[Hämta nu (kostnads fritt)](./create-new-saas-offer.md#get-it-now-free)** i Marketplace.
- **Kostnads fritt**: Microsoft debiterar inte kunder för deras användning av den här typen av Marketplace-erbjudande. Visas som **[kostnads fri utvärderings version](./create-new-saas-offer.md##free-trial-listing)** i Marketplace.
- **Microsoft as åter försäljare**: representerar erbjudanden som säljs av Microsoft-återförsäljare som en del av **[CSP-programmet (Cloud Solution Provider)](./create-new-saas-offer.md#csp-program-opt-in)** .

![Partner Center, analysera beställningar efter licens typ](./media/analyze-license-type.png)

### <a name="orders-by-customer-type"></a>Beställningar efter kundtyp

I diagrammet **order efter kund typ** stapeldiagram visas antalet beställningar som delas mellan **nya kunder** och **befintliga kunder**. 

- En **ny kund** har skaffat ett eller flera av dina erbjudanden för första gången inom samma kalender månad (y-axeln). En **befintlig kund** har tidigare förvärvat ett erbjudande från dig före den kalender månad som rapporter ATS (på y-axeln). 
- Ett ytterligare cirkel diagram representerar alla beställningar som skapats av den nya eller befintliga kunden för det valda datum intervallet.
- I båda diagrammen kan du välja att bara visa nya eller bara befintliga kunder genom att välja respektive förklaring.

![Partner Center analyserar beställningar efter kundtyp](./media/analyze-order-by-customer.png)

### <a name="order-details-table"></a>Order detalj tabell

**Tabellen Order information** visar en numrerad lista över de 1000 främsta order som sorteras efter förvärvs datum.

- Varje kolumn i rutnätet är sorterbar.
- Data kan extraheras till en TSV-fil om antalet poster är mindre än 1000.
- Om poster nummer över 1000 placeras exporterade data asynkront på sidan nedladdningar under de närmaste 30 dagarna.
- Filter kan tillämpas på **tabellen Order information** för att visa endast de data som du är intresse rad av. Data kan filtreras efter land, Azure-licensserver, Marketplace-licens typ, erbjudande typ, order status, kostnads fria historik, prenumerations-ID för Marketplace, kund-ID och företags namn.

![Information om hur du analyserar Partner Center](./media/analyze-order-details.png)

## <a name="customer-dashboard"></a>Kund instrument panel

På **instrument panelen för kunder i** **analys** menyn visas data för kunder som har köpt dina erbjudanden. Du kan visa grafiska representationer av:

- [Kund summor](#customer-totals)
- [Kunder efter geografi](#customers-by-geography)
- [Kund trender](#customer-trends)
- [Kunder per order](#customers-by-orders)
- [Tabellen Kund information](#customer-details-table)

### <a name="customer-totals"></a>Kund summor

I avsnittet **kund summor** visas antalet kunder, inklusive nya, befintliga och omsättnings bara under det valda [datum intervallet](#date-range).

- Kundernas procentuella tillväxt jämfört med föregående månad anges av talet och uppåt i den gröna eller nedåtriktade indikatorn i rött.
- Tillväxt trender representeras av Stolp diagram och visar värdet för varje månad genom att hovra över kolumnerna i diagrammet.

#### <a name="customer-types"></a>Kund typer

Det finns tre kund typer: nya, befintliga och uppdelade. 

- En ny kund har skaffat ett eller flera av dina erbjudanden för första gången inom den valda månaden.
- En befintlig kund har köpt ett eller flera av dina erbjudanden före den månad som valts.
- En upprättad kund har avbrutit alla erbjudanden som tidigare har köpts.

### <a name="customer-trends"></a>Kund trender

I tabellen med **kund trender** visas antalet kunder, inklusive nya, befintliga och uppkopplade, med en tillväxt trend per månad.

- Linje diagrammet representerar de övergripande procent andelen av kundens tillväxt. 
- Kolumnen månad representerar antalet kunder som är staplade efter nya, befintliga och uppdelade kunder.
- Antalet uppräknade kunder visas i Y-axelns negativa riktning.
- Du kan välja särskilda förklarings objekt för att visa mer detaljerade vyer. Välj till exempel nya kunder från förklaringen för att endast visa nya kunder.
- Du kan använda skjutreglaget överst i diagrammet för att rulla åt höger och vänster på x-axeln och fokusera på vissa data punkter för att visa mer information.
- Om du hovrar över en kolumn i diagrammet visas endast information för den månaden.

![Partner Center analysera kund trender](./media/analyze-customer-trends.png)

### <a name="customers-by-orders"></a>Kunder per order

I diagrammet **kunder per order** visas den **främsta kund percentilen** längs x-axeln, vilket bestäms av antalet beställningar. Y-axeln visar kundens order antal. Z-axeln (linje diagrammet) visar den ackumulerade procent andelen av det totala antalet beställningar. Du kan visa information genom att hovra över punkter längs linje diagrammet.

Som exempel kan du lära dig från de här kund order data som de översta 30% av kunderna bidrar till 83% av orderna, lika med 2 130 order.

![Partner Center analysera kund beställningar](./media/analyze-customer-orders.png)

### <a name="customer-details-table"></a>Tabellen Kund information

**Tabellen Kund information** visar en numrerad lista över de 1000 främsta kunderna sorterat efter det datum då de först köpte ett av dina erbjudanden.

- Kundens personliga information kommer bara att vara tillgänglig om kunden har gett sitt medgivande. Du kan bara se den här informationen om du har loggat in med behörighets nivån **ägare** av rollen. [Läs mer om användar roller och behörigheter](./manage-account.md#define-user-roles-and-permissions).
- Varje kolumn i rutnätet är sorterbar.
- Data kan extraheras till en TSV-fil om antalet poster är mindre än 1000.
- Om poster nummer över 1000 placeras exporterade data asynkront på sidan nedladdningar under de närmaste 30 dagarna.
- Filter kan tillämpas på tabellen för att visa endast de data som du är intresse rad av. Data kan filtreras efter företags namn, kund-ID, marknads plats prenumerations-ID, typ av Azure-licens, datum för inhämtat, datum förlorat, kund-e-post, kund land/delstat/ort/post, kund språk osv.

![Partner Center, analysera kund information](./media/analyze-customer-table.png)

## <a name="downloads-dashboard"></a>Hämta instrument panel

Instrument panelen för **nedladdningar** i **analys** menyn visar begär Anden om hämtningar som innehåller över 1000 rader med kund-eller order data.

Du får ett popup-meddelande och ett e-postmeddelande som innehåller en länk till instrument panelen för **nedladdningar** när du begär en nedladdning med över 1000 rader med data. Dessa data hämtningar är tillgängliga under en 30-dagarsperiod och tas sedan bort.

![Hämtnings bara filer för partner Center](./media/analyze-downloads.png)

### <a name="dictionary-of-data-terms"></a>Ord lista med data termer

| Attributnamn | Rapporter | Definition|
|---|---|---|
| Typ av Azure-licens | Kund, order | Den typ av licens avtal som kunder använder för att köpa Azure. Kallas även för kanal |
| Azure-licens typ: Cloud Solution Provider | Kund, order | Slutanvändaren kan köpa Azure och ditt Marketplace-erbjudande via moln lösnings leverantören, som agerar som åter försäljare.|
| Azure-licens typ: Enterprise | Kund, order | Slutanvändaren kan köpa Azure och ditt Marketplace-erbjudande via en Enterprise-avtal, som loggas direkt med Microsoft.|
| Azure-licens typ: Enterprise genom åter försäljare  | Kund, order | Slutanvändaren säljer Azure och Marketplace-erbjudandet via en åter försäljare som fören klar sina Enterprise-avtal med Microsoft.|  |
| Azure-licens typ: betala per användning| Kund, order | Slutanvändaren debiterar Azure och Marketplace-erbjudandet genom ett "betala per användning"-avtal, undertecknat direkt med Microsoft.||
| Moln instans namn| Beställa| Den Microsoft Cloud där en VM-distribution ägdes.||
| Moln instans namn: Azure Global| Beställa| Det offentliga globala Microsoft-molnet.|| |
| Moln instans namn: Azure Government | Beställa| Myndighets Microsoft-moln för någon av följande myndigheter: Kina, Tyskland eller USA i Amerika.| |
| Kund ort| Kund| Orts namnet som tillhandahålls av kunden. Staden kan skilja sig från staden i en kunds Azure-prenumeration.||
| Kundens kommunikations språk  | Kund| Det språk som kunden föredrar för kommunikation.||
| Kund företags namn | Kund, order | Företags namnet som tillhandahålls av kunden. Namnet får inte vara detsamma som staden i en kunds Azure-prenumeration.|  |
| Kund land | Kund, order | Landets namn som tillhandahålls av kunden. Landet kan skilja sig från landet i en kunds Azure-prenumeration.|  |
| Kund-e-post| Kund| E-postadressen som tillhandahålls av slut kunden. E-postadressen kan skilja sig från e-postadressen i en kunds Azure-prenumeration.||
| Förnamn för kund| Kund| Det namn som tillhandahålls av kunden. Namnet får inte vara detsamma som det namn som anges i en kunds Azure-prenumeration.| |
| Kund-ID | Kund, order | Den unika identifierare som tilldelats till en kund. En kund kan ha noll eller flera prenumerationer på Azure Marketplace.|  |
| Kund post nummer  | Kund| Post numret från kunden. Koden får inte vara samma som post numret i en kunds Azure-prenumeration.| |
| Kund tillstånd| Kund| Den stat (adress) som tillhandahålls av kunden. Tillstånd kan skilja sig från det tillstånd som tillhandahölls i en kunds Azure-prenumeration.| |
| Inköps datum| Kund| Det första datumet då kunden köpte ett erbjudande som publicerats av dig.| |
| Datum förlorat| Kund| Det senaste datumet då kunden avbröt den sista av alla erbjudanden som tidigare har köpts.||
| Är ny kund  | Beställa| Värdet identifierar en ny kund som förvärvar ett eller flera av dina erbjudanden för första gången (eller inte). Värdet blir "Ja" om det är inom samma kalender månad för "inköps datum". Värdet blir "nej" om kunden har köpt något av dina erbjudanden före den kalender månad som rapporter ATS. |
| Är för hands version av SKU| Beställa| Värdet meddelar dig om du har taggat SKU som "Preview". Värdet är "Ja" om SKU: n har taggats enligt detta och bara Azure-prenumerationer som har auktoriserats av dig kan distribuera och använda den här avbildningen. Värdet är "nej" om SKU: n inte har identifierats som "för hands version".  |
| Är kampanj kontakt deltagande| Kund| Värdet gör att du vet om kunden proaktivt har valt reklam kontakt från utgivare. För tillfället visar vi inte alternativet för kunder, så vi har angett "nej" på tavlan. När den här funktionen har distribuerats kommer vi att börja uppdatera enligt detta.|
| Marketplace-licens typ| Beställa| Fakturerings metoden för Marketplace-erbjudandet.||
| Marketplace-licens typ: debiteras via Azure| Beställa| Microsoft är din agent för det här Marketplace-erbjudandet och fakturerar kunder åt dig. (Antingen PAYG kredit kort eller företags faktura)||
| Marketplace-licens typ: ta med din egen licens | Beställa| Den virtuella datorn kräver en licens nyckel från kunden som ska distribueras. Microsoft fakturerar inte kunder för att lista sina erbjudanden på det här sättet via Marketplace.||
| Marketplace-licens typ: kostnads fri| Beställa| Erbjudandet är konfigurerat att vara kostnads fritt för alla användare. Microsoft fakturerar inte kunder för användningen av det här erbjudandet.||
| Marketplace-licens typ: Microsoft as åter försäljare  | Beställa| Microsoft är din åter försäljare för det här Marketplace-erbjudandet.|  |
| Prenumerations-ID för Marketplace | Kund, order | Den unika identifierare som är kopplad till Azure-prenumerationen som kunden använde för att köpa Marketplace-erbjudandet. ID var tidigare GUID för Azure-prenumerationen.||
| Erbjudandets namn  | Beställa| Namnet på Marketplace-erbjudandet.|| |
| Erbjudandetyp  | Beställa| Typ av Microsoft Marketplace erbjudande.|||
| Erbjudande typ: hanterat program  | För | Använd Azure app: den hanterade appens erbjudande typ när följande villkor är uppfyllda: du distribuerar antingen en prenumerations baserad lösning för din kund med antingen en virtuell dator eller en hel IaaS lösning. Du eller din kund kräver att lösningen hanteras av en partner. |
| Erbjudande typ: Azure Application| För | Använd mallen för Azure Application lösnings erbjudande när din lösning kräver ytterligare distribution och konfigurations automatisering utöver en enkel virtuell dator.||
| Erbjudande typ: konsult tjänst| Beställa| Konsult tjänster i Azure Marketplace hjälper till att ansluta kunder med tjänster som stöder och utökar användningen av Azure.| |
| Erbjudande typ: container | Beställa| Använd containerns erbjudande typ när din lösning är en Docker-behållar avbildning som tillhandahålls som en Kubernetes-baserad Azure Container Service.||
| Erbjudande typ: Dynamics 365 Business Central| Beställa| Använd den här erbjudande typen när din lösning är integrerad med Dynamics 365 för finanser och åtgärder| |
| Erbjudande typ: Dynamics 365 för kund engagemang | Beställa| Använd den här erbjudande typen när din lösning är integrerad med Dynamics 365 för kund engagemang.||
| Erbjudande typ: IoT Edge modul | Beställa| Azure IoT Edge moduler är de minsta beräknings enheter som hanteras av IoT Edge och kan innehålla Microsoft-tjänster (till exempel Azure Stream Analytics), tjänster från tredje part eller din egen lösnings-specifika kod. |
| Erbjudande typ: Power BI program | Beställa| Använd typen Power BI program erbjudande när du distribuerar ett program som är integrerat med Power BI.|  |
| Erbjudande typ: SaaS-program| Beställa| Använd SaaS-appens erbjudande typ för att göra det möjligt för kunden att köpa din SaaS-baserade, tekniska lösning som en prenumeration.||
| Erbjudande typ: virtuell dator | Beställa| Använd den virtuella datorns erbjudande typ när du distribuerar en virtuell installation till prenumerationen som är kopplad till din kund.||
| Erbjudande typ: tillägg för Visual Studio Marketplace  | Beställa| Erbjudande typ som tidigare var tillgänglig för Azure DevOps Extension-utvecklare. Att gå vidare med Azure DevOps Extension-utvecklare kan sälja sitt tillägg direkt till kunderna. Tilläggs erbjudanden kan konfigureras som betalda eller inklusive en utvärderings version. |
| Annullera datum för beställning| Beställa| Datumet då Marketplace-ordern annullerades.||
| Order-ID| Beställa| Den unika identifieraren för kund ordern för din Marketplace-tjänst. Användnings erbjudanden för virtuella datorer är inte kopplade till någon order.| |
| Inköps datum för beställning| Beställa| Datumet då Marketplace-ordern skapades.|||
| Order status| Beställa| Status för en Marketplace-order vid den tidpunkt då data senast uppdaterades.|     |
| Order status: aktiv  | Beställa| Kunden har köpt en order och har inte annullerat sin beställning.|         |
| Order status: avbruten | Beställa| Kunden köpte tidigare en order och annullerade sedan sin beställning.||
| Providerns e-postadress| Kund| E-postadressen till leverantören som är involverad i relationen mellan Microsoft och slut kunden. Om kunden är ett företag genom åter försäljaren blir detta åter försäljaren. Om en moln lösnings leverantör (CSP) är involverad är detta CSP.|
| Providernamn| Kund| Namnet på leverantören som är involverad i relationen mellan Microsoft och slut kunden. Om kunden är ett företag genom åter försäljaren blir detta åter försäljaren. Om en moln lösnings leverantör (CSP) är involverad är detta CSP.|
| SKU| Beställa| SKU-namn som definieras under publiceringen. Ett erbjudande kan ha många SKU: er, men en SKU kan bara associeras med ett enda erbjudande.||
| Slutdatum för utvärdering| Beställa| Det datum då utvärderings perioden för den här ordern upphör eller har avslut ATS.||

## <a name="ratings-and-reviews"></a>Klassificeringar och recensioner

När kunder bläddrar, söker efter och köper erbjudanden på båda marknads platser kan de lämna betyg och recensioner om de erbjudanden de har köpt. De kan skicka in nya klassificeringar eller recensioner och uppdatera eller ta bort klassificeringar eller recensioner som redan har skickats. Granskningar publiceras i Azure Marketplace eller AppSource på fliken recensioner på sidan för erbjudandets produkt visning. Kunder kan inkludera sina namn för klassificeringar eller recensioner, eller så kan de publicera anonymt.  

> [!NOTE]
> Kunder kan bara göra ändringar i de klassificeringar och recensioner de äger.

På **instrument panelen klassificering & granska** visas trender och insikter om kund aktiviteter. Den ger grafiska representationer av:

- Klassificering & granskningar  
- Granska kommentarer

### <a name="marketplace-storefront-tabs"></a>Marketplace butik-flikar

Utgivare kan visa sina erbjudande mått separat på AppSource-& Azure Marketplace-flikarna. Om du vill visa vissa erbjudande mått väljer du **erbjudandet** i list rutan:

![List rutan för att analysera erbjudande i partner centret](./media/analyze-offer-dropdown.png)

### <a name="ratings-and-reviews-summary"></a>Sammanfattning av klassificering och recensioner

I sammanfattnings avsnittet klassificerings & recensioner visas måtten nedan för det valda datum intervallet:

- **Genomsnittligt omdöme:** Viktad genomsnittlig stjärn klassificering av alla klassificeringar som skickats av kunder för det valda erbjudandet.
- **Klassificering av klassificering:** Nedbrytning av stjärn klassificeringen med antalet kunder som har skickat betyg. Stapeldiagrammet staplas med faktiska och ändrade klassificeringar (uppdaterat betygs antal).
- **Total betyg:** Totalt antal inskickade klassificeringar. Det här antalet inkluderar även klassificeringar med och utan recensioner.
- **Klassificering med recensioner:** Antal granskningar som har skickats.

![Översikt över analys och granskning i Partner Center](./media/analyze-ratings-summary.png)

### <a name="review-comments"></a>Granska kommentarer

Granskningar visas i kronologisk ordning för när de publicerades. I standardvyn visas alla granskningar. Du kan filtrera igenom granskningarna efter stjärn klassificering med **filtret** i list menyn. Du kan också söka efter nyckelord som visas i granskningen.  

![Partner Center, analysera gransknings kommentarer](./media/analyze-reviews.png)

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

Det här avsnittet innehåller svar på vanliga frågor om **inga analyser som är tillgängliga än** meddelanden i Partner Center.

**Jag kan inte Visa mina analys data i Partner Center. När jag har åtkomst till dessa sidor visas meddelandet nedan. Varför är det här?**

![Inga data för dina erbjudanden än](./media/analytics-faq-no-data.png)

Varför du kan få det här meddelandet:

- Dina erbjudanden som anges i Marketplace är inte SaaS erbjudanden. För närvarande ger Partner Center rapporter för analys för SaaS-erbjudanden. Dynamics 365-erbjudanden kan publiceras i Partner Center, men analyser för dessa erbjudanden är inte aktiverade än i Partner Center.
- Det finns för närvarande inga förvärv för dina publicerade erbjudanden i Marketplace. Detta kan betyda att dina erbjudanden är Live i Marketplace och att du får vyer från kunder på produktens visnings sidor, men kunder som inte har vidtagit åtgärder för att köpa och distribuera dem.
- Publiceringen av ditt erbjudande kan vara i gång och är inte i drift än. Endast Live-erbjudanden kan förvärvas av kunder. För att kontrol lera statusen för dina erbjudanden, se Översikt i [analys instrument panelen](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary). Mer information finns i [översikts instrument panelen](#summary-dashboard) i den här artikeln.
- Dina erbjudanden kan visas som **kontakta mig**, som endast är List erbjudanden och inte kan köpas av kunder på Marketplace. Även om dessa erbjudanden genererar leads och delas med dig, skapas inga beställningar för dessa erbjudanden eftersom de inte kan köpas. Om du vill kontrol lera typen av erbjudande lista går du till installations sidan.

**Jag vet att jag har analys data, men meddelandet nedan visas:**

![Inga data för angivet datum intervall](./media/analytics-faq-data-range.png)

Om du får det här meddelandet betyder det att du har analys data, men det finns inga data för det datum intervall som du har valt. Välj ett annat datum intervall eller anpassat datum intervall om du vill visa data sedan 2010. Mer information finns i [datum intervall](#date-range).
