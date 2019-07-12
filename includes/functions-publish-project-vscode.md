---
title: ta med fil
description: ta med fil
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 04/16/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 3cfa36331f8f4ad45f3bf8ff32eee7d89c7d8852
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2019
ms.locfileid: "67608387"
---
## <a name="publish-the-project-to-azure"></a>Publicera projektet på Azure

I Visual Studio Code kan du publicera funktionsprojektet direkt på Azure. Samtidigt skapar du en funktionsapp och relaterade resurser i Azure-prenumerationen. Funktionsappen är ett körningssammanhang för dina funktioner. Projektet paketeras och distribueras till den nya funktionsappen i Azure-prenumerationen.

Som standard skapar alla Azure resurser som krävs för att skapa din funktionsapp med Visual Studio. Namnen på de här resurserna baseras på funktionsappens namn du väljer. Om du behöver ha fullständig kontroll över de skapade resurserna kan du i stället [publicera med hjälp av avancerade alternativ](../articles/azure-functions/functions-develop-vs-code.md#enabled-publishing-with-advanced-create-options).

Det här avsnittet förutsätter att du skapar en ny funktionsapp i Azure.

> [!IMPORTANT]
> Om du publicerar till en befintlig funktionsapp skrivs innehållet i den appen över i Azure.

1. I Visual Studio Code trycker du på F1 för att öppna kommandopaletten. I kommandopaletten, Sök efter och välj `Azure Functions: Deploy to function app...`.

1. Om inte loggat in, uppmanas du att **logga in på Azure**. Du kan också **skapa ett kostnadsfritt konto**. Efter lyckad inloggning i webbläsaren, gå tillbaka till Visual Studio Code. 

1. Om du har flera prenumerationer **Välj en prenumeration** för funktionsappen, välj sedan **+ Skapa ny Funktionsapp i Azure**.

1. Ange ett globalt unikt namn som identifierar funktionsappen och tryck på Retur. Giltiga tecken i ett namn på en funktionsapp är `a-z`, `0-9` och `-`.

    När du trycker på RETUR, skapas följande Azure-resurser i din prenumeration:

    * **[Resursgrupp](../articles/azure-resource-manager/resource-group-overview.md)** : Innehåller alla de skapa Azure-resurserna. Namnet är baserad på din funktionsappens namn.
    * **[Storage-konto](../articles/storage/common/storage-quickstart-create-account.md)** : Ett standard Storage-konto har skapats med ett unikt namn som baseras på din funktionsappens namn.
    * **[Värdplan](../articles/azure-functions/functions-scale.md)** : En förbrukningsplan har skapats i regionen USA, västra som värd för din funktionsapp utan server.
    * **Funktionsapp**: Projektet distribueras till och körs i den nya funktionsappen.

    Ett meddelande visas när funktionsappen har skapats och distributionspaketet har tillämpats. Välj **visa utdata** i det här meddelandet att visa skapande och distributionsresultat, inklusive Azure-resurser som du skapade.

1. I den **Azure: Functions** området Expandera den nya funktionsappen i din prenumeration. Expandera **Functions**, högerklicka på **HttpTrigger**, och välj sedan **kopiera Funktionswebbadressen**.

    ![Kopiera Funktionswebbadressen för den nya HTTP-utlösaren](./media/functions-publish-project-vscode/function-copy-endpoint-url.png)
