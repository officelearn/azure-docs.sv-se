---
title: Manuell redundans för Azure IoT-hubb | Microsoft Docs
description: Visar hur du utför en manuell redundans för en Azure IoT-hubb
author: robinsh
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 07/11/2018
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: bd9cb76557c65832de5d249cdccdc36101edf646
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55821294"
---
# <a name="tutorial-perform-manual-failover-for-an-iot-hub-public-preview"></a>Självstudier: Utför en manuell redundans för en IoT-hubb (offentlig förhandsversion)

Manuell redundans är en funktion i IoT Hub-tjänsten som gör att kunder kan utföra [redundans](https://en.wikipedia.org/wiki/Failover) för hubbens åtgärder från en primär region till motsvarande geoparade Azure-region. Manuell redundans kan utföras i händelse av ett regionalt haveri eller ett längre tjänstavbrott. Du kan även utföra en planerad redundans för att testa din haveriberedskap, men vi rekommenderar att du använder en IoT-testhubb i stället för en som körs i produktion. Funktionen för manuell redundans erbjuds till kunder utan extra kostnad.

I den här självstudien utför du följande åtgärder:

> [!div class="checklist"]
> * Använd Azure-portalen för att skapa en IoT-hubb. 
> * Utför en redundans. 
> * Se att hubben körs på den sekundära platsen.
> * Utför en återställning efter fel för att återställa IoT-hubbens åtgärder till den primära platsen. 
> * Bekräfta att hubben körs korrekt på rätt plats.

## <a name="prerequisites"></a>Nödvändiga komponenter

- En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

1. Logga in på [Azure Portal](https://portal.azure.com). 

2. Klicka på **+ Skapa en resurs** och välj **Internet of Things** (Sakernas Internet) och sedan **IoT Hub**.

   ![Skärmbild som visar skapande av en IoT-hubb](./media/tutorial-manual-failover/create-hub-01.png)

3. Välj fliken **Basics** (Grunder). Fyll i följande fält.

    **Prenumeration**: Välj den Azure-prenumeration som du vill använda.

    **Resursgrupp**: Klicka på **Skapa ny** och ange **ManlFailRG** för resursgruppens namn.

    **Region**: Välj en region nära dig som är en del av förhandsversionen. I den här självstudien används `westus2`. Det går bara att utföra en redundans mellan geoparade Azure-regioner. Den region som geoparats med westus2 är WestCentralUS.
    
   > [!NOTE]
   > Manuell redundans är för närvarande i offentlig förhandsversion och är *inte* tillgängligt i följande Azure-regioner: USA, östra, USA, västra, Europa, norra, Europa, västra, Brasilien, södra och USA, södra centrala.

   **Namn på IoT-hubb**: Ange ett namn för IoT-hubben. Hubbnamnet måste vara globalt unikt. 

   ![Skärmbild som visar Basics-fönsterruta (Grunder) för skapande av en IoT-hubb](./media/tutorial-manual-failover/create-hub-02-basics.png)

   Klicka på **Granska + skapa**. (Den använder standardinställningarna för storlek och skala.) 

4. Granska informationen och klicka sedan på **Skapa** för att skapa IoT-hubben. 

   ![Skärmbild som visar det sista steget för skapande av en IoT-hubb](./media/tutorial-manual-failover/create-hub-03-create.png)

## <a name="perform-a-manual-failover"></a>Utför en manuell redundans

Observera att det finns en gräns på två redundanser och två återställningar efter fel per dag för en IoT-hubb.

1. Klicka på **Resursgrupper** och välj sedan resursgruppen **ManlFailRG**. Klicka på din hubb i listan över resurser. 

2. Under **Återhämtning** på IoT Hub-fönsterrutan klickar du på **Manual failover (preview)** (Manuell redundans (förhandsversion)). Observera att om din hubb inte har konfigurerats i en giltig region kommer alternativet för manuell redundans att inaktiveras.

   ![Skärmbild som visar fönsterrutan med egenskaper för IoT Hub](./media/tutorial-manual-failover/trigger-failover-01.png)

3. På fönsterrutan för manuell redundans ser du den **primära platsen för IoT Hub** och den **sekundära platsen för IoT Hub**. Den primära platsen är inledningsvis inställd på den plats du angav när du skapade IoT-hubben och anger alltid den plats där hubben för närvarande är aktiv. Den sekundära platsen är den standardmässiga [geoparade Azure-regionen](../best-practices-availability-paired-regions.md) som är parkopplad till den primära platsen. Du kan inte ändra platsvärdena. För den här självstudien är den primära platsen `westus2` och den sekundära platsen är `WestCentralUS`.

   ![Skärmbild som visar fönsterrutan för manuell redundans](./media/tutorial-manual-failover/trigger-failover-02.png)

3. Längst upp i fönsterrutan för manuell redundans klickar du på **Initiate failover** (Initiera redundans). Du ser fönsterrutan **Confirm manual failover** (Bekräfta manuell redundans). Fyll i namnet på din IoT-hubb för att bekräfta att det är den som du vill utföra redundans för. Sedan initierar du redundansen genom att klicka på **OK**.

   Den tid det tar att utföra manuell redundans är proportionell mot antalet enheter som är registrerade för din hubb. Om du till exempel har 100 000 enheter kan det ta 15 minuter, men om du har fem miljoner enheter kan det ta en timme eller längre.

4. I fönsterrutan **Confirm manual failover** (Bekräfta manuell redundans) fyller du i namnet på din IoT-hubb för att bekräfta att det är den som du vill utföra redundans för. Sedan initierar du redundansen genom att klicka på OK. 

   ![Skärmbild som visar fönsterrutan för manuell redundans](./media/tutorial-manual-failover/trigger-failover-03-confirm.png)

   Bär processen för manuell redundans körs finns det en banderoll i fönsterrutan Manual Failover (Manuell redundans) som visar att en manuell redundans pågår. 

   ![Skärmbild som visar att manuell redundans pågår](./media/tutorial-manual-failover/trigger-failover-04-in-progress.png)

   Om du stänger fönsterrutan IoT Hub och öppnar den igen genom att klicka på den i fönsterrutan Resource Group (Resursgrupp) ser du en banderoll som visar att hubben inte är aktiv. 

   ![Skärmbild som visar att IoT Hub är inaktiv](./media/tutorial-manual-failover/trigger-failover-05-hub-inactive.png)

   När det är klart vänds de primära och sekundära regionerna på sidan Manuell redundans, och hubben blir aktiv igen. I det här exemplet är den primära platsen nu `WestCentralUS` och den sekundära platsen är nu `westus2`. 

   ![Skärmbild som visar att redundansen är klar](./media/tutorial-manual-failover/trigger-failover-06-finished.png)

## <a name="perform-a-failback"></a>Utföra en återställning efter fel 

När du har utfört en manuell redundans kan du växla tillbaka hubbens åtgärder till den ursprungliga primära regionen – det här kallas en återställning efter fel. Om du precis har utfört en redundans måste du vänta ungefär en timme innan du kan begära en återställning efter fel. Om du försöker utföra återställningen på en kortare tid visas ett felmeddelande.

En återställning efter fel utförs precis som en manuell redundans. Det här är stegen: 

1. För att utföra en återställning efter fel går du tillbaka till fönsterrutan för IoT Hub för din IoT-hubb.

2. Under **Återhämtning** på IoT Hub-fönsterrutan klickar du på **Manual failover (preview)** (Manuell redundans (förhandsversion)). 

3. Längst upp i fönsterrutan för manuell redundans klickar du på **Initiate failover** (Initiera redundans). Du ser fönsterrutan **Confirm manual failover** (Bekräfta manuell redundans). 

4. I fönsterrutan **Confirm manual failover** (Bekräfta manuell redundans) fyller du i namnet på din IoT-hubb för att bekräfta att det är den som du vill utföra återställning efter fel för. Sedan initierar du återställning efter fel genom att klicka på OK. 

   ![Skärmbild på manuell begäran om återställning efter fel](./media/tutorial-manual-failover/trigger-failback-01-regions.png)

   Banderollerna visas enligt beskrivning i avsnittet utföra en redundans. När återställningen är klar visar den återigen `westus2` som den primära platsen och `WestCentralUS` som den sekundära platsen, enligt den ursprungliga inställningen.

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
[Hantera tillståndet för en IoT-enhet](tutorial-device-twins.md)
