---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/03/2016
ms.openlocfilehash: 951ab2300aa4ffed2c5f1039ff993cd7f6af543f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789687"
---
## <a name="prerequisites"></a>Krav

* Ett Azure-konto; du kan skapa ett [gratis konto](https://azure.microsoft.com/free)
* Ett [OneDrive-konto](https://www.microsoft.com/store/apps/onedrive/9wzdncrfj1p3) 

Innan du kan använda ditt OneDrive-konto i en logikapp godkänner du att logikappen ansluter till ditt OneDrive-konto.  Du kan enkelt göra detta i logikappen på Azure-portalen. 

Auktorisera logikappen för att ansluta till ditt OneDrive-konto med följande steg:

1. Skapa en logikapp. I Logic Apps-designern väljer du **Visa Microsoft-hanterade API:er** i listrutan och anger sedan "onedrive" i sökrutan. Välj en av utlösarna eller åtgärderna:  
   ![](./media/connectors-create-api-onedrive/onedrive-1.png)
2. Om du inte tidigare har skapat några anslutningar till OneDrive uppmanas du att logga in med dina OneDrive-autentiseringsuppgifter:  
   ![](./media/connectors-create-api-onedrive/onedrive-2.png)
3. Välj **Logga in**och ange ditt användarnamn och lösenord. Välj **Logga in:**  
   ![](./media/connectors-create-api-onedrive/onedrive-3.png)   
   
    Dessa autentiseringsuppgifter används för att auktorisera din logikapp att ansluta till och komma åt data i ditt OneDrive-konto. 
4. Välj **Ja** om du vill auktorisera logikappen för att använda ditt OneDrive-konto:  
   ![](./media/connectors-create-api-onedrive/onedrive-4.png)   
5. Observera att anslutningen har skapats. Fortsätt nu med de andra stegen i logikappen:  
   ![](./media/connectors-create-api-onedrive/onedrive-5.png)

