---
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 11/03/2016
ms.author: estfan
ms.openlocfilehash: 3f4430631a664f81f53f9df1f46ebc27c635de36
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2018
ms.locfileid: "50132720"
---
### <a name="prerequisites"></a>Förutsättningar
* En [Dropbox](https://www.Dropbox.com/) konto 

Innan du kan använda ditt Dropbox-konto i en logikapp, måste du godkänna logikappen som ska ansluta till ditt Dropbox-konto. Som tur är kan göra du det enkelt från i logikappen på Azure Portal. 

Här följer stegen för att auktorisera din logikapp för att ansluta till ditt Dropbox-konto:

1. Om du vill skapa en anslutning till Dropbox, i Logic appdesigner väljer **visa Microsoft hanterade API: er** i nedrullningsbara listan anger *Dropbox* i sökrutan. Välj utlösaren eller åtgärden som du kommer att tycka att använda:  
   ![Dropbox steg 1](./media/connectors-create-api-dropbox/dropbox-1.png)
2. Om du inte skapat några anslutningar till Dropbox innan du kan hämta uppmanas du att ange dina autentiseringsuppgifter för Dropbox. Dessa autentiseringsuppgifter används för att auktorisera din logikapp för att ansluta till och komma åt data i ditt Dropbox-konto:  
   ![Dropbox steg 2](./media/connectors-create-api-dropbox/dropbox-2.png)
3. Ange ditt Dropbox-användarnamn och lösenord för att auktorisera din logikapp:  
   ![Dropbox steg 3](./media/connectors-create-api-dropbox/dropbox-3.png)   
4. Auktorisera att Logic app att använda ditt Dropbox-konto:  
   ![Dropbox steg 4](./media/connectors-create-api-dropbox/dropbox-4.png)
5. Lägg märke till anslutningen har skapats och du kan nu fortsätta med andra steg i logikappen:  
   ![Dropbox steg 5](./media/connectors-create-api-dropbox/dropbox-5.png)   

