---
title: Bygga butikslösningar med Azure IoT Central | Microsoft-dokument
description: Lär dig mer om hur du använder Azure IoT Central-programmallar för att skapa ansluten logistik, digitalt distributionscenter, analyser i butik, tillståndsövervakning, kassa, smart lagerhantering och butikslösningar.
author: avneet723
ms.author: avneets
ms.date: 01/10/2020
ms.topic: overview
ms.service: iot-central
ms.subservice: iot-central-retail
services: iot-central
ms.openlocfilehash: 808a17fcf303c6eb72f11f5379fa172302dc782a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77059868"
---
# <a name="building-retail-solutions-with-azure-iot-central"></a>Skapa lösningar för detaljhandeln med Azure IoT Central



Azure IoT Central är en IoT-appplattform som minskar bördan och kostnaden för att utveckla, hantera och underhålla IoT-lösningar i företagsklass. Om du väljer att bygga med Azure IoT Central kan du fokusera tid, pengar och energi på att omvandla din verksamhet med IoT-data, i stället för att bara underhålla och uppdatera en komplex och ständigt föränderlig IoT-infrastruktur.

I den här artikeln beskrivs flera butiksspecifika IoT Central-programmallar. Som lösningsbyggare kan du använda dessa mallar för att skapa IoT-lösningar som optimerar leveranskedjor, förbättrar butiksupplevelser för kunder och spårar lager mer effektivt.

> [!div class="mx-imgBorder"]
> ![Översikt över Azure IoT Retail](./media/overview-iot-central-retail/retail-app-templates.png)

I följande avsnitt beskrivs funktionerna i dessa programmallar:

## <a name="connected-logistics"></a>Ansluten logistik

Globala logistikutgifterna förväntas nå $ 106000000000000 2020. Transport av varor står för majoriteten av dessa utgifter och sjöfartsleverantörer är under intensiv konkurrenstryck och begränsningar.

Du kan använda IoT-sensorer för att samla in och övervaka omgivningsförhållanden som temperatur, luftfuktighet, lutning, stötar, ljus och placeringen av en sändning. Du kan kombinera telemetri som samlats in från IoT-sensorer och enheter med andra datakällor som väder- och trafikinformation i molnbaserade business intelligence-system.

Fördelarna med en ansluten logistiklösning är:

* Sändningsövervakning med spårning och spårning i realtid. 
* Sändningsintegritet med övervakning av omgivningsförhållanden i realtid.
* Säkerhet från stöld, förlust eller skada av försändelser.
* Geo-stängsel, ruttoptimering, vagnparkshantering och fordonsanalys.
* Prognostisering för förutsägbar avgång och ankomst av transporter.

Följande skärmbilder visar den färdiga instrumentpanelen i programmallen. Instrumentpanelen är helt anpassningsbar för att uppfylla dina specifika lösningskrav:

> [!div class="mx-imgBorder"]
> ![Instrumentpanel för ansluten logistik](./media/overview-iot-central-retail/connected-logistics-dashboard1.png)

> [!div class="mx-imgBorder"]
> ![Instrumentpanel för ansluten logistik](./media/overview-iot-central-retail/connected-logistics-dashboard2.png)

Mer information finns i [självstudien för distribuera och gå igenom en mall för ansluten logistikprogram.](./tutorial-iot-central-connected-logistics-pnp.md)

## <a name="digital-distribution-center"></a>Digitalt distributionscenter

Som tillverkare och återförsäljare etablera världsomspännande närvaro, deras leveranskedjor gren ut och bli mer komplexa. Konsumenterna förväntar sig nu att stora urval av produkter ska vara tillgängliga, och att dessa varor kommer fram inom en eller två dagar efter köpet. Distributionscentraler måste anpassa sig till dessa trender och samtidigt övervinna befintliga ineffektivitet. 

Idag innebär en tillit till manuellt arbete att plockning och förpackning står för 55-65% av distributionscentralen kostnader. Manuell plockning och förpackning är också vanligtvis långsammare än automatiserade system, och snabbt fluktuerande personalbehov gör det ännu svårare att möta fraktvolymer. Denna säsongsvariation leder till hög personalomsättning och ökar sannolikheten för kostsamma fel.

Lösningar baserade på IoT-aktiverade kameror kan ge transformationsfördelar genom att aktivera en digital återkopplingsslinga. Data från hela distributionscentret leder till användbara insikter som i sin tur resulterar i bättre data.

Fördelarna med ett digitalt distributionscenter är:

* Kameror övervakar varor när de anländer och rör sig genom transportörsystemet.
* Automatisk identifiering av felaktiga varor.
* Effektiv orderspårning.
* Minskade kostnader, förbättrad produktivitet och optimerad användning.

Följande skärmbild visar den färdiga instrumentpanelen i programmallen. Instrumentpanelen är helt anpassningsbar för att uppfylla dina specifika lösningskrav: 

> [!div class="mx-imgBorder"]
> ![Instrumentpanelen för digital distributionscenter](./media/overview-iot-central-retail/digital-distribution-center-dashboard.png)

Mer information finns i [självstudien för distribution och gå igenom en programmall för ett programmall för digitalt distributionscenter.](./tutorial-iot-central-digital-distribution-center-pnp.md)

## <a name="in-store-analytics---condition-monitoring"></a>Analys i butik - tillståndsövervakning

För många återförsäljare är miljöförhållandena i deras butiker en viktig skillnad från sina konkurrenter. Återförsäljare vill upprätthålla trevliga förhållanden inom sina butiker till förmån för sina kunder.  

Som lösningsbyggare kan du använda IoT Central i butik analytics condition monitoring application template för att skapa en heltäckande lösning. Med programmallen kan du ansluta digitalt till och övervaka en butiksmiljö med hjälp av olika typer av sensorenheter. Dessa sensorenheter genererar telemetri som du kan konvertera till affärsinsikter som hjälper återförsäljaren att minska driftskostnaderna och skapa en bra upplevelse för sina kunder.

Använd programmallen för att:

* Anslut en mängd IoT-sensorer till en IoT Central-programinstans.
* Övervaka och hantera sensornätverkets hälsa samt alla gatewayenheter i miljön.
* Skapa anpassade regler kring miljöförhållandena i en butik för att utlösa aviseringar för butikschefer.
* Omvandla miljöförhållandena i din butik till insikter som butiksteamet kan använda för att förbättra kundupplevelsen.
* Exportera de aggregerade insikterna till befintliga eller nya affärsprogram för att ge användbar och aktuell information till butikspersonal.

Programmallen levereras med en uppsättning enhetsmallar och använder en uppsättning simulerade enheter för att fylla i instrumentpanelen. 

Följande skärmbild visar den färdiga instrumentpanelen i programmallen. Instrumentpanelen är helt anpassningsbar för att uppfylla dina specifika lösningskrav: 

> [!div class="mx-imgBorder"]
> ![Övervakning av analystillstånd i butik](./media/overview-iot-central-retail/in-store-analytics-condition-dashboard.png)

Mer information finns i [självstudien Skapa ett analysprogram i butik i Azure IoT Central.](./tutorial-in-store-analytics-create-app-pnp.md)

## <a name="in-store-analytics---checkout"></a>Analyser i butik – utcheckning

För vissa återförsäljare, kassan erfarenhet inom sina butiker är en viktig differentiator från sina konkurrenter. Återförsäljare vill leverera en smidig kassaupplevelse i sina butiker för att uppmuntra kunderna att återvända.  

Som lösningsbyggare kan du använda ioT Central i butik analytics checkout application template för att skapa en lösning som ger insikter från kassan i en butik till butikspersonal. Sensorer kan till exempel ge information om kölängder och genomsnittliga väntetider för varje utcheckningsfil.

Använd programmallen för att:

* Anslut en mängd IoT-sensorer till en IoT Central-programinstans.
* Övervaka och hantera sensornätverkets hälsa samt alla gatewayenheter i miljön.
* Skapa anpassade regler runt kassavillkoret i en butik för att utlösa aviseringar för butikspersonal.
* Omvandla kassavillkoren i butiken till insikter som butiksteamet kan använda för att förbättra kundupplevelsen.
* Exportera de aggregerade insikterna till befintliga eller nya affärsprogram för att ge användbar och aktuell information till butikspersonal.

Programmallen levereras med en uppsättning enhetsmallar och använder en uppsättning simulerade enheter för att fylla instrumentpanelen med filbeläggningsdata. 

Följande skärmbild visar den färdiga instrumentpanelen i programmallen. Instrumentpanelen är helt anpassningsbar för att uppfylla dina specifika lösningskrav: 

> [!div class="mx-imgBorder"]
> ![Kassan för analys i butik](./media/overview-iot-central-retail/In-Store-Analytics-Checkout-Dashboard.png)

Mer information finns i [självstudien Skapa ett analysprogram i butik i Azure IoT Central.](./tutorial-in-store-analytics-create-app-pnp.md)

## <a name="smart-inventory-management"></a>Smart lagerhantering

Lager är lagret av varor en återförsäljare innehar. Lagerhantering är avgörande för att säkerställa att rätt produkt är på rätt plats vid rätt tidpunkt. En återförsäljare måste balansera kostnaderna för att lagra för mycket lager mot kostnaderna för att inte ha tillräckligt med artiklar i lager för att möta efterfrågan.

IoT-data som genereras från RFID-taggar(Radio Frequency Identification), beacons och kameror ger möjligheter att förbättra lagerhanteringsprocesser. Du kan kombinera telemetri som samlats in från IoT-sensorer och enheter med andra datakällor som väder- och trafikinformation i molnbaserade business intelligence-system.

Fördelarna med smart lagerhantering är:

* Minska risken för att artiklar är i lager och säkerställa önskad kundservice nivå. 
* Djupgående analys och insikter i lagernoggrannhet i nära realtid.
* Verktyg för att avgöra rätt mängd lager att hålla för att möta kundorder.

Den här programmallen fokuserar på enhetsanslutning och konfiguration och hantering av RFID- och Bluetooth-ble-läsare (Low Energy).

Följande skärmbild visar den färdiga instrumentpanelen i programmallen. Instrumentpanelen är helt anpassningsbar för att uppfylla dina specifika lösningskrav:

> [!div class="mx-imgBorder"]
> ![Instrumentpanel för smart lagerhantering](./media/overview-iot-central-retail/smart-inventory-management-dashboard.png)

Mer information finns i [självstudien distribuera och gå igenom en självstudiekurs för ett smart lagerhanteringsprogram.](./tutorial-iot-central-smart-inventory-management-pnp.md)

## <a name="micro-fulfillment-center"></a>Mikrofullgörelsecenter

I det alltmer konkurrenskraftiga detaljhandelslandskapet står detaljhandlarna ständigt inför påtryckningar för att överbrygga klyftan mellan efterfrågan och tillfredsställelse. En ny trend som har uppstått för att möta den växande konsumenternas efterfrågan är att hysa lager nära slutkunderna och de butiker de besöker.

IoT Central micro-fulfillment center application template gör det möjligt för lösningsbyggare att övervaka och hantera alla aspekter av sina helt automatiserade uppfyllelsecenter. Mallen innehåller en uppsättning simulerade tillståndsövervakningssensorer och robotbärare för att påskynda lösningsutvecklingsprocessen. Dessa sensorenheter fångar meningsfulla signaler som kan omvandlas till affärsinsikter som gör det möjligt för återförsäljare att minska sina driftskostnader och skapa upplevelser för sina kunder.

Med programmallen kan du: 

- Anslut sömlöst olika typer av IoT-sensorer som robotar eller tillståndsövervakningssensorer till en IoT Central-applikationsinstans.
- Övervaka och hantera sensornätverkets och alla gatewayenheter i miljön.
- Skapa anpassade regler kring miljövillkoren i ett uppfyllelsecenter för att utlösa lämpliga aviseringar.
- Omvandla miljöförhållandena inom ditt uppfyllelsecenter till insikter som kan utnyttjas av butikslagerteamet.
- Exportera de aggregerade insikterna till befintliga eller nya affärsapplikationer till förmån för butikspersonalen.

Följande skärmbild visar den färdiga instrumentpanelen i programmallen. Instrumentpanelen är helt anpassningsbar för att uppfylla dina specifika lösningskrav:

> [!div class="mx-imgBorder"]
> ![Mikro-uppfyllelse Center](./media/overview-iot-central-retail/MFC-Dashboard.png)

Mer information finns i [självstudien för distribution och gå igenom programmallen för mikrouppfyllelsecenter.](./tutorial-micro-fulfillment-center-pnp.md)

## <a name="next-steps"></a>Nästa steg

Så här kommer du igång med att bygga en butikslösning:

* Kom igång med [programmet Skapa ett analysprogram i Azure IoT Central](./tutorial-in-store-analytics-create-app-pnp.md) som hjälper dig att skapa en lösning med en av analysprogrammallarna i butik.
* [Distribuera och gå igenom en ansluten logistikapplikationsmall](./tutorial-iot-central-connected-logistics-pnp.md).
* [Distribuera och gå igenom en programmall för ett program för ett digitalt distributionscenter](./tutorial-iot-central-digital-distribution-center-pnp.md).
* [Distribuera och gå igenom en programmall för smart lagerhantering](./tutorial-iot-central-smart-inventory-management-pnp.md).
* [Distribuera och gå igenom programmallen för mikrouppfyllelsecenter](./tutorial-micro-fulfillment-center-pnp.md).
* Läs mer om IoT Central i [IoT Central översikt](../preview/overview-iot-central.md).
