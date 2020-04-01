---
title: Manuell redundans för Azure IoT-hubb | Microsoft Docs
description: Lär dig hur du utför en manuell redundans av din IoT-hubb till en annan region och bekräftar att den fungerar och sedan returnera den till den ursprungliga regionen och kontrollera den igen.
author: robinsh
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 07/24/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: bf37f7b27e3f8923a229cc0617365d912d47aec2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77110658"
---
# <a name="tutorial-perform-manual-failover-for-an-iot-hub"></a>Självstudiekurs: Utför manuell redundans för en IoT-hubb

Manuell redundans är en funktion i IoT Hub-tjänsten som gör att kunder kan utföra [redundans](https://en.wikipedia.org/wiki/Failover) för hubbens åtgärder från en primär region till motsvarande geoparade Azure-region. Manuell redundans kan utföras i händelse av ett regionalt haveri eller ett längre tjänstavbrott. Du kan även utföra en planerad redundans för att testa din haveriberedskap, men vi rekommenderar att du använder en IoT-testhubb i stället för en som körs i produktion. Funktionen för manuell redundans erbjuds till kunder utan extra kostnad.

I den här självstudien utför du följande åtgärder:

> [!div class="checklist"]
> * Använd Azure-portalen för att skapa en IoT-hubb. 
> * Utför en redundans. 
> * Se att hubben körs på den sekundära platsen.
> * Utför en återställning efter fel för att återställa IoT-hubbens åtgärder till den primära platsen. 
> * Bekräfta att hubben körs korrekt på rätt plats.

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

* Kontrollera att port 8883 är öppen i brandväggen. Enhetsexemplet i den här självstudien använder MQTT-protokollet, som kommunicerar över port 8883. Den här porten kan vara blockerad i vissa företags- och utbildningsnätverksmiljöer. Mer information och sätt att lösa problemet finns i [Ansluta till IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

1. Logga in på [Azure Portal](https://portal.azure.com). 

2. Klicka på **+ Skapa en resurs** och välj **Internet of Things** (Sakernas Internet) och sedan **IoT Hub**.

   ![Skärmbild som visar skapande av en IoT-hubb](./media/tutorial-manual-failover/create-hub-01.png)

3. Välj fliken **Grunderna.** Fyll i följande fält.

    **Prenumeration**: Välj den Azure-prenumeration som du vill använda.

    **Resursgrupp**: Klicka på **Skapa ny** och ange **ManlFailRG** för resursgruppens namn.

    **Region**: välj en region nära dig. I den här självstudien används `West US 2`. Det går bara att utföra en redundans mellan geoparade Azure-regioner. Regionen geo-paras ihop med Västra US 2 är WestCentralUS.
    
   **Namn på IoT-hubb**: Ange ett namn för IoT-hubben. Hubbnamnet måste vara globalt unikt. 

   ![Skärmbild som visar Basics-fönsterruta (Grunder) för skapande av en IoT-hubb](./media/tutorial-manual-failover/create-hub-02-basics.png)

   Klicka på **Granska + skapa**. (Den använder standardinställningarna för storlek och skala.) 

4. Granska informationen och klicka sedan på **Skapa** för att skapa IoT-hubben. 

   ![Skärmbild som visar det sista steget för skapande av en IoT-hubb](./media/tutorial-manual-failover/create-hub-03-create.png)

## <a name="perform-a-manual-failover"></a>Utför en manuell redundans

Observera att det finns en gräns på två redundanser och två återställningar efter fel per dag för en IoT-hubb.

1. Klicka på **Resursgrupper** och välj sedan resursgruppen **ManlFailRG**. Klicka på din hubb i listan över resurser. 

1. Klicka på **Redundans**under **Inställningar** i fönstret IoT Hub .

   ![Skärmbild som visar fönsterrutan med egenskaper för IoT Hub](./media/tutorial-manual-failover/trigger-failover-01.png)

1. I fönstret Manuell redundans visas den **aktuella platsen** och **redundansplatsen**. Den aktuella platsen anger alltid den plats där navet är aktivt. Redundansplatsen är den [standardbaserade Azure-geoparerade regionen](../best-practices-availability-paired-regions.md) som är kopplad till den aktuella platsen. Du kan inte ändra platsvärdena. För den här självstudien är `West US 2` den `West Central US`aktuella platsen och redundansplatsen är .

   ![Skärmbild som visar fönsterrutan för manuell redundans](./media/tutorial-manual-failover/trigger-failover-02.png)

1. Klicka på Starta redundans högst upp i fönstret Manuell **redundans.** 

1. I bekräftelsefönstret fyller du i namnet på din IoT-hubb för att bekräfta att det är den du vill redundans. Klicka sedan på **Redundans**om du vill starta redundansen.

   Den tid det tar att utföra manuell redundans är proportionell mot antalet enheter som är registrerade för din hubb. Om du till exempel har 100 000 enheter kan det ta 15 minuter, men om du har fem miljoner enheter kan det ta en timme eller längre.

   ![Skärmbild som visar fönsterrutan för manuell redundans](./media/tutorial-manual-failover/trigger-failover-03-confirm.png)

   Medan den manuella redundansprocessen körs visas en banderoll som talar om för dig att en manuell redundans pågår. 

   ![Skärmbild som visar att manuell redundans pågår](./media/tutorial-manual-failover/trigger-failover-04-in-progress.png)

   Om du stänger fönstret IoT Hub och öppnar det igen genom att klicka på den i fönstret Resursgrupp visas en banderoll som talar om att hubben är mitt i en manuell redundans. 

   ![Skärmbild som visar pågående IoT Hub-redundans](./media/tutorial-manual-failover/trigger-failover-05-hub-inactive.png)

   När den är klar vänds de aktuella regionerna och redundansregionerna på sidan Manuell redundans och navet är aktivt igen. I det här exemplet är `WestCentralUS` den aktuella platsen `West US 2`nu och redundansplatsen är nu . 

   ![Skärmbild som visar att redundansen är klar](./media/tutorial-manual-failover/trigger-failover-06-finished.png)

   Översiktssidan visar också en banderoll som anger att redundansen `West Central US`är klar och IoT Hub körs i .

   ![Skärmbild som visar redundans är klar på översiktssidan](./media/tutorial-manual-failover/trigger-failover-06-finished-overview.png)


## <a name="perform-a-failback"></a>Utföra en återställning efter fel 

När du har utfört en manuell redundans kan du växla tillbaka hubbens åtgärder till den ursprungliga primära regionen – det här kallas en återställning efter fel. Om du precis har utfört en redundans måste du vänta ungefär en timme innan du kan begära en återställning efter fel. Om du försöker utföra återställningen på en kortare tid visas ett felmeddelande.

En återställning efter fel utförs precis som en manuell redundans. Det här är stegen: 

1. För att utföra en återställning efter fel går du tillbaka till fönsterrutan för IoT Hub för din IoT-hubb.

2. Klicka på **Redundans**under **Inställningar** i fönstret IoT Hub . 

3. Klicka på Starta redundans högst upp i fönstret Manuell **redundans.** 

4. I bekräftelsefönstret fyller du i namnet på din IoT-hubb för att bekräfta att det är den du vill återställa. Sedan initierar du återställning efter fel genom att klicka på OK. 

   ![Skärmbild på manuell begäran om återställning efter fel](./media/tutorial-manual-failover/trigger-failover-03-confirm.png)

   Banderollerna visas enligt beskrivning i avsnittet utföra en redundans. När återställningen av återställningen `West US 2` är klar visas `West Central US` den igen som aktuell plats och som redundansplats, som ursprungligen angavs.

## <a name="clean-up-resources"></a>Rensa resurser 

Om du vill ta bort de resurser du skapade för den här självstudien tar du bort hela resursgruppen. Detta tar även bort alla resurser som ingår i gruppen. I det här fallet tas IoT-hubben och själva resursgruppen bort. 

1. Klicka på **Resursgrupper**. 

2. Leta upp och välj resursgruppen **ManlFailRG**. Klicka på det för att öppna det. 

3. Klicka på **Ta bort resursgrupp**. När du uppmanas anger du namnet på resursgruppen och klickar på **Ta bort** för att bekräfta. 

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du konfigurerar och utför en manuell redundans samt hur du begär en återställning efter fel genom att utföra följande uppgifter:

> [!div class="checklist"]
> * Använd Azure-portalen för att skapa en IoT-hubb. 
> * Utför en redundans. 
> * Se att hubben körs på den sekundära platsen.
> * Utför en återställning efter fel för att återställa IoT-hubbens åtgärder till den primära platsen. 
> * Bekräfta att hubben körs korrekt på rätt plats.

Gå vidare till nästa självstudie där du får lära dig hur du hanterar tillstånd för en IoT-enhet. 

> [!div class="nextstepaction"]
> [Hantera tillståndet för en IoT-enhet](tutorial-device-twins.md)
