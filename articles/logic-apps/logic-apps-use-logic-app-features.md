---
title: "Lägg till villkor och starta arbetsflöden - Azure Logic Apps | Microsoft Docs"
description: "Styra hur arbetsflöden körs i Azure Logic Apps genom att lägga till införa villkorslogik, utlösare, åtgärder och parametrar."
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: e4e24de4-049a-4b3a-a14c-3bf3163287a8
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/28/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 751bcc21397effc501931c00afef05f2dab24e4a
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/03/2018
---
# <a name="use-logic-apps-features"></a>Använda funktioner i Logic Apps

I en [föregående avsnitt](../logic-apps/quickstart-create-first-logic-app-workflow.md), du har skapat din första logikapp. Du kan ange olika sökvägar för din logikapp för att köra och bearbeta data i matriser, samlingar och batchar för att kontrollera din logikapp arbetsflöde. Du kan inkludera dessa element i arbetsflödet logik app:

* Villkor och [växla instruktioner](../logic-apps/logic-apps-switch-case.md) låta din logikapp som kör olika åtgärder baserat på om särskilda villkor uppfylls.

* [Slingor](../logic-apps/logic-apps-loops-and-scopes.md) låta din logikapp köra steg upprepade gånger. T.ex, du kan upprepa åtgärder via en matris när du använder en **For_each** loop. Du kan upprepa åtgärder förrän villkor när du använder en **tills** loop.

* [Scope](../logic-apps/logic-apps-loops-and-scopes.md) kan du gruppera flera instruktioner, till exempel om du vill implementera undantagshantering.

* [Debatching](../logic-apps/logic-apps-loops-and-scopes.md) kan logikappen startar separat arbetsflöden för objekt i en matris när du använder den **SplitOn** kommando.

Det här avsnittet beskriver andra begrepp för att skapa din logikapp:

* Kodvy att redigera en befintlig logikapp
* Alternativ för att starta ett arbetsflöde

## <a name="conditions-run-steps-only-after-meeting-a-condition"></a>Villkor: Köra steg efter uppfyller ett villkor

Om du vill att din logikapp steg endast körs när data uppfyller specifika villkor kan du lägga till ett villkor som jämför data i arbetsflödet mot specifika fält eller värden.

Anta att du har en logikapp som skickar för många e-postmeddelanden för inlägg på en webbplats RSS-feed. Du kan lägga till ett villkor för din logikapp skickar e-postmeddelande när det nya inlägget tillhör en viss kategori.

1. I den [Azure-portalen](https://portal.azure.com), hitta och öppna logikappen i logik App Designer.

2. Lägga till ett villkor i arbetsflödet platsen som du vill. 

   Om du vill lägga till villkor mellan befintliga stegen i logik app arbetsflödet muspekaren på pilen där du vill lägga till villkoret. 
   Välj den **plustecknet** (**+**), Välj **Lägg till ett villkor**. Exempel:

   ![Lägg till villkor logikapp](./media/logic-apps-use-logic-app-features/add-condition.png)

   > [!NOTE]
   > Om du vill lägga till ett villkor i slutet av arbetsflödet aktuella gå längst ned i din logikapp och välj **+ nytt steg**.

3. Nu ska du ange villkoret. Ange det fält i datakällan som du vill utvärdera åtgärden som ska utföras och målvärdet eller fältet. Lägg till befintliga fält i dina villkor, Välj den **Lägg till dynamisk innehållslistan**.

   Exempel:

   ![Redigera villkor i grundläggande läge](./media/logic-apps-use-logic-app-features/edit-condition-basic-mode.png)

   Här är det fullständiga villkoret:

   ![Slutförd](./media/logic-apps-use-logic-app-features/edit-condition-basic-mode-2.png)

   > [!TIP]
   > Om du vill ange villkoret i koden, Välj **redigera i Avancerat läge**. Exempel:
   > 
   > ![Redigera villkor i koden](./media/logic-apps-use-logic-app-features/edit-condition-advanced-mode.png)

4. Under **Ja om** och **om Nej**, lägga till stegen för att utföra baserat på om villkoret är uppfyllt.

   Exempel:

   ![Villkor med Ja och inga sökvägar](./media/logic-apps-use-logic-app-features/condition-yes-no-path.png)

   > [!TIP]
   > Du kan dra befintliga åtgärder i den **Ja om** och **om Nej** sökvägar.

5. När du är klar sparar du din logikapp.

Nu får du e-postmeddelanden endast när meddelandena uppfyller villkoret.

## <a name="repeat-actions-over-a-list-with-foreach"></a>Upprepa åtgärder via en lista med forEach

ForEach-loop anger en matris om du vill upprepa åtgärden över. Flödet misslyckas om den inte är en matris. Om du har action1 som en matris med meddelanden och du vill skicka meddelandet, inkludera du den här forEach satsen i egenskaperna för åtgärden:`forEach : "@action('action1').outputs.messages"`

## <a name="edit-the-code-definition-for-a-logic-app"></a>Redigera koden definitionen för en logikapp

Även om du har logik App Designer kan redigera du den kod som definierar en logikapp direkt.

1. Välj på kommandofältet **vyn kod**.

    En fullständig redigerare öppnas och visar du redigerade definitionen.

    ![Kodvy](media/logic-apps-use-logic-app-features/codeview.png)

    Du kan kopiera och klistra in flera olika åtgärder i samma logikappen eller mellan logikappar i textredigeraren. 
    Du kan också enkelt lägga till eller ta bort hela avsnitt från definitionen och du kan också dela definitioner med andra.

2. Om du vill spara ändringarna, Välj **spara**.

## <a name="parameters"></a>Parametrar

Vissa funktioner i Logic Apps är endast tillgängliga i kodvy, till exempel parametrar. Parametrar som gör det lättare att återanvända värden i hela din logikapp. Om du har en e-postadress som du vill använda i flera åtgärder, bör du till exempel definiera den e-postadressen som en parameter.

Parametrarna är bra för att dra ut värden som troligen kommer att ändras mycket. De är särskilt användbar när du vill åsidosätta parametrar i olika miljöer. Information om hur du åsidosätter parametrar utifrån miljö finns [redigera logik app definitioner](../logic-apps/logic-apps-author-definitions.md) och [REST API-dokumentation](https://docs.microsoft.com/rest/api/logic).

Det här exemplet visar hur du uppdaterar din befintliga logikapp så att du kan använda parametrarna för frågetermen.

1. I kodvyn, hitta den `parameters : {}` objekt och lägger till en `currentFeedUrl` objekt:

        "currentFeedUrl" : {
            "type" : "string",
            "defaultValue" : "http://rss.cnn.com/rss/cnn_topstories.rss"
        }

2. Gå till den `When_a_feed-item_is_published` åtgärd, hitta den `queries` avsnittet och Ersätt värdet för frågan med:`"feedUrl": "#@{parameters('currentFeedUrl')}"` 

    För att ansluta två eller flera strängar kan du också använda den `concat` funktion. 
    Till exempel `"@concat('#',parameters('currentFeedUrl'))"` fungerar på samma sätt som anges ovan.

3.  När du är klar väljer **spara**. 

    Nu kan du ändra webbplatsens RSS-feed genom att ange en annan URL till den `currentFeedURL` objekt.

Lär dig mer om [hur du skapar logiken app definitioner](../logic-apps/logic-apps-author-definitions.md).

## <a name="start-logic-app-workflows"></a>Starta logik app arbetsflöden

Har du olika alternativ för att starta arbetsflödet som definierats i din logikapp. Du kan alltid starta ett arbetsflöde på begäran i den [Azure-portalen].

### <a name="recurrence-triggers"></a>Utlösare för upprepning

En utlösare för upprepning körs vid ett intervall som du anger. När utlösaren har införa villkorslogik, avgör utlösaren om arbetsflödet måste köras. En utlösare Anger att arbetsflödet ska köras genom att returnera en `200` statuskod. När arbetsflödet inte behöver köra utlösaren returnerar en `202` statuskod.

### <a name="callback-using-rest-apis"></a>Återanrop med hjälp av REST API: er

Om du vill starta ett arbetsflöde, kan tjänster anropa en logik app slutpunkt. Om du vill starta den här typen av logik app på begäran, Välj **kör nu** i kommandofältet. Se [starta arbetsflöden genom att anropa logik app slutpunkter som utlösare](../logic-apps/logic-apps-http-endpoint.md). 

<!-- Shared links -->
[Azure-portalen]: https://portal.azure.com

## <a name="next-steps"></a>Nästa steg

* [Växeln-instruktioner](../logic-apps/logic-apps-switch-case.md) 
* [Loopar, omfattningar och debatching](../logic-apps/logic-apps-loops-and-scopes.md)
* [Skapa Logic App-definitioner](../logic-apps/logic-apps-author-definitions.md)