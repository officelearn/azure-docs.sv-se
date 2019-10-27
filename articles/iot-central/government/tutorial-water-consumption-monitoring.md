---
title: Skapa en app för övervakning av program förbrukning med Azure IoT Central | Microsoft Docs
description: Lär dig att skapa ett program för övervakning av vatten förbrukning med hjälp av Azure IoT Central programmallar.
author: miriambrus
ms.author: miriamb
ms.date: 09/24/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 543d019628e0f3c8423a71a4aa8d5b24a60e82e3
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2019
ms.locfileid: "72956721"
---
# <a name="tutorial-create-a-water-consumption-monitoring-application-in-iot-central"></a>Självstudie: skapa ett program för övervakning av vatten förbrukning i IoT Central

I den här självstudien får du hjälp med att skapa ett program för övervakning av Azure IoT Central vatten förbrukning från program mal len för övervakning av IoT Central. 

I självstudien får du lära dig att: 

> [!div class="checklist"]
> * Använd övervaknings mal len Azure IoT Central **vattenförbrukning** för att skapa ett program för övervakning av vatten förbrukning
> * Utforska och anpassa instrument panelen för operatör 
> * Utforska enhets mal len
> * Utforska simulerade enheter
> * Utforska och konfigurera regler
> * Konfigurera jobb
> * Anpassa din program anpassning med adresser

## <a name="prerequisites"></a>Krav

För att slutföra den här självstudien behöver du:
-  En Azure-prenumeration rekommenderas. Om du inte har någon Azure-prenumeration kan du skapa en på [sidan för Azure-registrering](https://aka.ms/createazuresubscription).

## <a name="create-water-consumption-monitoring-app-in-iot-central"></a>Skapa appen för övervakning av vattenförbrukning i IoT Central

I det här avsnittet ska vi använda mallen för övervakning av Azure IoT Central- **vattenförbrukning** för att skapa ett program för övervakning av vatten förbrukning i IoT Central.

Så här skapar du ett nytt program för övervakning av Azure IoT Central vatten förbrukning:  

1. Gå till webbplatsen för [Azure IoT Central start sida](https://aka.ms/iotcentral) .

    Om du har en Azure-prenumeration loggar du in med de autentiseringsuppgifter som du använder för att komma åt den, annars loggar du in med en Microsoft-konto:

    ![Ange ditt organisationskonto](media/tutorial-waterconsumptionmonitoring/sign-in.png)

2. Klicka på **build** i den vänstra navigerings menyn och välj fliken **myndigheter** . Sidan myndigheter visar flera myndighets programmallar.

   ![Bygg app-mallar för myndigheter](./media/tutorial-waterconsumptionmonitoring/iotcentral-government-tab-overview1.png)

1. Välj program mal len för **övervakning av vattenförbrukning** . Den här mallen innehåller exempel på enhets mall för vattenförbrukning, simulerad enhet, operatörs instrument panel och förkonfigurerade övervaknings regler.    

2. Klicka på **skapa app**, så öppnas formuläret för att skapa **nya program** med följande fält:
    * **Program namn**: som standard använder programmet *vattenförbruknings övervakning* följt av en unik ID-sträng som IoT Central genererar. Du kan också välja ett eget program namn. Du kan senare ändra program namnet.
    * **URL**: IoT Central genererar automatiskt en URL åt dig baserat på programmets namn. Du kan välja att uppdatera URL: en till dina önskemål. Du kan ändra URL: en senare. 
    * Om du har en Azure-prenumeration anger du din *katalog, din Azure-prenumeration och region*. Om du inte har någon prenumeration kan du aktivera den **kostnads fria utvärderings versionen på 7 dagar** och slutföra den kontakt information som krävs.  

    Mer information om kataloger och prenumerationer finns i [snabbstarten om att skapa ett program](../core/quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

5. Klicka på knappen **skapa** längst ned på sidan. 

    ![Sidan Skapa program i Azure IoT Central](./media/tutorial-waterconsumptionmonitoring/new-application-waterconsumptionmonitoring.png)

6. Nu har du skapat en app för övervakning av dricks-appar med hjälp av mallen Azure IoT Central **vattenförbruknings övervakning** .

Gratulerar! Du är klar med att skapa ett program för övervakning av vatten kvalitet, som medföljer förkonfigurerat:
* Exempel instrument panels instrument paneler
* Exempel på fördefinierade vatten flöden och ventiler för ventilbaserade enheter
* Simulerade vatten flöden och smarta ventil enheter
* Förkonfigurerade regler och jobb
* Exempel anpassning med vita etiketter 

Det är ditt program och du kan ändra det när som helst. Nu ska vi utforska programmet och göra några anpassningar.  

## <a name="explore-and-customize-operator-dashboard"></a>Utforska och anpassa instrument panelen för operatör 
När du har skapat det program som du har skapat på instrument panelen för exempel operatören heter **Wide World vatten konsumtions instrument panel**.

   ![Instrument panel för övervakning av vatten förbrukning](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-dashboardfull.png)

Som ett verktyg kan du skapa och anpassa vyer på instrument panelen för operatörer. Vi går igenom instrument panelen innan du försöker anpassa. 

> [!NOTE]
> Alla data som visas i instrument panelen baseras på simulerade enhets data, som vi kommer att utforska i nästa avsnitt. 
  
Instrument panelen består av olika typer av paneler:

* **Bild panel för Wide World vatten verktyg**: den första panelen på instrument panelen är en bild panel med ett fiktivt vatten verktyg "Wide World vatten". Du kan anpassa panelen och placera en egen avbildning eller ta bort den. 

* KPI-panelen i **genomsnitts flöde**: KPI-panelen är konfigurerad att visa som ett exempel *på genomsnittet under de senaste 30 minuterna*. Du kan anpassa KPI-paneler och ange en annan typ och ett tidsintervall.

* Sedan har den rätt i panel *enhetens kommando* paneler för att **stänga ventil**, **öppna ventiler**eller **ställa in ventil position**. Om du klickar på kommandona tas du till kommando sidan simulerad enhets enhet. I IoT Central ett *kommando* är en *enhets kapacitets* typ som vi kommer att utforska senare i **enhetens mall-avsnitt** i den här självstudien.

*  **Områdes mappning för vatten distribution**: kartan använder Azure Maps, som du kan konfigurera direkt i Azure IoT Central. Kart panelen visar enhetens plats. Försök att hovra över kartan och testa kontrollerna över kartan, t. ex. *Zooma in*, *Zooma ut* eller *expandera*. 

    ![Instrument panels karta för instrument förbrukning](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-dashboard-map.png)

* Linje diagram över **Genomsnittligt vatten flödes linje diagram** och **miljö villkor**: du kan visualisera en eller flera enhets telemetrivärden ritade som ett linje diagram över ett önskat tidsintervall.  

* **Diagram över genomsnittlig ventil tryck termisk karta**: du kan välja termisk karta visualiserings typ för enhets telemetridata som du är intresse rad av att titta på fördelningen över ett tidsintervall med ett färg index.

* **Återställ innehålls panelen för aviserings tröskeln**: du kan ta med anrop till innehålls paneler för åtgärds innehåll som bäddar in länken till en åtgärds sida. I detta fall återställs aviserings tröskeln till de program **jobb** där du kan köra uppdateringar av enhets egenskaper, som vi kommer att utforska senare i avsnittet **Konfigurera jobb** i den här självstudien.

* **Egenskaps paneler**: instrument panelen visar **ventilens operativa information**, **flödes tröskelvärden för aviseringar**och **underhålls information** som är enhets egenskaper.  


### <a name="customize-dashboard"></a>Anpassa instrument panelen 

Som ett verktyg kan du anpassa vyer på instrument panelen för operatörer. Du kan prova:
1. Klicka på **Redigera** för att anpassa den **breda instrument panelen för övervakning av vatten konsumtion**. Du kan anpassa instrument panelen genom att klicka på **Redigera** -menyn. När instrument panelen är i **redigerings** läge kan du lägga till nya paneler, eller så kan du konfigurera 

     ![Redigera instrument panel](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-edit-dashboard.png)

2. Klicka på **+ ny** för att skapa en ny instrument panel och konfigurera från början. Du kan ha flera instrument paneler och du kan navigera mellan dina instrument paneler på instrument panelens meny. 

## <a name="explore-device-template"></a>Utforska enhets mal len
En enhets mall i Azure IoT Central definierar funktionen hos en enhet, som kan vara telemetri, egenskap eller kommando. Som ett verktyg kan du definiera en eller flera enhetsspecifika mallar i IoT Central som representerar funktionerna för de enheter som du ansluter till. 
 

Programmet för **övervakning av vattenförbrukning** levereras med två referens enhets mallar som representerar en *flödes mätare* och en *Smart ventil* -enhet. 

Så här visar du enhets mal len:

1. Klicka på **mallar för enheter** i det vänstra navigerings fönstret i programmet i IoT Central. 
    I listan mallar för enheter visas två **flödes mätare** för enhets mätare och **smarta ventiler**

   ![Enhets mall](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate.png)

2. Klicka på enhets mal len **flödes mätare** och bekanta dig med enhets funktionerna 

     ![Flödes mätare för enhets mal len](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate-flowmeter.png)

### <a name="customizing-the-device-template"></a>Anpassa enhets mal len

Försök att anpassa följande:
1. Navigera för att **Anpassa** från menyn på enhets mal len
2. Hitta typen av telemetri för `Temperature`
3. Uppdatera **visnings namnet** för `Temperature` till `Reported temperature`
4. Uppdatera mått enhet eller ange *minsta värde* och *Max värde*
5. **Spara** ändringar 

### <a name="add-a-cloud-property"></a>Lägg till en moln egenskap 
1. Gå till **moln egenskap** från enhetens mall-menyn
2. Lägg till en ny moln egenskap genom att klicka på **+ Lägg till moln egenskap**. 
    I IoT Central kan du lägga till en egenskap som är relevant för enheten. Som exempel kan en moln egenskap vara en aviserings tröskel som är speciell för installations områden, till gångs information eller underhålls information osv. 
3. **Spara** ändringar 
 
### <a name="views"></a>Vyer 
Enhets mal len för vattenförbruknings övervakning levereras med fördefinierade vyer. Utforska vyerna så kan du göra uppdateringar. Vyerna definierar hur operatörer ser enhets data, men som även anger moln egenskaper. 

  ![Vyer för enhets mal len](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate-views.png)

### <a name="publish"></a>Publicera 
Om du har gjort några ändringar måste du **publicera** enhets mal len. 

### <a name="create-a-new-device-template"></a>Skapa en ny enhetsmall 
- Välj **+ nytt** för att skapa en ny enhets mall och följ proceduren för att skapa. Du kommer att kunna skapa en anpassad enhets mall från början eller välja en enhets mall från Azures enhets katalog. 

## <a name="explore-simulated-devices"></a>Utforska simulerade enheter
I IoT Central kan du skapa simulerade enheter för att testa enhetens mall och program. Programmet för **övervakning av vattenförbrukning** har två simulerade enheter som är kopplade till mallarna *flödes mätare* och *Smart ventil* -enhet. 

### <a name="to-view-the-devices"></a>Så här visar du enheterna:
1. Navigera till **enheten** från IoT Central vänstra navigerings fönstret. 

   ![Enheter](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devices.png)

2. Klicka på en **Smart ventil 1** 

    ![Enhet 1](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitor-device1.png)

3.  I **enhets kommandona** kan du se de tre enhets kommandona *öppen ventil*, *Stäng ventil*och *ställa in ventil position* som är funktioner som definierats i mallen *Smart ventil* enhet. 
4. Utforska fliken **enhets egenskaper** och fliken **enhets instrument panel** . 

> [!NOTE]
> Observera att alla flikar har kon figurer ATS från Device Template-vyerna.

### <a name="add-new-devices"></a>Lägg till nya enheter
* Lägg till nya enheter genom att klicka på **+ ny** på fliken **enheter** . 

## <a name="explore-and-configure-rules"></a>Utforska och konfigurera regler

I Azure IoT Central kan du skapa regler för automatisk övervakning på enhetens telemetri och utlösa åtgärder när ett eller flera villkor uppfylls. Åtgärderna kan omfatta att skicka e-postmeddelanden eller utlösa en Microsoft Flow åtgärd eller en webhook-åtgärd för att skicka data till andra tjänster.

Det program för **övervakning av vatten förbrukning** som du har skapat mallen har tre förkonfigurerade regler.

### <a name="to-view-rules"></a>Så här visar du regler:
1. Navigera till **regler** från IoT Central vänstra navigerings fönstret. 

   ![Regler](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-rules.png)

2. Välj och klicka på **hög pH-avisering** , vilket är en av de förkonfigurerade reglerna i programmet.

     ![Hög pH-avisering](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-highflowalert.png)

    `High flow alert`s regeln har kon figurer ATS för att kontrol lera mot villkors `Acidity (pH)` `greater than` `Max flow threshold`. Högsta flödes tröskel är en moln egenskap som definierats i mallen enhet *Smart ventil* enhet. Värdet för `Max flow threshold` anges per enhets instans. 

Nu ska vi skapa en e-poståtgärd.

Så här lägger du till en åtgärd i regeln:

1. Välj **+ e-post**. 
1. Ange *hög pH-avisering* som det egna **visnings namnet** för åtgärden.
    * Ange den e-postadress som är kopplad till ditt IoT Central konto i **till**. 
1. Du kan också ange en anteckning som ska inkluderas i e-postmeddelandets text.
1. Välj **klar** för att slutföra åtgärden.
1. Välj **Spara** för att spara och aktivera den nya regeln. 

Inom några minuter bör du få ett e-postmeddelande när det konfigurerade **villkoret** är uppfyllt.

> [!NOTE]
> Programmet kommer att skicka ett e-postmeddelande varje gången ett villkor är uppfyllt. **Inaktivera** regeln om du vill sluta ta emot e-post från den automatiserade regeln. 
  
Så här skapar du en ny regel: 
- Välj **+ nytt** i **reglerna** i det vänstra navigerings fönstret.

## <a name="configure-jobs"></a>Konfigurera jobb

I IoT Central kan du använda jobb för att utlösa enhets-eller moln egenskaps uppdateringar på flera enheter. Förutom egenskaper kan du också använda jobb för att utlösa enhets kommandon på flera enheter. IoT Central automatiserar arbets flödet åt dig. 

1. Gå till **jobb** från det vänstra navigerings fönstret. 
2. Klicka på **+ ny** och konfigurera ett eller flera jobb. 


## <a name="customize-your-application"></a>Anpassa ditt program 
Som verktyg kan du ändra flera inställningar för att anpassa användar upplevelsen i ditt program.

1.  Gå till **Administration > anpassa ditt program**.
3. Använd knappen **ändra** för att välja en bild att ladda upp som **program logo typ**.
4. Använd knappen **ändra** för att välja en **ikon för en webb läsar ikon** som visas på flikar i webbläsaren.
5. Du kan också ersätta standard **webbläsarens färger** genom att lägga till HTML-hexadecimala färg koder.

   ![Azure IoT Central anpassa ditt program](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-customize-your-application.png)

1.  Du kan också ändra program avbildningarna genom att gå till **administrations > program inställningar** och **välja knappen bild** för att välja en bild som ska laddas upp som program avbildning. 
2. Slutligen kan du även ändra **temat** genom att klicka på **Inställningar** på program huvud rubriken för programmet. 

  
## <a name="clean-up-resources"></a>Rensa resurser

Om du inte kommer att fortsätta att använda det här programmet tar du bort programmet med följande steg:

1. Öppna fliken Administration från den vänstra navigerings menyn i IoT Central programmet. 
2. Välj program inställningar och klicka på knappen Ta bort längst ned på sidan. 


## <a name="next-steps"></a>Nästa steg

* Läs mer om [begrepp för övervakning av vattenförbrukning](./concepts-waterconsumptionmonitoring-architecture.md)
