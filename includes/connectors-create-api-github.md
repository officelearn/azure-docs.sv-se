---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 03/02/2018
ms.openlocfilehash: 1aabc1b33722c571c6519b6620fb62211a44c2e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789761"
---
1. Skapa en tom logikapp i [Azure-portalen.](https://portal.azure.com) 

2. Ange "github" som filter i Logic Apps Designer. 

3. Välj GitHub-kopplingen och den utlösare som du vill använda.

   ![Välj GitHub-anslutning och en utlösare](./media/connectors-create-api-github/github-connector.png)

   > [!NOTE]
   > Alla logikapparbetsflöden måste börja med en utlösare. Du kan bara välja åtgärder när logikarbetsflödet redan börjar med en utlösare. 

4. Om du inte tidigare har skapat en anslutning väljer du **Logga in** så att du kan ange dina GitHub-autentiseringsuppgifter när du uppmanas att göra det.  

   ![Logga in med dina GitHub-autentiseringsuppgifter](./media/connectors-create-api-github/github-connector-sign-in-credentials.png)

   Logikappen använder dessa autentiseringsuppgifter för att auktorisera anslutning och åtkomst till data för ditt GitHub-konto. 

5. Ange ditt användarnamn och lösenord för GitHub och bekräfta sedan din auktorisering.

   ![Ange autentiseringsuppgifter och bekräfta auktorisering](./media/connectors-create-api-github/github-connector-authorize.png)   

   Anslutningen skapas nu i Azure-portalen och är klar för användning.

6. Fortsätt att definiera arbetsflödet för logikappen.

   ![Lägga till fler åtgärder i logikapparbetsflödet](./media/connectors-create-api-github/github-connector-logic-app.png)

