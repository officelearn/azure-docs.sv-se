---
title: Skapa en app för att övervaka en solter med IoT Central | Microsoft Docs
description: Lär dig hur du skapar ett sol panel program med hjälp av Azure IoT Central programmallar.
author: op-ravi
ms.author: omravi
ms.date: 10/23/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: dd7e3ed767539f8de46e5f77a39b1e7bac0cb3b0
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/27/2019
ms.locfileid: "72965813"
---
# <a name="tutorial-create-and-walk-through-the-solar-panel-monitoring-app-template"></a>Självstudie: skapa och gå igenom en mall för övervakning av solpanels program 

Den här självstudien vägleder dig genom processen för att skapa övervaknings programmet för sol panelen, som innehåller en exempel enhets modell med simulerade data. I den här självstudiekursen lär du dig:


> [!div class="checklist"]
> * Skapa en testpanel-app utan kostnad
> * Program genom gång
> * Rensa resurser


Om du inte har någon prenumeration kan du [skapa ett kostnads fritt utvärderings konto](https://azure.microsoft.com/free)

## <a name="prerequisites"></a>Krav
- Inget
- Azure-prenumeration rekommenderas, men du behöver inte testa


## <a name="create-a-solar-panel-monitoring-app"></a>Skapa en övervaknings app för en sol panel 

Du kan skapa det här programmet i tre enkla steg:

1. Öppna [Azure IoT Central start sida](https://apps.azureiotcentral.com) och klicka på **skapa** för att skapa ett nytt program. 

2. Välj fliken **energi** och klicka på **skapa app** i övervaknings program panelen i **sol panelen** . 

    > [!div class="mx-imgBorder"]
    > ![Bygg app](media/tutorial-iot-central-solar-panel/solar-panel-build.png)
  
3. **Skapa app** öppnar **nytt program** formulär. Fyll i den begärda informationen som visas i bilden nedan:
    * **Program namn**: Välj ett namn för IoT Central programmet. 
    * **URL**: välj en IoT Central URL, plattformen verifierar att den är unik.
    * **7-dagars kostnads fri utvärderings version**: om du redan har en Azure-prenumeration rekommenderas standardinställningen. Om du inte har en Azure-prenumeration börjar du med den kostnads fria utvärderings versionen.
    * **Fakturerings information**: själva programmet är kostnads fritt. Katalogen, Azure-prenumerationen och regions informationen krävs för att etablera resurserna för din app.
    * Klicka på knappen **skapa** längst ned på sidan så skapas din app på en minut.
        > [!div class="mx-imgBorder"]
        > ![nytt program formulär](media/tutorial-iot-central-solar-panel/solar-panel-create-app.png)


### <a name="verify-the-application-and-simulated-data"></a>Verifiera programmet och simulerade data

Den nyligen skapade solpanelen-appen är din app och du kan ändra den när du vill. Vi ser till att appen distribueras och fungerar som förväntat innan du ändrar den.

Om du vill kontrol lera att appen skapas och data simuleringen går du till **instrument panelen**. Om du kan se panelerna med vissa data, lyckades din app-distribution. Data simuleringen kan ta några minuter att generera data, så ge den 1-2 minuter. 

## <a name="application-walk-through"></a>Program genom gång
När du har distribuerat app-mallen levereras den med exempel på Smart meters enhet, enhets modell och instrument panel.

Adatum är ett fiktivt energi företag som övervakar och hanterar sol paneler. På instrument panelen för övervakning av sol panelen ser du egenskaper för sol panel, data och exempel kommandon. Det gör det möjligt för operatörer och support team att proaktivt utföra följande aktiviteter innan de blir i support ärenden:
* Granska den senaste panel informationen och dess installerade plats på kartan
* Kontrol lera panel status och anslutnings status proaktivt
* Granska utvecklings-och temperatur trenderna för att fånga eventuella avvikande mönster
* Spåra den totala energi generationen för planering och fakturering
* Kommando-och kontroll åtgärder som aktiverings panel och uppdatering av inbyggd program vara. I mallen visar kommando knapparna de möjliga funktionerna och skickar inte riktiga kommandon.

> [!div class="mx-imgBorder"]
> instrument panel för ![sol panels övervakning](media/tutorial-iot-central-solar-panel/solar-panel-dashboard.png)

### <a name="devices"></a>Enheter
Appen levereras med ett exempel på en sol-panels enhet. Du kan se enhets informationen genom att klicka på fliken **enheter** .

> [!div class="mx-imgBorder"]
> ![sol panels enheter](media/tutorial-iot-central-solar-panel/solar-panel-device.png)


Klicka på **SP0123456789** -länken exempel på enhet för att se information om enheten. På sidan **Uppdatera egenskaper** kan du uppdatera enhetens skrivbara egenskaper och visualisera de uppdaterade värdena på instrument panelen. 

> [!div class="mx-imgBorder"]
> ![egenskaper för sol panelen](media/tutorial-iot-central-solar-panel/solar-panel-device-properties.png)


### <a name="device-template"></a>Enhets mall
Klicka på fliken **enhets mallar** om du vill se enhets modellen för sol panelen. Modellen har ett för hands definierat gränssnitt för data, egenskaper, kommandon och vyer.

> [!div class="mx-imgBorder"]
> mall för ![sol panel enheter](media/tutorial-iot-central-solar-panel/solar-panel-device-templates.png)


## <a name="clean-up-resources"></a>Rensa resurser
Om du väljer att inte fortsätta använda programmet tar du bort programmet med följande steg:

1. På den vänstra menyn öppnar du fliken Administration
2. Välj program inställningar och klicka på knappen Ta bort längst ned på sidan. 

    > [!div class="mx-imgBorder"]
    > ![ta bort program](media/tutorial-iot-central-solar-panel/solar-panel-delete-app.png)


## <a name="next-steps"></a>Nästa steg
* Lär dig mer om program arkitekturen på solpanelen se [begrepps artikeln](https://docs.microsoft.com/azure/iot-central/energy/concept-iot-central-solar-panel-app)
* Skapa programmallar på solpanelen gratis: [solpanels app](https://apps.azureiotcentral.com/build/new/solar-panel-monitoring)
* Läs mer om IoT Central i [IoT Central översikt](https://docs.microsoft.com/azure/iot-central/)

