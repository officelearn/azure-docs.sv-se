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
ms.openlocfilehash: d586ca18953b12045fbbaa4a656d78a7192eb88e
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/06/2018
ms.locfileid: "34371243"
---
## <a name="create-an-iot-hub"></a>Skapa en IoT Hub
Skapa en IoT Hub som din simulerade enhetsapp kan ansluta till. Följande steg visar hur du kan slutföra den här åtgärden med hjälp av Azure Portal.

1. Logga in på [Azure Portal][lnk-portal].

1. Välj **Skapa en resurs** > **Sakernas internet** > **IoT Hub**.
   
    ![Jumpbar i Azure Portal][1]

1. I rutan **IoT-hubb** anger du följande information för IoT-hubben:

   * **Prenumeration**: Välj den prenumeration som du vill använda för att skapa IoT-hubben.

   * **Resursgrupp**: Skapa en resursgrupp som ska vara värd för IoT-hubben eller använd en befintlig. Mer information finns i [Använda resursgrupper för att hantera Azure-resurser][lnk-resource-groups].

   * **Region**: Välj den plats som är närmast dig.

   * **Namn**: Skapa ett namn för din IoT Hub. Om namnet som du anger är tillgängligt visas en grön bockmarkering.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   ![Fönster med grundläggande IoT Hub-inställningar][2]

2. Välj **Nästa: Storlek och skalning** för att fortsätta att skapa IoT-hubben. 

3. Välj **pris- och skalningsnivå**. I den här artikeln väljer du **F1 – kostnadsfri nivå** om den nivån fortfarande är tillgänglig för din prenumeration. Mer information finns i avsnittet om [pris- och skalnivåer][lnk-pricing].

   ![Fönster för IoT Hub-storlek och IoT Hub-skalning][3]

4. Välj **Granska + skapa**.

1. Gå igenom informationen om IoT-hubben och klicka sedan på **Skapa**. Det kan ta några minuter innan IoT-hubben har skapats. Du kan övervaka förloppet i **meddelandefönstret**.

1. När den nya IoT-hubben är klar klickar du på panelen för hubben på Azure Portal så öppnas hubbens egenskapsfönster. Nu när du har skapat en IoT-hubb letar du upp viktig information som du använder för att ansluta enheter och program till IoT-hubben. Klicka på **Principer för delad åtkomst**.
   
1. Välj principen **iothubowner** i **Principer för delad åtkomst**. Kopiera **Anslutningssträng – primärnyckel** för IoT Hub eftersom du behöver den senare. Mer information finns i avsnittet om [åtkomstkontroll][lnk-access-control] i ”utvecklarhandboken för IoT Hub”.
   
    ![Principer för delad åtkomst][5]

<!-- Images. -->
[1]: ./media/iot-hub-get-started-create-hub/create-iot-hub1.png
[2]: ./media/iot-hub-get-started-create-hub/create-iot-hub2.png
[3]: ./media/iot-hub-get-started-create-hub/create-iot-hub3.png
[4]: ./media/iot-hub-get-started-create-hub/create-iot-hub4.png
[5]: ./media/iot-hub-get-started-create-hub/create-iot-hub5.png

<!-- Links -->
[lnk-access-control]: ../articles/iot-hub/iot-hub-devguide-security.md
[lnk-portal]: https://portal.azure.com/
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-resource-groups]: ../articles/azure-resource-manager/resource-group-portal.md
