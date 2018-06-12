---
title: Anropa, utlösare eller kapsla arbetsflöden med HTTP-slutpunkter – Azure Logic Apps | Microsoft Docs
description: Konfigurera HTTP-slutpunkter för anrop, utlösare eller kapsla arbetsflöden för Azure Logic Apps
services: logic-apps
keywords: arbetsflöden, HTTP-slutpunkter
author: jeffhollan
manager: jeconnoc
editor: ''
documentationcenter: ''
ms.assetid: 73ba2a70-03e9-4982-bfc8-ebfaad798bc2
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.custom: H1Hack27Feb2017
ms.date: 03/31/2017
ms.author: klam; LADocs
ms.openlocfilehash: 9c02a0f540f52007412a850b9d69e337743fc35f
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35300206"
---
# <a name="call-trigger-or-nest-workflows-with-http-endpoints-in-logic-apps"></a>Anropa utlösare eller kapsla arbetsflöden med HTTP-slutpunkter i logikappar

Du kan internt exponera synkron HTTP-slutpunkter som utlösare för logic apps så att du kan utlösa eller anropa dina logic apps via en annan URL. Du kan också kapsla arbetsflöden i dina logic apps med hjälp av ett mönster för callable slutpunkter.

Du kan lägga till dessa utlösare så att dina logic apps kan ta emot inkommande begäranden om du vill skapa HTTP-slutpunkter:

* [Förfrågan](../connectors/connectors-native-reqres.md)

* [API-anslutning Webhooken](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnection-trigger)

* [HTTP-webhook](../connectors/connectors-native-webhook.md)

   > [!NOTE]
   > Även om de här exemplen använder den **begära** utlösare, du kan använda någon av de listade HTTP-utlösarna och identiskt gäller alla principer för andra typer av utlösare.

## <a name="set-up-an-http-endpoint-for-your-logic-app"></a>Ställ in en HTTP-slutpunkt för din logikapp

Lägg till en utlösare som kan ta emot inkommande begäranden om du vill skapa en HTTP-slutpunkt.

1. Logga in på [Azure Portal](https://portal.azure.com "Azure Portal"). Gå till din logikapp och öppna logik App Designer.

2. Lägg till en utlösare som gör att din logikapp ta emot inkommande begäranden. Till exempel lägga till den **begära** utlösaren till din logikapp.

3.  Under **begära brödtext JSON-Schema**, du kan även ange en JSON-schema för nyttolasten (data) som du förväntar dig utlösaren tar emot.

    Det här schemat används i designer för att generera token som din logikapp kan använda för att använda parsa och skicka data från utlösaren genom arbetsflödet. 
    Mer om [token genereras från JSON-scheman](#generated-tokens).

    Ange schemat visas i designern för det här exemplet:

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

    ![Lägg till instruktionen begäran][1]

    > [!TIP]
    > 
    > Du kan skapa ett schema för en JSON-nyttolast med exempel från ett verktyg som [jsonschema.net](http://jsonschema.net/), eller i den **begära** utlösaren genom att välja **Använd exemplet nyttolast för att skapa schema**. 
    > Ange ditt exempel nyttolasten och välj **klar**.

    Till exempel det här exemplet nyttolast:

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

4.  Spara din logikapp. Under **HTTP POST till denna URL**, nu bör du hitta en URL som genererade motringning som det här exemplet:

    ![Genererade återanrop URL för slutpunkten](./media/logic-apps-http-endpoint/generated-endpoint-url.png)

    Denna URL innehåller en delad signatur åtkomst (SAS)-nyckel i frågeparametrar som används för autentisering. 
    Du kan också få HTTP slutpunkts-URL från din app översikt för logiken i Azure-portalen. Under **utlösaren historik**, Välj din utlösare:

    ![Hämta HTTP slutpunkts-URL från Azure-portalen][2]

    Eller du kan hämta Webbadressen genom att göra anropet:

    ```
    POST https://management.azure.com/{logic-app-resourceID}/triggers/{myendpointtrigger}/listCallbackURL?api-version=2016-06-01
    ```

## <a name="change-the-http-method-for-your-trigger"></a>Ändra HTTP-metod för utlösaren

Som standard den **begäran** utlösaren förväntar sig en HTTP POST-begäran, men du kan använda en annan HTTP-metod. 

> [!NOTE]
> Du kan ange endast en metod.

1. På din **begära** utlösa, Välj **visa avancerade alternativ**.

2. Öppna den **metoden** lista. Det här exemplet väljer du **hämta** så att du kan testa din HTTP slutpunkts-URL senare.

    > [!NOTE]
    > Du kan välja andra HTTP-metoden eller ange en anpassad metod för egna logikapp.

    ![Ändra HTTP-metoden](./media/logic-apps-http-endpoint/change-method.png)

## <a name="accept-parameters-through-your-http-endpoint-url"></a>Acceptera parametrar via HTTP-slutpunkt-URL

Om du vill HTTP slutpunkts-URL att acceptera parametrar kan anpassa din utlösaren relativ sökväg.

1. På din **begära** utlösa, Välj **visa avancerade alternativ**. 

2. Under **metoden**, ange HTTP-metod som du vill att din begäran om att använda. Det här exemplet väljer du den **hämta** metod om du inte redan har gjort så att du kan testa din HTTP slutpunkts-URL.

      > [!NOTE]
      > Du måste också explicit ange HTTP-metoden för utlösaren när du anger en relativ sökväg för utlösaren.

3. Under **relativa sökvägen**, ange den relativa sökvägen för den parameter som URL: en ska acceptera, till exempel `customers/{customerID}`.

    ![Ange HTTP-metoden och relativ sökväg för parametern](./media/logic-apps-http-endpoint/relativeurl.png)

4. Använd parametern genom att lägga till en **svar** åtgärder för att din logikapp. (Välj under din utlösaren **nytt steg** > **lägga till en åtgärd** > **svar**) 

5. I ditt svar **brödtext**, inkludera token för den parameter som du angav i ditt utlösaren relativ sökväg.

    Till exempel för att returnera `Hello {customerID}`, uppdatera ditt svar **brödtext** med `Hello {customerID token}`. 
    Listan över dynamiskt innehåll ska visas och visa den `customerID` token som du kan välja.

    ![Lägg till parametern brödtext för svar](./media/logic-apps-http-endpoint/relativeurlresponse.png)

    Din **brödtext** bör se ut som i det här exemplet:

    ![Svarstexten med parametern](./media/logic-apps-http-endpoint/relative-url-with-parameter.png)

6. Spara din logikapp. 

    HTTP-slutpunkt-URL innehåller nu relativ sökväg, till exempel: 

    https&#58;//prod-00.southcentralus.logic.azure.com/workflows/f90cb66c52ea4e9cabe0abf4e197deff/triggers/manual/paths/invoke/customers/{customerID}...

7. Om du vill testa HTTP-slutpunkt, kopiera och klistra in den uppdaterade URL: en i ett nytt webbläsarfönster, men Ersätt `{customerID}` med `123456`, och tryck på RETUR.

    Webbläsaren bör visa den här texten: 

    `Hello 123456`

<a name="generated-tokens"></a>

### <a name="tokens-generated-from-json-schemas-for-your-logic-app"></a>Token genereras från JSON-scheman för din logikapp

När du anger ett JSON-schema i din **begära** utlösaren logik App Designer genererar token för egenskaper i schemat. Du kan sedan använda dessa token för att skicka data via logik app arbetsflödet.

Det här exemplet, om du lägger till den `title` och `name` egenskaper till JSON-schema, deras token är nu tillgänglig för användning i senare arbetsflödessteg. 

Här är fullständigt JSON-schema:

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

## <a name="create-nested-workflows-for-logic-apps"></a>Skapa inkapslade arbetsflöden för logic apps

Du kan kapsla arbetsflöden i din logikapp genom att lägga till andra logikappar som kan ta emot begäranden. För att inkludera dessa logikappar, lägger du till den **Logikappar i Azure - Välj ett arbetsflöde för Logic Apps** åtgärd att utlösaren. Sedan kan du välja från berättigade logic apps.

![Lägg till en annan logikapp](./media/logic-apps-http-endpoint/choose-logic-apps-workflow.png)

## <a name="call-or-trigger-logic-apps-through-http-endpoints"></a>Anropa eller utlösare logikappar via HTTP-slutpunkter

När du har skapat HTTP-slutpunkten kan du utlösa logikappen via en `POST` metod för att en fullständig URL. Logikappar har inbyggt stöd för direkt åtkomst slutpunkter.

> [!NOTE] 
> Om du vill köra en logikapp manuellt när som helst i verktygsfältet logik App Designer eller logik App kodvy väljer **kör**.

## <a name="reference-content-from-an-incoming-request"></a>Referensinnehåll från en inkommande begäran

Om det innehåll av typen `application/json`, kan du referera egenskaper från den inkommande begäranden. Annars behandlas innehåll som en binär enhet som du kan skicka till andra API: er. Om du vill referera till det här innehållet i arbetsflödet, måste du konvertera innehållet. Om du skickar till exempel `application/xml` innehåll, kan du använda `@xpath()` för ett XPath-extrahering eller `@json()` för att konvertera XML till JSON. Lär dig mer om [arbeta med innehållstyper](../logic-apps/logic-apps-content-type.md).

Om du vill hämta utdata från en inkommande begäran, kan du använda den `@triggerOutputs()` funktion. Utdata kan se ut det här exemplet:

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

Att få åtkomst till den `body` egenskapen mer specifikt kan du använda den `@triggerBody()` genväg. 

## <a name="respond-to-requests"></a>Svara på begäranden

Du kanske vill besvara vissa begäranden som startar en logikapp genom att returnera innehållet till anroparen. Du kan använda för att skapa statuskod, sidhuvud och brödtext för ditt svar i **svar** åtgärd. Den här åtgärden kan finnas var som helst i din logikapp, inte bara i slutet av arbetsflödet.

> [!NOTE] 
> Om din logikapp inte innehåller en **svar**, HTTP-slutpunkten svarar *omedelbart* med en **202 godkända** status. Dessutom för den ursprungliga begäranden hämta svaret, även alla steg som krävs för svaret måste slutföras inom den [tidsgränsen för begäran](./logic-apps-limits-and-config.md) såvida inte du anropa arbetsflödet som en kapslad logikapp. Om inget svar inträffar inom denna inkommande begäran på grund av timeout och tar emot HTTP-svaret **408 klientens**. För kapslade logic apps fortsätter logikappen överordnade att vänta tills slutförts, oavsett hur lång tid som krävs.

### <a name="construct-the-response"></a>Skapa svaret

Du kan inkludera mer än ett sidhuvud och varje typ av innehåll i brödtext för svar. I exempel-svaret huvudet anger att svaret har innehållstyp `application/json`. och innehåller `title` och `name`, baserat på JSON-schema som tidigare har uppdaterats för den **begära** utlösare.

![Åtgärd för HTTP-svar][3]

Svar har följande egenskaper:

| Egenskap  | Beskrivning |
| --- | --- |
| statuskod |Anger HTTP-statuskod för svarar på inkommande begäran. Den här koden kan vara en giltig statuskod som börjar med 2xx, 4xx eller 5xx. 3xx statuskoder är inte tillåtna. |
| rubriker |Definierar valfritt antal huvuden ska ingå i svaret. |
| brödtext |Anger ett body-objekt som kan vara en sträng, en JSON-objekt eller även binära innehåll som refereras från föregående steg. |

Här är JSON-schema ser ut nu för den **svar** åtgärd:

``` json
"Response": {
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
   "runAfter": {},
   "type": "Response"
}
```

> [!TIP]
> Välj för att visa fullständiga JSON-definitionen för din logikapp på logiken App Designer **vyn kod**.

## <a name="q--a"></a>Frågor och svar

#### <a name="q-what-about-url-security"></a>F: Vad händer om URL-säkerhet?

S: azure genererar på ett säkert sätt logik app återanrop URL: er med hjälp av en delad signatur åtkomst (SAS). Den här signaturen passerar som en frågeparameter och måste verifieras innan din logikapp kan utlösas. Azure skapar signaturen med en unik kombination av en hemlig nyckel per logikapp, Utlösarnamnet och åtgärden som utförs. Så att de inte kan skapa en giltig signatur om någon har åtkomst till nyckeln hemliga logik app.

   > [!IMPORTANT]
   > Produktions- och säkra system rekommenderar vi mot anropa logikappen direkt från webbläsaren eftersom:
   > 
   > * Den delade åtkomstnyckeln som visas i URL: en.
   > * Du kan inte hantera säker innehåll principer på grund av delade domäner över Logikapp kunder.

#### <a name="q-can-i-configure-http-endpoints-further"></a>F: kan jag konfigurera HTTP-slutpunkter ytterligare?

S: Ja, HTTP-slutpunkter stöd för mer avancerad konfiguration via [ **API Management**](../api-management/api-management-key-concepts.md). Den här tjänsten erbjuder också möjligheten för du hantera alla dina API: er, inklusive logikappar, konfigurera anpassade domännamn, till exempel använda flera autentiseringsmetoder och mycket mer konsekvent:

* [Ändra metod för begäran](https://docs.microsoft.com/azure/api-management/api-management-advanced-policies#SetRequestMethod)
* [Ändra URL-segment i begäran](https://docs.microsoft.com/azure/api-management/api-management-transformation-policies#RewriteURL)
* Ställ in din API Management-domäner i den [Azure-portalen](https://portal.azure.com/ "Azure-portalen")
* Konfigurera princip för att söka efter grundläggande autentisering

#### <a name="q-what-changed-when-the-schema-migrated-from-the-december-1-2014-preview"></a>F: vad ändrats när schemat migreras från 1 December 2014 preview?

S: här är en sammanfattning om dessa ändringar:

| 1 december 2014 preview | Den 1 juni 2016 |
| --- | --- |
| Klicka på **HTTP-lyssnaren** API-App |Klicka på **manuell utlösaren** (ingen API-App krävs) |
| HTTP-lyssnaren inställningen ”*skickar svar automatiskt*” |Innehåller en **svar** åtgärd eller inte i arbetsflödesdefinitionen |
| Konfigurera grundläggande eller OAuth-autentisering |via API-hantering |
| Konfigurera HTTP-metoden |Under **visa avancerade alternativ**, Välj en HTTP-metod |
| Konfigurera relativ sökväg |Under **visa avancerade alternativ**, lägga till en relativ sökväg |
| Referera till inkommande brödtexten via `@triggerOutputs().body.Content` |Referens till `@triggerOutputs().body` |
| **Skicka HTTP-svar** åtgärd på HTTP-lyssnare |Klicka på **svarar på HTTP-begäran** (ingen API-App krävs) |

## <a name="get-help"></a>Få hjälp

I [Azure Logic Apps-forumet](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) kan du ställa frågor, besvara frågor och se vad andra Azure Logic Apps-användare håller på med.

På [webbplatsen för Azure Logic Apps-användarfeedback](http://aka.ms/logicapps-wish) kan du hjälpa till med att förbättra Azure Logic Apps och anslutningsapparna genom att rösta på förslag eller komma med egna förslag på förbättringar.

## <a name="next-steps"></a>Nästa steg

* [Skapa Logic App-definitioner](./logic-apps-author-definitions.md)
* [Hantera fel och undantag](./logic-apps-exception-handling.md)

[1]: ./media/logic-apps-http-endpoint/manualtrigger.png
[2]: ./media/logic-apps-http-endpoint/manualtriggerurl.png
[3]: ./media/logic-apps-http-endpoint/response.png