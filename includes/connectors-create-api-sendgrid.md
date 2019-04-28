---
ms.openlocfilehash: b06c57f50f759677770839ae06cb05e4de36f84c
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62103688"
---
### <a name="prerequisites"></a>Nödvändiga komponenter
* En [SendGrid](https://www.SendGrid.com/) konto 

Innan du kan använda SendGrid-konto i en logikapp, måste du godkänna logikappen att ansluta till SendGrid-konto. Som tur är kan göra du det enkelt från i logikappen på Azure Portal. 

Här följer stegen för att auktorisera din logikapp för att ansluta till SendGrid-konto:

1. Om du vill skapa en anslutning till SendGrid, i Logic appdesigner väljer **visa Microsoft hanterade API: er** i nedrullningsbara listan anger *SendGrid* i sökrutan. Välj utlösaren eller åtgärden som du kommer att tycka att använda:  
   ![SendGrid-steg 1](./media/connectors-create-api-sendgrid/sendgrid-1.png)
2. Om du inte skapat några anslutningar till SendGrid innan du kan hämta uppmanas du att ange din SendGrid-autentiseringsuppgifter. Dessa autentiseringsuppgifter används för att auktorisera din logikapp för att ansluta till och komma åt data i ditt SendGrid-konto:  
   ![SendGrid steg 2](./media/connectors-create-api-sendgrid/sendgrid-2.png)
3. Lägg märke till anslutningen har skapats och du kan nu fortsätta med andra steg i logikappen:  
   ![SendGrid steg 3](./media/connectors-create-api-sendgrid/sendgrid-3.png)   

