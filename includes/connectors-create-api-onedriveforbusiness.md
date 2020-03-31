---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/03/2016
ms.openlocfilehash: 3a0cb48e7fe5f3eef101f644e4f72fcfa2689d6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789466"
---
## <a name="prerequisites"></a>Krav

* Ett [OneDrive-konto](https://OneDrive.com) 

Innan du kan använda ditt OneDrive för företag-konto i en Logic-app måste du auktorisera Logic-appen för att ansluta till ditt OneDrive för företag-konto. Lyckligtvis kan du enkelt göra detta inifrån logikappen på Azure Portal. 

Här är stegen för att auktorisera din Logic-app för att ansluta till ditt OneDrive för företag-konto:

1. Om du vill skapa en anslutning till OneDrive för företag väljer du **Visa Microsoft-hanterade API:er** i listrutan Visa Microsoft i listrutan och anger sedan *OneDrive för företag* i sökrutan. Välj den utlösare eller åtgärd som du vill använda:  
   ![](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-1.png)
2. Om du inte har skapat några anslutningar till OneDrive för företag tidigare uppmanas du att ange dina OneDrive för företag-autentiseringsuppgifter. Dessa autentiseringsuppgifter används för att auktorisera din Logic-app att ansluta till och komma åt dina OneDrive för företag-kontos data:  
   ![](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-2.png)
3. Ange ditt användarnamn och lösenord för OneDrive för företag för att auktorisera din Logic-app:  
   ![](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-3.png)   
4. Observera att anslutningen har skapats och du är nu fri att fortsätta med de andra stegen i logikappen:  
   ![](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-4.png)   

