---
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 11/03/2016
ms.author: estfan
ms.openlocfilehash: 0cabc58d856c09accd9b1924fe63d6518b1cb9ef
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2018
ms.locfileid: "50093504"
---
För att ansluta till **SharePoint Online**, måste du ange din identitet (användarnamn och lösenord, smartkort autentiseringsuppgifter osv) till SharePoint Online. När du har autentiserats kan du fortsätta att använda SharePoint Online-kopplingen i din logikapp. 

I designer av din logikapp, följer du dessa steg för att logga in på SharePoint för att skapa den **anslutning** för användning i din logikapp:

1. Ange SharePoint i sökrutan och vänta tills sökning för att returnera alla utlösare och åtgärder som är relaterade till SharePoint Online:   
   ![Konfigurera SharePoint][1]  
2. Välj den **SharePoint Online – när en fil skapas** utlösare  
3. Välj **logga in till SharePoint Online**:   
   ![Konfigurera SharePoint][2]    
4. Ange dina autentiseringsuppgifter för SharePoint för att logga in att autentisera med SharePoint   
   ![Konfigurera SharePoint][3]     
5. När autentiseringen är klar kommer du att omdirigeras till din logikapp. Det var allt – anslutningen har skapats. Meddelande längst ned på sidan som visar att du är nu ansluten till SharePoint.  
   ![Konfigurera SharePoint][4]  
6. Du kan sedan lägga till andra utlösare och åtgärder som du behöver för att slutföra din logikapp.   

[1]: ./media/connectors-create-api-sharepointonline/connectionconfig1.png
[2]: ./media/connectors-create-api-sharepointonline/connectionconfig2.png 
[3]: ./media/connectors-create-api-sharepointonline/connectionconfig3.png
[4]: ./media/connectors-create-api-sharepointonline/connectionconfig4.png
[5]: ./media/connectors-create-api-sharepointonline/connectionconfig5.png
