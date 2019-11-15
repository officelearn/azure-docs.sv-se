---
title: 'Självstudie: skapa en Smart meter analys app med IoT Central'
description: 'Självstudie: Lär dig hur du skapar ett program för smart avläsning av övervakning med hjälp av Azure IoT Central programmallar.'
author: op-ravi
ms.author: omravi
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: a73f4d75811a384eb822d1f8594a22506509d560
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112532"
---
# <a name="tutorial-create-and-walk-through-the-smart-meter-monitoring-app-template"></a>Självstudie: skapa och gå igenom App-mallen för smart avläsning av övervakning 

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Den här självstudien vägleder dig genom processen att skapa ett program för smart avläsning av övervakning, som innehåller en exempel enhets modell med simulerade data. I den här självstudiekursen lär du dig:

> [!div class="checklist"]
> * Skapa appen för smart avläsning utan kostnad
> * Program genom gång
> * Rensa resurser


Om du inte har någon prenumeration kan du [skapa ett kostnads fritt utvärderings konto](https://azure.microsoft.com/free)

## <a name="prerequisites"></a>Krav
- Ingen
- Azure-prenumeration rekommenderas, men du behöver inte testa

## <a name="create-a-smart-meter-monitoring-app"></a>Skapa en app för smart mätarövervakning 

Du kan skapa det här programmet i tre enkla steg:

1. Öppna [Azure IoT Central start sida](https://apps.azureiotcentral.com) och klicka på **skapa** för att skapa ett nytt program. 

2. Välj fliken **energi** och klicka på **skapa app** under fönster panel för **övervakning av smarta Mät** värden.

    > [!div class="mx-imgBorder"]
    > ![Bygg app](media/tutorial-iot-central-smart-meter/smart-meter-build.png)
    

3. **Skapa app** öppnar formuläret **ny** app. Fyll i den begärda informationen som visas i bilden nedan:
    * **Program namn**: Välj ett namn för IoT Central programmet. 
    * **URL**: välj en IoT Central URL, plattformen verifierar att den är unik.
    * **7-dagars kostnads fri utvärderings version**: om du redan har en Azure-prenumeration rekommenderas standardinställningen. Om du inte har en Azure-prenumeration börjar du med den kostnads fria utvärderings versionen.
    * **Fakturerings information**: själva programmet är kostnads fritt. Katalogen, Azure-prenumerationen och regions informationen krävs för att etablera resurserna för din app.
    * Klicka på knappen **skapa** längst ned på sidan så skapas din app på en minut.     
        > [!div class="mx-imgBorder"]
        > ![nytt program formulär](media/tutorial-iot-central-smart-meter/smart-meter-create-new-app.png)


### <a name="verify-the-application-and-simulated-data"></a>Verifiera programmet och simulerade data

Den nyligen skapade appen för smart avläsning är din app och du kan ändra den när du vill. Vi ser till att appen distribueras och fungerar som förväntat innan du ändrar den.

Om du vill kontrol lera att appen skapas och data simuleringen går du till **instrument panelen**. Om du kan se panelerna med vissa data, lyckades din app-distribution. Data simuleringen kan ta några minuter att generera data, så ge den 1-2 minuter. 

## <a name="application-walk-through"></a>Program genom gång
När du har distribuerat app-mallen levereras den med exempel på Smart meters enhet, enhets modell och en instrument panel. 

Adatum är ett fiktivt energi företag som övervakar och hanterar smarta mätare. På instrument panelen för övervakning av smarta mätare ser du egenskaper för smart mätare, data och exempel kommandon. Det gör det möjligt för operatörer och support team att proaktivt utföra följande aktiviteter innan de blir i support ärenden: 
* Granska den senaste mätar informationen och dess installerade plats på kartan
* Kontrol lera mätar nätverket och anslutnings statusen proaktivt 
* Övervaka minsta och högsta tillåtna spännings läsningar för nätverks hälsa 
* Granska trender för energi, kraft och spänning för att fånga eventuella avvikande mönster 
* Spåra den totala energi förbrukningen för planering och fakturering
* Kommando-och kontroll åtgärder, till exempel version för åter anslutning, mätare och uppdatering av inbyggd program vara. I mallen visar kommando knapparna de möjliga funktionerna och skickar inte riktiga kommandon. 

> [!div class="mx-imgBorder"]
> instrument panel för ![Smart mätnings övervakning](media/tutorial-iot-central-smart-meter/smart-meter-dashboard.png)

### <a name="devices"></a>Enheter
Appen levereras med ett exempel på en smart meters enhet. Du kan se enhets informationen genom att klicka på fliken **enheter** .

> [!div class="mx-imgBorder"]
> ![Smart meters enheter](media/tutorial-iot-central-smart-meter/smart-meter-devices.png)

Klicka på **SM0123456789** -länken exempel på enhet för att se information om enheten. Du kan uppdatera enhetens skrivbara egenskaper på sidan **Uppdatera egenskaper** och visualisera de uppdaterade värdena på instrument panelen.

> [!div class="mx-imgBorder"]
> ![Smart mätar egenskaper](media/tutorial-iot-central-smart-meter/smart-meter-device-properties.png)

### <a name="device-template"></a>Enhets mall
Klicka på fliken **enhets mallar** om du vill se enhets modellen för smart mätare. Modellen har ett för hands definierat gränssnitt för data, egenskaper, kommandon och vyer.

> [!div class="mx-imgBorder"]
> ![Smart meters enhet mallar](media/tutorial-iot-central-smart-meter/smart-meter-device-template.png)


## <a name="clean-up-resources"></a>Rensa resurser
Om du väljer att inte fortsätta använda programmet tar du bort programmet med följande steg:

1. Öppna fliken Administration i den vänstra rutan
2. Välj program inställningar och klicka på knappen Ta bort längst ned på sidan. 

    > [!div class="mx-imgBorder"]
    > ![ta bort program](media/tutorial-iot-central-smart-meter/smart-meter-delete-app.png)


## <a name="next-steps"></a>Nästa steg
* Lär dig mer om program arkitekturen för smart avläsning i [begrepps artikeln](https://docs.microsoft.com/azure/iot-central/energy/concept-iot-central-smart-meter-app)
* Skapa mallar för smart avläsning av program vara kostnads fritt: [appen Smart avläsning](https://apps.azureiotcentral.com/build/new/smart-meter-monitoring)
* Läs mer om IoT Central i [IoT Central översikt](https://docs.microsoft.com/azure/iot-central/)
