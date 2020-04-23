---
title: 'Självstudiekurs: Skapa en övervakningsapp för vattenkvalitet med Azure IoT Central'
description: 'Självstudiekurs: Lär dig hur du skapar ett program för övervakning av vattenkvalitet med hjälp av Azure IoT Central-programmallar.'
author: miriambrus
ms.author: miriamb
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 77ec999d63175f63c1de6e31fdb3f72c963d228c
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "82024480"
---
# <a name="tutorial-create-a-water-quality-monitoring-application-in-azure-iot-central"></a>Självstudiekurs: Skapa ett program för övervakning av vattenkvalitet i Azure IoT Central



Den här självstudien guidar dig genom att skapa ett program för övervakning av vattenkvalitet i Azure IoT Central. Du skapar programmet från Azure IoT Central **Water quality monitoring** application template.

I de här självstudierna får du lära dig att:

> [!div class="checklist"]
> * Använd mallen för övervakning av **vattenkvalitet** för att skapa ett program för övervakning av vattenkvalitet.
> * Utforska och anpassa en operatörsinstrumentpanel.
> * Utforska en mall för övervakning av vattenkvalitet.
> * Utforska simulerade enheter.
> * Utforska och konfigurera regler.
> * Konfigurera jobb.
> * Anpassa applikationsprofilering med hjälp av vit märkning.

## <a name="prerequisites"></a>Krav

Vi rekommenderar att du har en Azure-prenumeration för att slutföra den här självstudien. Om du inte har en Azure-prenumeration kan du skapa en på [sidan För Azure-registrering](https://aka.ms/createazuresubscription).

## <a name="create-a-water-quality-monitoring-application-in-azure-iot-central"></a>Skapa ett program för övervakning av vattenkvalitet i Azure IoT Central

I det här avsnittet använder du azure IoT Central **Water kvalitetsövervakningsmall** för att skapa ett program för övervakning av vattenkvalitet.

1. Gå till startsidan för [Azure IoT Central](https://aka.ms/iotcentral).

    Om du har en Azure-prenumeration loggar du in med de autentiseringsuppgifter du använder för att komma åt den. Annars loggar du in med ett Microsoft-konto:

    ![Logga in på ditt organisationskonto](./media/tutorial-waterqualitymonitoring/sign-in.png)

1. Välj **Skapa** i fönstret längst till vänster i Azure IoT Central och välj fliken **Regering.** I myndighetsfönstret visas flera statliga programmallar.

    ![Mallar för statliga program](./media/tutorial-waterqualitymonitoring/iotcentral-government-tab-overview1.png)

1. Välj programmallen för övervakning **av vattenkvalitet.** Den här programmallen innehåller en mall för vattenkvalitetsenheter, simulerade enheter, en operatörsinstrumentpanel och förkonfigurerade övervakningsregler.

1. Välj **Skapa app**. Det **nya programfönstret** öppnas och visar följande element:

    * **Programnamn**: Som standard är programnamnet **Vattenkvalitetsövervakning** följt av en unik ID-sträng som Azure IoT Central genererar. Om du vill kan du ange ett visningsnamn eller ändra programnamnet senare.
    * **URL**: Du kan ange vilken webbadress du vill eller ändra URL-värdet senare.
    * Om du har en Azure-prenumeration anger du värden för **Directory,** **Azure-prenumeration**och **Region**. Om du inte har en prenumeration kan du aktivera **7 dagars kostnadsfri utvärderingsversion** och slutföra den erforderliga kontaktinformationen.

    Mer information om kataloger och prenumerationer finns i snabbstarten [Skapa ett program.](../core/quick-deploy-iot-central.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)

1. Välj knappen **Skapa** längst ned till vänster på sidan.

    ![Sidan Nya program för Azure IoT Central](./media/tutorial-waterqualitymonitoring/new-application-waterqualitymonitoring1.png)

    ![Faktureringsinformation för Azure IoT Central](./media/tutorial-waterqualitymonitoring/new-application-waterqualitymonitoring1-billinginfo.png)

Du har nu skapat ett program för övervakning av vattenkvalitet med hjälp av azure IoT Central **Water kvalitetsövervakningsmall.**

Det nya programmet levereras med följande förkonfigurerade komponenter:

* Instrumentpaneler för operatör
* Mallar för övervakning av vattenkvalitet
* Simulerade övervakningsanordningar för vattenkvalitet
* Regler och jobb
* Branding som använder vit märkning

Du kan ändra ditt program när som helst.

Därefter utforska programmet och göra några anpassningar.

## <a name="explore-and-customize-the-operator-dashboard"></a>Utforska och anpassa operatörsinstrumentpanelen

När du har skapat programmet öppnas **instrumentpanelsfönstret för vattenkvalitet vid världsvattenkvalitet.**

   ![Instrumentbrädan för övervakning av vattenkvalitet](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-dashboard1.png)

Som byggare kan du skapa och anpassa vyer på instrumentpanelen för användning av operatörer. Men innan du försöker anpassa, utforska först instrumentpanelen.

Alla data som visas i instrumentpanelen baseras på simulerade enhetsdata, som beskrivs i nästa avsnitt.

Instrumentpanelen innehåller följande typer av paneler:

* **Wide World vatten verktyg bildbricka:** Den första brickan i det övre vänstra hörnet av instrumentpanelen är en bild som visar fiktiva verktyget heter Wide World. Du kan anpassa panelen så att den använder en egen bild eller ta bort panelen.

* **Genomsnittliga pH KPI-paneler:** KPI-paneler som **Genomsnittligt pH under de senaste 30 minuterna** finns högst upp i instrumentpanelsfönstret. Du kan anpassa KPI-paneler och ställa in var och en på en annan typ och tidsintervall.

* **Karta över vattenövervakningsområde**: Azure IoT Central använder Azure Maps, som du direkt kan ange i ditt program för att visa enhetens plats. Du kan också mappa platsinformation från ditt program till din enhet och sedan använda Azure Maps för att visa informationen på en karta. Hovra över kartan och prova kontrollerna.

* **Genomsnittligt pH-fördelningsvärmekartiseringsdiagram:** Du kan välja olika visualiseringsdiagram för att visa enhetstelemetri på det sätt som är mest lämpligt för ditt program.

* **Linjediagram för kritiska kvalitetsindikatorer:** Du kan visualisera enhetstelemetri som ritas som ett linjediagram över ett tidsintervall.  

* **Koncentration av stapeldiagram för kemiska agenser**: Du kan visualisera enhetstelemetri i ett stapeldiagram.

* **Åtgärdsknapp**: Instrumentpanelen innehåller en panel för åtgärder som en operatör kan initiera direkt från övervakningsinstrumentpanelen. Att återställa en enhets egenskaper är ett exempel på sådana åtgärder.

* **Paneler för egenskapslista**: Instrumentpanelen har flera egenskapspaneler som representerar tröskelinformation, information om enhetens hälsotillstånd och underhållsinformation.

### <a name="customize-the-dashboard"></a>Anpassa instrumentpanelen

Som byggare kan du anpassa vyer på instrumentpanelen för användning av operatorer.

1. Välj **Redigera** om du vill anpassa **instrumentpanelsfönstret för vattenkvalitet vid hela världen.** Du kan anpassa instrumentpanelen genom **Edit** att välja kommandon på Redigera-menyn. När instrumentpanelen är i redigeringsläge kan du lägga till nya paneler eller konfigurera befintliga filer.

    ![Redigera instrumentpanelen](./media/tutorial-waterqualitymonitoring/edit-dashboard.png)

1. Välj **+ Nytt** om du vill skapa en ny instrumentpanel som du kan konfigurera. Du kan ha flera instrumentpaneler och navigera bland dem från instrumentpanelsmenyn.

## <a name="explore-a-water-quality-monitoring-device-template"></a>Utforska en mall för övervakning av vattenkvalitet

En enhetsmall i Azure IoT Central definierar funktionerna för en enhet. Tillgängliga funktioner är telemetri, egenskaper och kommandon. Som byggare kan du definiera enhetsmallar i Azure IoT Central som representerar funktionerna för de anslutna enheterna. Du kan också skapa simulerade enheter för att testa enhetsmallen och programmet.

Vattenkvaliteten övervakning ansökan du skapade levereras med en vattenkvalitet övervakningsenhet mall.

Så här visar du enhetsmallen:

1. Välj **Enhetsmallar** längst till vänster i programmet i Azure IoT Central.
1. Välj **Vattenkvalitetsövervakare**i listan över enhetsmallar . Enhetsmallen öppnas.

    ![Enhetsmallen](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devicetemplate.png)

### <a name="customize-the-device-template"></a>Anpassa enhetsmallen

Öva på att anpassa följande inställningar för enhetsmallar:

1. Välj **Anpassa**på enhetsmallsmenyn .
1. Gå till telemetritypen **Temperatur.**
1. Ändra **värdet för visningsnamn** till **Rapporterad temperatur**.
1. Ändra måttenheten eller ange **Min-värde** och **Max-värde**.
1. Välj **Spara**.

#### <a name="add-a-cloud-property"></a>Lägga till en molnegenskap

1. Välj Egenskapen Moln på enhetsmallens **meny**.
1. Om du vill lägga till en ny molnegenskap väljer du **+ Lägg till molnegenskap**. I Azure IoT Central kan du lägga till en egenskap som är relevant för en enhet men som inte förväntas skickas av enheten. Ett exempel på en sådan egenskap är ett tröskelvärde för en varning som är specifik för installationsområde, tillgångsinformation eller underhållsinformation.
1. Välj **Spara**.

### <a name="explore-views"></a>Utforska vyer

Mallen för övervakningsenhet för vattenkvalitet levereras med fördefinierade vyer. Vyerna definierar hur operatörer ser enhetsdata och anger molnegenskaper. Utforska vyerna och öva på att göra ändringar.

  ![Vyer för enhetsmall](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devicetemplate-views.png)

### <a name="publish-the-device-template"></a>Publicera enhetsmallen

Om du gör några ändringar måste du välja **Publicera** för att publicera enhetsmallen.

### <a name="create-a-new-device-template"></a>Skapa en ny enhetsmall

1. Välj **+ Ny** om du vill skapa en ny enhetsmall och följa skapandeprocessen.
1. Skapa en anpassad enhetsmall eller välj en enhetsmall från Azure IoT-enhetskatalogen.

## <a name="explore-simulated-devices"></a>Utforska simulerade enheter

Programmet för övervakning av vattenkvalitet som du skapade från programmallen har två simulerade enheter. Dessa enheter mappas till mallen för övervakning av vattenkvalitet.

### <a name="view-the-devices"></a>Visa enheterna

1. Välj **Enheter** längst till vänster i programmet.

   ![Enheter](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devices.png)

1. Välj en simulerad enhet.

    ![Välj enhet 1](./media/tutorial-waterqualitymonitoring/waterqualitymonitor-device1.png)

1. Ändra **tröskelvärdet acidity (pH)** på fliken **Molnegenskaper** från **8** till **9**.
1. Utforska fliken **Enhetsegenskaper** och fliken **Enhetsinstrumentpanel.**

> [!NOTE]
> Alla flikar har konfigurerats från **enhetsmallvyer**.

### <a name="add-new-devices"></a>Lägga till nya enheter

På fliken **Enheter** väljer du **+ Ny** om du vill lägga till en ny enhet.

## <a name="explore-and-configure-rules"></a>Utforska och konfigurera regler

I Azure IoT Central kan du skapa regler som automatiskt övervakar enhetstelemetri. Dessa regler utlöser en åtgärd när något av deras villkor är uppfyllda. En möjlig åtgärd är att skicka e-postmeddelanden. Andra möjligheter är en Microsoft Flow-åtgärd eller en webhook-åtgärd för att skicka data till andra tjänster.

Det vattenkvalitetsövervakningsprogram som du skapade har två förkonfigurerade regler.

### <a name="view-rules"></a>Visa regler

1. Välj **Regler** längst till vänster i programmet.

   ![Regler](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-rules.png)

1. Välj **Hög pH-avisering**, som är en av de förkonfigurerade reglerna i programmet.

   ![Den höga pH-varningsregeln](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-highphalert.png)

   **Regeln hög pH-varning** är konfigurerad för att kontrollera att surhetstillståndet (pH) är större än 8.

Lägg sedan till en e-poståtgärd i regeln:

1. Välj **+ E-post**.
1. Ange **Hög pH-varning**i rutan **Visningsnamn** .
1. I rutan **Till** anger du den e-postadress som är kopplad till ditt Azure IoT Central-konto.
1. Du kan också skriva en anteckning som ska inkluderas i texten i e-postmeddelandet.
1. Välj **Klar** för att slutföra åtgärden.
1. Välj **Spara** om du vill spara och aktivera den nya regeln.

Inom några minuter bör du få e-post när det konfigurerade villkoret är uppfyllt.

> [!NOTE]
> Programmet skickar e-post varje gång ett villkor uppfylls. Välj **Inaktivera** för en regel om du vill sluta ta emot automatisk e-post från den regeln.
  
Om du vill skapa en ny regel väljer du **Regler** längst till vänster i programmet och väljer sedan **+Nytt**.

## <a name="configure-jobs"></a>Konfigurera jobb

Med Azure IoT Central-jobb kan du utlösa uppdateringar till enhets- eller molnegenskaper på flera enheter. Du kan också använda jobb för att utlösa enhetskommandon på flera enheter. Azure IoT Central automatiserar arbetsflödet åt dig.

1. Välj **Jobb** i den vänstra rutan i programmet.
1. Välj **+Nytt** och konfigurera ett eller flera jobb.

## <a name="customize-your-application"></a>Anpassa ditt program

Som byggare kan du ändra flera inställningar för att anpassa användarupplevelsen i ditt program.

1. Välj **Administration** > **Anpassa ditt program**.
1. Under **Programlogotyp**väljer du **Ändra** för att välja vilken bild som ska laddas upp som logotyp.
1. Under **Ikonen Webbläsare**väljer du **Ändra** för att välja den bild som visas på webbläsarflikar.
1. Under **Webbläsarfärger**kan du ersätta standardvärdena med HTML hexadecimala färgkoder.
1. Välj **Inställningar** om du vill ändra värdet på **Tema**.

   ![Anpassa ditt program](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-customize-your-application1.png)

### <a name="update-the-application-image"></a>Uppdatera programavbildningen

1. Välj **Inställningar för administrationsprogram** > **Application settings**.

1. Använd knappen **Välj bild** för att välja en bild att ladda upp som programbild.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte ska fortsätta använda programmet tar du bort programmet med följande steg:

1. Öppna fliken **Administration** längst till vänster i programmet.
1. Välj **Programinställningar** och välj knappen **Ta bort.**

    ![Ta bort programmet](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-application-settings-delete-app1.png)

## <a name="next-steps"></a>Nästa steg

* Läs mer om [vattenkvalitetsövervakningskoncept](./concepts-waterqualitymonitoring-architecture.md).
