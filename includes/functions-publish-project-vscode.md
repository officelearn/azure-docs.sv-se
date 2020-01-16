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
ms.openlocfilehash: 66c100ecd40e9f0899f7686397339ab849315c3e
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2020
ms.locfileid: "76021298"
---
## <a name="publish-the-project-to-azure"></a>Publicera projektet på Azure

I Visual Studio Code kan du publicera funktionsprojektet direkt på Azure. Samtidigt skapar du en funktionsapp och relaterade resurser i Azure-prenumerationen. Funktionsappen är ett körningssammanhang för dina funktioner. Projektet paketeras och distribueras till den nya funktionsappen i Azure-prenumerationen.

Som standard skapar Visual Studio Code alla Azure-resurser som krävs för att skapa din Function-app. Namnen på de här resurserna baseras på det program namn för funktionen som du väljer. Om du behöver fullständig kontroll över de skapade resurserna kan du i stället [publicera med avancerade alternativ](../articles/azure-functions/functions-develop-vs-code.md#enable-publishing-with-advanced-create-options).

Det här avsnittet förutsätter att du skapar en ny function-app i Azure.

> [!IMPORTANT]
> Om du publicerar till en befintlig funktionsapp skrivs innehållet i den appen över i Azure.

1. I Visual Studio Code, trycker du på F1 för att öppna kommando paletten. I paletten kommando söker du efter och väljer `Azure Functions: Deploy to function app...`.

1. Om du inte är inloggad uppmanas du att **Logga in på Azure**. Du kan också **skapa ett kostnads fritt Azure-konto**. När du har loggat in från webbläsaren går du tillbaka till Visual Studio Code. 

1. Om du har flera prenumerationer **väljer du en prenumeration** för Function-appen och väljer sedan **+ Skapa ny Funktionsapp i Azure**.

1. Ange ett globalt unikt namn som identifierar funktionsappen och tryck på Retur. Giltiga tecken i ett namn på en funktionsapp är `a-z`, `0-9` och `-`.

    När du trycker på RETUR skapas följande Azure-resurser i din prenumeration:

    * **[Resurs grupp](../articles/azure-resource-manager/management/overview.md)** : innehåller alla Azure-resurser som skapats. Namnet baseras på namnet på din Function-app.
    * **[Lagrings konto](../articles/storage/common/storage-account-create.md)** : ett standard lagrings konto skapas med ett unikt namn som baseras på namnet på din Function-app.
    * **[Värd plan](../articles/azure-functions/functions-scale.md)** : en förbruknings plan skapas i regionen Västra USA för att vara värd för din server lös Function-app.
    * **Function-app**: ditt projekt distribueras till och körs i den här nya Function-appen.

    Ett meddelande visas när funktionsappen har skapats och distributionspaketet har tillämpats. Välj **Visa utdata** i det här meddelandet för att Visa skapande-och distributions resultaten, inklusive de Azure-resurser som du har skapat.

1. Gå tillbaka till avsnittet **Azure: Functions** , expandera den nya Function-appen under din prenumeration. Expandera **funktioner**, högerklicka på **HttpTrigger**och välj sedan **Kopiera funktions webb adress**.

    ![Kopiera funktions webb adressen för den nya HTTP-utlösaren](./media/functions-publish-project-vscode/function-copy-endpoint-url.png)
