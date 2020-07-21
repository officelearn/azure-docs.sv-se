---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/03/2016
ms.openlocfilehash: 8b5f72f18ee5bf9c1e4dbaaa87d6705f55b09bc5
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2020
ms.locfileid: "86524223"
---
## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto; Du kan skapa ett [kostnads fritt konto](https://azure.microsoft.com/free)
* Ett [OneDrive](https://www.microsoft.com/store/apps/onedrive/9wzdncrfj1p3) -konto 

Innan du kan använda ditt OneDrive-konto i en Logic-app ska du godkänna att Logic-appen ansluter till ditt OneDrive-konto.  Du kan enkelt göra detta i din Logic-app på Azure Portal. 

Auktorisera din Logic app för att ansluta till ditt OneDrive-konto med följande steg:

1. Skapa en Logic-app. I Logic Apps designer väljer du **Visa Microsoft-hanterade API: er** i list rutan och anger sedan "OneDrive" i sökrutan. Välj en av utlösarna eller åtgärderna:  
   ![En dialog ruta med namnet "Visa Microsoft Managed API: er" har en sökruta som innehåller "OneDrive". Nedan visas en lista över fyra utlösare. Först i listan är "OneDrive-när en fil skapas", som är markerad.](./media/connectors-create-api-onedrive/onedrive-1.png)
2. Om du inte redan har skapat några anslutningar till OneDrive uppmanas du att logga in med dina OneDrive-autentiseringsuppgifter:  
   ![En dialog ruta med namnet "OneDrive-när en fil skapas" har en knapp med etiketten "logga in".](./media/connectors-create-api-onedrive/onedrive-2.png)
3. Välj **Logga**in och ange ditt användar namn och lösen ord. Välj **Logga**in:  
   ![En dialog ruta med namnet "logga in" anger att du ska använda Microsoft-konto. Den har två text rutor med etiketten "e-post eller telefon" och "lösen ord" den innehåller också en kryss ruta med namnet "Håll mig inloggad" och en knapp med namnet "logga in".](./media/connectors-create-api-onedrive/onedrive-3.png)   
   
    Dessa autentiseringsuppgifter används för att auktorisera din Logic app att ansluta till och komma åt data i ditt OneDrive-konto. 
4. Välj **Ja** för att godkänna att Logic-appen använder ditt OneDrive-konto:  
   ![En dialog ruta med namnet "ge den här appen åtkomst till din information?" ber om behörighet att göra följande fyra saker: 1) "logga in automatiskt", 2) "åtkomst till dina e-postadresser", 3) "få åtkomst till din information när som helst och 4)" komma åt OneDrive-filer ". Det finns en "Ja"-knapp för att ge behörighet och knappen "nej" för att neka den. Det finns en länk för att ändra dessa program behörigheter.](./media/connectors-create-api-onedrive/onedrive-4.png)   
5. Observera att anslutningen har skapats. Fortsätt nu med de andra stegen i din Logic app:  
   ![En dialog ruta med namnet "när en fil skapas" har en text ruta med namnet "mapp" med en associerad Browse-knapp.](./media/connectors-create-api-onedrive/onedrive-5.png)

