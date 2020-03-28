---
title: 'Självstudiekurs: Skapa en ansluten avfallshanteringsapp med Azure IoT Central'
description: 'Självstudiekurs: Lär dig att skapa Skapa ett anslutet avfallshanteringsprogram med hjälp av Azure IoT Central-programmallar.'
author: miriambrus
ms.author: miriamb
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 274d725a0e7987c8cb15363ba276aec7ebc541d5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77426368"
---
# <a name="tutorial-create-a-connected-waste-management-application-in-iot-central"></a>Självstudiekurs: Skapa en ansluten avfallshanteringsapplikation i IoT Central



Den här självstudien hjälper dig att skapa ett Azure IoT Central-anslutet avfallshanteringsprogram från ioT Central **Connected avfallshanteringsprogrammallen.** 

I den här självstudien får du lära dig hur man: 

> [!div class="checklist"]

> * Använd azure IoT Central **Connected avfallshanteringsmall** för att skapa ditt anslutna avfallshanteringsprogram
> * Utforska och anpassa operatörsinstrumentpanelen 
> * Utforska mall för ansluten avfallskorgsenhet
> * Utforska simulerade enheter
> * Utforska och konfigurera regler
> * Konfigurera jobb
> * Anpassa ditt program branding med whitelabeling

## <a name="prerequisites"></a>Krav

För att slutföra den här kursen behöver du:
-  En Azure-prenumeration rekommenderas. Du kan eventuellt använda en kostnadsfri 7-dagars testperiod. Om du inte har en Azure-prenumeration kan du skapa en på [sidan För Azure-registrering](https://aka.ms/createazuresubscription).

## <a name="create-connected-waste-management-app-in-iot-central"></a>Skapa appen Ansluten avfallshantering i IoT Central

I det här avsnittet använder du azure IoT Central **Connected avfallshanteringsmall för** att skapa ditt anslutna avfallshanteringsprogram i IoT Central.

Så här skapar du ett nytt Azure IoT Central-anslutet avfallshanteringsprogram:  

1. Navigera till [Azure IoT Central Hemsida](https://aka.ms/iotcentral) webbplats.

    Om du har en Azure-prenumeration loggar du in med de autentiseringsuppgifter du använder för att komma åt den, annars logga in med ett Microsoft-konto:

    ![Ange ditt organisationskonto](./media/tutorial-connectedwastemanagement/sign-in.png)

2. Klicka på **Skapa** från den vänstra rutan och välj fliken **Regering.** På regeringssidan visas flera statliga programmallar.

    ![Skapa mallar för government app](./media/tutorial-connectedwastemanagement/iotcentral-government-tab-overview.png)

1. Välj programmallen För hantering **av ansluten avfallshantering.** Den här mallen innehåller exempelmall för ansluten avfallslagerenhet, simulerad enhet, operatörsinstrumentpanel och förkonfigurerade övervakningsregler.    

2. Klicka på **Skapa app**, som öppnar formuläret För att skapa **nya program** med följande fält:
    * **Programnamn**. Som standard använder programmet *Ansluten avfallshantering* följt av en unik ID-sträng som IoT Central genererar. Du kan också välja ett eget programnamn. Du kan också ändra programnamnet senare.
    * **URL** – Du kan välja önskad webbadress. Du kan också ändra webbadressen senare. 
    * Om du har en Azure-prenumeration anger du din *Katalog, Azure-prenumeration och Region*. Om du inte har en prenumeration kan du aktivera **7 dagars kostnadsfri utvärderingsversion** och slutföra den erforderliga kontaktinformationen.  

    Mer information om kataloger och prenumerationer finns i [snabbstarten om att skapa ett program](../core/quick-deploy-iot-central.md).

5. Klicka på **Knappen Skapa** längst ned på sidan. 

    ![Sidan Azure IoT Central Skapa program för anslutet avfall](./media/tutorial-connectedwastemanagement/new-application-connectedwastemanagement.png)
    
    ![Azure IoT Central Skapa ansluten faktureringsinformation](./media/tutorial-connectedwastemanagement/new-application-connectedwastemanagement-billinginfo.png)

 
6. Du har nu skapat en ansluten avfallshanteringsapp med azure IoT Central **Connected avfallshanteringsmallen**. 

Grattis! Ditt nyskapade program levereras med förkonfigurerade:
* Exempel på operatörsinstrumentpaneler
* Exempel på fördefinierade mallar för anslutna avfallslager
* Simulerade anslutna avfallsbehållare
* Förkonfigurerade regler och jobb
* Exempel på varumärke med vit märkning 

Det är din ansökan och du kan ändra den när som helst. Låt oss nu utforska programmet och göra några anpassningar.  

## <a name="explore-and-customize-operator-dashboard"></a>Utforska och anpassa operatörsinstrumentpanelen 
När du har skapat programmet du landar i **Wide Waste connected avfallshantering instrumentpanelen**.

   ![Instrumentpanel för ansluten avfallshantering](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard1.png)

Som byggare kan du skapa och anpassa vyer på instrumentpanelen för operatorer. Innan du försöker anpassa ska vi utforska instrumentpanelen. 

> [!NOTE]
> Alla data som visas i instrumentpanelen baseras på simulerade enhetsdata, som kommer att utforskas i nästa avsnitt. 

Instrumentpanelen består av olika typer av paneler:

* ***Wide World Waste utility image tile***: den första brickan i instrumentpanelen är en bildpanel av en fiktiv Avfall verktyg "Wide World Waste". Du kan anpassa panelen och placera din egen bild eller ta bort den. 

* ***Avfallslager bildpanel:*** Du kan använda bild och innehåll paneler för att skapa en visuell representation av enheten som övervakas tillsammans med en beskrivande text. 

* ***KPI-panel på fyllningsnivå:*** panelen visar ett värde som rapporteras av en *fyllningsnivåsensor* i en papperskorg. *Fyllningsnivå* och andra sensorer som *luktmätare* eller *vikt* i en soptunna kan fjärrövervakas. En operatör kan vidta åtgärder, till exempel att skicka sophämtningsbil. 

*  ***Avfall övervakningsområde karta:*** kartan använder Azure Maps, som du kan konfigurera direkt i Azure IoT Central. Kartpanelen visar enhetens plats. Försök att hovra över kartan och prova kontrollerna över kartan, som zooma in, zooma ut eller expandera.

     ![Instrumentpanelskarta för ansluten avfallshantering](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard-map.png)


* ***Fyllning, lukt, viktnivå stapeldiagram:** du kan visualisera en eller flera enhet telemetri data i ett stapeldiagram. Du kan också expandera stapeldiagrammet.  

  ![Instrumentpanelsschema för ansluten avfallshantering](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard-barchart.png)


* **Content tile för Fälttjänster**: instrumentpanelen innehåller länk till hur du integrerar med Dynamics 365 Field Services från ditt Azure IoT Central-program. Som ett exempel kan du använda Field Services för att skapa biljetter för att skicka skräpinsamlingstjänster. 


### <a name="customize-dashboard"></a>Anpassa instrumentpanelen 

Som byggare kan du anpassa vyer i instrumentpanelen för operatörer. Du kan prova:
1. Klicka på **Redigera** för att anpassa **wide world-anslutna avfallshanteringsinstrumentpanelen**. Du kan anpassa instrumentpanelen **Edit** genom att klicka på Redigera-menyn. När instrumentpanelen är i **redigeringsläge** kan du lägga till nya paneler eller konfigurera 

    ![Redigera instrumentpanel](./media/tutorial-connectedwastemanagement/edit-dashboard.png)

2. Du kan också klicka på **+ Ny** för att skapa ny instrumentpanel och konfigurera från grunden. Du kan ha flera instrumentpaneler och du kan navigera mellan instrumentpanelerna från instrumentpanelsmenyn. 

## <a name="explore-connected-waste-bin-device-template"></a>Utforska mall för ansluten avfallskorgsenhet

En enhetsmall i Azure IoT Central definierar möjligheten för en enhet, som kan vara telemetri, egenskaper eller kommando. Som byggare kan du definiera enhetsmallar som representerar möjligheten för de enheter som du ansluter. 
 

**Programmet För hantering av ansluten avfall** levereras med en mall för ansluten avfallsbehållare.

Så här visar du enhetsmallen:

1. Klicka på **Enhetsmallar** från den vänstra rutan i ditt program i IoT Central. 

    ![Enhetsmall](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate.png)

2. I listan Enhetsmallar visas **Ansluten avfallsbehållare**. Öppna genom att klicka på namnet.

3. Bekanta dig med enhetsmallfunktionerna. Du kan se den definierar sensorer som *Fyllningsnivå,* *Luktmätare,* *vikt,* *plats*och andra.

   ![Enhetsmall](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate-connectedbin.png)

   

### <a name="customizing-the-device-template"></a>Anpassa enhetsmallen

Försök att anpassa följande:
1. Navigera till **Anpassa** från enhetsmallsmenyn
2. Hitta `Odor meter` telemetritypen
3. Uppdatera **visningsnamnet** `Odor meter` för att`Odor level`
4. Du kan också prova att uppdatera måttenheten eller ange *Min-värde* och *Max-värde*
5. **Spara** eventuella ändringar 

### <a name="add-a-cloud-property"></a>Lägga till en molnegenskap 

1. Navigera till **egenskapen Cloud** från enhetsmallens meny
2. Lägg till en ny molnegenskap genom att klicka på **+ Lägg till molnegenskap**. I IoT Central kan du lägga till en egenskap som är relevant för enheten men som inte förväntas skickas av en enhet. Ett molnegenskap kan till exempel vara ett tröskelvärde för aviseringar som är specifikt för installationsområde, tillgångsinformation eller underhållsinformation och annan information. 
3. **Spara** eventuella ändringar 
 
### <a name="views"></a>Vyer 
* Mallen för ansluten avfallslagerenhet levereras med fördefinierade vyer. Utforska vyerna och du kan göra uppdateringar. Vyerna definierar hur operatörerna ska se enhetsdata men även mata in molnegenskaper. 

  ![Vyer av enhetsmall](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate-views.png)

### <a name="publish"></a>Publicera 

* Om du har gjort några ändringar måste du se till att **publicera** enhetsmallen. 

### <a name="create-a-new-device-template"></a>Skapa en ny enhetsmall 

* Välj **+ Ny** om du vill skapa en ny enhetsmall och följa skapandeprocessen. Du kan skapa en anpassad enhetsmall från grunden eller välja en enhetsmall från Azure Device Catalog. 

## <a name="explore-simulated-devices"></a>Utforska simulerade enheter

I IoT Central kan du skapa simulerade enheter för att testa enhetsmallen och programmet. 

**Programmet För hantering av ansluten avfall** har två simulerade enheter mappade till mallen för ansluten avfallsbehållare. 

### <a name="to-view-the-devices"></a>Så här visar du enheterna:

1. Navigera till **enhet** från IoT Central vänster fönsterruta. 

   ![Enheter](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devices.png)

2. Välj och klicka på Ansluten avfallskorg enhet.  

     ![Enhet 1](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devices-bin1.png)

3. Navigera till fliken **Molnegenskaper** `Bin full alert threshold` prova `95` att `100`uppdatera värdet från till . 
* Utforska fliken **Enhetsegenskaper** och fliken **Enhetspanel.** 

> [!NOTE]
> Observera att alla flikar har konfigurerats från **enhetsmallvyerna**.

### <a name="add-new-devices"></a>Lägga till nya enheter

* Du kan lägga till nya enheter genom att klicka på **+ Nytt** på fliken **Enheter.** 

## <a name="explore-and-configure-rules"></a>Utforska och konfigurera regler

I Azure IoT Central kan du skapa regler för att automatiskt övervaka på enhetstelemetri och utlösa åtgärder när ett eller flera villkor uppfylls. Åtgärderna kan omfatta att skicka e-postmeddelanden, utlösa en Microsoft Flow-åtgärd eller en webhook-åtgärd för att skicka data till andra tjänster.

**Ansökan om uppkopplad avfallshantering** har fyra urvalsregler.

### <a name="to-view-rules"></a>Så här visar du regler:
1. Navigera till **Regler** från IoT Central vänster fönster

   ![Regler](./media/tutorial-connectedwastemanagement/connectedwastemanagement-rules.png)

2. Välj **varningen för lagerplats**

     ![Fullständig avisering av lagerplats](./media/tutorial-connectedwastemanagement/connectedwastemanagement-binfullalert.png)

 3. Kontrollerna `Bin full alert` när **villkor** `Fill level is greater than or equal to Bin full alert threshold`.

    Den `Bin full alert threshold` är en *molnegenskap* som definieras i `Connected waste bin` enhetsmallen. 

Nu ska vi skapa en e-poståtgärd.

### <a name="create-an-email-action"></a>Skapa en e-poståtgärd
Så här konfigurerar du en e-poståtgärd i regelns åtgärdslista:
1. Välj **+ E-post**. 
2. Ange *hög pH-avisering* som det egna **visningsnamnet** för åtgärden.
3. Ange den e-postadress som är kopplad till ditt IoT Central-konto i **Till**. 
4. Du kan också skriva en anteckning som ska inkluderas i texten i e-postmeddelandet.
5. Välj **Klar** för att slutföra åtgärden.
6. Välj **Spara** om du vill spara och aktivera den nya regeln. 

Du bör få e-post när det konfigurerade **villkoret** är uppfyllt.

> [!NOTE]
> Ansökan kommer att skicka e-post varje gång ett villkor är uppfyllt. **Inaktivera** regeln om du vill sluta ta emot e-post från den automatiska regeln. 
  
Så här skapar du en ny regel: 
1. Välj **+Nytt** i **reglerna** i den vänstra rutan.

## <a name="configure-jobs"></a>Konfigurera jobb

I IoT Central kan du med jobb utlösa uppdateringar av enhets- eller molnegenskaper på flera enheter. Förutom egenskaper kan du också använda jobb för att utlösa enhetskommandon på flera enheter. IoT Central automatiserar arbetsflödet åt dig. 

1. Gå till **Jobb** från den vänstra rutan. 
2. Klicka på **+Nytt** och konfigurera ett eller flera jobb. 


## <a name="customize-your-application"></a>Anpassa ditt program 

Som byggare kan du ändra flera inställningar för att anpassa användarupplevelsen i ditt program.

### <a name="to-change-the-application-theme"></a>Så här ändrar du programtemat:

1. Gå till **Administration > Anpassa programmet**.
3. Använd knappen **Ändra** för att välja en bild att ladda upp som **programlogotyp**.
4. Använd knappen **Ändra** för att välja en **ikonbild för webbläsaren** som ska visas på webbläsarflikar.
5. Du kan också ersätta **standardfärgerna för webbläsare** genom att lägga till HTML hexadecimala färgkoder.

   ![Azure IoT Central anpassa ditt program](./media/tutorial-connectedwastemanagement/connectedwastemanagement-customize-your-application.png)

6. Du kan också ändra programbilder genom att gå till **administrationen > programinställningar** och **välj bildknapp** för att välja en bild att ladda upp som programavbildning.
7. Slutligen kan du också ändra **temat** genom att klicka på **Inställningar** på programmets huvudrubrik.

  
## <a name="clean-up-resources"></a>Rensa resurser

Om du inte ska fortsätta att använda det här programmet tar du bort programmet med följande steg:

1. Öppna fliken Administration från den vänstra rutan i ditt IoT Central-program.
2. Välj Programinställningar och klicka på Knappen Ta bort längst ned på sidan.

  

## <a name="next-steps"></a>Nästa steg

* Läs mer om [koncept för uppkopplad avfallshantering](./concepts-connectedwastemanagement-architecture.md)
