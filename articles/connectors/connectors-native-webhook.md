---
title: Vänta och svara på händelser – Azure Logic Apps
description: Automatisera arbets flöden som utlöser, pausar och återupptar baserat på händelser på en tjänst slut punkt med hjälp av Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 10/10/2019
tags: connectors
ms.openlocfilehash: 36b0ea7233b449584bd83450b45276da5baa135b
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2019
ms.locfileid: "72264335"
---
# <a name="create-and-run-automated-event-based-workflows-by-using-http-webhooks-in-azure-logic-apps"></a>Skapa och kör automatiska händelsebaserade arbets flöden genom att använda HTTP-webhookar i Azure Logic Apps

Med [Azure Logic Apps](../logic-apps/logic-apps-overview.md) och den inbyggda http webhook-anslutningen kan du automatisera arbets flöden som väntar och körs baserat på vissa händelser som inträffar på en http-eller HTTPS-slutpunkt genom att skapa Logi Kap par. Du kan till exempel skapa en Logic-app som övervakar en tjänst slut punkt genom att vänta på en viss händelse innan du utlöser arbets flödet och kör de angivna åtgärderna, i stället för att regelbundet kontrol lera eller *avsöka* den slut punkten.

Här följer några exempel på händelsebaserade arbets flöden:

* Vänta tills ett objekt tas emot från en [Azure Event Hub](https://github.com/logicappsio/EventHubAPI) innan du utlöser en Logic app-körning.
* Vänta på ett godkännande innan du fortsätter med ett arbets flöde.

## <a name="how-do-webhooks-work"></a>Hur fungerar Webhooks?

En HTTP-webhook-utlösare är event-baserad, som inte är beroende av att kontrol lera eller avsökningar regelbundet för nya objekt. När du sparar en Logic app som börjar med en webhook-utlösare, eller när du ändrar din Logi Kap par från inaktive rad till aktive rad, utlöser webhooken *prenumerationer* på en viss tjänst eller slut punkt genom att registrera en *callback-URL* med den tjänsten eller slut punkten Utlösaren väntar sedan på att tjänsten eller slut punkten ska anropa URL: en, som börjar köra Logic app. I likhet med [begär ande utlösaren](connectors-native-reqres.md)utlöses Logic app omedelbart när den angivna händelsen inträffar. Utlösaren *avbryter prenumerationen* på tjänsten eller slut punkten om du tar bort utlösaren och sparar din Logic app, eller när du ändrar din Logi Kap par från aktive rad till inaktive rad.

En HTTP webhook-åtgärd är också händelse-baserad och *prenumererar* på en viss tjänst eller slut punkt genom att registrera en *återanrops-URL* med den tjänsten eller slut punkten. Webhook-åtgärden pausar Logic app-arbetsflöde och väntar tills tjänsten eller slut punkten anropar URL: en innan Logic-appen återupptas. Åtgärds logik appen *avbryter prenumerationen* på tjänsten eller slut punkten i följande fall:

* När webhook-åtgärden har slutförts
* Om körningen av Logic app avbryts under väntan på svar
* Innan tids gränsen nåtts för Logic app

Till exempel är Office 365 Outlook Connector [ **-e-** ](connectors-create-api-office365-outlook.md) poståtgärden skicka godkännande ett exempel på en webhook-åtgärd som följer det här mönstret. Du kan utöka det här mönstret till vilken tjänst som helst med hjälp av webhook-åtgärden.

> [!NOTE]
> Logic Apps tillämpar Transport Layer Security (TLS) 1,2 när anropet tas emot till HTTP webhook-utlösaren eller åtgärden. Om du ser fel i SSL-handskakning kontrollerar du att du använder TLS 1,2.

Mer information finns i följande avsnitt:

* [Parametrar för utlösare för HTTP-webhook](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)
* [Webhooks och prenumerationer](../logic-apps/logic-apps-workflow-actions-triggers.md#webhooks-and-subscriptions)
* [Skapa anpassade API: er som stöder en webhook](../logic-apps/logic-apps-create-api-app.md)

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* URL: en för en redan distribuerad slut punkt eller API som har stöd för webhook-prenumerationen och prenumerations mönstret för [webhook-utlösare i Logic Apps](../logic-apps/logic-apps-create-api-app.md#webhook-triggers) eller [webhook-åtgärder i Logic Apps](../logic-apps/logic-apps-create-api-app.md#webhook-actions) efter behov

* Grundläggande information om [hur du skapar Logic Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md). Om du är nybörjare på Logi Kap par kan du läsa om [Vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

* Den Logic-app där du vill vänta på specifika händelser vid mål slut punkten. Börja med HTTP webhook-utlösaren genom att [skapa en tom Logic-app](../logic-apps/quickstart-create-first-logic-app-workflow.md). Om du vill använda HTTP webhook-åtgärden startar du din Logic-app med valfri utlösare som du vill använda. I det här exemplet används HTTP-utlösaren som det första steget.

## <a name="add-an-http-webhook-trigger"></a>Lägg till en HTTP-webhook-utlösare

Den här inbyggda utlösaren registrerar en callback-URL med den angivna tjänsten och väntar på att tjänsten ska skicka en HTTP POST-begäran till den URL: en. När den här händelsen inträffar, utlöses utlösaren och kör sedan den logiska appen direkt.

1. Logga in på [Azure-portalen](https://portal.azure.com). Öppna din tomma Logic-app i Logic App Designer.

1. I rutan Sök i designern anger du "http-webhook" som filter. Välj **http-webhook-** utlösaren i listan **utlösare** .

   ![Välj utlösare för HTTP-webhook](./media/connectors-native-webhook/select-http-webhook-trigger.png)

   I det här exemplet byter du ut utlösaren till HTTP webhook-utlösaren så att steget får ett mer beskrivande namn. Exemplet lägger också till en HTTP-webhook-åtgärd och båda namnen måste vara unika.

1. Ange värdena för de [Parametrar för http-webhook-utlösare](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger) som du vill använda för prenumerations-och uppsägnings samtal, till exempel:

   ![Ange Utlösar parametrarna för HTTP-webhook](./media/connectors-native-webhook/http-webhook-trigger-parameters.png)

1. Om du vill lägga till andra tillgängliga parametrar öppnar du listan **Lägg till ny parameter** och väljer de parametrar som du vill använda.

   Mer information om autentiseringstyper som är tillgängliga för HTTP-webhook finns i [autentisera HTTP-utlösare och åtgärder](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication).

1. Fortsätt att skapa ditt Logic Apps-arbetsflöde med åtgärder som körs när utlösaren utlöses.

1. Kom ihåg att spara din Logic app när du är klar. I verktygsfältet designer väljer du **Spara**.

   När du sparar din Logi Kap par anropas prenumerations slut punkten och återanrops-URL: en registreras för att utlösa denna Logic app.

1. När mål tjänsten däremot skickar en `HTTP POST`-begäran till återanrops-URL: en, utlöses Logic Apps och innehåller alla data som skickas via begäran.

## <a name="add-an-http-webhook-action"></a>Lägg till en HTTP webhook-åtgärd

Den här inbyggda åtgärden registrerar en callback-URL med den angivna tjänsten, pausar Logic app-arbetsflöde och väntar på att tjänsten ska skicka en HTTP POST-begäran till den URL: en. När den här händelsen inträffar fortsätter åtgärden att köra Logic-appen.

1. Logga in på [Azure-portalen](https://portal.azure.com). Öppna din Logic app i Logic App Designer.

   I det här exemplet används HTTP webhook-utlösaren som det första steget.

1. Under steget där du vill lägga till åtgärden HTTP-webhook väljer du **nytt steg**.

   Om du vill lägga till en åtgärd mellan stegen flyttar du pekaren över pilen mellan stegen. Välj plus tecknet ( **+** ) som visas och välj sedan **Lägg till en åtgärd**.

1. I rutan Sök i designern anger du "http-webhook" som filter. I listan **åtgärder** väljer du åtgärden **http-webhook** .

   ![Välj åtgärden HTTP-webhook](./media/connectors-native-webhook/select-http-webhook-action.png)

   Det här exemplet byter namn på åtgärden till "HTTP-webhook-åtgärd", så att steget har ett mer beskrivande namn.

1. Ange värdena för HTTP webhook-åtgärdens parametrar, som liknar de parametrar för [http webhook-utlösare](../logic-apps/logic-apps-workflow-actions-triggers.md##http-webhook-trigger) som du vill använda för prenumerations-och uppsägnings samtal, till exempel:

   ![Ange åtgärds parametrar för HTTP-webhook](./media/connectors-native-webhook/http-webhook-action-parameters.png)

   Under körningen anropar Logic-appen prenumerations slut punkten när den här åtgärden körs. Din Logic app pausar sedan arbets flödet och väntar på att mål tjänsten ska skicka en `HTTP POST`-begäran till återanrops-URL: en. Om åtgärden slutförs, avbryts åtgärden från slut punkten och din Logic app återupptar körningen av arbets flödet.

1. Om du vill lägga till andra tillgängliga parametrar öppnar du listan **Lägg till ny parameter** och väljer de parametrar som du vill använda.

   Mer information om autentiseringstyper som är tillgängliga för HTTP-webhook finns i [autentisera HTTP-utlösare och åtgärder](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication).

1. Kom ihåg att spara din Logic app när du är klar. I verktygsfältet designer väljer du **Spara**.

## <a name="connector-reference"></a>Referens för anslutningsapp

Mer information om utlösare och åtgärds parametrar, som liknar varandra, finns i [Parametrar för http-webhook](../logic-apps/logic-apps-workflow-actions-triggers.md##http-webhook-trigger).

### <a name="output-details"></a>Information om utdata

Här är mer information om utdata från en HTTP-webhook-utlösare eller åtgärd som returnerar denna information:

| Egenskapsnamn | Typ | Beskrivning |
|---------------|------|-------------|
| sidhuvud | objekt | Huvudena från begäran |
| brödtext | objekt | JSON-objekt | Objektet med bröd text innehållet från begäran |
| status kod | int | Status koden från begäran |
|||

| Statuskod | Beskrivning |
|-------------|-------------|
| 200 | OK |
| 202 | Accept |
| 400 | Felaktig begäran |
| 401 | Behörighet saknas |
| 403 | Förbjudet |
| 404 | Hittades inte |
| 500 | Internt Server fel. Ett okänt fel uppstod. |
|||

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps anslutningar](../connectors/apis-list.md)
