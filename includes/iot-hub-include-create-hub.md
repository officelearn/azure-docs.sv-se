---
title: ta med fil
description: ta med fil
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 02/13/2020
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: bbdafde85097d0052edd5984b594fd37066dc1e6
ms.sourcegitcommit: 0eb0673e7dd9ca21525001a1cab6ad1c54f2e929
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2020
ms.locfileid: "77279735"
---
I det här avsnittet beskrivs hur du använder [Azure-portalen](https://portal.azure.com) för att skapa en IoT-hubb.

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. På Start sidan för Azure väljer du knappen **+ skapa en resurs** och anger sedan *IoT Hub* i fältet **Sök i Marketplace** .

1. Välj **IoT Hub** från Sök resultaten och välj sedan **skapa**.

1. På fliken **grundläggande** inställningar fyller du i fälten på följande sätt:

   - **Prenumeration**: Välj den prenumeration som ska användas för hubben.

   - **Resurs grupp**: Välj en resurs grupp eller skapa en ny. Om du vill skapa en ny väljer du **Skapa ny** och fyller i det namn som du vill använda. Om du vill använda en befintlig resurs grupp väljer du den resurs gruppen. Mer information finns i [Hantera Azure Resource Manager-resursgrupper](../articles/azure-resource-manager/management/manage-resource-groups-portal.md).

   - **Region**: Välj den region där du vill att hubben ska placeras. Välj den plats som är närmast dig.

   - **IoT Hub namn**: Ange ett namn för hubben. Det här namnet måste vara globalt unikt. Om namnet som du anger är tillgängligt visas en grön bockmarkering.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   ![Skapa ett nav i Azure Portal](./media/iot-hub-include-create-hub/iot-hub-create-screen-basics.png)

1. Välj **Nästa: storlek och skala** för att fortsätta skapa hubben.

   ![Ange storlek och skala för en ny hubb med hjälp av Azure Portal](./media/iot-hub-include-create-hub/iot-hub-create-screen-size-scale.png)

    På den här skärmen kan du ange följande värden:

    - **Pris-och skalnings nivå**: den valda nivån. Du kan välja mellan flera nivåer, beroende på hur många funktioner du vill och hur många meddelanden du skickar genom din lösning per dag. Den kostnadsfria nivån är avsedd för testning och utvärdering. Det gör att 500-enheter kan anslutas till hubben och upp till 8 000 meddelanden per dag. Varje Azure-prenumeration kan skapa en IoT-hubb på den kostnads fria nivån.

    - **IoT Hub-enheter**: Antalet meddelanden som tillåts per enhet per dag beror på hubbens prisnivå. Om du till exempel vill att hubben ska stödja ingångar av 700 000-meddelanden väljer du två enheter i S1-nivån.
    Mer information om de andra alternativen för nivån finns i avsnittet om att [välja rätt nivå för IoT Hub](../articles/iot-hub/iot-hub-scaling.md).

    - **Azure Security Center**: aktivera det här för att lägga till ett extra lager med hot skydd i IoT och dina enheter. Det här alternativet är inte tillgängligt för hubbar på den kostnads fria nivån. Mer information om den här funktionen finns [Azure Security Center for IoT](https://docs.microsoft.com/azure/asc-for-iot/).

    - **Avancerade inställningar** > **enhet-till-moln-partitioner**: den här egenskapen relaterar enhets-till-moln-meddelanden till antalet samtidiga läsare av meddelanden. De flesta hubbar behöver bara fyra partitioner.

1. I den här artikeln godkänner du standard alternativen och väljer sedan **Nästa: Taggar** för att gå vidare till nästa skärm.

    Taggar är namn/värde-par. Du kan tilldela samma tagg till flera resurser och resurs grupper för att kategorisera resurser och konsolidera fakturering.

   ![Ange storlek och skala för en ny hubb med hjälp av Azure Portal](./media/iot-hub-include-create-hub/iot-hub-create-tabs.png)

    Välj **Nästa: granska + skapa** för att granska dina val. Du ser något som liknar den här skärmen.

   ![Granska informationen för att skapa den nya hubben](./media/iot-hub-include-create-hub/iot-hub-create-review.png)

1. Välj **skapa** för att skapa din nya hubb. Det tar några minuter att skapa hubben.
