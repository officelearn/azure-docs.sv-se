---
title: Skapa en ny app
titleSuffix: Language Understanding - Azure Cognitive Services
description: Skapa och hantera dina program på webbsidan för Språkförståelse (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: 3c35bb96c3ba5dbf1c3302836b2c73cf15128937
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55215188"
---
# <a name="create-a-new-luis-app-in-the-luis-portal"></a>Skapa en ny LUIS-app i LUIS-portalen
Det finns ett par olika sätt att skapa en LUIS-app. Du kan skapa en LUIS-app i den [LUIS](https://www.luis.ai) portal, eller via LUIS redigering [API: er](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f).

## <a name="using-the-luis-portal"></a>Med LUIS-portalen
Du kan skapa en ny app i portalen LUIS på flera olika sätt:

* Börja med en tom app och skapa avsikter och yttranden entiteter.
* Börja med en tom app och lägga till en [fördefinierade domän](luis-how-to-use-prebuilt-domains.md).
* Importera en LUIS-app från en JSON-fil som redan innehåller avsikter och yttranden entiteter.

## <a name="using-the-authoring-apis"></a>Med redigering API: er
Du kan skapa en ny app med redigering API: er på ett par olika sätt:

* [Starta](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f) med en tom app och skapa avsikter och yttranden entiteter.
* [Starta](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/59104e515aca2f0b48c76be5) med en fördefinierade domän.  


<a name="export-app"></a>
<a name="import-new-app"></a>
<a name="delete-app"></a>
 

## <a name="create-new-app-in-luis"></a>Skapa ny app i LUIS

1. På **Mina appar** väljer **skapar en ny app**.

    ![Lista över LUIS-appar](./media/luis-create-new-app/apps-list.png)


2. Namnge ditt program ”TravelAgent” i dialogrutan.

    ![Skapa ny app dialog](./media/luis-create-new-app/create-app.png)

3. Välj ditt program kulturen (TravelAgent appen, Välj engelska), och välj sedan **klar**. 

    > [!NOTE]
    > Kulturen kan inte ändras när appen har skapats. 


## <a name="next-steps"></a>Nästa steg

Din första uppgift i appen är att [Lägg till avsikter](luis-how-to-add-intents.md).
