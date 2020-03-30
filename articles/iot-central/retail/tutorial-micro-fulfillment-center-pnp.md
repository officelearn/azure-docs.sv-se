---
title: Självstudiekurs för appmallen För mikrouppfyllelsecenter | Microsoft-dokument
description: En självstudiekurs om programmallen för mikrouppfyllelsecenter för Azure IoT Central
author: avneet723
ms.author: avneets
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 01/09/2020
ms.openlocfilehash: 93906f582f1edc351088f8b4c453bf9ebda54f83
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77369796"
---
# <a name="tutorial-deploy-and-walk-through-a-micro-fulfillment-center-application-template"></a>Självstudiekurs: Distribuera och gå igenom en programmall för mikrouppfyllelsecenter

I den här självstudien använder du programmallen Azure IoT Central micro-fulfillment Center för att skapa en återförsäljarlösning. Du lär dig mer om hur du distribuerar mallen, vad som ingår i den och vad du kanske vill göra härnäst.

## <a name="prerequisites"></a>Krav
För att slutföra den här självstudieserien behöver du en Azure-prenumeration. Du kan eventuellt använda en kostnadsfri 7-dagars testperiod. Om du inte har en Azure-prenumeration kan du skapa en på [sidan För Azure-registrering](https://aka.ms/createazuresubscription).

## <a name="create-an-application"></a>Skapa ett program 
I det här avsnittet skapar du ett nytt Azure IoT Central-program från en mall. Du kommer att använda det här programmet i hela självstudieserien för att skapa en komplett lösning.

Så här skapar du ett nytt Azure IoT Central-program:

1. Gå till [azure IoT Central application manager](https://aka.ms/iotcentral) webbplats.
1. Om du har en Azure-prenumeration loggar du in med de autentiseringsuppgifter du använder för att komma åt den. Annars loggar du in med ett Microsoft-konto:

   ![Skärmbild av dialogrutan Inloggning för Microsoft-konto](./media/tutorial-in-store-analytics-create-app-pnp/sign-in.png)

1. Börja skapa ett nytt Azure IoT Central-program genom att välja **Nytt program**.

1. Välj **Butik**.  På återförsäljarsidan visas flera butiksprogrammallar.

Så här skapar du ett nytt program för mikrouppfyllelsecenter som använder förhandsgranskningsfunktioner:  
1. Välj programmallen **för programmallen För mikrouppfyllelsecenter.** Den här mallen innehåller enhetsmallar för alla enheter som används i självstudien. Mallen innehåller också en operatörsinstrumentpanel för övervakningsvillkor inom ditt uppfyllelsecenter, samt villkoren för dina robotoperatörer. 

    ![Skärmbild av Azure IoT Central Bygg din IoT-programsida](./media/tutorial-micro-fulfillment-center-app-pnp/iotc-retail-homepage-mfc.png)
    
1. Du kan också välja ett eget **programnamn**. Programmallen är baserad på det fiktiva företaget Northwind Traders. 

    >[!NOTE]
    >Om du använder ett eget programnamn måste du fortfarande använda ett unikt värde för programmets URL.

1. Om du har en Azure-prenumeration anger du din katalog, Azure-prenumeration och region. Om du inte har en prenumeration kan du aktivera 7 dagars kostnadsfri utvärderingsversion och slutföra den erforderliga kontaktinformationen.  

    Mer information om kataloger och prenumerationer finns i snabbstarten [Skapa ett program.](../preview/quick-deploy-iot-central.md)

1. Välj **Skapa**.

    ![Skärmbild av sidan Azure IoT Central New-program](./media/tutorial-micro-fulfillment-center-app-pnp/iotc-retail-create-app-mfc.png)

## <a name="walk-through-the-application"></a>Gå igenom programmet 

När du har distribuerat appmallen visas **instrumentpanelen för Mikrouppfyllelsecenter för Northwind Traders**. Northwind Traders är en fiktiv återförsäljare som har ett mikro-uppfyllelsecenter som hanteras i det här Azure IoT Central-programmet. På den här operatörsinstrumentpanelen ser du information och telemetri om enheterna i den här mallen, tillsammans med en uppsättning kommandon, jobb och åtgärder som du kan vidta. Instrumentpanelen är logiskt uppdelad i två avsnitt. Till vänster kan du övervaka miljöförhållandena inom uppfyllelsestrukturen, och till höger kan du övervaka hälsan hos en robotbärare i anläggningen.  

Från instrumentpanelen kan du:
   * Se enhetstelemetri, till exempel antalet plockningar, antalet bearbetade order och egenskaper, till exempel struktursystemets status.  
   * Visa planlösningen och placeringen av robotbärarna inom uppfyllelsestrukturen.
   * Utlösarkommandon, till exempel återställa kontrollsystemet, uppdatera operatörens inbyggda programvara och konfigurera om nätverket.

     ![Skärmbild av instrumentpanelen i Northwind Traders mikrouppfyllelsecenter](./media/tutorial-micro-fulfillment-center-app-pnp/mfc-dashboard1.png)
   * Se ett exempel på instrumentpanelen som en operatör kan använda för att övervaka villkor inom uppfyllelsecentret. 
   * Övervaka hälsotillståndet för de nyttolaster som körs på gateway-enheten i uppfyllelsecentret.    

     ![Skärmbild av instrumentpanelen i Northwind Traders mikrouppfyllelsecenter](./media/tutorial-micro-fulfillment-center-app-pnp/mfc-dashboard2.png)

## <a name="device-template"></a>Enhetsmall
Om du väljer fliken enhetsmallar ser du att det finns två olika enhetstyper som ingår i mallen: 
   * **Robotic Carrier**: Den här enhetsmallen representerar definitionen för en fungerande robotbärare som har distribuerats i uppfyllelsestrukturen och utför lämpliga lagrings- och hämtningsåtgärder. Om du väljer mallen ser du att roboten skickar enhetsdata, till exempel temperatur- och axelposition, och egenskaper som robotbärarstatus. 
   * **Övervakning av strukturtillstånd:** Den här enhetsmallen representerar en enhetssamling som gör att du kan övervaka miljötillståndet, liksom gateway-enheten som är värd för olika kantarbetsbelastningar för att driva ditt uppfyllelsecenter. Enheten skickar telemetridata, till exempel temperaturen, antalet plockningar och antalet order. Den skickar också information om tillståndet och hälsotillståndet för beräkningsarbetsbelastningar som körs i din miljö. 

     ![Enhetsmallar för mikrouppfyllelsecenter](./media/tutorial-micro-fulfillment-center-app-pnp/device-templates.png)

Om du väljer fliken enhetsgrupper ser du också att dessa enhetsmallar automatiskt har enhetsgrupper skapade för dem.

## <a name="rules"></a>Regler
På fliken **Regler** visas en exempelregel som finns i programmallen för att övervaka temperaturförhållandena för robotoperatören. Du kan använda den här regeln för att varna operatören om en viss robot i anläggningen är överhettning och måste tas offline för service. 

Använd exempelregeln som inspiration för att definiera regler som är lämpligare för dina affärsfunktioner.

![Skärmbild av fliken Regler](./media/tutorial-micro-fulfillment-center-app-pnp/rules.png)

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte ska fortsätta att använda det här programmet tar du bort programmallen. Gå till Inställningar för **Delete** **Administrationsprogram** > **Application settings**och välj Ta bort .

![Skärmbild av sidan Programinställningar för Mikrouppfyllelsecenter](./media/tutorial-micro-fulfillment-center-app-pnp/delete.png)

## <a name="next-steps"></a>Nästa steg
* Läs mer om [lösningens arkitektur för mikrouppfyllelsecenter](./architecture-micro-fulfillment-center-pnp.md).
* Läs mer om andra [Azure IoT Central-återförsäljarmallar](./overview-iot-central-retail-pnp.md).
* Läs [översikten över Azure IoT Central](../preview/overview-iot-central.md).
