---
title: Skapa en ny app-LUIS
titleSuffix: Azure Cognitive Services
description: Skapa och hantera dina program på webbsidan för Språkförståelse (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 87244169aa8e50ddd503086121dd84f5d50c5df4
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68932780"
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

## <a name="import-an-app-from-file"></a>Importera en app från en fil

1. På sidan **Mina appar** väljer du **Importera ny app**.
1. I popup-dialogrutan väljer du en giltig app JSON-fil och väljer sedan **färdig**.

### <a name="import-errors"></a>Import fel

Möjliga fel är: 

* Det finns redan en app med det namnet. Du kan åtgärda detta genom att importera appen igen och ange det **valfria namnet** som ett nytt namn. 

## <a name="export-app-for-backup"></a>Exportera app för säkerhets kopiering

1. På sidan **Mina appar** väljer du **Exportera**.
1. Välj **Exportera som JSON**. Din webbläsare laddar ned den aktiva versionen av appen.
1. Lägg till den här filen i säkerhets kopierings systemet för att arkivera modellen.

## <a name="export-app-for-containers"></a>Exportera app för behållare

1. På sidan **Mina appar** väljer du **Exportera**.
1. Välj **Exportera som behållare** och välj sedan vilken Publicerad plats (produktion eller fas) som du vill exportera.
1. Använd den här filen med din [Luis-behållare](luis-container-howto.md). 

    Om du är intresse rad av att exportera en utbildad men ännu inte publicerad modell som ska användas med LUIS-behållaren går du till sidan **versioner** och exporterar därifrån. 

## <a name="delete-app"></a>Ta bort app

1. På sidan **Mina appar** väljer du de tre punkterna (...) i slutet av raden app.
1. Välj **ta bort** på menyn.
1. Välj **OK** i bekräftelse fönstret.

## <a name="next-steps"></a>Nästa steg

Din första uppgift i appen är att [Lägg till avsikter](luis-how-to-add-intents.md).
