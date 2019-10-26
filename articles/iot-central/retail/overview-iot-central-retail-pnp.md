---
title: Skapa detalj handels lösningar med Azure IoT Central | Microsoft Docs
description: Lär dig att skapa en ansluten logistik, digital distributions Center, övervakning av hanterings villkor i butiken, utcheckning och smart inventering, detalj handels lösningar med Azure IoT Central med hjälp av programmallar.
author: KishorIoT
ms.author: nandab
ms.date: 10/22/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom:
- iot-p0-scenario
ms.openlocfilehash: 5db494b17b131e79bc84ccd6cfa2888bf3632e1d
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2019
ms.locfileid: "72957456"
---
# <a name="building-retail-solutions-with-azure-iot-central"></a>Skapa detalj handels lösningar med Azure IoT Central
Azure IoT Central är en Sakernas Internet-plattform (IoT) för lösnings byggare som fören klar utmaningarna med att skapa & hantera skalbara program. I den här artikeln kommer vi att markera flera återförsäljar-specifika programmallar inom IoT Central. Lösnings byggare kan dra nytta av publicerade mallar för att skapa IoT-lösningar för att optimera leverans kedjan, förbättra i Store-upplevelsen för kunder och spåra inventeringen mer effektivt.

> [!div class="mx-imgBorder"]
> Översikt över ![Azure IoT återförsäljarversion](./media/overview-iot-central-retail/retail-app-templates.png)


## <a name="what-is-connected-logistics-solution"></a>Vad är den anslutna logistik lösningen?
Globala logistik utgifter förväntas uppnå $10.6 TRN i 2020, den största BNP enligt bransch. Transport av varor är majoriteten (70%) av totala logistik utgifter. Leverantörer av leverans är under intensiv konkurrens kraftig belastning och begränsningar. 3PL-leverantörer hanterar föränderliga tids ramar och ökade kompensations kostnader. Logistik är ytterligare begränsad av riskerna med naturpolitiske, extrema klimat händelser och brottslighet. 

Med hjälp av IoT-sensorer kan vi samla in & övervakar omgivande förhållanden, t. ex. temperatur, fuktighet, lutning, stöt, ljus och leveransens plats via GPS i multi-modal omvandling, t. ex. luft, vatten, mark. Data som samlas in från sensorer, enheter, väder & händelser kan integreras med molnbaserad Business Intelligence-system. Fördelarna med den anslutna logistik lösningen är,
* Transference för leverans med real tids spårning & spårning 
* Leverans integritet med övervakning av omgivande förhållanden i real tid & kall kedja
* Säkerhet från stöld, förlust eller skada för leverans
* Geo-staket, väg optimering, flott hantering. Analys av fordon
* Prognostisera & förutsägbart med avgångs &s ankomst 

### <a name="out-of-box-experience"></a>Out of Box-upplevelse
Partner kan utnyttja mallen för att utveckla slut punkt för anslutna logistik lösningar & fördelarna med förmåner. Den här publicerade mallen fokuserar på enhets anslutning, konfiguration & hantering av enheter i IoT Central. 

> [!div class="mx-imgBorder"]
> ![ansluten logistik instrument panel](./media/overview-iot-central-retail/connected-logistics-dashboard1.png)

> [!div class="mx-imgBorder"]
> ![ansluten logistik instrument panel](./media/overview-iot-central-retail/connected-logistics-dashboard2.png)

Observera att instrument panelen ovan är en exempel upplevelse och du kan anpassa programmet fullständigt så att det passar ditt användnings fall.

Kom igång med [självstudier från slut punkt till slut punkt](./tutorial-iot-central-connected-logistics-pnp.md) som vägleder dig genom hur du skapar en lösning med hjälp av en av de anslutna logistik lösnings mallarna.



## <a name="what-is-digital-distribution-center-solution"></a>Vad är en lösning för digital distributions Center?
I takt med att fler tillverkare och åter försäljare upprättar världen över, har deras leverans kedjor förgrenade för att bli mer komplexa än någonsin tidigare. Distributions Center blir en primär utmaning. Distributions Center/lager platser känner av brunt för trycket från e-handel. Konsumenterna förväntar sig nu stora val av produkter som är tillgängliga och för att varorna ska komma in inom en till två dagars inköps dagar. Distributions Center måste anpassas till dessa trender samtidigt som du överkommer till befintliga ineffektiva. 

Idag innebär en förlitande av manuell arbete att plocka och packa konton för 55-65% av distributions Center kostnaderna. Även om det är dåligt att manuell arbete saktar ned distributions Center, kan du snabbt nå personal behoven (högtids personal går upp med 10X), vilket gör det ännu svårare att uppfylla frakt volymer. Den här säsongs variationen resulterar i hög omsättning och sannolikheten för fel och behovet av kostsamt återarbete ökar också.
Lösningar som baseras på IoT-aktiverade kameror kan leverera debildnings förmåner genom att aktivera en digital feedback-slinga. Den här inmatningen av data från distributions centret leder till användbara insikter som i sin tur leder till bättre data.

Fördelar är, 
* Kameror övervakar varor när de anländer och passerar genom transport system
* Identifiera felaktiga varor och skicka dem för reparation
* Håll koll på beställningar effektivt
* Sänk kostnaderna, förbättrad produktivitet & maximera användningen

### <a name="out-of-box-experience"></a>Out of Box-upplevelse
Partner kan använda den här program mal len för att bygga upp ett digitalt distributions Center för att få åtgärds bara insikter & ovan fördelade förmåner. Den publicerade mallen fokuserar på enhets anslutningens konfiguration & hantering av kamera-och gräns enheter i IoT Central. 

> [!div class="mx-imgBorder"]
> Instrument panel för ![Digital distributions Center](./media/overview-iot-central-retail/digital-distribution-center-dashboard.png)

Observera att instrument panelen ovan är en exempel upplevelse och du kan anpassa programmet fullständigt så att det passar ditt användnings fall.

Kom igång med [självstudier från slut punkt till slut punkt](./tutorial-iot-central-digital-distribution-center-pnp.md) som vägleder dig genom hur du skapar en lösning med hjälp av en av de digitala distributions Center mallarna.



## <a name="what-is-in-store-analytics-condition-monitoring"></a>Vad är övervakning av villkor i Store Analytics?
I dagens konkurrens krafts landskap söker åter försäljare efter nya sätt att erbjuda kunderna något unikt eller särskilt för att kunna driva trafik via sina fysiska butiker. Många åter försäljare bekräftar vikten av miljö förhållanden i butiken som ett verktyg för att skilja med sina konkurrenter. Åter försäljare vill se till att de upprätthåller Pleasant-villkor i sina butiker hela tiden för att få en bekväm upplevelse till kunderna.  

Programmallen för övervakning av villkor i butiken i IoT Central tillhandahåller Solution Builder med en arbets yta som kan användas för att skapa en lösning för slut punkt till slut punkt. Med program mal len kan du ansluta och övervaka en detalj handels miljö med en mängd olika sensor enheter. Dessa sensor enheter fångar meningsfulla signaler som kan konverteras till affärs insikter som gör det möjligt för åter försäljare att minska sina drifts kostnader och skapa upplevelser som kunderna älskar.

Med program mal len kan du:

*  Anslut smidigt en mängd IoT-sensorer till en IoT Central program instans.
*  Övervaka och hantera hälso tillståndet för sensor nätverket samt Gateway-enheter i miljön.
*  Skapa anpassade regler kring miljö villkoren i en butik för att utlösa lämpliga aviseringar.
*  Transformera miljö villkoren i din butik till insikter som kan utnyttjas av butiks teamet.
* Exportera de sammanställda insikterna till befintliga eller nya affärs program som ger detalj handels personal medlemmar.

### <a name="out-of-box-experience"></a>Out of Box-upplevelse
Program mal len levereras med en uppsättning enhetsspecifika mallar och en operatörs upplevelse i rutan. Den använder en uppsättning simulerade enheter som fyller instrument panels elementen. När du har distribuerat ett IoT Central-program med hjälp av appen [för övervakning av hälso tillstånd i butiken](https://aka.ms/conditiontemplate) , hamnar du på standard instrument panelen som visas nedan. 

> [!div class="mx-imgBorder"]
> ![övervakning av villkor i Store Analytics](./media/overview-iot-central-retail/in-store-analytics-condition-dashboard.png)

Observera att instrument panelen ovan är en exempel upplevelse och du kan anpassa programmet fullständigt så att det passar ditt användnings fall. 

Kom igång med [självstudier från slut punkt till slut punkt](./tutorial-in-store-analytics-create-app-pnp.md) som vägleder dig genom hur du skapar en lösning med hjälp av en av mallarna för övervakning av analys villkor i butiken.



## <a name="what-is-in-store-analytics-checkout"></a>Vad är en check i Store Analytics?
I allt större konkurrens kraftigare landskap är modern åter försäljare ständigt växande belastning för att leverera en insamlad upplevelse som överskrider kund förväntningarna och håller dem igång igen. Även om flera åter försäljare har börjat distribuera teknik för att uppfylla det här behovet, är ett utrymme som är i stort sett viktigt för dig.

Programmallen för BA-utcheckning i IoT Central gör det möjligt för lösnings byggare att skapa erfarenheter som ger detalj handels personal med meningsfulla insikter runt arbets området för butiken. Den använder en uppsättning simulerade enheter för att fastställa beläggnings status för var och en av de olika banden i en detalj handels butik. Sensorerna gör att du kan samla in antalet personer och den genomsnittliga vänte tiden för varje arbets band.

Mallen hjälper Solution Builder att påskynda sina go-to-Marketing-planer genom att tillhandahålla en bas linje IoT-lösning som gör att de kan: 

* Anslut smidigt en mängd IoT-sensorer till en IoT Central program instans.
* Övervaka och hantera hälso tillståndet för sensor nätverket samt Gateway-enheter i miljön.
* Skapa anpassade regler kring villkoret för utcheckning i en butik för att utlösa lämpliga aviseringar.
* Transformera betalnings villkoren i butiken till insikter som kan utnyttjas av butiks teamet.
* Exportera de sammanställda insikterna till befintliga eller nya affärs program som ger detalj handels personal medlemmar.

### <a name="out-of-box-experience"></a>Out of Box-upplevelse
Program mal len levereras med en uppsättning enhetsspecifika mallar och en operatörs upplevelse i rutan. Den använder en uppsättning simulerade enheter som fyller instrument panels elementen. När du har distribuerat ett IoT Central program med hjälp av mallen [för att checka in-Store Analytics](https://aka.ms/checkouttemplate) -mall kommer du att landa på standard instrument panelen som visas nedan. 

> [!div class="mx-imgBorder"]
> ![checkar in-Store Analytics](./media/overview-iot-central-retail/In-Store-Analytics-Checkout-Dashboard.png)

Observera att instrument panelen ovan är en exempel upplevelse och du kan anpassa programmet fullständigt så att det passar ditt användnings fall. 


Kom igång med [självstudier från slut punkt till slut punkt](./tutorial-in-store-analytics-create-app-pnp.md) som vägleder dig genom hur du skapar en lösning med hjälp av en av mallarna för BA-utcheckning i butiken.


## <a name="what-is-smart-inventory-management-solution"></a>Vad är smart Inventory Management-lösning?
"Inventering" är lager varor som innehas av en åter försäljare. Varje åter försäljare behöver inventering för att kunna ta hand om leverans-och logistik produktions tid. Inventeringen är utan tvekan för den mest värdefulla resurs som varje åter försäljare behöver handel. I dagens Omnichannel värld är inventerings hantering ett kritiskt krav för att säkerställa att rätt produkt är på rätt plats vid rätt tidpunkt. Att lagra för mycket eller för lite lager kan försämra affärs åter försäljaren. Varje års åter försäljare förlorar 8-10% av intäkterna på grund av brist på inventerings hanterings funktioner.

IoT-data som aktive ras av radio frekvens identifiering (RFID), beacons & kamera är möjligheten att ta itu med denna omfattande utmaning i stor skala. Anslutningen och real tids analysen i IoT-signaler har blivit spelväxlare för åter försäljaren av woes.  Data som samlas in från sensorer, enheter, väder & händelser kan integreras med molnbaserad Business Intelligence-system.  
Fördelarna med Smart inventerings hantering är, 
* Skyddar organisationen mot lagerkvantiteter och säkerställer den önskade kund service nivån. 
* Djupgående analys & insikter om inventerings precision i nära real tid
* Bestäm den rätt mängd inventering som har tillräckligt med kund order

### <a name="out-of-box-experience"></a>Out of Box-upplevelse
Partner kan använda mallen för att utveckla lösningar för hantering av Smart inventerings lösningar &de förmåner. Den här publicerade mallen fokuserar på enhets anslutning, konfiguration & hantering av RFID-& Bluetooth låg energi (Bell)-läsare i IoT Central. 

> [!div class="mx-imgBorder"]
> ![Smart Inventory Management-instrumentpanel](./media/overview-iot-central-retail/smart-inventory-management-dashboard.png)

Observera att instrument panelen ovan är en exempel upplevelse och du kan anpassa programmet fullständigt så att det passar ditt användnings fall. 

Kom igång med [självstudier från slut punkt till slut punkt](./tutorial-iot-central-smart-inventory-management-pnp.md) som vägleder dig genom hur du skapar en lösning med hjälp av en av mallarna för smart Inventory Management.


## <a name="next-steps"></a>Nästa steg
För att komma igång med att skapa en detalj handels lösning:
* Kom igång med [kursen från slut punkt till slut punkt](./tutorial-in-store-analytics-create-app-pnp.md) som vägleder dig genom hur du skapar en lösning med hjälp av en av mallarna för BA-programmallar.
* Lär dig att distribuera en [mall för kopplad logistik lösning](./tutorial-iot-central-connected-logistics-pnp.md)
* Lär dig hur du distribuerar [mallen för digital distributions Center](./tutorial-iot-central-digital-distribution-center-pnp.md)
* Lär dig hur du distribuerar [Smart Inventory Management-mall](./tutorial-iot-central-smart-inventory-management-pnp.md)
* Läs mer om IoT Central referera till [IoT Central översikt](../core/overview-iot-central-pnp.md)
