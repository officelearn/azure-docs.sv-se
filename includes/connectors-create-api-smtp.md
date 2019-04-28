---
ms.openlocfilehash: d90ef1737f0107e5e323c1e90e3f9021efd4e4f7
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62130019"
---
### <a name="prerequisites"></a>Nödvändiga komponenter
* En [SMTP](https://wikipedia.org/wiki/Simple_Mail_Transfer_Protocol) konto  

Innan du kan använda din SMTP-kontot i en logikapp, måste du godkänna logikappen att ansluta till SMTP-kontot. Som tur är kan göra du det enkelt från i logikappen på Azure Portal.  

Här följer stegen för att auktorisera din logikapp för att ansluta till SMTP-kontot:  

1. Om du vill skapa en anslutning till SMTP, i logic appdesigner väljer **visa Microsoft hanterade API: er** i nedrullningsbara listan anger *SMTP* i sökrutan. Välj utlösaren eller åtgärden som du kommer att tycka att använda:  
   ![](./media/connectors-create-api-smtp/smtp-1.png)  
2. Om du inte skapat några anslutningar till SMTP innan du kan hämta uppmanas du att ange dina autentiseringsuppgifter för SMTP. Dessa autentiseringsuppgifter används för att auktorisera din logikapp för att ansluta till och få åtkomst till SMTP-kontot data:  
   ![](./media/connectors-create-api-smtp/smtp-2.png)  
3. Lägg märke till anslutningen har skapats och du kan nu fortsätta med andra steg i logikappen:  
   ![](./media/connectors-create-api-smtp/smtp-3.png)  

