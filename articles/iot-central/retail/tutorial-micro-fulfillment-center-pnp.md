---
title: Själv studie kurs för Micro-uppfyllelse Center-Mall | Microsoft Docs
description: En själv studie kurs om program mal len för Micro-uppfyllelse Center för IoT Central
author: avneet723
ms.author: avneets
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 01/09/2020
ms.openlocfilehash: 2ed6f37bc3b00397fa6331a501e1b16c8d622b5f
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2020
ms.locfileid: "77027792"
---
# <a name="tutorial-deploy-and-walk-through-a-micro-fulfillment-center-application-template"></a>Självstudie: Distribuera och gå igenom en Programmall för Micro-uppfyllelse Center

I den här självstudien kommer vi att använda programmallen för Azure IoT Central ***Micro-uppfyllelse Center*** för att visa hur du skapar en detalj handels lösning. Du får lära dig mer om hur du distribuerar MFC-mallen, vad som ingår i rutan och vad du kanske vill göra härnäst.

I den här guiden får du lära dig hur man: 
> [!div class="checklist"]
> * Använd mallen Azure IoT Central **Micro-fullföljning Center** för att skapa ett program för åter försäljning
> * Gå igenom programmet 

## <a name="prerequisites"></a>Krav
För att kunna slutföra den här själv studie serien behöver du:
* En Azure-prenumeration. Du kan välja att använda en kostnads fri 7-dagars utvärderings version. Om du inte har någon Azure-prenumeration kan du skapa en på [sidan för Azure-registrering](https://aka.ms/createazuresubscription).

## <a name="create-an-application"></a>Skapa ett program 
I det här avsnittet skapar du ett nytt Azure IoT Central-program från en mall. Du använder det här programmet i själv studie serien för att bygga en komplett lösning.

Så här skapar du ett nytt Azure IoT Central-program:

1. Gå till webbplatsen för [Azure IoT Central Application Manager](https://aka.ms/iotcentral) .
1. Om du har en Azure-prenumeration loggar du in med de autentiseringsuppgifter som du använder för att komma åt den, annars loggar du in med en Microsoft-konto:

   ![Ange ditt organisationskonto](./media/tutorial-in-store-analytics-create-app-pnp/sign-in.png)

1. Börja skapa ett nytt Azure IoT Central-program genom att välja **Nytt program**.

1. Välj **åter försäljning**.  På sidan detalj handel visas flera mallar för åter försäljning.

Skapa ett nytt program för Micro-uppfyllelse Center som använder för hands versions funktioner:  
1. Välj program mal len för **Micro-uppfyllelse Center** . Den här mallen innehåller enhets mallar för alla enheter som används i självstudien. Mallen tillhandahåller också en instrument panel för instrument panelen för att övervaka villkor inom ditt uppfyllande Center samt villkoren för dina robot operatörer. 

    > [!div class="mx-imgBorder"]
    > Program typ för ![Micro-uppfyllelse](./media/tutorial-micro-fulfillment-center-app-pnp/iotc-retail-homepage-mfc.png)
    
1. Du kan också välja ett eget **program namn**.  Program mal len baseras på det fiktiva företaget Northwind Traders. 

    > [!NOTE]
    > Om du använder ett eget **program namn**måste du fortfarande använda ett unikt värde för programmets **URL**.

1. Om du har en Azure-prenumeration anger du din *katalog, din Azure-prenumeration och region*. Om du inte har någon prenumeration kan du aktivera den **kostnads fria utvärderings versionen på 7 dagar** och slutföra den kontakt information som krävs.  

    Mer information om kataloger och prenumerationer finns i [snabbstarten om att skapa ett program](../preview/quick-deploy-iot-central.md).

1. Välj **Skapa**.

> [!div class="mx-imgBorder"]
> ![skapa Micro-uppfyllelse-program](./media/tutorial-micro-fulfillment-center-app-pnp/iotc-retail-create-app-mfc.png)

## <a name="walk-through-the-application"></a>gå igenom programmet 

### <a name="dashboard"></a>Instrumentpanel 

När du har distribuerat app-mallen hamnar du först på **instrument panelen för Northwind Traders Micro-uppfyllelse Center**. Northwinds näringsidkare är en fiktiv åter försäljare som har ett mikropresterande Center som hanteras i det här IoT Central programmet. På instrument panelen för operatören visas information och telemetri om enheterna i den här mallen tillsammans med en uppsättning kommandon, jobb och åtgärder som du kan vidta. Instrument panelen delas logiskt i två avsnitt (vänster och höger). Till vänster har du möjlighet att övervaka miljö förhållandena i den uppfyllande strukturen och till höger kan du övervaka hälso tillståndet för en robot transport i anläggningen.  

Från instrument panelen kan du:
   * Se enhetens telemetri, till exempel antal plockningar, antal behandlade beställningar och egenskaper som struktur systemets status osv.  
   * Visa **våningsplanet** och platsen för de automatiska transport företagen i den uppfyllande strukturen.
   * Utlös kommandon, till exempel återställning av kontroll systemet, uppdatering av den inbyggda program varan, omkonfigurering av nätverket osv.

> [!div class="mx-imgBorder"]
> Instrument panel för ![Micro-uppfyllelse Center](./media/tutorial-micro-fulfillment-center-app-pnp/mfc-dashboard1.png)
   * Se ett exempel på instrument panelen som en operatör kan använda för att övervaka villkoren i uppfyllande Center. 
   * Övervaka hälso tillståndet för de nytto laster som körs på gateway-enheten inom uppfyllande Center.    

> [!div class="mx-imgBorder"]
> Instrument panel för ![Micro-uppfyllelse Center](./media/tutorial-micro-fulfillment-center-app-pnp/mfc-dashboard2.png)

## <a name="device-template"></a>Enhets mall
Om du klickar på fliken enhets mallar ser du att det finns två olika enhets typer som är en del av mallen: 
   * **Robot frakt firma**: den här enhets mal len representerar definitionen för ett fungerande robot transport företag som har distribuerats i uppfyllande struktur och som utför lämpliga lagrings-och hämtnings åtgärder. Om du klickar på mallen ser du att roboten skickar enhets data, till exempel temperatur, Axel position och egenskaper, t. ex. status för robot transport, osv. 
   * **Övervakning av struktur villkor**: den här enhets mal len representerar en enhets samling som gör att du kan övervaka miljö villkor och den gateway-enhet som är värd för olika Edge-arbetsbelastningar för att driva uppfyllande Center. Enheten skickar telemetridata som temperatur, antal plockningar, antal beställningar osv. Utöver status och hälsa för beräknings arbets belastningar som körs i din miljö. 

> [!div class="mx-imgBorder"]
> Mallar för ![Micro-uppfyllelse Center-enheter](./media/tutorial-micro-fulfillment-center-app-pnp/device-templates.png)

Om du klickar på fliken enhets grupper ser du också att dessa enhets mallar automatiskt har enhets grupper som skapats för dem.

## <a name="rules"></a>Regler
När du hoppar till fliken regler visas en exempel regel som finns i program mal len för att övervaka temperatur villkoren för robot transporten. Du kan använda den här regeln för att varna operatören om en speciell robot i anläggningen är överhettad och måste kopplas från för underhåll. 

Använd exempel regeln som inspiration för att definiera regler som passar dina affärs funktioner bättre.

   - **Robot transport är för varm**: den här regeln utlöses om det automatiska transport företaget når en temperatur tröskel under en viss tids period. 

> [!div class="mx-imgBorder"]
> regler för ![Micro-uppfyllelse Center](./media/tutorial-micro-fulfillment-center-app-pnp/rules.png)

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte kommer att fortsätta att använda det här programmet tar du bort program mal len genom att gå till **Administration** > **program inställningar** och klickar på **ta bort**.

> [!div class="mx-imgBorder"]
> ![rensning av program vara för Micro-uppfyllande Center](./media/tutorial-micro-fulfillment-center-app-pnp/delete.png)

## <a name="next-steps"></a>Nästa steg
* Läs mer om [lösnings arkitekturen för Micro-uppfyllelse Center](./architecture-micro-fulfillment-center-pnp.md)
* Läs mer om andra [IoT Central mallar för butik](./overview-iot-central-retail-pnp.md)
* Läs mer om IoT Central referera till [IoT Central översikt](../preview/overview-iot-central.md)