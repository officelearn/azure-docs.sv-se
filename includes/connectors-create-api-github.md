---
title: ta med fil
description: ta med fil
services: logic-apps
author: MandiOhlinger
ms.service: logic-apps
ms.topic: include
ms.date: 03/02/2018
ms.author: mandia
ms.custom: include file
ms.openlocfilehash: 11280e1678f52ede928cb2a85ea83add222e15fa
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66149215"
---
1. I den [Azure-portalen](https://portal.azure.com), skapa en tom logikapp. 

2. I Logic Apps Designer anger du ”github” som filter. 

3. Välj GitHub-anslutningsapp och utlösare som du vill använda.

   ![Välj den GitHub-anslutningen och en utlösare](./media/connectors-create-api-github/github-connector.png)

   > [!NOTE]
   > Alla arbetsflöden i logikappar måste börja med en utlösare. Du kan välja åtgärder endast när arbetsflödet logic redan börjar med en utlösare. 

4. Om du inte har tidigare har skapat en anslutning, välja **logga in** så att du kan ange dina autentiseringsuppgifter för GitHub när du tillfrågas.  

   ![Logga in med dina autentiseringsuppgifter för GitHub](./media/connectors-create-api-github/github-connector-sign-in-credentials.png)

   Logikappen använder dessa autentiseringsuppgifter för att auktorisera ansluter och komma åt data för ditt GitHub-konto. 

5. Ange ditt GitHub-användarnamn och lösenord och bekräfta sedan dina befogenheter.

   ![Ange autentiseringsuppgifter och Bekräfta auktorisering](./media/connectors-create-api-github/github-connector-authorize.png)   

   Anslutningen har nu skapats i Azure-portalen och är redo att användas.

6. Fortsätt att definiera logikappens arbetsflöde.

   ![Lägg till fler åtgärder logikappens arbetsflöde](./media/connectors-create-api-github/github-connector-logic-app.png)

