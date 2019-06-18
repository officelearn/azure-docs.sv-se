---
author: robinsh
manager: philmea
ms.author: robinsh
ms.topic: include
ms.date: 05/20/2019
ms.openlocfilehash: c164433efc6a34a3a06676a3145feb18d3de80b9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66249049"
---
## <a name="add-a-consumer-group-to-your-iot-hub"></a>Lägg till en konsumentgrupp till din IoT hub

[Konsumentgrupper](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#event-consumers) ger oberoende vyer i händelseströmmen som gör att appar och Azure-tjänster oberoende använda data från samma Event Hub-slutpunkt. I det här avsnittet ska du lägga till en konsumentgrupp till din IoT-hubb inbyggd slutpunkt som används senare under den här självstudien att hämta data från slutpunkten.

Följ dessa steg för att lägga till en konsumentgrupp till din IoT hub:

1. Öppna IoT-hubben på [Azure-portalen](https://portal.azure.com/).

2. I det vänstra fönstret, Välj **inbyggda slutpunkter**väljer **händelser** i den högra rutan, och ange ett namn under **konsumentgrupper**. Välj **Spara**.

   ![Skapa en konsumentgrupp i din IoT-hubb](./media/iot-hub-get-started-create-consumer-group/iot-hub-create-consumer-group-azure.png)
