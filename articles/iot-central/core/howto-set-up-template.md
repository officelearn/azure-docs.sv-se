---
title: Konfigurera en enhets mall i ett Azure IoT Central-program | Microsoft Docs
description: Lär dig hur du konfigurerar en enhets mall med mätningar, inställningar, egenskaper, regler och en instrument panel.
author: viv-liu
ms.author: viviali
ms.date: 06/19/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: c4df07174a5d8826acd7682fa3035fcfd201c5c9
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2019
ms.locfileid: "72953101"
---
# <a name="set-up-a-device-template"></a>Konfigurera en enhetsmall

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

En enhets mall är en skiss som definierar egenskaper och beteenden för en typ av enhet som ansluter till ett Azure IoT Central-program.

Ett verktyg kan till exempel skapa en enhets mal len för en ansluten fläkt med följande egenskaper:

- Mätning av temperatur telemetri
- Plats mätning
- Mätning av fel händelse för fläkt motor
- Mätning av fläktens drift tillstånd
- Inställning för fläkt hastighet
- Regler som skickar aviseringar
- Instrument panel som ger dig en övergripande vy av enheten

Med den här enhets mal len kan en operatör skapa och ansluta Real fläkt enheter med namn som **fläkt-1** och **fläkt-2**. Alla dessa fläktar har mått, inställningar, egenskaper, regler och en instrument panel som användare av ditt program kan övervaka och hantera.

> [!NOTE]
> Endast konstruktörer och administratörer kan skapa, redigera och ta bort mallar för enheter. Alla användare kan skapa enheter på sidan **Device Explorer** från befintliga enhets mallar.

## <a name="create-a-device-template"></a>Skapa en enhets mall

1. Gå till sidan **mallar för enheter** .

2. Börja med att välja **+ ny**för att skapa en mall.

3. Kom igång snabbt genom att välja bland de befintliga färdiga mallarna. Annars väljer du **anpassad**, anger ett namn och klickar på **skapa** för att skapa en egen mall från grunden.

   ![Bibliotek för enhets mallar](./media/howto-set-up-template/newtemplate.png)

4. När du skapar en anpassad mall visas **enhets informations** sidan för din nya enhets mall. IoT Central skapar automatiskt en simulerad enhet när du skapar en enhets mall. Med en simulerad enhet kan du testa appens beteende innan du ansluter en riktig enhet.

I följande avsnitt beskrivs var och en av flikarna på sidan **enhets mal len** .

## <a name="measurements"></a>Mått

Mått är de data som kommer från din enhet. Du kan lägga till flera mätningar till din enhets mall för att matcha funktionerna i enheten.

- Mått för **telemetri** är de numeriska data punkter som enheten samlar in över tid. De representeras som en kontinuerlig ström. Ett exempel är temperatur.
- **Händelse** mätningar är tidpunkts data som representerar något av betydelsen på enheten. En allvarlighets grad representerar en händelses betydelse. Ett exempel är ett fläkt motor fel.
- **Tillstånds** mått representerar enhetens eller komponenternas status under en viss tids period. Till exempel kan ett fläkt läge definieras som att ha **drift** och **stoppas** som de två möjliga tillstånden.
- **Plats** mått är de longitud-och latitud-koordinaterna för enheten under en viss tids period. Till exempel kan en fläkt flyttas från en plats till en annan.

### <a name="create-a-telemetry-measurement"></a>Skapa ett mått för telemetri

Om du vill lägga till ett nytt mått för telemetri väljer du **+ ny mätning**, väljer **telemetri** som mått typ och anger informationen i formuläret.

> [!NOTE]
> Fält namnen i enhets mal len måste matcha egenskaps namnen i motsvarande enhets kod för att måttet för telemetri ska visas i programmet när en riktig enhet är ansluten. Gör samma sak när du konfigurerar inställningar, enhets egenskaper och kommandon när du fortsätter att definiera enhets mal len i följande avsnitt.

Du kan till exempel lägga till en ny mätning av temperatur telemetri:

| Visningsnamn        | Fältnamn    |  Enheter    | Min   |Max.|
| --------------------| ------------- |-----------|-------|---|
| Temperatur         | styr          |  degC     |  0    |100|

![Formuläret "skapa telemetri" med information om temperatur mått](./media/howto-set-up-template/measurementsform.png)

När du har valt **Spara**visas **temperatur** måttet i listan över mått. I korthet visas visualiseringen av temperatur data från den simulerade enheten.

När du visar telemetri kan du välja bland följande sammansättnings alternativ: Average, minimum, maximum, sum och Count. **Genomsnitt** väljs som standard agg regering i diagrammet.

> [!NOTE]
> Data typen för måttet telemetri är ett flytt ALS nummer.

### <a name="create-an-event-measurement"></a>Skapa en händelse mätning

Om du vill lägga till en ny händelse mätning väljer du **+ ny mätning** och väljer **händelse** som mått typ. Ange information om formuläret **Skapa händelse** .

Ange **visnings namn**, **fält namn**och **allvarlighets grad** information för händelsen. Du kan välja bland de tre tillgängliga allvarlighets graderna: **fel**, **Varning**och **information**.

Du kan till exempel lägga till en ny **fläkt motor fel** händelse.

| Visningsnamn        | Fältnamn    |  Standard allvarlighets grad |
| --------------------| ------------- |-----------|
| Fel på fläktmotor     | fanmotorerror |  Fel    |

![Formuläret "Skapa händelse" med information om en fläkt motor händelse](./media/howto-set-up-template/eventmeasurementsform.png)

När du har valt **Spara**visas **fläktens motor fel** mått i listan över mått. I korthet visas visualiseringen av händelse data från den simulerade enheten.

Om du vill visa mer information om en händelse väljer du händelse ikonen i diagrammet:

![Information om händelsen "fläkt motor fel"](./media/howto-set-up-template/eventmeasurementsdetail.png)

> [!NOTE]
> Data typen för händelse mätningen är sträng.

### <a name="create-a-state-measurement"></a>Skapa en tillstånds mätning

Om du vill lägga till ett nytt tillstånds mått väljer du knappen **+ ny mätning** och väljer **tillstånd** som mått typ. Ange information i formuläret **skapa tillstånd** .

Ange information om **visnings namn**, **fält namn**och **värden** för tillstånd. Varje värde kan också ha ett visnings namn som ska användas när värdet visas i diagram och tabeller.

Du kan till exempel lägga till ett nytt tillstånd för **fläkt läge** som har två möjliga värden som enheten kan skicka, **arbeta** och **stoppa**.

| Visningsnamn | Fältnamn    |  Värde 1   | Visningsnamn | Värde 2    |Visningsnamn  |
| -------------| ------------- |----------- | -------------| -----------| -------------|
| Fläktläge     | fläktläge       |  1         | Drift    |     0      | Stoppad      |

![Formuläret "redigera tillstånd" med information om fläkt läget](./media/howto-set-up-template/statemeasurementsform.png)

När du har valt **Spara**visas status mått för **fläkt läge** i listan över mått. I korthet visas visualiseringen av tillstånds data från den simulerade enheten.

Om enheten skickar för många data punkter med en liten varaktighet, visas tillstånds måttet med ett annat visuellt objekt. Välj diagrammet för att visa alla data punkter inom den tids perioden i kronologisk ordning. Du kan också begränsa tidsintervallet för att se måttet som ritats i diagrammet.

> [!NOTE]
> Data typen för tillstånds måttet är sträng.

### <a name="create-a-location-measurement"></a>Skapa en plats mätning

Om du vill lägga till en ny plats mätning väljer du **+ ny mätning**, väljer **plats** som mått typ och anger informationen i formuläret **skapa mått** .

Du kan till exempel lägga till en ny mätning av telemetri för platser:

| Visningsnamn        | Fältnamn    |
| --------------------| ------------- |
| Till gångs plats      |  assetloc     |

![Formuläret "skapa plats" med information om plats mått](./media/howto-set-up-template/locationmeasurementsform.png)

När du har valt **Spara**visas **plats** måttet i listan över mått. I korthet visas visualiseringen av plats data från den simulerade enheten.

När du visar plats kan du välja bland följande alternativ: senaste plats och plats historik. **Plats historiken** gäller endast för det valda tidsintervallet.

Data typen för plats mätningen är ett objekt som innehåller longitud, latitud och en valfri höjd. Följande fragment visar JavaScript-strukturen:

```javascript
assetloc: {
  lon: floating point number,
  lat: floating point number,
  alt?: floating point number
}
```

När den riktiga enheten är ansluten uppdateras platsen som du lade till som mått med det värde som skickas av enheten. När du har konfigurerat måttet för din plats kan du [lägga till en karta för att visualisera platsen på enhets instrument panelen](#add-a-location-measurement-in-the-dashboard).

## <a name="settings"></a>Inställningar

Inställningar styr en enhet. De gör det möjligt för operatörer att tillhandahålla indata till enheten. Du kan lägga till flera inställningar till din enhets mall som visas som paneler på fliken **Inställningar** för de operatörer som ska användas. Du kan lägga till många typer av inställningar: tal, text, datum, växling och avsnitts etikett.

Inställningarna kan ha ett av tre tillstånd. Enheten rapporterar dessa tillstånd.

- **Synkroniserad**: enheten har ändrats för att återspegla inställning svärdet.

- **Väntar**: enheten håller på att ändras till inställning svärdet.

- **Fel**: enheten returnerade ett fel.

Du kan till exempel lägga till en ny inställning för fläkt hastighet genom att välja **Inställningar** och ange i den nya **tal** inställningen:

| Visningsnamn  | Fältnamn    |  Enheter  | decimaler |Grund|
| --------------| ------------- |---------| ---------|---- |
| Fläkt hastighet     | fanSpeed      | VARVTAL     | 2        | 0   |

![Formuläret "Konfigurera nummer" med information om hastighets inställningar](./media/howto-set-up-template/settingsform.png)

När du har valt **Spara**visas **fläkt hastigheten** som en panel. En operatör kan använda inställningen på sidan **Device Explorer** för att ändra enhetens fläkt hastighet.

## <a name="properties"></a>Egenskaper

Egenskaper är metadata som är associerade med enheten, till exempel en fast enhets plats och ett serie nummer. Lägg till flera egenskaper till din enhets mall som visas som paneler på fliken **Egenskaper** . En egenskap har en typ som Number, text, date, växling, enhets egenskap, etikett eller en fast plats. En operator anger värden för egenskaper när de skapar en enhet och de kan redigera dessa värden när som helst. Enhets egenskaperna är skrivskyddade och skickas från enheten till programmet. En operatör kan inte ändra enhets egenskaper. När en riktig enhet ansluter uppdateras enhetens egenskaps panel i programmet.

Det finns två kategorier med egenskaper:

- _Enhets egenskaper_ som enheten rapporterar till IoT Central programmet. Enhets egenskaperna är skrivskyddade värden som rapporteras av enheten och uppdateras i programmet när en riktig enhet är ansluten.
- _Program egenskaper_ som lagras i programmet och som kan redige ras av operatören. Program egenskaper lagras endast i programmet och visas aldrig av en enhet.

Du kan till exempel lägga till senaste service datum för enheten som en ny **datum** egenskap (en program egenskap) på fliken **Egenskaper** :

| Visningsnamn  | Fältnamn | Initialt värde   |
| --------------| -----------|-----------------|
| Senaste service      | lastServiced        | 01/29/2019     |

!["Konfigurera senaste tjänst"-formuläret på fliken "egenskaper"](./media/howto-set-up-template/propertiesform.png)

När du har valt **Spara**visas det senaste service datumet för enheten som en panel.

När du har skapat panelen kan du ändra värdet för program egenskapen i **Device Explorer**.

### <a name="create-a-location-property"></a>Skapa en plats egenskap

Du kan ge geografisk kontext till dina plats data i Azure IoT Central och mappa alla latitud-och longitud-koordinater eller en gatuadress. Azure Maps aktiverar den här funktionen i IoT Central.

Du kan lägga till två typer av plats egenskaper:

- **Plats som en program egenskap**, som lagras i programmet. Program egenskaper lagras endast i programmet och visas aldrig av en enhet.
- **Plats som enhets egenskap**, som enheten rapporterar till programmet. Den här typen av egenskap används bäst för en statisk plats.

> [!NOTE]
> Plats som en egenskap spelar inte in en historik. Om historik önskas kan du använda en plats mätning.

#### <a name="add-location-as-an-application-property"></a>Lägg till plats som en program egenskap

Du kan skapa en plats egenskap som en program egenskap genom att använda Azure Maps i IoT Central programmet. Du kan till exempel lägga till installations adressen för enheten:

1. Gå till fliken **Egenskaper** .

2. I biblioteket väljer du **plats**.

3. Konfigurera **visnings namn**, **fält namn**och (valfritt) **initialt värde** för platsen.

    | Visningsnamn  | Fältnamn | Initialt värde |
    | --------------| -----------|---------|
    | Installations adress | installAddress | Microsoft, 1 Microsoft Way, Redmond, WA 98052   |

   ![Formuläret "Konfigurera plats" med information om platsen](./media/howto-set-up-template/locationcloudproperty2.png)

   Det finns två format som stöds för att lägga till en plats:
   - **Plats som en adress**
   - **Plats som koordinater**

4. Välj **Spara**. En operatör kan uppdatera plats svärdet i **Device Explorer**.

#### <a name="add-location-as-a-device-property"></a>Lägg till plats som en enhets egenskap

Du kan skapa en plats egenskap som en enhets egenskap som enheten rapporterar. Om du till exempel vill spåra enhets platsen:

1. Gå till fliken **Egenskaper** .

2. Välj **enhets egenskap** från biblioteket.

3. Konfigurera visnings namn och fält namn och välj **plats** som data typ:

    | Visningsnamn  | Fältnamn | Datatyp |
    | --------------| -----------|-----------|
    | Enhetsplats | deviceLocation | location  |

   > [!NOTE]
   > Fält namnen måste matcha egenskaps namnen i motsvarande enhets kod

   ![Formuläret "Konfigurera enhets egenskaper" med information om platsen](./media/howto-set-up-template/locationdeviceproperty2.png)

När den riktiga enheten är ansluten uppdateras den plats som du har lagt till som enhets egenskap med det värde som skickas av enheten. När du har konfigurerat din plats egenskap kan du [lägga till en karta för att visualisera platsen i enhets instrument panelen](#add-a-location-property-in-the-dashboard).

## <a name="commands"></a>Kommandon

Kommandon används för att fjärrhantera en enhet. De gör det möjligt för operatörer att köra kommandon på enheten. Du kan lägga till flera kommandon till din enhets mall som visas som paneler på fliken **kommandon** för de operatörer som ska användas. Som verktyg för enheten har du flexibiliteten att definiera kommandon enligt dina krav.

Hur skiljer sig ett kommando från en inställning?

- **Inställning**: en inställning är en konfiguration som du vill tillämpa på en enhet. Du vill att enheten ska spara konfigurationen tills du ändrar den. Du kan till exempel ange temperatur för din fryser och du vill att inställningen även när frysningen startar om.

- **Kommando**: du använder kommandon för att direkt köra ett kommando på enheten via en fjärr anslutning från IoT Central. Om en enhet inte är ansluten är kommandot timeout och fungerar inte. Till exempel vill du starta om en enhet.

Du kan till exempel lägga till ett nytt **eko** kommando genom att välja fliken **kommandon** och sedan välja **+ nytt kommando**och ange den nya kommando informationen:

| Visningsnamn  | Fältnamn | Standardvärde för tidsgräns | Datatyp |
| --------------| -----------|---------------- | --------- |
| Ekokommando  | eko       |  30             | text      |

![Formuläret "Konfigurera kommando" med information om eko](./media/howto-set-up-template/commandsecho1.png)

När du har valt **Spara**visas **ECHO** -kommandot som en panel och är redo att användas från **Device Explorer** när den riktiga enheten ansluter. Fält namnen för ditt kommando måste matcha egenskaps namnen i motsvarande enhets kod för att kommandona ska kunna köras.

[Här är en länk till exempel C-enhets kod.](https://github.com/Azure/iot-central-firmware/blob/ad40358906aeb8f2040a822ba5292df866692c16/MXCHIP/mxchip_advanced/src/AzureIOTClient.cpp#L34)

## <a name="rules"></a>Regler

Regler gör det möjligt för operatörer att övervaka enheter i nära real tid. Regler anropar automatiskt åtgärder som att skicka ett e-postmeddelande när regeln utlöses. En typ av regel är tillgänglig idag:

- **Telemetri-regel**, som aktive ras när den valda enhetens telemetri korsar ett angivet tröskelvärde. [Lär dig mer om regler för telemetri](howto-create-telemetry-rules.md).

## <a name="dashboard"></a>Instrumentpanel

Instrument panelen är den plats där en operatör går att se information om en enhet. Som ett verktyg lägger du till paneler på den här sidan för att hjälpa operatörer att förstå hur enheten fungerar. Du kan lägga till många typer av instrument panels paneler, till exempel bild, linje diagram, stapeldiagram, Key Performance Indicator (KPI), inställningar och egenskaper och etikett.

Du kan till exempel lägga till en panel med **Inställningar och egenskaper** för att visa ett urval av de aktuella värdena för inställningar och egenskaper genom att välja fliken **instrument panel** och panelen från biblioteket:

![Formuläret "Konfigurera enhets information" med information om inställningar och egenskaper](./media/howto-set-up-template/dashboardsettingsandpropertiesform1.png)

Nu när en operator visar instrument panelen i **Device Explorer**kan de se panelen.

### <a name="add-a-location-measurement-in-the-dashboard"></a>Lägg till en plats mätning på instrument panelen

Om du har konfigurerat en plats mätning kan du visualisera platsen med en karta i din enhets instrument panel. För plats mått har du möjlighet att rita plats historiken.

1. Gå till fliken **instrument panel** .

1. På instrument panelen för enheten väljer du **Mappa** från biblioteket.

1. Ge kartan en rubrik. I följande exempel finns rubriken **enhetens aktuella plats**. Välj sedan den plats mätning som du tidigare har konfigurerat på fliken **mått** . I följande exempel är måttet **till till gångs plats** valt:

   ![Formuläret "konfigurera mappning" med information om rubrik och egenskaper](./media/howto-set-up-template/locationcloudproperty5map.png)

1. Välj **Spara**. Kart panelen visar nu den plats som du har valt.

Du kan ändra storlek på kart panelen. När en operator visar instrument panelen i **Device Explorer**visas alla paneler i instrument panelen som du har konfigurerat, inklusive en plats karta.

### <a name="add-a-location-property-in-the-dashboard"></a>Lägg till en plats egenskap på instrument panelen

Om du har konfigurerat en plats egenskap kan du visualisera platsen med en karta i din enhets instrument panel.

1. Gå till fliken **instrument panel** .

1. På instrument panelen för enheten väljer du **Mappa** från biblioteket.

1. Ge kartan en rubrik. I följande exempel finns rubriken **enhetens aktuella plats**. Välj sedan den plats egenskap som du tidigare har konfigurerat på fliken **Egenskaper** . I följande exempel väljs mått **enhets platsen** :

   ![Konfigurera kart formulär med information om rubrik och egenskaper](./media/howto-set-up-template/locationcloudproperty6map.png)

1. Välj **Spara**. Kart panelen visar nu den plats som du har valt.

Du kan ändra storlek på kart panelen. När en operator visar instrument panelen i **Device Explorer**visas alla paneler i instrument panelen som du har konfigurerat, inklusive en plats karta.

Mer information om hur du använder paneler i Azure IoT Central finns i [använda paneler i instrument paneler](howto-use-tiles.md).

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du konfigurerar en enhets mall i ditt Azure IoT Central-program kan du:

- [Skapa en ny version av enhets mal len](howto-version-device-template.md)
- [Ansluta en MXChip IoT DevKit-enhet till ditt Azure IoT Central-program](howto-connect-devkit.md)
- [Ansluta ett allmänt klient program till ditt Azure IoT Central-program (Node. js)](howto-connect-nodejs.md)
