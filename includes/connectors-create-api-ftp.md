---
ms.openlocfilehash: 65f1e6d2489775a17ba2dacef0623706364fffab
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62108989"
---
### <a name="prerequisites"></a>Nödvändiga komponenter
* En [FTP](https://wikipedia.org/wiki/File_Transfer_Protocol) konto  

Innan du kan använda din FTP-konto i en logikapp, måste du godkänna logikappen att ansluta till FTP-konto. Som tur är kan göra du det enkelt från i logikappen på Azure Portal.  

Här följer stegen för att auktorisera din logikapp för att ansluta till FTP-konto:  

1. Om du vill skapa en anslutning till FTP, i logic appdesigner väljer **visa Microsoft hanterade API: er** i nedrullningsbara listan anger *FTP* i sökrutan. Välj utlösaren eller åtgärden som du kommer att tycka att använda:  
   ![FTP-anslutning, skapa steg](./media/connectors-create-api-ftp/ftp-1.png)  
2. Om du inte skapat några anslutningar till FTP innan du kan hämta uppmanas du att ange din FTP-autentiseringsuppgifter. Dessa autentiseringsuppgifter används för att auktorisera din logikapp för att ansluta till och få åtkomst till data för din FTP-konto:  
   ![FTP-anslutning, skapa steg](./media/connectors-create-api-ftp/ftp-2.png)  
3. Lägg märke till anslutningen har skapats och du kan nu fortsätta med andra steg i logikappen:  
   ![FTP-anslutning, skapa steg](./media/connectors-create-api-ftp/ftp-3.png)  

