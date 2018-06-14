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
ms.openlocfilehash: ec5b3ca9ccd139cbdf17768056eb1d835336e7a7
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2018
ms.locfileid: "29764202"
---
1. I den [Azure-portalen](https://portal.azure.com), skapa en tom logikapp. 

2. Ange ”github” som filter i Logic Apps Designer. 

3. Välj den GitHub-anslutningen och utlösare som du vill använda.

   ![Välj den GitHub-anslutningen och en utlösare](./media/connectors-create-api-github/github-connector.png)

   > [!NOTE]
   > Alla arbetsflöden i logik-app måste inledas med en utlösare. Du kan välja åtgärder när arbetsflödet logik redan börjar med en utlösare. 

4. Om du inte tidigare skapade en anslutning, Välj **logga in** så att du kan ange dina GitHub-autentiseringsuppgifter när du tillfrågas.  

   ![Logga in med dina GitHub](./media/connectors-create-api-github/github-connector-sign-in-credentials.png)

   Logikappen använder dessa autentiseringsuppgifter för att auktorisera anslutningen och åtkomst till data för ditt GitHub-konto. 

5. Ange ditt GitHub-användarnamn och lösenord och bekräfta dina befogenheter.

   ![Ange autentiseringsuppgifter och Bekräfta auktorisering](./media/connectors-create-api-github/github-connector-authorize.png)   

   Anslutningen har skapats i Azure-portalen och är redo för användning.

6. Fortsätt att definiera logik app arbetsflödet.

   ![Lägga till fler åtgärder i logik app arbetsflödet](./media/connectors-create-api-github/github-connector-logic-app.png)

