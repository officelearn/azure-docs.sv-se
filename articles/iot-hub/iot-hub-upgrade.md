---
title: Uppgradera Azure IoT Hub | Microsoft-dokument
description: Ändra pris- och skalnivå för IoT Hub för att få fler meddelande- och enhetshanteringsfunktioner.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: robinsh
ms.openlocfilehash: 96c3a7b2cfda23f173f4caeff4fb7a92b1ddc438
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "61440241"
---
# <a name="how-to-upgrade-your-iot-hub"></a>Uppgradera din IoT-hubb

När din IoT-lösning växer är Azure IoT Hub redo att hjälpa dig att skala upp. Azure IoT Hub erbjuder två nivåer, grundläggande (B) och standard (S), för att hantera kunder som vill använda olika funktioner. Inom varje nivå finns tre storlekar (1, 2 och 3) som avgör antalet meddelanden som kan skickas varje dag.

När du har fler enheter och behöver fler funktioner finns det tre sätt att justera din IoT-hubb efter dina behov:

* Lägg till enheter i IoT-hubben. Varje ytterligare enhet i en B1 IoT-hubb tillåter till exempel ytterligare 400 000 meddelanden per dag.

* Ändra storleken på IoT-hubben. Migrera till exempel från B1-nivån till B2-nivån för att öka antalet meddelanden som varje enhet kan stödja per dag.

* Uppgradera till en högre nivå. Uppgradera till exempel från B1-nivån till S1-nivån för åtkomst till avancerade funktioner med samma meddelandekapacitet.

Dessa ändringar kan alla inträffa utan att avbryta befintliga åtgärder.

Om du vill nedgradera IoT-hubben kan du ta bort enheter och minska storleken på IoT-hubben, men du kan inte nedgradera till en lägre nivå. Du kan till exempel flytta från S2-nivån till S1-nivån, men inte från S2-nivån till B1-nivån. Endast en typ av [Iot Hub-utgåva](https://azure.microsoft.com/pricing/details/iot-hub/) inom en nivå kan väljas per IoT Hub. Du kan till exempel skapa en IoT Hub med flera enheter S1, men inte med en blandning av enheter från olika utgåvor, till exempel S1 och B3, eller S1 och S2.

De här exemplen är avsedda att hjälpa dig att förstå hur du justerar din IoT-hubb när din lösning ändras. För specifik information om varje nivås funktioner bör du alltid läsa [Azure IoT Hub-priser](https://azure.microsoft.com/pricing/details/iot-hub/).

## <a name="upgrade-your-existing-iot-hub"></a>Uppgradera din befintliga IoT-hubb

1. Logga in på [Azure-portalen](https://portal.azure.com/) och navigera till din IoT-hubb.

2. Välj **Prissättning och skala**.

   ![Prissättning och skala](./media/iot-hub-upgrade/pricing-scale.png)

3. Om du vill ändra nivån för hubben väljer du **Pris- och skalnivå**. Välj den nya nivån och klicka sedan på **välj**.

   ![Pris- och skalnivå](./media/iot-hub-upgrade/select-tier.png)

4. Om du vill ändra antalet enheter i navet anger du ett nytt värde under **IoT Hub-enheter**.

5. Välj **Spara** för att spara ändringarna.

IoT-hubben har nu justerats och konfigurationerna är oförändrade.

Den maximala partitionsgränsen för grundläggande nivå IoT Hub och standardnivå IoT Hub är 32. De flesta IoT-hubbar behöver bara 4 partitioner. Partitionsgränsen väljs när IoT Hub skapas och relaterar meddelanden mellan enheter och moln till antalet samtidiga läsare av dessa meddelanden. Det här värdet förblir oförändrat när du migrerar från grundläggande nivå till standardnivå.

## <a name="next-steps"></a>Nästa steg

Få mer information om [Hur du väljer rätt IoT Hub-nivå](iot-hub-scaling.md).