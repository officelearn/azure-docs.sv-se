---
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 11/03/2016
ms.author: estfan
ms.openlocfilehash: 53c683dacbb3b94e34bd8662743673c3a0490d94
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2018
ms.locfileid: "50093325"
---
#### <a name="prerequisites"></a>Förutsättningar
* Ett Azure-konto; Du kan skapa en [kostnadsfritt konto](https://azure.microsoft.com/free)
* En [Dynamics CRM Online](https://www.microsoft.com/en-us/dynamics/crm-free-trial-overview.aspx) konto 

Auktorisera logikappen som ska ansluta till ditt CRM Online-konto innan du använder ditt Dynamics-konto i en logikapp. Du kan göra detta enkelt i din logikapp på Azure portal. 

Auktorisera din logikapp för att ansluta till ditt CRM Online-konto med följande steg:

1. Skapa en logikapp. I Logic Apps-designern väljer **visa Microsoft hanterade API: er** i nedrullningsbara listan, och ange sedan ”dynamics” i sökrutan. Välj något av utlösare eller åtgärder:  
   ![](./media/connectors-create-api-crmonline/dynamics-triggers.png)
2. Om du inte har skapat några anslutningar till Dynamics, uppmanas du att logga in med dina Dynamics-autentiseringsuppgifter:  
   ![](./media/connectors-create-api-crmonline/dynamics-signin.png)
3. Välj **logga in**, och ange ditt användarnamn och lösenord. Välj **logga in**. 
   
    Dessa autentiseringsuppgifter används för att auktorisera din logikapp för att ansluta till och komma åt data i ditt Dynamics-konto. 
4. Observera att anslutningen har skapats. Nu kan fortsätta med andra steg i logikappen:  
   ![](./media/connectors-create-api-crmonline/dynamics-properties.png)

