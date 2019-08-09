---
title: ta med fil
description: ta med fil
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 08/07/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 795e805e4629a4f4674c296e555ece6230cf66f1
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883115"
---
I det här avsnittet beskrivs hur du använder [Azure-portalen](https://portal.azure.com) för att skapa en IoT-hubb.

1. Logga in på [Azure Portal](https://portal.azure.com).

1. Välj **skapa en resurs**och ange sedan *IoT Hub* i fältet **Sök i Marketplace** .

1. Välj **IoT Hub** från Sök resultaten och välj sedan **skapa**.

1. På fliken **grundläggande** inställningar fyller du i fälten på följande sätt:

   - **Prenumeration**: Välj den prenumeration som ska användas för hubben.

   - **Resursgrupp**: Välj en resurs grupp eller skapa en ny. Om du vill skapa en ny väljer du **Skapa ny** och fyller i det namn som du vill använda. Om du vill använda en befintlig resurs grupp väljer du den resurs gruppen. Mer information finns i [Hantera Azure Resource Manager-resursgrupper](../articles/azure-resource-manager/manage-resource-groups-portal.md).

   - **Region**: Välj den region där du vill att hubben ska placeras. Välj den plats som är närmast dig.

   - **Namn på IoT-hubb**: Ange ett namn för hubben. Det här namnet måste vara globalt unikt. Om namnet som du anger är tillgängligt visas en grön bockmarkering.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   ![Skapa ett nav i Azure Portal](./media/iot-hub-include-create-hub/iot-hub-create-screen-basics-vs2019.png)

1. Välj **Nästa: Storlek och skala** för att fortsätta skapa hubben.

   ![Ange storlek och skala för en ny hubb med hjälp av Azure Portal](./media/iot-hub-include-create-hub/iot-hub-create-screen-size-scale.png)

    På den här skärmen kan du ange följande värden:

    - **Pris- och skalningsnivå**: Den valda nivån. Du kan välja bland flera nivåer beroende på hur många funktioner du vill ha och hur många meddelanden du skickar via din lösning per dag. Den kostnadsfria nivån är avsedd för testning och utvärdering. Det gör att 500-enheter kan anslutas till hubben och upp till 8 000 meddelanden per dag. Varje Azure-prenumeration kan skapa en IoT-hubb på den kostnadsfria nivån.

    - **IoT Hub-enheter**: Det antal meddelanden som tillåts per enhet per dag beror på hubbens prisnivå. Om du till exempel vill att hubben ska stödja ingångar av 700 000-meddelanden väljer du två enheter i S1-nivån.
    Mer information om de andra alternativen för nivån finns i avsnittet om att [välja rätt nivå för IoT Hub](../articles/iot-hub/iot-hub-scaling.md).

    - Avancerade > **partitioner från enhet till moln**: Den här egenskapen avser antalet meddelanden från enhet till moln för samtidiga läsare av meddelandena. De flesta hubbar behöver bara fyra partitioner.

1. I den här självstudien accepterar du standard alternativen och väljer sedan **Granska + skapa** för att granska dina val. Du ser något som liknar den här skärmen.

   ![Granska informationen för att skapa den nya hubben](./media/iot-hub-include-create-hub/iot-hub-create-review-vs2019.png)

1. Välj **skapa** för att skapa din nya hubb. Det tar några minuter att skapa hubben.
