---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/03/2016
ms.openlocfilehash: c94e7d1fb5c42a0246b38c88eb097c75ec8ca4e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789843"
---
## <a name="prerequisites"></a>Krav

* Ett [Facebook-konto](https://www.facebook.com/) 

Innan du kan använda ditt Facebook-konto i en Logic-app måste du auktorisera Logic-appen för att ansluta till ditt Facebook-konto. Lyckligtvis kan du enkelt göra detta inifrån logikappen på Azure Portal. 

Här är stegen för att auktorisera din Logic-app för att ansluta till ditt Facebook-konto:

1. Om du vill skapa en anslutning till Facebook väljer du **Visa Hanterade API:er** för Microsoft i listrutan och anger sedan *Facebook* i sökrutan i logikappdesignern. Välj den utlösare eller åtgärd som du vill använda:  
   ![facebook steg 1](./media/connectors-create-api-facebook/facebook-1.png)
2. Om du inte har skapat några anslutningar till Facebook tidigare uppmanas du att ange dina Facebook-autentiseringsuppgifter. Dessa autentiseringsuppgifter används för att auktorisera din Logic-app att ansluta till och komma åt dina Facebook-kontos data:  
   ![facebook steg 2](./media/connectors-create-api-facebook/facebook-2.png)
3. Ange ditt Användarnamn och lösenord för Facebook för att auktorisera din Logic-app:  
   ![facebook steg 3](./media/connectors-create-api-facebook/facebook-3.png)   
4. Observera att anslutningen har skapats och du är nu fri att fortsätta med de andra stegen i logikappen:  
   ![facebook steg 4](./media/connectors-create-api-facebook/facebook-4.png)   

