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
ms.openlocfilehash: 9a29406b92f7d2e2ce8171974efb5a264e112d1d
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
1. Logga in på [Azure Portal][lnk-portal].
1. Välj **Skapa en resurs** > **Sakernas internet** > **IoT Hub**.
   
    ![Skärmbild av Azure portal navigering till IoT-hubb][1]

1. I rutan **IoT-hubb** anger du följande information för IoT-hubben:

   * **Prenumerationen**: Välj den prenumeration som du vill använda för att skapa den här IoT-hubben.

   * **Resursgrupp**: Skapa en resursgrupp som ska vara värd för IoT-hubben eller använd en befintlig. Mer information finns i [använda resursgrupper för att hantera dina Azure-resurser][lnk-resource-groups].

   * **Region**: Välj den närmaste platsen för dig.

   * **Namn**: Skapa ett namn för din IoT Hub. Om det namn som du anger är tillgänglig, visas en grön bock.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   ![IoT-hubb grunderna fönster][2]

2. Välj **nästa: storlek och skala** fortsätta skapa din IoT-hubb. 

3. Välj din **priser och skalnivå**. Den här artikeln, Välj den **F1 - ledigt** tjänstnivån om det fortfarande är tillgänglig på din prenumeration. Mer information finns i avsnittet om [pris- och skalnivåer][lnk-pricing].

   ![IoT-hubb storlek och skala fönster][3]

4. Välj **granska + skapa**.

1. Granska din IoT-hubb information och klicka sedan på **skapa**. Det kan ta några minuter innan IoT-hubben har skapats. Du kan övervaka förloppet i **meddelandefönstret**.
<!-- Images -->
[1]: ./media/iot-hub-create-hub/create-iot-hub1.png
[2]: ./media/iot-hub-create-hub/create-iot-hub2.png
[3]: ./media/iot-hub-create-hub/create-iot-hub3.png
<!-- Links -->
[lnk-portal]: https://portal.azure.com/
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-resource-groups]: ../articles/azure-resource-manager/resource-group-portal.md