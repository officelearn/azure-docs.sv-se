---
title: Självstudiekurs - Skapa ett analysprogram i butik i Azure IoT Central
description: Den här självstudien visar hur du skapar ett butiksanalysprogram i IoT Central. Du ska skapa den, anpassa den och lägga till sensorenheter.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.custom:
- iot-storeAnalytics-checkout
- iot-p0-scenario
ms.author: timlt
author: timlt
ms.date: 11/12/2019
ms.openlocfilehash: 6b89b667855d827acfb7c181da014cd0d0b4bfc4
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77022111"
---
# <a name="tutorial-create-an-in-store-analytics-application-in-azure-iot-central"></a>Självstudiekurs: Skapa ett analysprogram i butik i Azure IoT Central



Självstudien visar lösningsbyggare hur du skapar ett Azure IoT Central-analysprogram i butik. Exempelprogrammet är för en butik. Det är en lösning på det gemensamma näringslivets behov av att övervaka och anpassa sig till beläggning och miljöförhållanden.

Exempelprogrammet som du bygger innehåller tre riktiga enheter: en Rigado Cascade 500-gateway och två RuuviTag-sensorer. Självstudien visar också hur du använder den simulerade beläggningssensorn som ingår i programmallen för testning. Rigado C500 gateway fungerar som kommunikationsnav i din ansökan. Den kommunicerar med sensorer i din butik och hanterar deras anslutningar till molnet. RuuviTag är en miljösensor som ger telemetri inklusive temperatur, fuktighet och tryck. Den simulerade beläggningssensorn är ett sätt att spåra rörelse och närvaro i kassan i en butik. 

Den här självstudien innehåller anvisningar för anslutning av Rigado- och RuuviTag-enheter till ditt program. Om du har en annan gateway och sensorer kan du fortfarande följa stegen för att skapa ditt program. Handledningen visar också hur man skapar simulerade RuuviTag sensorer. De simulerade sensorerna gör att du kan bygga programmet om du inte har riktiga enheter. 

Du utvecklar kassan och tillståndsövervakningslösningen i tre delar:

* Skapa programmet och anslut enheter för att övervaka förhållanden
* Anpassa instrumentpanelen så att operatörer kan övervaka och hantera enheter
* Konfigurera dataexport så att butikschefer kan köra analyser och visualisera insikter

I den här självstudiekursen får du lära du dig att:
> [!div class="checklist"]
> * Använda Azure IoT Central **In-Store analytics - kassamall** för att skapa ett butiksprogram
> * Anpassa programinställningarna
> * Skapa och anpassa IoT-enhetsmallar
> * Ansluta enheter till ditt program
> * Lägga till regler och åtgärder för att övervaka villkor

## <a name="prerequisites"></a>Krav

För att slutföra den här självstudieserien behöver du:
* En Azure-prenumeration rekommenderas. Du kan eventuellt använda en kostnadsfri 7-dagars testperiod. Om du inte har en Azure-prenumeration kan du skapa en på [sidan För Azure-registrering](https://aka.ms/createazuresubscription).
* Tillgång till en gateway-enhet och två miljösensorer (du kan eventuellt använda simulerade enheter enligt beskrivningen i självstudien)
* Enhetsmallar för de enheter du använder (mallar tillhandahålls för alla enheter som används i självstudien)

## <a name="create-an-application"></a>Skapa ett program
I det här avsnittet skapar du ett nytt Azure IoT Central-program från en mall. Du kommer att använda det här programmet i hela självstudieserien för att skapa en komplett lösning.

Så här skapar du ett nytt Azure IoT Central-program:

1. Navigera till [Azure IoT Central application manager](https://aka.ms/iotcentral) webbplats.

1. Om du har en Azure-prenumeration loggar du in med de autentiseringsuppgifter du använder för att komma åt den, annars logga in med ett Microsoft-konto:

    ![Ange ditt organisationskonto](./media/tutorial-in-store-analytics-create-app-pnp/sign-in.png)

1. Börja skapa ett nytt Azure IoT Central-program genom att välja **Nytt program**.

1. Välj **Butik**.  På återförsäljarsidan visas flera butiksprogrammallar.

Så här skapar du ett nytt analysutcheckningsprogram i butik:  

1. Välj mallen **För analys i butik – utcheckningsprogram.** Den här mallen innehåller enhetsmallar för alla enheter som används i självstudien förutom RuuviTag-sensorer. Mallen innehåller också en instrumentpanel för operatör för övervakning av kassa- och miljöförhållanden samt enhetsstatus. 

1. Du kan också välja ett eget **programnamn**. Det här programmet är baserat på en fiktiv butik med namnet Contoso. Självstudien använder **programnamnet** *Contoso-utcheckningen*. Programmallen är baserad på det fiktiva företaget Northwind. I den här självstudien använder du Contoso för att lära dig hur du anpassar programmet.

    > [!NOTE]
    > Om du använder ett eget **programnamn**måste du fortfarande använda ett unikt värde för **programmets URL**.

1. Om du har en Azure-prenumeration anger du din *Katalog, Azure-prenumeration och Region*. Om du inte har en prenumeration kan du aktivera **7 dagars kostnadsfri utvärderingsversion** och slutföra den erforderliga kontaktinformationen.  

    Mer information om kataloger och prenumerationer finns i [snabbstarten om att skapa ett program](../core/quick-deploy-iot-central.md).

1. Välj **Skapa**.

    ![Sidan Skapa program i Azure IoT Central](./media/tutorial-in-store-analytics-create-app-pnp/preview-application-template.png)

    ![Faktureringsinformation för Azure IoT Central Skapa program](./media/tutorial-in-store-analytics-create-app-pnp/preview-application-template-billinginfo.png)

## <a name="customize-application-settings"></a>Anpassa programinställningar

Som byggare kan du ändra flera inställningar för att anpassa användarupplevelsen i ditt program. I det här avsnittet väljer du ett fördefinierat programtema. Du kan också lära dig hur du skapar ett anpassat tema och uppdaterar programavbildningen. Med ett anpassat tema kan du ställa in programmets webbläsarfärger, webbläsarikon och programlogotypen som visas i huvudrubriken.

Så här väljer du ett fördefinierat programtema:

1. Välj **Inställningar** på masthuvudet.

    ![Azure IoT Central programinställningar](./media/tutorial-in-store-analytics-create-app-pnp/settings-icon.png)

2. Välj ett nytt **tema**.

3. Välj **Spara**.

I stället för att använda ett fördefinierat tema kan du skapa ett anpassat tema. Om du vill använda en uppsättning exempelbilder för att anpassa programmet och slutföra självstudien hämtar du [Contoso-exempelbilderna](https://github.com/Azure-Samples/iot-central-docs-samples/tree/master/retail).

Så här skapar du ett anpassat tema:

1. Expandera den vänstra rutan, om den inte redan har expanderats.

    ![Vänster fönsterruta för Azure IoT Central](./media/tutorial-in-store-analytics-create-app-pnp/dashboard-expand.png)

1. Välj **Administration > Anpassa programmet**.

1. Använd knappen **Ändra** för att välja en bild att ladda upp som **programlogotyp**. Du kan också ange ett värde för **alternativ text för Logotyp**. 

1. Använd knappen **Ändra** för att välja en **ikonbild för webbläsaren** som ska visas på webbläsarflikar.

1. Du kan också ersätta **standardfärgerna för webbläsare** genom att lägga till HTML-hexadecimala färgkoder. Lägg till *#008575*för **rubriken**.  Lägg till *#A1F3EA*för **accenten**. 

1. Välj **Spara**. 

    ![Anpassad logotyp för Azure IoT Central](./media/tutorial-in-store-analytics-create-app-pnp/select-application-logo.png)

    När du har sparat uppdaterar programmet webbläsarfärgerna, logotypen i masttoppen och webbläsarikonen. 

    ![Azure IoT Central uppdaterade programinställningar](./media/tutorial-in-store-analytics-create-app-pnp/saved-application-settings.png)

Så här uppdaterar du programavbildningen:

1. Välj **Administration > Programinställningar**.

1. Använd knappen **Välj bild** för att välja en bild att ladda upp som programbild. Den här bilden visas på programpanelen på sidan **Mina appar** i IoT Central application manager.

1. Välj **Spara**.

1. Du kan också navigera till vyn **Mina appar** på azure [IoT Central application](https://aka.ms/iotcentral) manager-webbplatsen. Programpanelen visar den uppdaterade programavbildningen.

    ![Azure IoT Central anpassa programavbildning](./media/tutorial-in-store-analytics-create-app-pnp/customize-application-image.png)

## <a name="create-device-templates"></a>Skapa enhetsmallar
Som byggare kan du skapa enhetsmallar som gör att du och programoperatorerna kan konfigurera och hantera enheter. Du skapar en mall genom att skapa en anpassad mall, genom att importera en befintlig mallfil eller genom att importera en mall från Azure IoT-enhetskatalogen. När du har skapat och anpassat en enhetsmall använder du den för att ansluta riktiga enheter till ditt program. Du kan också använda en enhetsmall för att generera simulerade enheter för testning.

Programmallen **För butiksanalys – utcheckning** har enhetsmallar för flera enheter.  Det finns enhetsmallar för två av de tre enheter som du använder i programmet. RuuviTag-enhetsmallen ingår inte i programmallen **För butiksanalys – utcheckning.** I det här avsnittet lägger du till en enhetsmall för RuuviTag-sensorer i ditt program.

Så här lägger du till en RuuviTag-enhetsmall i ditt program:

1. Välj **Enhetsmallar** i den vänstra rutan.

1. Välj **+ Ny** om du vill skapa en ny enhetsmall.

1. Hitta och välj **RuuviTag-sensorenhetsmallen** i Azure IoT-enhetskatalogen. 

1. Välj **Nästa: Anpassa**.

    ![Azure IoT Central RuuviTag sensor enhetsmall](./media/tutorial-in-store-analytics-create-app-pnp/ruuvitag-device-template.png)

1. Välj **Skapa**. Programmet lägger till RuuviTag-enhetsmallen.

1. Välj **Enhetsmallar** i den vänstra rutan. På sidan visas alla enhetsmallar som ingår i programmallen och den RuuviTag-enhetsmall som du just har lagt till.

    ![Azure IoT Central RuuviTag sensor enhetsmall](./media/tutorial-in-store-analytics-create-app-pnp/device-templates-list.png)

## <a name="customize-device-templates"></a>Anpassa enhetsmallar
Du kan anpassa enhetsmallarna i programmet på tre sätt. Först anpassar du de inbyggda gränssnitten på dina enheter genom att ändra enhetsfunktionerna. Med en temperatursensor kan du till exempel ändra information som visningsnamnet för temperaturgränssnittet, datatypen, måttenheterna och lägsta och högsta driftsområden. 

För det andra anpassar du enhetsmallarna genom att lägga till molnegenskaper. Molnegenskaper är inte en del av de inbyggda enhetsfunktionerna. Molnegenskaper är anpassade data som ditt Azure IoT Central-program skapar, lagrar och associerar med dina enheter. Ett exempel på en molnegenskap kan vara ett beräknat värde, eller metadata, till exempel en plats som du vill associera med en uppsättning enheter. 

För det tredje anpassar du enhetsmallar genom att skapa anpassade vyer. Vyer är ett sätt för operatörer att visualisera telemetri och metadata för dina enheter, till exempel enhetsmått och hälsa.

Här använder du de två första metoderna för att anpassa enhetsmallen för dina RuuviTag-sensorer. Information om hur du skapar vyer för dina sensorer finns i [snabbstarten Lägg till en simulerad enhet i din IoT Central-program.](../core/quick-create-pnp-device.md)

Så här anpassar du de inbyggda gränssnitten för RuuviTag-enhetsmallen:

1. Välj **Enhetsmallar** i den vänstra rutan. 

1. Välj mall för RuuviTag-sensorer. 

1. Dölj den vänstra rutan. Sammanfattningsvyn för mallen visar enhetsfunktionerna.

    ![Sammanfattningsvy för Azure IoT Central RuuviTag-enhetsmall](./media/tutorial-in-store-analytics-create-app-pnp/ruuvitag-device-summary-view.png)

1. Välj **Anpassa** på menyn i RuuviTag-enhetsmallen. 

1. Bläddra i listan över funktioner `humidity` och hitta telemetritypen. Det är radobjektet med fuktens redigerbara **visningsnamnsvärde** *.*

I följande steg kan `humidity` du anpassa telemetritypen för RuuviTag-sensorerna. Du kan också anpassa några av de andra telemetrityperna.

För `humidity` telemetritypen gör du följande ändringar:

1. Markera kontrollen **Expandera** om du vill expandera schemainformationen för raden.

1. Uppdatera värdet **Visningsnamn** från *fuktighet* till ett anpassat värde, till exempel *Relativ luftfuktighet*.

1. Ändra alternativet **Semantisk typ** från *Ingen* till *Luftfuktighet*.  Du kan också ange schemavärden för fukttelemetritypen i den utökade schemavyn. Med schemainställningar kan du skapa detaljerade valideringskrav för de data som sensorerna spårar. Du kan till exempel ange lägsta och högsta driftsintervallvärden för ett visst gränssnitt.

1. Välj **Spara** för att spara ändringarna.

    ![Anpassning av Azure IoT Central RuuviTag-enhetsmall](./media/tutorial-in-store-analytics-create-app-pnp/ruuvitag-device-template-customize.png)

Så här lägger du till en molnegenskap i en enhetsmall i programmet:

1. Välj **Molnegenskaper** på menyn För RuuviTag-enhetsmall.

1. Välj **Lägg till molnegenskap**. 

Ange följande värden för att skapa en anpassad egenskap för att lagra platsen för varje enhet:

1. Ange värdet *Plats* för **visningsnamnet**. Det här värdet kopieras automatiskt till fältet **Namn,** som är ett eget namn för egenskapen. Du kan använda det kopierade värdet eller ändra det.

1. Välj *Sträng* i listrutan **Schema.** Med en strängtyp kan du associera en platsnamnssträng med valfri enhet baserat på mallen. Du kan till exempel associera ett område i en butik med varje enhet. Du kan också ange den **semantiska typen** av din egendom till *Plats*, och detta ställer automatiskt **in schemat** till *Geopoint*. Det gör att du kan associera GPS-koordinater med en enhet. 

1. Ställ in **minsta längd** till *2*. 

1. Ställ in **Trimma blanktecken** **på På**.

1. Välj **Spara** om du vill spara din anpassade molnegenskap.

    ![Anpassning av Azure IoT Central RuuviTag-enhetsmall](./media/tutorial-in-store-analytics-create-app-pnp/ruuvitag-device-template-cloud-property.png)

1. Välj **Publicera**. 

    Om du publicerar en enhetsmall blir den synlig för programoperatorer. När du har publicerat en mall använder du den för att generera simulerade enheter för testning eller för att ansluta riktiga enheter till ditt program. Om du redan har enheter som är anslutna till ditt program, skickar publiceringen av en anpassad mall ändringarna till enheterna.

## <a name="add-devices"></a>Lägg till enheter
När du har skapat och anpassat enhetsmallar är det dags att lägga till enheter. 

För den här självstudien använder du följande uppsättning riktiga och simulerade enheter för att skapa programmet:
- En riktig Rigado C500 gateway
- Två riktiga RuuviTag sensorer
- En simulerad **beläggningssensor.** Den simulerade sensorn ingår i programmallen, så du behöver inte skapa den. 

> [!NOTE]
> Om du inte har riktiga enheter kan du fortfarande slutföra den här självstudien genom att skapa simulerade RuuviTag-sensorer. Följande anvisningar inkluderar steg för att skapa en simulerad RuuviTag. Du behöver inte skapa en simulerad gateway.

Fyll stegen i följande två artiklar för att ansluta en riktig Rigado gateway och RuuviTag sensorer. När du är klar går du tillbaka till den här självstudien. Eftersom du redan har skapat enhetsmallar i den här självstudien behöver du inte skapa dem igen i följande anvisningar.

- Information om hur du ansluter en Rigado-gateway finns i [Anslut en Rigado Cascade 500 till ditt Azure IoT Central-program](../core/howto-connect-rigado-cascade-500.md?toc=/azure/iot-central/retail/toc.json&bc=/azure/iot-central/retail/breadcrumb/toc.json).
- Information om hur du ansluter RuuviTag-sensorer finns i [Anslut en RuuviTag-sensor till ditt Azure IoT Central-program](../core/howto-connect-ruuvi.md?toc=/azure/iot-central/retail/toc.json&bc=/azure/iot-central/retail/breadcrumb/toc.json). Du kan också använda dessa anvisningar för att skapa två simulerade sensorer om det behövs.

## <a name="add-rules-and-actions"></a>Lägga till regler och åtgärder
Som en del av att använda sensorer i ditt Azure IoT Central-program för att övervaka villkor kan du skapa regler för att köra åtgärder när vissa villkor är uppfyllda. En regel är associerad med en enhetsmall och en eller flera enheter och innehåller villkor som måste uppfyllas baserat på enhetstelemetri eller händelser. En regel har också en eller flera associerade åtgärder. Åtgärderna kan omfatta att skicka e-postmeddelanden eller utlösa en webhook-åtgärd för att skicka data till andra tjänster. Mallen **För analys i butik – utcheckningsprogram** innehåller vissa fördefinierade regler för enheterna i programmet.

I det här avsnittet skapar du en ny regel som kontrollerar den maximala relativa fuktighetsnivån baserat på RuuviTag-sensorelemetrin. Du lägger till en åtgärd i regeln så att om luftfuktigheten överskrider den maximala, skickar programmet e-post. 

Så här skapar du en regel: 

1. Expandera den vänstra rutan.

1. Välj **regler**.

1. Välj **+ Nytt**.

1. Ange *fuktighetsnivå* som namnet på regeln. 

1. Välj RuuviTag-enhetsmallen i **Scopes**. Regeln som du definierar gäller för alla sensorer baserat på mallen. Du kan också skapa ett filter som endast skulle tillämpa regeln på en definierad delmängd av sensorerna. 

1. Välj `Relative humidity` som **telemetri**. Det är enhetskapaciteten som du har anpassat i ett tidigare steg.

1. Välj `Is greater than` som **operator**. 

1. Ange en typisk övre nivå inomhus luftfuktighet för din miljö som **värde**. Ange till exempel *65*. Du har ställt in ett villkor för din regel som uppstår när relativ fuktighet i en RuuviTag verklig eller simulerad sensor överstiger detta värde. Du kan behöva justera värdet uppåt eller nedåt beroende på det normala fuktighetsområdet i din miljö.  

   ![Azure IoT Central lägger till regelvillkor](./media/tutorial-in-store-analytics-create-app-pnp/rules-add-conditions.png)

Så här lägger du till en åtgärd i regeln:

1. Välj **+ E-post**. 

1. Ange *meddelande om hög luftfuktighet* som det egna **visningsnamnet** för åtgärden. 

1. Ange den e-postadress som är kopplad till ditt konto i **Till**. Om du använder ett annat e-postmeddelande måste adressen du använder vara för en användare som har lagts till i programmet. Användaren måste också logga in och ut minst en gång.

1. Du kan också skriva en anteckning som ska inkluderas i texten i e-postmeddelandet.

1. Välj **Klar** för att slutföra åtgärden.

   ![Azure IoT Central lägger till åtgärder i regler](./media/tutorial-in-store-analytics-create-app-pnp/rules-add-action.png)

1. Välj **Spara** om du vill spara och aktivera den nya regeln. 

    Inom några minuter ska det angivna e-postkontot börja ta emot e-postmeddelanden. Programmet skickar e-post varje gång en sensor anger att luftfuktigheten överskred värdet i ditt tillstånd.

## <a name="next-steps"></a>Nästa steg
I den här självstudiekursen lärde du dig att:

* Använda Azure IoT Central **In-Store analytics - kassamall** för att skapa ett butiksprogram
* Anpassa programinställningarna
* Skapa och anpassa IoT-enhetsmallar
* Ansluta enheter till ditt program
* Lägga till regler och åtgärder för att övervaka villkor

Nu när du har skapat ett Azure IoT Central condition monitoring-program, här är det föreslagna nästa steget:

> [!div class="nextstepaction"]
> [Anpassa operatörsinstrumentpanelen](./tutorial-in-store-analytics-customize-dashboard-pnp.md)
