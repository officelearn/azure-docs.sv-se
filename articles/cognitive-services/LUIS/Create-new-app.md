---
title: Skapa en ny app med THOMAS | Microsoft Docs
description: Skapa och hantera dina program på webbsidan språk förstå (THOMAS).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 04/17/2018
ms.author: v-geberr
ms.openlocfilehash: 75edd39346995cdef72bb1e1fcb9eaff53d29702
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "35355755"
---
# <a name="create-an-app"></a>Skapa en app
Du kan skapa en ny app på olika sätt: 

* [Starta](#create-new-app) med en tom app och skapa avsikter, utterances och entiteter.
* [Starta](#create-new-app) med en tom app och lägga till en [färdiga domän](luis-how-to-use-prebuilt-domains.md).
* [Importera en THOMAS app](#import-new-app) från en JSON-fil som innehåller redan avsikter, utterances och entiteter.

## <a name="what-is-an-app"></a>Vad är en app
Appen innehåller [versioner](luis-how-to-manage-versions.md) av din modell samt andra [medarbetare](luis-how-to-collaborate.md) för appen. När du skapar appen väljer du kulturen ([språk](luis-supported-languages.md)) som **kan inte ändras senare**. 

Standardversionen av en ny app är ”0.1”. 

Du kan skapa och hantera dina program på **Mina appar** sidan. Du kan alltid komma åt den här sidan genom att välja **Mina appar** i det övre navigeringsfältet av den [THOMAS](luis-reference-regions.md) webbplats. 

[![](media/luis-create-new-app/apps-list.png "Skärmbild av listan över appar")](media/luis-create-new-app/apps-list.png#lightbox)

## <a name="create-new-app"></a>Skapa ny app

1. På **Mina appar** väljer **Skapa ny app**.
2. I dialogrutan namn för programmet ”TravelAgent”.

    ![Skapa ny app dialog](./media/luis-create-new-app/create-app.png)

3. Välj programmet-kulturen (TravelAgent appen, Välj engelska), och välj sedan **klar**. 

    >[!NOTE]
    >Kulturen kan inte ändras när programmet har skapats. 

## <a name="import-new-app"></a>Importera nya app
Du kan ange namn (max 50 tecken), version (max 10 tecken) och beskrivning av en app i JSON-filen. Exempel på program JSON-filer finns på [THOMAS exempel](https://github.com/Microsoft/LUIS-Samples/tree/master/documentation-samples/Examples-BookFlight).

1. På **Mina appar** väljer **Importera nya app**.
2. I den **Importera nya app** dialogrutan Välj JSON-fil som definierar THOMAS appen.

    ![Importera en ny app dialog](./media/luis-create-new-app/import-app.png)

## <a name="export-app"></a>Exportera appen
1. På **Mina appar** markerar du de tre punkterna (...) i slutet av raden app.

    [![](media/luis-create-new-app/apps-list.png "Skärmbild av popup-fönstret för per app-åtgärder")](media/luis-create-new-app/three-dots.png#lightbox)

2. Välj **Export app** på menyn. 

## <a name="rename-app"></a>Byt namn på appen

1. På **Mina appar** markerar du de tre punkterna (...) i slutet av raden app. 
2. Välj **Byt namn på** på menyn.
3. Ange det nya namnet för appen och välj **klar**.

## <a name="delete-app"></a>Ta bort app

> [!CAUTION]
> Du tar bort app för alla medarbetare och ägare. [Exportera](#export-app) appen innan den tas bort. 

1. På **Mina appar** markerar du de tre punkterna (...) i slutet av raden app. 
2. Välj **ta bort** på menyn.
3. Välj **Ok** i bekräftelsefönstret.

## <a name="export-endpoint-logs"></a>Exportera endpoint loggar
Loggarna innehåller frågan UTC-tid och LUIS JSON-svar.

1. På **Mina appar** markerar du de tre punkterna (...) i slutet av raden app. 
2. Välj **exporterar endpoint loggar** på menyn.

```
Query,UTC DateTime,Response
text i'm driving and will be 30 minutes late to the meeting,02/13/2018 15:18:43,"{""query"":""text I'm driving and will be 30 minutes late to the meeting"",""intents"":[{""intent"":""None"",""score"":0.111048922},{""intent"":""SendMessage"",""score"":0.987501}],""entities"":[{""entity"":""i ' m driving and will be 30 minutes late to the meeting"",""type"":""Message"",""startIndex"":5,""endIndex"":58,""score"":0.162995353}]}"
```

## <a name="next-steps"></a>Nästa steg

Den första aktiviteten i appen är att [lägga till avsikter](luis-how-to-add-intents.md).