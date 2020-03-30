---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/03/2016
ms.openlocfilehash: 3ca8d631110f8b175e7dc68d61cc6da4ac87d375
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789534"
---
## <a name="prerequisites"></a>Krav

* Ett [Office 365-användarkonto](https://office365.com)  

Innan du kan använda ditt Office 365-användare i en Logic-app måste du auktorisera Logic-appen för att kunna ansluta till ditt Office 365-användarkonto. Lyckligtvis kan du enkelt göra detta inifrån logikappen på Azure Portal.  

Här är stegen för att auktorisera logikappen för att ansluta till ditt Office 365-användarkonto:  

1. Om du vill skapa en anslutning till Office 365-användare väljer du **Visa Microsoft-hanterade API:er** i listrutan i Logic-appen och anger sedan *Office 365-användare* i sökrutan. Välj den utlösare eller åtgärd som du vill använda:  
   ![Steg för att skapa anslutningar för Office 365-användare](./media/connectors-create-api-office365users/office365users-1.png)  
2. Om du inte har skapat några anslutningar till Office 365-användare tidigare uppmanas du att ange dina autentiseringsuppgifter för Office 365-användare. Dessa autentiseringsuppgifter används för att auktorisera logikappen att ansluta till och komma åt office 365-kontots data:  
   ![Steg för att skapa anslutningar för Office 365-användare](./media/connectors-create-api-office365users/office365users-2.png)  
3. Ange ditt användarnamn och lösenord för Office 365-användare för att auktorisera logikappen:  
   ![Steg för att skapa anslutningar för Office 365-användare](./media/connectors-create-api-office365users/office365users-3.png)  
4. Observera att anslutningen har skapats och du är nu fri att fortsätta med de andra stegen i logikappen:  
   ![Steg för att skapa anslutningar för Office 365-användare](./media/connectors-create-api-office365users/office365users-4.png)  

