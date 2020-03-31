---
title: Definiera en ny IoT-enhetstyp i Azure IoT Central | Microsoft-dokument
description: I den här artikeln visas hur du skapar en ny Azure IoT-enhetsmall i ditt Azure IoT Central-program. Du definierar telemetri, tillstånd, egenskaper och kommandon för din typ.
author: dominicbetts
ms.author: dobett
ms.date: 12/06/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 6f93d74653aab78e48e613ddf9252a0876548b95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157678"
---
# <a name="define-a-new-iot-device-type-in-your-azure-iot-central-application"></a>Definiera en ny IoT-enhetstyp i ditt Azure IoT Central-program

En enhetsmall är en skiss som definierar egenskaperna och beteendena för en typ av enhet som ansluter till ett Azure IoT Central-program.

En byggare kan till exempel skapa en enhetsmall för en ansluten fläkt med följande egenskaper:

- Skickar temperaturtelemetri
- Egenskapen Plats skickar plats
- Skickar felhändelser för fläktmotor
- Skickar fläktdriftstillstånd
- Tillhandahåller egenskapen skrivbar fläkthastighet
- Ger ett kommando för att starta om enheten
- Ger dig en övergripande bild av enheten via en instrumentpanel

Från den här enhetsmallen kan en operatör skapa och ansluta riktiga fläktenheter. Alla dessa fläktar har mått, egenskaper och kommandon som operatörerna använder för att övervaka och hantera dem. Operatörer använder enhetens instrumentpaneler och formulär för att interagera med fläktenheterna.

> [!NOTE]
> Endast byggare och administratörer kan skapa, redigera och ta bort enhetsmallar. Alla användare kan skapa enheter på sidan **Enheter** från befintliga enhetsmallar.

[IoT Plug and Play (förhandsgranskning)](../../iot-pnp/overview-iot-plug-and-play.md) gör det möjligt för IoT Central att integrera enheter utan att du skriver någon inbäddad enhetskod. Kärnan i IoT Plug and Play (förhandsgranskning) är ett enhetsmodellschema som beskriver enhetsfunktioner. I ett IoT Central-program använder enhetsmallar dessa IoT Plug and Play-modeller (preview).

Som byggare har du flera alternativ för att skapa enhetsmallar:

- Utforma enhetsmallen i IoT Central och implementera sedan dess enhetskapacitetsmodell i enhetskoden.
- Importera en enhetskapacitetsmodell från [Azure Certified for IoT-enhetskatalogen](https://aka.ms/iotdevcat). Lägg sedan till alla molnegenskaper, anpassningar och instrumentpaneler som ditt IoT Central-program behöver.
- Skapa en enhetskapacitetsmodell med hjälp av Visual Studio Code. Implementera enhetskoden från modellen. Importera enhetskapacitetsmodellen manuellt till ditt IoT Central-program och lägg sedan till alla molnegenskaper, anpassningar och instrumentpaneler som ditt IoT Central-program behöver.
- Skapa en enhetskapacitetsmodell med hjälp av Visual Studio Code. Implementera enhetskoden från modellen och anslut din riktiga enhet till ditt IoT Central-program med hjälp av en enhetsanslutning. IoT Central hittar och importerar enhetskapacitetsmodellen från den offentliga databasen åt dig. Du kan sedan lägga till alla molnegenskaper, anpassningar och instrumentpaneler som ditt IoT Central-program behöver i enhetsmallen.

## <a name="create-a-device-template-from-the-device-catalog"></a>Skapa en enhetsmall från enhetskatalogen

Som byggare kan du snabbt börja bygga ut din lösning med hjälp av en IoT Plug and Play -certifierad enhet (preview). Se listan i [Azure IoT-enhetskatalogen](https://catalog.azureiotsolutions.com/alldevices). IoT Central integreras med enhetskatalogen så att du kan importera en enhetskapacitetsmodell från någon av dessa IoT Plug and Play-certifierade enheter (preview). Så här skapar du en enhetsmall från någon av dessa enheter i IoT Central:

1. Gå till sidan **Enhetsmallar** i ditt IoT Central-program.
1. Välj **+ Nytt**och välj sedan någon av IoT Plug and Play -certifierade enheter (förhandsversion) från katalogen. IoT Central skapar en enhetsmall baserat på den här enhetskapacitetsmodellen.
1. Lägg till molnegenskaper, anpassningar eller vyer i enhetsmallen.
1. Välj **Publicera** om du vill göra mallen tillgänglig för operatörer att visa och ansluta enheter.

## <a name="create-a-device-template-from-scratch"></a>Skapa en enhetsmall från grunden

En enhetsmall innehåller:

- En _enhetskapacitetsmodell_ som anger telemetri, egenskaper och kommandon som enheten implementerar. Dessa funktioner är ordnade i ett eller flera gränssnitt.
- _Molnegenskaper_ som definierar information som ditt IoT Central-program lagrar om dina enheter. En molnegenskap kan till exempel registrera det datum då en enhet senast servades. Den här informationen delas aldrig med enheten.
- _Med anpassningar_ kan byggaren åsidosätta några av definitionerna i enhetskapacitetsmodellen. Byggverktyget kan till exempel åsidosätta namnet på en enhetsegenskap. Egenskapsnamn visas i IoT Central-instrumentpaneler och formulär.
- _Med instrumentpaneler och formulär_ kan byggaren skapa ett användargränssnitt som gör det möjligt för operatörer att övervaka och hantera de enheter som är anslutna till ditt program.

Så här skapar du en enhetsmall i IoT Central:

1. Gå till sidan **Enhetsmallar** i ditt IoT Central-program.
1. Välj **+ Ny** > **anpassad**.
1. Ange ett namn på mallen, till exempel **Miljösensor**.
1. Tryck på **Retur**. IoT Central skapar en tom enhetsmall.

## <a name="manage-a-device-template"></a>Hantera en enhetsmall

Du kan byta namn på eller ta bort en mall från mallens startsida.

När du har lagt till en enhetskapacitetsmodell i mallen kan du publicera den. Du kan inte ansluta en enhet baserat på den här mallen så att operatörerna kan se på sidan **Enheter** förrän du har publicerat mallen.

## <a name="create-a-capability-model"></a>Skapa en kapacitetsmodell

Om du vill skapa en enhetskapacitetsmodell kan du:

- Använd IoT Central för att skapa en anpassad modell från grunden.
- Importera en modell från en JSON-fil. En enhetsbyggare kan ha använt Visual Studio-kod för att skapa en enhetskapacitetsmodell för ditt program.
- Välj en av enheterna i enhetskatalogen. Det här alternativet importerar den enhetsfunktionsmodell som tillverkaren har publicerat för den här enheten. En enhetskapacitetsmodell som importeras på det här sätt publiceras automatiskt.

## <a name="manage-a-capability-model"></a>Hantera en kapacitetsmodell

När du har skapat en enhetskapacitetsmodell kan du:

- Lägg till gränssnitt till modellen. En modell måste ha minst ett gränssnitt.
- Redigera modellmetadata, till exempel dess ID, namnområde och namn.
- Ta bort modellen.

## <a name="create-an-interface"></a>Skapa ett gränssnitt

En enhetskapacitet måste ha minst ett gränssnitt. Ett gränssnitt är en återanvändbar samling funktioner.

Så här skapar du ett gränssnitt:

1. Gå till enhetskapacitetsmodellen och välj **+ Lägg till gränssnitt**.

1. På sidan **Välj ett gränssnitt** kan du:

    - Skapa ett anpassat gränssnitt från grunden.
    - Importera ett befintligt gränssnitt från en fil. En enhetsbyggare kan ha använt Visual Studio-kod för att skapa ett gränssnitt för din enhet.
    - Välj ett av standardgränssnitten, till exempel **gränssnittet för enhetsinformation.** Standardgränssnitt anger de funktioner som är gemensamma för många enheter. Dessa standardgränssnitt publiceras av Azure IoT och kan inte versionsutredas eller redigeras.

1. När du har skapat ett gränssnitt väljer du **Redigera identitet** för att ändra visningsnamnet för gränssnittet.

1. Om du väljer att skapa ett anpassat gränssnitt från grunden kan du lägga till enhetens funktioner. Enhetsfunktionerna är telemetri, egenskaper och kommandon.

### <a name="telemetry"></a>Telemetri

Telemetri är en ström av värden som skickas från enheten, vanligtvis från en sensor. En sensor kan till exempel rapportera omgivningstemperaturen.

I följande tabell visas konfigurationsinställningarna för en telemetrifunktion:

| Field | Beskrivning |
| ----- | ----------- |
| Visningsnamn | Visningsnamnet för telemetrivärdet som används på instrumentpaneler och formulär. |
| Namn | Namnet på fältet i telemetrimeddelandet. IoT Central genererar ett värde för det här fältet från visningsnamnet, men du kan välja ditt eget värde om det behövs. |
| Typ av kapacitet | Telemetri. |
| Semantisk typ | Den semantiska typen av telemetri, till exempel temperatur, tillstånd eller händelse. Valet av semantisk typ avgör vilka av följande fält som är tillgängliga. |
| Schema | Datatypen telemetri, till exempel dubbel, sträng eller vektor. De tillgängliga alternativen bestäms av den semantiska typen. Schema är inte tillgängligt för händelse- och tillståndsenmantiska typer. |
| Severity | Endast tillgängligt för händelsesenmantisk typ. Allvarlighetsgraden är **Fel,** **Information**eller **Varning**. |
| Tillståndsvärden | Endast tillgängligt för tillståndssemantisk typ. Definiera möjliga tillståndsvärden, som alla har visningsnamn, namn, uppräkningstyp och värde. |
| Enhet | En enhet för telemetrivärdet, till **%** exempel **mph**, , eller ** &deg;C**. |
| Visningsenhet | En visningsenhet som kan användas på instrumentpaneler och formulär. |
| Kommentar | Eventuella kommentarer om telemetrikapaciteten. |
| Beskrivning | En beskrivning av telemetrikapaciteten. |

### <a name="properties"></a>Egenskaper

Egenskaper representerar point-in-time-värden. En enhet kan till exempel använda en egenskap för att rapportera den måltemperatur som den försöker nå. Du kan ange skrivbara egenskaper från IoT Central.

I följande tabell visas konfigurationsinställningarna för en egenskapsfunktion:

| Field | Beskrivning |
| ----- | ----------- |
| Visningsnamn | Visningsnamnet för egenskapsvärdet som används på instrumentpaneler och formulär. |
| Namn | Namnet på egenskapen. IoT Central genererar ett värde för det här fältet från visningsnamnet, men du kan välja ditt eget värde om det behövs. |
| Typ av kapacitet | Egenskapen. |
| Semantisk typ | Egenskapens semantiska typ, till exempel temperatur, tillstånd eller händelse. Valet av semantisk typ avgör vilka av följande fält som är tillgängliga. |
| Schema | Egenskapsdatatypen, till exempel dubbel, sträng eller vektor. De tillgängliga alternativen bestäms av den semantiska typen. Schema är inte tillgängligt för händelse- och tillståndsenmantiska typer. |
| Skrivbar | Om egenskapen inte kan skrivas kan enheten rapportera egenskapsvärden till IoT Central. Om egenskapen kan skrivas kan enheten rapportera egenskapsvärden till IoT Central och IoT Central kan skicka egenskapsuppdateringar till enheten.
| Severity | Endast tillgängligt för händelsesenmantisk typ. Allvarlighetsgraden är **Fel,** **Information**eller **Varning**. |
| Tillståndsvärden | Endast tillgängligt för tillståndssemantisk typ. Definiera möjliga tillståndsvärden, som alla har visningsnamn, namn, uppräkningstyp och värde. |
| Enhet | En enhet för egenskapsvärdet, **%** till exempel **mph**, eller ** &deg;C**. |
| Visningsenhet | En visningsenhet som kan användas på instrumentpaneler och formulär. |
| Kommentar | Några kommentarer om egenskapen kapacitet. |
| Beskrivning | En beskrivning av egenskapsfunktionen. |

### <a name="commands"></a>Kommandon

Du kan ringa enhetskommandon från IoT Central. Kommandon skickar eventuellt parametrar till enheten och får svar från enheten. Du kan till exempel anropa ett kommando för att starta om en enhet på 10 sekunder.

I följande tabell visas konfigurationsinställningarna för en kommandofunktion:

| Field | Beskrivning |
| ----- | ----------- |
| Visningsnamn | Visningsnamnet för kommandot som används på instrumentpaneler och formulär. |
| Namn | Namnet på kommandot. IoT Central genererar ett värde för det här fältet från visningsnamnet, men du kan välja ditt eget värde om det behövs. |
| Typ av kapacitet | Kommandot. |
| Kommando | `SynchronousExecutionType`. |
| Kommentar | Eventuella kommentarer om kommandofunktionen. |
| Beskrivning | En beskrivning av kommandofunktionen. |
| Förfrågan | Om det är aktiverat, en definition av begäran parametern, inklusive: namn, visningsnamn, schema, enhet och visningsenhet. |
| Svar | Om det är aktiverat, en definition av kommandot svar, inklusive: namn, visningsnamn, schema, enhet och visningsenhet. |

## <a name="manage-an-interface"></a>Hantera ett gränssnitt

Om du inte har publicerat gränssnittet kan du redigera de funktioner som definierats av gränssnittet. När du har publicerat gränssnittet måste du skapa en ny version av enhetsmallen och version av gränssnittet om du vill göra några ändringar. Du kan göra ändringar som inte kräver versionshantering, till exempel visningsnamn eller enheter, i avsnittet **Anpassa.**

Du kan också exportera gränssnittet som en JSON-fil om du vill återanvända det i en annan kapacitetsmodell.

## <a name="add-cloud-properties"></a>Lägga till molnegenskaper

Använd molnegenskaper för att lagra information om enheter i IoT Central. Molnegenskaper skickas aldrig till en enhet. Du kan till exempel använda molnegenskaper för att lagra namnet på kunden som har installerat enheten eller enhetens senaste servicedatum.

I följande tabell visas konfigurationsinställningarna för en molnegenskap:

| Field | Beskrivning |
| ----- | ----------- |
| Visningsnamn | Visningsnamnet för molnegenskapsvärdet som används på instrumentpaneler och formulär. |
| Namn | Namnet på molnegenskapen. IoT Central genererar ett värde för det här fältet från visningsnamnet, men du kan välja ditt eget värde om det behövs. |
| Semantisk typ | Egenskapens semantiska typ, till exempel temperatur, tillstånd eller händelse. Valet av semantisk typ avgör vilka av följande fält som är tillgängliga. |
| Schema | Datatypen för molnegenskapen, till exempel dubbel, sträng eller vektor. De tillgängliga alternativen bestäms av den semantiska typen. |

## <a name="add-customizations"></a>Lägga till anpassningar

Använd anpassningar när du behöver ändra ett importerat gränssnitt eller lägga till IoT Central-specifika funktioner i en funktion. Du kan bara anpassa fält som inte bryter gränssnittskompatibiliteten. Du kan till exempel:

- Anpassa visningsnamnet och enheterna för en funktion.
- Lägg till en standardfärg som ska användas när värdet visas i ett diagram.
- Ange initiala, lägsta och högsta värden för en egenskap.

Du kan inte anpassa funktionsnamnet eller funktionstypen. Om det finns ändringar som du inte kan göra i avsnittet **Anpassa** måste du göra en version av enhetsmallen och gränssnittet för att ändra funktionen.

### <a name="generate-default-views"></a>Generera standardvyer

Att generera standardvyer är ett snabbt sätt att visualisera viktig enhetsinformation. Du har genererat upp till tre standardvyer för enhetsmallen:

- **Kommandon** ger en vy med enhetskommandon och gör det möjligt för operatören att skicka dem till enheten.
- **Översikt ger** en vy med enhetstelemetri, som visar diagram och mått.
- **Om** ger en vy med enhetsinformation som visar enhetsegenskaper.

När du har valt **Generera standardvyer**ser du att de automatiskt har lagts till under avsnittet **Vyer** i enhetsmallen.

## <a name="add-dashboards"></a>Lägga till instrumentpaneler

Lägg till instrumentpaneler i en enhetsmall så att operatörer kan visualisera en enhet med hjälp av diagram och mått. Du kan ha flera instrumentpaneler för en enhetsmall.

Så här lägger du till en instrumentpanel i en enhetsmall:

1. Gå till enhetsmallen och välj **Vyer**.
1. Välj **Visualisera enheten**.
1. Ange ett namn på instrumentpanelen i **Instrumentpanelsnamn**.
1. Lägg till paneler på instrumentpanelen från listan över statiska, egendom, molnegenskap, telemetri och kommandopaneler. Dra och släpp de paneler som du vill lägga till på instrumentpanelen.
1. Om du vill rita flera telemetrivärden på en enda diagrampanel markerar du telemetrivärdena och väljer sedan **Kombinera**.
1. Konfigurera varje panel som du lägger till för att anpassa hur data visas. Du kan göra detta genom att välja kugghjulsikonen eller genom att välja **Ändra konfiguration** på diagrampanelen.
1. Ordna och ändra storlek på panelerna på instrumentpanelen.
1. Spara ändringarna.

### <a name="configure-preview-device-to-view-dashboard"></a>Konfigurera förhandsgranskningsenhet för att visa instrumentpanelen

Om du vill visa och testa instrumentpanelen väljer du **Konfigurera förhandsgranskningsenhet**. På så sätt kan du se instrumentpanelen som din operatör ser den när den har publicerats. Använd det här alternativet för att verifiera att dina vyer visar rätt data. Du kan välja bland följande alternativ:

- Ingen förhandsgranskningsenhet.
- Den riktiga testenheten som du har konfigurerat för enhetsmallen.
- En befintlig enhet i ditt program, med hjälp av enhets-ID.

## <a name="add-forms"></a>Lägga till formulär

Lägg till formulär i en enhetsmall så att operatörer kan hantera en enhet genom att visa och ange egenskaper. Operatörer kan bara redigera molnegenskaper och skrivbara enhetsegenskaper. Du kan ha flera formulär för en enhetsmall.

Så här lägger du till ett formulär i en enhetsmall:

1. Gå till enhetsmallen och välj **Vyer**.
1. Välj **Redigera enhets- och molndata**.
1. Ange ett namn på formuläret i **Formulärnamn**.
1. Välj hur många kolumner som ska användas för att utforma formuläret.
1. Lägg till egenskaper i ett befintligt avsnitt i formuläret eller välj egenskaper och välj **Lägg till avsnitt**. Använd avsnitt för att gruppera egenskaper i formuläret . Du kan lägga till en rubrik i ett avsnitt.
1. Konfigurera varje egenskap i formuläret för att anpassa dess beteende.
1. Ordna egenskaperna i formuläret.
1. Spara ändringarna.

## <a name="publish-a-device-template"></a>Publicera en enhetsmall

Innan du kan ansluta en enhet som implementerar enhetskapacitetsmodellen måste du publicera enhetsmallen.

När du har publicerat en enhetsmall kan du bara göra begränsade ändringar i enhetskapacitetsmodellen. Om du vill ändra ett gränssnitt måste du [skapa och publicera en ny version](./howto-version-device-template.md).

Om du vill publicera en enhetsmall går du till enhetsmallen och väljer **Publicera**.

När du har publicerat en enhetsmall kan en operatör gå till sidan **Enheter** och lägga till verkliga eller simulerade enheter som använder enhetsmallen. Du kan fortsätta att ändra och spara enhetsmallen när du gör ändringar. När du vill skicka ut ändringarna till operatorn för att visa under sidan **Enheter** måste du välja **Publicera** varje gång.


## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

* Skapa en ny IoT-enhetsmall.
* Skapa molnegenskaper.
* Skapa anpassningar.
* Definiera en visualisering för enhetens telemetri.
* Publicera enhetsmallen.

Därefter kan du:

> [!div class="nextstepaction"]
> [Ansluta en enhet](howto-connect-devkit.md)
