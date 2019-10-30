---
title: Anropa, utlösa eller kapsla Logic Apps – Azure Logic Apps
description: Konfigurera HTTP-slutpunkter för att anropa, utlösa eller kapsla Logic app-arbetsflöden i Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.workload: integration
author: ecfan
ms.author: klam
ms.reviewer: jehollan, klam, LADocs
manager: carmonm
ms.assetid: 73ba2a70-03e9-4982-bfc8-ebfaad798bc2
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 03/31/2017
ms.openlocfilehash: 4fc20c4b1314d953ea979192c81b2c264292d3af
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/29/2019
ms.locfileid: "73041959"
---
# <a name="call-trigger-or-nest-logic-apps-by-using-http-endpoints-in-azure-logic-apps"></a>Anropa, utlösa eller kapsla Logi Kap par genom att använda HTTP-slutpunkter i Azure Logic Apps

Du kan på ett enhetligt sätt exponera synkrona HTTP-slutpunkter som utlösare i Logi Kap par så att du kan utlösa eller anropa dina Logi Kap par via en URL. Du kan också kapsla arbets flöden i dina Logi Kap par genom att använda ett mönster av anrops bara slut punkter.

För att skapa HTTP-slutpunkter kan du lägga till dessa utlösare så att dina Logi Kap par kan ta emot inkommande begär Anden:

* [Förfrågan](../connectors/connectors-native-reqres.md)

* [API-anslutning-webhook](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnection-trigger)

* [HTTP-webhook](../connectors/connectors-native-webhook.md)

   > [!NOTE]
   > Även om dessa exempel använder **begär** ande utlösare kan du använda någon av de listade begär ande utlösarna och alla principer som identiskt gäller för de andra utlösarna.

## <a name="set-up-an-http-endpoint-for-your-logic-app"></a>Konfigurera en HTTP-slutpunkt för din Logic app

Om du vill skapa en HTTP-slutpunkt lägger du till en utlösare som kan ta emot inkommande begär Anden.

1. Logga in på [Azure-portalen](https://portal.azure.com "Azure portal"). Gå till din Logic app och öppna Logic App Designer.

1. Lägg till en utlösare som tillåter att din Logic app tar emot inkommande begär Anden. Lägg till exempel till utlösaren för **begäran** till din Logic app.

1. Under **JSON-schemat för begär ande text**kan du ange ett JSON-schema för nytto lasten (data) som du förväntar sig att utlösaren ska ta emot.

   Designern använder det här schemat för att skapa token som din Logic app kan använda för att använda, parsa och skicka data från utlösaren via ditt arbets flöde. Läs mer om [tokens som genereras från JSON-scheman](#generated-tokens).

   I det här exemplet anger du det här schemat som det visas i designern:

   ```json
   {
      "type": "object",
      "properties": {
         "address": {
            "type": "string"
         }
      },
      "required": [
        "address"
      ]
    }
    ```

   ![Ange JSON-schema för begär ande åtgärden](./media/logic-apps-http-endpoint/manual-request-trigger-schema.png)

   > [!TIP]
   >
   > Du kan skapa ett schema för ett exempel på en JSON-nyttolast från ett verktyg som [jsonschema.net](https://jsonschema.net/) eller i utlösaren för **begäran** genom att välja **Använd exempel nytto last för att skapa schemat**. Ange provets nytto last och välj sedan **slutfört**.

   Exempel:

   ```json
   {
      "address": "21 2nd Street, New York, New York"
   }
   ```

   genererar det här schemat:

   ```json
   {
      "type": "object",
      "properties": {
         "address": {
            "type": "string"
         }
      }
   }
   ```

1. Spara din logikapp. Under **http post till denna URL**bör du nu hitta en genererad återanrops-URL, t. ex. det här exemplet:

   ![URL för återanrop har genererats för slut punkt](./media/logic-apps-http-endpoint/generated-endpoint-url.png)

   Denna URL innehåller en nyckel för signatur för delad åtkomst (SAS) i de frågeparametrar som används för autentisering. Du kan också hämta HTTP-slutpunktens URL från din Logic app-översikt i Azure Portal. Under **utlösnings historik**väljer du din utlösare:

   ![Hämta URL för HTTP-slutpunkt från Azure Portal](./media/logic-apps-http-endpoint/find-manual-trigger-url.png)

   Eller så kan du hämta webb adressen genom att göra det här anropet:

    ```http
    POST https://management.azure.com/{logic-app-resource-ID}/triggers/{myendpointtrigger}/listCallbackURL?api-version=2016-06-01
    ```

## <a name="change-the-http-method-for-your-trigger"></a>Ändra HTTP-metoden för utlösaren

Som standard förväntas utlösaren av **begäran** en http post-begäran, men du kan använda en annan http-metod.

> [!NOTE]
> Du kan bara ange en metod typ.

1. Välj **Visa avancerade alternativ**på din **begär** ande utlösare.

2. Öppna **metod** listan. I det här exemplet väljer du **Hämta** så att du kan testa URL: en för http-slutpunkten senare.

   > [!NOTE]
   > Du kan välja någon annan HTTP-metod eller ange en anpassad metod för din egen Logic-app.

   ![Välj HTTP-metod som ska användas för begäran](./media/logic-apps-http-endpoint/select-method-request-trigger.png)

## <a name="accept-parameters-through-your-http-endpoint-url"></a>Acceptera parametrar via URL: en för HTTP-slutpunkt

När du vill att URL: en för HTTP-slutpunkten ska acceptera parametrar, anpassa utlösarens relativa sökväg.

1. Välj **Visa avancerade alternativ**på din **begär** ande utlösare. 

2. Under **metod**anger du den http-metod som du vill att din begäran ska använda. I det här exemplet väljer du metoden **Get** , om du inte redan har gjort det, så att du kan testa URL: en för http-slutpunkten.

   > [!NOTE]
   > När du anger en relativ sökväg för utlösaren måste du även uttryckligen ange en HTTP-metod för utlösaren.

3. Under **relativ sökväg**anger du den relativa sökvägen till den parameter som din URL ska acceptera, till exempel `customers/{customerID}`.

   ![Ange HTTP-metod och relativ sökväg för parametern](./media/logic-apps-http-endpoint/relative-path-url-value.png)

4. Om du vill använda parametern lägger du till en **svars** åtgärd i din Logic app. (Under utlösaren väljer du **nytt steg**  > **lägger till en åtgärd**  > **svar**) 

5. I ditt svars **brödtext**inkluderar du token för den parameter som du angav i utlösarens relativa sökväg.

   Om du till exempel vill returnera `Hello {customerID}` uppdaterar du svarets **brödtext** med `Hello {customerID token}`. Listan med dynamiskt innehåll ska visas och Visa `customerID`-token som du kan välja.

   ![Lägg till parameter i svars texten](./media/logic-apps-http-endpoint/relative-url-with-parameter-token.png)

   **Bröd texten** bör se ut som i det här exemplet:

   ![Exempel på svars text med parameter](./media/logic-apps-http-endpoint/relative-url-with-parameter.png)

6. Spara din logikapp. 

    URL-adressen till HTTP-slutpunkten innehåller nu den relativa sökvägen, till exempel: 

    ```http
    https://prod-00.southcentralus.logic.azure.com/workflows/{logic-app-resource-ID}/triggers/manual/paths/invoke/customers/{customerID}...
    ```

7. Om du vill testa HTTP-slutpunkten kopierar du och klistrar in den uppdaterade URL: en i ett annat webbläsarfönster, men ersätter `{customerID}` med `123456` och trycker på RETUR.

   Webbläsaren ska visa den här texten: `Hello 123456`

<a name="generated-tokens"></a>

### <a name="tokens-generated-from-json-schemas-for-your-logic-app"></a>Tokens som genererats från JSON-scheman för din Logic app

När du anger ett JSON-schema i din **begär** ande utlösare genererar Logic App Designer tokens för egenskaper i schemat. Du kan sedan använda dessa token för att skicka data via ditt Logic app-arbetsflöde.

Om du i det här exemplet lägger till `title` och `name` egenskaper till ditt JSON-schema, är deras tokens nu tillgängliga för användning i senare arbets flödes steg. 

Här är det fullständiga JSON-schemat:

```json
{
   "type": "object",
   "properties": {
      "address": {
         "type": "string"
      },
      "title": {
         "type": "string"
      },
      "name": {
         "type": "string"
      }
   },
   "required": [
      "address",
      "title",
      "name"
   ]
}
```

## <a name="create-nested-workflows-for-logic-apps"></a>Skapa kapslade arbets flöden för Logic Apps

Du kan kapsla arbets flöden i din Logic app genom att lägga till andra Logic Apps som kan ta emot begär Anden. Om du vill ta med dessa Logi Kap par lägger du till åtgärden **Azure Logic Apps – Välj en Logic Apps arbets flödes** åtgärd i utlösaren. Du kan sedan välja från berättigade Logic Apps.

![Kapsla Logic app i aktuell Logic app](./media/logic-apps-http-endpoint/choose-logic-apps-workflow.png)

## <a name="call-or-trigger-logic-apps-through-http-endpoints"></a>Anropa eller utlös Logi Kap par via HTTP-slutpunkter

När du har skapat din HTTP-slutpunkt kan du utlösa din Logic app via en `POST`-metod till den fullständiga URL: en. Logic Apps har inbyggt stöd för slut punkter för direkt åtkomst.

> [!NOTE] 
> Om du vill köra en Logi Kap par manuellt klickar du på **Kör**i vyn Logic App Designer eller Logic app kodvyn.

## <a name="reference-content-from-an-incoming-request"></a>Referens innehåll från en inkommande begäran

Om innehålls typen är `application/json` kan du referera till egenskaper från den inkommande begäran. Annars behandlas innehåll som en enda binär enhet som du kan skicka till andra API: er. Om du vill referera till det här innehållet i arbets flödet måste du konvertera det innehållet. Om du till exempel skickar `application/xml` innehåll kan du använda `@xpath()` för en XPath-extrahering eller för att `@json()` konvertera XML till JSON. Lär dig mer om att [arbeta med innehålls typer](../logic-apps/logic-apps-content-type.md).

Du kan använda funktionen `@triggerOutputs()` för att hämta utdata från en inkommande begäran. Utdata kan se ut som i det här exemplet:

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

Om du vill komma åt egenskapen `body` specifikt kan du använda kortkommandot `@triggerBody()`.

## <a name="respond-to-requests"></a>Svara på begär Anden

Du kanske vill svara på vissa begär Anden som startar en Logic-app genom att returnera innehåll till anroparen. Om du vill skapa status kod, sidhuvud och brödtext för ditt svar kan du använda **svars** åtgärden. Den här åtgärden kan visas var som helst i din Logic app, inte bara i slutet av arbets flödet.

> [!NOTE] 
> Om din Logic app inte innehåller något **svar**, svarar http-slutpunkten *omedelbart* med en **202-godkänd** status. För att den ursprungliga begäran ska få svaret måste alla steg som krävs för svaret slutföras inom tids gränsen för [begäran](./logic-apps-limits-and-config.md) , om du inte anropar arbets flödet som en kapslad Logic-app. Om inget svar inträffar inom den här gränsen, tids gränsen för inkommande begäran och tar emot HTTP-svar **408-klientens tids gräns**. För kapslade Logic Apps fortsätter appen för överordnad logik att vänta på ett svar tills den har slutförts, oavsett hur lång tid som krävs.

### <a name="construct-the-response"></a>Skapa svaret

Du kan inkludera fler än en rubrik och vilken typ av innehåll som helst i svars texten. I exempel svaret anger sidhuvudet att svaret har innehålls typen `application/json`. och texten innehåller `title` och `name`, baserat på det JSON-schema som uppdaterades tidigare för **begäran** -utlösaren.

![Tillhandahåll svars innehåll för HTTP-svars åtgärd](./media/logic-apps-http-endpoint/content-for-response-action.png)

Svaren har följande egenskaper:

| Egenskap | Beskrivning |
| --- | --- |
| statusCode |Anger HTTP-statuskod för att svara på inkommande begäran. Den här koden kan vara vilken giltig status kod som helst som börjar med 2xx, 4xx eller 5xx. 3xx status koder är dock inte tillåtna. |
| sidhuvud |Definierar valfritt antal huvuden som ska inkluderas i svaret. |
| brödtext |Anger ett Body-objekt som kan vara en sträng, ett JSON-objekt eller till och med ett binärt innehåll som refereras från ett föregående steg. |

Så här ser JSON-schemat ut som nu för **svars** åtgärden:

``` json
"Response": {
   "type": "Response",
   "inputs": {
      "body": {
         "title": "@{triggerBody()?['title']}",
         "name": "@{triggerBody()?['name']}"
      },
      "headers": {
         "content-type": "application/json"
      },
      "statusCode": 200
   },
   "runAfter": {}
}
```

> [!TIP]
> Om du vill visa hela JSON-definitionen för din Logic app väljer du **kodvyn**i Logic App Designer.

## <a name="q--a"></a>Frågor och svar

#### <a name="q-what-about-url-security"></a>F: vad gäller URL-säkerhet?

A: Azure på ett säkert sätt skapar callback-URL: er för Logic app med hjälp av en signatur för delad åtkomst (SAS). Den här signaturen passerar som en frågeparameter och måste verifieras innan din Logi Kap par kan aktive ras. Azure genererar signaturen med en unik kombination av en hemlig nyckel per Logic app, Utlösarens namn och åtgärden som utförs. Så om någon har åtkomst till den hemliga Logic app-nyckeln kan de inte generera en giltig signatur.

   > [!IMPORTANT]
   > För produktion och säkra system rekommenderar vi starkt att du anropar din Logic-app direkt från webbläsaren eftersom:
   > 
   > * Den delade åtkomst nyckeln visas i URL: en.
   > * Du kan inte hantera säkra innehålls principer på grund av delade domäner i Logic app-kunder.

#### <a name="q-can-i-configure-http-endpoints-further"></a>F: kan jag konfigurera HTTP-slutpunkter ytterligare?

A: Ja, HTTP-slutpunkter har stöd för mer avancerad konfiguration via [Azure API Management](../api-management/api-management-key-concepts.md). Den här tjänsten ger också möjlighet att hantera alla dina API: er på ett konsekvent sätt, inklusive Logic Apps, konfigurera anpassade domän namn, använda fler autentiseringsmetoder med mera, till exempel:

* [Ändra metoden för begäran](../api-management/api-management-advanced-policies.md#SetRequestMethod)
* [Ändra URL-segmenten för begäran](../api-management/api-management-transformation-policies.md#RewriteURL)
* Konfigurera API Management domäner i [Azure Portal](https://portal.azure.com/)
* Konfigurera en princip för att kontrol lera grundläggande autentisering

#### <a name="q-what-changed-when-the-schema-migrated-from-the-december-1-2014-preview"></a>F: vad ändrades när schemat migrerades från den 1 december 2014 Preview?

S: här är en sammanfattning av dessa ändringar:

| 1 december 2014 för hands version | Den 1 juni 2016 |
| --- | --- |
| Klicka på **http Listener API-** appen |Klicka på **manuell utlösare** (ingen API-app krävs) |
| HTTP Listener Setting "*skickar svar automatiskt*" |Inkludera antingen en **svars** åtgärd eller inte i arbets flödes definitionen |
| Konfigurera Basic-eller OAuth-autentisering |via API Management |
| Konfigurera HTTP-metod |Under **Visa avancerade alternativ**väljer du en http-metod |
| Konfigurera relativ sökväg |Lägg till en relativ sökväg under **Visa avancerade alternativ** |
| Referera till det inkommande avsnittet via `@triggerOutputs().body.Content` |Referens genom `@triggerOutputs().body` |
| **Skicka HTTP-svar-** åtgärd på http-lyssnaren |Klicka på **svara på http-begäran** (ingen API-app krävs) |

## <a name="next-steps"></a>Nästa steg

* [Skapa Logic App-definitioner](logic-apps-author-definitions.md)
* [Hantera fel och undantag](logic-apps-exception-handling.md)