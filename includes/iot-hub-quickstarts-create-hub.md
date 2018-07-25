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
ms.openlocfilehash: 63acf0297a694ff442d56e67d52fd9b4e49f812d
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/13/2018
ms.locfileid: "39008594"
---
Det första steget är att använda Azure Portal till att skapa en IoT-hubb i din prenumeration. IoT-hubben gör att du kan mata in stora mängder telemetri i molnet från många enheter. Via hubben aktiveras sedan en eller flera servertjänster som körs i molnet, för läsning och bearbetning av telemetrin.

1. Logga in på [Azure-portalen](http://portal.azure.com).

1. Välj **Skapa en resurs** > **Sakernas internet** > **IoT Hub**.

    ![Välj för att installera IoT Hub][1]

1. I rutan **IoT-hubb** anger du följande information för IoT-hubben:

   * **Prenumeration**: Välj den prenumeration som du vill använda för att skapa IoT-hubben.
   * **Resursgrupp**: Skapa en resursgrupp som ska innehålla IoT-hubben eller använd en befintlig. Genom att lägga till alla relaterade resurser i en grupp, till exempel **TestResources**, kan du hantera dem allihop tillsammans. Till exempel tas alla resurser som ingår i gruppen bort om resursgruppen tas bort. Mer information finns i [Använda resursgrupper för att hantera Azure-resurser][lnk-resource-groups].
   * **Region**: Välj den plats som är närmast enheterna.
   * **Namn**: Skapa ett unikt namn för din IoT-hubb. Om namnet som du anger är tillgängligt visas en grön bockmarkering.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   ![Fönster med grundläggande IoT Hub-inställningar][2]

2. Välj **Nästa: Storlek och skalning** för att fortsätta att skapa IoT-hubben. 

3. Välj **pris- och skalningsnivå**. I den här artikeln väljer du **F1 – kostnadsfri nivå** om den nivån fortfarande är tillgänglig för din prenumeration. Mer information finns i avsnittet om [pris- och skalnivåer][lnk-pricing].

   ![Fönster för IoT Hub-storlek och IoT Hub-skalning][3]

4. Välj **Granska + skapa**.

1. Gå igenom informationen om IoT-hubben och klicka sedan på **Skapa**. Det kan ta några minuter innan IoT-hubben har skapats. Du kan övervaka förloppet i **meddelandefönstret**.


<!-- Images -->
[1]: ./media/iot-hub-create-hub/create-iot-hub1.png
[2]: ./media/iot-hub-create-hub/create-iot-hub2.png
[3]: ./media/iot-hub-create-hub/create-iot-hub3.png
<!-- Links -->
[lnk-portal]: https://portal.azure.com/
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-resource-groups]: ../articles/azure-resource-manager/resource-group-portal.md