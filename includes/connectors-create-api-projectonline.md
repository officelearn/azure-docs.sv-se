---
ms.openlocfilehash: 975d6625f46a6c10417793764b28d0a529a5c0cd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60921250"
---
### <a name="prerequisites"></a>Nödvändiga komponenter
* En [ProjectOnline](https://products.office.com/Project/project-online-with-project-for-office-365) konto 

Innan du kan använda ditt ProjectOnline-konto i en logikapp, måste du godkänna logikappen som ska ansluta till ditt ProjectOnline-konto. Som tur är kan göra du det enkelt från i logikappen på Azure Portal. 

Här följer stegen för att auktorisera din logikapp för att ansluta till din ProjectOnline-konto:

1. Om du vill skapa en anslutning till ProjectOnline, i Logic appdesigner väljer **visa Microsoft hanterade API: er** i nedrullningsbara listan anger *ProjectOnline* i sökrutan. Välj utlösaren eller åtgärden som du kommer att tycka att använda:  
   ![ProjectOnline steg 1](./media/connectors-create-api-projectonline/projectonline-1.png)
2. Om du inte skapat några anslutningar till ProjectOnline innan du kan hämta uppmanas du att ange dina autentiseringsuppgifter för ProjectOnline. Dessa autentiseringsuppgifter används för att auktorisera din logikapp för att ansluta till och komma åt ditt konto ProjectOnline data:  
   ![ProjectOnline steg 2](./media/connectors-create-api-projectonline/projectonline-2.png)
3. Ange ditt ProjectOnline-användarnamn och lösenord för att auktorisera din logikapp:  
   ![ProjectOnline steg 3](./media/connectors-create-api-projectonline/projectonline-3.png)   
4. Lägg märke till anslutningen har skapats och du kan nu fortsätta med andra steg i logikappen:  
   ![ProjectOnline steg 4](./media/connectors-create-api-projectonline/projectonline-4.png)   

