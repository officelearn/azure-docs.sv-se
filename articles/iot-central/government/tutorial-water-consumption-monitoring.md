---
title: 'Självstudiekurs: Skapa en övervakningsapp för vattenförbrukning med Azure IoT Central'
description: 'Självstudiekurs: Lär dig att skapa ett program för övervakning av vattenförbrukning med hjälp av Azure IoT Central-programmallar.'
author: miriambrus
ms.author: miriamb
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 7f00947504e5c6355379ce1e400fd2325016e05a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77122056"
---
# <a name="tutorial-create-a-water-consumption-monitoring-application-with-azure-iot-central"></a>Självstudiekurs: Skapa ett program för övervakning av vattenförbrukning med Azure IoT Central

Den här självstudien visar hur du skapar ett Azure IoT Central vattenförbrukningsövervakningsprogram med hjälp av Azure IoT Central vattenförbrukning övervakningsprogram mall.

I den här självstudien får du lära dig hur man:

> [!div class="checklist"]
> * Använd Azure IoT Central vattenförbrukning övervakningsmall för att skapa din vattenförbrukning övervakningsprogram.
> * Utforska och anpassa operatörsinstrumentpanelen.
> * Utforska enhetsmallar.
> * Utforska simulerade enheter.
> * Utforska och konfigurera regler.
> * Konfigurera jobb.
> * Anpassa ditt program branding med hjälp av vit märkning.

## <a name="prerequisites"></a>Krav

För att slutföra den här kursen behöver du:

- En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa en på [sidan För Azure-registrering](https://aka.ms/createazuresubscription).

## <a name="create-a-water-consumption-monitoring-app-with-azure-iot-central"></a>Skapa en övervakningsapp för vattenförbrukning med Azure IoT Central

I det här avsnittet använder du Azure IoT Central vattenförbrukning övervakningsmall för att skapa ditt vattenförbrukning övervakningsprogram i Azure IoT Central.

Så här skapar du ett nytt Azure IoT Central vattenförbrukningsövervakningsprogram:

1. Gå till [Azure IoT Central hemsida](https://aka.ms/iotcentral) webbplats.

    Om du har en Azure-prenumeration loggar du in med de autentiseringsuppgifter du använder för att komma åt den. Annars loggar du in med ett Microsoft-konto.

    ![Ange ditt organisationskonto](media/tutorial-waterconsumptionmonitoring/sign-in.png)

1. Välj **Skapa** i den vänstra rutan och välj fliken **Regering.** På sidan **Regeringen** visas flera statliga programmallar.

   ![Skapa mallar för myndighetsappar](./media/tutorial-waterconsumptionmonitoring/iotcentral-government-tab-overview1.png)

1. Välj programmallen För övervakning **av vattenförbrukning.**
Den här mallen innehåller en exempelmall för vattenförbrukningsenhet, en simulerad enhet, en operatörsinstrumentpanel och förkonfigurerade övervakningsregler.

1. Välj **Skapa app** om du vill öppna formuläret Skapa nytt **program** med följande fält:
    * **Programnamn**: Som standard använder programmet övervakning av *vattenförbrukning* följt av en unik ID-sträng som Azure IoT Central genererar. Du kan också välja ett eget programnamn. Du kan också ändra programnamnet senare.
    * **URL**: Azure IoT Central genererar automatiskt en URL baserat på programnamnet. Du kan välja att uppdatera webbadressen efter eget tycke. Du kan också ändra webbadressen senare.
    * Om du har en Azure-prenumeration anger du din **katalog-** **Azure-prenumeration**och **platsinformation.** Om du inte har en prenumeration kan du välja **alternativet med kostnadsfri provperiod** på sju dagar och slutföra den erforderliga kontaktinformationen.

    Mer information om kataloger och prenumerationer finns i [Skapa en snabbstart för programmet](../core/quick-deploy-iot-central.md).

1. Välj **Skapa** längst ned på sidan.

    ![Sidan Azure IoT Central New-program](./media/tutorial-waterconsumptionmonitoring/new-application-waterconsumptionmonitoring.png)

    ![Sidan Azure IoT Central Billing info](./media/tutorial-waterconsumptionmonitoring/new-application-waterconsumptionmonitoring-billinginfo.png)

Du har nu skapat en övervakningsapp för vattenförbrukning med hjälp av Azure IoT Central vattenförbrukningsövervakningsmallen.

Programmet för övervakning av vattenförbrukning levereras med förkonfigurerad:

* Exempel på operatörsinstrumentpaneler.
* Prov fördefinierade vattenflöde och ventilenheter.
* Simulerade vattenflöde och smarta ventilenheter.
* Regler och jobb.
* Prova branding med hjälp av vit märkning.

Det är din ansökan, och du kan ändra det när som helst. Nu ska vi utforska programmet och göra några anpassningar.

## <a name="explore-and-customize-the-operator-dashboard"></a>Utforska och anpassa operatörsinstrumentpanelen

När du har skapat programmet öppnas **exempelinstrumentpanelen för wide world-vattenförbrukning.**

   ![Instrumentpanel för övervakning av vattenförbrukning](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-dashboardfull.png)

Som byggare kan du skapa och anpassa vyer på instrumentpanelen för operatorer. Låt oss utforska instrumentpanelen innan du försöker anpassa den.

> [!NOTE]
> Alla data som visas på instrumentpanelen baseras på simulerade enhetsdata, som vi ska utforska i nästa avsnitt.
  
Instrumentpanelen består av olika typer av paneler:

* **Wide World Water Utility bild kakel:** Den första brickan i instrumentpanelen är en bild bricka av fiktiva vattenverket Wide World Water. Du kan anpassa panelen genom att infoga en egen bild eller ta bort den.
* **Genomsnittlig KPI-bricka för vattenflöde:** KPI-panelen är konfigurerad för att visa *medelvärdet under de senaste 30 minuterna*. Du kan anpassa KPI-panelen och ställa in den på en annan typ och tidsintervall.
* **Enhet kommando plattor:** Dessa plattor inkluderar **Stäng ventil,** **Öppen ventil**och **Ställ ventilen position** plattor. Om du väljer kommandona kommer du till kommandosidan för simulerad enhet. I Azure IoT Central är ett *kommando* en *enhetskapacitetstyp.* Vi kommer att utforska det här konceptet senare i avsnittet "Enhetsmall" i den här självstudien.
* **Karta över vattendistributionsområde**: Kartan använder Azure Maps, som du kan konfigurera direkt i Azure IoT Central. Kartpanelen visar enhetens plats. Hovra över kartan och prova kontrollerna över kartan, till exempel *zooma in,* *zooma ut*eller *expandera*.

    ![Instrumentpanelskarta för övervakning av vattenförbrukning](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-dashboard-map.png)

* **Genomsnittligt vattenflödeslinjediagram** och **linjediagram för miljötillstånd:** Du kan visualisera en eller flera enhetstelemetrier som ritas som ett linjediagram över ett önskat tidsintervall.
* **Genomsnittligt schema för värmekarta för ventiltryck:** Du kan välja den heatmap-visualiseringstypen för enhetstelemetridata som du vill se fördelas över ett tidsintervall med ett färgindex.
* **Innehållspanelen Återställ varningströsklar**: Du kan inkludera paneler för call to action-innehåll och bädda in en länk till en åtgärdssida. I det här fallet tar tröskelvärdet för återställningsavisering dig till programmet **Jobb**, där du kan köra uppdateringar av enhetsegenskaper. Vi kommer att utforska det här alternativet senare i avsnittet Konfigurera jobb i den här självstudien.
* **Egenskapspaneler:** Instrumentpanelen visar **ventilens driftinformation,** **tröskelvärden för flödesvarningar**och **underhållsinformationspaneler.**

### <a name="customize-the-dashboard"></a>Anpassa instrumentpanelen

Som byggare kan du anpassa vyer på instrumentpanelen för operatorer.

1. Välj **Redigera** om du vill anpassa **wide world-instrumentpanelen för vattenförbrukning**. Du kan anpassa instrumentpanelen **Edit** genom att välja Redigera-menyn. När instrumentpanelen är i **redigeringsläge** kan du lägga till nya paneler eller konfigurera den.

     ![Redigera instrumentpanel](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-edit-dashboard.png)

1. Välj **+ Nytt** om du vill skapa en ny instrumentpanel och konfigurera den från grunden. Du kan ha flera instrumentpaneler och du kan flytta mellan instrumentpanelerna på instrumentpanelsmenyn.

## <a name="explore-the-device-template"></a>Utforska enhetsmallen

En enhetsmall i Azure IoT Central definierar möjligheten för en enhet, som kan vara telemetri, egenskap eller kommando. Som byggare kan du definiera en eller flera enhetsmallar i Azure IoT Central som representerar möjligheten för de enheter som du ansluter.

Programmet för övervakning av vattenförbrukning levereras med två referensenhetsmallar som representerar en *flödesmätare* och en *smart ventilenhet.*

Så här visar du enhetsmallen:

1. Välj **Enhetsmallar** i den vänstra rutan i ditt program i Azure IoT Central. I listan **Enhetsmallar** visas två enhetsmallar, **Smart Valve** och **Flow meter**.

   ![Enhetsmall](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate.png)

1. Välj **mallen flödesmätare** och bekanta dig med enhetsfunktionerna.

     ![Flödesmätare för enhetsmall](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate-flowmeter.png)

### <a name="customize-the-device-template"></a>Anpassa enhetsmallen

Så här anpassar du enhetsmallen:

1. Gå till **Anpassa** på menyn **Enhetsmallar.**
1. Leta `Temperature` reda på telemetritypen.
1. Uppdatera **visningsnamnet** `Temperature` `Reported temperature`till .
1. Uppdatera måttenheten eller ange **minvärdet** och **maxvärdet**.
1. Välj **Spara** om du vill spara eventuella ändringar.

### <a name="add-a-cloud-property"></a>Lägga till en molnegenskap

1. Gå till **Molnegenskaper** på **enhetsmallar-menyn.**
1. Lägg till en ny molnegenskap genom att välja **+ Lägg till molnegenskap**.
    I Azure IoT Central kan du lägga till en egenskap som är relevant för enheten. Ett molnegenskap kan till exempel vara ett tröskelvärde för aviseringar som är specifika för ett installationsområde, tillgångsinformation eller annan underhållsinformation.
1. Välj **Spara** om du vill spara eventuella ändringar.

### <a name="views"></a>Vyer

Mallen för vattenförbrukningsövervakaren levereras med fördefinierade vyer. Utforska vyerna så kan du göra uppdateringar. Vyerna definierar hur operatörer ser enhetsdata men även indatamolnegenskaper.

  ![Vyer för enhetsmall](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate-views.png)

### <a name="publish"></a>Publicera

Om du har gjort några ändringar måste du se till att **publicera enhetsmallen.**

### <a name="create-a-new-device-template"></a>Skapa en ny enhetsmall

Välj **+ Ny** om du vill skapa en ny enhetsmall och följa skapandeprocessen.
Du kan skapa en anpassad enhetsmall från grunden eller välja en enhetsmall från Azure Device Catalog.

## <a name="explore-simulated-devices"></a>Utforska simulerade enheter

I Azure IoT Central kan du skapa simulerade enheter för att testa din enhetsmall och program. Programmet för övervakning av vattenförbrukning har två simulerade enheter mappade till **flödesmätaren** och smartventilenhetsmallarna. **Smart Valve**

### <a name="view-the-devices"></a>Visa enheterna

1. Välj **Enheter** > **Alla enheter** i den vänstra rutan.

   ![Fönstret Alla enheter](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devices.png)

1. Välj **Smart Ventil 1**.

    ![Smart ventil 1](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitor-device1.png)

1. På fliken **Kommandon** kan du se de tre enhetskommandona (**Stäng ventil**, **Öppen ventil**och Ställ in **ventilposition**) som är funktioner som definieras i mallen **för Smart Valve-enheten.**

1. Utforska fliken **Enhetsegenskaper** och fliken **Enhetsinstrumentpanel.**

> [!NOTE]
> Observera att alla flikar är konfigurerade från enhetsmallvyerna.

### <a name="add-new-devices"></a>Lägga till nya enheter

Lägg till nya enheter genom att välja **+ Nytt** på fliken **Enheter.**

## <a name="explore-and-configure-rules"></a>Utforska och konfigurera regler

I Azure IoT Central kan du skapa regler för att automatiskt övervaka enhetstelemetri och utlösa åtgärder när ett eller flera villkor uppfylls. Åtgärderna kan omfatta att skicka e-postmeddelanden eller utlösa en Microsoft Power Automate-åtgärd eller en webhook-åtgärd för att skicka data till andra tjänster.

Det vattenförbrukningsövervakningsprogram som du skapade har tre förkonfigurerade regler.

### <a name="view-rules"></a>Visa regler

1. Välj **Regler** i den vänstra rutan.

   ![Fönstret Regler](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-rules.png)

1. Välj **Hög pH-avisering**, som är en av de förkonfigurerade reglerna i programmet.

     ![Hög pH-varning](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-highflowalert.png)

    Regeln `High flow alert` är konfigurerad för att `Acidity (pH)` `greater than` kontrollera `Max flow threshold`mot villkoret är . Max flödeströskel är en **Smart Valve** molnegenskap som definieras i smart ventilenhetsmallen. Värdet `Max flow threshold` för anges per enhetsinstans.

Nu ska vi skapa en e-poståtgärd.

Så här lägger du till en åtgärd i regeln:

1. Välj **+ E-post**.
1. Ange **hög pH-avisering** som det egna **visningsnamnet** för åtgärden.
1. Ange den e-postadress som är kopplad till ditt Azure IoT Central-konto i **Till**.
1. Du kan också skriva en anteckning som ska inkluderas i texten i e-postmeddelandet.
1. Välj **Klar** för att slutföra åtgärden.
1. Välj **Spara** om du vill spara och aktivera den nya regeln.

Inom några minuter bör du få ett e-postmeddelande när det konfigurerade villkoret är uppfyllt.

> [!NOTE]
> Programmet skickar ett e-postmeddelande varje gång ett villkor uppfylls. Välj **Inaktivera** om du vill inaktivera regeln om du vill sluta ta emot e-post från den automatiska regeln.
  
Så här skapar du en ny regel:

* Välj **+ Nytt** på fliken **Regler** i den vänstra rutan.

## <a name="configure-jobs"></a>Konfigurera jobb

I Azure IoT Central kan du med jobb utlösa enhets- eller molnegenskapsuppdateringar på flera enheter. Förutom egenskaper kan du också använda jobb för att utlösa enhetskommandon på flera enheter. Azure IoT Central automatiserar arbetsflödet åt dig.

1. Välj **Jobb** i den vänstra rutan.
1. Välj **+ Nytt**och konfigurera ett eller flera jobb.

## <a name="customize-your-application"></a>Anpassa ditt program

Som byggare kan du ändra flera inställningar för att anpassa användarupplevelsen i ditt program.

1. Välj **Administration** > **Anpassa ditt program**.
1. Om du vill välja en bild att ladda upp som **programlogotyp**väljer du knappen **Ändra.**
1. Om du vill välja en **ikonbild** för webbläsaren som ska visas på webbläsarflikar väljer du knappen **Ändra.**
1. Du kan också ersätta **standardfärgerna för webbläsare** genom att lägga till HTML hexadecimala färgkoder.

   ![Val för programlogotyp, webbläsarikon och webbläsarfärger](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-customize-your-application.png)

1. Du kan också ändra programbilder genom att välja **Inställningar för Administrationsprogram** > **Application settings**. Om du vill välja en bild att ladda upp som programbild väljer du knappen **Välj bild.**
1. Slutligen kan du också ändra **temat** genom att välja **ikonen Inställningar** i det övre högra hörnet av programmet.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte ska fortsätta att använda det här programmet tar du bort det.

1. Välj **Administration** i den vänstra rutan i ditt Azure IoT Central-program.
1. Välj **Programinställningar**och välj sedan **Ta bort** längst ned på sidan.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [koncept för övervakning av vattenförbrukning](./concepts-waterconsumptionmonitoring-architecture.md).
