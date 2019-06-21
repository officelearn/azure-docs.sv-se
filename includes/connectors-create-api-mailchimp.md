---
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 11/03/2016
ms.author: estfan
ms.openlocfilehash: 752c43604349a2361a8f5b26cd6d0bce7b516bc0
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67187604"
---
### <a name="prerequisites"></a>Nödvändiga komponenter
* En [MailChimp](https://www.MailChimp.com/) konto 

Innan du kan använda ditt MailChimp-konto i en logikapp, måste du godkänna logikappen som ska ansluta till ditt MailChimp-konto. Som tur är kan göra du det enkelt från i logikappen på Azure Portal. 

Här följer stegen för att auktorisera din logikapp för att ansluta till ditt MailChimp-konto:

1. Om du vill skapa en anslutning till MailChimp, i Logic appdesigner väljer **visa Microsoft hanterade API: er** i nedrullningsbara listan anger *MailChimp* i sökrutan. Välj utlösaren eller åtgärden som du kommer att tycka att använda:  
   ![MailChimp step 1](./media/connectors-create-api-mailchimp/mailchimp-1.png)
2. Om du inte skapat några anslutningar till MailChimp innan du kan hämta uppmanas du att ange dina autentiseringsuppgifter för MailChimp. Dessa autentiseringsuppgifter används för att auktorisera din logikapp för att ansluta till och komma åt data i ditt MailChimp-konto:  
   ![MailChimp step 2](./media/connectors-create-api-mailchimp/mailchimp-2.png)
3. Ange ditt MailChimp-användarnamn och lösenord för att auktorisera din logikapp:  
   ![MailChimp step 3](./media/connectors-create-api-mailchimp/mailchimp-3.png)   
4. Lägg märke till anslutningen har skapats och du kan nu fortsätta med andra steg i logikappen:  
   ![MailChimp step 4](./media/connectors-create-api-mailchimp/mailchimp-4.png)

