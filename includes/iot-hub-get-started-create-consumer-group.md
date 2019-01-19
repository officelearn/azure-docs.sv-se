---
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.author: dobett
ms.openlocfilehash: 2d041ccbcf85f1931ca8ebc8b17595c9221c03cc
ms.sourcegitcommit: 82cdc26615829df3c57ee230d99eecfa1c4ba459
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2019
ms.locfileid: "54414650"
---
## <a name="add-a-consumer-group-to-your-iot-hub"></a>Lägg till en konsumentgrupp till din IoT hub

Konsumentgrupper används av program för att hämta data från Azure IoT Hub. I den här självstudien skapar du en konsumentgrupp som ska användas av en kommande Azure-tjänst för att läsa data från IoT hub.

Följ dessa steg för att lägga till en konsumentgrupp till din IoT hub:

1. Öppna IoT-hubben på [Azure-portalen](https://portal.azure.com/).

2. I den vänstra rutan klickar du på **inbyggda slutpunkter**väljer **händelser** i den övre panelen och ange ett namn under **konsumentgrupper** i den högra rutan. Klicka på **spara** när du har ändrat den **standard TTL** värde och returnera den tillbaka till det ursprungliga värdet.

   ![Skapa en konsumentgrupp i din IoT-hubb](./media/iot-hub-get-started-create-consumer-group/iot-hub-create-consumer-group-azure.png)
