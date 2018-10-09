---
title: Skapa en ny app med LUIS
description: Skapa och hantera dina program på webbsidan för Språkförståelse (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 55af0559c98c9fc63fd6902d80b4e6f2f0a7d2da
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2018
ms.locfileid: "48870123"
---
# <a name="create-an-app"></a>Skapa en app
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

    

<!--

## Import new app
You can set the name (50 char max), version (10 char max), and description of an app in the JSON file. Examples of application JSON files are available at [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/tree/master/documentation-samples/Examples-BookFlight).

1. On **My Apps** page, select **Import new app**.
2. In the **Import new app** dialog, select the JSON file defining the LUIS app.

    ![Import a new app dialog](./media/luis-create-new-app/import-app.png)

## Export app
1. On **My Apps** page, select the ellipsis (***...***) at the end of the app row.

    [![](media/luis-create-new-app/apps-list.png "Screenshot of pop-up dialog of per-app actions")](media/luis-create-new-app/three-dots.png#lightbox)

2. Select **Export app** from the menu. 

## Rename app

1. On **My Apps** page, select the ellipsis (***...***) at the end of the app row. 
2. Select **Rename** from the menu.
3. Enter the new name of the app and select **Done**.

## Delete app

> [!CAUTION]
> You are deleting the app for all collaborators and the owner. [Export](#export-app) the app before deleting it. 

1. On **My Apps** page, select the ellipsis (***...***) at the end of the app row. 
2. Select **Delete** from the menu.
3. Select **Ok** in the confirmation window.

## Export endpoint logs
The logs contain the Query, UTC time, and LUIS JSON response.

1. On **My Apps** page, select the ellipsis (***...***) at the end of the app row. 
2. Select **Export endpoint logs** from the menu.

```
Query,UTC DateTime,Response
text i'm driving and will be 30 minutes late to the meeting,02/13/2018 15:18:43,"{""query"":""text I'm driving and will be 30 minutes late to the meeting"",""intents"":[{""intent"":""None"",""score"":0.111048922},{""intent"":""SendMessage"",""score"":0.987501}],""entities"":[{""entity"":""i ' m driving and will be 30 minutes late to the meeting"",""type"":""Message"",""startIndex"":5,""endIndex"":58,""score"":0.162995353}]}"
```
-->
## <a name="next-steps"></a>Nästa steg

Din första uppgift i appen är att [Lägg till avsikter](luis-how-to-add-intents.md).