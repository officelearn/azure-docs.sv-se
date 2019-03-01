---
author: robinsh
manager: philmea
ms.author: robin.shahan
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 242f601ced4838a1b4e559774c25d05de04ddb77
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2019
ms.locfileid: "57016351"
---
## <a name="add-a-consumer-group-to-your-iot-hub"></a>Lägg till en konsumentgrupp till din IoT hub

Konsumentgrupper används av program för att hämta data från Azure IoT Hub. I den här självstudien skapar du en konsumentgrupp som ska användas av en kommande Azure-tjänst för att läsa data från IoT hub.

Följ dessa steg för att lägga till en konsumentgrupp till din IoT hub:

1. Öppna IoT-hubben på [Azure-portalen](https://portal.azure.com/).

2. I den vänstra rutan klickar du på **inbyggda slutpunkter**väljer **händelser** i den övre panelen och ange ett namn under **konsumentgrupper** i den högra rutan. Klicka på **spara** när du har ändrat den **standard TTL** värde och returnera den tillbaka till det ursprungliga värdet.

   ![Skapa en konsumentgrupp i din IoT-hubb](./media/iot-hub-get-started-create-consumer-group/iot-hub-create-consumer-group-azure.png)
