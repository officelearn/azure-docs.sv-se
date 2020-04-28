---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/03/2016
ms.openlocfilehash: a7290e46a0c5e228689dd1f1a0dc7f9a1182ad60
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "74789643"
---
## <a name="prerequisites"></a>Krav

* Ett [MailChimp](https://www.MailChimp.com/) -konto 

Innan du kan använda ditt MailChimp-konto i en Logic app måste du godkänna att Logic-appen ansluter till ditt MailChimp-konto. Lyckligt vis kan du enkelt göra det i din Logic-app på Azure-portalen. 

Här följer stegen för att godkänna din Logic app för att ansluta till ditt MailChimp-konto:

1. För att skapa en anslutning till MailChimp, i Logic App Designer, väljer du **Visa Microsoft-hanterade API: er** i list rutan och anger sedan *MailChimp* i sökrutan. Välj den utlösare eller åtgärd som du vill använda:  
   ![MailChimp steg 1](./media/connectors-create-api-mailchimp/mailchimp-1.png)
2. Om du inte har skapat några anslutningar till MailChimp tidigare uppmanas du att ange dina MailChimp-autentiseringsuppgifter. Dessa autentiseringsuppgifter används för att auktorisera din Logi Kap par för att ansluta till och komma åt ditt MailChimp-kontos data:  
   ![MailChimp steg 2](./media/connectors-create-api-mailchimp/mailchimp-2.png)
3. Ange ditt MailChimp användar namn och lösen ord för att auktorisera din Logic app:  
   ![MailChimp steg 3](./media/connectors-create-api-mailchimp/mailchimp-3.png)   
4. Observera att anslutningen har skapats och att du nu är kostnads fri att fortsätta med de andra stegen i din Logic app:  
   ![MailChimp steg 4](./media/connectors-create-api-mailchimp/mailchimp-4.png)

