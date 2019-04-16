---
title: Uppgradera med Azure IoT Hub | Microsoft Docs
description: Ändra priser och skalning nivå för IoT Hub för att få fler funktioner för hantering av meddelande- och enhetshantering.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: robinsh
ms.openlocfilehash: 96c3a7b2cfda23f173f4caeff4fb7a92b1ddc438
ms.sourcegitcommit: e89b9a75e3710559a9d2c705801c306c4e3de16c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2019
ms.locfileid: "59571285"
---
# <a name="how-to-upgrade-your-iot-hub"></a>Uppgradera din IoT-hubb

När din IoT-lösning växer kan är Azure IoT Hub klar kan du skala upp. Azure IoT Hub har två nivåer, (B), basic och standard (S), för att hantera kunder som vill använda olika funktioner. Inom varje nivå är tre storlekar (1, 2 och 3) som avgör hur många meddelanden som kan skickas varje dag.

När du har flera enheter, och du behöver fler funktioner, finns det tre sätt att justera din IoT-hubb så att den passar dina behov:

* Lägg till enheterna i IoT hub. Varje enhet i en B1 IoT-hubb kan till exempel en ytterligare 400 000 meddelanden per dag.

* Ändra storlek på IoT-hubben. Migrera till exempel från B1-nivå B2-nivån för att öka antalet meddelanden som har stöd för varje enhet per dag.

* Uppgradera till en högre nivå. Uppgradera till exempel från B1-nivå S1-nivån för åtkomst till avancerade funktioner med samma meddelanden kapacitet.

Dessa ändringar kan inträffa utan att avbryta befintliga åtgärder.

Om du vill nedgradera din IoT-hubb, kan du ta bort enheter och minska storleken på IoT-hubben men du kan inte Nedgradera till en lägre nivå. Du kan exempelvis flytta från på nivån S2 till S1-nivån, men inte från S2-nivån till B1-nivå. Endast en typ av [versionen av Iot Hub](https://azure.microsoft.com/pricing/details/iot-hub/) inom en nivå kan väljas per IoT Hub. Du kan till exempel skapa en IoT-hubb med flera enheter av S1, men inte med en blandning av enheter från olika versioner, till exempel S1 och B3, eller S1 och S2.

De här exemplen är avsedda att hjälpa dig att förstå hur du ändrar din IoT-hubb som dina ändringar i lösningen. Detaljerad information om varje nivå funktioner du bör alltid använda [priser för Azure IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/).

## <a name="upgrade-your-existing-iot-hub"></a>Uppgradera din befintliga IoT-hubb

1. Logga in på den [Azure-portalen](https://portal.azure.com/) och navigera till din IoT-hubb.

2. Välj **pris- och**.

   ![Priser och skalning](./media/iot-hub-upgrade/pricing-scale.png)

3. Om du vill ändra nivå för din hubb, Välj **pris- och skalnivåer**. Välj den nya nivån och klicka sedan på **Välj**.

   ![Pris- och skalnivå](./media/iot-hub-upgrade/select-tier.png)

4. Om du vill ändra antalet enheter i din hubb, ange ett nytt värde under **IoT Hub-enheter**.

5. Välj **spara** att spara dina ändringar.

IoT-hubben justeras nu och dina konfigurationer är oförändrade.

Maximal partitionsgränsen för IoT Hub på grundläggande nivån och standardnivån för IoT Hub är 32. De flesta IoT-hubbar behöver bara 4 partitioner. Partitionsgränsen väljs när IoT-hubben har skapats och avser antalet samtidiga läsare av dessa meddelanden på meddelanden från enheten till molnet. Det här värdet ändras inte när du migrerar från basic-nivån till standardnivån.

## <a name="next-steps"></a>Nästa steg

Läs mer om [välja rätt nivå för IoT Hub](iot-hub-scaling.md).