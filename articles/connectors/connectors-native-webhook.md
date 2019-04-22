---
title: Skapa händelse-baserade arbetsflöden eller åtgärder – Azure Logic Apps | Microsoft Docs
description: Automatisera händelsebaserad arbetsflöden eller åtgärder med webhooks och Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, jehollan, LADocs
ms.assetid: 71775384-6c3a-482c-a484-6624cbe4fcc7
ms.topic: article
tags: connectors
ms.date: 07/21/2016
ms.openlocfilehash: c3047000843e054e71ec1a80313118a25e7c4905
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "58895587"
---
# <a name="create-event-based-workflows-or-actions-by-using-webhooks-and-azure-logic-apps"></a>Skapa händelse-baserade arbetsflöden eller åtgärder med webhooks och Azure Logic Apps

Med webhook-åtgärd och utlösare kan du starta, pausa och återuppta flöden för att utföra dessa uppgifter:

* Utlösa från en [Azure Event Hub](https://github.com/logicappsio/EventHubAPI) när ett objekt tas emot
* Vänta tills ett godkännande innan du fortsätter ett arbetsflöde

Läs mer om [hur du skapar anpassade API: er som har stöd för en webhook](../logic-apps/logic-apps-create-api-app.md).

## <a name="use-the-webhook-trigger"></a>Använd webhook-utlösaren

En [ *utlösaren* ](../connectors/apis-list.md) är en händelse som startar en logikappens arbetsflöde. Webhook-utlösaren är händelsebaserad, som inte beror på avsökningen för nya objekt. När du sparar din logikapp med en webhooksutlösare eller när du ändrar din logikapp från inaktiverat aktiverat, webhook-utlösaren *prenumererar* till den angivna tjänsten eller slutpunkten genom att registrera en *Motringnings-URL för* med tjänsten eller slutpunkt. Utlösaren använder sedan den URL: en för att köra logikappen efter behov. Som den [begäransutlösare](connectors-native-reqres.md), logikappen utlöses omedelbart när den förväntade händelsen inträffar. Utlösaren *avbrutna prenumerationer* om du tar bort utlösaren och spara din logikapp, eller när du ändrar din logikapp från aktiverad till inaktiverad.

Här är ett exempel som visar hur du ställer in en HTTP-utlösare i Logic App Designer. Anvisningarna förutsätter att du redan har distribuerat eller har åtkomst till ett API som följer den [webhook prenumerera och avsluta prenumerationen mönster i logic apps](../logic-apps/logic-apps-create-api-app.md#webhook-triggers). 

**Att lägga till webhook-utlösaren**

1. Lägg till den **HTTP-Webhook** utlösaren som det första steget i en logikapp.
2. Fyll i parametrarna för webhooken prenumerera och avsluta prenumerationen anrop.

   Det här steget följer samma mönster som den [HTTP-åtgärd](connectors-native-http.md) format.

     ![HTTP-utlösare](./media/connectors-native-webhook/using-trigger.png)

3. Lägg till minst en åtgärd.
4. Klicka på **spara** att publicera logikappen. Det här steget anropar slutpunkten prenumerera med Motringnings-URL som behövs för att utlösa den här logikappen.
5. När tjänsten blir en `HTTP POST` till Webbadressen för återanrop logikappen utlöses och innehåller alla data som skickas till begäran.

## <a name="use-the-webhook-action"></a>Använd webhook-åtgärd

En [ *åtgärd* ](../connectors/apis-list.md) är en åtgärd som definieras och körning av logikappens arbetsflöde. När en logikapp körs en webhook-åtgärd, åtgärden *prenumererar* till den angivna tjänsten eller slutpunkten genom att registrera en *Motringnings-URL för* med tjänsten eller slutpunkt. Webhook-åtgärd och väntar tills som tjänsten anrop URL innan logic app-återupptar körs. Logikappen Avbryt prenumerationen på tjänsten eller slutpunkten i dessa fall: 

* Då webhook-åtgärden slutförs
* Om logikappen att köra avbryts under väntan på svar
* Innan logiken tidsgränsen app uppnås

Till exempel den [ **skicka e-postgodkännande** ](connectors-create-api-office365-outlook.md) åtgärd är ett exempel på webhook-åtgärder som följer detta mönster. Du kan utöka det här mönstret till valfri tjänst med webhook-åtgärd. 

Här är ett exempel som visar hur du ställer in en webhook-åtgärd i Logic App Designer. Dessa steg förutsätter att du redan har distribuerat eller har åtkomst till ett API som följer den [webhook prenumerera och avsluta prenumerationen mönster som används i logic apps](../logic-apps/logic-apps-create-api-app.md#webhook-actions). 

**Att lägga till en webhook-åtgärd**

1. Välj **nytt steg** > **Lägg till en åtgärd**.

2. I sökrutan skriver du ”webhook” för att hitta den **HTTP-Webhook** åtgärd.

    ![Välj frågeåtgärden](./media/connectors-native-webhook/using-action-1.png)

3. Fyll i parametrarna för webhooken prenumerera och avsluta prenumerationen anrop

   Det här steget följer samma mönster som den [HTTP-åtgärd](connectors-native-http.md) format.

     ![Fullständig frågeåtgärden](./media/connectors-native-webhook/using-action-2.png)
   
   Vid körning anropar logikappen prenumerera slutpunkten efter att ha uppnått det steget.

4. Klicka på **spara** att publicera logikappen.

## <a name="technical-details"></a>Teknisk information

Här finns mer information om utlösare och åtgärder som webhook har stöd för.

## <a name="webhook-triggers"></a>Webhook-utlösare

| Åtgärd | Beskrivning |
| --- | --- |
| HTTP-webhook |Prenumerera på en Motringnings-URL till en tjänst som kan anropa URL att utlöses logikapp efter behov. |

### <a name="trigger-details"></a>Utlösarinformation

#### <a name="http-webhook"></a>HTTP-webhook

Prenumerera på en Motringnings-URL till en tjänst som kan anropa URL att utlöses logikapp efter behov.
En * innebär obligatoriskt fält.

| Visningsnamn | Namn på egenskap | Beskrivning |
| --- | --- | --- |
| Prenumerera på metoden * |metod |HTTP-metoden ska användas för prenumerationsbegäran |
| Prenumerera på URI * |URI |HTTP-URI för prenumerationsbegäran |
| Avbryta prenumerationen metoden * |metod |HTTP-metod som ska användas för unsubscribe begäran |
| Avbryta prenumerationen URI * |URI |HTTP-URI som används för unsubscribe begäran |
| Prenumerera på brödtext |brödtext |HTTP-begärandetexten för prenumerera |
| Prenumerera på rubriker |Rubriker |HTTP-begärans sidhuvud för prenumerera |
| Prenumerera på autentisering |autentisering |HTTP-autentisering ska användas för prenumerera. [Se HTTP-anslutningsappen](connectors-native-http.md#authentication) information |
| Avbryta prenumerationen brödtext |brödtext |HTTP-begärandetexten för unsubscribe |
| Avbryta prenumerationen rubriker |Rubriker |HTTP-begärans sidhuvud för unsubscribe |
| Avbryta prenumerationen autentisering |autentisering |HTTP-autentisering ska användas för unsubscribe. [Se HTTP-anslutningsappen](connectors-native-http.md#authentication) information |

**Utdatainformation**

Webhook-begäran

| Namn på egenskap | Datatyp | Beskrivning |
| --- | --- | --- |
| Huvuden |objekt |Webhook-begärandehuvuden |
| Brödtext |objekt |Webhook-begäran-objekt |
| Statuskod |int |Statuskod för Webhook-begäran |

## <a name="webhook-actions"></a>Webhook-åtgärder

| Åtgärd | Beskrivning |
| --- | --- |
| HTTP-webhook |Prenumerera på en Motringnings-URL till en tjänst som kan anropa URL: en om du vill återuppta ett arbetsflödessteg efter behov. |

### <a name="action-details"></a>Åtgärdsinformation

#### <a name="http-webhook"></a>HTTP-webhook

Prenumerera på en Motringnings-URL till en tjänst som kan anropa URL: en om du vill återuppta ett arbetsflödessteg efter behov.
En * innebär obligatoriskt fält.

| Visningsnamn | Namn på egenskap | Beskrivning |
| --- | --- | --- |
| Prenumerera på metoden * |metod |HTTP-metoden ska användas för prenumerationsbegäran |
| Prenumerera på URI * |URI |HTTP-URI för prenumerationsbegäran |
| Avbryta prenumerationen metoden * |metod |HTTP-metod som ska användas för unsubscribe begäran |
| Avbryta prenumerationen URI * |URI |HTTP-URI som används för unsubscribe begäran |
| Prenumerera på brödtext |brödtext |HTTP-begärandetexten för prenumerera |
| Prenumerera på rubriker |Rubriker |HTTP-begärans sidhuvud för prenumerera |
| Prenumerera på autentisering |autentisering |HTTP-autentisering ska användas för prenumerera. [Se HTTP-anslutningsappen](connectors-native-http.md#authentication) information |
| Avbryta prenumerationen brödtext |brödtext |HTTP-begärandetexten för unsubscribe |
| Avbryta prenumerationen rubriker |Rubriker |HTTP-begärans sidhuvud för unsubscribe |
| Avbryta prenumerationen autentisering |autentisering |HTTP-autentisering ska användas för unsubscribe. [Se HTTP-anslutningsappen](connectors-native-http.md#authentication) information |

**Utdatainformation**

Webhook-begäran

| Namn på egenskap | Datatyp | Beskrivning |
| --- | --- | --- |
| Huvuden |objekt |Webhook-begärandehuvuden |
| Brödtext |objekt |Webhook-begäran-objekt |
| Statuskod |int |Statuskod för Webhook-begäran |

## <a name="next-steps"></a>Nästa steg

* [Skapa en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Sök efter andra anslutningar](apis-list.md)
