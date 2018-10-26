---
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 11/03/2016
ms.author: estfan
ms.openlocfilehash: 7cfce34cb2d6002dba5ec570bf859ec47e894c65
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2018
ms.locfileid: "50133033"
---
#### <a name="prerequisites"></a>Förutsättningar
* Ett Azure-konto; Du kan skapa en [kostnadsfritt konto](https://azure.microsoft.com/free)
* En [OneDrive](https://www.microsoft.com/store/apps/onedrive/9wzdncrfj1p3) konto 

Innan du kan använda ditt OneDrive-konto i en logikapp, auktorisera att logic app för att ansluta till ditt OneDrive-konto.  Du kan göra detta enkelt i din logikapp på Azure portal. 

Auktorisera din logikapp för att ansluta till ditt OneDrive-konto med följande steg:

1. Skapa en logikapp. I Logic Apps-designern väljer **visa Microsoft hanterade API: er** i nedrullningsbara listan, och ange sedan ”onedrive” i sökrutan. Välj något av utlösare eller åtgärder:  
   ![](./media/connectors-create-api-onedrive/onedrive-1.png)
2. Om du inte har skapat några anslutningar till OneDrive, uppmanas du att logga in med dina autentiseringsuppgifter för OneDrive:  
   ![](./media/connectors-create-api-onedrive/onedrive-2.png)
3. Välj **logga in**, och ange ditt användarnamn och lösenord. Välj **logga in**:  
   ![](./media/connectors-create-api-onedrive/onedrive-3.png)   
   
    Dessa autentiseringsuppgifter används för att auktorisera din logikapp för att ansluta till och komma åt data i ditt OneDrive-konto. 
4. Välj **Ja** att auktorisera att logic app att använda ditt OneDrive-konto:  
   ![](./media/connectors-create-api-onedrive/onedrive-4.png)   
5. Observera att anslutningen har skapats. Nu kan fortsätta med andra steg i logikappen:  
   ![](./media/connectors-create-api-onedrive/onedrive-5.png)

