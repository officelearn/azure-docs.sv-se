---
title: Uppgradera med Azure IoT Hub | Microsoft Docs
description: Ändra priser och skalning nivå för IoT Hub för att få fler funktioner för hantering av meddelande- och enhetshantering.
author: kgremban
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/02/2018
ms.author: kgremban
ms.openlocfilehash: e1342ed574d84ed5b4edd5060c2d6d3ec8bca1a8
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2018
ms.locfileid: "40003119"
---
# <a name="how-to-upgrade-your-iot-hub"></a>Uppgradera din IoT-hubb

När din IoT-lösning växer kan är Azure IoT Hub klar kan du skala upp. Azure IoT Hub har två nivåer, (B), basic och standard (S), för att hantera kunder som vill använda olika funktioner. Inom varje nivå är tre storlekar (1, 2 och 3) som avgör hur många meddelanden som kan skickas varje dag. 

När du har flera enheter, och du behöver fler funktioner, finns det tre sätt att justera din IoT-hubb så att den passar dina behov:

* Lägg till enheterna i IoT hub. Varje enhet i en B1 IoT-hubb kan till exempel en ytterligare 400 000 meddelanden per dag. 
* Ändra storlek på IoT-hubben. Migrera till exempel från B1-nivån till B2-nivån för att öka mängden meddelanden som har stöd för varje enhet per dag.
* Uppgradera till en högre nivå. Uppgradera till exempel från B1-nivå S1-nivån för samma meddelanden kapaciteten men med de avancerade funktionerna som ingår i standard-nivån.

Dessa ändringar kan inträffa utan att avbryta befintliga åtgärder.

Om du vill nedgradera din IoT-hubb, kan du ta bort enheter och minska storleken på IoT-hubben. Men kan inte du Nedgradera till en lägre nivå. Du kan exempelvis flytta från på nivån S2 till S1-nivån, men inte från S2-nivån till B1-nivå. Tänk också på den enda typen av [edition](https://azure.microsoft.com/pricing/details/iot-hub/) inom en nivå kan väljas per IoT Hub. Du kan till exempel skapa en IoT-hubb med flera enheter av S1, men inte med en blandning av enheter från olika versioner, till exempel S1 och B3, eller S1 och S2.

De här exemplen är avsedda att hjälpa dig att förstå hur du ändrar din IoT-hubb som dina ändringar i lösningen. Detaljerad information om varje nivå funktioner du bör alltid använda [priser för Azure IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/). 

## <a name="upgrade-your-existing-iot-hub"></a>Uppgradera din befintliga IoT-hubb 

1. Logga in på den [Azure-portalen](https://portal.azure.com/) och navigera till din IoT-hubb. 
2. Välj **pris- och**. 

   ![Priser och skalning](./media/iot-hub-upgrade/pricing-scale.png)

3. Om du vill ändra nivå för din hubb, Välj **pris- och skalnivåer**. Välj den nya nivån och klicka sedan på **Välj**.

   ![Priser och skalning](./media/iot-hub-upgrade/select-tier.png)

4. Om du vill ändra antalet enheter i din hubb, ange ett nytt värde under **IoT Hub-enheter**. 
5. Välj **spara** att spara dina ändringar. 

IoT-hubben justeras nu och dina konfigurationer är oförändrade. Observera att den maximala partitionsgränsen för basic-nivån IoT Hub är 8 och standard-nivån är 32. De flesta IoT-hubbar behöver bara 4 partitioner. Partitionsgränsen väljs när IoT-hubben har skapats och avser antalet samtidiga läsare av dessa meddelanden på meddelanden från enheten till molnet. Det här värdet ändras inte när du migrerar från basic-nivån till standardnivån. 

## <a name="next-steps"></a>Nästa steg

Läs mer om [välja rätt nivå för IoT Hub](iot-hub-scaling.md). 

