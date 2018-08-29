---
title: ta med fil
description: ta med fil
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 05/17/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 08afdcf91499fdb6f2da6e9ccc82313286f5c964
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2018
ms.locfileid: "43111212"
---
## <a name="create-an-iot-hub"></a>Skapa en IoT Hub
Skapa en IoT Hub som din simulerade enhetsapp kan ansluta till. Följande steg visar hur du kan slutföra den här åtgärden med hjälp av Azure Portal.

1. Logga in på [Azure-portalen](https://portal.azure.com/).

2. Välj **Skapa en resurs** > **Sakernas internet** > **IoT Hub**.
   
    ![Jumpbar i Azure Portal](./media/iot-hub-get-started-create-hub/create-iot-hub1.png)

3. I rutan **IoT-hubb** anger du följande information för IoT-hubben:

   * **Prenumeration**: Välj den prenumeration som du vill använda för att skapa IoT-hubben.

   * **Resursgrupp**: Skapa en resursgrupp som ska vara värd för IoT-hubben eller använd en befintlig. Mer information finns i [Använda resursgrupper för att hantera Azure-resurser](../articles/azure-resource-manager/resource-group-portal.md).

   * **Region**: Välj den plats som är närmast dig.

   * **Namn**: Skapa ett namn för din IoT Hub. Om namnet som du anger är tillgängligt visas en grön bockmarkering.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   ![Fönster med grundläggande IoT Hub-inställningar](./media/iot-hub-get-started-create-hub/create-iot-hub2.png)

4. Välj **Nästa: Storlek och skalning** för att fortsätta att skapa IoT-hubben. 

5. Välj **pris- och skalningsnivå**. I den här artikeln väljer du **F1 – kostnadsfri nivå** om den nivån fortfarande är tillgänglig för din prenumeration. Mer information finns i avsnittet om [pris- och skalnivåer](https://azure.microsoft.com/pricing/details/iot-hub/).

   ![Fönster för IoT Hub-storlek och IoT Hub-skalning](./media/iot-hub-get-started-create-hub/create-iot-hub3.png)

6. Välj **Granska + skapa**.

7. Gå igenom informationen om IoT-hubben och klicka sedan på **Skapa**. Det kan ta några minuter innan IoT-hubben har skapats. Du kan övervaka förloppet i **meddelandefönstret**.

8. När den nya IoT-hubben är klar klickar du på panelen för hubben på Azure Portal så öppnas hubbens egenskapsfönster. Nu när du har skapat en IoT-hubb letar du upp viktig information som du använder för att ansluta enheter och program till IoT-hubben. Klicka på **Principer för delad åtkomst**.
   
9. Välj principen **iothubowner** i **Principer för delad åtkomst**. Kopiera **Anslutningssträng – primärnyckel** för IoT Hub eftersom du behöver den senare. Mer information finns i avsnittet om [åtkomstkontroll](../articles/iot-hub/iot-hub-devguide-security.md) i ”utvecklarhandboken för IoT Hub”.
   
    ![Principer för delad åtkomst](./media/iot-hub-get-started-create-hub/create-iot-hub5.png)