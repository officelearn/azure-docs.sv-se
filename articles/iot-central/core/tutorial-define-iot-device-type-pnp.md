---
title: Definiera en ny IoT-enhets typ i Azure IoT Central | Microsoft Docs
description: Den här självstudien visar dig som ett verktyg för att skapa en ny mall för Azure IoT-enheter i ditt Azure IoT Central-program. Du definierar telemetri, tillstånd, egenskaper och kommandon för din typ.
author: rangv
ms.author: rangv
ms.date: 10/22/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 1f68ddabde58c68a4096fcc7a0b0e1dd8a2ba9e6
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73176198"
---
# <a name="tutorial-define-a-new-iot-device-type-in-your-azure-iot-central-application-preview-features"></a>Självstudie: definiera en ny IoT-enhets typ i ditt Azure IoT Central-program (för hands versions funktioner)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

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

[IoT plug and Play](../../iot-pnp/overview-iot-plug-and-play.md) gör det möjligt för IoT Central att integrera enheter utan att du behöver skriva någon inbäddad enhets kod. I IoT-Plug and Play är ett modell schema för enhets kapacitet som beskriver enhets funktioner. I ett IoT Central för hands versions program använder enhets mallarna de här IoT-Plug and Play enhets kapacitets modeller.

Som ett verktyg har du flera alternativ för att skapa enhets mallar:

- Utforma enhets mal len i IoT Central och implementera sedan dess enhets kapacitets modell i enhets koden.
- Importera en enhets kapacitets modell från [Azure-certifierad för IoT-katalogen](https://aka.ms/iotdevcat) och Lägg sedan till eventuella moln egenskaper, anpassningar och instrument paneler som ditt IoT Central program behöver.
- Skapa en enhets kapacitets modell med Visual Studio Code. Implementera din enhets kod från modellen. Importera enhetens kapacitets modell manuellt till ditt IoT Central program och Lägg sedan till eventuella moln egenskaper, anpassningar och instrument paneler som ditt IoT Central program behöver.
- Skapa en enhets kapacitets modell med Visual Studio Code. Implementera din enhets kod från modellen och Anslut din riktiga enhet till ditt IoT Central program med en enhets första anslutning. IoT Central hittar och importerar enhets kapacitets modellen från den offentliga lagrings platsen åt dig. Du kan sedan lägga till alla moln egenskaper, anpassningar och instrument paneler som ditt IoT Central program behöver för enhets mal len.

## <a name="prerequisites"></a>Krav

Du behöver ett Azure IoT Central-program för att kunna genomföra den här självstudien. Följ den här snabb starten för att [skapa ett Azure IoT Central-program](quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

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
    - Välj ett standard gränssnitt, till exempel **enhets informations** gränssnittet. Standard gränssnitt anger de funktioner som är gemensamma för många enheter. Dessa standard gränssnitt publiceras av Microsoft Azure IoT och kan inte skapas eller redige ras.

1. När du har skapat ett gränssnitt väljer du **Redigera identitet** för att ändra gränssnittets visnings namn.

1. Om du väljer att skapa ett anpassat gränssnitt från grunden kan du lägga till enhetens funktioner. Enhets funktioner är telemetri, egenskaper och kommandon.

### <a name="telemetry"></a>Telemetri

Telemetri är en data ström med värden som skickas från enheten, vanligt vis från en sensor. En sensor kan till exempel rapportera omgivnings temperaturen.

I följande tabell visas konfigurations inställningarna för en telemetri-funktion:

| Fält | Beskrivning |
| ----- | ----------- |
| Visningsnamn | Visnings namnet för telemetri-värdet som används på instrument paneler och formulär. |
| Namn | Namnet på fältet i telemetri meddelandet. IoT Central genererar ett värde för det här fältet från visnings namnet, men du kan välja ett eget värde om det behövs. |
| Funktions typ | Telemetridata. |
| Semantisk typ | Den semantiska typen av telemetri, till exempel temperatur, tillstånd eller händelse. Valet av semantisk typ avgör vilka av följande fält som är tillgängliga. |
| Schema | Data typen telemetri, till exempel Double, String eller Vector. De tillgängliga alternativen bestäms av semantisk typ. Schemat är inte tillgängligt för semantiska typer av händelse och tillstånd. |
| Allvarsgrad | Endast tillgängligt för den semantiska händelse typen. **Fel**, **information**eller **Varning**. |
| Tillstånds värden | Endast tillgängligt för semantisk typ av tillstånd. Definiera möjliga tillstånds värden, som var och en har visnings namn, namn, uppräknings typ och värde. |
| Enhet | En enhet för telemetri-värdet, till exempel **mph**, **%** eller **&deg;C**. |
| Visa enhet | En visnings enhet för användning på instrument paneler och formulär. |
| Kommentar | Eventuella kommentarer om telemetri-funktionerna. |
| Beskrivning | En beskrivning av telemetri-funktionen. |

### <a name="properties"></a>Egenskaper

Egenskaperna representerar punkt i tids värden. En enhet kan till exempel använda en egenskap för att rapportera mål temperaturen som den försöker nå. Du kan ange skrivbara egenskaper från IoT Central.

I följande tabell visas konfigurations inställningarna för en egenskaps funktion:

| Fält | Beskrivning |
| ----- | ----------- |
| Visningsnamn | Visnings namnet för egenskap svärdet som används på instrument paneler och formulär. |
| Namn | Egenskapens namn. IoT Central genererar ett värde för det här fältet från visnings namnet, men du kan välja ett eget värde om det behövs. |
| Funktions typ | Immaterialrätt. |
| Semantisk typ | Den semantiska typen för egenskapen, till exempel temperatur, tillstånd eller händelse. Valet av semantisk typ avgör vilka av följande fält som är tillgängliga. |
| Schema | Egenskaps data typen, t. ex. Double, String eller Vector. De tillgängliga alternativen bestäms av semantisk typ. Schemat är inte tillgängligt för semantiska typer av händelse och tillstånd. |
| Skrivbar | Om egenskapen inte är skrivbar kan enheten rapportera egenskaps värden till IoT Central. Om egenskapen är skrivbar kan enheten rapportera egenskaps värden till IoT Central och IoT Central kan skicka egenskaps uppdateringar till enheten.
| Allvarsgrad | Endast tillgängligt för den semantiska händelse typen. **Fel**, **information**eller **Varning**. |
| Tillstånds värden | Endast tillgängligt för semantisk typ av tillstånd. Definiera möjliga tillstånds värden, som var och en har visnings namn, namn, uppräknings typ och värde. |
| Enhet | En enhet för egenskap svärdet, till exempel **mph**, **%** eller **&deg;C**. |
| Visa enhet | En visnings enhet för användning på instrument paneler och formulär. |
| Kommentar | Kommentarer om egenskaps funktionen. |
| Beskrivning | En beskrivning av egenskaps funktionen. |

### <a name="commands"></a>Kommandon

Du kan anropa enhets kommandon från IoT Central. Kommandon kan skicka parametrar till enheten och få ett svar från enheten. Du kan till exempel anropa ett kommando för att starta om en enhet på 10 sekunder.

I följande tabell visas konfigurations inställningarna för en kommando funktion:

| Fält | Beskrivning |
| ----- | ----------- |
| Visningsnamn | Visnings namnet för kommandot som används på instrument paneler och formulär. |
| Namn | Kommandots namn. IoT Central genererar ett värde för det här fältet från visnings namnet, men du kan välja ett eget värde om det behövs. |
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
| Namn | Namnet på moln egenskapen. IoT Central genererar ett värde för det här fältet från visnings namnet, men du kan välja ett eget värde om det behövs. |
| Semantisk typ | Den semantiska typen för egenskapen, till exempel temperatur, tillstånd eller händelse. Valet av semantisk typ avgör vilka av följande fält som är tillgängliga. |
| Schema | Data typen Cloud Property, till exempel Double, String eller Vector. De tillgängliga alternativen bestäms av semantisk typ. |

## <a name="add-customizations"></a>Lägg till anpassningar

Använd anpassningar när du behöver ändra ett importerat gränssnitt eller lägga till IoT Central-/regionsspecifika funktioner till en funktion. Du kan bara anpassa fält som inte avbryter gränssnittets kompatibilitet. Du kan till exempel:

- Anpassa visnings namn och enheter för en funktion.
- Lägg till en standard färg som ska användas när värdet visas i ett diagram.
- Ange start-, minimi-och max värden för en egenskap.

Du kan inte anpassa funktions namnet eller funktions typen. Om det finns ändringar som du inte kan göra i avsnittet **Anpassa** , måste du konfigurera din enhets mall och ditt gränssnitt för att ändra funktionen.

### <a name="generate-default-views"></a>Generera standardvyer

Att generera standardvyer är ett snabbt sätt att komma igång med visualisering av viktig enhets information. Du kommer att ha upp till tre standardvyer som genereras för din enhets mall:

- **Kommandona** ger en vy med enhets kommandon och gör att operatören kan skicka dem till enheten.
- **Översikten** visar en vy med enhets telemetri, som visar diagram och mått.
- **Om** ger en vy med enhets information och visar enhets egenskaper.

När du har valt **generera standardvyer** ser du att de har lagts till automatiskt under avsnittet **vyer** i din enhets mall.

## <a name="add-dashboards"></a>Lägg till instrument paneler

Lägg till instrument paneler till en enhets mall för att aktivera operatörer för att visualisera en enhet med hjälp av diagram och mått. Du kan ha flera instrument paneler för en enhets mall.

Lägga till en instrument panel i en enhets mall:

- Gå till din enhets mall och välj **vyer**.
- Välj sedan **visualisering av enheten**.
- Ange ett namn på instrument panelen i **instrument panelens namn**.
- Lägg till paneler på instrument panelen från listan över statiska, egenskaper, moln egenskaper, telemetri och kommando paneler. Dra och släpp de paneler som du vill lägga till på instrument panelen.
- Om du vill rita flera telemetri värden på en enda diagram panel väljer du telemetri-värden och väljer sedan **kombinera**.
- Konfigurera varje panel som du lägger till för att anpassa hur den visar data genom att välja kugg hjuls ikonen eller genom att välja knappen **ändra konfiguration** på diagram panelen.
- Ordna och ändra storlek på panelerna på instrument panelen.
- Spara ändringarna.

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

## <a name="define-a-new-iot-gateway-device-type-preview-features"></a>Definiera en ny enhets typ för IoT Gateway (för hands versions funktioner)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Den här självstudien visar dig som ett verktyg som beskriver hur du använder en gateway-mall för att definiera en ny typ av Azure IoT-enhet i ditt Azure IoT Central-program. 

I det här avsnittet skapar du en mall för **Smart skapande** enhet. En enhet för smart skapande Gateway:

* Skickar telemetri som temperatur och beläggning.
* Svarar på skrivbara egenskaper när de uppdateras i molnet, till exempel intervall för att skicka telemetri.
* Svarar på kommandon som att återställa temperatur.
* Tillåter relationer till andra enhets kapacitets modeller

### <a name="create-iot-device-templates"></a>Skapa mallar för IoT-enheter

Du kommer att skapa IoT Device-mallar. 

Klicka på enhets mallar i det vänstra navigerings fönstret, klicka på **+ ny**, Välj **IoT Device** panel och välj beläggnings sensor panel och klicka på **Nästa: anpassa**

![IoT-enhet](./media/tutorial-define-iot-device-type-pnp/gateway-downstream-new.png)

Sidan granskning visas. Klicka på knappen **skapa** . 

![IoT-enhet](./media/tutorial-define-iot-device-type-pnp/gateway-downstream-review.png)

Ny enhets mall skapas. 

![IoT-enhet](./media/tutorial-define-iot-device-type-pnp/occupancy-sensor.png)

Du kommer att skapa en enhets mall för S1-sensor. 

Klicka på enhets mallar i det vänstra navigerings fönstret, klicka på **+ ny**, Välj **IoT Device** panel och välj beläggnings sensor panel och klicka på **Nästa: anpassa**

![IoT-enhet](./media/tutorial-define-iot-device-type-pnp/s1-sensor.png)

Sidan granskning visas. Klicka på knappen **skapa** . 

![Underordnad enhet](./media/tutorial-define-iot-device-type-pnp/s1-review.png)

Ny enhets mall skapas. 

![Underordnad enhet](./media/tutorial-define-iot-device-type-pnp/s1-template.png)

## <a name="create-an-iot-gateway-device-template"></a>Skapa en IoT Gateway-enhets mall

Du kan välja att skapa en enhets mal len för IoT Gateway. Gateway-enheten kommer att ha relationer med underordnade enheter som ansluter till IoT Central via gateway-enheten. 

### <a name="downstream-device-relationships-with-gateway-device"></a>Underordnade enhets relationer med gateway-enhet

IoT-enheter kan ansluta till Azure IoT gateway-enheten 

![Sidan Central program](./media/tutorial-define-iot-device-type-pnp/gatewaypattern.png)

Som ett verktyg kan du skapa och redigera mallar för Azure IoT gateway-enheter i ditt program. När du har publicerat en enhets mall kan du ansluta riktiga enheter som implementerar enhets mal len.

### <a name="select-device-template-type"></a>Välj typ av enhets mal len 

Om du vill lägga till en ny enhets mall i programmet går du till sidan **mallar för enheter** . Det gör du genom att välja fliken **enhets mallar** i det vänstra fönstret.

![Sidan Central program](./media/tutorial-define-iot-device-type-pnp/devicetemplate.png)

Klicka på **+ ny** för att börja skapa en ny enhets mall.

![Enhets mallar – ny](./media/tutorial-define-iot-device-type-pnp/devicetemplatenew.png)

![Val av enhets mallar – Gateway](./media/tutorial-define-iot-device-type-pnp/gateway-review.png)

Du kommer att hamna på sidan Val av Malltyp på enheten. Välj **Azure IoT** -panel och klicka på **Nästa: knappen anpassa** längst ned

Markera kryss rutan gateway och klicka på **skapa** 

![Val av enhets mallar – Gateway](./media/tutorial-define-iot-device-type-pnp/gateway-customize.png)

Du kommer att visas en gransknings sida klickar du på **skapa** 

![Enhets mall – Gateway](./media/tutorial-define-iot-device-type-pnp/gateway-review.png)

Ange namnet på mallen för att **skapa**en gateway för gateway-mallen. Klicka på **anpassad** panel.

Lägg till **enhets information**för standard gränssnitt.

### <a name="add-relationships"></a>Lägg till relationer

Du kan lägga till underordnade relationer till enhets kapacitets modeller för enheter som du ansluter till gateway-enheten.

Skapa relationer till kapacitets modeller för underordnade enheter. Klicka på **Spara**

![Enhets mall – Gateway](./media/tutorial-define-iot-device-type-pnp/gateway-occupancy-s1-rel.png)

### <a name="add-cloud-properties"></a>Lägg till moln egenskaper

En enhets mall kan innehålla moln egenskaper. Moln egenskaper finns bara i IoT Central programmet och skickas aldrig till eller tas emot från en enhet.

1. Välj **moln egenskaper** och sedan **+ Lägg till moln egenskap**. Använd informationen i följande tabell för att lägga till en moln egenskap till din enhets mall.

    | Visningsnamn      | Semantisk typ | Schema |
    | ----------------- | ------------- | ------ |
    | Senaste servicedatum | Inget          | Datum   |
    | Kund namn     | Inget          | Sträng |

2. Välj **Spara** för att spara ändringarna:

### <a name="add-customizations"></a>Lägg till anpassningar

Använd anpassningar när du behöver ändra ett gränssnitt eller lägga till IoT Central-/regionsspecifika funktioner till en funktion som inte kräver att du har version av enhetens kapacitets modell. Du kan anpassa fält när kapacitets modellen är i ett utkast eller publicerings tillstånd. Du kan bara anpassa fält som inte avbryter gränssnittets kompatibilitet. Du kan till exempel:

- Anpassa visnings namn och enheter för en funktion.
- Lägg till en standard färg som ska användas när värdet visas i ett diagram.
- Ange start-, minimi-och max värden för en egenskap.

Du kan inte anpassa funktions namnet eller funktions typen. Klicka på **Spara**

### <a name="create-views"></a>Skapa vyer

Som ett verktyg kan du anpassa programmet för att visa relevant information om miljö sensor enheten i en operatör. Dina anpassningar gör att operatören kan hantera miljö sensor enheter som är anslutna till programmet. Du kan skapa två typer av vyer för en operator som ska användas för att interagera med enheter:

* Formulär för att visa och redigera egenskaper för enhet och moln.
* Instrument paneler för att visualisera enheter.

### <a name="generate-default-views"></a>Generera standardvyer

Klicka på generera standardvyer i den här självstudien. Översikt & om instrument paneler genereras. 

## <a name="publish-device-template"></a>Publicera enhets mal len

Innan du kan skapa en simulerad miljö sensor, eller ansluta en riktig miljö sensor, måste du publicera din enhets mall.

Så här publicerar du en enhets mal len:

1. Gå till din enhets mall från sidan **enhets mallar** .

2. Välj **Publicera**.

3. I dialog rutan **publicera en enhets mall** väljer du **publicera**:

När en enhets mal len har publicerats visas den på sidan **enheter** och i operatorn. I en publicerad enhets mall kan du inte redigera en enhets kapacitets modell utan att skapa en ny version. Du kan dock göra uppdateringar av moln egenskaper, anpassningar och vyer i en publicerad enhets mall utan versions hantering. När du har gjort några ändringar väljer du **publicera** för att skicka ut ändringarna till din operatör.

## <a name="create-gateway-simulated-device"></a>Skapa en simulerad gateway-enhet

I enhets Utforskaren skapar du en simulerad Smart skapande Gateway. 

![Enhets mall – Gateway](./media/tutorial-define-iot-device-type-pnp/smartbuildingdevice.png)

## <a name="create-downstream-simulated-devices"></a>Skapa underordnade simulerade enheter

I enhets Utforskaren skapar du en simulerad besittnings sensor. 

![Enhets mal len-beläggning](./media/tutorial-define-iot-device-type-pnp/occupancydevice.png)

I enhets Utforskaren skapar du en simulerad S1-sensor. 

![Enhets mal len-S1](./media/tutorial-define-iot-device-type-pnp/s1device.png)

## <a name="add-downstream-devices-relationships-to-gateway-device"></a>Lägg till underordnade enhets relationer till gatewayenheten

Välj S1 sensor och beläggnings sensor och klicka på **Anslut till gateway**. 

![Enhets mal len-S1](./media/tutorial-define-iot-device-type-pnp/connecttogateway.png)

Välj mall för gatewayenhet, Gateway enhets instans och klicka på **Anslut**.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

* Skapa en ny IoT-gateway som en enhets mall
* Skapa moln egenskaper.
* Skapa anpassningar.
* Definiera en visualisering för enhetens telemetri.
* Lägg till relationer
* Publicera din enhets mall.

Här är det föreslagna nästa steg:

> [!div class="nextstepaction"]
> [Anslut en enhet](tutorial-connect-pnp-device-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
