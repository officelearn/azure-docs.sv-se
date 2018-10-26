---
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 11/03/2016
ms.author: estfan
ms.openlocfilehash: b216de0a5094066977467b2899567122d585fb7e
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2018
ms.locfileid: "50133796"
---
#### <a name="prerequisites"></a>Förutsättningar
* Ett Azure-konto; Du kan skapa en [kostnadsfritt konto](https://azure.microsoft.com/free)
* En [Office 365](https://office365.com) konto  

Auktorisera logikappen som ska ansluta till ditt Office 365-konto innan du använder Office 365-konto i en logikapp. Du kan göra detta enkelt i din logikapp på Azure portal.  

Auktorisera din logikapp för att ansluta till ditt Office 365-konto med följande steg:

1. Skapa en logikapp. I Logic Apps-designern väljer **visa Microsoft hanterade API: er** i nedrullningsbara listan, och ange sedan ”office 365” i sökrutan. Välj något av utlösare eller åtgärder:  
    ![Steg i Office 365-anslutning att skapa](./media/connectors-create-api-office365-outlook/office365-sendemail.png)  
2. Om du inte har skapat några anslutningar till Office 365, uppmanas du att logga in med dina autentiseringsuppgifter för Office 365:  
    ![Steg i Office 365-anslutning att skapa](./media/connectors-create-api-office365-outlook/office365-signin.png)  
3. Välj **logga in**, och ange ditt användarnamn och lösenord. Välj **logga in**:  
    ![Steg i Office 365-anslutning att skapa](./media/connectors-create-api-office365-outlook/office365-usernamepassword.png)
   
    Dessa autentiseringsuppgifter används för att auktorisera din logikapp för att ansluta till och få åtkomst till ditt Office 365-konto. 
4. Observera att anslutningen har skapats. Nu kan fortsätta med andra steg i logikappen:   
    ![Steg i Office 365-anslutning att skapa](./media/connectors-create-api-office365-outlook/office365-sendemailproperties.png)  

