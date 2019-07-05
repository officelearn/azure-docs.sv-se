---
title: Skapa händelsebaserade aktiviteter och arbetsflöden i Azure Logic Apps
description: Utlösa, pausa och återuppta automatiserade uppgifter, processer och arbetsflöden baserat på händelser som sker på en slutpunkt med hjälp av Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/05/2019
tags: connectors
ms.openlocfilehash: c2658df185d4836210c496d2c46a00a3541257a2
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2019
ms.locfileid: "67541360"
---
# <a name="automate-event-based-tasks-and-workflows-by-using-http-webhooks-in-azure-logic-apps"></a>Automatisera händelsebaserade aktiviteter och arbetsflöden med hjälp av HTTP-webhookar i Azure Logic Apps

Med [Azure Logic Apps](../logic-apps/logic-apps-overview.md) och den inbyggda HTTP-Webhook-anslutningen kan du automatisera arbetsflöden som väntar och körs baserat på specifika händelser som sker på en HTTP- eller HTTPS-slutpunkt genom att skapa logikappar. Du kan till exempel skapa en logikapp som övervakar en tjänstslutpunkt genom att vänta tills en specifik händelse innan du utlöser arbetsflödet och kör de angivna åtgärderna, snarare än regelbundet kontrollerar eller *avsökning* att slutpunkten.

Här följer några exempel händelsebaserad arbetsflöden:

* Vänta tills ett objekt tas emot från en [Azure Event Hub](https://github.com/logicappsio/EventHubAPI) innan du utlöser en logikappskörning.
* Vänta tills ett godkännande innan du fortsätter ett arbetsflöde.

## <a name="how-do-webhooks-work"></a>Hur fungerar webhooks?

En HTTP-webhook-utlösare är händelsebaserad, som inte är beroende kontrollerar eller söker regelbundet efter nya objekt. När du sparar en logikapp som börjar med en webhooksutlösare eller när du ändrar din logikapp från inaktiverat aktiverat, webhook-utlösaren *prenumererar* till en viss tjänst eller en slutpunkt genom att registrera en *Motringnings-URL för* med tjänsten eller slutpunkt. Utlösaren väntar sedan den tjänst eller en slutpunkt för att anropa den URL som startar logikappen. Liknar den [begäransutlösare](connectors-native-reqres.md), logikappen utlöses omedelbart när den angivna händelsen inträffar. Utlösaren *avbrutna prenumerationer* från tjänsten eller slutpunkten om du tar bort utlösaren och spara din logikapp, eller när du ändrar din logikapp från aktiverad till inaktiverad.

En HTTP-webhook-åtgärd är också händelsebaserad och *prenumererar* till en viss tjänst eller en slutpunkt genom att registrera en *Motringnings-URL för* med tjänsten eller slutpunkt. Webhook-åtgärd pausar den logic app-arbetsflöde och väntar tills tjänsten eller slutpunkten anrop URL innan logic app-återupptar som körs. Åtgärden logikappen *avbrutna prenumerationer* från tjänsten eller slutpunkten i dessa fall:

* Då webhook-åtgärden slutförs
* Om logikappen att köra avbryts under väntan på svar
* Innan logiken tidsgränsen app uppnås

Till exempel Office 365 Outlook kopplingens [ **skicka e-postgodkännande** ](connectors-create-api-office365-outlook.md) åtgärd är ett exempel på webhook-åtgärder som följer detta mönster. Du kan utöka det här mönstret till valfri tjänst med hjälp av webhook-åtgärd.

Mer information finns i följande avsnitt:

* [Parametrar för HTTP-Webhook-utlösare](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)
* [Webhooks och prenumerationer](../logic-apps/logic-apps-workflow-actions-triggers.md#webhooks-and-subscriptions)
* [Skapa anpassade API: er som har stöd för en webhook](../logic-apps/logic-apps-create-api-app.md)

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* URL-Adressen för en redan distribuerad slutpunkt eller API som stöder webhooken prenumerera och avsluta prenumerationen mönster för [webhook-utlösare i logikappar](../logic-apps/logic-apps-create-api-app.md#webhook-triggers) eller [webhook-åtgärder i logic apps](../logic-apps/logic-apps-create-api-app.md#webhook-actions) efter behov

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md). Om du är nybörjare till logic apps, granska [vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

* Logikappen där du vill vänta tills specifika händelser i mål-slutpunkten. Du kommer igång med HTTP-Webhook-utlösaren [skapa en tom logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md). Starta din logikapp med en utlösare som du vill om du vill använda HTTP-Webhook-åtgärd. Det här exemplet använder HTTP-utlösaren som det första steget.

## <a name="add-an-http-webhook-trigger"></a>Lägg till en HTTP-Webhook-utlösare

Den här inbyggda utlösaren registrerar en Motringnings-URL med den angivna tjänsten och väntar på att tjänsten för att skicka en HTTP POST-begäran till URL: en. När detta sker utlösaren utlöses och omedelbart kör logikappen.

1. Logga in på [Azure Portal](https://portal.azure.com). Öppna din tom logikapp i Logic App Designer.

1. På designern i sökrutan anger du ”http-webhook” som filter. Från den **utlösare** väljer den **HTTP-Webhook** utlösaren.

   ![Välj HTTP-Webhook-utlösare](./media/connectors-native-webhook/select-http-webhook-trigger.png)

   Det här exemplet byter namn på utlösare till ”HTTP-Webhook-utlösaren” så att steget innehåller ett mer beskrivande namn. Dessutom exemplet lägger till senare en HTTP-Webhook-åtgärd, och båda måste vara unika.

1. Ange värden för den [parametrar för HTTP-Webhook-utlösaren](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger) att du vill använda för prenumerera och avsluta prenumerationen anrop, till exempel:

   ![Ange parametrar för HTTP-Webhook-utlösare](./media/connectors-native-webhook/http-webhook-trigger-parameters.png)

1. Om du vill lägga till andra tillgängliga parametrar, öppna den **Lägg till ny parameter** och välj de parametrar som du vill.

   Läs mer om typer av autentisering som är tillgängliga för HTTP-Webhook, [autentisera HTTP-utlösare och åtgärder](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication).

1. Fortsätt att skapa din logikapp arbetsflöde med åtgärder som körs när den utlöses.

1. När du är klar, gjort, Kom ihåg att spara din logikapp. På verktygsfältet för appdesignern väljer **spara**.

   Sparar logikappen anropar slutpunkten prenumerera och registrerar Motringnings-URL för att utlösa den här logikappen.

1. Nu när Måltjänsten skickar en `HTTP POST` begäran till Webbadressen för återanrop logic app utlöses, och innehåller alla data som skickas med begäran.

## <a name="add-an-http-webhook-action"></a>Lägg till en HTTP-Webhook-åtgärd

Den här inbyggda åtgärden registrerar en Motringnings-URL med den angivna tjänsten, pausar den logic app-arbetsflöde och väntar på att tjänsten för att skicka en HTTP POST-begäran till URL: en. När detta sker fortsätter åtgärden som kör logikappen.

1. Logga in på [Azure Portal](https://portal.azure.com). Öppna logikappen i Logic App Designer.

   Det här exemplet använder HTTP-Webhook-utlösaren som det första steget.

1. Under det steget där du vill lägga till HTTP-Webhook-åtgärd, väljer **nytt steg**.

   Om du vill lägga till en åtgärd mellan stegen, flyttar du pekaren över pilen mellan stegen. Klicka på plustecknet ( **+** ) som visas och välj sedan **Lägg till en åtgärd**.

1. På designern i sökrutan anger du ”http-webhook” som filter. Från den **åtgärder** väljer den **HTTP-Webhook** åtgärd.

   ![Välj HTTP-Webhook-åtgärd](./media/connectors-native-webhook/select-http-webhook-action.png)

   Det här exemplet byter namn på åtgärden som ska ”HTTP-Webhook-åtgärd” så att steget innehåller ett mer beskrivande namn.

1. Ange värden för HTTP-Webhook åtgärdsparametrar som liknar den [parametrar för HTTP-Webhook-utlösaren](../logic-apps/logic-apps-workflow-actions-triggers.md##http-webhook-trigger) att du vill använda för prenumerera och avsluta prenumerationen anrop, till exempel:

   ![Ange åtgärdsparametrar för HTTP-Webhook](./media/connectors-native-webhook/http-webhook-action-parameters.png)

   Under körning anropar logikappen prenumerera slutpunkt när du kör den här åtgärden. Logikappen sedan pausas arbetsflödet och väntar tills Måltjänsten att skicka en `HTTP POST` begäran om att Motringnings-URL. Om åtgärden slutförs åtgärden Avbryt prenumerationen på slutpunkten och logikappen återupptar kör arbetsflödet.

1. Om du vill lägga till andra tillgängliga parametrar, öppna den **Lägg till ny parameter** och välj de parametrar som du vill.

   Läs mer om typer av autentisering som är tillgängliga för HTTP-Webhook, [autentisera HTTP-utlösare och åtgärder](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication).

1. När du är klar, Kom ihåg att spara din logikapp. På verktygsfältet för appdesignern väljer **spara**.

## <a name="connector-reference"></a>Referens för anslutningsapp

Mer information om utlösare och åtgärd parametrar som liknar varandra, finns i [HTTP-Webhook-parametrarna](../logic-apps/logic-apps-workflow-actions-triggers.md##http-webhook-trigger).

### <a name="output-details"></a>Utdatainformation

Här finns mer information om utdata från en HTTP-Webhook-utlösare eller åtgärd som returnerar den här informationen:

| Egenskapsnamn | Typ | Beskrivning |
|---------------|------|-------------|
| Rubriker | object | Rubriker i begäran |
| body | object | JSON-objekt | Objektet med brödtext i begäran |
| Statuskod | int | Statuskoden i begäran |
|||

| Statuskod | Beskrivning |
|-------------|-------------|
| 200 | Ok |
| 202 | Accepterat |
| 400 | Felaktig förfrågan |
| 401 | Behörighet saknas |
| 403 | Förbjudna |
| 404 | Det gick inte att hitta |
| 500 | Internt serverfel. Okänt fel uppstod. |
|||

## <a name="next-steps"></a>Nästa steg

* Läs mer om andra [Logic Apps-anslutningsprogram](../connectors/apis-list.md)
