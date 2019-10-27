---
title: Skapa en app för övervakning av vatten kvalitet med Azure IoT Central
description: Lär dig hur du skapar ett program för övervakning av vatten kvalitet med hjälp av Azure IoT Central programmallar.
author: miriambrus
ms.author: miriamb
ms.date: 10/23/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: c2e68e83999ace4094d060a21e91b87347b03e9c
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2019
ms.locfileid: "72955111"
---
# <a name="tutorial-create-a-water-quality-monitoring-application-in-iot-central"></a>Självstudie: skapa ett program för övervakning av vatten kvalitet i IoT Central

I den här självstudien får du hjälp med att skapa ett program för övervakning av Azure-IoT Central vatten kvalitet från program mal len övervakning av IoT Central vatten kvalitet 

I den här självstudien får du lära dig att: 

> [!div class="checklist"]
> * Använd mallen för övervakning av Azure IoT Central **vatten kvalitet** för att skapa ett program för övervakning av vatten kvalitet
> * Utforska och anpassa instrument panelen för operatör 
> * Utforska enhets mal len för övervakning av vatten kvalitet
> * Utforska simulerade enheter
> * Utforska och konfigurera regler
> * Konfigurera jobb
> * Anpassa din program anpassning med adresser


## <a name="prerequisites"></a>Krav

För att slutföra den här självstudien behöver du:
-  En Azure-prenumeration rekommenderas. Om du inte har någon Azure-prenumeration kan du skapa en på [sidan för Azure-registrering](https://aka.ms/createazuresubscription).


## <a name="create-water-quality-monitoring-app-in-iot-central"></a>Skapa appen för övervakning av vatten kvalitet i IoT Central

I det här avsnittet ska du använda mallen för övervakning av Azure IoT Central **vatten kvalitet** för att skapa ett program för övervakning av vatten kvalitet i IoT Central.


Skapa ett nytt program för övervakning av Azure IoT Central vatten kvalitet:  

1. Gå till webbplatsen för [Azure IoT Central start sida](https://aka.ms/iotcentral) .

      Om du har en Azure-prenumeration loggar du in med de autentiseringsuppgifter som du använder för att komma åt den, annars loggar du in med en Microsoft-konto:

    ![Ange ditt organisationskonto](./media/tutorial-waterqualitymonitoring/sign-in.png)

2. Klicka på **build** i den vänstra navigerings menyn och välj fliken **myndigheter** . Sidan myndigheter visar flera myndighets programmallar.

    ![Bygg app-mallar för myndigheter](./media/tutorial-waterqualitymonitoring/iotcentral-government-tab-overview1.png)


1. Välj program mal len **övervakning av vatten kvalitet** . Den här mallen innehåller exempel på enhets mal len för vatten kvalitet, simulerad enhet, operatörs instrument panel och förkonfigurerade övervaknings regler.    

2. Klicka på **skapa app**, så öppnas formuläret för att skapa **nya program** med följande fält:
    * **Program namn**. Som standard använder programmet *övervakning av vatten kvalitet* följt av en unik ID-sträng som IoT Central genererar. Du kan också välja ett eget program namn. Du kan senare ändra program namnet.
    * **URL** – du kan också välja att ange önskad URL. Du kan ändra URL: en senare. 
    * Om du har en Azure-prenumeration anger du din *katalog, din Azure-prenumeration och region*. Om du inte har någon prenumeration kan du aktivera den **kostnads fria utvärderings versionen på 7 dagar** och slutföra den kontakt information som krävs.  

    Mer information om kataloger och prenumerationer finns i [snabbstarten om att skapa ett program](../core/quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

5. Klicka på knappen **skapa** längst ned på sidan. 

    ![Sidan Skapa program i Azure IoT Central](./media/tutorial-waterqualitymonitoring/new-application-waterqualitymonitoring1.png)


6. Nu har du skapat en app för övervakning av vatten kvalitet med hjälp av mallen för övervakning av Azure IoT Central **vatten kvalitet**. 

Det nyligen skapade programmet levereras med förkonfigurerade:
* Exempel instrument panels instrument paneler
* Exempel på fördefinierad enhets kvalitet för övervakning av vatten kvalitet
* Simulerade enheter för övervakning av vatten kvalitet
* Förkonfigurerade regler och jobb
* Exempel anpassning med vita etiketter 

Det är ditt program och du kan ändra det när som helst. Nu ska vi utforska programmet och göra några anpassningar.  


## <a name="explore-and-customize-operator-dashboard"></a>Utforska och anpassa instrument panelen för operatör 
När du har skapat det program som du har skapat på **instrument panelen för bred vatten kvalitet övervakning**.


   ![Instrument panel för övervakning av vatten kvalitet](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-dashboard1.png)

Som ett verktyg kan du skapa och anpassa vyer på instrument panelen för operatörer. Vi går igenom instrument panelen innan du försöker anpassa. 

Alla data som visas i instrument panelen baseras på simulerade enhets data som kommer att utforskas i nästa avsnitt. 

Instrument panelen består av olika typer av paneler:

* **Bild panel för Wide World vatten verktyg**: den första panelen på instrument panelen är en bild panel med ett fiktivt vatten verktyg "Wide World vatten". Du kan anpassa panelen och placera en egen avbildning eller ta bort den. 

* **KPI-panelen i genomsnitts pH**: du kan se att det finns KPI-paneler överst som **genomsnitts pH under de senaste 30 minuterna**. Du kan anpassa KPI-paneler och ange en annan typ och ett tidsintervall.

*  **Områdes karta för vatten övervakning**: IoT Central använder Azure Maps, som du kan ange direkt i ditt program och Visa enhets platsen. Du kan också mappa plats information till din enhet från programmet och kan använda Azure Maps för att visa den på en karta. Försök att hovra över kartan och prova kontrollerna över kartan. 

* **Diagram över Genomsnittligt termisk karta**: du kan välja olika visualiserings diagram för att visa information om enheternas telemetri på det sätt som passar bäst för ditt program. Termisk karta 

* **Linje diagram över kritiska kvalitets indikatorer**: du kan visualisera en eller flera enhets telemetrivärden ritade som ett linje diagram över ett önskat tidsintervall.  

* **Diagram över koncentration av kemiska agenter**: du kan visualisera en eller flera telemetridata i ett stapeldiagram som i exemplet. 

* **Åtgärds knapp**: instrument panelen innehåller ett exempel på en åtgärds panel som en operatör kan initiera direkt från instrument panelen för övervakning, t. ex. att initiera en åtgärd för att återställa enhets egenskaper. 

* **List paneler för egenskaper**: instrument panelen har flera egenskaps paneler som visar tröskel information, information om enhets hälsa och underhålls information. 


### <a name="customize-dashboard"></a>Anpassa instrument panelen 

Som ett verktyg kan du anpassa vyer på instrument panelen för operatörer. 
1. Klicka på **Redigera** för att anpassa **instrument panelen för bred vatten kvalitet övervakning**. Du kan anpassa instrument panelen genom att klicka på **Redigera** -menyn. När instrument panelen är i **redigerings** läge kan du lägga till nya paneler, eller så kan du konfigurera

    ![Redigera instrument panel](./media/tutorial-waterqualitymonitoring/edit-dashboard.png)

2. Klicka på **+ ny** för att skapa en ny instrument panel och konfigurera från början. Du kan ha flera instrument paneler och du kan navigera mellan dina instrument paneler på instrument panelens meny.

## <a name="explore-water-quality-monitor-device-template"></a>Utforska enhets mal len för övervakning av vatten kvalitet
En enhets mall i Azure IoT Central definierar funktionen hos en enhet, som kan vara telemetri, egenskaper och kommandon. Som ett verktyg kan du definiera enhetsspecifika i IoT Central som representerar kapaciteten hos de enheter som ska anslutas. I IoT Central kan du också skapa simulerade enheter för att testa enhetens mall och program. 
 

Programmet för **övervakning av vatten kvalitet** som du har skapat från program mal len levereras med en enhets mall för övervakning av vatten kvalitet.

Så här visar du enhets mal len:

1.  Klicka på **mallar för enheter** i det vänstra navigerings fönstret i programmet i IoT Central. 
2. I listan mallar för enheter visas **vatten kvalitet**. Öppna genom att klicka på namnet.

    ![Enhets mall](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devicetemplate.png)

### <a name="customizing-the-device-template"></a>Anpassa enhets mal len

Försök att anpassa följande:
1. Navigera för att **Anpassa** från menyn på enhets mal len
2. Hitta typen av telemetri för `Temperature`
3. Uppdatera **visnings namnet** för `Temperature` till `Reported temperature`
4. Uppdatera mått enhet eller ange *minsta värde* och *Max värde*
5. **Spara** ändringar 

#### <a name="add-a-cloud-property"></a>Lägg till en moln egenskap 
1. Gå till **moln egenskap** från enhetens mall-menyn
2. Lägg till en ny moln egenskap genom att klicka på **+ Lägg till moln egenskap**. I IoT Central kan du lägga till en egenskap som är relevant för enheten men som inte förväntas skickas av en enhet. Som exempel kan en moln egenskap vara en aviserings tröskel som är speciell för installations områden, till gångs information eller underhålls information osv. 
3. **Spara** ändringar 
 
### <a name="views"></a>Vyer 
Enhets mal len för övervakning av vatten kvalitet levereras med fördefinierade vyer. Utforska vyerna så kan du göra uppdateringar. Vyerna definierar hur operatörer ser enhets data, men som även anger moln egenskaper. 

  ![Vyer för enhets mal len](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devicetemplate-views.png)

### <a name="publish"></a>Publicera 

Om du har gjort några ändringar måste du **publicera** enhets mal len. 


### <a name="create-a-new-device-template"></a>Skapa en ny enhetsmall 
1. Välj **+ nytt** för att skapa en ny enhets mall och följ proceduren för att skapa. 
2. Skapa en anpassad enhets mall från början eller Välj en enhets mall från Azures enhets katalog. 


## <a name="explore-simulated-devices"></a>Utforska simulerade enheter

Programmet för **övervakning av vatten kvalitet** som du har skapat från program mal len har två simulerade enheter som är kopplade till enhets mal len för enhets övervakaren för vatten kvalitet. 

### <a name="to-view-the-devices"></a>Så här visar du enheterna:
1. Navigera till **enheten** från IoT Central vänstra navigerings fönstret. 

   ![Enheter](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devices.png)


2. Markera och klicka på en simulerad enhet 

    ![Enhet 1](./media/tutorial-waterqualitymonitoring/waterqualitymonitor-device1.png)

3. På fliken **moln egenskaper** försöker du uppdatera `Acidity (pH) Threshold` värde från `8` till `9`. 
4. Utforska fliken **enhets egenskaper** och fliken **enhets instrument panel** . 

> [!NOTE]
> Observera att alla flikar har kon figurer ATS från **Device Template-vyerna**.


### <a name="add-new-devices"></a>Lägg till nya enheter
Du kan lägga till nya enheter genom att klicka på **+ ny** på fliken **enheter** . 


## <a name="explore-and-configure-rules"></a>Utforska och konfigurera regler

I Azure IoT Central kan du skapa regler för automatisk övervakning på enhetens telemetri och utlösa en åtgärd när ett eller flera villkor uppfylls. Åtgärderna kan omfatta att skicka e-postmeddelanden eller utlösa en Microsoft Flow åtgärd eller en webhook-åtgärd för att skicka data till andra tjänster.

Det program för **övervakning av vatten kvalitet** som du har skapat mallen har två förkonfigurerade regler.

### <a name="to-view-rules"></a>Så här visar du regler:
1. Navigera till **regler** från IoT Central vänstra navigerings fönstret. 

   ![Regler](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-rules.png)


2. Välj och klicka på **hög pH-avisering** , vilket är en av de förkonfigurerade reglerna i programmet. 

     ![Hög pH-avisering](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-highphalert.png)

    `High pH alert`s regeln har kon figurer ATS för att kontrol lera mot villkors `Acidity (pH) is greater than 8`.

Nu ska vi skapa en e-poståtgärd.

Så här lägger du till en åtgärd i regeln:

1. Välj **+ e-post**. 
2.  Ange *hög pH-avisering* som det egna **visnings namnet** för åtgärden.
3. Ange den e-postadress som är kopplad till ditt IoT Central konto i **till**. 
4. Du kan också ange en anteckning som ska inkluderas i e-postmeddelandets text.
5. Välj **klar** för att slutföra åtgärden.
6. Välj **Spara** för att spara och aktivera den nya regeln. 

Inom några minuter bör du få ett e-postmeddelande när det konfigurerade **villkoret** är uppfyllt.

> [!NOTE]
> Programmet kommer att skicka ett e-postmeddelande varje gången ett villkor är uppfyllt. **Inaktivera** regeln om du vill sluta ta emot e-post från den automatiserade regeln. 
  
Så här skapar du en ny regel: 
1. Välj **+ nytt** i **reglerna** i det vänstra navigerings fönstret.

## <a name="configure-jobs"></a>Konfigurera jobb

I IoT Central kan du använda jobb för att utlösa enhets-eller moln egenskaper uppdateringar på flera enheter. Förutom egenskaper kan du också använda jobb för att utlösa enhets kommandon på flera enheter. IoT Central automatiserar arbets flödet åt dig. 

1. Gå till **jobb** från det vänstra navigerings fönstret. 
2. Klicka på **+ ny** och konfigurera ett eller flera jobb. 


## <a name="customize-your-application"></a>Anpassa ditt program 
Som verktyg kan du ändra flera inställningar för att anpassa användar upplevelsen i ditt program.

1. Välj **Administration > anpassa ditt program**.
2. Använd knappen **ändra** för att välja en bild att ladda upp som **program logo typ**.
3.  Använd knappen **ändra** för att välja en **ikon för en webb läsar ikon** som visas på flikar i webbläsaren.
4. Du kan också ersätta standard **webbläsarens färger** genom att lägga till HTML-hexadecimala färg koder.
5. Ändra även **temat** genom att klicka på **inställningarna** i huvud rubriken.

   ![Azure IoT Central anpassa ditt program](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-customize-your-application1.png)

### <a name="to-update-the-application-image"></a>Så här uppdaterar du program avbildningen:

6.  Välj **Administration > program inställningar**.

7. Använd knappen **Välj bild** för att välja en bild som ska laddas upp som program avbildning. 


  
## <a name="clean-up-resources"></a>Rensa resurser

Om du inte kommer att fortsätta att använda det här programmet tar du bort programmet med följande steg:

1. Öppna fliken Administration från den vänstra navigerings menyn i IoT Central programmet. 
2. Välj program inställningar och klicka på knappen Ta bort längst ned på sidan. 

    ![Ta bort program](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-application-settings-delete-app1.png)        



## <a name="next-steps"></a>Nästa steg

* Läs mer om [begrepp för övervakning av vatten kvalitet](./concepts-waterqualitymonitoring-architecture.md)