---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/03/2016
ms.openlocfilehash: 951ab2300aa4ffed2c5f1039ff993cd7f6af543f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "74789687"
---
## <a name="prerequisites"></a>Krav

* Ett Azure-konto; Du kan skapa ett [kostnads fritt konto](https://azure.microsoft.com/free)
* Ett [OneDrive](https://www.microsoft.com/store/apps/onedrive/9wzdncrfj1p3) -konto 

Innan du kan använda ditt OneDrive-konto i en Logic-app ska du godkänna att Logic-appen ansluter till ditt OneDrive-konto.  Du kan enkelt göra detta i din Logic-app på Azure Portal. 

Auktorisera din Logic app för att ansluta till ditt OneDrive-konto med följande steg:

1. Skapa en Logic-app. I Logic Apps designer väljer du **Visa Microsoft-hanterade API: er** i list rutan och anger sedan "OneDrive" i sökrutan. Välj en av utlösarna eller åtgärderna:  
   ![](./media/connectors-create-api-onedrive/onedrive-1.png)
2. Om du inte redan har skapat några anslutningar till OneDrive uppmanas du att logga in med dina OneDrive-autentiseringsuppgifter:  
   ![](./media/connectors-create-api-onedrive/onedrive-2.png)
3. Välj **Logga**in och ange ditt användar namn och lösen ord. Välj **Logga**in:  
   ![](./media/connectors-create-api-onedrive/onedrive-3.png)   
   
    Dessa autentiseringsuppgifter används för att auktorisera din Logic app att ansluta till och komma åt data i ditt OneDrive-konto. 
4. Välj **Ja** för att godkänna att Logic-appen använder ditt OneDrive-konto:  
   ![](./media/connectors-create-api-onedrive/onedrive-4.png)   
5. Observera att anslutningen har skapats. Fortsätt nu med de andra stegen i din Logic app:  
   ![](./media/connectors-create-api-onedrive/onedrive-5.png)

