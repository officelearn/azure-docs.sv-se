---
title: Konfigurera en mall för enheten i ett program med Azure IoT Central | Microsoft Docs
description: Lär dig hur du ställer in en mall för enheten med mått, inställningar, egenskaper, regler och en instrumentpanel.
author: viv-liu
ms.author: viviali
ms.date: 10/26/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 61bc9da45ac420e5683be1ea3ad253eae9c0ba5a
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2018
ms.locfileid: "50158949"
---
# <a name="set-up-a-device-template"></a>Konfigurera en enhetsmall

En mall för enheten är en skiss som definierar egenskaper och beteenden för en typ av enhet som ansluter till ett Azure IoT Central program.

Hjälpverktyg kan till exempel skapa en mall för enheten för en IoT-anslutna fläkt med:

- Temperaturen som telemetri mätning

- Fläkt meddelar fel händelse mätning

- Fläkt fungerar tillstånd mätning

- Fläkt hastighet

- Egenskapen Location

- Regler som skickar aviseringar

- Instrumentpanel som ger dig en övergripande vy av enheten

Från den här enheten mallen en operatör kan skapa och ansluta riktiga fläkt enheter med namn som **fläkt-1** och **fläkt 2**. Alla dessa fans ha mått, inställningar, egenskaper, regler och en instrumentpanel som användare av ditt program kan övervaka och hantera.

> [!NOTE]
> Endast builders och administratörer kan skapa, redigera och ta bort enheten mallar. Alla användare kan skapa enheter på den **Device Explorer** sida från befintliga mallar för enheten.

## <a name="create-a-device-template"></a>Skapa en mall för enhet

1. Gå till den **Application Builder** sidan.

2. Om du vill skapa en tom mall, Välj **Skapa mall för enheten**, och välj sedan **anpassad**.

3. Ange ett namn (t.ex. kylskåp-1) för den nya mallen för enheten och välj **skapa**.

   ![Med ”kylskåp” som mallnamn på-enhetens informationssida](./media/howto-set-up-template/devicedetailspage.png)

4. Nu kan du på den **enhetsinformation** sida i en ny simulerad enhet. En simulerad enhet skapas automatiskt åt dig när du skapar en mall för enheten. Den rapporterar data och kan styras precis som en riktig enhet.

Nu ska vi titta på flikarna på den **enhetsinformation** sidan.

## <a name="measurements"></a>Mått

Mätning av faktisk användning är de data som kommer från din enhet. Du kan lägga till flera mått till mallen för enheten att matcha funktionerna i din enhet.

- **Telemetri** måtten är numeriska datapunkter som din enhet som samlar in över tid. De är representeras som en löpande ström. Ett exempel är temperatur.
- **Händelsen** måtten är point-in-time-data som representerar något signifikanta på enheten. En allvarlighetsgrad som representerar vikten av en händelse. Ett exempel är ett meddelar fläkt-fel.
- **Tillstånd** mått representerar tillståndet för enheten eller dess komponenter under en viss tidsperiod. Till exempel ett fläkt-läge kan definieras som att ha **Operating** och **stoppad** som två möjliga tillstånd.

### <a name="create-a-telemetry-measurement"></a>Skapa ett telemetri-mått
Om du vill lägga till ett nytt mått i telemetri, Välj **Redigera mall**, och klicka sedan på den **+ ny mätning** knappen. Välj **telemetri** som mätningen skriver och anger informationen den **skapa telemetri** formuläret.

> [!NOTE]
> Fältnamn i mallen enheten måste matcha egenskapsnamnen i motsvarande enhet koden för telemetri-mått som ska visas i programmet när en riktig enhet är ansluten. Göra detsamma när du konfigurerar inställningar och egenskaper för enhet kommandon när du fortsätter att definiera enhet-mall i följande avsnitt.

Du kan exempelvis lägga till en ny temperatur telemetri mätning:
| Visningsnamn        | Fältnamn    |  Enheter    | Min   |Max|
| --------------------| ------------- |-----------|-------|---|
| Temperatur         | Temp          |  degC     |  0    |100|

![”Skapa telemetri” formuläret med information om temperatur mätning](./media/howto-set-up-template/measurementsform.png)

När du har valt **klar**, **temperatur** mått visas i listan över mått. Du kan se visualisering av temperaturdata som den simulerade enheten har skapat i en liten stund. När du skapar en mall för enheten skapas en simulerad enhet från den mall som gör att du kan testa hur ditt program innan en fysisk/real-enhet är ansluten.


> [!NOTE]
  Datatypen för måttet telemetri är en flytande peka tal.

### <a name="create-an-event-measurement"></a>Skapa en händelse
Om du vill lägga till en ny händelse mätning, Välj **Redigera mall**, och klicka sedan på den **+ ny mätning** knappen. Välj **händelse** som mätningen skriver och anger informationen den **Skapa händelse** formuläret.

Ange den **visningsnamn**, **fältnamn**, och **allvarlighetsgrad** information om händelsen. Du kan välja mellan tre tillgängliga nivåer av allvarlighetsgrad: **fel**, **varning**, och **Information**.  

Exempel: du kan lägga till en ny **fläkt meddelar fel** händelse.

| Visningsnamn        | Fältnamn    |  Standard-allvarlighetsgrad | 
| --------------------| ------------- |-----------|
| Fel på fläktmotor     | fanmotorerror |  Fel    | 

![”Skapa händelse” formuläret med information om en fläkt meddelar händelse](./media/howto-set-up-template/eventmeasurementsform.png)

När du har valt **klar**, **fläkt meddelar fel** mått visas i listan över mått. En operatör kan se visualisering av händelsedata som enheten skickar.

![Diagram för mätning av händelse](./media/howto-set-up-template/eventmeasurementschart.png)

Välj ikonen händelse i diagrammet om du vill visa mer information om händelsen.

![Information om händelsen ”fläkt meddelar fel”](./media/howto-set-up-template/eventmeasurementsdetail.png)

> [!NOTE]
  Datatypen för måttet händelse är sträng.

### <a name="create-a-state-measurement"></a>Skapa ett mått för tillstånd
Om du vill lägga till ett nytt tillstånd mått, Välj **Redigera mall**, och klicka sedan på den **+ ny mätning** knappen. Välj **tillstånd** som mätningen skriver och anger informationen den **skapa tillstånd** formuläret.

Ange information för **visningsnamn**, **fältnamn**, och **värden** av tillstånd. Varje värde kan också ha ett namn som ska användas när värdet visas i diagram och tabeller.

Exempel: du kan lägga till en ny **fläkt läge** tillstånd som har två möjliga värden som enheten kan skicka **Operating** och **stoppad**.


| Visningsnamn | Fältnamn    |  Värde 1   | Visningsnamn | Värde 2    |Visningsnamn  | 
| -------------| ------------- |----------- | -------------| -----------| -------------|
| Fläktläge     | fläktläge       |  1         | Drift    |     0      | Stoppad      |

![”Redigeringsläge” formuläret med information om fläkt läge](./media/howto-set-up-template/statemeasurementsform.png)

När du har valt **klar**, **fläkt läge** tillstånd mått visas i listan över mått. Operatören kan se visualisering av tillståndsdata som enheten skickar.

![Statliga mätning diagram](./media/howto-set-up-template/statemeasurementschart.png)

Om enheten skickar för många datapunkter i en liten varaktighet, visas mätningen tillstånd med ett annat visuellt objekt enligt följande skärmbild. Om du klickar på diagrammet visas alla datapunkter inom den tidsperioden i kronologisk ordning. Du kan också begränsa tidsintervall för mätningen ritas i diagrammet.

> [!NOTE]
  Datatypen för måttet tillstånd är sträng.

## <a name="settings"></a>Inställningar

Inställningar styr en enhet. De gör att operatörer för programmet och ange indata till enheten. Du kan lägga till flera inställningar till mallen för enheten som visas som paneler på den **inställningar** fliken för operatörer att använda. Du kan lägga till många typer av inställningar: tal, text, datum, växla, plocklista och områdesetikett. 

Inställningarna kan vara i något av tre tillstånd. Enheten rapporterar tillståndet.

- **Synkroniserad**: enheten har ändrats för att återspegla värdet.

- **Väntande**: enheten för närvarande att inställningens värde.

- **Fel**: enheten har returnerat ett fel.

Du kan till exempel lägga till en ny fläkt hastighet inställning genom att välja **Redigera mall** och ange i den nya **nummer** inställningen:

| Visningsnamn  | Fältnamn    |  Enheter  | Decimaler |Inledande|
| --------------| ------------- |---------| ---------|---- |
| Fläkthastighet     | fanSpeed      | RPM     | 2        | 0   |

![”Konfigurera tal” formuläret med information om inställningar för uppspelningshastighet](./media/howto-set-up-template/settingsform.png)

När du har valt **spara**, **fläkthastighet** inställningen visas som en panel och är redo att användas för att ändra fläkt hastigheten på enheten.

När du har skapat en panel, Välj **klar** i den övre högra delen av skärmen. När den faktiska enheten är ansluten till programmet, ändras inställningens värde till synkroniserade.

![Fliken ”inställningar” med ”designläget” växel för panelen](./media/howto-set-up-template/settingstile.png)

## <a name="properties"></a>Egenskaper

Egenskaperna är enhetsmetadata som är associerat med enheten, till exempel enhetsplats och serienummer. Du kan lägga till flera egenskaper till mallen för enheten som visas som paneler på den **egenskaper** fliken. Du kan lägga till många typer av egenskaper: tal, text, datum, växla, enhetsegenskap, etikett och plats. En operatör kan ange värden för egenskaper när de skapar en enhet och de kan redigera dessa värden när som helst. Dock enhetsegenskaper är skrivskyddade och skickas från enheten till programmet och kan inte ändras av operatorn. När den faktiska enheten är ansluten, kommer egenskapen enhetspanelen att uppdateras i programmet. 

Det finns två kategorier med egenskaper:

- **Enheten** egenskaper som enheten rapporterar att programmet IoT Central. Dessa är skrivskyddade värdena som rapporteras av enheten och kommer att uppdateras i programmet när riktig enhet är ansluten. 
- **Programmet** egenskaper som lagras helt och hållet i programmet och kan redigeras av operatorn. Enheten inte kan identifiera egenskaper för program.

Du kan till exempel lägga till enheternas plats som en ny **Text** egenskapen (en programegenskap) genom att välja **Redigera mall** och ange i den nya egenskapen:

| Visningsnamn  | Fältnamn | Beskär inledande blanksteg  | Trimma avslutande blanksteg  | Skiftlägeskänslighet| Minimilängd | Maxlängd |
| --------------| -----------|---------| ---------|---- |----|----|
| Plats      | LOC        | Av     |  Av     | Blandat  | 0 | 100|

![”Konfigurera texten” formuläret på fliken ”Egenskaper”](./media/howto-set-up-template/propertiesform.png)

När du har valt **spara**, enhetsplats visas som en panel:

![Plats-panel](./media/howto-set-up-template/propertiestile.png)

När du har skapat panelen kan du ändra egenskapsvärdet för program. Börja med att välja **klar** i den övre högra delen av skärmen.

### <a name="create-a-location-property-through-azure-maps"></a>Skapa en Platsegenskapen via Azure Maps
Du kan ge geografisk kontext till dina platsdata i Azure IoT Central och mappa alla latitud och longitud koordinaterna för en gatuadress. Du kan helt enkelt latitud och longitud kartkoordinater. Azure Maps gör det möjligt för den här funktionen i IoT Central.

Du kan lägga till två typer av platsegenskaper:
- **Plats som en programegenskap**, som lagras helt och hållet i programmet. Enheten inte kan identifiera egenskaper för program.
- **Plats som en enhetsegenskap**, som enheten rapporterar till programmet.

#### <a name="add-location-as-an-application-property"></a>Lägg till-plats som en egenskap för programmet 
Du kan skapa en plats-egenskap som en programegenskap med hjälp av Azure Maps i ditt IoT Central-program. Exempelvis kan du lägga till Enhetsadressen som installationen. 

1. På den **egenskaper** fliken **Redigera mall**.

   ![”Egenskaper” fliken med designläget aktiverat](./media/howto-set-up-template/locationcloudproperty1.png)

2. Välj i biblioteket, **plats**.
3. Konfigurera **visningsnamn**, **fältnamn**, och (frivilligt) **ursprungsvärdet** för platsen. 

    | Visningsnamn  | Fältnamn | Initialt värde |
    | --------------| -----------|---------| 
    | Installationen adress | insta_address | Microsoft, 1 Microsoft Way, Redmond, WA 98052   |

   ![”Konfigurera platsen” formuläret med information om plats](./media/howto-set-up-template/locationcloudproperty2.png)

   Det finns två format som stöds för att lägga till en plats:
   - **Platsen som en adress**
   - **Platsen som koordinater** 

4. Välj **spara** och **klar**. En operatör kan nu uppdatera platsvärdet i formuläret för fältet plats. 

#### <a name="add-location-as-a-device-property"></a>Lägg till plats som en enhetsegenskap 

Du kan skapa en plats-egenskap som en enhet som enheten rapporterar. Exempel: Om du vill spåra enhetens plats:

1. På den **egenskaper** fliken **Redigera mall**.

   ![”Egenskaper” fliken med designläget aktiverat](./media/howto-set-up-template/locationdeviceproperty1.png)

2. Välj **enhetsegenskap** från biblioteket.
3. Konfigurera visningsnamn och fältnamn och välj **plats** som datatyp. 

    | Visningsnamn  | Fältnamn | Datatyp |
    | --------------| -----------|-----------| 
    | Enhetsplats | deviceLoc| location  |

   > [!NOTE]
   > Fältnamn måste matcha egenskapsnamnen i motsvarande kod för enhet

   ![”Konfigurera enhetsegenskaper” formuläret med information om plats](./media/howto-set-up-template/locationdeviceproperty2.png)

När den faktiska enheten är ansluten, uppdateras den plats som du har lagt till som en enhetsegenskap med värdet som skickas från enheten. Installationen-adressen, den plats som du har lagt till som en programegenskap är ett redigerbart panel. Nu när du har konfigurerat din Platsegenskapen, kan du [lägga till en karta för att visualisera plats i instrumentpanelen för enheten](#add-an-azure-maps-location-in-the-dashboard).

## <a name="commands"></a>Kommandon

Kommandon används för att hantera en enhet. De gör att operatörer av ditt program att köra kommandon direkt på enheten. Du kan lägga till flera kommandon till mallen för enheten som visas som paneler på den **kommandon** fliken för operatörer att använda. Som enhetens builder har du möjlighet att definiera kommandon efter behov.

Hur skiljer sig ett kommando från en inställning? 

* **Ange**: en är en konfiguration som du vill tillämpa på en enhet och du vill att enheten att spara konfigurationen tills du ändrar den. Till exempel vill du ange temperaturen för din frysfartyg och du vill att även om frysfartyg startas om. 

* **Kommandot**: du använda kommandon för att omedelbart köra ett kommando på enheten via fjärranslutning från IoT Central. Om en enhet inte är ansluten kommandot tidsgränsen och misslyckas. Exempelvis kan du starta om en enhet.  


Exempel: du kan lägga till en ny **Echo** kommandot genom att välja **redigera mallen**, klicka på **+ nytt kommando**, och ange i det nya kommandot:

| Visningsnamn  | Fältnamn | Standardvärde för tidsgräns | Datatyp |
| --------------| -----------|---------------- | --------- | 
| Ekokommando  | eko       |  30             | text      |

![”Konfigurera kommandot” formuläret med information om echo](./media/howto-set-up-template/commandsecho.png)

När du har valt **spara** och **klar**, **Echo** kommandot visas som en panel och är redo att användas för att skapa ett eko av enheten när enheten verkliga är ansluten. Fältnamn för kommandot måste matcha egenskapsnamnen i motsvarande enhet koden för kommandon att kunna köras.


## <a name="rules"></a>Regler

Regler för operatörerna kan övervaka enheter i nära realtid. Regler för anropa automatiskt åtgärder som att skicka ett e-postmeddelande när regeln utlöses. En typ av regel är tillgängliga idag:

- **Telemetri regeln**, som utlöses när den valda enhetstelemetri överskrider ett angivet tröskelvärde. [Mer information om regler för telemetri](howto-create-telemetry-rules.md).

## <a name="dashboard"></a>Instrumentpanel

Instrumentpanelen är där en operatör kan gå för att se information om en enhet. Du kan lägga till paneler på den här sidan för att hjälpa operatörer att förstå hur enheten fungerar som ett verktyg. Du kan lägga till flera paneler på instrumentpanelen till mallen för enheten. Du kan lägga till många typer av paneler som bild, linjediagram, stapeldiagram, indikatorn KPI (Key Performance), inställningar och egenskaper och etiketten.

Du kan till exempel lägga till en **inställningar och egenskaper** rutan för att visa ett urval av de aktuella värdena för inställningar och egenskaper genom att välja **Redigera mall** och panel från biblioteket:

![”Konfigurera enhetsinformation” formuläret med information om inställningar och egenskaper](./media/howto-set-up-template/dashboardsettingsandpropertiesform.png)

När en operatör visar instrumentpanelen, kan de nu se den här panelen som visar egenskaper och inställningar för enheten:

![Fliken ”instrumentpanel” med visas inställningar och egenskaper för panelen](./media/howto-set-up-template/dashboardtile.png)

### <a name="add-an-azure-maps-location-in-the-dashboard"></a>Lägg till en Azure Maps-plats på instrumentpanelen

Om du har konfigurerat en Platsegenskapen tidigare i [skapa en Platsegenskapen via Azure Maps](#create-a-location-property-through-azure-maps), kan du visualisera platsen med hjälp av en karta i instrumentpanelen i enheten.

1. På den **instrumentpanelen** fliken **Redigera mall**.

   ![Fliken ”instrumentpanel” med designläget aktiverat](./media/howto-set-up-template/locationcloudproperty4map.png)

2. På instrumentpanelen för enheten väljer **kartan** från biblioteket. 
3. Ge det en titel. I exemplet nedan har rubriken installationsplats, välj sedan egenskapen location som du tidigare har konfigurerat under fliken Egenskaper. I exemplet nedan, **Installation adress** har valts.

   ![”Konfigurera kartan” formuläret med information om namn och egenskaper](./media/howto-set-up-template/locationcloudproperty5map.png)

4. Välj **Spara**. Panelen tjänstkarta visar nu den plats som du har valt. 

   ![Mappa panel med valda platsen](./media/howto-set-up-template/locationcloudproperty6map.png) 

Du kan ändra storlek på kartan till din önskade storlek. Nu när en operatör visar instrumentpanelen, kan de se alla paneler som du har konfigurerat, inklusive en platskarta.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du ställer in en mall för enheten i Azure IoT Central programmet, kan du:

> [!div class="nextstepaction"]
> [Skapa en ny enhet mallversion](howto-version-devicetemplate.md)
