---
title: Ange en mall för enheten i Azure IoT centrala program | Microsoft Docs
description: Lär dig hur du ställer in en mall för enheten med mått, inställningar, egenskaper, regler och en instrumentpanel.
author: viv-liu
ms.author: viviali
ms.date: 04/16/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: f71d4c7dc94fedfd598ab87c51366ba9fb1f184a
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063043"
---
# <a name="set-up-a-device-template"></a>Ange en mall för enhet

En mall för enheten är en modell som definierar egenskaper och beteenden för en typ av enhet som ansluter till en Microsoft Azure IoT centralt program.

En builder kan till exempel skapa en mall för enheten för en IoT-anslutna fläkt som har:

- Temperatur telemetri mätning

- Fläkt meddelar fel händelse mätning

- Fläkt operativsystem tillstånd mätning

- Hastighet för fläkten

- Platsegenskapen

- Regler som skickar aviseringar

- Instrumentpanel som ger en övergripande bild av enheten

Med den här enheten mallen operatör kan skapa och ansluta verkliga fläkt enheter med namn som **fläkt 1** och **fläkt 2**. Dessa fläktar har mått, inställningar, egenskaper, regler och en instrumentpanel som användare av ditt program kan övervaka och hantera.

> [!NOTE]
> Endast builders och administratörer kan skapa, redigera och ta bort enheten mallar. Alla användare kan skapa enheter på den **enheten Explorer** sida från befintliga mallar för enheten.

## <a name="create-a-device-template"></a>Skapa en mall för enhet

1. Gå till den **Application Builder** sidan.

2. Om du vill skapa en tom mall **Skapa mall för enheten**, och välj sedan **anpassad**.

3. Ange ett namn för den nya mallen för enheten och välj **skapa**.

   ![Sidan med ”kylskåpet” som namnet på enheten](./media/howto-set-up-template/devicedetailspage.png)

4. Nu har du kommit till den **enhetsinformation** sidan i en ny simulerade enhet. En simulerad enhet skapas automatiskt för dig när du skapar en mall för enheten. Den rapporterar data och kan kontrolleras precis som en verklig enhet.

Nu titta på flikarna på den **enhetsinformation** sidan.

## <a name="measurements"></a>Mått

Mått är de data som kommer från din enhet. Du kan lägga till flera mått mallen enheten för att matcha funktionerna för enheten.

- **Telemetri** måtten är numeriska datapunkter som enheten samlar in över tid. De är representeras som en ständig ström. Ett exempel är temperatur.
- **Händelsen** måtten är point-in-time-data som representerar något signifikanta på enheten. En allvarlighetsgrad representerar vikten av en händelse. Ett exempel är en fläkt meddelar fel.
- **Tillstånd** mått representerar tillståndet för enheten och dess komponenter under en viss tidsperiod. Till exempel ett fläkt-läge kan definieras med **operativsystem** och **stoppad** som två möjliga tillstånd.

### <a name="create-a-telemetry-measurement"></a>Skapa en telemetri
Om du vill lägga till en ny telemetri mätning, Välj den **+ ny mätning** knappen. Välj **telemetri** som mätningen skriver och anger information den **skapa telemetri** formuläret.

> [!NOTE]
> När en verklig enhet är ansluten, ska du vara uppmärksam på namnet på måttet som enheten rapporterar. Namnet måste exakt matcha de **fältnamn** post för ett mått.

Du kan exempelvis lägga till en ny temperatur telemetri mätning:

![”Skapa telemetri” formulär med information för temperatur mätning](./media/howto-set-up-template/measurementsform.png)

När du har valt **spara**, **temperatur** mått som visas i listan över mått. En operatör kan se visualiseringen temperaturen data som samlar in enheten.

![Mätning diagram](./media/howto-set-up-template/measurementsgraph.png)

### <a name="create-an-event-measurement"></a>Skapa en händelse
Om du vill lägga till en ny händelse mätning, Välj den **+ ny mätning** knappen. Välj **händelse** som mätningen skriver och anger information den **Skapa händelse** formuläret.

Ange den **visningsnamn**, **fältnamn**, och **allvarlighetsgrad** information för händelsen. Du kan välja mellan tre tillgängliga nivåer av allvarlighetsgrad: **fel**, **varning**, och **Information**.  

T.ex, du kan lägga till en ny **fläkt meddelar fel** händelse.

![”Skapa en händelse” formulär med information om en fläkt meddelar händelse](./media/howto-set-up-template/eventmeasurementsform.png)

När du har valt **spara**, **fläkt meddelar fel** mått som visas i listan över mått. En operatör kan se visualisering av händelsedata som enheten skickar.

![Diagram för mätning av händelse](./media/howto-set-up-template/eventmeasurementschart.png)

Välj ikonen händelse i diagrammet om du vill visa mer information om händelsen:

![Information om händelsen ”fläkt meddelar Error”](./media/howto-set-up-template/eventmeasurementsdetail.png)


### <a name="create-a-state-measurement"></a>Skapa ett mått i tillstånd
Om du vill lägga till en ny mätning tillstånd, Välj den **+ ny mätning** knappen. Välj **tillstånd** som mätningen skriver och anger information den **skapa tillstånd** formuläret.

Ange detaljer för **visningsnamn**, **fältnamn**, och **värden** för tillståndet. Varje värde kan också ha ett namn som ska användas när värdet visas i diagram och tabeller.

T.ex, du kan lägga till en ny **fläkt läge** tillstånd som har två möjliga värden som enheten kan skicka **operativsystem** och **stoppad**.

![”Redigeringsläge” formulär med information om fläkt läge](./media/howto-set-up-template/statemeasurementsform.png)

När du har valt **spara**, **fläkt läge** tillstånd mätning visas i listan över mått. Operatorn kan se visualisering av systemtillstånd som enheten skickar.

![Tillstånd mätning diagram](./media/howto-set-up-template/statemeasurementschart.png)

Om enheten skickar för många datapunkter i en liten varaktighet, visas tillstånd måttet med en annan visual som visas i följande skärmbild. Om du klickar på diagrammet visas alla datapunkter inom denna tidsperiod i kronologisk ordning. Du kan också begränsa tidsintervallet för att se mätningen ritas i diagrammet.

![Information för mätning ”statiska fläkt läget” tillstånd](./media/howto-set-up-template/statemeasurementsdetail.png)


## <a name="settings"></a>Inställningar

Inställningar styr en enhet. De kan operatörer för programmet och ange indata till enheten. Du kan lägga till flera inställningar i mallen för enheten som visas sida vid sida på den **inställningar** för ansvariga att använda. Du kan lägga till sex olika typer av inställningar: tal, text, datum, växla, urvalet och etiketten avsnitt.

> [!NOTE]
> När en verklig enhet är ansluten, ska du vara uppmärksam på namnet på den inställning som enheten rapporter. Namnet måste exakt matcha de **fältnamn** post för en inställning.

Inställningarna kan vara i ett av tre lägen. Enheten rapporteras dessa lägen.

- **Synkroniserade**: enheten har ändrats för att återspegla värdet.

- **Väntande**: enheten för närvarande att inställningens värde.

- **Fel**: enheten har returnerat ett fel.

Exempelvis kan du lägga till en ny fläkt hastighet inställning:

![”Konfigurera antalet” formulär med information om hastigheten](./media/howto-set-up-template/settingsform.png)

När du har valt **spara**, **fläkthastighet** inställningen visas som en panel och är redo att användas för att ändra fläkt hastigheten på enheten.

När du har skapat en panel kan du prova den nya inställningen. Inaktivera först designläge på den övre högra delen av skärmen.

![Fliken ”inställningar” med ”designläge” växel för panelen](./media/howto-set-up-template/settingstile.png)

## <a name="properties"></a>Egenskaper

Egenskaperna är enhetens metadata som är kopplad till enheten, till exempel enhetsplats och serienummer. Du kan lägga till flera egenskaper i mallen för enheten som visas sida vid sida på den **egenskaper** fliken. En operatör kan ange värden för egenskaper när de skapar en enhet, och de kan ändra dessa värden när som helst. Du kan lägga till sex typer av egenskaper: tal, text, datum, växla, enhetsegenskap och etikett.

Det finns två typer av egenskaper:

- **Enheten** egenskaper som enheten rapporterar.
- **Programmet** egenskaper som lagras enbart i programmet. Enheten inte kan identifiera egenskaper för program.

> [!NOTE]
> För egenskaper för enhet, när en verklig enhet är ansluten, kan du vara uppmärksam på namnet på egenskapen som enheten rapporterar. Namnet måste exakt matcha de **fältnamn** post för egenskapen. För programegenskaper kan fältnamnet vara vad du vill, så länge namnet är unikt i mallen för enheten.

Du kan till exempel lägga till enhetsplats som en ny egenskap:

![”Konfigurera Text” formulär på fliken ”Egenskaper”](./media/howto-set-up-template/propertiesform.png)

När du har valt **spara**, enhetsplats visas som en panel:

![Plats sida vid sida](./media/howto-set-up-template/propertiestile.png)

Du kan ändra egenskapsvärdet för när du har skapat en panel. Inaktivera först designläge i den övre högra delen av skärmen.

### <a name="create-a-location-property-through-azure-maps"></a>Skapa en Platsegenskapen via Azure Maps
Du kan ge geografiska kontext till data i Azure IoT Central plats och mappa alla latitud och longitud koordinaterna för en gatuadress. Du kan bara latitud och longitud kartkoordinater. Azure Maps kan den här funktionen i IoT Central.

Du kan lägga till två typer av platsegenskaper:
- **Platsen som en egenskap på programmet**, vilken lagras enbart i programmet. Enheten inte kan identifiera egenskaper för program.
- **Platsen som en enhetsegenskap**, där enheten rapporter.

#### <a name="add-location-as-an-application-property"></a>Lägg till plats som en egenskap för programmet 
Du kan skapa en plats-egenskap som en egenskap på programmet med hjälp av Azure Maps i tillämpningsprogrammet IoT Central. Exempelvis kan du lägga till Enhetsadressen som installationen. 

1. På den **egenskaper** kontrollerar du att **designläge** är **på**.

   ![Fliken ”Egenskaper” med designläge aktiverad](./media/howto-set-up-template/locationcloudproperty1.png)

2. I biblioteket, Välj **plats**.
3. Konfigurera **visningsnamn**, **fältnamn**, och (valfritt) **startvärde** för platsen. 

   ![”Konfigurera plats” formulär med information om plats](./media/howto-set-up-template/locationcloudproperty2.png)

   Det finns två format som stöds för att lägga till en plats:
   - **Platsen som en adress**
   - **Platsen som koordinater** 

4. Välj **Spara**. 

   ![Platsegenskapen med installationen-adress som lagts till](./media/howto-set-up-template/locationcloudproperty3.png)

En operatör kan nu uppdatera plats värdet i formuläret plats fältet. 

#### <a name="add-location-as-a-device-property"></a>Lägg till plats som en enhetsegenskap för 

Du kan skapa en plats som en enhet som enheten rapporterar. Till exempel om du vill spåra enheten var:

1. På den **egenskaper** kontrollerar du att **designläge** är **på**.

   ![Fliken ”Egenskaper” med designläge aktiverad](./media/howto-set-up-template/locationdeviceproperty1.png)

2. Välj **enhetsegenskap** från biblioteket.
3. Konfigurera namn och fältnamn och välj **plats** som datatyp. 

   > [!NOTE]
   > Fältnamnet måste exakt matcha namnet på egenskapen som enheten rapporterar. 

   ![”Konfigurera egenskaper för enhet” formulär med information om plats](./media/howto-set-up-template/locationdeviceproperty2.png)

Du kan nu när du har konfigurerat din Platsegenskapen [lägga till en karta för att visualisera plats i enheten instrumentpanelen](#add-an-azure-maps-location-in-the-dashboard).

## <a name="commands"></a>Kommandon

Kommandon används för att hantera en enhet. De kan operatörer att omedelbart köra kommandon på enheten. Du kan lägga till flera kommandon till enheten mallen som visas sida vid sida på den **kommandon** för ansvariga att använda. Som builder på enheten har du möjlighet att definiera kommandon enligt dina krav.

Hur skiljer sig ett kommando från en inställning? 

* **Ange**: en inställning är en konfiguration som du vill koppla till en enhet och enheten så att konfigurationen är kvar tills du ändrar den. Till exempel du vill ange temperaturen för din frysfartyg och du vill att det även när frysfartyg startas om. 

* **Kommandot**: du använda kommandon för att omedelbart köra ett kommando på enheten via fjärranslutning från IoT Central. Om en enhet inte är ansluten kommandot timeout och misslyckas. Till exempel att du vill starta om en enhet.  

När du kör ett kommando, kan det vara i ett av tre lägen, beroende på om enheten emot kommandot. 

T.ex, du kan lägga till en ny **Echo** kommando:

![”Konfigurera kommandot” formulär med information om echo](./media/howto-set-up-template/commandsecho.png)

När du har valt **spara**, **Echo** kommandot visas som en panel och är redo att användas för att skapa ett eko av enheten.

När du har skapat en panel kan du testa din nya kommandot.

## <a name="rules"></a>Regler

Regler kan operatörer att övervaka enheter i nära realtid. Regler anropa automatiskt åtgärder som skickar ett e-postmeddelande när regeln utlöses. En typ av regel finns idag:

- **Telemetri regeln**, som utlöses när den valda enhetstelemetrin överskrider ett angivet tröskelvärde. [Mer information om regler för telemetri](howto-create-telemetry-rules.md).

## <a name="dashboard"></a>Instrumentpanel

Instrumentpanelen är operatör kan var finns information om en enhet. Du kan lägga till paneler på den här sidan för att hjälpa operatörer att förstå hur enheten fungerar som en builder. Du kan lägga till flera instrumentpaneler mallen för enheten. Du kan lägga till sex typer av instrumentpaneler: bild, diagram, liggande diagram, KPI: N, inställningar och egenskaper och etikett.

Du kan till exempel lägga till en **inställningar och egenskaper** rutan för att visa ett urval av aktuella värden för egenskaper och inställningar:

![”Konfigurera enhetsinformation” formulär med information om inställningar och egenskaper](./media/howto-set-up-template/dashboardsettingsandpropertiesform.png)

Nu när en operator visar instrumentpanelen, ser den här panelen som visar egenskaper och inställningar på enheten:

![Fliken ”instrumentpanelen” med inställningar som visas och egenskaper för panelen](./media/howto-set-up-template/dashboardtile.png)

### <a name="add-an-azure-maps-location-in-the-dashboard"></a>Lägg till en Azure Maps-plats på instrumentpanelen

Om du har konfigurerat en Platsegenskapen tidigare i [skapa en Platsegenskapen via Azure Maps](#create-a-location-property-through-azure-maps), kan du visualisera platsen med hjälp av en karta i instrumentpanelen för enheten.

1. På den **instrumentpanelen** kontrollerar du att **designläge** är **på**.

   ![Fliken ”instrumentpanelen” med designläge aktiverad](./media/howto-set-up-template/locationcloudproperty4map.png)

2. På instrumentpanelen för enheten, Välj **kartan** från biblioteket. 
3. Ge ett namn och Välj egenskapen location som tidigare har konfigurerats som en del av enhetens egenskaper.

   ![”Konfigurera karta” formulär med information om namn och egenskaper](./media/howto-set-up-template/locationcloudproperty5map.png)

4. Välj **Spara**. Panelen karta visar nu den plats som du har valt. 

   ![Mappa sida vid sida med den valda platsen](./media/howto-set-up-template/locationcloudproperty6map.png) 

Du kan ändra storlek på kartan till din önskad storlek.

Nu när en operator visar instrumentpanelen, kan de se alla instrumentpaneler som du har konfigurerat, inklusive en platskarta.

![Paneler på instrumentpanelen](./media/howto-set-up-template/locationcloudproperty7map.png) 

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du ställer in en mall för enheten i Azure IoT centrala programmet, kan du:

> [!div class="nextstepaction"]
> [Skapa en ny enhet mallversion](howto-version-devicetemplate.md)
