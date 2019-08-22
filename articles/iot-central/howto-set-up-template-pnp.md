---
title: Konfigurera en enhets mall i ett Azure IoT Central-program | Microsoft Docs
description: Lär dig hur du konfigurerar en enhets mall med mätningar, inställningar, egenskaper, regler och en instrument panel.
author: sarahhubbard
ms.author: sahubbar
ms.date: 07/23/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 982ebf28a0f99a6eace2448676c934855cd99d1f
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/21/2019
ms.locfileid: "69879739"
---
# <a name="set-up-and-manage-a-device-template-preview-features"></a>Konfigurera och hantera en enhets mall (för hands versions funktioner)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

En enhets mall är en skiss som definierar egenskaper och beteenden för en typ av enhet som ansluter till ett Azure IoT Central-program.

Ett verktyg kan till exempel skapa en enhets mal len för en ansluten fläkt med följande egenskaper:

- Skickar temperatur telemetri
- Skickar plats egenskap
- Skickar fel händelser för fläkt motor
- Skickar fläkt drifts tillstånd
- Skrivbar fläkt hastighets egenskap
- Kommando för att starta om enheten
- Instrument panel som ger dig en övergripande vy av enheten

Med den här enhets mal len kan en operatör skapa och ansluta Real fläkt enheter. Alla dessa fläktar har mått, egenskaper och kommandon som operatörer använder för att övervaka och hantera. Operatörer använder enhets instrument paneler och formulär för att interagera med fläkt enheterna.

> [!NOTE]
> Endast konstruktörer och administratörer kan skapa, redigera och ta bort mallar för enheter. Alla användare kan skapa enheter på sidan **enheter** från befintliga enhets mallar.

[IoT plug and Play](https://aka.ms/iot-pnp-docs) gör det möjligt för IoT Central att integrera enheter utan att du behöver skriva någon inbäddad enhets kod. I IoT-Plug and Play är ett modell schema för enhets kapacitet som beskriver enhets funktioner. I ett IoT Central för hands versions program använder enhets mallarna de här IoT-Plug and Play enhets kapacitets modeller.

Som ett verktyg har du flera alternativ för att skapa enhets mallar:

- Utforma enhets mal len i IoT Central och implementera sedan dess enhets kapacitets modell i enhets koden.
- Importera en enhets kapacitets modell från [Azure-certifierad för IoT-katalogen](https://aka.ms/iotdevcat) och Lägg sedan till eventuella moln egenskaper, anpassningar och instrument paneler som ditt IoT Central program behöver.
- Skapa en enhets kapacitets modell med Visual Studio Code. Implementera din enhets kod från modellen. Importera enhetens kapacitets modell manuellt till ditt IoT Central program och Lägg sedan till eventuella moln egenskaper, anpassningar och instrument paneler som ditt IoT Central program behöver.
- Skapa en enhets kapacitets modell med Visual Studio Code. Implementera din enhets kod från modellen och Anslut din riktiga enhet till ditt IoT Central program med en enhets första anslutning. IoT Central hittar och importerar enhets kapacitets modellen från den offentliga lagrings platsen åt dig. Du kan sedan lägga till alla moln egenskaper, anpassningar och instrument paneler som ditt IoT Central program behöver för enhets mal len.

## <a name="create-a-device-template-from-the-device-catalog"></a>Skapa en enhets mall från enhets katalogen

Som ett verktyg kan du snabbt börja skapa din lösning med hjälp av en IoT Plug and Play-certifierad enhet som anges i [Azure IoT-katalogen](https://catalog.azureiotsolutions.com/alldevices). IoT Central integreras med enhets katalogen så att du kan importera en enhets kapacitets modell från någon av dessa IoT Plug and Play-certifierade enheter. Så här skapar du en enhets mal len från någon av dessa enheter i IoT Central:

1. Gå till sidan **Device templates** i ditt IoT Central-program.
1. Välj **+ ny**och välj sedan någon av IoT-plug and Play certifierade enheter från katalogen som anges nedan. IoT Central skapar en enhets mal len baserat på enhetens kapacitets modell.
1. Lägg till eventuella moln egenskaper, anpassningar eller vyer i din enhets mall.
1. Välj **publicera** för att publicera den här enhets mal len så att den blir tillgänglig för operatörer för att visa och ansluta enheter.

## <a name="create-a-device-template-from-scratch"></a>Skapa en enhets mal len från grunden

En enhets mall innehåller:

- En _modell för enhets kapacitet_ som anger telemetri, egenskaper och kommandon som enheten implementerar. Dessa funktioner är indelade i ett eller flera gränssnitt.
- _Moln egenskaper_ som definierar information som IoT Central program lagrar om dina enheter. Till exempel kan en moln egenskap registrera de data som en enhet senast servade. Den här informationen delas aldrig med enheten.
- _Anpassningar_ gör att verktyget åsidosätter vissa definitioner i enhetens kapacitets modell. Verktyget kan till exempel åsidosätta namnet på en enhets egenskap. Egenskaps namn visas i IoT Central instrument paneler och formulär.
- _Instrument paneler och formulär_ gör att verktyget kan skapa ett användar gränssnitt som gör att operatörer kan övervaka och hantera de enheter som är anslutna till ditt program.

Så här skapar du en enhets mal len i IoT Central:

1. Gå till sidan **Device templates** i ditt IoT Central-program.
1. Välj **+ ny**och välj sedan **anpassad**.
1. Ange ett namn för mallen, t. ex. **miljö sensor**.
1. Tryck på **RETUR**. IoT Central skapar en tom enhets mal len.

## <a name="manage-a-device-template"></a>Hantera en enhets mall

Du kan byta namn på eller ta bort en mall från mallens start sida.

När du har lagt till en enhets kapacitets modell i mallen kan du publicera den. Du kan inte ansluta en enhet som baseras på den här mallen för dina operatörer att se på sidan **enheter** förrän du har publicerat mallen.

## <a name="create-a-capability-model"></a>Skapa en kapacitets modell

För att skapa en enhets kapacitets modell kan du:

- Använd IoT Central för att skapa en anpassad modell från grunden.
- Importera en modell från en JSON-fil. En Device Builder kan använda Visual Studio Code för att skapa en enhets kapacitets modell för ditt program.
- Välj en av enheterna i enhets katalogen. Med det här alternativet importeras enhetens kapacitets modell som tillverkaren har publicerat för den här enheten. En enhets funktions modell som importeras som detta publiceras automatiskt.

## <a name="manage-a-capability-model"></a>Hantera en kapacitets modell

När du har skapat en enhets kapacitets modell kan du:

- Lägg till gränssnitt i modellen. En modell måste ha minst ett gränssnitt.
- Redigera modellens metadata, till exempel ID, namnrymd och namn.
- Ta bort modellen.

## <a name="create-an-interface"></a>Skapa ett gränssnitt

En enhets kapacitet måste ha minst ett gränssnitt. Ett gränssnitt är en återanvändbar samling funktioner.

Så här skapar du ett gränssnitt:

1. Gå till enhetens funktions modell och välj **+ Lägg till gränssnitt**.

1. På sidan **Välj ett gränssnitt** kan du:

    - Skapa ett anpassat gränssnitt från grunden.
    - Importera ett befintligt gränssnitt från en fil. En Device Builder kan använda Visual Studio Code för att skapa ett gränssnitt för enheten.
    - Välj ett standard gränssnitt, till exempel enhets **informations** gränssnittet. Standard gränssnitt anger de funktioner som är gemensamma för många enheter. Dessa standard gränssnitt publiceras av Microsoft Azure IoT och kan inte skapas eller redige ras.

1. När du har skapat ett gränssnitt väljer du **Redigera identitet** för att ändra gränssnittets visnings namn.

1. Om du väljer att skapa ett anpassat gränssnitt från grunden kan du lägga till enhetens funktioner. Enhets funktioner är telemetri, egenskaper och kommandon.

### <a name="telemetry"></a>Telemetri

Telemetri är en data ström med värden som skickas från enheten, vanligt vis från en sensor. En sensor kan till exempel rapportera omgivnings temperaturen.

I följande tabell visas konfigurations inställningarna för en telemetri-funktion:

| Fält | Beskrivning |
| ----- | ----------- |
| Visningsnamn | Visnings namnet för telemetri-värdet som används på instrument paneler och formulär. |
| Name | Namnet på fältet i telemetri meddelandet. IoT Central genererar ett värde för det här fältet från visnings namnet, men du kan välja ett eget värde om det behövs. |
| Funktions typ | Telemetridata. |
| Semantiktyp | Den semantiska typen av telemetri, till exempel temperatur, tillstånd eller händelse. Valet av semantisk typ avgör vilka av följande fält som är tillgängliga. |
| Schema | Data typen telemetri, till exempel Double, String eller Vector. De tillgängliga alternativen bestäms av semantisk typ. Schemat är inte tillgängligt för semantiska typer av händelse och tillstånd. |
| severity | Endast tillgängligt för den semantiska händelse typen. **Fel**, **information**eller **Varning**. |
| Tillstånds värden | Endast tillgängligt för semantisk typ av tillstånd. Definiera möjliga tillstånds värden, som var och en har visnings namn, namn, uppräknings typ och värde. |
| Enhet | En enhet för telemetri-värdet, tillexempel mph **%** , eller  **&deg;C**. |
| Visa enhet | En visnings enhet för användning på instrument paneler och formulär. |
| Kommentar | Eventuella kommentarer om telemetri-funktionerna. |
| Beskrivning | En beskrivning av telemetri-funktionen. |

### <a name="properties"></a>properties

Egenskaperna representerar punkt i tids värden. En enhet kan till exempel använda en egenskap för att rapportera mål temperaturen som den försöker nå. Du kan ange skrivbara egenskaper från IoT Central.

I följande tabell visas konfigurations inställningarna för en egenskaps funktion:

| Fält | Beskrivning |
| ----- | ----------- |
| Visningsnamn | Visnings namnet för egenskap svärdet som används på instrument paneler och formulär. |
| Name | Egenskapens namn. IoT Central genererar ett värde för det här fältet från visnings namnet, men du kan välja ett eget värde om det behövs. |
| Funktions typ | Immaterialrätt. |
| Semantiktyp | Den semantiska typen för egenskapen, till exempel temperatur, tillstånd eller händelse. Valet av semantisk typ avgör vilka av följande fält som är tillgängliga. |
| Schema | Egenskaps data typen, t. ex. Double, String eller Vector. De tillgängliga alternativen bestäms av semantisk typ. Schemat är inte tillgängligt för semantiska typer av händelse och tillstånd. |
| Kan skrivas | Om egenskapen inte är skrivbar kan enheten rapportera egenskaps värden till IoT Central. Om egenskapen är skrivbar kan enheten rapportera egenskaps värden till IoT Central och IoT Central kan skicka egenskaps uppdateringar till enheten.
| severity | Endast tillgängligt för den semantiska händelse typen. **Fel**, **information**eller **Varning**. |
| Tillstånds värden | Endast tillgängligt för semantisk typ av tillstånd. Definiera möjliga tillstånds värden, som var och en har visnings namn, namn, uppräknings typ och värde. |
| Enhet | En enhet för egenskap svärdet, till exempel **mph**, **%** eller  **&deg;C**. |
| Visa enhet | En visnings enhet för användning på instrument paneler och formulär. |
| Kommentar | Kommentarer om egenskaps funktionen. |
| Beskrivning | En beskrivning av egenskaps funktionen. |

### <a name="commands"></a>Kommandon

Du kan anropa enhets kommandon från IoT Central. Kommandon kan skicka parametrar till enheten och få ett svar från enheten. Du kan till exempel anropa ett kommando för att starta om en enhet på 10 sekunder.

I följande tabell visas konfigurations inställningarna för en kommando funktion:

| Fält | Beskrivning |
| ----- | ----------- |
| Visningsnamn | Visnings namnet för kommandot som används på instrument paneler och formulär. |
| Name | Kommandots namn. IoT Central genererar ett värde för det här fältet från visnings namnet, men du kan välja ett eget värde om det behövs. |
| Funktions typ | Kommando |
| Kommando | SynchronousExecutionType. |
| Kommentar | Eventuella kommentarer om kommando funktionen. |
| Beskrivning | En beskrivning av kommando funktionen. |
| Förfrågan | Om aktive rad, en definition av Request-parametern, inklusive: namn, visnings namn, schema, enhet och visnings enhet. |
| Svar | Om aktive rad är en definition av kommando svaret inklusive: namn, visnings namn, schema, enhet och visnings enhet. |

## <a name="manage-an-interface"></a>Hantera ett gränssnitt

Förutsatt att du inte har publicerat gränssnittet kan du redigera funktionerna som definierats av gränssnittet. När gränssnittet har publicerats måste du skapa en ny version av enhets mal len och versionen av gränssnittet för att göra ändringar. Ändringar som inte kräver versions hantering, till exempel visnings namn eller enheter, kan göras i avsnittet **Anpassa** .

Du kan också exportera gränssnittet som en JSON-fil om du vill återanvända det i en annan funktions modell.

## <a name="add-cloud-properties"></a>Lägg till moln egenskaper

Använd moln egenskaper för att lagra information om enheter i IoT Central. Moln egenskaper skickas aldrig till en enhet. Du kan till exempel använda moln egenskaper för att lagra namnet på kunden som har installerat enheten, eller enhetens senaste service datum.

I följande tabell visas konfigurations inställningarna för en moln egenskap:

| Fält | Beskrivning |
| ----- | ----------- |
| Visningsnamn | Visnings namnet för moln egenskap svärdet som används på instrument paneler och formulär. |
| Name | Namnet på moln egenskapen. IoT Central genererar ett värde för det här fältet från visnings namnet, men du kan välja ett eget värde om det behövs. |
| Semantiktyp | Den semantiska typen för egenskapen, till exempel temperatur, tillstånd eller händelse. Valet av semantisk typ avgör vilka av följande fält som är tillgängliga. |
| Schema | Data typen Cloud Property, till exempel Double, String eller Vector. De tillgängliga alternativen bestäms av semantisk typ. |

## <a name="add-customizations"></a>Lägg till anpassningar

Använd anpassningar när du behöver ändra ett importerat gränssnitt eller lägga till IoT Central-/regionsspecifika funktioner till en funktion. Du kan bara anpassa fält som inte avbryter gränssnittets kompatibilitet. Du kan till exempel:

- Anpassa visnings namn och enheter för en funktion.
- Lägg till en standard färg som ska användas när värdet visas i ett diagram.
- Ange start-, minimi-och max värden för en egenskap.

Du kan inte anpassa funktions namnet eller funktions typen. Om det finns ändringar som du inte kan göra i avsnittet **Anpassa** , måste du konfigurera din enhets mall och ditt gränssnitt för att ändra funktionen.

### <a name="generate-default-views"></a>Generera standardvyer

Att generera standardvyer är ett snabbt sätt att komma igång med visualisering av viktig enhets information. Du kommer att ha upp till tre standardvyer som genereras för din enhets mall:

1. **Kommandona** ger en vy med enhets kommandon och gör att operatören kan skicka dem till enheten.
1. **Översikten** visar en vy med enhets telemetri, som visar diagram och mått.
1. **Om** ger en vy med enhets information och visar enhets egenskaper.

När du har valt **generera standardvyer** ser du att de har lagts till automatiskt under avsnittet **vyer** i din enhets mall.

## <a name="add-dashboards"></a>Lägg till instrument paneler

Lägg till instrument paneler till en enhets mall för att aktivera operatörer för att visualisera en enhet med hjälp av diagram och mått. Du kan ha flera instrument paneler för en enhets mall.

Lägga till en instrument panel i en enhets mall:

1. Gå till din enhets mall och välj **vyer**.
1. Välj sedan **visualisering av enheten**.
1. Ange ett namn på instrument panelen i **instrument panelens namn**.
1. Lägg till paneler på instrument panelen från listan över statiska, egenskaper, moln egenskaper, telemetri och kommando paneler. Dra och släpp de paneler som du vill lägga till på instrument panelen.
1. Om du vill rita flera telemetri värden på en enda diagram panel väljer du telemetri-värden och väljer sedan **kombinera**.
1. Konfigurera varje panel som du lägger till för att anpassa hur den visar data genom att välja kugg hjuls ikonen eller genom att välja knappen **ändra konfiguration** på diagram panelen.
1. Ordna och ändra storlek på panelerna på instrument panelen.
1. Spara ändringarna.

### <a name="configure-preview-device-to-view-dashboard"></a>Konfigurera förhands gransknings enheten för att visa instrument panelen

Om du vill visa och testa din instrument panel kan du välja **Konfigurera för hands versions enhet**så att du kan se instrument panelen när den har publicerats. Med det här alternativet kan du kontrol lera att vyerna visar rätt data. Du kan välja från ingen förhands gransknings enhet, den riktiga test enheten som du har konfigurerat för din enhets mall eller en befintlig enhet i ditt program med hjälp av enhets-ID.

## <a name="add-forms"></a>Lägg till formulär

Lägg till formulär i en enhets mall för att aktivera operatörer för att hantera en enhet genom att visa och ange egenskaper. Operatörer kan bara redigera moln egenskaper och skrivbara enhets egenskaper. Du kan ha flera formulär för en enhets mall.

Så här lägger du till ett formulär i en enhets mal len:

1. Gå till din enhets mall och välj **vyer**.
1. Välj sedan **Redigera enhets-och moln data**.
1. Ange ett namn för ditt formulär i **formulär namn**.
1. Välj hur många kolumner som ska användas för att utforma ditt formulär.
1. Lägg till egenskaper till ett befintligt avsnitt i formuläret eller välj egenskaper och sedan **Lägg till avsnitt**. Använd avsnitt för att gruppera egenskaper i formuläret. Du kan lägga till en rubrik i ett avsnitt.
1. Konfigurera varje egenskap i formuläret för att anpassa dess beteende.
1. Ordna egenskaperna i formuläret.
1. Spara ändringarna.

## <a name="publish-a-device-template"></a>Publicera en enhets mall

Innan du kan ansluta en enhet som implementerar din enhets kapacitets modell måste du publicera din enhets mall.

När du har publicerat en enhets mall kan du bara göra begränsade ändringar i enhetens kapacitets modell. Om du vill ändra ett gränssnitt måste du [skapa och publicera en ny version](./howto-version-device-template-pnp.md).

Om du vill publicera en enhets mal len går du till din enhets mall och väljer **publicera**.

När du har publicerat en enhets mall kan en operatör gå till sidan **enheter** och lägga till antingen verkliga eller simulerade enheter som använder din enhets mall. Du kan fortsätta att ändra och spara din enhets mall när du gör ändringar, men när du vill skicka ut dessa ändringar till operatören och visa dem på sidan **enheter** måste du välja **publicera** varje gång.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du konfigurerar en enhets mall i ditt Azure IoT Central-program kan du:

> [!div class="nextstepaction"]
> [Skapa en ny enhets mal len version](howto-version-device-template-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
> [Anslut en MXChip IoT DevKit-enhet till ditt Azure IoT Central-program](howto-connect-devkit-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
