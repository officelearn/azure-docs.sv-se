---
title: Anropa, utlösa eller kapsla logikappar
description: Konfigurera HTTP-slutpunkter för att anropa, utlösa eller kapsla Logic app-arbetsflöden i Azure Logic Apps
services: logic-apps
ms.workload: integration
ms.reviewer: klam, jehollan, logicappspm
ms.topic: article
ms.date: 11/04/2019
ms.openlocfilehash: d5b5a69c7927d07c0ae6b3b56ec97b6551e5d46b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "77191336"
---
# <a name="call-trigger-or-nest-logic-apps-by-using-http-endpoints-in-azure-logic-apps"></a>Anropa, utlösa eller kapsla Logi Kap par genom att använda HTTP-slutpunkter i Azure Logic Apps

För att din Logi Kap par ska kunna anropas via en URL så att din Logi Kap par kan ta emot inkommande begär Anden från andra tjänster, kan du på ett enhetligt sätt exponera en synkron HTTP-slutpunkt som en utlösare för den logiska appen. När du ställer in den här funktionen kan du också kapsla din Logi Kap par i andra Logic Apps, vilket gör att du kan skapa ett mönster för slut punkter som kan anropas.

Om du vill konfigurera en HTTP-slutpunkt kan du använda vilken typ av utlösare som helst som gör det möjligt för logi Kap par att ta emot inkommande begär Anden:

* [Förfrågan](../connectors/connectors-native-reqres.md)
* [HTTP-webhook](../connectors/connectors-native-webhook.md)
* Hanterade anslutnings utlösare som har [typen ApiConnectionWebhook](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger) och kan ta emot inkommande HTTP-begäranden

> [!NOTE]
> I de här exemplen används begär ande utlösare, men du kan använda alla HTTP-begäranden som är baserade på föregående lista. Alla principer som identiskt gäller för dessa typer av utlösare.

Om du inte har använt Logic Apps, se [Vad är Azure Logic Apps](../logic-apps/logic-apps-overview.md) och [snabb start: skapa din första Logic-app](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du inte har någon prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Den Logic-app där du vill konfigurera HTTP-slutpunkten som utlösaren. Du kan börja med antingen en tom Logic-app eller en befintlig Logic-app där du vill ersätta den aktuella utlösaren. Det här exemplet börjar med en tom Logic-app.

## <a name="create-a-callable-endpoint"></a>Skapa en slut punkt som går att anropa

1. Logga in på [Azure-portalen](https://portal.azure.com). Skapa och öppna en tom Logic-app i Logic App Designer.

   I det här exemplet används en begär ande utlösare, men du kan använda en utlösare som kan ta emot inkommande HTTP-begäranden. Alla principer gäller identiskt för dessa utlösare. Mer information om begär ande utlösare finns i [ta emot och svara på inkommande https-anrop med hjälp av Azure Logic Apps](../connectors/connectors-native-reqres.md).

1. Välj **inbyggd i**rutan Sök. I rutan Sök anger `request` du som filter. Välj **när en HTTP-begäran tas emot**från listan utlösare.

   ![Sök efter och välj begär ande utlösare](./media/logic-apps-http-endpoint/find-and-select-request-trigger.png)

1. Du kan också ange ett JSON-schema som beskriver den nytto last eller de data som du förväntar dig att utlösaren ska ta emot i rutan **JSON-schema för begär ande text** .

   Designern använder det här schemat för att skapa tokens som representerar utlösare. Du kan sedan enkelt referera till dessa utdata i din Logic app-arbetsflöde. Läs mer om [tokens som genereras från JSON-scheman](#generated-tokens).

   I det här exemplet anger du följande schema:

   ```json
      {
      "type": "object",
      "properties": {
         "address": {
            "type": "object",
            "properties": {
               "streetNumber": {
                  "type": "string"
               },
               "streetName": {
                  "type": "string"
               },
               "town": {
                  "type": "string"
               },
               "postalCode": {
                  "type": "string"
               }
            }
         }
      }
   }
    ```

   ![Ange JSON-schema för begär ande åtgärden](./media/logic-apps-http-endpoint/manual-request-trigger-schema.png)

   Du kan också skapa ett JSON-schema genom att ange en exempel nytto last:

   1. I utlösaren för **begäran** väljer **du Använd exempel nytto last för att generera schemat**.

   1. I rutan **Ange eller klistra in ett exempel-JSON-nyttolast** , anger du exempelets nytto Last, till exempel:

      ```json
      {
         "address": {
            "streetNumber": "00000",
            "streetName": "AnyStreet",
            "town": "AnyTown",
            "postalCode": "11111-1111"
        }
      }
      ```

   1. När du är klar väljer du **klar**.

      I rutan **begär text-JSON-schema** visas nu det genererade schemat.

1. Spara din logikapp.

   I rutan **http post till denna URL** visas nu den genererade återanrops-URL som andra tjänster kan använda för att anropa och utlösa din Logic app. Denna URL innehåller en nyckel för signatur för delad åtkomst (SAS) som används för autentisering, i frågeparametrar, till exempel:

   ![URL för återanrop har genererats för slut punkt](./media/logic-apps-http-endpoint/generated-endpoint-url.png)

   Du kan också hämta HTTP-slutpunktens URL från din Logic appss **översikts** fönster.

   1. På din Logic Apps-meny väljer du **Översikt**.

   1. I avsnittet **Sammanfattning** väljer du **Se utlösarens historik**.

      ![Hämta URL för HTTP-slutpunkt från Azure Portal](./media/logic-apps-http-endpoint/find-manual-trigger-url.png)

   1. Under **återanrops-URL [post]**, kopierar du URL: en:

      ![Kopiera URL för HTTP-slutpunkt från Azure Portal](./media/logic-apps-http-endpoint/copy-manual-trigger-callback-url.png)

      Eller så kan du hämta webb adressen genom att göra det här anropet:

      ```http
      POST https://management.azure.com/{logic-app-resource-ID}/triggers/{endpoint-trigger-name}/listCallbackURL?api-version=2016-06-01
      ```

<a name="set-method"></a>

## <a name="set-expected-http-method"></a>Ange förväntad HTTP-metod

Som standard förväntas utlösaren av begäran en HTTP POST-begäran. Du kan dock ange en annan metod för att förvänta, men endast en metod.

1. Öppna listan **Lägg till ny parameter** i begär ande utlösare och välj **metod**, som lägger till den här egenskapen i utlösaren.

   ![Lägg till egenskapen "metod" i utlösare](./media/logic-apps-http-endpoint/select-add-new-parameter-for-method.png)

1. I listan **metod** väljer du en annan metod som utlösaren förväntar sig i stället. Du kan också ange en anpassad metod.

   Välj till exempel metoden **Get** så att du kan testa URL: en för http-slutpunkten senare.

   ![Välj HTTP-metod som ska användas för utlösare](./media/logic-apps-http-endpoint/select-method-request-trigger.png)

## <a name="accept-parameters-in-endpoint-url"></a>Acceptera parametrar i slut punkts-URL

När du vill att URL-adressen för slut punkten ska acceptera parametrar, anger du den relativa sökvägen i utlösaren. Du måste också uttryckligen [Ange metoden](#set-method) som din http-begäran förväntar sig.

1. Öppna listan **Lägg till ny parameter** i begär ande utlösare och välj **relativ sökväg**, som lägger till den här egenskapen i utlösaren.

   ![Lägg till egenskapen "relativ sökväg" i utlösaren](./media/logic-apps-http-endpoint/select-add-new-parameter-for-relative-path.png)

1. I egenskapen **relativ sökväg** anger du den relativa sökvägen för parametern i JSON-schemat som du vill att din URL ska acceptera, till exempel `address/{postalCode}`.

   ![Ange den relativa sökvägen för parametern](./media/logic-apps-http-endpoint/relative-path-url-value.png)

1. Om du vill använda parametern kan du söka efter och lägga till en **svars** åtgärd i din Logic app.

   1. Under utlösaren för begäran väljer du **nytt steg** > **Lägg till en åtgärd**.

   1. Under **Välj en åtgärd**går du till rutan Sök och anger `response` som ditt filter.

   1. I listan åtgärder väljer du åtgärden **svar** .

1. I svars åtgärdens egenskap **Body** inkluderar du den token som representerar den parameter som du angav i utlösarens relativa sökväg.

   Anta till exempel att du vill att svars åtgärden ska returneras `Postal Code: {postalCode}`.

   I egenskapen **Body** anger `Postal Code: ` du med ett avslutande blank steg. Välj token **Postnr** i listan med dynamiskt innehåll som visas.

   ![Lägg till den angivna parametern i svars texten](./media/logic-apps-http-endpoint/relative-url-with-parameter-token.png)

   Egenskapen **Body** innehåller nu den valda parametern:

   ![Exempel på svars text med parameter](./media/logic-apps-http-endpoint/relative-url-with-parameter.png)

1. Spara din logikapp.

    URL-adressen till HTTP-slutpunkten innehåller nu den relativa sökvägen, till exempel:

    ```http
    https://prod-25.westus.logic.azure.com/workflows/{logic-app-resource-ID}/triggers/manual/paths/invoke/address/{postalCode}?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig={shared-access-signature}
    ```

1. Om du vill testa HTTP-slutpunkten kopierar du och klistrar in den uppdaterade URL: `{postalCode}` en `123456`i ett annat webbläsarfönster, men ersätter med och trycker på RETUR.

   Webbläsaren visar den här texten:`Postal Code: 123456`

## <a name="call-logic-app-through-http-endpoint"></a>Anropa Logic app via HTTP-slutpunkt

När du har skapat HTTP-slutpunkten kan du utlösa Logic-appen genom att `POST` skicka en http-begäran till slut punktens fullständiga URL. Logic Apps har inbyggt stöd för slut punkter för direkt åtkomst.

<a name="generated-tokens"></a>

## <a name="tokens-generated-from-schema"></a>Tokens som genererats från schema

När du anger ett JSON-schema i begär ande utlösaren genererar Logic App Designer tokens för egenskaperna i schemat. Du kan sedan använda dessa token för att skicka data via ditt Logic app-arbetsflöde.

Om du till exempel lägger till fler egenskaper, `"suite"`till exempel till JSON-schemat, är tokens för dessa egenskaper tillgängliga för dig i senare steg för din Logic app. Här är det fullständiga JSON-schemat:

```json
   {
   "type": "object",
   "properties": {
      "address": {
         "type": "object",
         "properties": {
            "streetNumber": {
               "type": "string"
            },
            "streetName": {
               "type": "string"
            },
            "suite": {
               "type": "string"
            },
            "town": {
               "type": "string"
            },
            "postalCode": {
               "type": "string"
            }
         }
      }
   }
}
```

## <a name="create-nested-logic-apps"></a>Skapa kapslade Logic Apps

Du kan kapsla arbets flöden i din Logic app genom att lägga till andra Logic Apps som kan ta emot begär Anden. Följ dessa steg om du vill ta med dessa Logic Apps:

1. Under steget där du vill anropa en annan Logic app väljer du **nytt steg** > **Lägg till en åtgärd**.

1. Under **Välj en åtgärd**väljer du **inbyggt**. I rutan Sök anger `logic apps` du som filter. I listan åtgärder väljer du **Välj ett Logic Apps arbets flöde**.

   ![Kapsla Logic app i aktuell Logic app](./media/logic-apps-http-endpoint/choose-logic-apps-workflow.png)

   Designern visar de berättigade Logic Apps som du kan välja.

1. Välj den Logic-app som ska anropas från din aktuella Logic-app.

   ![Välj Logic app som ska anropas från den aktuella Logic-appen](./media/logic-apps-http-endpoint/select-logic-app-to-nest.png)

## <a name="reference-content-from-an-incoming-request"></a>Referens innehåll från en inkommande begäran

Om den inkommande begärans innehålls typ är `application/json`, kan du referera till egenskaperna i den inkommande begäran. Annars behandlas det här innehållet som en enda binär enhet som du kan skicka till andra API: er. Om du vill referera till det här innehållet i din Logic app-arbetsflöde måste du först konvertera det innehållet.

Om du till exempel skickar innehåll som är `application/xml` av typen kan du använda [ `@xpath()` uttrycket](../logic-apps/workflow-definition-language-functions-reference.md#xpath) för att utföra en XPath-extrahering eller använda [ `@json()` uttrycket](../logic-apps/workflow-definition-language-functions-reference.md#json) för att konvertera XML till JSON. Lär dig mer om att arbeta med [innehålls typer](../logic-apps/logic-apps-content-type.md)som stöds.

Du kan använda [ `@triggerOutputs` uttrycket](../logic-apps/workflow-definition-language-functions-reference.md#triggerOutputs)för att hämta utdata från en inkommande begäran. Anta till exempel att du har utdata som ser ut som i det här exemplet:

```json
{
   "headers": {
      "content-type" : "application/json"
   },
   "body": {
      "myProperty" : "property value"
   }
}
```

För att få åtkomst `body` till egenskapen specifikt kan du använda [ `@triggerBody()` uttrycket](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody) som en genväg.

## <a name="respond-to-requests"></a>Svara på begär Anden

Ibland vill du svara på vissa förfrågningar som utlöser din Logic app genom att returnera innehåll till anroparen. Använd svars åtgärden för att skapa status kod, sidhuvud och brödtext för ditt svar. Den här åtgärden kan visas var som helst i din Logic app, inte bara i slutet av arbets flödet. Om din Logic app inte innehåller någon svars åtgärd, svarar HTTP-slutpunkten *omedelbart* med status **202 accepterad** .

För att den ursprungliga anroparen ska kunna hämta svaret måste alla nödvändiga steg för svaret slutföras inom [tids gränsen för begäran](./logic-apps-limits-and-config.md) , om inte den Utlös ande Logic-appen anropas som en kapslad Logic-app. Om inget svar returneras inom den här gränsen, tids gränsen överskrids den inkommande begäran och tar emot **408-klientens timeout** -svar.

För kapslade Logic Apps fortsätter appen för överordnad logik att vänta på ett svar tills alla steg har slutförts, oavsett hur lång tid det tar.

### <a name="construct-the-response"></a>Skapa svaret

I svars texten kan du inkludera flera huvuden och vilken typ av innehåll som helst. Det här svarets rubrik anger till exempel att svarets innehålls typ är `application/json` och att texten innehåller värden för egenskaperna `town` och `postalCode` , baserat på det JSON-schema som beskrivs tidigare i det här avsnittet för begäran utlösare.

![Tillhandahåll svars innehåll för HTTP-svars åtgärd](./media/logic-apps-http-endpoint/content-for-response-action.png)

Svaren har följande egenskaper:

| Egenskap (Visa) | Egenskap (JSON) | Beskrivning |
|--------------------|-----------------|-------------|
| **Status kod** | `statusCode` | HTTP-statuskod som ska användas i svaret på inkommande begäran. Den här koden kan vara vilken giltig status kod som helst som börjar med 2xx, 4xx eller 5xx. 3xx status koder är dock inte tillåtna. |
| **Rubriker** | `headers` | En eller flera huvuden som ska inkluderas i svaret |
| **Brödtext** | `body` | Ett Body-objekt som kan vara en sträng, ett JSON-objekt eller till och med ett binärt innehåll som refereras från ett föregående steg |
||||

Om du vill visa JSON-definitionen för svars åtgärden och din Logic Apps fullständiga JSON-definition går du till verktygsfältet Logic App Designer och väljer **kodvyn**.

``` json
"Response": {
   "type": "Response",
   "kind": "http",
   "inputs": {
      "body": {
         "postalCode": "@triggerBody()?['address']?['postalCode']",
         "town": "@triggerBody()?['address']?['town']"
      },
      "headers": {
         "content-type": "application/json"
      },
      "statusCode": 200
   },
   "runAfter": {}
}
```

## <a name="q--a"></a>Frågor och svar

#### <a name="q-what-about-url-security"></a>F: vad gäller URL-säkerhet?

**A**: Azure på ett säkert sätt skapar callback-URL: er för Logic app med hjälp av [signaturen för delad åtkomst (SAS)](https://docs.microsoft.com/rest/api/storageservices/delegate-access-with-shared-access-signature). Den här signaturen passerar som en frågeparameter och måste verifieras innan din Logi Kap par kan köras. Azure genererar signaturen med en unik kombination av en hemlig nyckel per Logic app, Utlösarens namn och åtgärden som utförs. Så om någon har åtkomst till den hemliga Logic app-nyckeln kan de inte generera en giltig signatur.

> [!IMPORTANT]
> För produktions-och högre säkerhets system rekommenderar vi starkt att du anropar din Logic-app direkt från webbläsaren av följande anledningar:
>
> * Den delade åtkomst nyckeln visas i URL: en.
> * Du kan inte hantera principer för säkerhets innehåll på grund av delade domäner i Azure Logic Apps kunder.

#### <a name="q-can-i-configure-http-endpoints-further"></a>F: kan jag konfigurera HTTP-slutpunkter ytterligare?

**A**: Ja, http-slutpunkter har stöd för mer avancerad konfiguration via [Azure API Management](../api-management/api-management-key-concepts.md). Den här tjänsten ger också möjlighet att hantera alla dina API: er på ett konsekvent sätt, inklusive Logic Apps, konfigurera anpassade domän namn, använda fler autentiseringsmetoder med mera, till exempel:

* [Ändra metoden för begäran](../api-management/api-management-advanced-policies.md#SetRequestMethod)
* [Ändra URL-segmenten för begäran](../api-management/api-management-transformation-policies.md#RewriteURL)
* Konfigurera API Management domäner i [Azure Portal](https://portal.azure.com/)
* Konfigurera en princip för att kontrol lera grundläggande autentisering

## <a name="next-steps"></a>Nästa steg

* [Ta emot och svara på inkommande HTTPS-anrop genom att använda Azure Logic Apps](../connectors/connectors-native-reqres.md)