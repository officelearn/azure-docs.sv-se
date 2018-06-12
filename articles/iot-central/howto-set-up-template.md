---
title: Ange en mall för enheten i Azure IoT centrala program | Microsoft Docs
description: Lär dig hur du ställer in en mall för enheten med mått, inställningar, egenskaper, regler och instrumentpanelen.
author: viv-liu
ms.author: viviali
ms.date: 04/16/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: bda056a75ae9d696dab389b85fe1bfb2935ee1a8
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35261992"
---
# <a name="set-up-a-device-template"></a>Ange en mall för enhet

En mall för enheten är en modell som definierar egenskaper och beteenden för en typ av enhet som ansluter till en Microsoft Azure IoT centralt program.

En builder kan till exempel skapa en mall för enheten för en ansluten IoT-fläkt som har:

- Temperatur telemetri mätning

- Fläkt meddelar fel händelse mätning

- Fläkt operativsystem tillstånd mätning

- Hastighet för fläkten

- Platsegenskapen

- Regler som skickar aviseringar

- Instrumentpanel som ger dig en alla upp visar information om enheten

Med den här enheten mallen operatör kan skapa och ansluta verkliga fläkt enheter med namn som **fläkt 1** och **fläkt 2**. Dessa fläktar har mått, inställningar och egenskaper, regler och en instrumentpanel som användare av ditt program kan övervaka och hantera.

> [!NOTE]
Endast builders och administratörer kan skapa, redigera och ta bort enheten mallar. Alla användare kan skapa enheter i den **enheten Explorer** sida från befintliga mallar för enheten.

## <a name="create-a-new-device-template"></a>Skapa en ny enhetsmall

1. Navigera till den **Application Builder** sidan.

1. Välj för att skapa en tom mall **Skapa mall för enheten**, och välj sedan **anpassad**.

1. Ange ett namn för den nya mallen för enheten och välj **skapa**.

    ![Informationssidan för enheten](./media/howto-set-up-template/devicedetailspage.png)

1. Nu har du kommit till den **enhetsinformation** sidan i en ny simulerade enhet. En simulerad enhet skapas automatiskt för dig när du skapar en ny mall för enheten. Den rapporterar data och kan kontrolleras precis som en verklig enhet.

Nu titta på flikarna på den **enhetsinformation** sidan.

## <a name="measurements"></a>Mått

Måtten är data från enheten. Du kan lägga till flera mått mallen enheten för att matcha funktionerna för enheten. Telemetri och händelse för närvarande typer av mått stöds.

- **Telemetri** måtten är numeriska datapunkter enheten samlar in över tid och är representeras en ständig ström. Till exempel temperatur.
- **Händelsen** måtten är point-in-time-data som representerar något signifikanta på enheten. Händelser har allvarlighetsgrad som är kopplade till dem som representerar vikten av händelsen. Till exempel fläkt meddelar fel
- **Tillstånd** mått representerar tillståndet för enheten och dess komponenter under en viss tidsperiod. Till exempel fläkt läge som kan definieras med operativsystemet och stoppas som två möjliga tillstånd.

### <a name="create-a-telemetry-measurement"></a>Skapa en telemetri
Klicka på Lägg till en ny telemetri mätning **+ ny mätning** knapp som öppnar ett formulär med alternativ att välja måttenhet. Välj **telemetri** och ange information på den **skapa telemetri** formuläret.

> [!NOTE]
> När en verklig enhet är ansluten, betala uppmärksam på namnet på måttet rapporterar enheten. Namnet måste exakt matcha de **fältnamn** på ett mått.

Du kan exempelvis lägga till en ny temperatur telemetri mätning:

![Mått formulär](./media/howto-set-up-template/measurementsform.png)

När du har valt **spara**, **temperatur** mätning visas i listan över mått och operatör kan se visualiseringen temperaturen data att samla in enheten.

![Mått diagram](./media/howto-set-up-template/measurementsgraph.png)

### <a name="create-an-event-measurement"></a>Skapa en händelse
Klicka på Lägg till en ny händelse mätning **+ ny mätning** knapp som öppnar ett formulär med alternativ att välja måttenhet. Välj **händelse** och ange information på den **Skapa händelse** formuläret.

I det här formuläret ger den **visningsnamn**, **fältnamn**, och **allvarlighetsgrad** för händelsen. Du kan välja mellan tre tillgängliga nivåer av allvarlighetsgrad - **fel**, **varning**, och **Information**.  

Exempelvis kan du lägga till en ny händelse fläkt meddelar fel.

![Händelsen mätningar formulär](./media/howto-set-up-template/eventmeasurementsform.png)

När du har valt **spara**, **fläkt meddelar fel** mått som visas i listan över mått och en operatör kan se visualisering av enheten skickar informationen om händelsen.

![Händelsen mätningar diagram](./media/howto-set-up-template/eventmeasurementschart.png)

Klicka på ikonen händelse i diagrammet för att visa ytterligare information om händelsen:

![Händelsebeskrivning mått](./media/howto-set-up-template/eventmeasurementsdetail.png)


### <a name="create-a-state-measurement"></a>Skapa ett mått i tillstånd
Om du vill lägga till en ny mätning tillstånd, klickar du på **+ ny mätning** knapp som öppnar ett formulär med alternativ att välja måttenhet. Välj **tillstånd** och ange information på den **skapa tillstånd** formuläret.

I det här formuläret ger den **visningsnamn**, **fältnamn**, och möjliga **värden** för tillståndet. Varje **värdet** kan också ha ett namn som ska användas när värdet visas i diagram och tabeller.

Exempelvis kan du lägga till ett nytt fläkt-läge tillstånd som har två möjliga värden som enheten kan skicka **operativsystem** och **stoppad**.

![Tillstånd mätningar formulär](./media/howto-set-up-template/statemeasurementsform.png)

När du har valt **spara**, **fläkt läge** tillstånd mätning visas i listan över mått och operatorn kan se visualisering av systemtillstånd enheten skickar.

![Tillstånd mätningar diagram](./media/howto-set-up-template/statemeasurementschart.png)

Om det finns för många datapunkter som skickas av enheten på kort tid visas tillståndsmåttet med ett annat utseende enligt exemplet nedan. Om du klickar på diagrammet visas alla datapunkter inom den tidsperioden i kronologisk ordning. Du kan också begränsa tidsintervallet för att se mätningen ritas i diagrammet.

![Information om tillstånd mått](./media/howto-set-up-template/statemeasurementsdetail.png)


## <a name="settings"></a>Inställningar

Inställningar styr en enhet. De kan operatörer för programmet och ange indata till enheten. Du kan lägga till flera inställningar i mallen för enheten som visas sida vid sida på den **inställningar** för ansvariga att använda. Det finns sex olika typer av inställningar som du kan lägga till: tal, text, datum, växla, urvalet och etiketten avsnitt.

> [!NOTE]
> När en verklig enhet är ansluten, kan du vara uppmärksam på namnet på inställningen rapporter om enhetens. Namnet måste exakt matcha de **fältnamn** inställning.

Inställningarna kan vara i ett av tre lägen. Dessa tillstånd rapporteras av enheten.

- **Synkroniserade**: enheten har ändrats för att återspegla värdet.

- **Väntande**: enheten för närvarande att inställningens värde.

- **Fel**: enheten har returnerat ett fel.

Exempelvis kan du lägga till en ny fläkt hastighet inställning:

![Inställningar för formulär](./media/howto-set-up-template/settingsform.png)

När du har valt **spara**, **fläkt hastighet** inställningen visas som en panel och är redo för att användas för att ändra fläkt hastigheten på enheten.

> [!NOTE]
> När du har skapat en ny panel kan du prova den nya inställningen. Först stänga av designläge överst höger på skärmen:

![Inställningar för sida vid sida](./media/howto-set-up-template/settingstile.png)

## <a name="properties"></a>Egenskaper

Egenskaperna är enhetsmetadata som associeras med enhet, till exempel enhetsplats och serienummer. Du kan lägga till flera egenskaper i mallen för enheten som visas sida vid sida på den **egenskaper** fliken. En operatör kan ange värden för egenskaper när de skapar en ny enhet, och de kan ändra dessa värden när som helst. Det finns sex typer av du kan lägga till egenskaper: tal, text, datum, växla, enhetsegenskap och etikett.

Det finns två typer av egenskaper:

- **Egenskaper för enhet** rapporteras egenskaper av enheten.
- **Programegenskaper** egenskaper lagras enbart i programmet. Enheten har ingen kunskap om programegenskaperna.

> [!NOTE]
> När en verklig enhet är ansluten, betala uppmärksam på namnet på egenskapen för enhetsegenskaper rapporterar enheten. Namnet måste exakt matcha de **fältnamn** för egenskapen. För programegenskaper kan fältnamnet vara vad du vill, så länge namnet är unikt i mallen för enheten.

Du kan till exempel lägga till enhetsplats som en ny egenskap:

![Egenskaper för formulär](./media/howto-set-up-template/propertiesform.png)

När du har valt **spara**, enhetsplats visas som en panel:

![Panelen Egenskaper](./media/howto-set-up-template/propertiestile.png)

> [!NOTE]
> Du kan ändra egenskapsvärdet för när du har skapat en ny panel. Först stänga av designläge överst höger på skärmen.

### <a name="create-a-location-property-powered-by-azure-maps"></a>Skapa en plats-egenskap som tillhandahålls av Azure Maps
Du kan ge geografiska kontext till data i Azure IoT Central plats och mappa alla latitud och longitud koordinaterna för en gatuadress eller helt enkelt latitud och longitud koordinater. Den här funktionen i Azure IoT Central drivs av Azure Maps.

Det finns två typer av du kan lägga till egenskaper:
- **Platsen som en egenskap på programmet** som kommer att lagras enbart i programmet. Enheten har ingen kunskap om programegenskaperna.
- **Platsen som en enhetsegenskap** som rapporteras av enheten.

####<a name="adding-location-as-an-application-property"></a>Lägga till platsen som en egenskap för programmet 
Du kan skapa en plats egenskapen som en egenskap för program som använder Azure mappar i ditt Azure IoT centrala program. Exempelvis kan du lägga till Enhetsadressen som installationen. 

1. Gå till fliken enhetsegenskap. Kontrollera designläge är påslagen.

![Platsegenskapen](./media/howto-set-up-template/locationcloudproperty1.png)

2. Klicka på plats på fliken Egenskaper.
3. Konfigurera visningsnamn, fältnamn och det initiala värdet för platsen (valfritt). 

![Plats egenskapen formulär](./media/howto-set-up-template/locationcloudproperty2.png)

Det finns två format som stöds för att lägga till en plats:
- **Platsen som en adress**
- **Platsen som koordinater** 

4. Klicka på Spara. 

![Egenskapen Platsfältet](./media/howto-set-up-template/locationcloudproperty3.png)

En operatör kan nu uppdatera plats värdet i formuläret plats fältet. 

####<a name="adding-location-as-a-device-property"></a>Lägga till platsen som en enhetsegenskap för 

Du kan skapa en plats som en enhet som rapporteras av enheten.
Till exempel att du vill spåra platsen för enheten.

1.  Gå till fliken enhetsegenskap. Kontrollera designläge är påslagen.
2.  Klicka på enhetsegenskap från biblioteket.

![Egenskapen Platsfältet](./media/howto-set-up-template/locationdeviceproperty1.png)

3.  Konfigurera visningsnamn, fältnamn och välj ”plats” som en datatyp. 

> [!NOTE]
Fältnamnet måste exakt matcha namnet på egenskapen enheten rapporter. 

![Egenskapen Platsfältet](./media/howto-set-up-template/locationdeviceproperty2.png)

![Plats egenskapen operator-vyn](./media/howto-set-up-template/locationdeviceproperty2.png)

Nu när du har konfigurerat din Platsegenskapen, kan du lägga till en karta för att visualisera plats i instrumentpanelen för enheten. Se hur du [Lägg till plats Azure kartan i instrumentpanelen](howto-set-up-template.md).




## <a name="rules"></a>Regler

Regler kan operatörer att övervaka enheter i nära realtid. Regler automatiskt anropa **åtgärder** som skickar ett e-postmeddelande när regeln utlöser. Det finns en typ av regel idag:

- **Telemetri regel:** telemetri regel utlöser när den valda enhetstelemetrin överskrider ett angivet tröskelvärde. Lär dig mer om [telemetri regler](howto-create-telemetry-rules.md).

## <a name="dashboard"></a>Instrumentpanel

Instrumentpanelen är operatör kan var finns information om en enhet. Du kan lägga till paneler till den här sidan som hjälpa operatörer att förstå hur enheten fungerar som en builder. Du kan lägga till flera instrumentpaneler mallen för enheten. Det finns sex typer av instrumentpaneler som du kan lägga till: bild, diagram, liggande diagram, KPI: N, inställningar och egenskaper och etikett.

Du kan till exempel lägga till en **inställningar och egenskaper** rutan för att visa ett urval av aktuella värden för egenskaper och inställningar:

![Instrumentpanelen enheten formulär](./media/howto-set-up-template/dashboardsettingsandpropertiesform.png)

Nu när en operator visar instrumentpanelen, ser den här panelen som visar egenskaper och inställningar på enheten:

![Instrumentpanelen](./media/howto-set-up-template/dashboardtile.png)

### <a name="add-location-azure-map-in-dashboard"></a>Lägg till plats Azure kartan i instrumentpanelen

Om du har konfigurerat en Platsegenskapen enligt stegen [skapa en plats-egenskap som tillhandahålls av Azure Maps]((howto-set-up-template.md), du kommer att kunna visualisera platsen med hjälp av en karta direkt i instrumentpanelen i enheten.

1.  Gå till fliken instrumentpanelen för enheten. Kontrollera designläge är påslagen.
2.  Välj karta från biblioteket på instrumentpanelen för enheten. 

![Instrumentpanelen Azure platskarta väljer](./media/howto-set-up-template/locationcloudproperty4map.png)

3.  Ge ett namn och Välj egenskapen location som du tidigare har konfigurerat som en del av din enhetsegenskap.

![Konfigurera Azure kartan plats på instrumentpanelen](./media/howto-set-up-template/locationcloudproperty5map.png)

4.  Spara och du ser kartan panelen visar den plats som du har valt. 

![Azure kartan visualisera plats på instrumentpanelen](./media/howto-set-up-template/locationcloudproperty6map.png) 

Du kommer att kunna ändra storlek på kartan för att den har önskad storleken.

Nu när en operator visar instrumentpanelen, ser den här alla instrumentpanelen rutor du har konfigurerat inklusive plats karta!

![Instrumentpanelen plats Azure kartan instrumentpanelen](./media/howto-set-up-template/locationcloudproperty7map.png) 



## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du ställer in en mall för enheten i Azure IoT centrala programmet, är här det föreslagna nästa steget:

> [!div class="nextstepaction"]
> [Skapa en ny enhet mallversion](howto-version-devicetemplate.md)
