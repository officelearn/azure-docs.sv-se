---
title: Skapa detalj handels lösningar med Azure IoT Central | Microsoft Docs
description: Lär dig mer om att använda Azure IoT Central-programmallar för att skapa ansluten logistik, digital distributions Center, in-Store-analys, villkors övervakning, utcheckning, hantering av smarta lager och detalj handels lösningar.
author: avneet723
ms.author: avneets
ms.date: 01/10/2020
ms.topic: overview
ms.service: iot-central
ms.subservice: iot-central-retail
services: iot-central
ms.openlocfilehash: 8b1861b96f7ed53388d49e226bf3880bfcf657cb
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2020
ms.locfileid: "75895868"
---
# <a name="building-retail-solutions-with-azure-iot-central"></a>Skapa lösningar för detaljhandeln med Azure IoT Central

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Azure IoT Central är en IoT app-plattform som minskar den börda och kostnad som är kopplad till utveckling, hantering och underhåll av IoT-lösningar i företags klass. Genom att välja att skapa med Azure IoT Central får du möjlighet att fokusera tid, pengar och energi på att omvandla din verksamhet med IoT-data, i stället för att bara underhålla och uppdatera en komplex och ständigt växande IoT-infrastruktur.

Den här artikeln beskriver flera återförsäljar-specifika IoT Central programmallar. Som Solution Builder kan du använda de här mallarna för att skapa IoT-lösningar som optimerar leverans kedjor, förbättra i Store-upplevelser för kunder och spåra inventeringen mer effektivt.

> [!div class="mx-imgBorder"]
> Översikt över ![Azure IoT återförsäljarversion](./media/overview-iot-central-retail/retail-app-templates.png)

I följande avsnitt beskrivs funktionerna i dessa programmallar:

## <a name="connected-logistics"></a>Ansluten logistik

Globala logistik utgifter förväntas uppnå $10.6 TN i 2020. Transport av gods konton för de flesta av dessa utgifter och leverantörer av leverans är under intensiv konkurrens kraftig belastning och begränsningar.

Du kan använda IoT-sensorer för att samla in och övervaka omgivnings förhållanden som temperatur, fuktighet, lutning, stöt, ljus och platsen för en försändelse. Du kan kombinera telemetri som samlats in från IoT-sensorer och enheter med andra data källor, till exempel väder-och trafik information i molnbaserade Business Intelligence system.

Fördelarna med en ansluten logistik lösning är:

* Leverans övervakning med spårning och spårning i real tid. 
* Leverans integritet med övervakning i real tid med omgivnings tillstånd.
* Säkerhet från stöld, förlust eller skada för försändelser.
* Geo-staket, väg optimering, hantering av flottan och analys av fordon.
* Prognosticering för förutsägbar avresa och införsel av försändelser.

Följande skärm bilder visar den färdiga instrument panelen i program mal len. Instrument panelen är helt anpassningsbar för att uppfylla dina särskilda lösnings krav:

> [!div class="mx-imgBorder"]
> ![ansluten logistik instrument panel](./media/overview-iot-central-retail/connected-logistics-dashboard1.png)

> [!div class="mx-imgBorder"]
> ![ansluten logistik instrument panel](./media/overview-iot-central-retail/connected-logistics-dashboard2.png)

Mer information finns i själv studie kursen [distribuera och gå igenom en sammankopplad logistik-Programmall](./tutorial-iot-central-connected-logistics-pnp.md) .

## <a name="digital-distribution-center"></a>Digitalt distributionscenter

När tillverkare och åter försäljare upprättar över hela världen, kan deras försörjnings kedjor förgrena ut och bli mer komplexa. Konsumenterna förväntar sig nu stora val av produkter som är tillgängliga och för de varorna att komma in inom en eller två dagars inköps dagar. Distributions Center måste anpassas till dessa trender samtidigt som du överkommer till befintliga ineffektiva. 

Idag innebär ett beroende av manuell arbete att plocknings-och förpacknings konton för 55-65% av distributions Center kostnaderna. Manuell plockning och paketering är också ofta långsammare än automatiserade system, och de behöver inte göra det ännu svårare att uppfylla frakt volymer. Den här säsongs variationen resulterar i hög personal omsättning och ökar risken för kostsamma fel.

Lösningar som baseras på IoT-aktiverade kameror kan leverera debildnings förmåner genom att aktivera en digital feedback-slinga. Data från alla distributions Center leder till användbara insikter som i sin tur resulterar i bättre data.

Fördelarna med en digital distributions Center är:

* Kameror övervakar varor när de tas emot och flyttas genom transport band systemet.
* Automatisk identifiering av felaktiga varor.
* Effektiv order spårning.
* Lägre kostnader, förbättrad produktivitet och optimerad användning.

Följande skärm bild visar den färdiga instrument panelen i program mal len. Instrument panelen är helt anpassningsbar för att uppfylla dina särskilda lösnings krav: 

> [!div class="mx-imgBorder"]
> Instrument panel för ![Digital distributions Center](./media/overview-iot-central-retail/digital-distribution-center-dashboard.png)

Mer information finns i själv studie kursen [distribuera och gå igenom en app-mall för digital distributions Center](./tutorial-iot-central-digital-distribution-center-pnp.md) .

## <a name="in-store-analytics---condition-monitoring"></a>In-Store Analytics – villkors övervakning

För många åter försäljare är miljö förhållanden inom sina butiker en viktig särskiljare från sina konkurrenter. Åter försäljare vill ha Pleasant-villkor inom sina butiker för att få nytta av sina kunder.  

Som Solution Builder kan du använda programmallen IoT Central in-Store Analytics-övervakning för att skapa en lösning från slut punkt till slut punkt. Med program mal len kan du ansluta till och övervaka en detalj handels miljö med olika sensor enheter. Dessa sensor enheter genererar telemetri som du kan konvertera till affärs insikter som hjälper åter försäljare att minska drifts kostnaderna och skapa en bra upplevelse för kunderna.

Använd program mal len för att:

* Anslut en mängd IoT-sensorer till en IoT Central program instans.
* Övervaka och hantera hälso tillståndet för sensor nätverket samt alla gateway-enheter i miljön.
* Skapa anpassade regler kring miljö villkoren i en butik för att utlösa aviseringar för Store-hanterare.
* Omvandla miljö villkoren i butiken till insikter som butiks teamet kan använda för att förbättra kund upplevelsen.
* Exportera de sammanställda insikterna till befintliga eller nya affärs program för att tillhandahålla användbar och tidsmerad information till butiks personalen.

Program mal len innehåller en uppsättning enhetsspecifika mallar och använder en uppsättning simulerade enheter som fyller på instrument panelen. 

Följande skärm bild visar den färdiga instrument panelen i program mal len. Instrument panelen är helt anpassningsbar för att uppfylla dina särskilda lösnings krav: 

> [!div class="mx-imgBorder"]
> ![övervakning av villkor i Store Analytics](./media/overview-iot-central-retail/in-store-analytics-condition-dashboard.png)

Mer information finns i själv studie kursen [skapa ett program för analys i Azure IoT Central](./tutorial-in-store-analytics-create-app-pnp.md) .

## <a name="in-store-analytics---checkout"></a>In-Store-analys-utcheckning

För vissa åter försäljare är utcheckningen i sina butiker en viktig särskiljare från sina konkurrenter. Åter försäljare vill tillhandahålla en smidig utcheckning i sina butiker för att uppmuntra kunderna att komma tillbaka.  

Som Solution Builder kan du använda mallen IoT Central Mall för att skapa en lösning som ger insikter från arbets platsen i en butik till butiks personal. Sensorer kan till exempel ge information om Kölängd-och genomsnitts vänte tider för varje kassa band.

Använd program mal len för att:

* Anslut en mängd IoT-sensorer till en IoT Central program instans.
* Övervaka och hantera hälso tillståndet för sensor nätverket samt alla gateway-enheter i miljön.
* Skapa anpassade regler runt betalnings villkoret i en butik för att utlösa aviseringar för detalj handels personal.
* Transformera arbets villkoren i butiken till insikter som butiks teamet kan använda för att förbättra kund upplevelsen.
* Exportera de sammanställda insikterna till befintliga eller nya affärs program för att tillhandahålla användbar och tidsmerad information till butiks personalen.

Program mal len levereras med en uppsättning enhetsspecifika mallar och använder en uppsättning simulerade enheter för att fylla på instrument panelen med Lane-beläggnings data. 

Följande skärm bild visar den färdiga instrument panelen i program mal len. Instrument panelen är helt anpassningsbar för att uppfylla dina särskilda lösnings krav: 

> [!div class="mx-imgBorder"]
> ![checkar in-Store Analytics](./media/overview-iot-central-retail/In-Store-Analytics-Checkout-Dashboard.png)

Mer information finns i själv studie kursen [skapa ett program för analys i Azure IoT Central](./tutorial-in-store-analytics-create-app-pnp.md) .

## <a name="smart-inventory-management"></a>Smart lagerhantering

Inventory är stocken för varor som en åter försäljare innehåller. Lager hantering är viktigt för att säkerställa att rätt produkt är på rätt plats vid rätt tidpunkt. En åter försäljare måste balansera kostnaderna för att lagra för mycket inventering mot kostnaderna för att inte ha tillräckliga artiklar i lager för att möta efter frågan.

IoT-data som genereras från RFID-taggar,-signaler och-kameror ger möjligheter att förbättra inventerings hanterings processerna. Du kan kombinera telemetri som samlats in från IoT-sensorer och enheter med andra data källor, till exempel väder-och trafik information i molnbaserade Business Intelligence system.

Fördelarna med Smart Inventory Management är:

* Minska risken för att artiklar inte är i lager och garantera önskad kund service nivå. 
* Djupgående analys och insikter om inventerings precision i nära real tid.
* Verktyg som hjälper dig att bestämma hur mycket inventering som ska hållas för att möta kund order.

Den här program mal len fokuserar på enhets anslutningar och konfigurationen och hanteringen av enheter med RFID-och Bluetooth låg energi (enhet).

Följande skärm bild visar den färdiga instrument panelen i program mal len. Instrument panelen är helt anpassningsbar för att uppfylla dina särskilda lösnings krav:

> [!div class="mx-imgBorder"]
> ![Smart Inventory Management-instrumentpanel](./media/overview-iot-central-retail/smart-inventory-management-dashboard.png)

Mer information finns i själv studie kursen [distribuera och gå igenom en smart inventerings hantering](./tutorial-iot-central-smart-inventory-management-pnp.md) .

## <a name="micro-fulfillment-center"></a>Micro-uppfyllelse Center

I ett alltmer konkurrens kraftig åter försäljnings fack kan detaljister ständigt ligga till höger för att stänga klyftan mellan efter frågan och utförande. En ny trend som har blivit framgångs punkt för att tillgodose den växande konsument efter frågan är att man ska kunna komma i lager nära slut kunderna och de butiker de besöker.

Med programmallen IoT Central Micro-uppfyllelse Center kan lösningar som hjälper dig att övervaka och hantera alla aspekter av sina helt automatiserade uppfyllande Center. Mallen innehåller en uppsättning simulerade villkor som övervakar sensorer och robot operatörer för att påskynda utvecklings processen för lösningen. Dessa sensor enheter fångar in meningsfulla signaler som kan konverteras till affärs insikter som gör det möjligt för åter försäljare att minska sina drifts kostnader och skapa upplevelser för kunderna.

Med program mal len kan du: 

- Anslut smidigt en mängd IoT-sensorer som robots eller villkors övervaknings sensorer till en IoT Central program instans.
- Övervaka och hantera hälso tillståndet för sensor nätverket samt alla gateway-enheter i miljön.
- Skapa anpassade regler kring miljö villkoren i ett uppfyllande Center för att utlösa lämpliga aviseringar.
- Transformera miljö villkoren i uppfyllande centrum till insikter som kan utnyttjas av butiks lager teamet.
- Exportera de aggregerade insikterna till befintliga eller nya affärs program till förmån för medlemmarna i detalj handels personalen.

Följande skärm bild visar den färdiga instrument panelen i program mal len. Instrument panelen är helt anpassningsbar för att uppfylla dina särskilda lösnings krav:

> [!div class="mx-imgBorder"]
> ![Micro-uppfyllelse Center](./media/overview-iot-central-retail/MFC-Dashboard.png)

Mer information finns i själv studie kursen [distribuera och gå igenom programmallen för Micro-uppfyllelse Center](./tutorial-iot-central-smart-inventory-management-pnp.md) .

## <a name="next-steps"></a>Nästa steg

För att komma igång med att skapa en detalj handels lösning:

* Kom igång med guiden [skapa ett in-Store-analys i Azure IoT Central](./tutorial-in-store-analytics-create-app-pnp.md) -självstudier som vägleder dig genom hur du skapar en lösning med en av mallarna för BA-program i butiken.
* [Distribuera och gå igenom en sammankopplad logistik program mall](./tutorial-iot-central-connected-logistics-pnp.md).
* [Distribuera och gå igenom en app-mall för digital distributions Center](./tutorial-iot-central-digital-distribution-center-pnp.md).
* [Distribuera och gå igenom en mall för hantering av Smart inventerings program](./tutorial-iot-central-smart-inventory-management-pnp.md).
* [Distribuera och gå igenom programmallen för Micro-uppfyllelse Center](./tutorial-iot-central-smart-inventory-management-pnp.md).
* Läs mer om IoT Central i [översikten över IoT Central](../preview/overview-iot-central.md).
