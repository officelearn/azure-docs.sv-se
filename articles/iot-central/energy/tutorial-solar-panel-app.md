---
title: 'Självstudiekurs: Skapa en app för övervakning av solceller med IoT Central'
description: 'Självstudiekurs: Lär dig hur du skapar ett solpanelprogram med hjälp av Azure IoT Central-programmallar.'
author: op-ravi
ms.author: omravi
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: d5ea3d3420cb598693ccaede7ee10d2f8c4fd839
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77025783"
---
# <a name="tutorial-create-and-walk-through-the-solar-panel-monitoring-app-template"></a>Självstudiekurs: Skapa och gå igenom webbpanelens övervakningsappmall 



Den här självstudien guidar dig genom processen att skapa övervakningsprogrammet för solpaneler, som innehåller en exempelenhetsmodell med simulerade data. I den här självstudiekursen lär du dig:


> [!div class="checklist"]
> * Skapa solpanelsappen gratis
> * Genomgång av program
> * Rensa resurser


Om du inte har en prenumeration [skapar du ett kostnadsfritt utvärderingskonto](https://azure.microsoft.com/free)

## <a name="prerequisites"></a>Krav
- Inget
- Azure-prenumeration rekommenderas, men krävs inte för att prova


## <a name="create-a-solar-panel-monitoring-app"></a>Skapa en app för solpanelsövervakning 

Du kan skapa det här programmet i tre enkla steg:

1. Öppna [Azure IoT Central hemsida](https://apps.azureiotcentral.com) och klicka på **Skapa** för att skapa ett nytt program. 

2. Välj **fliken Energi** och klicka på Skapa **app** under panelen För övervakning **av bildskärmsprogrammet.** 

    > [!div class="mx-imgBorder"]
    > ![Skapa app](media/tutorial-iot-central-solar-panel/solar-panel-build.png)
  
3. **Skapa app** öppnas **Nytt ansökningsformulär.** Fyll i de begärda uppgifterna enligt figuren nedan:
    * **Programnamn**: Välj ett namn för ditt IoT Central-program. 
    * **URL:** Välj en IoT Central URL, kommer plattformen att kontrollera dess unika.
    * **7-dagars kostnadsfri utvärderingsversion:** Om du redan har en Azure-prenumeration rekommenderas standardinställning. Om du inte har en Azure-prenumeration börjar du med kostnadsfri utvärderingsversion.
    * **Fakturering Info:** Själva programmet är gratis. Information om katalog, Azure-prenumeration och region krävs för att etablera resurserna för din app.
    * Klicka på **Knappen Skapa** längst ned på sidan så skapas appen inom en minut eller så.
        ![Nytt ansökningsformulär](media/tutorial-iot-central-solar-panel/solar-panel-create-app.png)
        
        ![Ny faktureringsinformation för ansökningsformulär](media/tutorial-iot-central-solar-panel/solar-panel-create-app-billinginfo.png)


### <a name="verify-the-application-and-simulated-data"></a>Verifiera programmet och simulerade data

Den nyskapade solpanelappen är din app och du kan ändra den när som helst. Nu ska vi se till att appen distribueras och fungerar som förväntat innan du ändrar den.

Om du vill verifiera att appen skapas och datasimuleringen går du till **instrumentpanelen**. Om du kan se panelerna med vissa data lyckades appdistributionen. Datasimuleringen kan ta några minuter att generera data, så ge den 1-2 minuter. 

## <a name="application-walk-through"></a>Genomgång av program
När du har distribuerat appmallen levereras den med exempel på smart mätarenhet, enhetsmodell och instrumentpanel.

Adatum är ett fiktivt energiföretag som övervakar och hanterar solpaneler. På instrumentpanelen för övervakning av solpaneler visas egenskaper, data och exempelkommandon på solpanelen. Det gör det möjligt för operatörer och supportteam att proaktivt utföra följande aktiviteter innan det förvandlas till supportincidenter:
* Granska den senaste panelinformationen och dess installerade plats på kartan
* Kontrollera panelstatus och anslutningsstatus proaktivt
* Granska energiproduktion och temperaturtrender för att fånga eventuella avvikande mönster
* Spåra den totala energiproduktionen för planering och fakturering
* Kommando- och kontrollåtgärder som aktivera panel- och uppdateringsversion av den inbyggda programvaran. I mallen visar kommandoknapparna möjliga funktioner och skickar inte riktiga kommandon.

> [!div class="mx-imgBorder"]
> ![Instrumentpanelens övervakningsinstrumentpanel](media/tutorial-iot-central-solar-panel/solar-panel-dashboard.png)

### <a name="devices"></a>Enheter
Appen levereras med ett exempel solpanel enhet. Du kan se enhetsinformationen genom att klicka på fliken **Enheter.**

> [!div class="mx-imgBorder"]
> ![Solpanelsenheter](media/tutorial-iot-central-solar-panel/solar-panel-device.png)


Klicka på exempelenheten **SP0123456789** länk för att se enhetsinformation. På sidan **Uppdatera egenskaper** kan du uppdatera enhetens skrivbara egenskaper och visualisera de uppdaterade värdena på instrumentpanelen. 

> [!div class="mx-imgBorder"]
> ![Egenskaper för solpaneler](media/tutorial-iot-central-solar-panel/solar-panel-device-properties.png)


### <a name="device-template"></a>Enhetsmall
Klicka på fliken **Enhetsmallar** för att se solpanelenhetsmodellen. Modellen har fördefiniera gränssnitt för data, egenskap, kommandon och vyer.

> [!div class="mx-imgBorder"]
> ![Mall för solpanelenheter](media/tutorial-iot-central-solar-panel/solar-panel-device-templates.png)


## <a name="clean-up-resources"></a>Rensa resurser
Om du bestämmer dig för att inte fortsätta använda det här programmet tar du bort programmet med följande steg:

1. Öppna fliken Administration i den vänstra rutan
2. Välj Programinställningar och klicka på Knappen Ta bort längst ned på sidan. 

    > [!div class="mx-imgBorder"]
    > ![Ta bort program](media/tutorial-iot-central-solar-panel/solar-panel-delete-app.png)


## <a name="next-steps"></a>Nästa steg
* Lär dig mer om arkitektur för solpaneler [i apptexten finns i konceptartikeln](https://docs.microsoft.com/azure/iot-central/energy/concept-iot-central-solar-panel-app)
* Skapa solpanel programmallar gratis: [solpanel app](https://apps.azureiotcentral.com/build/new/solar-panel-monitoring)
* Läs mer om IoT Central, se [IoT Central översikt](https://docs.microsoft.com/azure/iot-central/)

