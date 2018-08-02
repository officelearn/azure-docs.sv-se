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
ms.openlocfilehash: fce9e42e24be7f8678292a5d98a683ca4e579cd2
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2018
ms.locfileid: "39357018"
---
1. Logga in på [Azure-portalen](https://portal.azure.com/).

2. Välj **Skapa en resurs** > **Sakernas internet** > **IoT Hub**.
   
    ![Skärmbild av Azure-portalnavigering till IoT Hub](./media/iot-hub-create-hub/create-iot-hub1.png)

3. I rutan **IoT-hubb** anger du följande information för IoT-hubben:

   * **Prenumeration**: Välj den prenumeration som du vill använda för att skapa IoT-hubben.

   * **Resursgrupp**: Skapa en resursgrupp som ska vara värd för IoT-hubben eller använd en befintlig. Mer information finns i [Använda resursgrupper för att hantera Azure-resurser](../articles/azure-resource-manager/resource-group-portal.md).

   * **Region**: Välj den plats som är närmast dig.

   * **Namn**: Skapa ett namn för din IoT Hub. Om namnet som du anger är tillgängligt visas en grön bockmarkering.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   ![Fönster med grundläggande IoT Hub-inställningar](./media/iot-hub-create-hub/create-iot-hub2.png)

4. Välj **Nästa: Storlek och skalning** för att fortsätta att skapa IoT-hubben. 

5. Välj **pris- och skalningsnivå**. I den här artikeln väljer du **F1 – kostnadsfri nivå** om den nivån fortfarande är tillgänglig för din prenumeration. Mer information finns i avsnittet om [pris- och skalnivåer](https://azure.microsoft.com/pricing/details/iot-hub/).

   ![Fönster för IoT Hub-storlek och IoT Hub-skalning](./media/iot-hub-create-hub/create-iot-hub3.png)

6. Välj **Granska + skapa**.

7. Gå igenom informationen om IoT-hubben och klicka sedan på **Skapa**. Det kan ta några minuter innan IoT-hubben har skapats. Du kan övervaka förloppet i **meddelandefönstret**.