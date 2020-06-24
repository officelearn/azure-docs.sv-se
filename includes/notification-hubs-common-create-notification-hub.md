---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 7c3887367b6365ea3577bbff6cc19bf34d178ee6
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2020
ms.locfileid: "85081553"
---
### <a name="create-a-notification-hub"></a>Skapa en meddelandehubb 

I det här avsnittet skapar du en Notification Hub och konfigurerar autentisering med **APNs**. Du kan använda ett P12-Push-certifikat eller tokenbaserad autentisering. Om du vill använda ett meddelande nav som du redan har skapat kan du gå vidare till steg 5.

1. Logga in på [Azure](https://portal.azure.com).

1. Klicka på **skapa en resurs**, Sök sedan efter och välj **Notification Hub**och klicka sedan på **skapa**.

1. Uppdatera följande fält och klicka sedan på **skapa**:

    **GRUNDLÄGGANDE INFORMATION**  

    **Prenumeration:** Välj mål **prenumerationen** i list rutan  
    **Resurs grupp:** Skapa en ny **resurs grupp** (eller Välj en befintlig)  

    **INFORMATION OM NAMNRYMD**  

    **Namn område för Notification Hub:** Ange ett globalt unikt namn för namn området för **Notification Hub**  

    > [!NOTE]
    > Se till att alternativet **Skapa nytt** är markerat för det här fältet.

    **INFORMATION OM NOTIFICATION HUB**  

    **Notification Hub:** Ange ett namn för **Notification Hub**  
    **Plats:** Välj en lämplig plats i list rutan  
    **Pris nivå:** Behåll standard alternativet **gratis**  

    > [!NOTE]
    > Om du inte har nått det maximala antalet hubbar på den kostnads fria nivån.

1. När **Notification Hub** har etablerats går du till den resursen.
1. Navigera till din nya **Notification Hub**.
1. Välj **åtkomst principer** från listan (under **Hantera**).
1. Anteckna **princip namn** värden tillsammans med motsvarande **anslutnings sträng** värden.
