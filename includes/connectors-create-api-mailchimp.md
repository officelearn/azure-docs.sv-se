---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/03/2016
ms.openlocfilehash: a7290e46a0c5e228689dd1f1a0dc7f9a1182ad60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789643"
---
## <a name="prerequisites"></a>Krav

* Ett [MailChimp-konto](https://www.MailChimp.com/) 

Innan du kan använda ditt MailChimp-konto i en Logic-app måste du auktorisera Logic-appen för att ansluta till ditt MailChimp-konto. Lyckligtvis kan du enkelt göra detta inifrån logikappen på Azure Portal. 

Här är stegen för att auktorisera din Logic-app för att ansluta till ditt MailChimp-konto:

1. Om du vill skapa en anslutning till MailChimp väljer du **Visa Microsoft-hanterade API:er** i listrutan Visa Microsoft och anger sedan *MailChimp* i sökrutan. Välj den utlösare eller åtgärd som du vill använda:  
   ![MailChimp steg 1](./media/connectors-create-api-mailchimp/mailchimp-1.png)
2. Om du inte har skapat några anslutningar till MailChimp tidigare uppmanas du att ange dina MailChimp-autentiseringsuppgifter. Dessa autentiseringsuppgifter används för att auktorisera din Logic-app att ansluta till och komma åt e-postkontots data:  
   ![MailChimp steg 2](./media/connectors-create-api-mailchimp/mailchimp-2.png)
3. Ange ditt MailChimp-användarnamn och lösenord för att auktorisera din Logic-app:  
   ![MailChimp steg 3](./media/connectors-create-api-mailchimp/mailchimp-3.png)   
4. Observera att anslutningen har skapats och du är nu fri att fortsätta med de andra stegen i logikappen:  
   ![MailChimp steg 4](./media/connectors-create-api-mailchimp/mailchimp-4.png)

