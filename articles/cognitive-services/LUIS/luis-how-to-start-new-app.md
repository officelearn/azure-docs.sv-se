---
title: Skapa en ny app med LUIS | Microsoft Docs
description: Skapa och hantera dina program på webbsidan för Språkförståelse (LUIS).
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 04/17/2018
ms.author: diberry
ms.openlocfilehash: 3adeecd4a4e2040a92689b7c92be9630c9a0d93b
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2018
ms.locfileid: "39225422"
---
# <a name="create-an-app"></a>Skapa en app
Du kan skapa en ny app på olika sätt: 

* [Starta](#create-new-app) med en tom app och skapa avsikter och yttranden entiteter.
* [Starta](#create-new-app) med en tom app och lägga till en [fördefinierade domän](luis-how-to-use-prebuilt-domains.md).
* [Importera en LUIS-app](#import-new-app) från en JSON-fil som innehåller redan avsikter och yttranden entiteter.

## <a name="what-is-an-app"></a>Vad är en app
Appen innehåller [versioner](luis-how-to-manage-versions.md) av din modell samt andra [medarbetare](luis-how-to-collaborate.md) för appen. När du skapar appen, väljer du kulturen ([språk](luis-supported-languages.md)) som **kan inte ändras senare**. 

Standardversionen av en ny app är ”0.1”. 

Du kan skapa och hantera dina program på **Mina appar** sidan. Du kan alltid komma åt den här sidan genom att välja **Mina appar** på det övre navigeringsfältet för den [LUIS](luis-reference-regions.md) webbplats. 

[![](media/luis-create-new-app/apps-list.png "Skärmbild av listan över appar")](media/luis-create-new-app/apps-list.png#lightbox)

## <a name="create-new-app"></a>Skapa ny app

1. På **Mina appar** väljer **skapar en ny app**.
2. Namnge ditt program ”TravelAgent” i dialogrutan.

    ![Skapa ny app dialog](./media/luis-create-new-app/create-app.png)

3. Välj ditt program kulturen (TravelAgent appen, Välj engelska), och välj sedan **klar**. 

    >[!NOTE]
    >Kulturen kan inte ändras när programmet har skapats. 

## <a name="import-new-app"></a>Importera ny app
Du kan ange namn (högst 50 tecken), version (högst 10 tecken) och beskrivning av en app i JSON-filen. Exempel på JSON-filer för programmet finns på [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/tree/master/documentation-samples/Examples-BookFlight).

1. På **Mina appar** väljer **importera ny app**.
2. I den **importera ny app** dialogrutan Välj JSON-fil som definierar LUIS-app.

    ![Importera en ny app-dialogruta](./media/luis-create-new-app/import-app.png)

## <a name="export-app"></a>Exportera app
1. På **Mina appar** väljer du de tre punkterna (***...*** ) i slutet av appraden.

    [![](media/luis-create-new-app/apps-list.png "Skärmbild av standardarbetsytan i dialogrutan för per app-åtgärder")](media/luis-create-new-app/three-dots.png#lightbox)

2. Välj **Export app** på menyn. 

## <a name="rename-app"></a>Byt namn på appen

1. På **Mina appar** väljer du de tre punkterna (***...*** ) i slutet av appraden. 
2. Välj **Byt namn på** på menyn.
3. Ange det nya namnet på appen och välj **klar**.

## <a name="delete-app"></a>Ta bort app

> [!CAUTION]
> Du tar bort appen för alla medarbetare och ägare. [Exportera](#export-app) appen innan den tas bort. 

1. På **Mina appar** väljer du de tre punkterna (***...*** ) i slutet av appraden. 
2. Välj **ta bort** på menyn.
3. Välj **Ok** i bekräftelsefönstret.

## <a name="export-endpoint-logs"></a>Exportloggar slutpunkt
Loggarna innehåller frågan, UTC-tid och LUIS JSON-svar.

1. På **Mina appar** väljer du de tre punkterna (***...*** ) i slutet av appraden. 
2. Välj **Exportloggar endpoint** på menyn.

```
Query,UTC DateTime,Response
text i'm driving and will be 30 minutes late to the meeting,02/13/2018 15:18:43,"{""query"":""text I'm driving and will be 30 minutes late to the meeting"",""intents"":[{""intent"":""None"",""score"":0.111048922},{""intent"":""SendMessage"",""score"":0.987501}],""entities"":[{""entity"":""i ' m driving and will be 30 minutes late to the meeting"",""type"":""Message"",""startIndex"":5,""endIndex"":58,""score"":0.162995353}]}"
```

## <a name="next-steps"></a>Nästa steg

Din första uppgift i appen är att [Lägg till avsikter](luis-how-to-add-intents.md).