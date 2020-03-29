---
title: Vänta och svara på händelser
description: Automatisera arbetsflöden som utlöser, pausar och återupptar baserat på händelser vid en tjänstslutpunkt med hjälp av Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/06/2020
tags: connectors
ms.openlocfilehash: 1578ca030bc8bab971a44e1afcce1d1ab9e1d5e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78674096"
---
# <a name="create-and-run-automated-event-based-workflows-by-using-http-webhooks-in-azure-logic-apps"></a>Skapa och kör automatiserade händelsebaserade arbetsflöden med hjälp av HTTP-webbkrokar i Azure Logic Apps

Med [Azure Logic Apps](../logic-apps/logic-apps-overview.md) och den inbyggda HTTP Webhook-anslutningen kan du automatisera arbetsflöden som väntar och körs baserat på specifika händelser som inträffar vid en HTTP- eller HTTPS-slutpunkt genom att skapa logikappar. Du kan till exempel skapa en logikapp som övervakar en tjänstslutpunkt genom att vänta på en viss händelse innan du utlöser arbetsflödet och kör de angivna åtgärderna, i stället för att regelbundet kontrollera eller *avsöka* slutpunkten.

Här är några exempel på händelsebaserade arbetsflöden:

* Vänta tills ett objekt anländer från en [Azure Event Hub](https://github.com/logicappsio/EventHubAPI) innan du utlöser en logikappkörning.
* Vänta på ett godkännande innan du fortsätter ett arbetsflöde.

## <a name="how-do-webhooks-work"></a>Hur fungerar webhooks?

En HTTP webhook-utlösare är händelsebaserad, vilket inte är beroende av att regelbundet kontrollera eller avsöka nya objekt. När du sparar en logikapp som börjar med en webhook-utlösare, eller när du ändrar logikappen från inaktiverad till aktiverad, *prenumererar* webhook-utlösaren på en viss tjänst eller slutpunkt genom att registrera en *motringnings-URL* med den tjänsten eller slutpunkten. Utlösaren väntar sedan på att tjänsten eller slutpunkten ska anropa URL:en, som börjar köra logikappen. I likhet [med utlösaren Begäran](connectors-native-reqres.md)utlöses logikappen omedelbart när den angivna händelsen inträffar. Utlösaren *avslutar prenumerationer* från tjänsten eller slutpunkten om du tar bort utlösaren och sparar logikappen, eller när du ändrar logikappen från aktiverad till inaktiverad.

En HTTP webhook-åtgärd är också händelsebaserad och *prenumererar på* en viss tjänst eller slutpunkt genom att registrera en *motringnings-URL* med den tjänsten eller slutpunkten. Webhook-åtgärden pausar logikappens arbetsflöde och väntar tills tjänsten eller slutpunkten anropar URL:en innan logikappen fortsätter att köras. Åtgärdslogiken *avregistrerar prenumerationer* från tjänsten eller slutpunkten i dessa fall:

* När webhook-åtgärden har slutförts
* Om logikappkörningen avbryts i väntan på ett svar
* Innan logikappen time out

Office 365 Outlook-anslutningsappens [**e-poståtgärd Skicka godkännande**](connectors-create-api-office365-outlook.md) är till exempel ett exempel på webhook-åtgärd som följer det här mönstret. Du kan utöka det här mönstret till valfri tjänst med hjälp av webhook-åtgärden.

> [!NOTE]
> Logic Apps tillämpar TLS (Transport Layer Security) 1.2 när du tar emot samtalet tillbaka till HTTP webhook-utlösaren eller åtgärden. Om du ser SSL-handskakningsfel kontrollerar du att du använder TLS 1.2. För inkommande samtal, här är de chiffersviter som stöds:
>
> * TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
> * TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
> * TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
> * TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
> * TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
> * TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
> * TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
> * TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256

Mer information finns i de här ämnena:

* [HTTP Webhook-utlösarparametrar](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)
* [Webhooks och prenumerationer](../logic-apps/logic-apps-workflow-actions-triggers.md#webhooks-and-subscriptions)
* [Skapa anpassade API:er som stöder en webhook](../logic-apps/logic-apps-create-api-app.md)

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* URL:en för en redan distribuerad slutpunkt eller ett API som stöder webhook-prenumerationsmönstret för [webhook-utlösare i logikappar](../logic-apps/logic-apps-create-api-app.md#webhook-triggers) eller [webhook-åtgärder i logikappar](../logic-apps/logic-apps-create-api-app.md#webhook-actions) efter behov

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md). Om du inte har tidigare i logikappar läser du [Vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

* Logikappen där du vill vänta på specifika händelser vid målslutpunkten. Om du vill börja med HTTP Webhook-utlösaren [skapar du en tom logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md). Om du vill använda HTTP Webhook-åtgärden startar du logikappen med valfri utlösare. I det här exemplet används HTTP-utlösaren som det första steget.

## <a name="add-an-http-webhook-trigger"></a>Lägga till en HTTP Webhook-utlösare

Den här inbyggda utlösaren anropar slutpunkten för teckning på måltjänsten och registrerar en motringnings-URL med måltjänsten. Logikappen väntar sedan på att måltjänsten ska skicka en `HTTP POST` begäran till motringnings-URL:en. När den här händelsen inträffar utlöses utlösaren och skickar data i begäran till arbetsflödet.

1. Logga in på [Azure-portalen](https://portal.azure.com). Öppna din tomma logikapp i Logic App Designer.

1. Ange `http webhook` som filter i designerns sökruta. Välj **HTTP Webhook-utlösaren** i listan **Utlösare.**

   ![Välj HTTP Webhook-utlösare](./media/connectors-native-webhook/select-http-webhook-trigger.png)

   I det här exemplet `HTTP Webhook trigger` byter du namn på utlösaren så att steget får ett mer beskrivande namn. Dessutom lägger exemplet senare till en HTTP Webhook-åtgärd och båda namnen måste vara unika.

1. Ange värdena för [de HTTP Webhook-utlösarparametrar](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger) som du vill använda för prenumerations- och avregistreringssamtalen.

   I det här exemplet innehåller utlösaren de metoder, URI:er och meddelandekroppar som ska användas när du utför prenumerations- och avregistreringsåtgärderna.

   ![Ange http webhook-utlösarparametrar](./media/connectors-native-webhook/http-webhook-trigger-parameters.png)

   | Egenskap | Krävs | Beskrivning |
   |----------|----------|-------------|
   | **Prenumeration - Metod** | Ja | Den metod som ska användas när du prenumererar på målslutpunkten |
   | **Prenumerera - URI** | Ja | URL:en som ska användas för att prenumerera på målslutpunkten |
   | **Prenumerera - Kropp** | Inga | Alla meddelandetexter som ska inkluderas i prenumerationsbegäran. Det här exemplet innehåller den motringnings-URL som unikt identifierar prenumeranten, som är din logikapp, genom att använda uttrycket `@listCallbackUrl()` för att hämta logikappens motringnings-URL. |
   | **Avsluta prenumerationen - metod** | Inga | Den metod som ska användas vid avskrivning från målslutpunkten |
   | **Avsluta prenumerationen - URI** | Inga | URL:en som ska användas för att avregistrera sig från målslutpunkten |
   | **Avsluta prenumerationen - Kropp** | Inga | En valfri meddelandetext som ska inkluderas i begäran om att avsluta prenumerationen <p><p>**Den**här egenskapen stöder inte `listCallbackUrl()` användning av funktionen. Utlösaren inkluderar och skickar dock automatiskt `x-ms-client-tracking-id` rubrikerna och `x-ms-workflow-operation-name`, som måltjänsten kan använda för att unikt identifiera prenumeranten. |
   ||||

1. Om du vill lägga till andra utlösaregenskaper öppnar du listan **Lägg till ny parameter.**

   ![Lägga till fler utlösaregenskaper](./media/connectors-native-webhook/http-webhook-trigger-add-properties.png)

   Om du till exempel behöver använda autentisering kan du lägga till egenskaperna **Prenumerera - Autentisering** och **Avsluta prenumeration - Autentisering.** Mer information om tillgängliga autentiseringstyper för HTTP Webhook finns i [Lägga till autentisering i utgående samtal](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

1. Fortsätt att skapa logikappens arbetsflöde med åtgärder som körs när utlösaren utlöses.

1. När du är klar, klar, kom ihåg att spara logikappen. Välj **Spara**i designerverktygsfältet .

   Om du sparar logikappen anropas slutpunkten för prenumerationen på måltjänsten och motringnings-URL:en registreras. Logikappen väntar sedan på att måltjänsten ska skicka en `HTTP POST` begäran till motringnings-URL:en. När den här händelsen inträffar utlöses utlösaren och skickar data i begäran till arbetsflödet. Om den här åtgärden har slutförts fortsätter utlösaren avregistreringarna från slutpunkten och logikappen fortsätter det återstående arbetsflödet.

## <a name="add-an-http-webhook-action"></a>Lägga till en HTTP Webhook-åtgärd

Den här inbyggda åtgärden anropar slutpunkten för teckning på måltjänsten och registrerar en motringnings-URL med måltjänsten. Logikappen pausar och väntar på att `HTTP POST` måltjänsten ska skicka en begäran till motringnings-URL:en. När den här händelsen inträffar skickar åtgärden alla data i begäran tillsammans med arbetsflödet. Om åtgärden har slutförts slutförs åtgärden, åtgärden avsluta prenumerationer från slutpunkten och din logikapp fortsätter att köra det återstående arbetsflödet.

1. Logga in på [Azure-portalen](https://portal.azure.com). Öppna logikappen i Logic App Designer.

   I det här exemplet används HTTP Webhook-utlösaren som det första steget.

1. Välj **Nytt steg**under det steg där du vill lägga till HTTP Webhook-åtgärden .

   Om du vill lägga till en åtgärd mellan stegen flyttar du pekaren över pilen mellan stegen. Markera plustecknet**+**( ) som visas och välj sedan **Lägg till en åtgärd**.

1. Ange `http webhook` som filter i designerns sökruta. Välj HTTP **Webhook-åtgärden** i listan **Åtgärder.**

   ![Välj HTTP Webhook-åtgärd](./media/connectors-native-webhook/select-http-webhook-action.png)

   I det här exemplet byter du namn på åtgärden till "HTTP Webhook-åtgärd" så att steget har ett mer beskrivande namn.

1. Ange värdena för HTTP Webhook-åtgärdsparametrarna, som liknar [http Webhook-utlösarparametrarna](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger), som du vill använda för prenumerations- och avregistreringssamtalen.

   I det här exemplet innehåller åtgärden de metoder, URI:er och meddelandekroppar som ska användas när du utför prenumerations- och avregistreringsåtgärderna.

   ![Ange http webhook-åtgärdsparametrar](./media/connectors-native-webhook/http-webhook-action-parameters.png)

   | Egenskap | Krävs | Beskrivning |
   |----------|----------|-------------|
   | **Prenumeration - Metod** | Ja | Den metod som ska användas när du prenumererar på målslutpunkten |
   | **Prenumerera - URI** | Ja | URL:en som ska användas för att prenumerera på målslutpunkten |
   | **Prenumerera - Kropp** | Inga | Alla meddelandetexter som ska inkluderas i prenumerationsbegäran. Det här exemplet innehåller den motringnings-URL som unikt identifierar prenumeranten, som är din logikapp, genom att använda uttrycket `@listCallbackUrl()` för att hämta logikappens motringnings-URL. |
   | **Avsluta prenumerationen - metod** | Inga | Den metod som ska användas vid avskrivning från målslutpunkten |
   | **Avsluta prenumerationen - URI** | Inga | URL:en som ska användas för att avregistrera sig från målslutpunkten |
   | **Avsluta prenumerationen - Kropp** | Inga | En valfri meddelandetext som ska inkluderas i begäran om att avsluta prenumerationen <p><p>**Den**här egenskapen stöder inte `listCallbackUrl()` användning av funktionen. Åtgärden innehåller dock automatiskt och skickar `x-ms-client-tracking-id` rubrikerna `x-ms-workflow-operation-name`och , som måltjänsten kan använda för att unikt identifiera prenumeranten. |
   ||||

1. Om du vill lägga till andra åtgärdsegenskaper öppnar du listan **Lägg till ny parameter.**

   ![Lägga till fler åtgärdsegenskaper](./media/connectors-native-webhook/http-webhook-action-add-properties.png)

   Om du till exempel behöver använda autentisering kan du lägga till egenskaperna **Prenumerera - Autentisering** och **Avsluta prenumeration - Autentisering.** Mer information om tillgängliga autentiseringstyper för HTTP Webhook finns i [Lägga till autentisering i utgående samtal](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

1. När du är klar ska du komma ihåg att spara logikappen. Välj **Spara**i designerverktygsfältet .

   När den här åtgärden körs anropar logikappen slutpunkten för prenumerationen på måltjänsten och registrerar motringningsadressen. Logikappen pausar sedan arbetsflödet och väntar på `HTTP POST` att måltjänsten ska skicka en begäran till motringnings-URL:en. När den här händelsen inträffar skickar åtgärden alla data i begäran tillsammans med arbetsflödet. Om åtgärden har slutförts slutförs åtgärden, åtgärden avsluta prenumerationer från slutpunkten och din logikapp fortsätter att köra det återstående arbetsflödet.

## <a name="connector-reference"></a>Referens för anslutningsapp

Mer information om utlösar- och åtgärdsparametrar, som liknar varandra, finns i [HTTP Webhook-parametrar](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger).

### <a name="output-details"></a>Information om utdata

Här finns mer information om utdata från en HTTP Webhook-utlösare eller -åtgärd som returnerar den här informationen:

| Egenskapsnamn | Typ | Beskrivning |
|---------------|------|-------------|
| Headers | objekt | Rubrikerna från begäran |
| body | objekt | JSON-objekt | Objektet med brödtextinnehållet från begäran |
| statuskod | int | Statuskoden från begäran |
|||

| Statuskod | Beskrivning |
|-------------|-------------|
| 200 | OK |
| 202 | Accepterad |
| 400 | Felaktig begäran |
| 401 | Behörighet saknas |
| 403 | Förbjudet |
| 404 | Hittades inte |
| 500 | Internt serverfel. Okänt fel uppstod. |
|||

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps-kopplingar](../connectors/apis-list.md)
