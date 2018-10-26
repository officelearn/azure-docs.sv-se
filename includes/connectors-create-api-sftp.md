---
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 11/03/2016
ms.author: estfan
ms.openlocfilehash: d249a205c64f4e067f2d81c7e1068c8ad9756958
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2018
ms.locfileid: "50094914"
---
### <a name="prerequisites"></a>Förutsättningar
* En [SFTP](https://en.wikipedia.org/wiki/SSH_File_Transfer_Protocol) konto  

Innan du kan använda ditt SFTP-konto i en logikapp, måste du godkänna logikappen som ska ansluta till ditt SFTP-konto. Som tur är kan göra du det enkelt från i logikappen på Azure Portal.  

Här följer stegen för att auktorisera din logikapp för att ansluta till ditt SFTP-konto:  

1. Om du vill skapa en anslutning till SFTP, i logic appdesigner väljer **visa Microsoft hanterade API: er** i nedrullningsbara listan anger *SFTP* i sökrutan. Välj den **SFTP - när en fil läggs till eller ändras** utlösare:  
   ![SFTP direktanslutning bild 1](./media/connectors-create-api-sftp/sftp-1.png)  
2. Om du inte skapat några anslutningar till SFTP innan du kan hämta uppmanas du att ange dina autentiseringsuppgifter för SFTP. Dessa autentiseringsuppgifter används för att auktorisera din logikapp för att ansluta till och få åtkomst till ditt SFTP-konto data:  
   ![SFTP direktanslutning bild 2](./media/connectors-create-api-sftp/sftp-2.png)  
3. Lägg märke till anslutningen har skapats och du kan nu fortsätta med andra steg i logikappen:   
   ![SFTP direktanslutning bild 3](./media/connectors-create-api-sftp/sftp-3.png) 

