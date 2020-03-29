---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/03/2016
ms.openlocfilehash: c0dcba2dd003dfebdd9ce67bf4d78082c32824ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789501"
---
## <a name="prerequisites"></a>Krav

* Ett [Office 365-videokonto](https://support.office.com/article/Meet-Office-365-Video-ca1cc1a9-a615-46e1-b6a3-40dbd99939a6)  

Innan du kan använda ditt Office 365 Video-konto i en Logic-app måste du auktorisera Logic-appen för att ansluta till ditt Office 365 Video-konto. Lyckligtvis kan du enkelt göra detta inifrån logikappen på Azure Portal.  

Här är stegen för att auktorisera din Logic-app för att ansluta till ditt Office 365 Video-konto:  

1. Om du vill skapa en anslutning till Office 365 Video väljer du **Visa Hanterade API:er** för Microsoft i listrutan i Logikappdesignern och anger sedan *Office 365 Video* i sökrutan. Välj den utlösare eller åtgärd som du vill använda:  
   ![Steg för att skapa videoanslutning i Office 365](./media/connectors-create-api-office365video/office365video-1.png)  
2. Om du inte har skapat några anslutningar till Office 365 Video tidigare uppmanas du att ange dina Office 365 Video-autentiseringsuppgifter. Dessa autentiseringsuppgifter används för att auktorisera logikappen att ansluta till och komma åt dina Office 365 Video-kontos data:  
   ![Steg för att skapa videoanslutning i Office 365](./media/connectors-create-api-office365video/office365video-2.png)  
3. Ange dina autentiseringsuppgifter för att ansluta till Office 365 Video:  
   ![Steg för att skapa videoanslutning i Office 365](./media/connectors-create-api-office365video/office365video-3.png)  
4. Observera att anslutningen har skapats och du är nu fri att fortsätta med de andra stegen i logikappen:  
   ![Steg för att skapa videoanslutning i Office 365](./media/connectors-create-api-office365video/office365video-4.png)  

