---
title: Skapa en ny app - LUIS
titleSuffix: Azure Cognitive Services
description: Skapa och hantera dina program på webbsidan Språk understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: diberry
ms.openlocfilehash: d3f8696388a33a8ea112aae438c6bbe9af520c61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220835"
---
# <a name="create-a-new-luis-app-in-the-luis-portal"></a>Skapa en ny LUIS-app i LUIS-portalen
Det finns ett par sätt att skapa en LUIS-app. Du kan skapa en LUIS-app i LUIS-portalen eller via [LUIS-redigerings-API:erna](developer-reference-resource.md).

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="using-the-luis-portal"></a>Använda LUIS-portalen

Du kan skapa en ny app i förhandsgranskningsportalen på flera sätt:

* Börja med en tom app och skapa avsikter, yttranden och entiteter.
* Börja med en tom app och lägg till en [fördefinierad domän](luis-how-to-use-prebuilt-domains.md).
* Importera en LUIS-app från en `.lu` eller `.json` en fil som redan innehåller avsikter, yttranden och entiteter.

## <a name="using-the-authoring-apis"></a>Använda redigerings-API:erna
Du kan skapa en ny app med redigerings-API:erna på ett par olika sätt:

* [Lägg till program](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2f) - börja med en tom app och skapa avsikter, yttranden och entiteter.
* [Lägg till fördefinierat program](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/59104e515aca2f0b48c76be5) - börja med en fördefinierad domän, inklusive avsikter, yttranden och entiteter.  


<a name="export-app"></a>
<a name="import-new-app"></a>
<a name="delete-app"></a>
 

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-new-app-in-luis"></a>Skapa ny app i LUIS

1. På sidan **Mina appar** väljer du din prenumeration och skapar resursen sedan **+ Skapa**. Om du använder kostnadsfri utvärderingsversionsnyckel kan du läsa om hur du [skapar en redigeringsresurs](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal).

    ![Lista över LUIS-appar](./media/create-app-in-portal.png)


1. I dialogrutan anger du namnet på programmet, `Pizza Tutorial`till exempel .

    ![Dialogrutan Skapa ny app](./media/create-pizza-tutorial-app-in-portal.png)

1. Välj programkultur och välj sedan **Klar**. Beskrivnings- och förutsägelseresursen är valfria just nu. Du kan ställa in sedan när som helst i avsnittet **Hantera** på portalen.

    > [!NOTE]
    > Kulturen kan inte ändras när appen har skapats. 

    När appen har skapats visar **Intents** LUIS-portalen `None` listan Avsikter med den avsikt som redan har skapats åt dig. Du har nu en tom app. 
    
    > [!div class="mx-imgBorder"]
    > ![Avsikter lista med Ingen avsikt skapas utan exempel yttranden.](media/pizza-tutorial-new-app-empty-intent-list.png)

## <a name="other-actions-available"></a>Andra åtgärder som finns tillgängliga

Kontextverktygsfältet innehåller andra åtgärder:

* Byt namn på appen
* Importera från `.lu` fil med eller`.json`
* Exportera app `.lu` som (för `.json` [LUDown](https://github.com/microsoft/botbuilder-tools/tree/master/packages/Ludown)), eller `.zip` (för [LUIS-behållare)](luis-container-howto.md)
* Importera slutpunktsloggar för behållare, för att granska slutpunktsyttranden
* Exportera slutpunktsloggar, `.csv`som en , för offlineanalys
* Ta bort app

## <a name="next-steps"></a>Nästa steg

Om appdesignen innehåller avsiktsidentifiering [skapar du nya avsikter](luis-how-to-add-intents.md)och lägger till exempelyttranden. Om appdesignen bara är dataextrahering lägger du till exempelyttranden i ingen avsikten, skapar sedan [entiteter](luis-how-to-add-example-utterances.md)och märker exempelyttrandena med dessa entiteter. 
