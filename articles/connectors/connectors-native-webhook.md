---
title: "Webhook-koppling för Logikappar i Azure | Microsoft Docs"
description: "Hur du använder webhook-åtgärder och utlösare för att utföra åtgärder som Filter matris från logikappar"
services: logic-apps
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
tags: connectors
ms.assetid: 71775384-6c3a-482c-a484-6624cbe4fcc7
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/21/2016
ms.author: jehollan; LADocs
ms.openlocfilehash: fbfef291334109c6dcfcde80741874549fb7929f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-the-webhook-connector"></a>Kom igång med webhook-koppling

Med webhook åtgärd och utlösare kan du starta, pausa och återuppta flöden för att utföra dessa uppgifter:

* Starta från en [Azure Event Hub](https://github.com/logicappsio/EventHubAPI) när ett objekt tas emot
* Vänta på ett godkännande innan du fortsätter ett arbetsflöde

Lär dig mer om [hur du skapar anpassade API: er som stöder en webhook](../logic-apps/logic-apps-create-api-app.md).

## <a name="use-the-webhook-trigger"></a>Använda webhook-utlösare

En [ *utlösaren* ](connectors-overview.md) är en händelse som startar ett arbetsflöde för logik-app. En webhook-utlösare är händelsebaserat och förlitar sig inte på avsökning för nya objekt. Som det [begäran utlösaren](connectors-native-reqres.md), logikappen utlöses det ögonblick som en händelse inträffar. Webhook-utlösaren registrerar en *motringning URL* för en tjänst och använder den URL eller logikappen som behövs.

Här är ett exempel som visar hur du ställer in en HTTP-utlösare i logik App Designer. Anvisningarna förutsätter att du redan har distribuerat eller använder ett API som följer den [webhook prenumerera och avbryta prenumerationen mönster i logikappar](../logic-apps/logic-apps-create-api-app.md#webhook-triggers). Prenumerera anrop görs när en logikapp sparas med en ny webhook eller växlade från inaktivt till aktivt. Unsubscribe-anrop görs när en webhook apputlösare logik tas bort och spara eller växlade från aktiverad till inaktiverad.

**Att lägga till webhook-utlösare**

1. Lägg till den **HTTP Webhook** utlösare som det första steget i en logikapp.
2. Fyll i parametrarna för webhooken prenumerera och avbryta prenumerationen anrop.

   Det här steget följer samma mönster som den [HTTP-åtgärd](connectors-native-http.md) format.

     ![HTTP-utlösare](./media/connectors-native-webhook/using-trigger.png)

3. Lägg till minst en åtgärd.
4. Klicka på **spara** att publicera logikappen. Det här steget anropar prenumerera slutpunkten med återanrop URL krävs för att utlösa den här logikapp.
5. När tjänsten gör en `HTTP POST` till återanrop URL, logikappen utlöses och innehåller alla data som skickades i begäran.

## <a name="use-the-webhook-action"></a>Använd åtgärden webhooken

En [ *åtgärd* ](connectors-overview.md) är en åtgärd som utförs av arbetsflödet som definierats i en logikapp. En webhook-åtgärden registrerar en *motringning URL* med en tjänst och väntar tills URL: en anropas innan du fortsätter. Den [”Skicka godkännande e-post”](connectors-create-api-office365-outlook.md) är ett exempel på en koppling som följer detta mönster. Du kan utöka det här mönstret till alla tjänster genom webhook-åtgärd. 

Här är ett exempel som visar hur du ställer in en webhook-åtgärd i logik App Designer. Dessa instruktioner förutsätter att du redan har distribuerat eller använder ett API som följer den [webhook prenumerera och avbryta prenumerationen mönster som används i logikappar](../logic-apps/logic-apps-create-api-app.md#webhook-actions). Prenumerera anrop görs när en logikapp kör webhook-åtgärden. Unsubscribe anrop görs när en körning avbryts under väntan på ett svar, innan logiken app.

**Att lägga till en webhook-åtgärd**

1. Välj **nytt steg** > **lägga till en åtgärd**.

2. I sökrutan skriver du ”webhook” för att hitta den **HTTP Webhook** åtgärd.

    ![Åtgärden Select-frågan](./media/connectors-native-webhook/using-action-1.png)

3. Fyll i parametrarna för webhooken prenumerera och avbryta prenumerationen anrop

   Det här steget följer samma mönster som den [HTTP-åtgärd](connectors-native-http.md) format.

     ![Fullständig frågeåtgärden](./media/connectors-native-webhook/using-action-2.png)
   
   Vid körning anropar logikappen prenumerera slutpunkten efter att ha uppnått steget.

4. Klicka på **spara** att publicera logikappen.

## <a name="technical-details"></a>Teknisk information

Här finns mer information om utlösare och åtgärder som webhook har stöd för.

## <a name="webhook-triggers"></a>Webhook-utlösare

| Åtgärd | Beskrivning |
| --- | --- |
| HTTP-Webhook |Prenumerera på en motringning URL till en tjänst som kan anropa Webbadressen för att utlösa logikapp efter behov. |

### <a name="trigger-details"></a>Information om utlösaren

#### <a name="http-webhook"></a>HTTP-Webhook

Prenumerera på en motringning URL till en tjänst som kan anropa Webbadressen för att utlösa logikapp efter behov.
Ett * innebär obligatoriskt fält.

| Visningsnamn | Egenskapsnamn | Beskrivning |
| --- | --- | --- |
| Prenumerera metoden * |Metoden |HTTP-metod som ska användas för prenumerera begäran |
| Prenumerera URI * |URI: N |HTTP-URI att använda för prenumerera begäran |
| Avbryta prenumerationen metoden * |Metoden |HTTP-metod som ska användas för unsubscribe begäran |
| Avbryta prenumerationen URI * |URI: N |HTTP-URI att använda för unsubscribe begäran |
| Prenumerera brödtext |Brödtext |Text för HTTP-begäran för prenumerera |
| Prenumerera rubriker |Rubriker |HTTP-huvuden för begäran för prenumerera |
| Prenumerera autentisering |Autentisering |HTTP-autentisering ska användas för att prenumerera. [HTTP-anslutningen finns](connectors-native-http.md#authentication) information |
| Avbryta prenumerationen brödtext |Brödtext |Text för HTTP-begäran för unsubscribe |
| Avbryta prenumerationen rubriker |Rubriker |HTTP-huvuden för begäran för unsubscribe |
| Avbryta prenumerationen autentisering |Autentisering |HTTP-autentisering ska användas för unsubscribe. [HTTP-anslutningen finns](connectors-native-http.md#authentication) information |

**Information för utdata**

Webhook-begäran

| Egenskapsnamn | Datatyp | Beskrivning |
| --- | --- | --- |
| Rubriker |Objektet |Webhook-huvuden för begäran |
| Innehåll |Objektet |Webhook request-objektet |
| Statuskod |int |Webhook statuskod för begäran |

## <a name="webhook-actions"></a>Webhook-åtgärder

| Åtgärd | Beskrivning |
| --- | --- |
| HTTP-Webhook |Prenumerera på en motringning URL till en tjänst som kan anropa URL Om du vill återuppta ett arbetsflödessteg efter behov. |

### <a name="action-details"></a>Åtgärdsinformation

#### <a name="http-webhook"></a>HTTP-Webhook

Prenumerera på en motringning URL till en tjänst som kan anropa URL Om du vill återuppta ett arbetsflödessteg efter behov.
Ett * innebär obligatoriskt fält.

| Visningsnamn | Egenskapsnamn | Beskrivning |
| --- | --- | --- |
| Prenumerera metoden * |Metoden |HTTP-metod som ska användas för prenumerera begäran |
| Prenumerera URI * |URI: N |HTTP-URI att använda för prenumerera begäran |
| Avbryta prenumerationen metoden * |Metoden |HTTP-metod som ska användas för unsubscribe begäran |
| Avbryta prenumerationen URI * |URI: N |HTTP-URI att använda för unsubscribe begäran |
| Prenumerera brödtext |Brödtext |Text för HTTP-begäran för prenumerera |
| Prenumerera rubriker |Rubriker |HTTP-huvuden för begäran för prenumerera |
| Prenumerera autentisering |Autentisering |HTTP-autentisering ska användas för att prenumerera. [HTTP-anslutningen finns](connectors-native-http.md#authentication) information |
| Avbryta prenumerationen brödtext |Brödtext |Text för HTTP-begäran för unsubscribe |
| Avbryta prenumerationen rubriker |Rubriker |HTTP-huvuden för begäran för unsubscribe |
| Avbryta prenumerationen autentisering |Autentisering |HTTP-autentisering ska användas för unsubscribe. [HTTP-anslutningen finns](connectors-native-http.md#authentication) information |

**Information för utdata**

Webhook-begäran

| Egenskapsnamn | Datatyp | Beskrivning |
| --- | --- | --- |
| Rubriker |Objektet |Webhook-huvuden för begäran |
| Innehåll |Objektet |Webhook request-objektet |
| Statuskod |int |Webhook statuskod för begäran |

## <a name="next-steps"></a>Nästa steg

* [Skapa en logikapp](../logic-apps/logic-apps-create-a-logic-app.md)
* [Sök efter andra kopplingar](apis-list.md)