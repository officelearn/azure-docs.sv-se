---
title: ta med fil
description: ta med fil
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 08/14/2018
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 983c65ba6e8b87f1dd66fcfdb50eac088ffab5d0
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2018
ms.locfileid: "49960177"
---
En av de viktigaste funktionerna i Azure IoT Edge är möjligheten att distribuera moduler till IoT Edge-enheter från molnet. En IoT Edge-modul är ett körbart paket som implementeras som en container. I det här avsnittet ska du distribuera en modul som genererar telemetri för din simulerade enhet.

1. Gå till din IoT-hubb på Azure Portal.
1. Gå till **IoT Edge** under **Automatisk enhetshantering** och välj din IoT Edge-enhet.
1. Välj **Ange moduler**.
1. I avsnittet **Distributionsmoduler** på steget **Lägg till moduler** klickar du på **Lägg till** och sedan **IoT Edge-modul**.
1. I **Namn**-fältet skriver du `tempSensor`.
1. I **URI för avbildning**-fältet skriver du `mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0`.
1. Lämna de andra inställningarna som de är och välj **Spara**.

   ![Spara IoT Edge-modulen när du har angett namn och bild-URI](./media/iot-edge-deploy-module/name-image.png)

1. I steget **Lägg till moduler** väljer du **Nästa**.
1. I steget **Ange rutter** ska du ha en standardväg som skickar alla meddelanden från alla moduler till IoT Hub. Om du inte har det lägger du till följande kod och väljer sedan **Nästa**.

   ```json
   {
       "routes": {
           "route": "FROM /messages/* INTO $upstream"
       }
   }
   ```

1. Välj **Skicka** i steget **Granska distribution**.
1. Återgå till informationssidan om enheten och välj **Uppdatera**. Förutom edgeAgent-modulen som skapades när du startade tjänsten för första gången ska du även se en annan körningsmodul med namnet **edgeHub** och modulen **tempSensor**.

   ![Visa tempSensor i listan med distribuerade moduler](./media/iot-edge-deploy-module/deployed-modules.png)
