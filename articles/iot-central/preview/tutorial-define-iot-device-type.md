---
title: Definiera en ny IoT-enhets typ i Azure IoT Central | Microsoft Docs
description: Den här självstudien visar dig som ett verktyg för att skapa en ny mall för Azure IoT-enheter i ditt Azure IoT Central-program. Du definierar telemetri, tillstånd, egenskaper och kommandon för din typ.
author: rangv
ms.author: rangv
ms.date: 12/09/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 2127bec4d5fdf0d3bf76fb31c548eab98f910d42
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74979027"
---
# <a name="tutorial-define-a-new-iot-device-type-in-your-azure-iot-central-application-preview-features"></a>Självstudie: definiera en ny IoT-enhets typ i ditt Azure IoT Central-program (för hands versions funktioner)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

En enhets mall är en skiss som definierar egenskaper och beteenden för en typ av enhet som ansluter till ett Azure IoT Central-program.

Ett verktyg kan till exempel skapa en enhets mal len för en ansluten fläkt med följande egenskaper:

- Skickar temperatur telemetri
- Skickar plats egenskap
- Skickar fel händelser för fläkt motor
- Skickar fläkt drifts tillstånd
- Tillhandahåller en skrivbar fläkt hastighets egenskap
- Innehåller ett kommando för att starta om enheten
- Ger dig en övergripande vy av enheten via en instrument panel

Med den här enhets mal len kan en operatör skapa och ansluta Real fläkt enheter. Alla dessa fläktar har mått, egenskaper och kommandon som operatörer använder för att övervaka och hantera dem. Operatörer använder enhets instrument paneler och formulär för att interagera med fläkt enheterna.

> [!NOTE]
> Endast konstruktörer och administratörer kan skapa, redigera och ta bort mallar för enheter. Alla användare kan skapa enheter på sidan **enheter** från befintliga enhets mallar.

[IoT plug and Play](../../iot-pnp/overview-iot-plug-and-play.md) gör det möjligt för IoT Central att integrera enheter utan att du behöver skriva någon inbäddad enhets kod. I IoT-Plug and Play är ett modell schema för enhets kapacitet som beskriver enhets funktioner. I ett IoT Central för hands versions program använder enhets mallarna de här IoT-Plug and Play enhets kapacitets modeller.

Som ett verktyg har du flera alternativ för att skapa enhets mallar:

- Utforma enhets mal len i IoT Central och implementera sedan dess enhets kapacitets modell i enhets koden.
- Importera en enhets kapacitets modell från [Azure-certifierad för IoT-katalogen](https://aka.ms/iotdevcat). Lägg sedan till eventuella moln egenskaper, anpassningar och instrument paneler som ditt IoT Central program behöver.
- Skapa en enhets kapacitets modell med hjälp av Visual Studio Code. Implementera din enhets kod från modellen. Importera enhetens kapacitets modell manuellt till IoT Central programmet och Lägg sedan till eventuella moln egenskaper, anpassningar och instrument paneler som ditt IoT Central program behöver.
- Skapa en enhets kapacitets modell med hjälp av Visual Studio Code. Implementera din enhets kod från modellen och Anslut din riktiga enhet till ditt IoT Central program genom att använda en enhets första anslutning. IoT Central hittar och importerar enhets kapacitets modellen från den offentliga lagrings platsen åt dig. Du kan sedan lägga till alla moln egenskaper, anpassningar och instrument paneler som ditt IoT Central program behöver för enhets mal len.

## <a name="prerequisites"></a>Krav

För att slutföra den här självstudien måste du [skapa ett Azure IoT Central-program](quick-deploy-iot-central.md).

## <a name="create-a-device-template-from-the-device-catalog"></a>Skapa en enhets mall från enhets katalogen

Som ett verktyg kan du snabbt börja skapa din lösning med hjälp av en IoT Plug and Play-certifierad enhet. Se listan i [Azure IoT-katalogen](https://catalog.azureiotsolutions.com/alldevices). IoT Central integreras med enhets katalogen så att du kan importera en enhets kapacitets modell från någon av dessa IoT Plug and Play-certifierade enheter. Så här skapar du en enhets mal len från någon av dessa enheter i IoT Central:

1. Gå till sidan **Device templates** i ditt IoT Central-program.
1. Välj **+ ny**och välj sedan någon av IoT-plug and Play certifierade enheter från katalogen. IoT Central skapar en enhets mal len baserat på enhetens kapacitets modell.
1. Lägg till eventuella moln egenskaper, anpassningar eller vyer i din enhets mall.
1. Välj **publicera** för att göra mallen tillgänglig för operatörer för att visa och ansluta enheter.

## <a name="create-a-device-template-from-scratch"></a>Skapa en enhets mal len från grunden

En enhets mall innehåller:

- En _modell för enhets kapacitet_ som anger telemetri, egenskaper och kommandon som enheten implementerar. Dessa funktioner är indelade i ett eller flera gränssnitt.
- _Moln egenskaper_ som definierar information som IoT Central program lagrar om dina enheter. Till exempel kan en moln egenskap registrera datumet då enheten senast servades. Den här informationen delas aldrig med enheten.
- _Anpassningar_ gör att verktyget åsidosätter vissa definitioner i enhetens kapacitets modell. Verktyget kan till exempel åsidosätta namnet på en enhets egenskap. Egenskaps namn visas i IoT Central instrument paneler och formulär.
- _Instrument paneler och formulär_ gör att verktyget kan skapa ett användar gränssnitt som gör att operatörer kan övervaka och hantera de enheter som är anslutna till ditt program.

Så här skapar du en enhets mal len i IoT Central:

1. Gå till sidan **Device templates** i ditt IoT Central-program.
1. Välj **+ ny** > **anpassad**.
1. Ange ett namn för mallen, t. ex. **miljö sensor**.
1. Tryck på **RETUR**. IoT Central skapar en tom enhets mal len.

## <a name="manage-a-device-template"></a>Hantera en enhets mall

Du kan byta namn på eller ta bort en mall från mallens start sida.

När du har lagt till en enhets kapacitets modell i mallen kan du publicera den. Innan du har publicerat mallen kan du inte ansluta en enhet som baseras på den här mallen för dina operatörer att se på sidan **enheter** .

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
    - Välj ett standard gränssnitt, till exempel **enhets informations** gränssnittet. Standard gränssnitt anger de funktioner som är gemensamma för många enheter. Dessa standard gränssnitt publiceras av Azure IoT och kan inte installeras eller redige ras.

1. När du har skapat ett gränssnitt väljer du **Redigera identitet** för att ändra gränssnittets visnings namn.

1. Om du väljer att skapa ett anpassat gränssnitt från grunden kan du lägga till enhetens funktioner. Enhets funktioner är telemetri, egenskaper och kommandon.

### <a name="telemetry"></a>Telemetri

Telemetri är en data ström med värden som skickas från enheten, vanligt vis från en sensor. En sensor kan till exempel rapportera omgivnings temperatur.

I följande tabell visas konfigurations inställningarna för en telemetri-funktion:

| Fält | Beskrivning |
| ----- | ----------- |
| Visningsnamn | Visnings namnet för telemetri-värdet som används på instrument paneler och formulär. |
| Namn | Namnet på fältet i telemetri meddelandet. IoT Central genererar ett värde för det här fältet från visnings namnet, men du kan välja ett eget värde om det behövs. |
| Typ av kapacitet | Telemetridata. |
| Semantiktyp | Den semantiska typen av telemetri, till exempel temperatur, tillstånd eller händelse. Valet av semantisk typ avgör vilka av följande fält som är tillgängliga. |
| Schema | Data typen telemetri, till exempel Double, String eller Vector. De tillgängliga alternativen bestäms av semantisk typ. Schemat är inte tillgängligt för semantiska typer av händelse och tillstånd. |
| Allvarsgrad | Endast tillgängligt för den semantiska händelse typen. Allvarlighets graderna är **fel**, **information**eller **Varning**. |
| Tillstånds värden | Endast tillgängligt för semantisk typ av tillstånd. Definiera möjliga tillstånds värden, som var och en har visnings namn, namn, uppräknings typ och värde. |
| Enhet | En enhet för telemetri-värdet, till exempel **mph**, **%** eller **&deg;C**. |
| Visa enhet | En visnings enhet för användning på instrument paneler och formulär. |
| Kommentar | Eventuella kommentarer om telemetri-funktionerna. |
| Beskrivning | En beskrivning av telemetri-funktionen. |

### <a name="properties"></a>Egenskaper

Egenskaperna representerar tidpunkts värden. En enhet kan till exempel använda en egenskap för att rapportera mål temperaturen som den försöker nå. Du kan ange skrivbara egenskaper från IoT Central.

I följande tabell visas konfigurations inställningarna för en egenskaps funktion:

| Fält | Beskrivning |
| ----- | ----------- |
| Visningsnamn | Visnings namnet för egenskap svärdet som används på instrument paneler och formulär. |
| Namn | Egenskapens namn. IoT Central genererar ett värde för det här fältet från visnings namnet, men du kan välja ett eget värde om det behövs. |
| Typ av kapacitet | Immaterialrätt. |
| Semantiktyp | Den semantiska typen för egenskapen, till exempel temperatur, tillstånd eller händelse. Valet av semantisk typ avgör vilka av följande fält som är tillgängliga. |
| Schema | Egenskaps data typen, t. ex. Double, String eller Vector. De tillgängliga alternativen bestäms av semantisk typ. Schemat är inte tillgängligt för semantiska typer av händelse och tillstånd. |
| Skrivbar | Om egenskapen inte är skrivbar kan enheten rapportera egenskaps värden till IoT Central. Om egenskapen är skrivbar kan enheten rapportera egenskaps värden till IoT Central och IoT Central kan skicka egenskaps uppdateringar till enheten.
| Allvarsgrad | Endast tillgängligt för den semantiska händelse typen. Allvarlighets graderna är **fel**, **information**eller **Varning**. |
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
| Typ av kapacitet | Kommandoprompt. |
| Kommando | `SynchronousExecutionType`. |
| Kommentar | Eventuella kommentarer om kommando funktionen. |
| Beskrivning | En beskrivning av kommando funktionen. |
| Förfrågan | Om aktive rad, en definition av Request-parametern, inklusive: namn, visnings namn, schema, enhet och visnings enhet. |
| Svar | Om aktive rad, en definition av kommando svaret, inklusive: namn, visnings namn, schema, enhet och visnings enhet. |

## <a name="manage-an-interface"></a>Hantera ett gränssnitt

Om du inte har publicerat gränssnittet kan du redigera funktionerna som definierats av gränssnittet. Om du vill göra ändringar när du har publicerat gränssnittet måste du skapa en ny version av enhets mal len och versionen av gränssnittet. Du kan göra ändringar som inte kräver versions hantering, till exempel visnings namn eller enheter, i avsnittet **Anpassa** .

Du kan också exportera gränssnittet som en JSON-fil om du vill återanvända det i en annan funktions modell.

## <a name="add-cloud-properties"></a>Lägg till moln egenskaper

Använd moln egenskaper för att lagra information om enheter i IoT Central. Moln egenskaper skickas aldrig till en enhet. Du kan till exempel använda moln egenskaper för att lagra namnet på kunden som har installerat enheten, eller enhetens senaste service datum.

I följande tabell visas konfigurations inställningarna för en moln egenskap:

| Fält | Beskrivning |
| ----- | ----------- |
| Visningsnamn | Visnings namnet för moln egenskap svärdet som används på instrument paneler och formulär. |
| Namn | Namnet på moln egenskapen. IoT Central genererar ett värde för det här fältet från visnings namnet, men du kan välja ett eget värde om det behövs. |
| Semantiktyp | Den semantiska typen för egenskapen, till exempel temperatur, tillstånd eller händelse. Valet av semantisk typ avgör vilka av följande fält som är tillgängliga. |
| Schema | Data typen Cloud Property, till exempel Double, String eller Vector. De tillgängliga alternativen bestäms av semantisk typ. |

## <a name="add-customizations"></a>Lägg till anpassningar

Använd anpassningar när du behöver ändra ett importerat gränssnitt eller lägga till IoT Central-/regionsspecifika funktioner till en funktion. Du kan bara anpassa fält som inte avbryter gränssnittets kompatibilitet. Du kan till exempel:

- Anpassa visnings namn och enheter för en funktion.
- Lägg till en standard färg som ska användas när värdet visas i ett diagram.
- Ange start-, minimi-och max värden för en egenskap.

Du kan inte anpassa funktions namnet eller funktions typen. Om det finns ändringar som du inte kan göra i avsnittet **Anpassa** , måste du konfigurera din enhets mall och ditt gränssnitt för att ändra funktionen.

### <a name="generate-default-views"></a>Generera standardvyer

Att generera standardvyer är ett snabbt sätt att visualisera viktig enhets information. Du har upp till tre standardvyer som skapats för din enhets mall:

- **Kommandona** visar en vy med enhets kommandon och låter operatören skicka dem till enheten.
- **Översikt** innehåller en vy med enhets telemetri, som visar diagram och mått.
- **Om** visar en vy med enhets information och visar enhets egenskaper.

När du har valt **generera standardvyer**ser du att de har lagts till automatiskt under avsnittet **vyer** i din enhets mall.

## <a name="add-dashboards"></a>Lägg till instrument paneler

Lägg till instrument paneler till en enhets mall för att aktivera operatörer för att visualisera en enhet med hjälp av diagram och mått. Du kan ha flera instrument paneler för en enhets mall.

Lägga till en instrument panel i en enhets mall:

1. Gå till din enhets mall och välj **vyer**.
1. Välj **visualisera enheten**.
1. Ange ett namn på instrument panelen i **instrument panelens namn**.
1. Lägg till paneler på instrument panelen från listan över statiska, egenskaper, moln egenskaper, telemetri och kommando paneler. Dra och släpp de paneler som du vill lägga till på instrument panelen.
1. Om du vill rita flera telemetri värden på en enda diagram panel väljer du telemetri-värden och väljer sedan **kombinera**.
1. Konfigurera varje panel som du lägger till för att anpassa hur data visas. Du kan göra detta genom att välja kugg hjuls ikonen eller genom att välja **ändra konfiguration** på diagram panelen.
1. Ordna och ändra storlek på panelerna på instrument panelen.
1. Spara ändringarna.

### <a name="configure-preview-device-to-view-dashboard"></a>Konfigurera förhands gransknings enheten för att visa instrument panelen

Välj **Konfigurera för hands versions enhet**om du vill visa och testa din instrument panel. På så sätt kan du se instrument panelen när din operatör ser den när den har publicerats. Använd det här alternativet för att kontrol lera att dina vyer visar rätt data. Du kan välja bland följande alternativ:

- Ingen förhands gransknings enhet.
- Den riktiga testen het som du har konfigurerat för din enhets mall.
- En befintlig enhet i programmet med hjälp av enhets-ID: t.

## <a name="add-forms"></a>Lägg till formulär

Lägg till formulär i en enhets mall för att aktivera operatörer för att hantera en enhet genom att visa och ange egenskaper. Operatörer kan bara redigera moln egenskaper och skrivbara enhets egenskaper. Du kan ha flera formulär för en enhets mall.

Så här lägger du till ett formulär i en enhets mal len:

1. Gå till din enhets mall och välj **vyer**.
1. Välj **Redigera enhets-och moln data**.
1. Ange ett namn för ditt formulär i **formulär namn**.
1. Välj hur många kolumner som ska användas för att utforma ditt formulär.
1. Lägg till egenskaper till ett befintligt avsnitt i formuläret eller välj egenskaper och sedan **Lägg till avsnitt**. Använd avsnitt för att gruppera egenskaper i formuläret. Du kan lägga till en rubrik i ett avsnitt.
1. Konfigurera varje egenskap i formuläret för att anpassa dess beteende.
1. Ordna egenskaperna i formuläret.
1. Spara ändringarna.

## <a name="publish-a-device-template"></a>Publicera en enhetsmall

Innan du kan ansluta en enhet som implementerar din enhets kapacitets modell måste du publicera din enhets mall.

När du har publicerat en enhets mall kan du bara göra begränsade ändringar i enhetens kapacitets modell. Om du vill ändra ett gränssnitt måste du [skapa och publicera en ny version](./howto-version-device-template.md).

Om du vill publicera en enhets mal len går du till din enhets mall och väljer **publicera**.

När du har publicerat en enhets mall kan en operatör gå till sidan **enheter** och lägga till antingen verkliga eller simulerade enheter som använder din enhets mall. Du kan fortsätta att ändra och spara din enhets mall när du gör ändringar. När du vill skicka ut dessa ändringar till operatören och visa dem på sidan **enheter** måste du välja **publicera** varje gång.

## <a name="define-a-new-iot-gateway-device-type-preview-features"></a>Definiera en ny enhets typ för IoT Gateway (för hands versions funktioner)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

I den här självstudien får du, som ett verktyg, använda en mall för en gateway-enhet för att definiera en ny typ av IoT-enhet i IoT Central programmet. 

I det här avsnittet skapar du en mall för **Smart skapande** enhet. En enhet för smart skapande Gateway:

* Skickar telemetri, till exempel temperatur och användning.
* Svarar på skrivbara egenskaper när de uppdateras i molnet, till exempel intervall för att skicka telemetri.
* Svarar på kommandon, t. ex. återställning av temperatur.
* Tillåter relationer till andra enhets kapacitets modeller.

### <a name="create-iot-device-templates"></a>Skapa mallar för IoT-enheter

Så här skapar du mallar för IoT-enheter: 

1. I det vänstra navigerings fältet väljer du **enhets mallar**. Välj sedan **+ ny**och välj panel för **IoT-enhet** och beläggnings sensor. Välj **Nästa: anpassa**.

   ![Skärm bild av sidan mallar och alternativ för enhet](./media/tutorial-define-iot-device-type/gateway-downstream-new.png)

1. På sidan **Granska** väljer du **skapa**. 

   ![Skärmbild av granskningssidan](./media/tutorial-define-iot-device-type/gateway-downstream-review.png)

1. En ny enhets mall skapas. 

   ![Skärm bild av ny enhets mall](./media/tutorial-define-iot-device-type/occupancy-sensor.png)

Så här skapar du en enhets mall för S1-sensor:

1. I det vänstra navigerings fältet väljer du **enhets mallar**. Välj sedan **+ ny**och välj panelen **IoT-enhet** och välj beläggnings sensor panel. Välj **Nästa: anpassa**.

   ![Skärm bild av sidan mallar och alternativ för enhet](./media/tutorial-define-iot-device-type/s1-sensor.png)

1. På sidan **Granska** väljer du **skapa**. 

   ![Skärmbild av granskningssidan](./media/tutorial-define-iot-device-type/s1-review.png)

1. En ny enhets mall skapas. 

   ![Skärm bild av ny enhets mall](./media/tutorial-define-iot-device-type/s1-template.png)

## <a name="create-an-iot-gateway-device-template"></a>Skapa en IoT Gateway-enhets mall

Du kan välja att skapa en enhets mal len för IoT Gateway. Gateway-enheten har relationer med underordnade enheter som ansluter till IoT Central via gateway-enheten. 

### <a name="downstream-device-relationships-with-gateway-device"></a>Underordnade enhets relationer med gateway-enhet

IoT-enheter kan ansluta till en IoT gateway-enhet.

![Diagram över relationen mellan gateway-enheter och underordnade enheter](./media/tutorial-define-iot-device-type/gatewaypattern.png)

Som ett verktyg kan du skapa och redigera IoT gateway-enhetens mallar i ditt program. När du har publicerat en enhets mall kan du ansluta riktiga enheter som implementerar enhets mal len.

### <a name="select-a-device-template-type"></a>Välj en typ av enhets mal len 

Så här lägger du till en ny enhets mal len i programmet:

1. I den vänstra rutan väljer du fliken **enhets mallar** .

   ![Skärm bild av sidan mallar för enheter](./media/tutorial-define-iot-device-type/devicetemplate.png)

1. Välj **+ nytt** för att börja skapa en ny enhets mall.

   ![Skärm bild av sidan enhets mallar med ny markerad](./media/tutorial-define-iot-device-type/devicetemplatenew.png)

   ![Skärm bild av sidan Anpassa enhet](./media/tutorial-define-iot-device-type/gateway-review.png)

1. På sidan **Välj Malltyp** väljer du **Azure IoT**och väljer sedan **Nästa: anpassa**.

   ![Skärm bild av sidan Välj Malltyp](./media/tutorial-define-iot-device-type/gateway-customize.png)

1. Markera kryss rutan gateway och välj **skapa**.

   ![Skärm bild av sidan Anpassa enhet med Gateway markerad](./media/tutorial-define-iot-device-type/gateway-review.png)

1. På sidan Granska väljer du **skapa**. 

1. Ange namnet på Gateway-mallen och **mallen för smart skapande Gateway**. Välj den **anpassade** panelen.

1. Lägg till **enhets information**för standard gränssnitt.

### <a name="add-relationships"></a>Lägg till relationer

Du kan lägga till underordnade relationer till enhets kapacitets modeller för enheter som du ansluter till en gateway-enhet.

Skapa relationer till kapacitets modeller för underordnade enheter. Välj **Spara**.

![Skärm bild av mallen för smart Building Gateway, med olika alternativ markerade](./media/tutorial-define-iot-device-type/gateway-occupancy-s1-rel.png)

### <a name="add-cloud-properties"></a>Lägg till moln egenskaper

En enhets mall kan innehålla moln egenskaper. Moln egenskaper finns bara i IoT Central programmet och skickas aldrig till eller tas emot från en enhet.

1. Välj **moln egenskaper** >  **+ Lägg till moln egenskap**. Använd informationen i följande tabell för att lägga till en moln egenskap till din enhets mall.

    | Visningsnamn      | Semantisk typ | Schema |
    | ----------------- | ------------- | ------ |
    | Senaste servicedatum | Inget          | Datum   |
    | Kundnamn     | Inget          | Sträng |

2. Välj **Spara**.

### <a name="add-customizations"></a>Lägg till anpassningar

Använd anpassningar för att ändra ett gränssnitt eller lägga till IoT Central-/regionsspecifika funktioner till en funktion som inte kräver att du har version av enhetens kapacitets modell. Du kan anpassa fält när kapacitets modellen är i ett utkast eller publicerings tillstånd. Du kan bara anpassa fält som inte avbryter gränssnittets kompatibilitet. Du kan till exempel:

- Anpassa visnings namn och enheter för en funktion.
- Lägg till en standard färg som ska användas när värdet visas i ett diagram.
- Ange start-, minimi-och max värden för en egenskap.

Du kan inte anpassa funktions namnet eller funktions typen.

När du är klar med anpassningen väljer du **Spara**.

### <a name="create-views"></a>Skapa vyer

Som ett verktyg kan du anpassa programmet för att visa relevant information om miljö sensor enheten i en operatör. Dina anpassningar gör att operatören kan hantera miljö sensor enheter som är anslutna till programmet. Du kan skapa två typer av vyer för en operator som ska användas för att interagera med enheter:

* Formulär för att visa och redigera egenskaper för enhet och moln.
* Instrument paneler för att visualisera enheter.

### <a name="generate-default-views"></a>Generera standardvyer

Om du väljer **generera standardvyer**kan du generera en **Översikt** och **om** instrument paneler. 

## <a name="publish-a-device-template"></a>Publicera en enhetsmall

Innan du kan skapa en simulerad miljö sensor, eller ansluta en riktig miljö sensor, måste du publicera din enhets mall.

Så här publicerar du en enhets mal len:

1. Gå till din enhets mall från sidan **enhets mallar** .

2. Välj **Publicera**.

3. I dialog rutan **publicera en enhets mall** väljer du **publicera**.

När en enhets mal len har publicerats visas den på sidan **enheter** och i operatorn. I en publicerad enhets mall kan du inte redigera en enhets kapacitets modell utan att skapa en ny version. Du kan dock göra uppdateringar av moln egenskaper, anpassningar och vyer i en publicerad enhets mall. De här uppdateringarna innebär inte att en ny version skapas. När du har gjort några ändringar väljer du **publicera** för att skicka ut ändringarna till din operatör.

## <a name="create-a-gateway-simulated-device"></a>Skapa en simulerad gateway-enhet

I enhets Utforskaren skapar du en simulerad Smart skapande Gateway. 

![Skärm bild av dialog rutan skapa ny enhet](./media/tutorial-define-iot-device-type/smartbuildingdevice.png)

## <a name="create-downstream-simulated-devices"></a>Skapa underordnade simulerade enheter

Skapa en simulerad beläggnings sensor från Device Explorer. 

![Skärm bild av dialog rutan skapa ny enhet](./media/tutorial-define-iot-device-type/occupancydevice.png)

Skapa en simulerad S1-sensor från Device Explorer. 

![Skärm bild av dialog rutan skapa ny enhet](./media/tutorial-define-iot-device-type/s1device.png)

## <a name="add-downstream-devices-relationships-to-a-gateway-device"></a>Lägg till underordnade enhets relationer till en gateway-enhet

Välj S1 sensor och beläggnings sensor och välj **Anslut till gateway**. 

![Skärm bild av besittnings sensor med anslutning till gateway markerad](./media/tutorial-define-iot-device-type/connecttogateway.png)

Välj en mall för gateway-enhet och gateway-enhet och välj **Anslut**.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

* Skapa en ny IoT-gateway som en enhets mall.
* Skapa moln egenskaper.
* Skapa anpassningar.
* Definiera en visualisering för enhetens telemetri.
* Lägg till relationer.
* Publicera din enhets mall.

Sedan kan du:

> [!div class="nextstepaction"]
> [Anslut en enhet](tutorial-connect-pnp-device.md)
