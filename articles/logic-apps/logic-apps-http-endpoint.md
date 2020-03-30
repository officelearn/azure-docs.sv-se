---
title: Anropa, utlösa eller kapsla logikappar
description: Konfigurera HTTP-slutpunkter för att anropa, utlösa eller kapsla logikapparbetsflöden i Azure Logic Apps
services: logic-apps
ms.workload: integration
ms.reviewer: klam, jehollan, logicappspm
ms.topic: article
ms.date: 11/04/2019
ms.openlocfilehash: d5b5a69c7927d07c0ae6b3b56ec97b6551e5d46b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77191336"
---
# <a name="call-trigger-or-nest-logic-apps-by-using-http-endpoints-in-azure-logic-apps"></a>Anropa, utlösa eller kapsla logikappar med hjälp av HTTP-slutpunkter i Azure Logic Apps

Om du vill att logikappen ska kunna anropas via en URL så att logikappen kan ta emot inkommande begäranden från andra tjänster kan du internt exponera en synkron HTTP-slutpunkt som en utlösare i den logikappen. När du ställer in den här funktionen kan du också kapsla logikappen i andra logikappar, vilket gör att du kan skapa ett mönster av samtalsbara slutpunkter.

Om du vill konfigurera en HTTP-slutpunkt kan du använda någon av dessa utlösartyper som gör det möjligt för logikappar att ta emot inkommande begäranden:

* [Förfrågan](../connectors/connectors-native-reqres.md)
* [HTTP-webhook](../connectors/connectors-native-webhook.md)
* Hanterade anslutningsutlösare som har [apiconnectionwebhook-typen](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger) och kan ta emot inkommande HTTP-begäranden

> [!NOTE]
> I de här exemplen används utlösaren Begäran, men du kan använda valfri HTTP-begäran-baserad utlösare som finns i föregående lista. Alla principer gäller på samma sätt för dessa andra utlösartyper.

Om du inte har tidigare information om logikappar läser du [Vad är Azure Logic Apps](../logic-apps/logic-apps-overview.md) och [Snabbstart: Skapa din första logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du inte har någon prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Logikappen där du vill konfigurera HTTP-slutpunkten som utlösare. Du kan börja med antingen en tom logikapp eller en befintlig logikapp där du vill ersätta den aktuella utlösaren. Det här exemplet börjar med en tom logikapp.

## <a name="create-a-callable-endpoint"></a>Skapa en samtalsbar slutpunkt

1. Logga in på [Azure-portalen](https://portal.azure.com). Skapa och öppna en tom logikapp i Logic App Designer.

   I det här exemplet används utlösaren Begäran, men du kan använda alla utlösare som kan ta emot inkommande HTTP-begäranden. Alla principer gäller på samma sätt för dessa utlösare. Mer information om utlösaren för begäran finns i [Ta emot och svara på inkommande HTTPS-anrop med hjälp av Azure Logic Apps](../connectors/connectors-native-reqres.md).

1. Välj **Inbyggd**under sökrutan . Ange som filter `request` i sökrutan. Välj **När en HTTP-begäran tas emot i**listan utlösare .

   ![Söka efter och välj utlösaren För begäran](./media/logic-apps-http-endpoint/find-and-select-request-trigger.png)

1. I rutan **Begärandetext JSON Schema** kan du också ange ett JSON-schema som beskriver nyttolasten eller data som du förväntar dig att utlösaren ska ta emot.

   Designern använder det här schemat för att generera token som representerar utlösande utgångar. Du kan sedan enkelt referera till dessa utdata i logikappens arbetsflöde. Läs mer om [token som genereras från JSON-scheman](#generated-tokens).

   I det här exemplet anger du det här schemat:

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

   ![Ange JSON-schema för åtgärden Begär](./media/logic-apps-http-endpoint/manual-request-trigger-schema.png)

   Du kan också skapa ett JSON-schema genom att tillhandahålla ett exempel på nyttolast:

   1. I **utlösaren Begäran** väljer du **Använd exempelnyttolast för att generera schema**.

   1. I rutan **Ange eller klistra in ett exempel på JSON-nyttolast** anger du exempelnyttolasten, till exempel:

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

   1. När du är klar väljer du **Klar**.

      Rutan **Begärandetext JSON Schema** visar nu det genererade schemat.

1. Spara din logikapp.

   Http **POST till den här URL-rutan** visar nu den genererade motringnings-URL:en som andra tjänster kan använda för att anropa och utlösa logikappen. Den här URL:en innehåller en SAS-nyckel (Shared Access Signature), som används för autentisering, i frågeparametrarna, till exempel:

   ![Genererad motringnings-URL för slutpunkt](./media/logic-apps-http-endpoint/generated-endpoint-url.png)

   Du kan också hämta HTTP-slutpunkts-URL:en från **logikappens** översiktsfönster.

   1. På logikappens meny väljer du **Översikt**.

   1. I avsnittet **Sammanfattning** väljer du **Visa utlösarhistorik**.

      ![Hämta URL för HTTP-slutpunkt från Azure-portalen](./media/logic-apps-http-endpoint/find-manual-trigger-url.png)

   1. Under **Motringningsadress [POST]** kopierar du webbadressen:

      ![Kopiera HTTP-url till slutpunkt från Azure-portalen](./media/logic-apps-http-endpoint/copy-manual-trigger-callback-url.png)

      Eller så kan du få webbadressen genom att ringa det här samtalet:

      ```http
      POST https://management.azure.com/{logic-app-resource-ID}/triggers/{endpoint-trigger-name}/listCallbackURL?api-version=2016-06-01
      ```

<a name="set-method"></a>

## <a name="set-expected-http-method"></a>Ange förväntad HTTP-metod

Som standard förväntar sig utlösaren Begäran en HTTP POST-begäran. Du kan dock ange en annan metod att förvänta sig, men bara en metod.

1. Öppna listan **Lägg till ny parameter** i utlösaren Begäran och välj **Metod**, som lägger till den här egenskapen i utlösaren.

   ![Lägg till egenskapen "Metod" för att utlösa](./media/logic-apps-http-endpoint/select-add-new-parameter-for-method.png)

1. Välj en annan metod som utlösaren förväntar sig i stället i listan **Metod.** Du kan också ange en anpassad metod.

   Välj till exempel **GET-metoden** så att du kan testa HTTP-slutpunktens URL senare.

   ![Välj HTTP-metod som ska användas för utlösare](./media/logic-apps-http-endpoint/select-method-request-trigger.png)

## <a name="accept-parameters-in-endpoint-url"></a>Acceptera parametrar i slutpunkts-URL

När du vill att slutpunkts-URL:en ska acceptera parametrar anger du den relativa sökvägen i utlösaren. Du måste också uttryckligen [ange den metod](#set-method) som http-begäran förväntar sig.

1. Öppna listan **Lägg till ny parameter** i utlösaren Begäran och välj Relativ **sökväg**, som lägger till den här egenskapen i utlösaren.

   ![Lägg till egenskapen Relativ sökväg för att utlösa](./media/logic-apps-http-endpoint/select-add-new-parameter-for-relative-path.png)

1. I egenskapen **Relativ sökväg** anger du den relativa sökvägen för parametern i JSON-schemat som du vill att webbadressen ska acceptera, till exempel `address/{postalCode}`.

   ![Ange den relativa sökvägen för parametern](./media/logic-apps-http-endpoint/relative-path-url-value.png)

1. Om du vill använda parametern hittar och lägger du till en **svarsåtgärd** i logikappen.

   1. Under utlösaren Begäran väljer du **Nytt steg** > **Lägg till en åtgärd**.

   1. Ange `response` som filter i sökrutan under **Välj en åtgärd.**

   1. Välj åtgärden **Svar** i åtgärdslistan.

1. I egenskapen Svarsåtgärden **Body** inkludera du den token som representerar parametern som du angav i utlösarens relativa sökväg.

   Anta till exempel att du vill `Postal Code: {postalCode}`att åtgärden Svar ska returneras .

   Ange ett avslutande `Postal Code: ` utrymme i egenskapen **Body.** Välj **postalCode-token** i listan med dynamiskt innehåll som visas.

   ![Lägga till den angivna parametern i svarstexten](./media/logic-apps-http-endpoint/relative-url-with-parameter-token.png)

   Egenskapen **Body** innehåller nu den valda parametern:

   ![Exempel på svarstext med parameter](./media/logic-apps-http-endpoint/relative-url-with-parameter.png)

1. Spara din logikapp.

    HTTP-slutpunkts-URL:en innehåller nu den relativa sökvägen, till exempel:

    ```http
    https://prod-25.westus.logic.azure.com/workflows/{logic-app-resource-ID}/triggers/manual/paths/invoke/address/{postalCode}?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig={shared-access-signature}
    ```

1. Om du vill testa HTTP-slutpunkten kopierar och klistrar `{postalCode}` `123456`du in den uppdaterade webbadressen i ett annat webbläsarfönster, men ersätter med och trycker på Retur.

   Din webbläsare visar den här texten:`Postal Code: 123456`

## <a name="call-logic-app-through-http-endpoint"></a>Anropa logikapp via HTTP-slutpunkt

När du har skapat HTTP-slutpunkten kan du utlösa `POST` logikappen genom att skicka en HTTP-begäran till slutpunktens fullständiga URL. Logikappar har inbyggt stöd för slutpunkter med direkt åtkomst.

<a name="generated-tokens"></a>

## <a name="tokens-generated-from-schema"></a>Token som genereras från schemat

När du anger ett JSON-schema i utlösaren Begäran genererar Logic App Designer token för egenskaperna i schemat. Du kan sedan använda dessa token för att skicka data via logikapparbetsflödet.

Om du till exempel lägger `"suite"`till fler egenskaper, till exempel , i JSON-schemat, är token för dessa egenskaper tillgängliga för dig att använda i de senare stegen för logikappen. Här är det fullständiga JSON-schemat:

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

## <a name="create-nested-logic-apps"></a>Skapa kapslade logikappar

Du kan kapsla arbetsflöden i logikappen genom att lägga till andra logikappar som kan ta emot begäranden. Så här tar du med dessa logikappar:

1. Under det steg där du vill anropa en annan logikapp väljer du **Nytt steg** > **Lägg till en åtgärd**.

1. Under **Välj en åtgärd**väljer du **Inbyggd**. Ange som filter `logic apps` i sökrutan. Välj ett Logic **Apps-arbetsflöde**i listan Åtgärder .

   ![Kapsla logikapp i aktuell logikapp](./media/logic-apps-http-endpoint/choose-logic-apps-workflow.png)

   Designern visar de kvalificerade logikapparna som du kan välja.

1. Välj den logikapp som ska anropas från din aktuella logikapp.

   ![Välj logikapp att anropa från aktuell logikapp](./media/logic-apps-http-endpoint/select-logic-app-to-nest.png)

## <a name="reference-content-from-an-incoming-request"></a>Referera till innehåll från en inkommande begäran

Om den inkommande begärans `application/json`innehållstyp är kan du referera till egenskaperna i den inkommande begäran. Annars behandlas det här innehållet som en enda binär enhet som du kan skicka till andra API:er. Om du vill referera till det här innehållet i logikappens arbetsflöde måste du först konvertera innehållet.

Om du till exempel skickar innehåll `application/xml` som har typ kan du använda [ `@xpath()` uttrycket](../logic-apps/workflow-definition-language-functions-reference.md#xpath) för att utföra en XPath-extrahering eller använda [ `@json()` uttrycket](../logic-apps/workflow-definition-language-functions-reference.md#json) för att konvertera XML till JSON. Läs mer om hur du arbetar med [innehållstyper](../logic-apps/logic-apps-content-type.md)som stöds .

Om du vill hämta utdata från en inkommande begäran kan du använda [ `@triggerOutputs` uttrycket](../logic-apps/workflow-definition-language-functions-reference.md#triggerOutputs). Anta till exempel att du har utdata som ser ut så här:

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

Om du vill `body` komma åt egenskapen kan du använda [ `@triggerBody()` uttrycket](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody) som en genväg.

## <a name="respond-to-requests"></a>Svara på förfrågningar

Ibland vill du svara på vissa begäranden som utlöser logikappen genom att returnera innehåll till anroparen. Om du vill skapa statuskod, rubrik och brödtext för ditt svar använder du åtgärden Svar. Den här åtgärden kan visas var som helst i logikappen, inte bara i slutet av arbetsflödet. Om logikappen inte innehåller en svarsåtgärd svarar HTTP-slutpunkten *omedelbart* med **statusen 202 Accepterad.**

För att den ursprungliga anroparen ska kunna få svaret måste alla nödvändiga steg för svaret slutföras inom [tidsgränsen för begäran](./logic-apps-limits-and-config.md) om inte den utlösta logikappen anropas som en kapslad logikapp. Om inget svar returneras inom den här gränsen timeout timeout den inkommande begäran och tar emot **408 Client timeout-svar.**

För kapslade logikappar fortsätter den överordnade logikappen att vänta på ett svar tills alla steg har slutförts, oavsett hur mycket tid som krävs.

### <a name="construct-the-response"></a>Konstruera svaret

I svarstexten kan du inkludera flera rubriker och alla typer av innehåll. Det här svarets rubrik anger till exempel att svarets innehållstyp är `application/json` och `town` `postalCode` att brödtexten innehåller värden för egenskaperna och, baserat på det JSON-schema som beskrivs tidigare i det här avsnittet för utlösaren Begäran.

![Ange svarsinnehåll för HTTP-svarsåtgärd](./media/logic-apps-http-endpoint/content-for-response-action.png)

Svaren har följande egenskaper:

| Egenskap (Display) | Egenskap (JSON) | Beskrivning |
|--------------------|-----------------|-------------|
| **Statuskod** | `statusCode` | DEN HTTP-statuskod som ska användas i svaret för den inkommande begäran. Denna kod kan vara en giltig statuskod som börjar med 2xx, 4xx eller 5xx. 3xx-statuskoder är dock inte tillåtna. |
| **Rubriker** | `headers` | En eller flera rubriker som ska inkluderas i svaret |
| **Brödtext** | `body` | Ett brödtextobjekt som kan vara en sträng, ett JSON-objekt eller till och med binärt innehåll som refereras från ett tidigare steg |
||||

Om du vill visa JSON-definitionen för svarsåtgärden och logikappens fullständiga JSON-definition väljer du **Kodvy**i verktygsfältet Logikappdesigner .

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

## <a name="q--a"></a>Vanliga frågor och svar

#### <a name="q-what-about-url-security"></a>F: Hur är det med url-säkerhet?

**S:** Azure genererar url:er för återanrop av logikappar med hjälp av [SAS (Shared Access Signature).](https://docs.microsoft.com/rest/api/storageservices/delegate-access-with-shared-access-signature) Signaturen skickas igenom som en frågeparameter och måste valideras innan logikappen kan köras. Azure genererar signaturen med hjälp av en unik kombination av en hemlig nyckel per logikapp, utlösarnamnet och åtgärden som utförs. Så om inte någon har tillgång till den hemliga logikappnyckeln kan de inte generera en giltig signatur.

> [!IMPORTANT]
> För produktion och högre säkerhetssystem avråder vi starkt från att ringa din logikapp direkt från webbläsaren av dessa skäl:
>
> * Den delade åtkomstnyckeln visas i URL:en.
> * Du kan inte hantera principer för säkerhetsinnehåll på grund av delade domäner i Azure Logic Apps-kunder.

#### <a name="q-can-i-configure-http-endpoints-further"></a>F: Kan jag konfigurera HTTP-slutpunkter ytterligare?

**S**: Ja, HTTP-slutpunkter stöder mer avancerad konfiguration via [Azure API Management](../api-management/api-management-key-concepts.md). Den här tjänsten erbjuder också möjligheten för dig att konsekvent hantera alla dina API:er, inklusive logikappar, konfigurera anpassade domännamn, använda fler autentiseringsmetoder med mera, till exempel:

* [Ändra begäranemetoden](../api-management/api-management-advanced-policies.md#SetRequestMethod)
* [Ändra URL-segmenten för begäran](../api-management/api-management-transformation-policies.md#RewriteURL)
* Konfigurera dina API Management-domäner i [Azure-portalen](https://portal.azure.com/)
* Ställ in princip för att söka efter grundläggande autentisering

## <a name="next-steps"></a>Nästa steg

* [Ta emot och svara på inkommande HTTPS-samtal med hjälp av Azure Logic Apps](../connectors/connectors-native-reqres.md)