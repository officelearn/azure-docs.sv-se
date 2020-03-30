---
author: robinsh
manager: philmea
ms.author: robinsh
ms.topic: include
ms.date: 05/20/2019
ms.openlocfilehash: c164433efc6a34a3a06676a3145feb18d3de80b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "66249049"
---
## <a name="add-a-consumer-group-to-your-iot-hub"></a>Lägga till en konsumentgrupp i din IoT-hubb

[Konsumentgrupper](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#event-consumers) tillhandahåller oberoende vyer i händelseströmmen som gör det möjligt för appar och Azure-tjänster att självständigt använda data från samma Event Hub-slutpunkt. I det här avsnittet lägger du till en konsumentgrupp i IoT-hubbens inbyggda slutpunkt som används senare i den här självstudien för att hämta data från slutpunkten.

Så här lägger du till en konsumentgrupp i IoT-hubben:

1. Öppna IoT-hubben på [Azure-portalen](https://portal.azure.com/).

2. I den vänstra rutan väljer du **Inbyggda slutpunkter,** väljer **Händelser** i den högra rutan och anger ett namn under **Konsumentgrupper**. Välj **Spara**.

   ![Skapa en konsumentgrupp i din IoT-hubb](./media/iot-hub-get-started-create-consumer-group/iot-hub-create-consumer-group-azure.png)
