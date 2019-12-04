---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/03/2016
ms.openlocfilehash: 635a69fc8e75e3a6249728dc1df31cf839b345c6
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789599"
---
## <a name="prerequisites"></a>Krav

* Ett Azure-konto; Du kan skapa ett [kostnads fritt konto](https://azure.microsoft.com/free)
* Ett [Office 365](https://office365.com) -konto  

Innan du använder ditt Office 365-konto i en Logic-app ska du godkänna att Logic-appen ansluter till ditt Office 365-konto. Du kan enkelt göra detta i din Logic-app på Azure Portal.  

Auktorisera din Logic app för att ansluta till ditt Office 365-konto med följande steg:

1. Skapa en Logic-app. I Logic Apps designer väljer du **Visa Microsoft-hanterade API: er** i list rutan och anger sedan "Office 365" i sökrutan. Välj en av utlösarna eller åtgärderna:  
    steg för att skapa ![Office 365-anslutning](./media/connectors-create-api-office365-outlook/office365-sendemail.png)  
2. Om du inte tidigare har skapat några anslutningar till Office 365 uppmanas du att logga in med dina Office 365-autentiseringsuppgifter:  
    ![Steg för att skapa anslutning i Office 365](./media/connectors-create-api-office365-outlook/office365-signin.png)  
3. Välj **Logga**in och ange ditt användar namn och lösen ord. Välj **Logga**in:  
    steg för att skapa ![Office 365-anslutning](./media/connectors-create-api-office365-outlook/office365-usernamepassword.png)
   
    Dessa autentiseringsuppgifter används för att auktorisera din Logi Kap par för att ansluta till och få åtkomst till ditt Office 365-konto. 
4. Observera att anslutningen har skapats. Fortsätt nu med de andra stegen i din Logic app:   
    ![Steg för att skapa anslutning i Office 365](./media/connectors-create-api-office365-outlook/office365-sendemailproperties.png)  

